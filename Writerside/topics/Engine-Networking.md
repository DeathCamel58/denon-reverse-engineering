# Engine Networking

Everything on this page was recovered statically from `/usr/Engine/Engine` in the **SC6000 5.0.4**
firmware using the method in [](Engine-Ghidra-Analysis.md). Nothing here was confirmed by talking to
a device, so items are marked as observed-in-binary rather than verified on the wire.

`Engine` links `libgrpc.so.37` and uses Protocol Buffers for all of its device-to-device and
device-to-cloud RPC. The gRPC service and message names survive in `.rodata` as full method-path
strings and protobuf descriptor field names, so the interface can be enumerated exactly.

> For a full interop reference (every service, method kind, message, field number and wire type,
> plus reconstructed `.proto` files and a worked raw-HTTP/2 example) see [](Engine-gRPC.md). This
> page covers the surrounding network behaviour.
>
{style="note"}

## gRPC services

Four service packages appear in the binary. For each, `Engine` contains both the generated
client-side `Stub` classes and the server-side `Service` / `WithCallbackMethod_*` base classes, which
means the device **both calls and serves** these interfaces.

### `enginelibrary.v1.EngineLibraryService`

Library sharing: the interface behind linked/shared libraries between players.

| Method                   |
|--------------------------|
| `GetCredentials`         |
| `GetLibraries`           |
| `GetLibrary`             |
| `GetTracks`              |
| `GetTrack`               |
| `SearchTracks`           |
| `GetSearchFilters`       |
| `GetHistorySessions`     |
| `GetHistoryPlayedTracks` |
| `EventStream`            |
| `PutEvents`              |

### `enginesync.v1.EngineSyncService`

Cross-device beat/tempo sync.

| Method                    |
|---------------------------|
| `SetSyncState`            |
| `ExchangeTimestamps`      |
| `UpdateSyncLeadAudioInfo` |

Messages carry a `hostUuid` field (`enginesync.v1.TimestampSync.hostUuid`,
`enginesync.v1.DeckID.hostUuid`).

### `networktrust.v1.NetworkTrustService`

Device pairing. One method, `CreateTrust`.

The request carries two fields:

| Field                                            | Purpose                               |
|--------------------------------------------------|---------------------------------------|
| `networktrust.v1.CreateTrustRequest.ed25519_pk`  | Requesting peer's Ed25519 public key  |
| `networktrust.v1.CreateTrustRequest.device_name` | Human-readable name shown to the user |

The response is a one-of over three outcomes: `CreateTrustGranted`, `CreateTrustDenied`,
`CreateTrustBusy`; which together with the strings `Password Received: fields:` and
`Cannot enter a password if there is not an active request` indicates an interactive
approve/deny prompt on the device, with only one pairing request in flight at a time.

The client side lives in `eaas::GRPCNetworkTrustClient`, and the assertion string
`_gRPCNetworkTrustClient\nPrecondition failed\n.../Libraries/Streaming/Providers/Eaas/src/GRPCWebAccessor.cpp:473`
places it in the Eaas (streaming) subsystem.

### `remotehostscreen.v1`

Remote screen mirroring: pushing deck/library state to another device's display. No method-path
strings, but a large set of message descriptors: `KeepAlive` (with `appName`, `appVersion`),
`SetDeckTrackData`, `SetPlaylistData`, `SetDeckQuickCue`, `SetDeckLoopRegion`, `SetTrackTitle`,
`DebugString` and many more. The controllers are under `App/src/Planck/Controller/RemoteHostScreen/`.

## How the server binds

`grpcutils::Server::addServiceOnInterfaceToBuilder` (`0115a9a0`) does the listener setup:

1. Calls `QNetworkInterface::addressEntries()` and iterates the interface's IP addresses.
2. Applies a caller-supplied `std::function` filter to each address.
3. Formats the endpoint as `%1:%2`, or `[%1]:%2` for IPv6.
4. Calls `grpc::InsecureServerCredentials()`.
5. Calls `grpc::ServerBuilder::AddListeningPort(builder, "<ip>:<port>", creds, nullptr)`.

