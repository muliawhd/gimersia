# 🖥️ UI & Scene State Flow — GIMERSIA

Seluruh elemen antarmuka menggunakan estetika **Tactile & Squishy Clay UI** yang dibangun di atas [Unity UI Toolkit](https://docs.unity3d.com/6000.4/Documentation/Manual/ui-systems/ui-shader-graph.html) / uGUI dengan animasi *squash-and-stretch* dari [DOTween](https://dotween.demigiant.com/) serta font [Fredoka](https://fonts.google.com/specimen/Fredoka) dan [Chewy](https://fonts.google.com/specimen/Chewy).

---

## 1. State Transition Diagram
```text
[00_Boot / Clay Splash Screen]
              ↓
        [01_Main Menu] ──(Settings Button)──→ [Squishy Settings Modal (Audio / Controls / Graphics)]
              │
              ├──(Host / Join Online atau Local Couch Co-op)
              ↓
   [02_Multiplayer Lobby (1–6 Players)]
   (Pilih Karakter/Kart Tanah Liat & Pilih 1 dari 3 Map: Kitchen / Sculptor's Desk / Playground)
              ↓ (All Players Ready / Host Start)
   [Pre-Race RNG Dice Roll Overlay] ──(All Press Skip/Ready)──┐
   (Animasi Dadu Tanah Liat Mengacak Grid Posisi 1–6)         │
              ↓ (Dice Finished) ←─────────────────────────────┘
   [03–05_Gameplay Race Scene (3 Laps)] ──(Press ESC / Start)──→ [Pause Modal (Local / Disconnect Menu)]
              │
              └──(3 Laps Completed by Racers)
              ↓
   [06_Podium & Race Results Scene]
   (Animasi Podium Tanah Liat 1st–6th, Statistik Drift & Wall-Boost, Rematch / Map Select / Main Menu)
```

---

## 2. Screen Specification & Squishy UX Checklist

### 2.1. Main Menu (`01_MainMenu`)
- **Elemen Utama:** Play Online (Quick Match / Room Code [Photon Fusion 2](https://doc.photonengine.com/fusion/v2/fusion-intro)), Local Couch Co-op (Split-Screen), Map Trial, Settings, Credits, Quit.
- **Efek Tactile:** Saat kursor menyentuh (*hover*) atau menekan tombol, tombol mengalami deformasi `DOPunchScale(new Vector3(0.15f, -0.15f, 0f), 0.25f)` seperti plastisin yang ditekan jari.

### 2.2. Lobby & Pre-Race RNG Dice Screen (`02_Lobby_And_DiceRoll`)
- **Slot 6 Pemain:** Menampilkan 6 *pedestal* tanah liat tempat kart pemain berputar dengan animasi *stop-motion* 12 FPS.
- **Pemilihan Map:** Kartu visual untuk **Map 1: The Kitchen Counter**, **Map 2: The Clay Sculptor's Desk**, dan **Map 3: The Playground**.
- **RNG Dice Roll Phase:**
  - Sebelum *countdown* balapan dimulai, setiap pemain melempar dadu tanah liat 3D di layar.
  - Hasil angka dadu langsung mengurutkan kart ke posisi *Starting Grid* 1–6.
  - Menampilkan indikator **"Skip Dice Roll (0/6 Ready)"** di pojok kanan bawah agar pemain bisa langsung masuk ke balapan jika seluruh *lobby* setuju melewati animasi.

### 2.3. Gameplay HUD (`ClayHudController.cs`)
HUD dirancang minimalis dan menyatu dengan dunia (*diegetic clay notes* yang tertempel di sudut layar):
- **Pojok Kiri Atas — Position & Lap Badge:** Tempelan tanah liat bertuliskan posisi saat ini (`1st` – `6th` dengan font [Chewy](https://fonts.google.com/specimen/Chewy)) dan `LAP 1/3`.
- **Pojok Kanan Atas — Item & Weight Debuff Meter:**
  - Menampilkan ikon *Power-Down* atau *Self-Bounce Power-Up* yang sedang dibawa di bak belakang.
  - **Weight Warning Indicator:** Saat membawa *item* berat, bingkai HUD ikut melengkung turun (penyok) dengan indikator persentase penalti beban (misal: `HEAVY LOAD -30% SPD`) dan ikon petunjuk (`THROW AT WALL TO SELF-BUFF` untuk *Power-Up* pantul).
- **Pojok Kanan Bawah — Drift Boost & Clay Wall Gauge:**
  - *Meter* busur tanah liat yang berubah warna sesuai *Tier Drift* (Tier 1 Kuning, Tier 2 Oranye, Tier 3 Biru Menyala).
  - Ikon cetak **Clay Wall** beserta status *Ready / Cooldown* dan petunjuk singkat (`DRIFT INTO WALL = RAMP BOOST!`).
- **Tengah Layar (Kontekstual):** Garis bantu titik-titik plastisin (*Trajectory Bounce Preview*) saat pemain menahan tombol lempar untuk memudahkan membidik pantulan *Power-Up* ke diri sendiri.

### 2.4. Podium & Victory Screen (`06_Podium_Results`)
- Menampilkan urutan finis 1–6 di atas podium tanah liat dengan penghitungan waktu tempuh, jumlah *Self-Bounce Hits*, dan jumlah *Wall-Ramp Overtakes*.
- Tombol cepat: **Next Map**, **Rematch (Re-roll Dice)**, dan **Back to Lobby**.
