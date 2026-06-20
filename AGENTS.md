# Agent Notes

## Read First

- Start with `README.md`, `SConstruct`, `.editorconfig`, `.pre-commit-config.yaml`, and `.github/workflows/*.yml`.
- Trust executable sources over prose when they differ.
- For renderer-scale work, keep `docs/atom_forward_scale/` current before behavior changes.

## Build / Verify

- Windows editor build: `scons platform=windows target=editor dev_build=yes debug_symbols=yes d3d12=no compiledb=yes -j%NUMBER_OF_PROCESSORS%`.
- `compiledb=yes` writes `compile_commands.json`; use it for IDE indexing and manual clang-tidy.
- CI smoke test is `--version`, `--help`, then `--test --force-colors` on the built editor.
- GDExtension compatibility path: `scons --directory=./tests/compatibility_test` then `./tests/compatibility_test/run_compatibility_test.py`.

## Style / Hooks

- C++ and GLSL use tabs; `SConstruct`, `SCsub`, and Python use spaces per `.editorconfig`.
- Pre-commit runs clang-format on C++/GLSL and ruff on Python/SCons files.
- Manual clang-tidy is `pre-commit run --hook-stage manual clang-tidy`.
- Do not edit generated `*.gen.h` files; edit the source `.glsl` or template input instead.

## Rendering Hotspots

- `servers/rendering/renderer_scene_cull.cpp` = CPU visibility cull, shadow scheduling, scene submit.
- `servers/rendering/renderer_rd/renderer_scene_render_rd.cpp` = render-data packing into RD.
- `servers/rendering/renderer_rd/forward_clustered/` = main Forward+ path.
- `servers/rendering/renderer_rd/storage_rd/light_storage.*` = lights, shadow atlas, light buffers.
- `servers/rendering/renderer_rd/cluster_builder_rd.*` and `servers/rendering/renderer_rd/shaders/cluster_*.glsl` = clustered light build.

## Safe Change Rule

- Keep renderer changes optional or safe by default.
- Preserve normal scenes, existing lights, existing shadows, existing materials, and editor viewport behavior.
