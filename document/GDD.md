# 🏎️ GIMERSIA - Game Design Document (GDD)

## 1. Overview & Elevator Pitch
- **Judul Game:** Gimersia
- **Genre:** Arcade Party Racing / Multiplayer Kart Racing
- **Perspektif:** Third-Person (Dynamic Chase Camera ala *Mario Kart*)
- **Player Count:** 6 Pemain (Online Multiplayer via [Photon Fusion 2](https://doc.photonengine.com/fusion/v2/fusion-intro) / Local Couch Co-op Split-Screen)
- **Art Style:** Claymation / Stop-Motion 3D (Tekstur plastisin buatan tangan dengan animasi karakter/kart 12–15 FPS di dalam dunia 60 FPS)
- **Target Platform:** Windows Standalone & WebGL (itch.io) menggunakan [Unity 3D (URP)](https://docs.unity3d.com/Packages/com.unity.shadergraph@latest/)
- **Elevator Pitch:** **Gimersia** adalah game balap kart 3D bergaya *claymation* untuk 6 pemain yang memadukan keseruan *arcade party racing* dengan mekanik manajemen risiko unik. Berbeda dari balap kart konvensional, menyimpan *item* memiliki **beban fisik (Weight Debuff)** yang menekan suspensi dan memperlambat kendaraan; pemain harus cerdik melempar *power-down* ke lawan, memantulkan *power-up* ke dinding agar mengenai diri sendiri, serta mencetak dinding tanah liat (*Clay Wall*) yang berfungsi ganda sebagai penghalang maupun *ramp booster* saat ditabrak sambil *drifting*.

---

## 2. Tema & Twist Inovasi Utama
- **Twist 1 — The "Weight" Item System (Beban Bawaan):** Mengambil kotak *item* di lintasan langsung menempelkan objek fisik *item* di bak belakang kart. Selama *item* belum dilempar, kart terkena *debuff* penurunan *max speed* dan peningkatan *drag* sesuai bobot *item* tersebut.
- **Twist 2 — Bouncing Projectiles & Self-Hit Power-Ups:** Semua *item* diaktifkan dengan cara dilempar dan dapat memantul di dinding/rintangan. *Power-Down* (item ofensif) dilempar untuk memantul ke arah lawan, sedangkan *Power-Up* (*buff* pertahanan/kecepatan) **wajib dilempar memantul ke dinding atau rintangan di depan agar memantul balik mengenai kart pemain sendiri**.
- **Twist 3 — Dual-Function Clay Wall (Blocker & Drift-Ramp Booster):** Pemain dapat mencetak dinding tanah liat di lintasan untuk memblokir jalur/memantulkan lemparan *item*, ATAU menggunakannya sebagai **tanjakan *booster* (*Wall-Ramp*)** — jika pemain melakukan *drift* dan menabrak dinding tanah liat pada sudut luncur, kart akan terpental naik dan memperoleh *speed boost* besar untuk menyalip lawan dari udara.
- **Twist 4 — Board-Game RNG Dice Start:** Posisi *grid* awal (1–6) sebelum balapan ditentukan melalui lemparan dadu RNG bergaya *board game* yang bisa di-*skip* bila seluruh pemain sudah *Ready*.

---

## 3. Core Gameplay Loop
1. **Pre-Race Roll (RNG Dice):** 6 pemain masuk ke *starting line* dan melempar dadu RNG untuk menentukan urutan *grid* 1–6 (memberikan nuansa *party board game* yang segar di tiap ronde).
2. **Drive & Drift:** Pemain memacu kart di lintasan yang sangat bergantung pada kemampuan *drifting* tajam di tikungan untuk mengisi *Drift Momentum Gauge* dan mendapatkan *boost* keluar tikungan.
3. **Risk-Reward Item Management:** Pemain mengambil *Item Box* dan langsung merasakan beban *debuff* (suspensi kart amblas, keluar partikel keringat/asap tanah liat, laju melambat). Pemain harus memutuskan kapan melempar *Power-Down* ke lawan atau menghitung sudut pantulan dinding untuk menangkap pantulan *Power-Up* sendiri.
4. **Tactical Clay Wall Placement:** Pemain membangun *Clay Wall* untuk menutup jalur lawan, memantulkan *item* ke diri sendiri, atau menabraknya sambil *drifting* untuk memicu *Wall-Ramp Boost* dan melompati rintangan/menyalip kerumunan.
5. **Finish & Podium:** Pemain berpacu menyelesaikan 3 *lap*; urutan finis 1–6 ditampilkan di podium tanah liat interaktif.

---

## 4. Detail Mekanik Inti

### 4.1. Racing & Drifting (Fisika & Kamera)
- **Drift-Centric Track Design:** Ketiga map dirancang dengan tikungan *hairpin*, jalur bercabang, dan celah lompatan yang mewajibkan penguasaan *drift* untuk menjaga kecepatan puncak.
- **Snappy Arcade Physics:** Menggunakan *Custom Raycast Vehicle Physics* (bukan `WheelCollider` kaku) agar transisi *hop*, *drift slide*, dan pendaratan udara terasa responsif serta ekspresif.
- **Dynamic Camera (Third-Person):** Dikendalikan oleh [Unity Cinemachine](https://docs.unity3d.com/Packages/com.unity.cinemachine@latest/); sudut pandang kamera melebar (*FOV fisheye effect*) saat *drifting* atau *wall-boosting* untuk menegaskan sensasi kecepatan tinggi.

### 4.2. The "Weight" Item System (Power-Down & Self-Bounce Power-Up)
- **Visual & Fisika Beban:** Saat membawa *item*, model 3D *item* terlihat bertumpuk di belakang kart. Suspensi belakang turun/penyok, kart mengeluarkan partikel tetesan/asap tanah liat, dan lawan dapat langsung membaca secara visual bahwa pemain tersebut sedang melambat dan membawa amunisi.
- **Kategori Item:**
  - **Offensive Power-Downs (Dilempar ke Lawan):** Beban sedang hingga berat (misal: *Heavy Clay Anvil*, *Bouncing Rolling Pin*, *Sticky Clay Glob*). Semakin mematikan efeknya terhadap lawan, semakin berat *debuff* kecepatan dan *drag* selama dibawa oleh pemain.
  - **Self-Bounce Power-Ups (Dipantulkan ke Diri Sendiri):** Beban ringan hingga sedang (misal: *Turbo Clay Berry*, *Elastic Mud Shield*). Tidak bisa diaktifkan langsung dengan satu tombol instan; pemain harus membidik dinding, rintangan panci, atau *Clay Wall* buatan sendiri agar proyektil memantul kembali dan ditangkap oleh kart sendiri.

### 4.3. Clay Wall Creation Mechanic (Defensif & Traversal Booster)
- **Spawning Dinding:** Pemain dapat mencetak dinding dari tanah liat di depan atau belakang kart dengan biaya *Clay Gauge* / *Cooldown*.
- **Fungsi 1 — Defensif & Pantulan:** Menghalangi laju lawan (lawan yang menabrak lurus tanpa *drift* akan terpental mundur) sekaligus menjadi bidang pantul instan untuk memantulkan *Self-Bounce Power-Up* ke diri sendiri di trek lurus.
- **Fungsi 2 — Ofensif / Traversal (Drift-Ramp Booster):** Dinding memiliki profil lengkung plastisin. Jika pemain melakukan *drift* dan menyapu/menabrak dinding sendiri (atau dinding rekan/lintasan) dalam *state* *drifting*, dinding bereaksi seperti ketapel/tanjakan elastis yang melontarkan kart ke depan-atas dengan *Speed Boost* besar untuk menyalip lawan dengan mudah.

### 4.4. Starting Position (RNG Dice)
- Sebelum lampu *start* menyala, seluruh 6 pemain melempar dadu tanah liat 3D untuk mengacak posisi *grid* 1–6.
- Mekanik *Wall-Ramp Boost* dan *item* pantul memastikan pemain yang mendapat angka dadu kecil (start di belakang) memiliki peluang *comeback* yang kuat sejak tikungan pertama.
- *Quality of Life:* Animasi lemparan dadu dapat langsung di-*skip* apabila ke-6 pemain menekan tombol *Ready/Skip* di *lobby* / *pre-race screen*.

---

## 5. World & Maps (3 Peta Utama)
Seluruh 3 map dirancang khusus untuk memaksimalkan ketergantungan pada *drifting*, *item bouncing*, dan *wall-ramp boosting*:

1. **Map 1: The Kitchen Counter (Dapur Raksasa)**
   - **Karakteristik Trek:** Meja dapur raksasa yang lebar dengan banyak sudut siku 90° dan *U-turn* tajam untuk *chain-drifting*.
   - **Rintangan & Interaksi:** Tumpukan piring keramik, panci logam, dan botol saus yang berfungsi sebagai dinding pantul alami untuk *item* lempar, serta celah wastafel yang bisa dilompati dengan *wall-ramp boost*.
2. **Map 2: The Clay Sculptor's Desk (Meja Kerja Pematung)**
   - **Karakteristik Trek:** Trek multi-level vertikal dengan banyak perbedaan ketinggian (rak buku, tumpukan balok tanah liat, papan pahat).
   - **Rintangan & Interaksi:** Sangat bergantung pada mekanik *Clay Wall-Ramp*; pemain yang membuat dinding di tepi meja dan melakukan *drift-boost* dapat memotong jalan ke level atas meja dan menghemat waktu signifikan.
3. **Map 3: The Playground (Taman Bermain Tanah Liat)**
   - **Karakteristik Trek:** Lintasan *rollercoaster* dinamis dengan *loop-the-loop*, *half-pipe* plastisin, dan tikungan miring (*banked curves*) tanpa pagar pembatas.
   - **Rintangan & Interaksi:** Fokus pada pelestarian *momentum drift*, pantulan dinding *half-pipe*, dan *air-boost* beruntun untuk menyalip di udara.

---

## 6. Win & Loss Conditions
- **Win Condition:** Menyelesaikan 3 *lap* dan melewati garis finis di posisi 1 (atau meraih poin akumulasi tertinggi setelah seri 3 map).
- **Loss / Setback Condition:** Finis di posisi terbawah (4–6), terjatuh ke luar batas meja/lintasan (*respawn* dengan penalti waktu singkat), atau terlalu lama menahan *item* berat sehingga tertinggal dari rombongan.

---

## 7. Art, UI/UX & Audio Direction

### 7.1. Visual Style (Claymation 3D)
- **Material & Shader:** Dibangun dengan [Unity Shader Graph](https://docs.unity3d.com/Packages/com.unity.shadergraph@latest/) menggunakan efek *subsurface scattering* lembut, *matte finish*, dan *normal map* sidik jari (*fingerprint*) yang bergeser secara periodik.
- **Stop-Motion Feel:** Dunia dan kamera tetap berjalan mulus di 60 FPS agar nyaman dimainkan, tetapi rotasi/pose lokal karakter, roda, dan deformasi kart di-*step* pada 12–15 FPS untuk ilusi film tanah liat klasik.

### 7.2. UI / UX (Tactile & Squishy)
- **Animasi Interaktif:** Seluruh tombol menu, kartu *lobby*, dan indikator HUD menggunakan [DOTween](https://dotween.demigiant.com/) di atas [Unity UI Toolkit](https://docs.unity3d.com/6000.4/Documentation/Manual/ui-systems/ui-shader-graph.html) / uGUI untuk efek *squash-and-stretch* (memantul dan penyok seperti plastisin saat diklik atau saat pemain terkena *debuff* beban).
- **Tipografi:** Menggunakan font bergaya plastisin membulat seperti [Fredoka](https://fonts.google.com/specimen/Fredoka) dan [Chewy](https://fonts.google.com/specimen/Chewy) dari Google Fonts (berlisensi OFL).
- **Diegetic Clay HUD:** Indikator posisi (1st–6th), *Lap Counter*, *Weight Meter* (beban item), dan *Clay Wall Charge* ditampilkan menyerupai tempelan catatan tanah liat di sudut layar.

### 7.3. Audio Direction (Foley & Dynamic SFX)
- **Tactile Foley:** Mengganti suara mesin konvensional dengan rekaman *foley* gesekan plastisin, tepukan tanah liat basah (*squish*), dan mainan pegas menggunakan [FMOD for Unity](https://www.fmod.com/unity).
- **Parameter Audio Dinamis:** Saat *drifting*, terdengar suara tanah liat tergores/terkelupas; saat membawa *item* berat, *pitch* mesin turun dan melewati filter *low-pass/muffled* untuk menegaskan beban kendaraan.

---

## 8. Controls & Input Mapping
Menggunakan [Unity Input System](https://docs.unity3d.com/Packages/com.unity.inputsystem@latest/) untuk mendukung Keyboard & Mouse serta Gamepad (hingga 6 pemain):

| Aksi | Keyboard & Mouse | Gamepad |
|---|---|---|
| **Accelerate (Gas)** | `W` / `Up Arrow` | `Right Trigger (RT / R2)` / `Button South (A / Cross)` |
| **Brake / Reverse** | `S` / `Down Arrow` | `Left Trigger (LT / L2)` |
| **Steer (Belok)** | `A` / `D` atau `Left` / `Right Arrow` | `Left Stick` |
| **Hop & Drift (Tahan)** | `Space` / `Left Shift` | `Right Bumper (RB / R1)` |
| **Aim & Throw Item (Depan/Belakang)** | `Left Click` / `E` (Tahan untuk garis bidik pantul) | `Button West (X / Square)` |
| **Spawn Clay Wall (Blocker / Ramp)** | `Right Click` / `Q` | `Button North (Y / Triangle)` |
| **Look Back (Kamera Belakang)** | `C` / `Middle Click` | `Right Stick Click (R3)` |
| **Skip Dice Roll / Pause Menu** | `Tab` / `Escape` | `Start / Options` |
