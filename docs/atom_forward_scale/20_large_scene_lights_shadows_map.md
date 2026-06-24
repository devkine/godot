# Large Scene Lights Shadows Map

## Goal

- Map where Forward+ handles visible lights, shadow rendering, shadow atlas use, and cluster building.
- Keep future changes small and optional.

## Main Flow

1. `servers/rendering/renderer_scene_cull.cpp`
   - Culls scene instances and visible lights.
   - Counts visible omni, spot, directional, and shadow-casting lights.
   - Selects shadow work and applies shadow budget logic already present on this branch.
2. `servers/rendering/renderer_rd/forward_clustered/render_forward_clustered.cpp`
   - Calls `light_storage->update_light_buffers(...)`.
   - Bakes clusters.
   - Copies cluster debug stats into `RenderInfo`.
3. `servers/rendering/renderer_rd/storage_rd/light_storage.cpp`
   - Uploads visible light data for GPU use.
   - Owns shadow atlas allocation and usage queries.
4. `servers/rendering/renderer_rd/cluster_builder_rd.cpp`
   - Clears, rasterizes, and packs clustered light data.
   - Already exposes debug stats: cluster count, average lights, max lights, overflow count.
5. `servers/rendering/renderer_viewport.cpp`
   - Current console print point for aggregated renderer stats.

## Visible Lights

- `renderer_scene_cull.cpp`
  - Directional visibility counts near `visible_directional_lights`.
  - Omni and spot visibility counts after `scene_cull_result.lights` is built.
  - `shadow_casting_lights_visible` is tracked here too.

## Shadow Rendering

- `renderer_scene_cull.cpp`
  - Directional shadows are prepared first.
  - Positional shadow candidates are built with coverage and distance data.
  - Branch already contains optional shadow budget sorting and minimum update interval logic under `rendering/atom_forward_scale/shadow_budget/*`.
  - `skipped_shadow_updates` and `shadow_maps_rendered` already exist as counters.

## Shadow Atlas

- `light_storage.cpp`
  - `shadow_atlas_update_light(...)` owns slot reuse and redraw decisions.
  - `shadow_atlas_get_usage(...)` returns occupied slot percentage.
  - Easy wasted-space estimate is `100 - usage`.

## Cluster Building

- `forward_clustered/render_forward_clustered.cpp`
  - `current_cluster_builder->begin(...)`
  - `light_storage->update_light_buffers(...)`
  - `current_cluster_builder->bake_cluster()`
- `cluster_builder_rd.cpp`
  - `collect_debug_stats(...)` already reports:
    - cluster count
    - average lights per non-empty cluster
    - max lights in cluster
    - overflow count

## Existing Debug Data

- `servers/rendering/rendering_server_types.h`
  - `RenderInfo` already has:
    - visible 3D instances
    - visible omni, spot, directional lights
    - visible shadow-casting lights
    - shadow maps rendered
    - shadow atlas usage
    - skipped shadow updates
    - cluster count
    - cluster average lights per non-empty cluster
    - cluster max lights
    - cluster overflow count
    - CPU cull time
    - GPU frame time

## Best First Patch

- No renderer behavior change.
- Add debug project settings.
- Reuse existing `RenderInfo` fields.
- Print grouped console stats from `renderer_viewport.cpp`.
