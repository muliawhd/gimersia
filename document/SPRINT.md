# 🗓️ 3-Week Development Sprint Plan — GIMERSIA (9 - 29 Nov 2026)

Roadmap pengembangan 3 minggu untuk membangun **Gimersia** (3D Claymation Arcade Party Racing, 6 Pemain) di [Unity 3D](https://docs.unity3d.com/Packages/com.unity.shadergraph@latest/) bersama AI coding assistant.

---

## 🏃 Sprint 1 (9 - 15 Nov 2026): Core Kart Physics, Drift & Risk-Reward Mechanics
**Fokus:** Fondasi *Custom Raycast Kart Physics*, *Drifting*, *Weight Item Debuff*, *Self-Bounce Projectile*, dan *Clay Wall* di 1 level *graybox*.
- [ ] Setup repository ([Unity Version Control](https://unity.com/features/version-control) / [Git LFS](https://git-lfs.com/)), struktur folder `_Project/`, dan `.cursorrules`.
- [ ] Implementasi `KartRaycastController.cs` (4-titik suspensi *raycast*, akselerasi *snappy*, dan gravitasi *arcade*).
- [ ] Implementasi `KartDriftSystem.cs` (*Hop*, *sliding lateral grip*, 3-tier *drift charge*, dan kamera FOV dinamis dengan [Unity Cinemachine](https://docs.unity3d.com/Packages/com.unity.cinemachine@latest/)).
- [ ] Implementasi `KartItemCarrier.cs` & `BouncingProjectile.cs` (sistem beban *debuff* kecepatan/drag saat membawa item, visual suspensi penyok, dan pantulan vektor untuk *Power-Down* vs *Self-Bounce Power-Up*).
- [ ] Implementasi `ClayWallConstruct.cs` (logika fungsi ganda: penghalang/pemantul proyektil vs *Drift-Ramp Booster* saat ditabrak sambil *drifting*).
- [ ] Grayboxing **Map 1: The Kitchen Counter** dengan *primitive shapes* untuk menguji sudut pantul item dan tikungan *drift*.
- [ ] Review internal tim akhir minggu ke-1: pastikan *feel* *drift* dan pantulan item ke diri sendiri sudah seru di mode *single/local*.

---

## 🚀 Sprint 2 (16 - 22 Nov 2026): 6-Player Networking, 3 Maps, Clay Shader & Mid-Checkpoint
**Fokus:** Sinkronisasi multiplayer 6 pemain dengan [Photon Fusion 2](https://doc.photonengine.com/fusion/v2/fusion-intro), sistem *RNG Dice Start*, penyelesaian 3 map, *Claymation Shader*, dan UI *squishy*.
- [ ] Integrasi [Photon Fusion 2](https://doc.photonengine.com/fusion/v2/fusion-intro) (`NetworkRunner`, prediksi *client-side* untuk 6 kart, sinkronisasi proyektil pantul dan *Clay Wall*).
- [ ] Implementasi `PreRaceDiceManager.cs` (lemparan dadu RNG penentu posisi start 1–6 beserta fitur *Skip Animation* saat semua pemain *Ready*).
- [ ] Pembangunan penuh **3 Map Playable**:
  - [ ] **Map 1:** *The Kitchen Counter* (sudut tajam & rintangan piring/panci pantul).
  - [ ] **Map 2:** *The Clay Sculptor's Desk* (trek vertikal multi-level yang memaksimalkan *Wall-Ramp Jump*).
  - [ ] **Map 3:** *The Playground* (*rollercoaster loop*, *half-pipe*, dan *air-boost*).
- [ ] Pembuatan *Custom Clay/Plasticine Shader* di [Unity Shader Graph](https://docs.unity3d.com/Packages/com.unity.shadergraph@latest/) + skrip `StopMotionAnimator.cs` (efek animasi 12–15 FPS).
- [ ] Integrasi UI Flow lengkap (Main Menu, Lobby 6 Pemain, HUD Tanah Liat, Pause, Podium) dengan animasi *squash-and-stretch* [DOTween](https://dotween.demigiant.com/) dan font [Fredoka](https://fonts.google.com/specimen/Fredoka) / [Chewy](https://fonts.google.com/specimen/Chewy).
- [ ] **19 Nov 2026:** Upload *build test* pertama ke itch.io (Draft/Restricted) & sesi *playtest* 6 pemain untuk *balancing* bobot item.

---

## 🎯 Sprint 3 (23 - 29 Nov 2026): Audio Foley, Clay Juice, Optimization & Final Submission
**Fokus:** Integrasi audio dinamis [FMOD for Unity](https://www.fmod.com/unity), *visual polish* tanah liat, audit lisensi aset, dan pengumpulan final.
- [ ] Integrasi audio *foley* tanah liat & plastik di [FMOD for Unity](https://www.fmod.com/unity) (suara *squish*, *drift* tanah liat tergores, serta filter *muffled* saat kart keberatan membawa *item*).
- [ ] Visual polish (*Trajectory aim line* untuk membantu membidik pantulan *Self-Bounce Power-Up*, partikel keringat tanah liat saat terkena *debuff* beban, dan *camera shake* saat tabrakan).
- [ ] Audit lengkap `docs/ASSET_MANIFEST.md` untuk memastikan seluruh aset 3D, tekstur sidik jari, font, dan audio mematuhi aturan non-AI Gimersia.
- [ ] QA pass & *bug hunting* jaringan/fisika (cek dan selesaikan daftar di `docs/KNOWN_ISSUES.md`).
- [ ] Rekam video *trailer gameplay* 6 pemain & tangkapan layar promosi di ketiga map.
- [ ] Build final Windows Standalone / WebGL & verifikasi performa 60 FPS stabil.
- [ ] **29 Nov 2026:** Final submission form Gimersia sebelum batas waktu!
