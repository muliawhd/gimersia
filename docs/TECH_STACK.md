# Technical Architecture & Stack

## 1. Game Engine & Environment
- **Engine Version:** [Misal: Unity 2022.3 LTS / Godot 4.3 / Unreal Engine 5.4]
- **Primary Language:** [C# / GDScript / C++]
- **Target Resolution & Aspect Ratio:** 1920x1080 (16:9), Responsive Canvas

## 2. Dependencies & Plugins
- Audio: [Default Engine Audio / FMOD]
- Tweening / Animation: [DOTween / Godot Tween]
- UI Framework: [TextMeshPro / Unity UI Toolkit / Godot Control Nodes]
- Input System: [Unity New Input System / Godot InputMap]

## 3. Project Directory Hierarchy
```
Assets/ (atau root source engine)
├── _Project/
│   ├── Scripts/
│   │   ├── Core/          # GameManager, SceneLoader, AudioController
│   │   ├── Player/        # Controller, Health, Input
│   │   ├── Enemies/       # AI, State Machine, Spawners
│   │   ├── Systems/       # Inventory, Combat, Scoring
│   │   └── UI/            # HUD, Menus, Dialogues
│   ├── Scenes/            # Boot, MainMenu, Gameplay_01, GameOver
│   ├── Prefabs/           # Player, Enemies, Projectiles, Collectibles
│   ├── Art/               # Sprites, Models, Materials
│   ├── Audio/             # BGM, SFX
│   └── Settings/          # Input Actions, Render Pipeline Settings
├── docs/                  # Markdown Brain Files
└── ThirdParty/            # Impor asset store / CC0 plugins terpisah
```
