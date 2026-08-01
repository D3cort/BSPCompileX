# BSPCompileX

<p align="center">
  <img src="0.7.0-beta/assets/bspcompilex-0.7.0-beta-cover.png" alt="BSPCompileX 0.7.0 Beta" width="960">
</p>

<p align="center">
  <img alt="Version" src="https://img.shields.io/badge/version-0.7.0%20Beta-f97316">
  <img alt="Platform" src="https://img.shields.io/badge/platform-Windows%20x64-2563eb">
  <img alt="Garry's Mod" src="https://img.shields.io/badge/engine-Garry's%20Mod-0ea5e9">
  <img alt="License" src="https://img.shields.io/badge/license-proprietary%20freeware-64748b">
</p>

**BSPCompileX** is a native Windows toolkit for Garry's Mod map compilation, BSP decompilation, model conversion and Source texture creation. The 0.7.0 Beta release keeps the normal VMF workflow simple while adding the tools that previously required several separate applications.

## Download

- **[Download BSPCompileX 0.7.0 Beta for Windows x64](https://github.com/D3cort/BSPCompileX/releases/download/v0.7.0-beta/BSPCompileX-0.7.0-Beta-Windows-x64.exe)**
- [Open the GitHub release](https://github.com/D3cort/BSPCompileX/releases/tag/v0.7.0-beta)
- [Read the English release article](BSPCompileX-0.7.0-Beta-EN.md)
- [Прочитать статью на русском](BSPCompileX-0.7.0-Beta-RU.md)
- [Verify the SHA-256 checksum](SHA256SUMS.txt)

## What is new in 0.7.0 Beta

- Separate **Compiler**, **Decompiler**, **Models** and **Textures** workspaces with independent logs.
- Built-in VMF/BSP and before/after asset viewports.
- Direct model conversion with LODs, collision, animations and optional 128-bone skeleton fitting — Blender is not required.
- Static and animated VTF/VMT conversion from images, GIFs and Windows-supported video formats.
- CPU, GPU and memory monitoring during map builds.
- Smart rebuilds, safer FullHDR lighting, Titan Geometry diagnostics and a map dependency ZIP packer.
- Automatic system-language selection and a localized interface in 20 languages.
- A localized first-run license agreement and a permanent License page in Settings.

## Quick start

1. Download and run `BSPCompileX-0.7.0-Beta-Windows-x64.exe`.
2. Accept the license agreement on the first launch.
3. Select or drop a `.vmf` file.
4. Leave the build profile on **Normal** for a regular build.
5. Choose the output folder and press **Start**.

The release is a signed single-file package. It extracts its authenticated runtime to a temporary per-run directory and removes that directory when the application closes. Python, Java and Blender are not runtime requirements.

## Requirements

- Windows x64.
- Garry's Mod and its x64 Source tools for map compilation and StudioMDL output.
- A final in-game check before publishing important maps or assets; this remains a Beta release.

## License

Official unmodified binaries may be used free of charge in personal, open-source, freeware and commercial projects. No royalty, payment or attribution is required. Reverse engineering, modification, redistribution for a separate fee, and use of leaked or recovered code are prohibited except where mandatory law provides otherwise. Read the complete [BSPCompileX license](LICENSE.txt) before use.

Developer: **[D3cort](https://github.com/D3cort)**
