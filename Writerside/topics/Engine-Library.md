# Engine Library

Ref: [Mixxx's Documentation](https://github.com/mixxxdj/mixxx/wiki/Engine-Library-Format)

The only missing thing that I've noticed is that `m.db`'s `AlbumArt.hash` isn't documented well. They assume it's an
SHA-1 hash, and I can verify through reverse engineering their binary that it is. However, it seems like they may be
modifying a constant in the SHA-1 algorithm, or modifying the binary blob of the image somehow prior to hashing.

Here's why I think they may be modifying the SHA-1 constants (ghidra analysis of `Engine` binary function in firmware
`2.0.1`):

```C
  [...]

  h_[0] = 0x67452301;
  h_[1] = 0xefCDAB89;
  h_[4] = 0x0141DF50;

  [...]
```

Referencing the source file used for generating this function, I found
that [Boost's UUID SHA-1 implementation](https://github.com/boostorg/uuid/blob/develop/include/boost/uuid/detail/sha1.hpp)
is the source file used to generate this. Note that `h_[4]` is set to `0x0141DF50`, whereas the original source uses
`0xC3D2E1F0`.

I requested some more information on the Engine
Community ([Forum Post](https://community.enginedj.com/t/engine-library-database-album-art-hash/59189/3) here). It seems
like they don't know either (although hopefully someone will chime in with additional information).

## Re-checked against firmware 5.0.4

The modified-constant finding above **does not reproduce** in the SC6000 `5.0.4` `Engine` binary.

Searching every initialized memory block for both candidate values (unaligned, in both
endiannesses) returns nothing:

| Constant     | Meaning                              | Occurrences in 5.0.4 |
|--------------|--------------------------------------|----------------------|
| `0xC3D2E1F0` | Standard SHA-1 `h_[4]`               | 0                    |
| `0x0141DF50` | The value documented above for 2.0.1 | 0                    |

What *is* present are two literal pools containing `0x67452301`, `0xEFCDAB89`, `0x98BADCFE`,
`0x10325476` at `00ef3478` and `00f222f8`. Those are groups of **four** words with no fifth, which
makes them **MD5** initialization constants rather than SHA-1; SHA-1 needs the fifth word that is
absent here. The second pool is referenced from a CryptoPP `TF_VerifierBase` / `RSA` / `PKCS1v15` /
`SHA1` signature-verification class, i.e. RSA signature checking, not artwork hashing.

The most likely explanation is that album-art hashing moved to OpenSSL. `EVP_sha1`,
`EVP_DigestInit_ex`, `EVP_DigestUpdate` and `EVP_DigestFinal_ex` are all imported by this build, and
OpenSSL keeps its round constants inside `libcrypto`, not in `Engine`. If so the hash may simply be
standard SHA-1 in current firmware.

> This has not been confirmed. Nobody has yet traced the code path that populates `AlbumArt.hash`
> in 5.0.4, or checked a hash produced by current firmware against a plain SHA-1 of the same image.
> That test would settle it, and is the obvious next step.
>
{style="note"}

Note also that the `Track` table in current firmware carries an `albumArtSourceHash CHAR(40)`
column (40 hex characters, consistent with SHA-1) alongside the older `AlbumArt.hash`.
