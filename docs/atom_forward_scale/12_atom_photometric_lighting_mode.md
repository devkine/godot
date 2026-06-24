# Atom Photometric Lighting Mode

## Goal

- Add optional Atom-inspired light behavior to Forward+.
- Keep default Godot scenes unchanged.
- Start with observation first. No lighting behavior change in first patch.

## Current Godot Light Pipeline

### 1. Where `Light3D` stores color and intensity

- `scene/3d/light_3d.h`
  - `Color color`
  - `real_t param[PARAM_MAX]`
  - `Color correlated_color`
  - `float temperature`
- `scene/3d/light_3d.cpp`
  - `set_color()` stores node color and pushes renderer color.
  - `set_param()` stores light params and pushes renderer params.
  - `PARAM_ENERGY` and `PARAM_INTENSITY` are the main scalar light inputs.

### 2. Where Godot applies temperature

- `scene/3d/light_3d.cpp`
  - `_color_from_temperature()` converts Kelvin to an sRGB color.
  - `set_temperature()` updates `correlated_color`.
  - When `rendering/lights_and_shadows/use_physical_light_units` is enabled,
    renderer color becomes `color * correlated_color` in linear space, then
    converts back to sRGB before `RS::light_set_color()`.
- `set_color()` uses the same combined path when physical light units are on.
- Atom patch note:
  - `scene/3d/light_3d.cpp` now also uses the combined color path when
    `rendering/atom_forward_scale/light_behavior/enabled=true` and
    `rendering/atom_forward_scale/light_behavior/color_as_energy_filter=true`.
  - This keeps GodotClassic unchanged while letting Atom mode treat color and
    Kelvin as an energy filter before renderer upload.

### 3. Where Godot uploads light data to renderer

- `servers/rendering/renderer_rd/forward_clustered/render_forward_clustered.cpp`
  - `light_storage->update_light_buffers(...)` is the Forward+ upload entry.
- `servers/rendering/renderer_rd/storage_rd/light_storage.cpp`
  - `LightStorage::update_light_buffers()` builds GPU light data for visible
    directional, omni, spot, and area lights.

### 4. Where final GPU light color is formed now

- Directional:
  - `light_storage.cpp`: uploads `DirectionalLightData.color` and separate
    `DirectionalLightData.energy`.
  - `servers/rendering/renderer_rd/shaders/forward_clustered/scene_forward_clustered.glsl`
    multiplies `directional_lights.data[i].color * directional_lights.data[i].energy`.
- Omni and Spot:
  - `light_storage.cpp` precomputes `light_data.color = linear_color * energy`.
  - `servers/rendering/renderer_rd/shaders/scene_forward_lights_inc.glsl`
    reads `omni_lights.data[idx].color` and `spot_lights.data[idx].color`.

### 5. Smallest safe patch location

- First debug-only observation patch:
  - `servers/rendering/rendering_server.cpp` for project settings.
  - `servers/rendering/renderer_rd/storage_rd/light_storage.cpp` for logging
    current final RGB during visible-light upload.
- First optional behavior patch:
  - `scene/3d/light_3d.cpp` for raw color plus Kelvin combination.
  - `LightStorage::update_light_buffers()` for per-type energy conversion.
  - This split is safest because `Light3D` has raw color and Kelvin, while
    `LightStorage` has visible-light type, fade, intensity, and exposure.

## Current Energy Paths

- Shared scalar base for all visible lights:
  - sign from `light->negative`
  - `LIGHT_PARAM_ENERGY`
  - distance fade for omni and spot
  - exposure normalization from camera attributes when present
- Physical light units path in `light_storage.cpp`:
  - Directional: `energy *= intensity`
  - Omni: `energy *= intensity / (4 * PI)`
  - Spot: `energy *= intensity / PI`
- Non-physical path:
  - `energy *= PI`

## Atom Helper Path

