# Shadow Scalability Plan

## Goal

- Reduce worst-case shadow cost in large scenes.
- Keep existing scenes unchanged when disabled.

## Current Branch Baseline

- This branch already has optional shadow budget controls under:
  - `rendering/atom_forward_scale/shadow_budget/enabled`
  - `rendering/atom_forward_scale/shadow_budget/max_shadow_maps_per_frame`
  - `rendering/atom_forward_scale/shadow_budget/static_light_cache_enabled`
  - `rendering/atom_forward_scale/shadow_budget/distance_priority_enabled`
  - `rendering/atom_forward_scale/shadow_budget/screen_size_priority_enabled`
  - `rendering/atom_forward_scale/shadow_budget/movement_priority_enabled`
  - `rendering/atom_forward_scale/shadow_budget/movement_priority_weight`
  - `rendering/atom_forward_scale/shadow_budget/offscreen_skip_enabled`
  - `rendering/atom_forward_scale/shadow_budget/min_update_interval_frames`
- Keep this namespace. Do not add a second `shadows/*` namespace.

## File Hooks

- `renderer_scene_cull.cpp`
  - Shadow candidate creation.
  - Coverage and distance priority inputs.
  - Final redraw gate before `_light_instance_update_shadow()`.
  - Existing skipped-shadow counter.
- `light_storage.cpp`
  - Shadow atlas slot ownership and redraw checks.
- `rendering_server.cpp`
  - Project setting registration.

## Phase 1

- Counters only.
- No sorting changes.
- No redraw logic changes.
- Print:
  - visible shadow-casting lights
  - shadow maps rendered
  - shadow maps skipped
  - shadow skips by cause: budget, min interval, offscreen
  - atlas usage
  - atlas wasted estimate

## Phase 2

- Extend existing shadow-budget path, not a new system.
- Priority inputs to keep:
  - screen coverage
  - distance to camera
  - dirty/pending preference
- Later optional inputs:
  - offscreen skip
  - movement priority
  - manual bias

## Current Movement Priority Hook

- `renderer_scene_cull.cpp`
  - When `movement_priority_enabled=true`, positional shadow candidates get a weighted boost if the light is dirty or already pending.
  - Weight comes from `movement_priority_weight`.
  - Disabled mode keeps old sort behavior.

## Current Manual Priority Hook

- `scene/3d/light_3d.cpp`
  - `set_shadow_priority(float)` and `get_shadow_priority()` are now available as script/API methods.
  - No inspector property yet.
- `renderer_scene_cull.cpp`
  - Manual priority multiplies the candidate score with default `1.0` meaning no change.
  - Higher values push authored-important lights ahead when budget is tight.

## Current Offscreen Skip Hook

- `renderer_scene_cull.cpp`
  - When `offscreen_skip_enabled=true`, positional shadows that need redraw but whose light volume does not intersect the camera frustum are skipped and left pending.
  - Default off.
  - Directional shadow behavior unchanged.
  - Debug output now distinguishes total skips from budget, interval, and offscreen skip causes.

## Safety Rules

- Default off.
- Directional sun stability first.
- Never globally disable shadows by accident.
- Keep editor viewport behavior stable.
