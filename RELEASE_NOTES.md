# BSPCompileX 0.7.2 Beta

0.7.2 focuses on two workflows that needed stricter Source compatibility: compiling very large Titan Geometry maps and turning skinned models into usable Garry's Mod assets.

## Changes

- Fixed a false Titan Geometry failure when a very small number of generated vertices could not be matched back to the lighting source. The safety gate now accepts at least 99.9% ownership coverage, reports the exact counts, and retains conservative self-shadowing for the unmatched remainder.
- Added automatic jointed physics for skinned imports. Skeletons, weights and animation clips are preserved, while the generated QC uses `$collisionjoints` and up to 32 local physics bodies for Garry's Mod ragdolls.
- Improved automatic ragdoll hulls for animals and other unusual rigs. Wide surfaces such as wings are partitioned around nearby rest-pose bones instead of becoming one oversized box across empty space.
- Split map ZIP export into two clear choices. An editable VMF package is available immediately; a compiled BSP package becomes available only after the current map completes successfully.
- VMF and BSP packages keep materials, models, textures, sounds, particles and scripts in their Garry's Mod directory layout. The chosen map file remains at the archive root.
- Added translations for the new ZIP choices in every supported interface language.

## Verification

The Windows x64 Release build passed all 19 registered CTest cases. A full FullHDR Titan Geometry build of `Babbdistreet (3).vmf` completed successfully with 456 generated models, 5,308 solids and 3,503 packed files. The final lighting report recorded no all-black VHV files and no seam groups above 5%.

The model converter was also tested with a 139-node animated dragon FBX. It produced a 119-bone Source model, four animation clips, four LOD levels and a valid `.phy` containing `solid` and `ragdollconstraint` sections.

The protected Windows package was smoke-tested after signing. Its Authenticode signature and SHA-256 checksum were verified before upload.

## Supported systems

The supported target is Windows 10 or Windows 11 on an x64 PC with the x64 Garry's Mod tools installed. A separate Visual C++ Redistributable is not required. Linux, macOS, Windows on ARM and systems without the Garry's Mod toolchain are not supported.

Automatic ragdoll constraints are intentionally generic so they remain safe across unrelated rigs. Models that need anatomically exact joint limits can still be refined later with a custom QC.
