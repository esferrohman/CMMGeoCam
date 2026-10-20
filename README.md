# CMM GeoCam 5.2.C — Screen Wake Lock + Arsip Rekaman Lokal


## Pembaruan 5.2.C (20 Oktober 2026)

### Prioritas 1: Screen Wake Lock
- Saat kamera aktif di landscape, aplikasi meminta izin **Screen Wake Lock** dari browser agar layar tidak mati karena timeout biasa.
- Label `LAYAR: AKTIF` menandakan Wake Lock berhasil. `LAYAR: GAGAL`/`TIDAK DIDUKUNG` berarti fitur tidak tersedia dan layar mungkin tetap mati.
- Saat beralih ke portrait, aplikasi dibawa ke background, atau tab disembunyikan, Wake Lock dilepaskan.
- Saat halaman kembali terlihat di landscape dan kamera aktif, Wake Lock diminta ulang.
- Jika halaman tersembunyi ketika merekam, sesi REC dihentikan secara aman agar segmen terakhir bisa difinalisasi. Pengguna perlu memeriksa Arsip Video.
- Wake Lock tidak dapat menjamin rekaman berjalan bila browser, OS, panggilan telepon, mode hemat daya, atau perangkat memutus proses.

### Prioritas 2: Arsip lokal & ekspor manual video
- Perekaman masih memakai segmen **maksimum 2 menit** dan komposit watermark yang sama.
- Setiap segmen yang selesai difinalisasi oleh MediaRecorder lalu **disimpan atomik di IndexedDB** bersama metadata sesi.
- **Tidak ada unduhan otomatis setiap 2 menit**. Tombol **ARSIP VIDEO** menampilkan daftar sesi, segmen, durasi, dan ukuran. Arsip dapat dibuka dari portrait ataupun landscape.
- Setelah stop, tekan **ARSIP VIDEO → Siapkan ZIP → Unduh ZIP**. Satu ZIP berisi seluruh video sesi secara terpisah serta `INFO_REKAMAN.txt`.
- Bisa mengunduh video satu per satu. Jika perekaman/browser terhenti mendadak, segmen yang telah disimpan tetap ada untuk diekspor saat membuka aplikasi lagi.
- Saat penyimpanan lokal gagal (mis. kuota penuh), perekaman dihentikan dan segmen yang gagal masih ditawarkan untuk unduhan darurat selama aplikasi belum ditutup.
- Segmen yang belum mencapai proses `onstop`/finalisasi saat browser crash **tidak dijamin pulih**. Uji rekaman panjang diperlukan di perangkat asli.
- Tidak ada fungsi penyatuan segmen otomatis; ZIP hanya mengumpulkan file individual.
- Jangan menghapus data situs (Clear Storage/Cache & Site Data), melepas aplikasi PWA, atau memakai mode incognito sebelum ekspor dan verifikasi berkas.
- Penyimpanan besar memerlukan ruang kosong yang cukup; pemrosesan ZIP besar memerlukan waktu. ZIP tunggal mendukung hingga batas ZIP32 ~4 GB, selebihnya unduh per segmen.

### GPS dan fitur existing
- `MAX_GPS_ACCURACY = 25` m. Ini adalah gate estimasi akurasi GPS, **bukan** bukti deviasi STA terhadap Ledger ≤25 m.
- GPS LOW sebelum REC memblokir capture, namun GPS LOW selama REC tidak otomatis menghentikan rekaman.
- Mainline JALUR A/B masih memakai bearing kendaraan/tren STA pada build ini. Pemisahan berdasarkan database spasial Jalur A/B menunggu data alignment dua jalur dari Inspector.
- Pinch zoom 1×–4×, preset zoom, kamera, foto, watermark, mainline/corridor, dan cuaca dipertahankan.

### Cara uji lapangan
1. Pasang semua file 5.2.C ke repo HTTPS yang sama. Reload PWA sampai judul 5.2.C muncul.
2. Putar ke landscape dan aktifkan kamera. Periksa label `LAYAR: AKTIF` (atau laporkan jika tidak didukung).
3. Saat status lokasi READY, rekam selama lebih dari 9 menit tanpa menyentuh layar. Hindari berpindah tab/portrait selama REC.
4. Hentikan REC. Buka ARSIP VIDEO, pastikan ada kurang lebih 5 segmen dengan durasi total mendekati durasi REC.
5. Siapkan ZIP, unduh ZIP satu kali, dan **cek semua file SEG001...SEG005 bisa diputar** sebelum menghapus arsip lokal.
6. Uji sekali lagi dengan mengunci layar/memindah aplikasi ke background untuk memastikan penghentian dan pemulihan segmen terakhir sesuai desain.

## Pembaruan 5.2.B
- Auto-STA portrait: tata letak header/logo lebih ringkas, hierarki kartu STA lebih kuat, telemetry grid lebih seimbang, teks sumber database, jarak lebih teratur.
- GeoCam landscape: pinch-to-zoom dua jari pada area preview kamera dari 1.0x sampai 4.0x (step 0.05x).
- Preset 1x / 2x / 4x tetap tersedia.
- Nilai zoom kontinu ditampilkan langsung di panel; tombol preset menandai nilai tepat saja.
- Tetap menggunakan native camera zoom bila diekspos browser; selebihnya digital crop yang sama untuk preview, foto dan video.
- Matching STA, corridor, jalur A/B, weather, stabilisasi, dan rolling recording tidak diubah.
- Portrait mode khusus informasi Auto-STA; photo/video tetap landscape.

