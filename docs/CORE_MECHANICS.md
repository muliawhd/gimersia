# Core Mechanics & Balancing Parameters

Dokumen ini menjadi acuan AI saat menyeimbangkan formula, kecepatan, atau parameter kalkulasi game.

## 1. Movement Parameters
- `Base Move Speed`: 6.0 units/sec
- `Sprint Multiplier`: 1.4x
- `Acceleration`: 12.0
- `Deceleration / Friction`: 18.0

## 2. Jump & Gravity Mechanics (Jika Platformer)
- `Gravity Scale`: -22.0 m/s²
- `Jump Velocity`: 14.5 m/s
- `Coyote Time`: 0.15 detik
- `Jump Buffer Time`: 0.10 detik
- `Fall Multiplier`: 2.5x (untuk snappy gravity feel)

## 3. Combat / Damage Calculations
- `Player Max HP`: 100
- `Invulnerability Frame (i-frame)`: 0.8 detik
- `Formula Damage Received`: $Damage = RawDamage 	imes rac{100}{100 + Armor}$
