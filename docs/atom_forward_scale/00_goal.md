# Atom Forward Scale Goal

## Goal

- Add Atom-inspired scalability ideas to Godot 4 Forward+.
- Keep normal Godot renderer and scene compatibility.
- Keep all new behavior optional or safe by default.

## Hard Rules

- No renderer replacement.
- No deferred renderer.
- No direct Atom code copy.
- Small patches only.
- Build after each behavior patch.
- No editor UI first.

## Phases

1. Docs and renderer map.
2. Debug-only renderer stats. No behavior change.
3. Benchmark scene/process docs.
4. Optional shadow budget settings.
5. Shadow priority and cache safety.
6. Cluster diagnostics and light culling improvements.
7. Experimental GPU visibility prototype.

## Success Target

- Existing projects open unchanged.
- Existing Forward+ lights, shadows, materials, viewport still work.
- Large scenes with many objects/lights/shadows scale better when optional systems enabled.
