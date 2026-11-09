# ⚙️ Core Mechanics & Balancing Parameters — GIMERSIA

Dokumen ini menjadi acuan numerik dan rumus fisika bagi programmer maupun AI coding assistant saat menulis skrip C# di [Unity 3D](https://docs.unity3d.com/Packages/com.unity.shadergraph@latest/) dan [Photon Fusion 2](https://doc.photonengine.com/fusion/v2/fusion-intro).

---

## 1. Arcade Kart Physics & Suspension (Custom Raycast)
- `Base Top Speed` ($v_{\text{max}}$): `24.0 m/s`
- `Reverse Max Speed`: `10.0 m/s`
- `Forward Acceleration`: `18.0 m/s²`
- `Braking / Deceleration`: `26.0 m/s²`
- `Base Linear Drag` ($d_{\text{base}}$): `0.8`
- `Turn Torque / Steer Rate`: `115.0 deg/s` (berkurang `15%` pada kecepatan puncak, meningkat `+35%` saat *Drifting*)
- **Raycast Suspension (4 Titik Sudut):**
  - `Suspension Rest Length`: `0.55 m` (normal) -> turun menjadi `0.32 m` di roda belakang saat membawa *Heavy Item*
  - `Spring Stiffness` ($k$): `38000 N/m`
  - `Damper Strength` ($c$): `4200 N·s/m`
  - `Custom Gravity Scale`: `-28.0 m/s²` (agar lompatan *ramp* terasa *snappy* dan tidak melayang terlalu lama)

---

## 2. Drift & Dynamic Camera Mechanics
- `Drift Hop Vertical Impulse`: `6.5 m/s` (lompatan kecil saat memulai *drift*)
- `Minimum Speed to Initiate Drift`: `10.0 m/s`
- `Lateral Grip Factor`:
  - Normal Driving: `0.92` (cengkeraman ban tinggi)
  - Active Drifting: `0.48` (*sliding* menyamping yang responsif)
- **3-Tier Drift Charge & Boost:**
  - **Tier 1 (Mini Clay Spark):** Durasi *drift* `0.8s` $\rightarrow$ Boost `+20%` *Top Speed* (`28.8 m/s`) selama `0.9 detik`.
  - **Tier 2 (Super Clay Spark):** Durasi *drift* `1.8s` $\rightarrow$ Boost `+35%` *Top Speed* (`32.4 m/s`) selama `1.4 detik`.
  - **Tier 3 (Ultra Clay Surge):** Durasi *drift* `3.0s` $\rightarrow$ Boost `+50%` *Top Speed* (`36.0 m/s`) selama `2.0 detik`.
