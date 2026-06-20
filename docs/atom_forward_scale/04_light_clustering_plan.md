# Light Clustering Plan

## Goal

- Improve many-light scalability without blind limit increases.

## File Hooks

- `servers/rendering/renderer_rd/storage_rd/light_storage.cpp`
  - Better CPU-side light rejection before upload.
- `servers/rendering/renderer_rd/forward_clustered/render_forward_clustered.cpp`
  - Cluster begin/bake timing and light count context.
- `servers/rendering/renderer_rd/cluster_builder_rd.h`
  - Per-type accepted counts.
- `servers/rendering/renderer_rd/cluster_builder_rd.cpp`
  - Cluster build diagnostics.
- `servers/rendering/renderer_rd/shaders/cluster_store.glsl`
  - Final packed cluster occupancy source.

## First Steps

1. Add per-frame cluster diagnostics.
2. Track accepted lights per type.
3. Track capped/rejected elements at CPU feed point.
4. Add overflow warnings before changing limits.

## Later Steps

- Separate shadow-casting vs non-shadow lights internally where useful.
- Better data layout if diagnostics prove bottleneck.
- Raise limits only after rejection and packing quality improve.
