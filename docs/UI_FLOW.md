# UI & Scene State Flow

## State Transition Diagram
```text
[Game Boot / Splash]
        ↓
  [Main Menu] ──(Settings Button)──→ [Settings Modal]
        ↓ (Play Button)
  [Level Selection / Intro]
        ↓
  [Gameplay Scene] ──(Press ESC)──→ [Pause Modal]
        │                               │
        ├───────────────────────────────┼──(Resume)──→ [Gameplay Scene]
        │                               └──(Exit)────→ [Main Menu]
        ├─ (HP <= 0) ──────→ [Game Over Scene] ──(Retry)──→ [Gameplay Scene]
        └─ (Objective Won) ─→ [Victory Scene]   ──(Next)───→ [Next Level / Menu]
```

## Screen Specification Checklist
- **Main Menu:** Start Game, Level Select, Options (Master Volume, SFX, BGM), Credits, Quit.
- **HUD (Gameplay):** Health bar, Score/Objectives counter, Active powerup icon, Mini timer.
- **Pause Menu:** Resume, Restart, Audio Settings, Return to Main Menu.
- **Game Over / Victory:** Result recap, High score display, Retry button, Main Menu button.
