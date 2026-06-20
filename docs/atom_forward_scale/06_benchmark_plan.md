# Benchmark Plan

## Measure

- Visible 3D instances.
- Visible omni/spot/directional lights.
- Shadow-casting visible lights.
- Shadow maps rendered.
- Shadow atlas usage.
- Skipped shadow updates.
- Cluster count.
- Average lights per non-empty cluster.
- Max lights in one cluster.
- Cluster overflow count.
- CPU cull time.
- GPU frame time when available.

## Test Scenes

1. Baseline small normal Godot scene.
2. Many static meshes, few lights.
3. Many meshes, many omni/spot lights.
4. Many shadow-casting lights.
5. Large world spread-out content.

## Capture Method

- Console/log only first.
- Compare default settings vs optional features enabled.
- Record camera path and viewport size.
- Keep same renderer, same project, same build.

## Before/After Table

- Scene name.
- Object count.
- Light count.
- Shadow count.
- CPU cull ms.
- GPU frame ms.
- Notes on overflow or skipped work.

## Measured Notes

- Temp benchmark projects used: `atom_bench_baseline` and `atom_bench_enabled` under `C:\Users\Eldin\AppData\Local\Temp\opencode\`.
- Viewport: `1280x720`, Forward+, camera orbit path, console stats enabled.
- Enabled settings during tests:
  - `rendering/atom_forward_scale/shadow_budget/enabled=true`
  - `rendering/atom_forward_scale/shadow_budget/max_shadow_maps_per_frame=8`
  - `rendering/atom_forward_scale/shadow_budget/static_light_cache_enabled=true`
  - `rendering/atom_forward_scale/shadow_budget/distance_priority_enabled=true`
  - `rendering/atom_forward_scale/shadow_budget/screen_size_priority_enabled=true`
  - `rendering/atom_forward_scale/shadow_budget/min_update_interval_frames=2`

### Small Normal

- Visible objects: about `81-82`.
- Visible lights: `2 omni`, `0 spot`, `1 directional`.
- Baseline steady state:
  - `shadow_maps=4`
  - `cpu_cull` about `0.08-0.19ms`
- Enabled steady state after startup:
  - `shadow_maps=4`
  - `cpu_cull` about `0.08-0.23ms`
- Startup burst was reduced from a persistent `10` to first-frame warmup only.

### Many Shadow Lights

- Visible objects: about `275-347` depending on camera angle.
- Visible lights: about `80-81 omni`, `8 spot`, `1 directional`.
- Baseline animated-light run:
  - `shadow_maps` about `251-252`
  - `cpu_cull` about `4.1-6.7ms`
- Enabled animated-light run:
  - `shadow_maps=10`
  - `cpu_cull` about `0.5-0.9ms`
- Main benefit shows up when many shadow-casting lights animate at once.

### GPU Time Note

- `gpu_frame_time_ms` can be unavailable on current timestamp path.
- Invalid GPU timestamp deltas are clamped to `0.00ms` to avoid misleading large values.
