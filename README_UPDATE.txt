CMM GeoCam - Prototype 10
ROAD-FIRST WATERMARK
=============================

Tujuan
------
Mengurangi area jalan yang tertutup watermark tanpa menghilangkan
informasi verifikasi lokasi.

Perubahan dari Prototype 9
--------------------------

KANAN ATAS
- panel dibuat lebih kecil
- lebar tetap mengikuti ukuran STA
- maksimum panel diperkecil
- STA sedikit diperkecil
- Jalur dan Segmen dibuat lebih ringkas
- tinggi panel berkurang
- opacity panel diturunkan

KIRI BAWAH
- tetap satu baris:
  Koordinat • Speed • Offset • Status
- tinggi bar diperkecil
- font diperkecil sedikit
- opacity panel diturunkan

LOGO MMS
- tetap kiri atas
- burn-in ke foto/video
- ukuran sedikit diperkecil agar tidak mengganggu objek inspeksi

PRINSIP DESAIN
--------------
1. Area tengah frame harus bebas watermark.
2. Watermark hanya berada di sudut.
3. STA tetap menjadi informasi paling menonjol.
4. Informasi teknis hanya sebagai validasi, bukan elemen utama.
5. Objek jalan/kerusakan harus tetap menjadi fokus dokumentasi.

Fitur lain tetap:
- strict landscape
- 1280x720
- ideal camera 30 fps
- manual frame sync
- requestVideoFrameCallback
- overlay cache
- MP4-first / WEBM fallback
- local-only
- Auto-STA / Jalur / Segmen / Offset / GPS Status

UPDATE GITHUB
-------------
Untuk update repository lama, cukup ganti:
- index.html
- service-worker.js

Untuk install/repository baru, gunakan paket FULL.