Services are attached by `grpcutils::Server::registerServicesOnBuilder` (`0115b334`) via
`grpc::ServerBuilder::RegisterService`.

The listening **port** is not a compile-time constant in these functions; it is read from a service
descriptor built by the caller, and the fourth argument to `AddListeningPort` is `nullptr`, so the
code never reads back the port actually bound.

### Observed ports

Measured on two SC6000 units running 5.0.4, attached over **Wi-Fi**:

| Port           | Both devices?      | What it is                                                                                                                            |
|----------------|--------------------|---------------------------------------------------------------------------------------------------------------------------------------|
| `50010`        | :white_check_mark: | gRPC over cleartext HTTP/2, hosting **`enginesync.v1`** only                                                                          |
| `41401`        | :white_check_mark: | License-activation callback inside Engine; route `/authorise?code=`                                                                   |
| ~12 high ports | :x:                | Differ per device; silent to an HTTP/2 preface. Probably the `airNetworkExchange` directory/service sockets. **Transient**: see below |
| `22` (SSH)     | :x:                | **Closed**: not enabled by default in 5.0.4                                                                                           |

### The high ports are transient, not fixed listeners

Once network library sharing was
active (one deck playing from an SD card physically inserted in the *other* deck) a rescan of the
library **host** showed three additional ports (`33199`, `41315`, `41965`), while the client deck was
unchanged. By the time a probe reached them seconds later they were **refusing connections**, and a
follow-up full sweep of `30000-47000` found them gone along with one port from the original baseline.

So these are per-session or per-transfer sockets with short lifetimes, consistent with
`airNetworkExchange::DirectorySocket` / `ServiceServer` instances rather than long-lived services.
Anything scanning them has to probe in the same pass as the scan, or it will lose the race.

Note also that only the deck **hosting** the media opened extra ports, which is a useful signal about
which side of a shared-library session serves data.

`50010` is the port the community associates with StagelinQ, and it is a fixed, well-known value
rather than the dynamic one the code structure suggested.

> Only `enginesync.v1` answers on `50010`. `enginelibrary.v1.EngineLibraryService` and
> `networktrust.v1.NetworkTrustService` both return `UNIMPLEMENTED` there, which is how gRPC responds
> for a service that was never registered. Where those two are exposed (if they are exposed on a
> network interface at all) is still unknown.
>
{style="note"}

## How clients connect

`grpcutils::createInsecureChannel` (`01158c44`) is the **only** channel factory in the binary. It
calls `grpc::CreateCustomChannel(target, grpc::InsecureChannelCredentials(), args)`.

Its callers are:

- `enginelibrary::v1::EngineLibraryService::Stub` registration (`00fc8ed0`)
- `EngineSyncClient` (`00d6a9e8`)
- `NetworkTrustService::CreateTrust` (`00fceecc`)

So all three device-to-device services share one insecure channel implementation.

## Per-call credentials

`engineSync::EngineSyncNetwork` attaches exactly one gRPC metadata header before each call:

```C
grpc::ClientContext::AddMetadata(ctx, "uuid", <hostUuid>);
```

The key string lives at `02a98028`: a `std::string` whose small-string buffer contains `uuid` with
length 4. No other metadata is attached, and no signature or MAC accompanies it.

The server confirms this is the credential it checks. Calling `ExchangeTimestamps` or
`UpdateSyncLeadAudioInfo` without it returns `FAILED_PRECONDITION` and the message
`The client did not pass the uuid in the metadata.` A second gate exists whose message is present in
`.rodata` (`The requesting host has not been discovered on the server side yet. Try again later.`)
so a caller must both present a `uuid` and already appear in the server's discovered-host table,
maintained by `SyncServiceDiscoverer` (`onHostAdded` / `onHostRemoved(uuid)`).

The same identifier is user-visible as the `/Network/NetworkId` state property. It is displayed in
the device's own settings UI and embedded in QML channel-assignment strings, e.g.
`Planck.engineConnect.generateChannelAssignmentString(networkId, deckLayerIndex + 1)`.

## Service discovery