- `rendering/atom_forward_scale/light_behavior/enabled=true` switches visible-light
  upload to explicit photometric helpers in `light_storage.cpp`.
- Directional keeps lux-style scalar intensity.
- Omni uses `candela = lumens / (4 * PI)`.
- Spot uses `solid_angle = 2 * PI * (1 - cos(outer_angle))`, then
  `candela = lumens / max(solid_angle, 1e-4)`.
- Disabled mode keeps current Godot upload behavior.

## Why Upload Path Is Best For Atom Mode

- Has final visible-light list already.
- Has per-type energy conversion already.
- Avoids changing default node storage behavior first.
- Keeps shader changes optional or unnecessary for first mode.

## Debug Patch Scope

- Add project settings:
  - `rendering/atom_forward_scale/light_behavior/enabled`
  - `rendering/atom_forward_scale/light_behavior/color_as_energy_filter`
  - `rendering/atom_forward_scale/light_behavior/debug_final_light_rgb`
- First patch only logs current final RGB values for visible lights.
- No lighting output change yet.

## Test Scene Plan

1. One white light, baseline energy.
2. One 50% gray light, same energy.
3. One pure red light, same energy.
4. One warm Kelvin light, same energy.
5. Repeat for `DirectionalLight3D`, `OmniLight3D`, and `SpotLight3D`.
6. Compare:
   - GodotClassic behavior
   - AtomPhotometric enabled later
7. Enable `rendering/atom_forward_scale/light_behavior/debug_final_light_rgb`
   and confirm logged RGB matches current Godot output before changing math.

## Manual Test Recipe

### Scene Layout

1. Create a new Forward+ 3D scene.
2. Add:
   - `Node3D` root
   - `Camera3D`
   - `MeshInstance3D` with `PlaneMesh` floor
   - `MeshInstance3D` with `SphereMesh`
   - one `DirectionalLight3D`
   - one `OmniLight3D`
   - one `SpotLight3D`
3. Give floor and sphere a neutral white material.
4. Put the camera so floor and sphere are both visible.
5. Keep only one test light enabled at a time when comparing outputs.

### Baseline Light Values

- Use same `light_energy` for each comparison pair.
- If physical light units are enabled:
  - `DirectionalLight3D.light_intensity_lux = 100000`
  - `OmniLight3D.light_intensity_lumens = 1000`
  - `SpotLight3D.light_intensity_lumens = 1000`
- Suggested spot setup:
  - `spot_angle = 45`
  - duplicate once with `spot_angle = 20` to verify solid-angle behavior.

### Variants To Compare

For each light type, test these one by one:

1. White:
   - `light_color = (1, 1, 1)`
   - `light_temperature = 6500`
2. Gray filter:
   - `light_color = (0.5, 0.5, 0.5)`
   - `light_temperature = 6500`
3. Red filter:
   - `light_color = (1, 0, 0)`
   - `light_temperature = 6500`
4. Warm white:
   - `light_color = (1, 1, 1)`
   - `light_temperature = 3000`
5. Warm red:
   - `light_color = (1, 0, 0)`
   - `light_temperature = 3000`

### Project Settings Matrix

Run the same scene with these settings:

1. GodotClassic baseline:
   - `rendering/atom_forward_scale/light_behavior/enabled = false`
   - `rendering/atom_forward_scale/light_behavior/color_as_energy_filter = false`
2. Atom energy only:
   - `enabled = true`
   - `color_as_energy_filter = false`
3. Atom full filter mode:
   - `enabled = true`
   - `color_as_energy_filter = true`

### What To Check

- White light should keep full brightness.
- Gray should visibly lose emitted energy versus white.
- Saturated red should not be renormalized back to white-like brightness.
- Warm Kelvin should combine cleanly with color instead of fighting it.
- Narrower spot should become brighter than wider spot at equal lumen value in Atom mode.
- With `debug_final_light_rgb = true`, confirm console RGB drops for gray and saturated colors.
