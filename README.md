# CMM GeoCam — Prototype 5.2.E

**Rilis 5.2.E merupakan penggantian penamaan dari 5.2.D.1 (Archive Connection Fix).** Mekanisme perekaman, GPS, zoom, watermark, dan struktur database tidak diubah. Yang diperbarui adalah identitas build pada halaman, metadata PWA, versi URL file CSV, cache service worker, dan informasi versi yang disertakan dalam ekspor arsip.

## Perbaikan arsip yang dipertahankan

Menangani error Chrome Android: `Failed to execute 'transaction' on 'IDBDatabase': The database connection is closing.` Jika koneksi IndexedDB sudah ditutup oleh browser, aplikasi menginvalidasi koneksi lama dan mencoba membuka koneksi baru. Saat pembuatan transaksi gagal karena koneksi sedang ditutup, sistem mencoba sekali lagi dengan koneksi yang baru. Panel Arsip dilengkapi opsi **Coba Buka Ulang Arsip** saat pembacaan gagal.

**Kompatibilitas data:** Nama database `CMMGeoCamRecordings`, versi skema `1`, dan object store `sessions` / `segments` tetap sama. Rekaman tersimpan pada origin/domain dan profil browser yang sama; perbarui situs pada URL yang sama agar arsip 5.2.C/5.2.D tetap dapat diakses. Jangan memilih Hapus data situs, membersihkan penyimpanan Chrome, atau uninstall PWA sebelum semua arsip penting diekspor dan diverifikasi.

## Cara kerja video

- Satu sesi REC menggunakan satu `MediaRecorder` berkelanjutan, sehingga setelah STOP normal, potongan data hasil satu recorder dapat disusun menjadi **satu file video** tanpa re-encoding.
- Browser diminta mengeluarkan checkpoint setiap ~30 detik. Browser, terutama pada format MP4, dapat menunda data sampai STOP. **Checkpoint bukan jaminan pemulihan jika browser ditutup paksa.**
- Arsip Video menawarkan **Siapkan Video Utuh → Unduh Video**. Format keluaran MP4 atau WebM mengikuti dukungan browser; jangan sekadar mengubah nama ekstensi.
- Arsip lama 5.2.C masih diekspor sebagai ZIP berisi segmen video mandiri. Menyatukan byte dari MP4 independen bukan metode penggabungan yang valid.
- Rekaman yang dihentikan abnormal belum tentu lengkap. Periksa durasi dan pemutaran dari awal hingga akhir sebelum menghapus arsip lokal.

## Cara memasang versi 5.2.E

1. Ekstrak ZIP dan **timpa seluruh berkas** di direktori GitHub Pages yang sama. Pastikan `index.html`, `manifest.webmanifest`, dan `service-worker.js` ikut terganti dan deploy telah berhasil.
2. Buka kembali situs di Chrome. Periksa tampilan portrait: **CMM GEOCAM 5.2.E**. Pembaruan service worker dapat memerlukan buka ulang tab. Jangan hapus data situs untuk memaksa pembaruan.
3. Pilih **Arsip Video** untuk memastikan sesi lama masih muncul dan dapat diekspor.
4. Untuk uji baru, masuk landscape, aktifkan kamera, tunggu status READY, rekam 9–10 menit, tekan STOP, lalu pilih **Siapkan Video Utuh → Unduh Video**.
5. Cek durasi, seluruh isi video, logo/watermark, dan format file hasil ekspor. Ulangi uji 20–30 menit bila uji 10 menit berhasil.

## Perilaku yang tidak berubah

| Komponen | Kondisi 5.2.E |
|---|---|
| GPS capture gate | `MAX_GPS_ACCURACY = 25` m saat memulai foto/video; bukan jaminan deviasi STA terhadap Ledger ≤25 m |
| GPS saat REC | Penurunan kualitas GPS tidak otomatis menghentikan REC berjalan |
| Jalur A/B mainline | Masih logika arah/tren lama; belum pemisahan alignment spasial per jalur |
| Mode | Portrait = Auto-STA Locator; landscape = GeoCam 16:9 |
| Kamera | Foto, video, watermark, stabilisasi, zoom 1×/2×/4× dan pinch-to-zoom |
| Screen Wake Lock | Diminta saat kamera landscape aktif; tergantung kemampuan Android/browser |
| Arsip | IndexedDB pada domain/profil Chrome yang sama, dengan ekspor video utuh dan ZIP cadangan |
| Database jalan | `STA_Tamer.csv` dan `Corridor_Tamer.csv` tidak berubah |

## Batasan validasi

Perbaikan koneksi IndexedDB dan ekspor video sebelumnya telah diuji dalam simulasi Chromium. **5.2.E adalah perubahan versi tanpa perubahan fungsional baru**; kestabilan penyimpanan dan REC panjang tetap harus diuji langsung pada Android. Jika error penyimpanan berulang, jangan bersihkan data browser; dokumentasikan pesan error, durasi REC, dan jumlah segmen terdaftar.

## Isi paket

`index.html`, `manifest.webmanifest`, `service-worker.js`, `STA_Tamer.csv`, `Corridor_Tamer.csv`, `logo-mms.png`, `icon-192.png`, `icon-512.png`, `README.md`.
