# GPU Visibility Plan

## Goal

- Add experimental GPU frustum culling path.
- Keep CPU culling as default and validation reference.

## Prototype Scope

- Upload instance bounds.
- Compute frustum culling on GPU.
- Produce visible instance list.
- Compare GPU list against CPU result.

## File Hooks

- `servers/rendering/renderer_scene_cull.cpp`
  - Best first validation hook after CPU cull and before scene submit.
- `servers/rendering/renderer_scene_cull.h`
  - Add debug/validation state if needed.
- `servers/rendering/renderer_rd/forward_clustered/render_forward_clustered.cpp`
  - Later integration point for instance packing or indirect submission.
- `servers/rendering/rendering_device.*`
  - Compute buffers, readback, timestamps.
- `drivers/vulkan/*`
  - Backend capability checks if needed.

## Rules

- Experimental only.
- CPU path stays authoritative first.
- Validation mode must catch mismatches.
- Later features wait: occlusion, HZB, LOD, indirect draw, meshlets.
