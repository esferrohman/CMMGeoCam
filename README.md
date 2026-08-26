# CMM GeoCam

**Geo-Referenced Visual Inspection PWA**  
Prototype aktif: **13 — Enhanced Direction & Recording UI**

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


## Prototype 13 Enhancements

Prototype 13 memperkuat penggunaan CMM GeoCam untuk dokumentasi foto dan video di lapangan.

### STA selalu lengkap

Informasi utama tidak lagi menggunakan ellipsis.

Contoh yang harus selalu terbaca penuh:

```text
STA 75+850
JALUR A
Segmen Serang Timur - Serang Barat
```

Jika lebar panel terbatas, ukuran font utama menyesuaikan secara otomatis. Nilai STA dan JALUR tidak dipotong menjadi `...`.

### Mainline Direction-Aware

Penentuan `JALUR A/B` sekarang menggunakan dua sumber bukti:

1. **Travel bearing kendaraan vs local alignment bearing** sebagai sumber utama.
2. **Trend perubahan STA** sebagai fallback.

Arah geometri mainline dihitung dari Database Alignment. Arah increasing STA diperlakukan sebagai `JALUR A`, sedangkan arah berlawanan sebagai `JALUR B`, konsisten dengan logika trend sebelumnya.

Arah perjalanan terakhir yang reliabel dipertahankan ketika kendaraan melambat atau berhenti. Dengan demikian foto yang diambil saat kendaraan berhenti di bahu jalan tidak langsung kehilangan informasi jalur.

Jika aplikasi baru dibuka ketika kendaraan sudah diam dan belum pernah mendapat bukti arah yang cukup, aplikasi tetap menampilkan `JALUR --` daripada menebak.

### Total REC Timer

Saat video direkam, UI menampilkan:

```text
● REC 00:07:32
```

Timer menghitung **durasi total sesi recording** dalam format `HH:MM:SS`.

Rolling finalized recording tetap memotong file setiap maksimal 2 menit, tetapi timer **tidak reset** saat pergantian segmen.

Timer hanya merupakan bantuan operator pada UI dan tidak ditambahkan lagi ke burned-in watermark video karena video sudah memiliki timestamp tanggal dan jam aktual.


## Timestamp & Weather

Prototype 12.5 menambahkan metadata waktu dan kondisi cuaca pada preview, foto, dan video.

Format watermark bawah:

```text
24/08/2026 10:03:32 • CUACA BERAWAN
Koordinat ... • Speed ... • Offset ... • Status ...
```

Timestamp menggunakan **tanggal dan jam lokal perangkat** dan diperbarui setiap detik.

Mode cuaca:

- `AUTO`
- `CERAH`
- `BERAWAN`
- `GERIMIS`
- `HUJAN`
- `HUJAN LEBAT`

Pada `AUTO`, versi prototype/evaluation mengambil `weather_code` berdasarkan GPS dan menerjemahkannya ke kategori operasional di atas. Refresh dilakukan sekitar setiap 10 menit atau setelah kendaraan berpindah sekitar 3 km.

Inspector dapat melakukan **manual override** kapan saja. Manual override sengaja dipertahankan karena kondisi visual di titik inspeksi dapat berbeda dari model cuaca/grid forecast.

Jika koneksi/cuaca AUTO tidak tersedia, locator dan perekaman tetap berjalan. Inspector dapat memilih kondisi cuaca manual.

> Catatan penggunaan: endpoint cuaca AUTO pada Prototype 12.5 ditujukan untuk evaluasi/prototyping. Untuk penggunaan operasional perusahaan, gunakan weather provider/API yang telah disetujui perusahaan dan memiliki lisensi komersial yang sesuai.

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

### Prototype 13
- STA dan informasi utama tidak lagi terpotong dengan ellipsis
- adaptive watermark font untuk menjaga nilai STA tetap lengkap
- penentuan JALUR A/B menggunakan travel bearing + local mainline bearing
- arah terakhir dipertahankan ketika kendaraan melambat/berhenti
- STA trend tetap tersedia sebagai fallback
- total REC timer `HH:MM:SS`
- REC timer tidak reset saat rolling segment 2 menit berganti
- REC timer diposisikan dekat kontrol recording agar lebih mudah dipantau

### Prototype 12.5
- timestamp tanggal dan jam lokal perangkat
- timestamp video dinamis setiap detik
- cuaca AUTO berdasarkan posisi GPS
- manual weather override: Cerah, Berawan, Gerimis, Hujan, Hujan Lebat
- watermark bawah menjadi dua baris
- locator/recording tetap bekerja saat weather service offline
- live corridor watermark width diselaraskan dengan output agar nama lokasi panjang tidak terpotong

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

**Current development branch:** Prototype 13
