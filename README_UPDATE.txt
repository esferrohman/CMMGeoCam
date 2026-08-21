CMM GeoCam PWA - Prototype 4
MP4-FIRST + LOCAL-ONLY
==============================

Perubahan dari Prototype 3:
1. Video sekarang mencoba MP4 terlebih dahulu.
2. Prioritas format:
   - video/mp4 + H.264 (jika browser mendukung)
   - video/mp4 generic
   - fallback WEBM VP9/VP8/WEBM
3. Ekstensi file mengikuti format sebenarnya.
   Tidak ada penggantian ekstensi palsu dari WEBM menjadi MP4.
4. Jika MP4 didukung:
   CMMGeoCam_VIDEO_STA_56-350_20260821_173500.mp4
5. Jika MP4 tidak didukung:
   CMMGeoCam_VIDEO_STA_56-350_20260821_173500.webm
6. Nama file foto juga dirapikan:
   CMMGeoCam_STA_56-350_20260821_173500.jpg
7. Foto/video tetap LOCAL-ONLY.
   Tidak ada upload ke GitHub, Google Drive, server, atau cloud.
8. Landscape, logo MMS, watermark, Foto/Video mode tetap dipertahankan.
9. Cache Service Worker dinaikkan ke v4 agar update lebih mudah muncul.

FILE YANG PERLU DI-UPDATE DI GITHUB
-----------------------------------
Cukup ganti:
- index.html
- service-worker.js

Cara:
1. Repository CMMGeoCam > Add file > Upload files.
2. Upload index.html dan service-worker.js.
3. Commit changes.
4. Tunggu GitHub Pages deploy.
5. Tutup CMM GeoCam di HP.
6. Buka ulang dari Home Screen / browser.
7. Jika masih versi lama, refresh atau hapus cache/site data.

CATATAN FORMAT VIDEO
--------------------
Dukungan MP4 melalui MediaRecorder tergantung browser/perangkat.
Karena itu Prototype 4 memakai MP4-first dengan fallback WEBM.
Hal ini lebih aman daripada memaksa MP4 pada perangkat yang tidak mendukung.

PENYIMPANAN
-----------
Hasil capture diunduh ke penyimpanan lokal perangkat melalui browser.
Prototype ini tidak mempunyai kode upload cloud.
