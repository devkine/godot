# Godot 4 Atom Edition

<p align="center">
  <a href="https://godotengine.org">
    <img src="misc/logo/logo_outlined.svg" width="400" alt="Godot Engine logo">
  </a>
</p>

## What This Fork Is

**Godot 4 Atom Edition** is a custom Godot 4 fork focused on two renderer goals:

- optional Atom-inspired photometric / filmic light behavior for Forward+
- better scalability for large 3D scenes with many lights and shadows

This is still Godot Forward+. It is not a renderer replacement, not deferred rendering, and not a full O3DE Atom port.

## Lighting At A Glance

| Mode | Standard Godot 4 | Godot 4 Atom Edition |
| --- | --- | --- |
| Default behavior | Classic Godot light behavior | Still classic Godot by default |
| Optional Atom mode | Not present | `rendering/atom_forward_scale/light_behavior/enabled=true` |
| Light color role | Standard Godot light color | Optional color-as-energy-filter mode |
| Kelvin + color | Godot physical-light-units path | Same base path, plus Atom filter option |
| Energy baseline | Godot defaults | Atom mode normalized so `light_energy = 1.0` stays practical |
| Spot response | Standard Godot behavior | Optional solid-angle-relative spot scaling |
| Debug RGB print | Not present | Optional visible-light RGB logging |

## Visual Comparison

Replace these placeholder paths with the real screenshot files when you place them in the repo.

### Atom Inspired Fork

![Godot 4 Atom Edition lighting comparison](docs/atom_forward_scale/images/atom_edition.png)

### Standard Godot 4

![Standard Godot 4 lighting comparison](docs/atom_forward_scale/images/godot4_standard.png)

## Hard Rules For This Fork

- default Godot behavior must stay intact
- new renderer features must be optional and disabled by default
- existing projects should continue to open and render normally
- changes should stay incremental and localized

## Current Fork Changes

### Atom Photometric Lighting Mode

- project settings under `rendering/atom_forward_scale/light_behavior/*`
- optional color-as-energy-filter behavior
- optional debug print of final visible-light RGB
- Atom mode normalized so `light_energy = 1.0` stays the practical baseline when enabled
- spot lights scale relative to cone solid angle from the default 45 degree baseline

Main docs:

- `docs/atom_forward_scale/12_atom_photometric_lighting_mode.md`

### Large Scene Light / Shadow Diagnostics

- project settings under `rendering/atom_forward_scale/debug/*`
- console-only light, shadow, and cluster stats
- cluster pressure warnings for overflow and near-budget occupancy
- skip breakdown counters for:
  - budget skips
  - min-interval skips
  - offscreen skips

Main docs:

- `docs/atom_forward_scale/20_large_scene_lights_shadows_map.md`
- `docs/atom_forward_scale/21_shadow_scalability_plan.md`
- `docs/atom_forward_scale/22_light_culling_plan.md`
- `docs/atom_forward_scale/23_cluster_debug_plan.md`
- `docs/atom_forward_scale/24_benchmark_scene_plan.md`

### Shadow Budget Controls

Optional shadow scheduling controls already live under:

- `rendering/atom_forward_scale/shadow_budget/enabled`
- `rendering/atom_forward_scale/shadow_budget/max_shadow_maps_per_frame`
- `rendering/atom_forward_scale/shadow_budget/static_light_cache_enabled`
- `rendering/atom_forward_scale/shadow_budget/distance_priority_enabled`
- `rendering/atom_forward_scale/shadow_budget/screen_size_priority_enabled`
- `rendering/atom_forward_scale/shadow_budget/movement_priority_enabled`
- `rendering/atom_forward_scale/shadow_budget/movement_priority_weight`
- `rendering/atom_forward_scale/shadow_budget/offscreen_skip_enabled`
- `rendering/atom_forward_scale/shadow_budget/min_update_interval_frames`

### Script / API Hooks Added In This Fork

- `Light3D.set_shadow_priority(float)`
- `Light3D.get_shadow_priority()`

This is currently script/API-only. No inspector property yet.

## Build

Windows editor build used on this branch:

```text
scons platform=windows target=editor dev_build=yes debug_symbols=yes d3d12=no compiledb=yes -j%NUMBER_OF_PROCESSORS%
```

Output binaries:

- `bin/godot.windows.editor.dev.x86_64.exe`
- `bin/godot.windows.editor.dev.x86_64.console.exe`

`compiledb=yes` also writes `compile_commands.json`.

## Where To Start Reading

- `AGENTS.md`
- `docs/atom_forward_scale/00_goal.md`
- `docs/atom_forward_scale/01_renderer_map.md`
- `docs/atom_forward_scale/12_atom_photometric_lighting_mode.md`
- `docs/atom_forward_scale/20_large_scene_lights_shadows_map.md`

## Key Renderer Files

- `servers/rendering/renderer_scene_cull.cpp`
- `servers/rendering/renderer_rd/forward_clustered/render_forward_clustered.cpp`
- `servers/rendering/renderer_rd/storage_rd/light_storage.cpp`
- `servers/rendering/renderer_rd/cluster_builder_rd.cpp`
- `servers/rendering/renderer_viewport.cpp`
- `scene/3d/light_3d.cpp`

## Upstream Base

This fork is based on Godot Engine and keeps upstream Godot structure, licensing, and overall renderer architecture.

- Upstream project: https://godotengine.org
- Upstream source: https://github.com/godotengine/godot
- License: MIT
