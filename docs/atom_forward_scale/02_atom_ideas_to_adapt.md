# Atom Ideas To Adapt

## Useful Atom Concepts

- Feature-processor style ownership split.
- Explicit render phases and pass boundaries.
- Shadow caching for static or unchanged lights.
- Shadow update budgeting instead of redraw-all.
- Better diagnostics for light and shadow cost.
- GPU-driven visibility as optional path.
- Bindless-style scalability thinking for future GPU work.

## Godot Adaptation

- Keep Godot renderer structure.
- Use internal separation by responsibility, not a new pipeline.
- Add stats first, then policy toggles, then optional experiments.
- Prefer CPU-side rejection before raising cluster limits.
- Reuse Godot timestamps, viewports, render data, and shadow atlas model.

## Do Not Adapt Directly

- No Atom pass tree rewrite.
- No Atom ECS or bus patterns.
- No direct source transplant.
- No hard dependency on GPU-driven submission for default path.
