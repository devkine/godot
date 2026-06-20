# Renderer Map

## Main Flow

1. `servers/rendering/renderer_viewport.cpp`
   Records viewport timing and calls 3D render path.
2. `servers/rendering/renderer_scene_cull.cpp`
   Builds camera data, runs CPU visibility cull, schedules shadow work, submits scene.
3. `servers/rendering/renderer_rd/renderer_scene_render_rd.cpp`
   Packs `RenderDataRD`, selects renderer implementation, enters Forward+ render path.
4. `servers/rendering/renderer_rd/forward_clustered/render_forward_clustered.cpp`
   Renders shadows, GI, cluster build, opaque/transparent passes.

## Key Files

- `servers/rendering/renderer_scene_cull.cpp`
  - CPU cull entry.
  - Visible instance/light lists.
  - Shadow update decisions.
- `servers/rendering/renderer_scene_cull.h`
  - `InstanceCullResult`, `CullData`, render entry signatures.
- `servers/rendering/renderer_rd/renderer_scene_render_rd.cpp`
  - Builds `RenderDataRD`.
- `servers/rendering/renderer_rd/storage_rd/render_data_rd.h`
  - Frame render payload passed into Forward+.
- `servers/rendering/renderer_rd/forward_clustered/render_forward_clustered.cpp`
  - Shadow render passes.
  - Cluster build trigger.
  - Render list fill and draw submission.
- `servers/rendering/renderer_rd/storage_rd/light_storage.cpp`
  - Light buffer upload.
  - Shadow atlas allocation/update policy.
- `servers/rendering/renderer_rd/storage_rd/light_storage.h`
  - Shadow atlas data structures.
- `servers/rendering/renderer_rd/cluster_builder_rd.cpp`
  - Cluster buffer clear, raster mark, compute pack.
- `servers/rendering/renderer_rd/cluster_builder_rd.h`
  - Per-type cluster counts and CPU feed path.
- `servers/rendering/renderer_rd/shaders/cluster_render.glsl`
  - Marks cluster element usage.
- `servers/rendering/renderer_rd/shaders/cluster_store.glsl`
  - Packs final cluster bitmasks and z-slice ranges.
- `servers/rendering/rendering_device.cpp`
  - Timestamp capture API.
- `drivers/vulkan/rendering_device_driver_vulkan.cpp`
  - Vulkan timestamp query implementation.

## Best Hook Points

- Debug stats: `renderer_scene_cull.cpp`, `render_forward_clustered.cpp`, `cluster_builder_rd.*`, `renderer_viewport.cpp`.
- Shadow budgeting: `renderer_scene_cull.cpp`, `light_storage.cpp`.
- Cluster diagnostics: `cluster_builder_rd.*`, `cluster_store.glsl`.
- GPU visibility prototype: `renderer_scene_cull.cpp` first, later `render_forward_clustered.cpp` draw submission path.
