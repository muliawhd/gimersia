# 🐛 Known Issues & Bug Tracker — GIMERSIA

Gunakan format: `[ID] [Severity: High/Med/Low] Deskripsi Masalah — Rencana Solusi — Status: Open/Investigating/Resolved`

- [ ] `[BUG-001]` `[High]` **Self-Bounce Power-Up Meledak Instan Saat Dilempar di Kecepatan Puncak:** Ketika kart melaju di atas `32 m/s` (saat *Tier 3 Drift Boost*) dan melempar *Power-Up* ke depan, *collider* kart terkadang langsung menyentuh proyektil sebelum sempat memantul di dinding.
  - *Solusi:* Tambahkan `SelfHitArmingDelay = 0.20f` DAN syarat wajib `CurrentBounceCount >= 1` pada `BouncingProjectile.cs` sebelum proyektil dapat mengaktifkan *buff* pada `OwnerPlayerRef`. — *Status: Open*
- [ ] `[BUG-002]` `[High]` **Transisi Wall-Ramp vs Blocker pada Sudut Miring:** Pemain yang baru saja melepas tombol *Drift* `0.05 detik` sebelum menyentuh `ClayWallConstruct` terdeteksi sebagai tabrakan normal (*Blocker*) alih-alih meluncur ke atas sebagai *Ramp Booster*.
  - *Solusi:* Tambahkan `DriftRampCoyoteTime = 0.25f` setelah *drift* berakhir agar pemain tetap memicu *Wall-Ramp Boost* secara mulus. — *Status: Open*
- [ ] `[BUG-003]` `[Med]` **Jitter Kamera Ketika Stop-Motion 12 FPS Aktif:** Jika `Camera Follow Target` di [Unity Cinemachine](https://docs.unity3d.com/Packages/com.unity.cinemachine@latest/) diarahkan ke `Transform` *mesh* visual kart yang di-*step* pada 12 FPS, gerakan kamera ikut patah-patah.
  - *Solusi:* Pastikan `CinemachineVirtualCamera` mengikuti *root* `Rigidbody` interpolasi 60 FPS, sedangkan `StopMotionAnimator.cs` hanya memodifikasi *child object* `VisualMeshRoot`. — *Status: Open*
- [ ] `[BUG-004]` `[Med]` **Desinkronisasi Posisi Start Saat Angka Dadu RNG Kembar:** Jika 2 atau lebih pemain mendapatkan angka dadu yang sama (misal dua pemain mendapat angka `4`), urutan posisi *grid* bisa berbeda antar *client*.
  - *Solusi:* Hitung seluruh permutasi *tie-breaker* di *Host/Server* menggunakan satu `NetworkRNG` *seed* di [Photon Fusion 2](https://doc.photonengine.com/fusion/v2/fusion-intro) dan kirimkan array final `AssignedGridIndex[6]` ke seluruh *client*. — *Status: Open*
- [ ] `[BUG-005]` `[Low]` **Suspensi Belakang Tembus Lantai pada Map 3 (Loop/Half-Pipe):** Saat kart membawa *Heavy Clay Anvil* (`-30% speed`, suspensi turun ke `0.32 m`) melewati *loop* vertikal di *Map 3: The Playground*, gaya sentripetal menekan bodi terlalu dalam.
  - *Solusi:* Batasi kompresi minimum suspensi (`MinSuspensionLength = 0.22 m`) dan gunakan *surface normal* lintasan untuk arah gaya pegas. — *Status: Open*
