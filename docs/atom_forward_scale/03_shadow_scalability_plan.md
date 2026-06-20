# Shadow Scalability Plan

## Goal

- Reduce worst-case shadow cost in large scenes.
- Preserve current behavior by default.

## Planned Settings

- `rendering/atom_forward_scale/shadow_budget/enabled`
- `rendering/atom_forward_scale/shadow_budget/max_shadow_maps_per_frame`
- `rendering/atom_forward_scale/shadow_budget/static_light_cache_enabled`
- `rendering/atom_forward_scale/shadow_budget/distance_priority_enabled`
- `rendering/atom_forward_scale/shadow_budget/screen_size_priority_enabled`
- `rendering/atom_forward_scale/shadow_budget/min_update_interval_frames`

## File Hooks

- `servers/rendering/renderer_scene_cull.cpp`
  - Coverage and priority inputs near positional shadow handling.
  - Final redraw gate before `_light_instance_update_shadow()`.
- `servers/rendering/renderer_rd/storage_rd/light_storage.cpp`
  - Shadow atlas slot ownership and realloc policy.
- `servers/rendering/rendering_server.cpp`
  - Project setting registration.

## Implementation Order

1. Add debug counters for shadow scheduling.
2. Add settings with default behavior matching current Godot.
3. Add shadow priority sort/budget gate.
4. Add static shadow cache safety checks.
5. Add skip counters and warnings.

## Safety Notes

- Default off.
- Never disable existing shadows globally.
- Directional shadows need separate handling from positional atlas updates.
