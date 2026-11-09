# 🛠️ Technical Architecture & Stack — GIMERSIA

Dokumen ini mendefinisikan arsitektur teknis proyek **Gimersia** (3D Claymation Arcade Party Racing, 6 Pemain) agar siap diimplementasikan di [Unity 3D](https://docs.unity3d.com/Packages/com.unity.shadergraph@latest/) dan dikolaborasikan bersama AI coding assistant.

---

## 1. Game Engine & Environment
- **Engine Version:** [Unity 3D](https://docs.unity3d.com/Packages/com.unity.shadergraph@latest/) (Unity 6 LTS / 2022.3 LTS) dengan **Universal Render Pipeline (URP)**
- **Primary Language:** C# (.NET Standard 2.1)
- **Target Resolution & Framerate:** 1920x1080 (16:9) @ 60 FPS (Simulasi Fisika & Kamera 60 FPS; Animasi Karakter/Visual Kart di-*step* pada 12–15 FPS untuk efek *stop-motion*)
- **Target Platform:** Windows Standalone & WebGL (itch.io)
- **Multiplayer Mode:** 6 Players Online ([Photon Fusion 2](https://doc.photonengine.com/fusion/v2/fusion-intro)) & Local Couch Co-op Split-Screen ([Unity Input System](https://docs.unity3d.com/Packages/com.unity.inputsystem@latest/))

---

## 2. Core Dependencies, Middleware & Plugins

| Komponen / Sistem | Teknologi / Library | Peran Spesifik dalam Gimersia | Dokumentasi Resmi |
|---|---|---|---|
| **Networking (6 Pemain)** | **Photon Fusion 2** | Menyediakan *Tick-Based Client-Side Prediction* & *Lag Compensation* melalui `NetworkRunner` dan `NetworkBehaviour` agar tabrakan kart, pantulan proyektil *power-up*, pembuatan *Clay Wall*, dan *seed* dadu RNG sinkron tanpa *rubber-banding*. | [Photon Fusion 2 Docs](https://doc.photonengine.com/fusion/v2/fusion-intro) |
| **Vehicle Physics** | **Custom Raycast C# Physics** | Menggantikan `WheelCollider` bawaan Unity dengan 4 titik *Raycast Spring-Damper* + `Rigidbody` terpusat. Memungkinkan kontrol penuh atas *drift sliding*, kompresi suspensi saat terkena *Weight Debuff*, dan lontaran *Wall-Ramp Boost*. | [Unity Physics Manual](https://docs.unity3d.com/Packages/com.unity.shadergraph@latest/) |
| **Camera System** | **Unity Cinemachine** | Mengatur kamera *third-person* di belakang kart, interpolasi sudut *drift*, pelebaran FOV dinamis (*fisheye speed illusion* dari 60° ke 82°), serta *Cinemachine Impulse* saat tabrakan atau mendarat dari *Wall-Ramp*. | [Unity Cinemachine](https://docs.unity3d.com/Packages/com.unity.cinemachine@latest/) |
| **Claymation Shaders** | **Unity Shader Graph (URP)** | Membangun *Custom Clay/Plasticine Shader* dengan *fresnel subsurface scattering* lembut, *matte roughness*, dan *UV-stepped fingerprint normal map* yang berganti tiap beberapa *frame*. | [Unity Shader Graph](https://docs.unity3d.com/Packages/com.unity.shadergraph@latest/) |
| **Stop-Motion System** | **`StopMotionAnimator.cs` (Custom C#)** | Mengunci pembaruan transformasi/pose visual mesh karakter, ekspresi wajah, dan goyangan kotak beban di 12 FPS (`0.0833s` interval) sementara fisika dan kamera tetap berjalan mulus di 60 FPS. | Internal (`Scripts/Visuals/`) |
| **UI Framework & Tween** | **Unity UI Toolkit / uGUI + DOTween** | Membangun HUD tanah liat dan menu *tactile* dengan animasi *squash-and-stretch* elastis menggunakan [DOTween](https://dotween.demigiant.com/) serta dukungan *shader* UI di [Unity UI Toolkit](https://docs.unity3d.com/6000.4/Documentation/Manual/ui-systems/ui-shader-graph.html). | [DOTween](https://dotween.demigiant.com/) / [UI Toolkit](https://docs.unity3d.com/6000.4/Documentation/Manual/ui-systems/ui-shader-graph.html) |
| **Typography** | **Fredoka & Chewy (OFL)** | Font membulat bergaya plastisin/tulisan tangan untuk angka posisi (1st–6th), *Lap Counter*, dan *Dice Roll UI*. | [Fredoka](https://fonts.google.com/specimen/Fredoka) / [Chewy](https://fonts.google.com/specimen/Chewy) |
| **Input Management** | **Unity Input System** | Mengelola *binding* Keyboard & Mouse serta hingga 6 Gamepad secara simultan untuk *Local Couch Co-op* dan Online. | [Unity Input System](https://docs.unity3d.com/Packages/com.unity.inputsystem@latest/) |
| **Audio Middleware** | **FMOD for Unity** (atau **Wwise**) | Mengatur *dynamic audio parameters* (RTPC): suara gesekan plastisin sesuai kecepatan, efek tanah liat terkelupas saat *drift*, dan efek *low-pass/muffled* saat membawa beban *item* berat. | [FMOD for Unity](https://www.fmod.com/unity) / [Audiokinetic Wwise](https://www.audiokinetic.com/en/public-library/2025.1.12_9287/?source=Help&id=using_wwise_with_source_control_system) |
| **Version Control** | **Unity Version Control (Plastic SCM) / Git LFS** | Mengelola kolaborasi skrip C# dan penyimpanan aset biner 3D tanah liat berukuran besar, tekstur, serta *audio bank*. | [Unity Version Control](https://unity.com/features/version-control) / [Git LFS](https://git-lfs.com/) |

---

## 3. Arsitektur Sub-Sistem Kunci

### 3.1. Arsitektur Jaringan (Photon Fusion 2)
- **Topologi:** *Host/Server Mode* (atau *Shared Mode* untuk *rapid prototyping* di WebGL) menggunakan [NetworkRunner](https://doc.photonengine.com/fusion/v2/manual/network-runner) dan properti `[Networked]` pada [NetworkBehaviour](https://doc.photonengine.com/fusion/v2/manual/network-behaviour).
- **Sinkronisasi State Inti:**
  - `NetworkKartController`: Menyinkronkan posisi/rotasi `Rigidbody`, `CurrentDriftTier` (0–3), `CarriedItemId`, dan `CurrentWeightMultiplier`.
  - `NetworkItemProjectile`: Menyinkronkan titik tembak, vektor kecepatan awal, dan sisa pantulan (`RemainingBounces`) dengan lintasan deterministik berbasis `Physics.SphereCast`.
  - `NetworkClayWall`: Menyinkronkan posisi, rotasi, pemilik dinding (`OwnerPlayerRef`), dan masa aktif dinding di lintasan.
  - `PreRaceDiceManager`: Menghasilkan `NetworkArray<int>` berisi hasil kocokan dadu 1–6 untuk ke-6 pemain sebelum balapan dimulai.

### 3.2. Custom Raycast Kart & Drift Architecture
- **Mengapa Bukan `WheelCollider`?** `WheelCollider` bawaan dirancang untuk simulasi mobil realistis dan sering tidak stabil saat melompat dari tanjakan curam atau *half-pipe*.
- **Pendekatan Raycast + Sphere/Box Collider:**
  1. **Suspension Force:** 4 titik *raycast* di sudut sasis menghitung hukum Hooke ($F = k \cdot x - c \cdot v$) untuk menahan kart di atas permukaan trek dan memiringkan visual bodi saat berbelok atau saat membawa beban berat di belakang.
  2. **Drift State Machine:** Saat pemain menekan *Hop + Drift* sambil berbelok, *lateral grip* dikurangi agar kart melakukan *sliding* terkendali, sambil mengakumulasi `DriftMomentum` yang dikonversi menjadi *Speed Boost* saat tombol dilepas.
  3. **Dynamic FOV & Squash:** `KartCameraController` mengubah FOV kamera [Unity Cinemachine](https://docs.unity3d.com/Packages/com.unity.cinemachine@latest/) secara proporsional terhadap rasio kecepatan aktual dan status *boost*.

### 3.3. Arsitektur The "Weight" Item & Bouncing System
- **Saat Mengambil Item (`ItemCarrierComponent`):**
  - Membaca data `ItemPayloadSO` (*Light*, *Medium*, *Heavy*).
  - Menampilkan *prefab* visual *item* di *socket* belakang kart, menurunkan `RestLength` suspensi belakang (efek kart amblas/penyok), menyalakan `ParticleSystem` keringat tanah liat, dan menerapkan *modifier* ke `KartRaycastController` (`MaxSpeed *= (1f - weightPenalty)`, `LinearDrag += dragPenalty`).
- **Saat Melempar Item (`BouncingProjectileComponent`):**
  - Begitu dilempar, *debuff* beban langsung hilang dan suspensi kart memantul kembali normal menggunakan *spring tween* [DOTween](https://dotween.demigiant.com/).
  - Proyektil bergerak menggunakan `SphereCast` per *tick* dan memantul pada permukaan ber-layer `Wall`, `Obstacle`, atau `ClayWall` dengan rumus `dir = Vector3.Reflect(dir, hit.normal)`.
  - **Aturan Self-Bounce Power-Up:** Memiliki *grace period* `0.25s` saat baru dilempar, dan flag `IsArmedForSelf = true` baru menyala **setelah`BounceCount >= 1`**, sehingga pemain wajib memantulkannya ke dinding/rintangan terlebih dahulu untuk mendapatkan *buff*.

### 3.4. Arsitektur Clay Wall (Blocker vs. Drift-Ramp Booster)
- Prefab `ClayWallConstruct` memiliki **dua zona interaksi kolisi**:
  1. **Front/Back Blocker Collider:** Jika kart menabrak dinding dalam kondisi normal (`IsDrifting == false`), kart terpental mundur dan kehilangan kecepatan; jika proyektil *item* menabraknya, proyektil langsung memantul.
  2. **Drift-Ramp Trigger Zone:** Jika kart menyentuh dinding dalam kondisi **`IsDrifting == true`** (atau milik pembuat dinding/rekan yang melakukan *drift slide* ke arah dinding), skrip `ClayWallBooster.cs` menonaktifkan tabrakan keras dan memberikan impuls vertikal (`Vector3.up * rampLaunchForce`) + *Forward Boost* (`1.45x` kecepatan puncak) lengkap dengan animasi dinding tanah liat yang memipih lalu melontarkan kart seperti ketapel plastisin.

---

## 4. Project Directory Hierarchy
```text
Assets/
├── _Project/
│   ├── Scripts/
│   │   ├── Core/              # GameManager, RaceManager, LapTracker, SceneLoader
│   │   ├── Network/           # FusionBootstrap, NetworkInputData, PreRaceDiceManager
│   │   ├── Kart/              # KartRaycastController, KartDriftSystem, KartCameraFOV, KartVisualSquash
│   │   ├── Items/             # ItemPayloadSO, ItemBoxSpawner, KartItemCarrier, BouncingProjectile
│   │   ├── Wall/              # ClayWallConfigSO, ClayWallSpawner, ClayWallConstruct
│   │   ├── Visuals/           # StopMotionAnimator (12fps limiter), ClayFingerprintCycler
│   │   ├── Audio/             # KartFmodEmitter, DynamicWeightAudioFilter
│   │   └── UI/                # SquishyButtonTween, LobbyDiceUI, ClayHudController, TrajectoryPreview
│   ├── ScriptableObjects/
│   │   ├── Karts/             # Preset statistik kart
│   │   ├── Items/             # Offensive Power-Downs & Self-Bounce Power-Ups
│   │   └── Maps/              # Metadata 3 Map (Kitchen, Sculptor's Desk, Playground)
│   ├── Scenes/
│   │   ├── 00_Boot.unity
│   │   ├── 01_MainMenu.unity
│   │   ├── 02_Lobby_And_DiceRoll.unity
│   │   ├── 03_Map01_KitchenCounter.unity
│   │   ├── 04_Map02_SculptorsDesk.unity
│   │   ├── 05_Map03_Playground.unity
│   │   └── 06_Podium_Results.unity
│   ├── Prefabs/
│   │   ├── Karts/             # Prefab Kart 6 Pemain + Visual Cargo Socket
│   │   ├── Items/             # Prefab Power-Down & Power-Up Pantul
│   │   ├── Walls/             # Prefab ClayWallConstruct (Blocker + Drift-Ramp)
│   │   └── Environment/       # Rintangan Piring, Panci, Pahat, Loop Track
│   ├── Shaders/               # ClayLit_URP.shadergraph, FingerprintNormal.shadergraph
│   ├── Art/                   # Models, Materials, Textures, Fonts (Fredoka, Chewy)
│   ├── Audio/                 # FMOD Banks / Foley Clay SFX & BGM
│   └── Settings/              # URP Asset, InputSystem_Actions.inputactions, PhotonAppSettings
├── docs/                      # Markdown Brain Files (GDD, TECH_STACK, CORE_MECHANICS, dll.)
└── ThirdParty/                # Photon Fusion, DOTween, FMOD, CC0 Assets
```