- **Dynamic Camera FOV ([Unity Cinemachine](https://docs.unity3d.com/Packages/com.unity.cinemachine@latest/)):**
  - `Base FOV`: `60.0°`
  - `Drift / Tier 1-2 Boost FOV`: `72.0°`
  - `Tier 3 / Wall-Ramp Boost FOV (Fisheye Effect)`: `82.0°` (interpolasi halus menggunakan `Mathf.SmoothDamp` dengan `smoothTime = 0.18s`).

---

## 3. The "Weight" Item System (Debuff & Bouncing Formulas)

### 3.1. Kalkulasi Debuff Beban Saat Membawa Item
Selama pemain menyimpan *item* di bak belakang kart (belum dilempar), kecepatan maksimum efektif ($v_{\text{max\_eff}}$) dan hambatan linear efektif ($d_{\text{eff}}$) dihitung menggunakan rumus:

$$v_{\text{max\_eff}} = v_{\text{max}} \times (1 - w_{\text{item}})$$

$$d_{\text{eff}} = d_{\text{base}} + \Delta d_{\text{item}}$$

### 3.2. Tabel Kelas Beban & Tipe Item
| Nama Item (Contoh) | Kategori | Cara Pakai | Bobot Penalty ($w_{\text{item}}$) | Drag Penalty ($\Delta d_{\text{item}}$) | Max Pantulan (`MaxBounces`) | Efek Saat Mengenai Target |
|---|---|---|---|---|---|---|
| **Turbo Clay Berry** | *Power-Up (Buff)* | **Lempar & Pantul ke Diri Sendiri** | `0.08` (-8% Speed) | `+0.25` | `3 kali` | Memberikan *Speed Boost* `+45%` selama `2.2 detik` dan membersihkan *debuff* lumpur. |
| **Elastic Mud Shield** | *Power-Up (Buff)* | **Lempar & Pantul ke Diri Sendiri** | `0.12` (-12% Speed) | `+0.35` | `3 kali` | Membungkus kart dengan perisai plastisin yang memantulkan 1 serangan lawan selama `6.0 detik`. |
| **Bouncing Rolling Pin** | *Power-Down (Offense)* | Lempar & Pantul ke Lawan | `0.18` (-18% Speed) | `+0.60` | `4 kali` | Memukul kart lawan hingga *spin-out* selama `1.1 detik` dan mengurangi kecepatan `-50%`. |
| **Sticky Clay Glob** | *Power-Down (Offense)* | Lempar & Pantul ke Lawan | `0.22` (-22% Speed) | `+0.75` | `2 kali` | Menempel di kart lawan dan menambahkan beban ekstra `-25%` *Max Speed* selama `4.0 detik`. |
| **Heavy Clay Anvil / Pot** | *Power-Down (Offense)* | Lempar Pendek / Pantul Dinding | `0.30` (-30% Speed) | `+1.10` | `1 kali` | Menciptakan gelombang kejut area (*AoE squash*) yang memipihkan kart lawan di sekitar titik jatuh selama `1.6 detik`. |

### 3.3. Fisika Pantulan Proyektil & Aturan Self-Hit
- **Rumus Vektor Pantulan Dinding:**
  ```csharp
  Vector3 reflectedVelocity = Vector3.Reflect(currentVelocity.normalized, hit.normal) * projectileSpeed * bounceRestitution;
  ```
- `Projectile Base Speed`: `34.0 m/s` (selalu lebih cepat dari *Base Top Speed* kart agar bisa mengejar atau memantul balik ke pemain).
- `Bounce Restitution`: `0.95` (hampir elastis sempurna pada dinding dapur, panci, atau *Clay Wall*).
- **Aturan Aktivasi Self-Bounce Power-Up:**
  - `SelfHitArmingDelay`: `0.20 detik` setelah dilempar ATAU wajib memiliki `CurrentBounceCount >= 1`.
  - Begitu `CurrentBounceCount >= 1`, proyektil *Power-Up* mengaktifkan *magnetism assist* ringan (`SphereCast` radius `1.6 m`) ke arah kart pemiliknya agar memantulkan *power-up* ke diri sendiri terasa memuaskan dan *skillful* tanpa membuat frustrasi saat kecepatan tinggi.

---

## 4. Clay Wall Creation Mechanic (Blocker vs. Drift-Ramp Booster)
- `Clay Wall Cooldown`: `9.0 detik` (atau dapat diisi lebih cepat dengan melakukan *drifting* sejauh `40 m`).
- `Wall Active Duration`: `7.5 detik` sebelum meleleh kembali ke lintasan.
- `Wall Dimensions`: Lebar `4.2 m`, Tinggi `1.8 m`, Tebal `1.2 m`.
- **Logika Cabang Kolisi (`ClayWallConstruct.cs`):**
  1. **Jika Ditabrak Proyektil Item:** Memantulkan proyektil dengan `bounceRestitution = 1.05` (sedikit mempercepat pantulan item).
  2. **Jika Ditabrak Kart TANPA Drifting (`!kart.IsDrifting`):** Berfungsi sebagai **Blocker Defensif** — kart lawan terpental mundur dengan impuls `14.0 m/s` dan kehilangan *momentum*.
  3. **Jika Ditabrak Kart SAMBIL Drifting (`kart.IsDrifting == true`):** Berfungsi sebagai **Ramp / Slingshot Booster** — dinding tanah liat tertekan menjadi tanjakan miring (`30°`) dan melontarkan kart:
     - `Vertical Launch Velocity`: `11.5 m/s` (cukup untuk melompati rintangan panci atau naik ke tingkat meja berikutnya di *Map 2*).
     - `Wall-Boost Speed Multiplier`: `1.45x` (`34.8 m/s`) selama `1.8 detik`.

---

## 5. Pre-Race RNG Dice Grid System (6 Pemain)
- `Player Count`: `6 Pemain` (Grid 1 hingga Grid 6).
- `Dice Range`: Dadu 6 sisi (`1` sampai `6`), diacak menggunakan `System.Random(networkSeed)` di *Host/Server* [Photon Fusion 2](https://doc.photonengine.com/fusion/v2/fusion-intro).
- **Aturan Penentuan Grid:**
  - Pemain diurutkan berdasarkan nilai lemparan dadu tertinggi ke terendah (Nilai tertinggi $\rightarrow$ Grid 1 terdepan; Nilai terendah $\rightarrow$ Grid 6 paling belakang).
  - Jika terjadi angka kembar (*tie*), sistem secara deterministik menjalankan *micro-tiebreak roll* instan pada *seed* yang sama sehingga setiap pemain pasti menempati slot unik `GridIndex [0..5]`.
  - `Grid Stagger Offset`: Jarak longitudinal antar baris adalah `3.5 m` dan jarak lateral `2.2 m`.
  - **Skip Votes:** Jika seluruh pemain aktif menekan tombol `Skip/Ready` (atau timer `6.0 detik` habis), kamera langsung melakukan transisi *cut* ke belakang kart masing-masing untuk hitung mundur `3-2-1-GO!`.
