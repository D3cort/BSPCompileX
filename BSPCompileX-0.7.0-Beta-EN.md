![BSPCompileX 0.7.0 Beta](https://raw.githubusercontent.com/D3cort/BSPCompileX/main/0.7.0-beta/assets/bspcompilex-0.7.0-beta-cover.png)

[Русская версия](https://github.com/D3cort/BSPCompileX/blob/main/BSPCompileX-0.7.0-Beta-RU.md)

# BSPCompileX 0.7.0 Beta: what changed since 0.6.0

BSPCompileX 0.6.0 was still, for the most part, a map compiler. Work on 0.7.0 started with a list of small annoyances: opening another tool just to inspect a VMF, trying to work out whether VRAD was busy or stuck, repairing texture paths exported by a modelling package, and manually collecting map files for an addon. That list did not stay small for long.

The result is a much broader application. Map compilation is still the first tab and the simple workflow has not changed, but decompilation, model conversion and texture conversion now have proper places of their own. A normal map build still takes three actions: choose the VMF, leave the preset on Normal, and press Start.

This release is labelled Beta on purpose. The main paths have been through automated tests and real Garry's Mod tools, but Source content is too varied to claim that every old FBX, unusual VMT or heavily modified VMF will work on the first attempt. Anything headed for a public release should still get a final in-game check.

## The short version

- Compiler, decompiler, models and textures now live in separate tabs with separate logs.
- VMF and BSP files can be inspected in the built-in 3D viewport.
- Models can be imported without installing Blender and compared before and after conversion.
- The model pipeline handles LODs, collision, animation and an optional safe reduction to Source's 128-bone limit.
- Images, GIFs and video can be converted to VTF/VMT without leaving the application.
- Builds now show CPU, GPU and memory use alongside stage progress.
- A ZIP packer collects the map and the dependencies it can resolve.
- The interface has 20 languages when Russian and English are included.
- The Windows release is a single executable. Java, Python and Blender are not runtime requirements.

## Map compilation

### The familiar path is still there

The compiler has three presets: Fast for rough iteration, Normal for everyday work, and FullHDR for a final lighting pass. BSPCompileX looks for the Garry's Mod install, `gameinfo.txt`, and the x64 `vbsp`, `vvis` and `vrad` tools. The detected paths can still be overridden when the game lives somewhere unusual.

The file picker now understands why it was opened. A map picker shows map files, the model picker shows 3D formats, and the texture picker includes images and supported video containers. It also searches folders properly. Selecting the root of a drive no longer produces a misleading “no files found” result when valid files exist further down the tree.

The source VMF is treated as read-only during a build. Any conversion needed by the selected options happens on a temporary copy, which is removed when the build finishes or is stopped.

The build pipeline remains easy to follow:

```text
VBSP → VVIS → VRAD → COPY → GAME
```

The package includes x64 wrappers for VBSP, VVIS and VRAD. They select a high-performance adapter through DXGI and run the GPU work that is available for that stage. If a map cannot use a native path, or a supporting runtime is missing, the wrapper falls back to the compatible Garry's Mod tool and records that decision in the log.

It is worth being direct about the GPU side. Source map compilation has not magically become a GPU-only workload. PortalFlow, parts of BSP construction, and a substantial amount of VRAD still run on the CPU. A few experimental FullHDR GPU paths were actually slower on large maps because dispatch, synchronisation and readback cost more than the work they replaced. Auto mode therefore keeps the paths that measured well and avoids the ones that merely make the GPU graph look busy.

### Smart rebuilds and oversized geometry

Smart Rebuild reuses an intermediate result only when the relevant inputs and stage settings still match. Editing geometry, lighting or a preset invalidates the affected cache entry.

On the large map used during development, a cold geometry build fell from 109.166 seconds to 85.792 seconds after the converter and scheduling changes. A warm pass moved from 26.487 to 24.217 seconds. Those figures are not a blanket speed claim; cache effectiveness depends on the map and on what changed between builds.

Titan Geometry is an explicit option for maps with a large amount of `func_detail`. Eligible detail geometry is moved into lit static groups with materials, shadows and collision. It does not remove Source's limits. BSP constraints, vertex limits and validity checks still apply, and the UI says so.

Two failures found while testing this path are worth mentioning. A generated group without a visual material used to stop the converter with `generated model group has no visual material`. A separate physical-lighting process failure could look like a build that had simply stopped making progress. Missing materials now take a logged fallback path, while a failed child process always closes the stage with an error and tears down the remaining process tree.

### FullHDR and model lighting

Some lighting bugs only became obvious after loading the finished BSP in Garry's Mod. One build launched in forced fullbright. Another caused dynamic models and player characters to fade towards black after normal lighting was enabled. The latter was not a single VRAD switch; it involved HDR data, prop lighting and the ownership of physical lightmap samples.

The current FullHDR path preserves both LDR and HDR light data and the corresponding BSP flags. The reference build produced no fully black VHV files, and physical samples were checked against the faces that own them. Polygon shadows, alpha-tested shadows and static-prop lighting were also part of the regression run.

FullHDR can still be expensive. The reference scene contained 197,422 triangles and 1,445,217 unique bake samples, with eight bounces and 1,099,169,627 traced rays. It took roughly 16 minutes on a Core i5-12400. The useful change in 0.7.0 is that the application now shows which stage is doing the work, whether output is still arriving, and what the process is using. A slow calculation and a dead process no longer look identical.

### Progress, resource use and logs

The bottom status row reports the current stage and percentage together with CPU, GPU and application memory use. Peak values are written to the log at the end of the run. The same log records stage backends, fallbacks, exit codes and familiar Source warnings such as leaks, missing materials and hard limits.

Compilation and decompilation no longer write into the same panel. Each tab has its own output, its own error list, and separate Copy, Save and Clear controls. Failure dialogs contain the short version; the complete, selectable output stays in the tab for anyone who needs to diagnose it.

### The map viewport

Selecting a VMF starts the preview load, but compilation does not wait for it. The camera supports mouse look, WASD movement, vertical movement and a Shift speed modifier. The skybox control can display the actual six-sided Source sky, show only sky geometry, or remove it from the preview.

The renderer received more work than its size in the interface might suggest. Depth handling, mip chains, translucent sorting, eye materials, the skybox mask and alpha surfaces all needed fixes. The Complexity panel no longer flickers. Dropping an unrelated file onto a loaded viewport does not destroy the current scene, and camera state is retained when changing a LOD or refreshing a result that does not require a full reset.

The right-click command launches the map in Garry's Mod at the current camera position. It is translated and appears only for map viewports; it was deliberately removed from model and decompiler contexts where it either made no sense or could not provide a reliable position.

Direct3D 11 is the primary renderer. It remained responsive while the test machine was compiling a heavy map, including active camera movement. Direct3D 12 remains available as the second backend, although D3D11 behaved better with a child-window swap chain competing with compiler GPU work.

### Packing a map

The ZIP button scans a completed map and collects the materials, models and related files it can resolve. The BSP is placed at the package root while other content keeps its Garry's Mod folder layout, including `materials` and `models`. Absolute paths, `..` traversal and entries escaping the destination are rejected. The packer has its own self-test rather than relying on a successful manual archive once.

## Native BSP decompilation

The decompiler is now an in-process C++ backend; it does not launch Java or BSPSource. It has a dedicated tab, viewport, output folder and log. The current implementation reconstructs world geometry, brushes, materials, props, displacements and embedded content before writing a VMF.

One of the larger tests used `rp_downtown_v4c.bsp`, a 32.6 MB map. The output contained 4,767 brushes and 657 props and occupied 491,519,248 bytes. The run took about 33 seconds. Repeating it through a Unicode path produced an identical SHA-256. A second map, `c17_courtyyard.bsp`, included 12 displacements; after the displacement reconstruction fix, the resulting VMF passed the stock VBSP compiler again.

## Model conversion

Model import happens inside BSPCompileX. Supported extensions include FBX, OBJ, GLB/glTF, DAE, STL, PLY, 3DS, BLEND, X, X3D, SMD, MD5MESH, LWO, BVH, 3MF, IQM and PMX/PMD, along with the other formats exposed by the bundled importer. Blender is neither launched nor required to be installed.

The source model stays on the left and the Garry's Mod result appears on the right. Converting no longer clears the source viewport. Cameras and animation playback are independent, which makes it possible to pause one side on a useful frame while the other continues. Both viewports offer orbit and free-flight cameras plus lit, unlit and geometry-only views.

Units and up axis are detected automatically but can be overridden. This fixes the common case where an FBX or glTF scene arrives lying on its side because its coordinate system does not match Source.

Texture references are resolved from the scene, beside the model and in nearby `textures` directories. If an exporter stored an obsolete absolute path, the converter continues by looking for the referenced filename. Base colour, normal, emissive, metallic, roughness and opacity inputs are mapped to the closest practical Source material. That mapping cannot always be exact: Source shaders predate modern PBR workflows by a long way.

LODs are listed to the right of the two viewports. Clicking a different level does not move the camera. Automatic LODs can be generated, or manually authored lower-detail models can be supplied. The heavy regression model started at 49,585 triangles and produced levels of 24,938, 13,783 and 10,406 triangles without changing its bounds.

Animation import now keeps the source and compiled previews separate. Fixes cover angle discontinuities, root-bone transforms and motion inherited through helper nodes. Source has a 128-bone limit, so skeleton fitting is a separate checkbox and is off by default. It removes only non-deforming helper nodes and bakes their motion into retained deformation bones. A bone carrying vertex weights is never silently dropped. If the rig still cannot fit safely, conversion stops with a precise error instead of producing an apparently successful but broken animation.

The final output is the normal Garry's Mod set: `MDL`, `VVD`, `VTX`, optional `PHY`, and VTF/VMT materials.

## Textures, GIFs and video

The static texture path accepts PNG, JPEG, TGA, DDS, BMP, TIFF, EXR and other WIC-supported images. It resizes to valid power-of-two dimensions, generates mipmaps, applies normal-map-specific filtering and preserves alpha. The right-hand preview is decoded from the VTF that was actually written, not from a second copy of the source image.

Transparency is covered by a full encode/decode test, including partial alpha values. The generated VMT adds `$translucent` when the content requires it.

Animation controls appear only after an animated input is detected. They cover frame rate, looping, frame count and maximum resolution. Frames are stored in a multi-frame VTF 7.2 and the VMT uses Source's `AnimatedTexture` proxy. GIF and AVI have direct regression coverage. MP4, M4V, MOV, WMV, ASF, MPEG, MKV and WebM are decoded through Windows Media Foundation, so the necessary codec still has to exist on the machine.

Long videos are bounded by memory, frame-count, resolution and VTF-size limits. A malformed file reports an error without wiping the previous successful preview.

Both asset tabs now have a queue. Multiple files can be processed in order, selected jobs can be removed, and completed entries can be cleared. Files with the same stem in different folders receive distinct stable suffixes instead of silently overwriting each other.

## Languages and packaging

In addition to Russian and English, 0.7.0 includes French, German, Polish, Turkish, Portuguese, Spanish, Ukrainian, Chinese, Korean, Czech, Slovak, Italian, Dutch, Japanese, Romanian, Hungarian, Arabic and Swedish.

The first run uses the Windows language when it is supported and falls back to English otherwise. The choice can be changed from the gear menu. A catalog test checks the locale list, required keys and English fallback so a UI change cannot quietly drop an entire language.

0.7.0 also introduces a localized proprietary freeware license. The first launch presents the complete agreement in a scrollable window and requires an explicit checkbox before continuing. Declining closes the application without recording acceptance. The same text remains available under Settings → License, and a new license revision can request acceptance again. Official unmodified binaries remain free to use in personal and commercial projects without royalties, payment or attribution.

The release is a native x64 C++20 application linked with the static MSVC runtime. Users receive one EXE and do not need a separate Visual C++ Redistributable or a directory full of runtime DLLs. Internal components are stored in an authenticated container and verified before execution.

## Testing the release

The main QA pass ran on Windows 11 with a Core i5-12400, 32 GB of RAM and a GeForce RTX 4060 Ti 8 GB, using the current x64 Garry's Mod tools.

There are 15 registered CTest cases covering the asset converter, content scanner, ZIP packer, lighting encoding and physics, decompiler and updater path security, process start/stop behaviour, GLB viewport loading, oversized-path selection, legacy Source tool paths, translation and license catalogs, independent log routing, and GPU VRAD argument handling. All 15 passed in Release before the package was prepared. The packaged single-file EXE then ran its embedded self-tests independently.

The model matrix included GLB, animated and static FBX, BLEND, DAE, STL, PLY, X and 3DS. Several scenes went through the full StudioMDL route, with checks for the MDL/VVD/VTX/PHY set, LOD counts, finite coordinates and normals, bounds and degenerate triangles.

Map coverage included:

- a small sealed `smoke_room.vmf`, which completed stock VBSP, VVIS and VRAD with exit code 0;
- the 32.6 MB `rp_downtown_v4c.bsp` decompilation case;
- `c17_courtyyard.bsp` for displacement round-trip testing;
- a 772 MB Workshop VMF, parsed and selected in 6.043 seconds.

Tests used temporary directories and did not write their output into the installed game. After 100 resize and tab-switch cycles, Win32 handles stayed at 373→373, GDI objects at 31→31, and USER objects at 20→20. The formal QA run logged 15 defects: 6 High, 5 Medium and 4 Low. All were fixed and rerun before this release candidate.

## Known boundaries of the Beta

- Windows x64 and an installed Garry's Mod toolchain are required.
- FullHDR and full VVIS can legitimately take a long time on large, open maps.
- Titan Geometry does not remove the engine's hard limits.
- Not every rig can be reduced to 128 bones without changing deformation.
- PBR conversion is limited by the materials Source can represent.
- Video support depends on the codecs available to Windows Media Foundation.
- Rare formats, old FBX variants and custom shaders may still need manual preparation.

Existing 0.6.0 users do not need to relearn the compiler. Start with the Normal preset and use the new tabs when a job calls for them. The most useful input for the next build is not another speculative checkbox; it is a real VMF, BSP, model or material that exposes a repeatable compatibility problem.
