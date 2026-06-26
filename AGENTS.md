# AGENTS.md

## Scope
- Repo = Godot 4.7 engine fork, not game project.
- Main runtime entrypoint = `main/main.cpp`.
- Most engine work lands in `core/`, `servers/`, `scene/`, `platform/`, `modules/`, `editor/`.
- Rendering work often spans `scene/resources/material.cpp` shader-string generation, `servers/rendering/renderer_rd/**`, and GLSL in `servers/rendering/renderer_rd/shaders/**`.

## Build
- Windows editor build used in this repo: `scons platform=windows target=editor dev_build=yes`.
- Add unit tests at build time with `tests=yes`.
- Useful DB generation: `scons compiledb=yes compiledb_gen_only=yes platform=windows target=editor dev_build=yes`.
- Windows outputs land in `bin/`, notably `godot.windows.editor.dev.x86_64.exe` and `.console.exe`.
- Build regenerates `*.glsl.gen.h`, `*.gen.cpp`, `version_hash.gen.cpp`, docs/theme generated headers. Do not hand-edit generated `*.gen.*` files.

## Test
- C++ tests are doctest-based. Build with `tests=yes`, then run binary with `--test`.
- Windows example: `bin/godot.windows.editor.dev.x86_64.console.exe --test`.
- Doctest filters pass through after `--test`; use them for focused runs.
- Scene/editor-tagged tests bootstrap mock display/rendering in `tests/test_main.cpp`; failures there can depend on engine init, not only local code.

## Format / Lint
- Root `.editorconfig`: tabs everywhere except Python/SCons/YAML/XML-style project files; max line length 120; LF endings.
- Python + SCons style enforced by Ruff from `pyproject.toml`.
- Pre-commit is source of truth for local checks: `clang-format` for C/C++/Java/`.inc`, separate `clang-format-glsl` for `.glsl`, Ruff, mypy, codespell, XML/codeowners/include/header guard/file-format validators, ESLint for web bits.
- Manual-only clang-tidy hook requires fresh `compile_commands.json`: `pre-commit run --hook-stage manual clang-tidy`.

## Rendering Gotchas
- `scene/resources/material.cpp` generates shader source strings for `BaseMaterial3D`; helpers declared only in renderer GLSL are **not** visible there. Any conditionals in generated material shader code must be resolved in C++ string generation or use existing spatial shader built-ins only.
- Shared emission/light response after user/material shader resolution belongs in renderer GLSL (`scene_forward_clustered.glsl`, `scene_forward_mobile.glsl`) via `scene_data` uniforms.
- CPU-side renderer tuning/settings registration usually belongs near `RenderingServer::init()` in `servers/rendering/rendering_server.cpp`.
- Physical light/exposure normalization already threads through `RenderSceneDataRD` and scene shaders; reuse those paths before adding new per-material hacks.

## Docs / API
- If changing exposed engine API, update class reference XML under `doc/classes` or module/platform `doc_classes`; pre-commit has `make-rst` and `doc-status` hooks for this.

## Fork Notes
- This fork already carries Atom-style lighting/emission customizations in renderer/material code. When touching those areas, preserve stock Godot behavior behind project settings switches unless explicitly changing defaults.
