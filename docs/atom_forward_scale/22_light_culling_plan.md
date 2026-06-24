# Light Culling Plan

## Goal

- Improve many-light scalability without blind light limit increases.
- Measure pressure first, reject smarter second.

## Current Hooks

- `renderer_scene_cull.cpp`
  - Visible light list after CPU cull.
  - Shadow-casting vs non-shadow visible counts.
- `light_storage.cpp`
  - Final visible light upload to GPU.
  - Best place to track accepted positional light counts.
- `cluster_builder_rd.cpp`
  - Best place to prove cluster pressure before changing limits.

## Phase 1

- Counters only.
- Print:
  - visible omni
  - visible spot
  - visible directional
  - shadow-casting visible lights
  - cluster avg/max/overflow

## Phase 2

- Optional safe rejection only:
  - lights outside camera influence earlier
  - tiny screen-impact lights
  - internal separation of shadow-casting vs non-shadow lights if useful
- No hidden hard cap increase.

## Warning Targets

- High cluster max light count.
- Any cluster overflow.
- Atlas pressure with many shadow lights visible.
