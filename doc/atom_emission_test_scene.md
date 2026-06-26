## Atom Emission Test Scene

Renderer: Forward+

Scene:
- `Node3D` root
- `Camera3D`
- `MeshInstance3D` with `PlaneMesh` floor
- `MeshInstance3D` with `SphereMesh`
- Several `MeshInstance3D` cards using emissive materials
- `WorldEnvironment`

Base material:
- `albedo = white`
- `roughness = 0.5`
- `metallic = 0.0`

Project settings matrix:
- Stock: `rendering/atom_forward_scale/emission_behavior/enabled = false`
- Atom emission: `rendering/atom_forward_scale/emission_behavior/enabled = true`
- Atom full: `rendering/atom_forward_scale/light_behavior/enabled = true`, `rendering/atom_forward_scale/emission_behavior/enabled = true`

Recommended Environment:
- Glow enabled
- Tonemap = Filmic or ACES
- Exposure artist controlled
- Bloom threshold tuned for HDR emissive surfaces

Emissive cards:
1. White `100 nits`
2. Gray `100 nits`
3. Red `100 nits`
4. Blue `100 nits`
5. Warm orange `1000 nits`
6. White `10000 nits`

Suggested StandardMaterial3D values:
- `emission_enabled = true`
- `emission = white/gray/red/blue/orange`
- `emission_energy_multiplier = 1.0`
- If physical light units enabled: set `emission_intensity` to target nits
- If physical light units disabled and Atom emission enabled: `emission_energy_multiplier = 1.0` maps to `default_emission_nits`

Expected results:
- White brightest neutral emitter
- Gray visibly dimmer than white
- Red stays red, no white-equivalent brightness normalization
- Blue stays blue
- Warm orange feels warmer and more cinematic
- `10000 nits` can bloom or overexpose when HDR/glow settings allow it

Notes:
- Emissive materials affect HDR buffer and glow directly
- GI contribution uses existing Godot systems only: SDFGI, VoxelGI, LightmapGI, reflections/probes where already supported
- This mode does not spawn real-time proxy lights
