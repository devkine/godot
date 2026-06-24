# Benchmark Scene Plan

## Goal

- Reproduce large-world light and shadow pressure in one repeatable test.

## Scene Recipe

1. Large outdoor terrain or proxy ground mesh.
2. `100` non-shadow omni lights.
3. `100` shadow omni lights.
4. `50` spot lights.
5. `1` directional sun.
6. Many static meshes across wide distance.
7. Many dynamic meshes near the camera path.
8. Camera flythrough across sparse and dense regions.

## Settings Matrix

1. Baseline:
   - all Atom debug settings off
   - shadow budget off
2. Debug counters on:
   - `rendering/atom_forward_scale/debug/lights_and_shadows_enabled=true`
   - print settings as needed
3. Later budget run:
   - `rendering/atom_forward_scale/shadow_budget/enabled=true`
   - optional: `rendering/atom_forward_scale/shadow_budget/movement_priority_enabled=true`
   - optional: `rendering/atom_forward_scale/shadow_budget/offscreen_skip_enabled=true`

## Measure

- FPS
- GPU frame time
- CPU cull time
- visible omni, spot, directional lights
- visible shadow-casting lights
- shadow maps rendered
- skipped shadow updates
 - skipped shadow updates by cause
- shadow atlas usage
- cluster average
- cluster max
- cluster overflow count
- draw calls
- VRAM if available

## Capture Method

- Console/log first.
- Fixed viewport size.
- Same camera path each run.
- Same build for before/after comparisons.

## First Table

- scene name
- viewport size
- visible lights
- visible shadow lights
- shadow maps rendered
- skipped shadow updates
- atlas usage
- cluster avg
- cluster max
- cluster overflow
- CPU cull ms
- GPU frame ms
- notes