## Catatan uji perangkat
Pinch bekerja saat dua jari dimulai di AREA GAMBAR kamera (bukan tombol atau watermark). Zoom 4x digital dapat mengurangi detail visual. Ketersediaan native zoom dan respons EIS berbeda antar perangkat/browser; uji langsung di smartphone.

---

# CMM GeoCam

**Geo-Referenced Visual Inspection PWA**  
Prototype aktif: **5.2.A — GPS Auto-STA + GeoCam Zoom**

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



## Prototype 5.1.A — Corridor 102 + Right-Hand Landscape Controls

Build 5.1.A menggabungkan database corridor final 102 corridor dengan perbaikan ergonomi kontrol landscape.


### Database corridor final

Runtime `Corridor_Tamer.csv` diperbarui dari database lama menjadi hasil Stage 4 final:

- 102 corridor tervalidasi
- 8.339 titik referensi corridor
- sampling internal 5 m
- endpoint aktual tetap dipertahankan
- mencakup RAMP, GATE, ACCESS, dan corridor Rest Area terbaru

### Perbaikan shutter landscape

Pada build sebelumnya, parent `.bottom` masih membawa `transform: translateX(-50%)`
dari layout landscape lama. Setelah parent diperluas menjadi fullscreen, transform tersebut
menggeser seluruh sistem koordinat kontrol ke kiri.

Prototype 5.1.A mereset parent control layer dengan:

```css
transform: none !important;
left: 0 !important;
right: 0 !important;
width: auto !important;
```

Kemudian cluster `FOTO / VIDEO + shutter` di-anchor langsung ke sisi kanan layar.

### Portrait (riwayat versi 5.1.A, digantikan oleh Auto-STA pada 5.2.A)

- shutter tetap di bawah tengah
- selector `FOTO / VIDEO` tetap menggunakan pola kamera biasa

### Landscape

- shutter otomatis pindah ke **kanan tengah**
- selector `FOTO / VIDEO` ikut pindah ke thumb-zone
- ukuran shutter diperbesar agar mudah dijangkau ibu jari
- timer `REC HH:MM:SS` berada di sebelah kiri kontrol agar tidak tertutup tangan
- evidence bar bagian bawah dibatasi agar tidak masuk ke area shutter
- area tengah jalan tetap terbuka

Rotasi hanya mengubah posisi UI. Locator, watermark hasil, timestamp, weather, Gyro-EIS, multi-corridor, dan rolling recording tidak berubah.


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

## Prototype 5.2.A — Integrasi Auto-STA dan GeoCam + Zoom

**Tampilan portrait:** GPS AUTO-STA LOCATOR. STA/Jalur/Segmen untuk MAINLINE,
serta Lokasi/Tipe/Arah/Jarak untuk RAMP, GATE, dan ACCESS. Menampilkan status GPS,
koordinat, akurasi GPS, offset network, kecepatan, dan timestamp. Tombol
“Aktifkan GPS Presisi” bisa digunakan tanpa menghidupkan kamera.

**Tampilan landscape:** CMM GeoCam 16:9 dengan kontrol FOTO/VIDEO dan shutter
pada sisi kanan, watermark, cuaca, stabilisasi, dan rolling recording 2 menit.
Tambahan tombol zoom **1×, 2×, 4×** berfungsi untuk preview, foto, dan video.
Bila `MediaStreamTrack.getCapabilities().zoom` tersedia, aplikasi mencoba zoom
device terlebih dahulu, lalu memakai digital crop untuk sisa perbesaran.
Bila tidak tersedia/gagal, zoom memakai digital crop penuh. Pada zoom digital
4×, kualitas gambar berpotensi menurun karena jumlah piksel sumber lebih sedikit.

**Perubahan orientasi:** Manifest dan browser tidak mengunci landscape. Data GPS/network dan bearing kendaraan dipertahankan.
Jika perekaman sedang berjalan dan pengguna memutar ke portrait,
perekaman difinalisasi otomatis sebagaimana mekanisme 5.1.A. GPS tidak
melakukan start ulang. Kamera cukup diaktifkan satu kali dari landscape.

**Penting:** Kamera dan GPS memerlukan HTTPS/localhost dan izin browser.
Pastikan file diunggah dengan nama tepat (tanpa angka suffix unduhan).
Jalankan sebagai static website/PWA; membuka HTML via `file://` tidak cukup.

## Changelog

### Prototype 5.2.A
- integrasi portrait GPS Auto-STA dan landscape GeoCam dalam satu halaman
- GPS watcher dan hasil matching dipakai bersama saat orientasi berubah
- zoom preset 1×, 2×, 4× untuk preview, hasil foto, dan rekaman video
- hardware zoom bila tersedia, digital fallback bila tidak
- cache Service Worker dibedakan dari build 5.1.A
- PWA manifest tidak lagi mengunci landscape secara permanen


### Prototype 13.1
- shutter landscape dipindahkan ke kanan tengah
- FOTO/VIDEO ikut berada di thumb-zone kanan
- shutter landscape diperbesar untuk penggunaan satu tangan
- portrait tetap bawah-tengah
- timer REC diposisikan agar tidak tertutup tangan
- tidak ada perubahan pada matcher, database, weather, watermark, atau recording engine

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

**Current development branch:** Prototype 5.2.C
