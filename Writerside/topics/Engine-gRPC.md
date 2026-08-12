# Engine gRPC Reference

A reconstruction of the gRPC and Protocol Buffers surface of `/usr/Engine/Engine`, intended for
anyone writing software that talks to Engine OS hardware.

Everything here was recovered from the **SC6000 5.0.4** binary using the method in
[](Engine-Ghidra-Analysis.md). Transport behavior was then verified against two live SC6000 units.
This is **not** an official schema, and no vendor documentation was consulted.

> **What is solid and what is not.** Service names, method paths, method kinds (unary vs streaming)
> and request/response types are **exact**. They come from C++ template parameters preserved in the
> binary's RTTI. Field numbers and wire types are **exact**. They come from the compiled
> serializers. Field **names** exist only for `string` fields, and **scalar types** are narrowed only
> as far as the wire format allows. Anything uncertain is suffixed `_UNKNOWN` or `_UNCONFIRMED` in
> the `.proto` files rather than guessed at.
>
{style="warning"}

## Transport

| Property          | Value                                                                                                                                     |
|-------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| Port              | **TCP 50010**, hardcoded (`movw #0xc35a` in Engine)                                                                                       |
| Discovery         | **UDP 11224** beacon, 6 bytes: `EAAS` + `0x01 0x00`, broadcast ~2.5 s per device                                                          |
| Bind address      | Every address returned by `QNetworkInterface::addressEntries()` that passes an interface filter. Confirmed reachable on the Wi-Fi address |
| Protocol          | gRPC over **cleartext HTTP/2**, no TLS, no ALPN negotiation                                                                               |
| Server preface    | Sends `SETTINGS` immediately on connect (`MAX_CONCURRENT_STREAMS=0x7fffffff`, `INITIAL_WINDOW_SIZE=4 MiB`)                                |
| Content type      | `application/grpc`                                                                                                                        |
| Reflection        | **Not available**: `grpc.reflection.v1alpha.ServerReflection` returns `UNIMPLEMENTED`                                                     |
| Compression       | Not observed; send the message with the compression flag clear                                                                            |
| Required metadata | `uuid: <host uuid>` on most methods (see below)                                                                                           |

Since there is no TLS and no reflection, a client is a plain HTTP/2 connection with
`content-type: application/grpc` and standard 5-byte-prefixed messages. No credentials plugin, no
channel certificates.

### Which services are actually served on 50010

Only one. This trips people up, because the binary contains client stubs *and* server bases for
several packages:

| Service                                    | On port 50010             |
|--------------------------------------------|---------------------------|
| `enginesync.v1.EngineSyncService`          | :white_check_mark: served |
| `enginelibrary.v1.EngineLibraryService`    | :x: `UNIMPLEMENTED`       |
| `networktrust.v1.NetworkTrustService`      | :x: `UNIMPLEMENTED`       |
| `grpc.reflection.v1alpha.ServerReflection` | :x: `UNIMPLEMENTED`       |

`UNIMPLEMENTED` is what gRPC returns for a service that was never registered, so the library and
pairing services are either exposed elsewhere, exposed only under some condition, or used purely as
clients on this device. Where they live is **not yet known**. See the open questions below.

### The `uuid` metadata header

Two of the three `enginesync` methods require a metadata header whose key is literally `uuid` and
whose value is the caller's host UUID. Omitting it produces:

```
grpc-status : 9 (FAILED_PRECONDITION)
grpc-message: The client did not pass the uuid in the metadata.
```

A second server-side check exists whose message is in `.rodata`:

```
The requesting host has not been discovered on the server side yet. Try again later.
```

Both stages were confirmed live, and they are distinguishable by the error you get:

| What you send                          | `grpc-status`           | `grpc-message`                                                                         |
|----------------------------------------|-------------------------|----------------------------------------------------------------------------------------|
| no `uuid` header                       | `9` FAILED_PRECONDITION | `The client did not pass the uuid in the metadata.`                                    |
| a `uuid` the device has not discovered | `9` FAILED_PRECONDITION | `The requesting host has not been discovered on the server side yet. Try again later.` |

So the gate is: present a `uuid`, **and** that UUID must already be in the device's discovered-host
table (maintained by `SyncServiceDiscoverer`, whose `onHostAdded`/`onHostRemoved(uuid)` slots appear
in the Qt metadata). The device's own UUID is user-visible as the `/Network/NetworkId` state property.

> Simply broadcasting the EAAS discovery beacon (UDP 11224, see [](Engine-Networking.md)) is **not**
> sufficient to get into that table. The error stays at "has not been discovered" afterward. The
> beacon is only 6 bytes and carries no UUID, so registration must happen through some further
> exchange that has not been identified yet.
>
{style="note"}

## Service definitions

### `enginesync.v1.EngineSyncService`: beat/tempo sync

