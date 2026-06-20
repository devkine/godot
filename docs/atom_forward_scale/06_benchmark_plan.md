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
