# Building BeShare on VitruvianOS

## Dependencies

| Library | Source | Notes |
|---------|--------|-------|
| libbe | VitruvianOS | Application Kit, Interface Kit, Storage Kit |
| libnetwork | VitruvianOS | BSocket, BNetworkInterface |
| libtranslation | VitruvianOS | BBitmapStream etc. |
| zlib | system | Compression (linked via `-lz`) |
| MUSCLE 6.x | git submodule | Networking/messaging framework |
| ColumnListView | VitruvianOS (interface kit) | Included in libbe — no separate santa lib needed |

## Get MUSCLE

```sh
git submodule update --init --recursive
```

This populates `source/muscle/` from the [jfriesne/muscle](https://github.com/jfriesne/muscle) stable branch.

## Build inside the Vitruvian source tree

The recommended way is to drop this repository into the Vitruvian apps
directory so it can use the `Application()` CMake macro and pick up all
the right headers and link flags automatically.

```sh
# From the Vitruvian source root
ln -s /path/to/beshare-vitruvian src/apps/beshare

# Then add to src/apps/CMakeLists.txt:
add_subdirectory(beshare)

# Build via harness
./harness/run.sh
```

## Standalone build (development / CI syntax check)

```sh
mkdir build && cd build
cmake .. -DCMAKE_BUILD_TYPE=Debug
make -j$(nproc)
```

This will link against whatever `be`, `network`, `translation` libraries
are present on the host — intended for syntax checking only, not for
producing a working binary outside VitruvianOS.

## Status

- [ ] Compiles against VitruvianOS libbe / libnetwork
- [ ] Resources (icons, strings) processed via `rc` + `xres`
- [ ] Connects to a MUSCLE server
- [ ] File transfer working
- [ ] Chat working

This is a work-in-progress port.  See [VitruvianOS issue #160](https://github.com/VitruvianOS/Vitruvian/issues/160) for tracking.