| Method                    | Kind                 | Request             | Response                |
|---------------------------|----------------------|---------------------|-------------------------|
| `SetSyncState`            | unary                | `SyncState`         | `google.protobuf.Empty` |
| `ExchangeTimestamps`      | **bidi streaming**   | `TimestampSync`     | `TimestampSync`         |
| `UpdateSyncLeadAudioInfo` | **client streaming** | `SyncLeadAudioInfo` | `google.protobuf.Empty` |

```
```
{ src="grpc/enginesync-v1-proto" }

### `enginelibrary.v1.EngineLibraryService`: library sharing

| Method                   | Kind                 | Request                         | Response                         |
|--------------------------|----------------------|---------------------------------|----------------------------------|
| `GetCredentials`         | unary                | `GetCredentialsRequest`         | `GetCredentialsResponse`         |
| `GetLibraries`           | unary                | `GetLibrariesRequest`           | `GetLibrariesResponse`           |
| `GetLibrary`             | unary                | `GetLibraryRequest`             | `GetLibraryResponse`             |
| `GetTrack`               | unary                | `GetTrackRequest`               | `GetTrackResponse`               |
| `GetSearchFilters`       | unary                | `GetSearchFiltersRequest`       | `GetSearchFiltersResponse`       |
| `GetHistorySessions`     | unary                | `GetHistorySessionsRequest`     | `GetHistorySessionsResponse`     |
| `GetHistoryPlayedTracks` | unary                | `GetHistoryPlayedTracksRequest` | `GetHistoryPlayedTracksResponse` |
| `PutEvents`              | unary                | `PutEventsRequest`              | `PutEventsResponse`              |
| `GetTracks`              | **server streaming** | `GetTracksRequest`              | `GetTracksResponse`              |
| `SearchTracks`           | **server streaming** | `SearchTracksRequest`           | `SearchTracksResponse`           |
| `EventStream`            | **server streaming** | `EventStreamRequest`            | `EventStreamResponse`            |

```
```
{ src="grpc/enginelibrary-v1-proto" }

### `networktrust.v1.NetworkTrustService`: device pairing

| Method        | Kind  | Request              | Response              |
|---------------|-------|----------------------|-----------------------|
| `CreateTrust` | unary | `CreateTrustRequest` | `CreateTrustResponse` |

The request carries an **Ed25519 public key as a `string`** (not `bytes`; so it is hex or base64
text) plus a `device_name` that is shown to the user. The response is one of `CreateTrustGranted`,
`CreateTrustDenied` or `CreateTrustBusy`, and the strings `Password Received: fields:` and
`Cannot enter a password if there is not an active request` indicate an interactive prompt on the
device with a single request in flight at a time.

```
```
{ src="grpc/networktrust-v1-proto" }

## `remotehostscreen.v1` is not gRPC

Worth stating plainly, because the package looks like the others: **there is no
`remotehostscreen` service and no method-path strings**. All 75 message types are fields of one
envelope, `HybridModeMessage`, exchanged as framed protobuf over its own transport. The relevant
binary is `/usr/bin/planck-remote-screen` a **JUCE** application (`juce::String`,
`SmexControlHost`, `SmexControlClient`), a completely different stack from the Qt-based `Engine`
launched by `/usr/Engine/Scripts/remote-screen.sh`.

The envelope has 77 message-typed fields at sparse numbers (1-4, 10-22, 31-35, a contiguous 40-77,
then 90-107), which suggests commands were appended over successive releases. The individual command
messages are well recovered; the mapping from envelope field number to command type is not, because
a serialized message field records only its number and length.

```
```
{ src="grpc/remotehostscreen-v1-proto" }

## Talking to the device

No `.proto` or generated stubs are needed. A raw HTTP/2 client is enough. Minimum viable call:

1. TCP connect to `<device>:50010`.
2. Send the HTTP/2 client preface `PRI * HTTP/2.0\r\n\r\nSM\r\n\r\n` and an empty `SETTINGS` frame.
3. Open a stream with `HEADERS`:
   `:method POST`, `:scheme http`, `:path /enginesync.v1.EngineSyncService/SetSyncState`,
   `:authority <device>:50010`, `content-type application/grpc`, `te trailers`,
   and `uuid <your-host-uuid>` for the methods that require it.
4. Send `DATA`: one byte compression flag (`0x00`), four bytes big-endian message length, then the
   serialized protobuf.
5. Read `grpc-status` from the response trailers (or headers, for an immediate failure).

A `SyncState` with no fields set is a valid, accepted message, which makes it a convenient
reachability probe. Useful status codes seen in practice:

| `grpc-status`           | Meaning here                                                                              |
|-------------------------|-------------------------------------------------------------------------------------------|
| `0` OK                  | Accepted                                                                                  |
| `9` FAILED_PRECONDITION | Missing `uuid` metadata, or host not yet discovered                                       |
| `12` UNIMPLEMENTED      | Service or method not registered on this port. Also returned for a malformed request body |

> A malformed protobuf body returns `UNIMPLEMENTED`, not `INVALID_ARGUMENT`. Do not read
> `UNIMPLEMENTED` as proof that a method does not exist.
>
{style="note"}

## Validation