Discovery does **not** use mDNS/DNS-SD. There are no `_tcp.`/`_udp.`/service-type strings in
`Engine`, and while Avahi does run on the device (see the journalctl snippet in [](JP11.md)) it
reports `No service file found in /etc/avahi/services`.

### EAAS discovery beacon: UDP 11224

Observed live on two SC6000s running 5.0.4. Both units broadcast a **6-byte** UDP datagram to port
**11224**, roughly every 2.5 seconds each, from an ephemeral source port:

```
0000  45 41 41 53 01 00     EAAS..
```

That is the ASCII magic `EAAS` followed by `0x01 0x00`: plausibly a version or a
version/flags pair. The beacon carries **no device token, no UUID and no port list**, so it appears
to announce presence only; a peer then connects to the fixed gRPC port 50010 to do anything useful.

> **The older StagelinQ discovery is not used on 5.0.4.** Listening on UDP **51337** for the `airD`
> magic produced nothing at all over a 40-second window, including while both decks had tracks loaded
> and playing. Sending well-formed `DISCOVERER_HOWDY_` announcements to 51337 drew no response
> either. Engine OS 5.0.4 appears to have moved to the EAAS beacon.
>
{style="note"}

Useful cross-reference: [chrisle/StageLinq](https://github.com/chrisle/StageLinq) is a TypeScript
implementation covering both the classic StagelinQ services (StateMap, BeatInfo, FileTransfer,
TimeSync, Directory, Broadcast (UDP 51337), `airD` magic, length-prefixed UTF-16BE strings) and the
newer EAAS layer. Two of its EAAS details did **not** match these 5.0.4 units:

| That project documents                                              | Observed on SC6000 5.0.4                                                                 |
|---------------------------------------------------------------------|------------------------------------------------------------------------------------------|
| `EngineLibraryService` and `NetworkTrustService` on gRPC port 50010 | Both return `UNIMPLEMENTED` on 50010; only `enginesync.v1` is registered                 |
| HTTP `/ping` and `/download/{path}` on port **50020**               | TCP 50020 is **closed** on both units: the cpp-httplib `EaasHttpServer` is not listening |

Those are the routes the `EaasHttpServer` registers in the binary (`/ping` and
`/download/(<)(.*)(>)`), so the code is present but the server was not running in any state tested,
including with tracks loaded and playing. Whether it starts under some other condition (a linked
library, a logged-in streaming account) is unknown.

Instead, there are two custom subsystems, both recovered as RTTI class namespaces:

| Namespace              | Notable classes                                                                                                                           |
|------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| `airNetworkDiscoverer` | `Discoverer`, `DiscovererThread`, `Sender`, `Receiver`, `Host`, `HostModel`, `HostController`, `HostAddressHelper`                        |
| `airNetworkExchange`   | `DirectoryServer` (incl. `sendServiceInfo`), `DirectorySocket`, `ServiceServer`, `Server`, `Host`, `HostEnumerator`, `Service`, `Network` |

The `Sender`/`Receiver` pair in `airNetworkDiscoverer` is consistent with a UDP
broadcast/announcement scheme, and `airNetworkExchange::DirectoryServer::sendServiceInfo`
(`016f99a8`) is where a peer is told which services exist and on which port. Two related state
properties are exposed to the UI: `/Network/NumberOfDirectorySockets` ("Connected Sockets") and
`/Network/NetworkId` ("Network ID").

This is very likely the same family of protocols the community calls **StagelinQ**, but that
correspondence has not been verified here.

## Ableton Link: UDP multicast 224.76.78.75:20808

With Ableton Link enabled, both decks join the standard Link session and multicast their state in
cleartext. This is **Ableton's open protocol, not an inMusic one**, but it is by far the easiest way to
read a deck's live tempo and beat phase from another machine: no pairing, no `uuid`, no gRPC. Multicast
is delivered to an ordinary managed-mode Wi-Fi station, so it needs no elevated privileges.

Observed packet (107 bytes, sent a few times a second by each deck):

```
0000  5f 61 73 64 70 5f 76 01 01 05 00 00 3e 77 5a 62  _asdp_v.....>wZb
0010  6e 54 66 5c 74 6d 6c 6e 00 00 00 18 00 00 00 00  nTf\tmln........
0020  00 09 bd 52 00 00 00 03 23 21 08 a4 00 00 00 00  ...R....#!......
0030  00 00 2e 98 73 65 73 73 00 00 00 08 32 7b 5f 74  ....sess....2{_t
0040  77 3e 23 45 73 74 73 74 00 00 00 11 00 00 00 00  w>#Estst........
0050  00 00 00 00 00 00 00 00 00 00 00 00 00 6d 65 70  .............mep
```

Layout: a 12-byte header, an 8-byte node id, then length-prefixed TLV sections keyed by a 4-character
ASCII tag.

| Offset / tag | Meaning                                                                                                         |
|--------------|-----------------------------------------------------------------------------------------------------------------|
| `0x00`       | magic `_asdp_v`                                                                                                 |
| `0x07`       | protocol version (`0x01`)                                                                                       |
| `0x08`       | message type: `1` ALIVE, `2` RESPONSE, `3` BYEBYE                                                               |
| `0x09`       | TTL (`0x05`)                                                                                                    |
| `0x0a`       | group id (`0x0000`)                                                                                             |
| `0x0c`       | 8-byte node id                                                                                                  |
| `tmln`       | timeline, 24 bytes = three big-endian int64: micros-per-beat, beat origin in micro-beats, time origin in micros |
| `sess`       | 8-byte session id: identical across peers in the same session                                                   |
| `stst`       | start/stop state, 17 bytes: int64 timestamp then a play flag                                                    |
| `mep4`       | measurement endpoint, 6 bytes: 4-byte IPv4 then 2-byte UDP port, for Link's latency ping/pong                   |

Decoded from the two SC6000s while both were playing:

| Field            | `10.2.0.209`                    | `10.2.0.211`                      |
|------------------|---------------------------------|-----------------------------------|
| node id          | `3e775a626e54665c`              | `4e50585b2f31572f`                |
| session id       | `327b5f74773e2345`              | `327b5f74773e2345` (same session) |
| tempo            | 638290 µs/beat → **94.001 BPM** | identical                         |
| beat origin      | 13474.269 beats                 | identical                         |
| `mep4`           | `10.2.0.209:53521`              | `10.2.0.211:60364`                |
| `stst` play flag | `0`                             | `0`                               |

Two things worth noting. The timeline is byte-identical across both decks, which is what a converged
Link session should look like. And the `stst` play flag reads `0` even with both decks playing, so
Engine evidently does not drive Link's optional start/stop sync; only tempo and phase.

> The Link **node id is 8 bytes** and is unrelated to the Engine **Network ID** (a 36-character UUID)
> used in the gRPC `uuid` metadata header. Observing Link does **not** reveal a deck's Engine UUID.
>
{style="note"}

## Network file access

A set of NFS classes is present, indicating library media is read over the network rather than
copied: `airNfsFileAdapter`, `airNfsFile`, `airNfsQtFileEngine`, `airNfsSubDirectory`, plus a
generic `airVfsFileEngine` and `airFileFactory`. These are Qt `QAbstractFileEngine` subclasses, so
NFS paths are transparent to the rest of the application.

## Cloud endpoints

Not device-to-device, but present in the same binary:

| Endpoint                                                                  | Purpose                    |
|---------------------------------------------------------------------------|----------------------------|
| `https://api.inmusicbrands.com/v1/connectable_accounts/NAMESPACE_ENGINE/` | Streaming account linking  |
| `https://dropbox.enginedj.com/api`                                        | Dropbox integration        |
| `https://dropbox.enginedj.com/ping/`                                      | Dropbox reachability check |
| `/oauth2/token`, `/oauth2/device_token`                                   | OAuth 2.0 device flow      |
| `http://127.0.0.1:9999`                                                   | Local loopback HTTP        |

The OAuth device-code flow is used for streaming-service login (`accessToken`, `refreshToken`,
`client_secret`, `id_token`, `TOKEN_KEYS`, `access_token_creation_date`).

### Two separate HTTP servers

Engine contains two unrelated HTTP server implementations, which is easy to conflate:

| Server                      | Implementation                                                                                                | Routes                                                                                                               | Notes                                                                                                                                                   |
|-----------------------------|---------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|
| `eaas::EaasHttpServer`      | **cpp-httplib** (`httplib::Server`), source `Libraries/private/Planck/Eaas/src/httpServer/EaasHttpServer.cpp` | `/ping`, `/download/(<)(.*)(>)` → `onDownloadRequest`, serving `application/octet-stream`                            | Restarted per interface via `onNetworkInterfaceActiveStatusChanged`. Not observed listening on either test unit                                         |
| License-activation callback | Hand-rolled `accept`/`recv`/`send`/`poll` loop at `00f1d2c8`, request matched with `strstr`                   | `GET /authorise?code=` → `303 See Other` to `https://device.inmusicbrands.com?result=granted&client_id=com.enginedj` | **This is what answers on port 41401.** Belongs to `inmusic::software_unlock::LicenseManager`; the surrounding functions are all CryptoPP RSA/PKCS1v15. |

The two are distinguishable on the wire: cpp-httplib emits conformant responses with headers, whereas
the hand-rolled server sends a bare `HTTP/1.1 404 Not Found` with no CRLF terminator and no headers at
all (which is why `curl` shows nothing for it).

`Eaas` also has its own **gRPC server** (`Libraries/private/Planck/Eaas/src/grpcServer/`, including
`Handlers/TrackWriterHandler.h`), separate from the `enginesync.v1` server on 50010.

### OAuth flows

Engine implements several OAuth variants, one per provider; they should not be conflated:

| Flow                                      | Where                                                                                                                                              | Notes                                                                                                                                                                                                                                                                                                                                                                                                                                               |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Device Authorization Grant** (RFC 8628) | TIDAL (`checkPairingCode`, scope `r_usr`, `client_unique_key`), Dropbox (`oauth2/device_authorization`); generic response parser at `FUN_00f538c4` | Handles `device_code`, `user_code`, `verification_uri`, `verification_uri_complete`, `interval`, `expires_in`; polls `oauth2/token` and handles `authorization_pending` / `slow_down`. **This is the QR-code login**: the deck has no browser, so it renders `verification_uri_complete` as a QR (via Nayuki `qrcodegen`), the user completes sign-in on a phone, and the deck polls until granted. No authorization code crosses the local network |
| Authorization code **with `state`**       | `inMusicCloud::OauthCode::vfunc_14`, `engineCloud::OauthCode::vfunc_14`                                                                            | Builds `?scope=…&response_type=code&state=…&redirect_uri=…&client_id=…&access_type=…`                                                                                                                                                                                                                                                                                                                                                               |
| Implicit (`response_type=token`)          | `inMusicCloud::OauthImplicit::vfunc_14`, `engineCloud::OauthImplicit::vfunc_14`                                                                    | Same parameter set with `response_type=token`                                                                                                                                                                                                                                                                                                                                                                                                       |
| Authorization code **without `state`**    | `FUN_011b5acc`, template `{}/auth/o/authorize/?client_id={}&response_type=code&redirect_uri={}`                                                    | A third-party provider; also references `/my/account/`, `u.txt`, `l.txt`, `r.txt`                                                                                                                                                                                                                                                                                                                                                                   |
| License activation callback               | Server loop at `00f1d2c8` on port **41401**                                                                                                        | `inmusic::software_unlock::LicenseManager`                                                                                                                                                                                                                                                                                                                                                                                                          |

`code_verifier`, `code_challenge` and `code_challenge_method` do not occur anywhere in the binary, so
**PKCE is not implemented in any of these flows**. For the device grant that matters little; for the
redirect-based flows it matters more.

Streaming DRM is Widevine (`wvcdm::`, `video_widevine::` and `drm_metrics::` classes are compiled in),
along with a CryptoPP build.

## Related Files

- [](Engine-Ghidra-Analysis.md) covers how these names were recovered.
- [](Engine.md) covers the application generally.
- [](Updating.md) covers the SSH access used to reach a running device.
