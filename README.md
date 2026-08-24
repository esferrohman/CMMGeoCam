# CMM GeoCam

**Geo-Referenced Visual Inspection PWA**  
Prototype aktif: **12.4 — Direction-Aware Multi-Corridor**

CMM GeoCam adalah Progressive Web App untuk dokumentasi visual inspeksi jalan berbasis GPS. Sistem menghubungkan foto/video dengan posisi aktual kendaraan pada jaringan Jalan Tol Tangerang–Merak.

## Road Network

Aplikasi menggunakan dua database:

- `STA_Tamer.csv` — Mainline / Database Alignment
- `Corridor_Tamer.csv` — Ramp, Gate, dan Access

Status lokasi yang dapat ditampilkan:

- `READY · MAINLINE`
- `READY · RAMP`
- `READY · GATE`
- `READY · ACCESS`
- `GPS LOW`
- `OUTSIDE NETWORK`

Mainline dan corridor menggunakan metode **spatial-grid nearest matching** yang sama.

## Network Switching

Untuk mencegah watermark berpindah-pindah di interchange, aplikasi menggunakan persistence / hysteresis.

Generic network switching:

- minimum advantage: 7 m
- confirmation: 3 GPS fixes

Untuk sibling corridor pada lokasi dan tipe yang sama, misalnya `GATE ENTRANCE` vs `GATE EXIT`:

- minimum advantage: 2 m
- confirmation: 2 GPS fixes

Prototype 12.4 juga menggunakan **arah pergerakan kendaraan** dan arah geometri corridor untuk membantu membedakan jalur paralel Entrance/Exit.

## Watermark

Mainline:

```text
STA 56+350
JALUR A
Segmen ...
```

Ramp:

```text
BITUNG
RAMP B OFF
+175 m
```

Gate:

```text
BALARAJA BARAT
GATE PLAZA
ENTRANCE
```

Access:

```text
SERANG TIMUR
ACCESS PETAK PAPAT
ENTRANCE
```

Tulisan `JALUR A/B` dibuat lebih besar untuk meningkatkan keterbacaan saat video bergerak.

## Camera & Video

Fitur utama:

- landscape-only capture
- camera input ideal 1920×1080
- video output 1280×720
- Gyro-EIS / Native EIS detection
- manual frame synchronization
- MP4 / H.264 preferred
- WEBM fallback
- MMS logo burn-in
- road-first watermark layout

## Rolling Recording

Prototype 12.3+ menggunakan **rolling finalized recording**.

Satu sesi REC dibagi otomatis setiap maksimal 2 menit:

```text
SEG001 → finalize
SEG002 → finalize
SEG003 → finalize
...
```

Kamera, GPS, watermark, dan compositor tetap berjalan selama pergantian segmen.

Tujuannya:

- menjaga penggunaan memori
- memberi setiap file kesempatan untuk memfinalisasi container MP4
- meningkatkan kompatibilitas seek/editor
- mempersiapkan upload cloud pada pengembangan berikutnya

## File Utama Repository

```text
index.html
service-worker.js
manifest.webmanifest
STA_Tamer.csv
Corridor_Tamer.csv
logo-mms.png
icon-192.png
icon-512.png
README.md
```

## Field Test Priority

Pengujian utama Prototype 12.4:

```text
ACCESS ENTRANCE
→ GATE PLAZA ENTRANCE
→ RAMP ON
→ MAINLINE
```

dan arah sebaliknya:

```text
MAINLINE
→ RAMP OFF
→ GATE PLAZA EXIT
→ ACCESS EXIT
```

Perhatikan terutama saat:

- mendekati gerbang
- berhenti di gerbang
- mulai bergerak kembali
- berpindah antara mainline dan ramp

## Changelog

### Prototype 12.4
- direction-aware corridor matching
- perbaikan Entrance vs Exit pada corridor paralel
- sibling corridor hysteresis
- watermark corridor diperlebar untuk nama lokasi panjang

### Prototype 12.3
- mainline dan corridor memakai unified spatial grid
- rolling finalized recording setiap 2 menit

### Prototype 12.2
- integrasi `Corridor_Tamer.csv`
- Multi-Corridor: Mainline, Ramp, Gate, Access
- network persistence / hysteresis
- watermark corridor
- ukuran tulisan Jalur diperbesar

### Prototype 12.1
- MP4 seek-safe finalize tanpa periodic MP4 timeslice

### Prototype 12
- Gyro-EIS experimental
- 1920×1080 camera overscan
- 1280×720 recording output
- stabilization diagnostic panel

---

**Current development branch:** Prototype 12.4
