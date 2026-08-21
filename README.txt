CMM GeoCam PWA Starter
=======================

Isi paket:
- index.html
- manifest.webmanifest
- service-worker.js
- STA_Tamer.csv
- icon-192.png
- icon-512.png

Watermark:
Kanan atas:
- STA
- Jalur
- Segmen

Kiri bawah:
- Koordinat
- Speed
- Offset Alignment
- Status Sistem

Cara tercepat:
1. Ekstrak ZIP.
2. Buat repository GitHub baru.
3. Upload SEMUA file di dalam folder ini ke root repository.
4. Settings > Pages > Deploy from a branch > main > /(root) > Save.
5. Tunggu URL GitHub Pages aktif.
6. Buka URL tersebut dari Chrome Android.
7. Izinkan Camera dan Precise Location.
8. Chrome menu > Install app / Add to Home screen.

Catatan:
- Kamera dan GPS memerlukan HTTPS. GitHub Pages sudah HTTPS.
- Service worker membuat app shell dan STA_Tamer.csv tersedia dari cache setelah berhasil dimuat.
- Prototype ini fokus FOTO. Video dinamis bisa ditambahkan pada versi berikutnya.
