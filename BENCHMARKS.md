# Performance and comparison

The Babbdistreet GIF is a continuous excerpt of real compilation at **1.5× playback**. It is not the complete build or a timing benchmark.

## CompilePal comparison

CompilePal documents packing, error checking, batch compilation, cubemaps, navigation generation and custom steps/plugins. Sources: [README](https://github.com/ruarai/CompilePal), [quick-start guide](https://github.com/ruarai/CompilePal/blob/master/Guides/QuickStart.md).

| Workflow | BSPCompileX 0.7.9 Beta | CompilePal |
|---|---|---|
| Source SDK compilation | Yes | Yes |
| Packing and error reporting | Yes | Yes |
| Live native VMF/BSP preview | Built in | Not advertised in the cited guide |
| Integrated BSP decompiler | Built in | Not advertised in the cited guide |
| Model/texture editors | Beta studio workspaces | Not advertised in the cited guide |
| Oversized detail geometry | Titan model conversion | Depends on selected compiler/custom steps |
| Custom compile steps/plugins | Fixed integrated workflows | Documented feature |
| Identical stock-tool speed comparison | Not measured head to head | Not measured head to head |

There is no measured “X times faster than CompilePal” claim in this release. Different tool flags, GPU paths, cache states and lighting quality cannot be presented as an equivalent-speed comparison.

## Reproducible timing method

1. Use identical VMF/content bytes and record SHA-256, SDK versions and flags.
2. Record CPU, GPU, RAM, thread count, drivers and power settings.
3. Run one tool at a time, with matching quality settings.
4. Separate cold builds, warm model caches and unchanged-map Smart Rebuild.
5. Run at least three repeats and publish median, range and stage logs.
6. Verify geometry, props, materials, displacements and lighting. A failed build has no valid completion-time ratio.

The original Babbdistreet stock-VBSP pass reaches Source's vertex limit; the demonstration then enters Titan recovery. The extra recovery work is not directly comparable to a completed ordinary stock compilation.
