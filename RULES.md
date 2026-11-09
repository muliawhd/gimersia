# Coding & AI Behavior Rules (Gimersia 2026)

## 1. Gimersia Compliance (CRITICAL)
- **STRICT PROHIBITION (Buku Panduan VIII.2 & VIII.3):** DILARANG menggunakan AI untuk menghasilkan aset visual (sprite, texture, 2D/3D model, UI illustration, promo art).
- AI HANYA digunakan untuk assisted programming (vibe coding), arsitektur logika, math/algoritma, unit testing, dan dokumentasi.
- Jika butuh aset visual placeholder di engine, instruksikan pembuatan *primitive shape* (Cube, Sphere, Quad, UI basic fill) atau arahkan ke library CC0/Asset Store legal.

## 2. Code Architecture & Conventions
- Engine: Unity (C#) / Godot (GDScript / C#) / Unreal (C++ / Blueprint).
- Clean Code: Single Responsibility Principle (SRP). Maksimal 300 baris per script. Pecah modular jika melebihi batas.
- Pattern: Hindari antipattern global singleton yang berlebihan; batasi hanya untuk Core/Service Management (misal: `GameManager`, `AudioManager`).
- Naming Convention:
  - Classes, Interfaces, Structs, Methods, Properties: `PascalCase`
  - Private fields, local variables, parameters: `camelCase` (private fields diawali `_` jika di Unity/C#)
  - Constants: `SCREAMING_SNAKE_CASE`
- Dokumentasi: Tambahkan XML doc summary untuk public API, method, dan konfigurasi inspector.

## 3. Workflow & Output Guidelines
- Selalu prioritaskan performa game jam: solusi sederhana, modular, dan cepat di-debug dibanding arsitektur over-engineered.
- Ketika mengusulkan skrip baru, sediakan instruksi penempatan komponen pada GameObject/Node terkait.
