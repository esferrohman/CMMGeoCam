CMM GeoCam - Prototype 11
FIELD TEST FINAL
===========================

Status
------
Versi final sebelum uji lapangan.

Revisi terakhir dari Prototype 10
---------------------------------
1. Watermark teknis kiri bawah tetap SATU BARIS.
2. Font hasil foto/video dinaikkan:
   - Label : 0.016 × frame height
   - Value : 0.018 × frame height
   Pada video 720p kira-kira:
   - Label ≈ 11.5 px
   - Value ≈ 13.0 px
3. Tinggi bar teknis sedikit dinaikkan dari 5.0% menjadi 5.5%
   agar teks tidak terlalu mepet.
4. Preview font teknis sedikit diperbesar.
5. Variabel lastFrameTime yang sudah tidak digunakan dihapus.
6. Tidak ada perubahan pada pipeline video atau algoritma Auto-STA.

Fitur yang dikunci untuk uji lapangan
-------------------------------------
- Strict landscape 16:9
- Kamera 1280x720, ideal/max 30 fps
- Actual camera FPS monitoring
- requestVideoFrameCallback
- captureStream(0) + requestFrame jika didukung
- fallback fixed-frame stream jika diperlukan
- cached overlay rendering
- MP4/H.264 first, fallback WEBM
- logo MMS burn-in
- watermark kanan atas:
  STA / Jalur / Segmen
- watermark kiri bawah satu baris:
  Koordinat • Speed • Offset • Status
- file local-only
- Auto-STA / Jalur / Segmen / Offset Alignment / GPS Status

UPDATE GITHUB
-------------
Cukup ganti:
- index.html
- service-worker.js

UJI LAPANGAN BESOK
------------------
Catat minimal:
1. Actual camera FPS
2. Kelancaran video
3. Keterbacaan watermark
4. STA saat kendaraan bergerak
5. Jalur A/B
6. Pergantian Segmen
7. Offset Alignment
8. Status GPS READY / OFF ROUTE
9. Hasil MP4 atau fallback WEBM
10. Apakah logo MMS tetap burn-in pada hasil file
