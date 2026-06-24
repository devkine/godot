# Cluster Debug Plan

## Goal

- Expose clustered light pressure before changing clustered behavior.

## Existing Data

- `cluster_builder_rd.cpp::collect_debug_stats(...)` already provides:
  - `cluster_count`
  - `average_lights_per_non_empty_cluster`
  - `max_lights_in_cluster`
  - `overflow_count`
- `render_forward_clustered.cpp` already copies these into `RenderInfo`.

## Phase 1

- Console output only.
- No shader changes.
- No heatmap yet.
- Print:
  - cluster count
  - average lights per non-empty cluster
  - max lights in one cluster
  - overflow count

## Phase 2

- Add high-pressure warnings when:
  - max lights in cluster is near cluster element budget
  - overflow count is non-zero
- Still no behavior change.
- Current warning threshold target:
  - near-budget warning at `80%` of `cluster_max_elements`
  - overflow warning at any non-zero overflow count

## Later

- Cluster heatmap.
- Per-type cluster pressure.
- Compare CPU-visible lights vs clustered accepted pressure.
