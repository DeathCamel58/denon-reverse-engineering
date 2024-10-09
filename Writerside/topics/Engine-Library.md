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