The four `.proto` files in this page are not just plausible-looking.

1. **They compile.** All four pass `protoc 3.21.12` cleanly (`--descriptor_set_out=/dev/null`).
2. **They generate usable stubs.** `grpc_tools.protoc` produces working Python bindings, and the
   encoder round-trips: a `SyncState` containing one `DeckState` with `field2 = 7` serializes to
   `0a 02 10 07` (field 1, length 2, containing field 2 varint 7, exactly as the wire format
   requires.)
3. **A real device accepts messages built from them.** Encoding `SyncState` values with the generated
   bindings and posting them to `10.2.0.209:50010` returns `grpc-status 0` in every case:

| Message sent                                       | Encoded body                | Result |
|----------------------------------------------------|-----------------------------|--------|
| empty `SyncState`                                  | *(empty)*                   | `0` OK |
| one `DeckState` with `DeckID{deck=1, hostUuid=…}`  | `0a2a0a280801122430303030…` | `0` OK |
| one `DeckState` with `deck=2` and fields 2/3/4 set | `0a0a0a020802100118012001`  | `0` OK |
| four `DeckState` entries                           | `0a040a0208010a040a020802…` | `0` OK |

That confirms the field numbers and wire types for `SyncState`, `DeckState` and `DeckID` are correct
as far as the device's parser is concerned. It does **not** confirm the *semantics*. The device
accepted the messages, but what it did with them was not observed.

## How this was recovered, and where it is weak

The binary uses **protobuf-lite**, which strips descriptors. There is no `FileDescriptorProto` to
dump and no reflection to query. Each piece came from somewhere different:

| What                                | Source                                                                                                                                   | Confidence              |
|-------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------|-------------------------|
| Service and method names            | `/package.Service/Method` string literals                                                                                                | Exact                   |
| Method kind, request/response types | C++ template parameters in RTTI typeinfo names, e.g. `RpcMethodHandler<...Service, SyncState, protobuf::Empty>`                          | Exact                   |
| Message inventory                   | Demangled typeinfo names per package                                                                                                     | Exact                   |
| Field numbers and wire types        | Tag bytes and field-number arguments in each message's `_InternalSerialize` (vtable slot 12)                                             | Exact where recovered   |
| `string` field names                | `WireFormatLite::VerifyUtf8String(..., "pkg.Msg.field")` protobuf emits this **only** for `string` fields                                | Exact                   |
| `string` vs `bytes`                 | Same mechanism, used as a discriminator: a length-delimited field written **without** a UTF-8 verification call is `bytes`, not `string` | Exact                   |
| Map fields                          | Generated `*_LoopsEntry_DoNotUse` helper types                                                                                           | Exact that a map exists |
| Everything else                     | Not recoverable                                                                                                                          |                         |

Known weaknesses, so nobody trusts this further than it deserves:

- **Non-string field names are unknown.** `bytes`, numeric, `bool`, `enum` and message fields leave
  no name behind. They are labeled `fieldN_UNKNOWN`.
- **Wire types under-determine declared types.** A `varint` may be any of `int32 int64 uint32 uint64
  sint32 sint64 bool enum`; `fixed64` may be `double fixed64 sfixed64`.
- **Message field types are unknown.** A message-typed field records only its number, so
  `GetTrackResponse` fields 1-3 are known to be messages, but their types are inferred at best.
- **Some serializers are shared.** protobuf deduplicates identical layouts, so several
  "single repeated message field" messages resolve to the same function (`0113fb90`). The shape is
  right; the element type is inferred from the method signature.
- **A few messages report no fields.** Either genuinely empty marker messages, or their fields are
  emitted through a path the extraction does not follow.
- **`TrackPerformanceData` has a conflicting recovery** for field 1. Flagged inline.
- Field numbers **not** listed are not necessarily unused; `TrackMetadata` skips 2 and `SetTrackData`
  skips 8, which usually means reserved or removed fields.

## Open questions

Concrete things a contributor could close:

1. **Where are `enginelibrary.v1` and `networktrust.v1` exposed?** They are not on 50010. Candidates:
   one of the ~12 per-device ephemeral ports, a different interface, or only after pairing.
2. **What are the ~12 high ports?** They differ per device and are silent to an HTTP/2 preface, so
   they are probably `airNetworkExchange` directory/service sockets, not gRPC.
3. **Envelope field mapping for `HybridModeMessage`**: a capture of a real remote-screen session
   would settle it quickly.
4. **Non-string field names** would need either a debug build, the `.proto` files, or careful
   correlation of observed traffic against known UI state.
5. `Eaas` has its **own gRPC server** (`Libraries/private/Planck/Eaas/src/grpcServer/`, including
   `Handlers/TrackWriterHandler.h`) which is not covered here at all.

## Related Files

- [](Engine-Networking.md): ports, discovery, the two HTTP servers, and cloud endpoints.
- [](Engine-Ghidra-Analysis.md): how to reproduce this recovery on another binary or firmware version.
- [](Engine.md): the application itself.
