# Agent Notes

## Read First

- Start with `README.md`, `SConstruct`, `.editorconfig`, `.pre-commit-config.yaml`, `.github/workflows/*.yml`, then `docs/atom_forward_scale/` for this branch.
- Trust executable sources over prose when they differ.

## Build / Verify

- Windows editor build: `scons platform=windows target=editor dev_build=yes debug_symbols=yes d3d12=no compiledb=yes -j%NUMBER_OF_PROCESSORS%`.
- `compiledb=yes` writes `compile_commands.json`; use it for IDE indexing and manual clang-tidy.
- CI smoke test order: `<bin> --version`, `<bin> --help`, `<bin> --test --force-colors`.
- GDExtension compatibility: `scons --directory=./tests/compatibility_test` then `./tests/compatibility_test/run_compatibility_test.py`.

## Style / Hooks

- C++ and GLSL use tabs; `SConstruct`, `SCsub`, and Python use spaces.
- Pre-commit runs clang-format on C++/GLSL and ruff on Python/SCons files.
- Manual clang-tidy: `pre-commit run --hook-stage manual clang-tidy`.
- Do not edit generated `*.gen.h` files; edit the source `.glsl` or template input instead.

## Rendering Map

- `servers/rendering/renderer_scene_cull.cpp`: CPU cull, shadow scheduling, scene submit.
- `servers/rendering/renderer_rd/renderer_scene_render_rd.cpp`: render-data packing into RD.
- `servers/rendering/renderer_rd/forward_clustered/`: main Forward+ path.
- `servers/rendering/renderer_rd/storage_rd/light_storage.*`: light buffers, light upload, shadow atlas.
- `servers/rendering/renderer_rd/cluster_builder_rd.*`: clustered light build and debug stats.
- `servers/rendering/renderer_viewport.cpp`: current console print hook for renderer/light/shadow stats.
- `servers/rendering/renderer_rd/shaders/scene_forward_lights_inc.glsl`: shared light shading code.
- `scene/3d/light_3d.*`: node-side light color, intensity, temperature, project to renderer.

## Branch Notes

- Large-scene shadow controls already live under `rendering/atom_forward_scale/shadow_budget/*`; keep that namespace instead of introducing `shadows/*`.
- Current debug counters route through `RenderingServerTypes::RenderInfo` and print from `renderer_viewport.cpp`; reuse those fields before adding new counters.
- `Light3D.set_shadow_priority()` / `get_shadow_priority()` exist as script/API-only hooks for shadow budget sorting. No inspector property yet.

## Safe Change Rule

- Keep renderer changes optional or safe by default.
- Preserve normal scenes, existing lights, existing shadows, existing materials, and editor viewport behavior.
