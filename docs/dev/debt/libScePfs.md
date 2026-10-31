# libScePfs technical debt

### Silent stubs

- [scePfsGetWorkBufferSize](../../../core/libs/prx/libScePfs/Export.cpp) (libScePfs) - returns the constant `0xed0d8`: in the bundled decrypted `libScePfs.prx` of Stellar Blade (PPSA13197) the export is `helper(8, -1) + 0x80000` where helper `0x15b0`'s bit-8 path returns `0xa8 + 0x6d020` and `0x15a0` adds `0x10`; there is no input-dependent path.

### Unknown function info

- [scePfsGetBlockTableReadCommand, scePfsGetHeaderReadCommand, scePfsGetPreadBlockRange, scePfsGetSingleBlockReadCommand, scePfsGetUncompressedSize, scePfsValidate](../../../core/libs/prx/libScePfs/Export.cpp) (libScePfs) - implemented from the bundled decrypted `libScePfs.prx` of Stellar Blade (PPSA13197, `unwrapped/libScePfs.prx.elf`, export VAs `0xef0`/`0xf20`/`0xd80`/`0xfb0`/`0x2f0`/`0xd0`). PFSC magic `0x43534650`; version u16 `+4`; total `+0x18`; v2 tag `+0x28` == 2, u64 block boundaries at `+0x30` masked 48-bit; v3 16-byte index records `+0x48`, types 3 (block table) and 7 (data region) keyed on the low byte, entries masked 44-bit; errors `0x8a780001`-`0x8a780005`. Deviation: v3 `scePfsGetSingleBlockReadCommand` with no type-3 record and `blockIndex >= 0` null-derefs on console; host returns `0x8a780003`.
  - names/NIDs verified from pinned [ps5rs stubs](https://github.com/claimore22/ps5rs/tree/5f252a658ff470bcb2b6cd15205fd8ca64ae4477/data/stubs/by_library) (`libScePfs.txt`); NID match confirmed via `tools/nid_names.py` `compute_nid`.
- [scePfsPread, scePfsPreadDecompressBlock](../../../core/libs/prx/libScePfs/Export.cpp) (libScePfs) - still `NotImplemented_nid_no_patch`. `scePfsPread` (VA `0x330`) signature decoded as `(dst, size, offset, header, workBuffer)`: null dst -> `0x8a780001`, zero size -> `0`, offset > 48 bits / null header / null workBuffer -> `0x8a780001`, non-PFSC -> `0x8a780002`, range past `+0x18` -> `0x8a780005`; its per-block worker loop and command marshalling are not yet ported. `scePfsPreadDecompressBlock` (VA `0x1180`) drives an internal range-decoder codec (`0xf0a20` block, calls `0x6380`, `0xaa0`); signature and codec semantics unverified.
