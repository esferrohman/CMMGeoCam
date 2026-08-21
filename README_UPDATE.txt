CMM GeoCam PWA - Prototype 2
=============================

Perubahan:
1. Tambah pilihan FOTO / VIDEO.
2. FOTO:
   - shutter putih
   - output JPG 1920x1080
   - watermark permanen pada file.
3. VIDEO:
   - shutter merah
   - tap pertama = mulai recording
   - tap kedua = stop
   - REC timer tampil
   - output WEBM 1280x720
   - watermark dinamis mengikuti STA/GPS selama recording.
4. Responsive portrait/landscape:
   - kanan atas tetap STA/Jalur/Segmen
   - kiri bawah tetap Koordinat/Speed/Offset/Status
   - landscape memindahkan panel teknis benar-benar ke kiri bawah.
5. Renderer watermark FOTO dan VIDEO disatukan agar hasil konsisten.
6. Service Worker dinaikkan dari cache v1 ke v2 dan memakai network-first
   supaya update GitHub lebih cepat muncul.

UPDATE REPOSITORY GITHUB
------------------------
Cara paling mudah:
1. Di repo CMMGeoCam buka Add file > Upload files.
2. Upload dua file ini:
   - index.html
   - service-worker.js
3. GitHub akan mengganti versi lama.
4. Commit changes.
5. Tunggu GitHub Pages selesai deploy.
6. Di HP tutup tab lama lalu buka ulang URL.
7. Jika masih terlihat versi lama:
   - refresh halaman
   - atau hapus site data/cache CMMGeoCam
   - jika PWA sudah di-install, tutup lalu buka kembali.

CATATAN VIDEO
-------------
- MVP video sengaja tanpa audio.
- Video memerlukan dukungan MediaRecorder + canvas.captureStream.
- Chrome/Chromium Android adalah target utama MVP ini.
- Perekaman hanya dapat dimulai saat Status Sistem = GPS READY.
