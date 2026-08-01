# BSPCompileX 0.7.1 Beta

0.7.1 is a compatibility update for the 0.7.0 Beta. It keeps the same workflow and file formats while making startup and rendering more predictable on less typical Windows installations.

## Changes

- BSPCompileX now asks for confirmation before closing while a map build, decompilation, asset conversion, queue, ZIP package or update download is active. The warning is available in all 20 interface languages.
- The Direct3D 11 viewport falls back to Windows WARP when no usable hardware device is available, including some virtual-machine and remote-session configurations.
- Windows Media Foundation is no longer a hard startup dependency. Windows N can use map, model and static-image features without the Media Feature Pack; video import reports what is missing instead of preventing the application from opening.
- A new release test verifies the x64 architecture, static MSVC runtime and delayed Media Foundation imports before packaging.

## Verification

The Windows x64 Release build passed all 19 registered CTest cases. The signed single-file package then passed its embedded translation and asset-converter self-tests. Its Authenticode signature and SHA-256 checksum were verified before upload.

## Supported systems

The supported target is Windows 10 or Windows 11 on an x64 PC with the x64 Garry's Mod tools installed. A separate Visual C++ Redistributable is not required. Linux, macOS, Windows on ARM and systems without the Garry's Mod toolchain are not supported.

Video decoding still depends on the Media Foundation codecs installed in Windows. Compilation time, GPU load and byte-for-byte BSP output can vary with Source tools, drivers and hardware.
