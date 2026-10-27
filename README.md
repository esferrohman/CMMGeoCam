# CMM GeoCam — Prototype 5.2.F

**Rilis 5.2.F melanjutkan 5.2.E dengan Auto-Download setelah STOP.** Sistem tetap merekam melalui satu MediaRecorder, menyimpan ke IndexedDB, lalu meminta unduhan satu video utuh secara otomatis setelah finalisasi. Perbaikan koneksi arsip 5.2.E tetap dipertahankan. GPS, zoom, watermark, dan skema database tidak berubah.

## Perbaikan arsip yang dipertahankan

Menangani error Chrome Android: `Failed to execute 'transaction' on 'IDBDatabase': The database connection is closing.` Jika koneksi IndexedDB sudah ditutup oleh browser, aplikasi menginvalidasi koneksi lama dan mencoba membuka koneksi baru. Saat pembuatan transaksi gagal karena koneksi sedang ditutup, sistem mencoba sekali lagi dengan koneksi yang baru. Panel Arsip dilengkapi opsi **Coba Buka Ulang Arsip** saat pembacaan gagal.

**Kompatibilitas data:** Nama database `CMMGeoCamRecordings`, versi skema `1`, dan object store `sessions` / `segments` tetap sama. Rekaman tersimpan pada origin/domain dan profil browser yang sama; perbarui situs pada URL yang sama agar arsip 5.2.C/5.2.D tetap dapat diakses. Jangan memilih Hapus data situs, membersihkan penyimpanan Chrome, atau uninstall PWA sebelum semua arsip penting diekspor dan diverifikasi.

## Cara kerja video

- Satu sesi REC menggunakan satu `MediaRecorder` berkelanjutan, sehingga setelah STOP normal, potongan data hasil satu recorder dapat disusun menjadi **satu file video** tanpa re-encoding.
- Browser diminta mengeluarkan checkpoint setiap ~30 detik. Browser, terutama pada format MP4, dapat menunda data sampai STOP. **Checkpoint bukan jaminan pemulihan jika browser ditutup paksa.**
- Setelah STOP normal, aplikasi mencoba **unduh otomatis satu video utuh**. Jika browser tidak memulai unduhan, gunakan **Unduh Ulang Video** yang tampil langsung, atau masuk ke Arsip Video dan gunakan **Siapkan Video Utuh → Unduh Video** sebagai cadangan. Format keluaran MP4 atau WebM mengikuti dukungan browser; jangan sekadar mengubah ekstensi.
- Arsip lama 5.2.C masih diekspor sebagai ZIP berisi segmen video mandiri. Menyatukan byte dari MP4 independen bukan metode penggabungan yang valid.
- Rekaman yang dihentikan abnormal belum tentu lengkap. Periksa durasi dan pemutaran dari awal hingga akhir sebelum menghapus arsip lokal.

## Cara memasang versi 5.2.F

1. Ekstrak ZIP dan **timpa seluruh berkas** di direktori GitHub Pages yang sama. Pastikan `index.html`, `manifest.webmanifest`, dan `service-worker.js` ikut terganti dan deploy telah berhasil.
2. Buka kembali situs di Chrome. Periksa tampilan portrait: **CMM GEOCAM 5.2.F**. Pembaruan service worker dapat memerlukan buka ulang tab. Jangan hapus data situs untuk memaksa pembaruan.
3. Pilih **Arsip Video** untuk memastikan sesi lama masih muncul dan dapat diekspor.
4. Untuk uji baru, masuk landscape, aktifkan kamera, tunggu status READY, rekam 9–10 menit, lalu tekan STOP **satu kali**. Tunggu finalisasi. Periksa apakah unduhan otomatis muncul; jika tidak, tekan **Unduh Ulang Video** pada panel yang muncul. Jika perlu, gunakan ekspor dari Arsip.
5. Cek durasi, seluruh isi video, logo/watermark, dan format file hasil ekspor. Ulangi uji 20–30 menit bila uji 10 menit berhasil.

## Perilaku yang tidak berubah

| Komponen | Kondisi 5.2.F |
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

Kode 5.2.F lolos pemeriksaan sintaks, paket ZIP lolos uji integritas, dan simulasi STOP memastikan status sesi berubah menjadi completed, blok terbaca dari arsip, serta tautan Unduh Ulang terbentuk. Lingkungan browser pengujian tidak mengizinkan pengukuran keberhasilan unduhan sesungguhnya ke perangkat. **Kestabilan REC panjang dan pengunduhan otomatis masih perlu diuji langsung pada Android.** Auto-download tidak menyelesaikan kegagalan STOP/finalisasi pada MediaRecorder. Jika error berulang, jangan bersihkan data browser; dokumentasikan pesan error, durasi REC, dan jumlah segmen.

## Isi paket

`index.html`, `manifest.webmanifest`, `service-worker.js`, `STA_Tamer.csv`, `Corridor_Tamer.csv`, `logo-mms.png`, `icon-192.png`, `icon-512.png`, `README.md`.


## 5.2.F — Otomatis unduh setelah STOP

- Tekan STOP satu kali. MediaRecorder difinalisasi dan seluruh blok video ditulis dulu ke IndexedDB.
- Setelah status sesi `completed`, sistem menyusun satu berkas MP4/WebM dan mencoba mengunduh otomatis ke browser.
- Karena unduhan setelah event asinkron tidak selalu diizinkan browser, panel **Unduh Ulang Video** tetap tersedia untuk satu ketukan manual.
- Jangan menganggap unduhan otomatis berhasil hanya karena aplikasi meminta Chrome mengunduh. Periksa Downloads, durasi, dan pemutaran.
- Jika IndexedDB gagal menyimpan, aplikasi TIDAK mengklaim unduhan sukses, dan menawarkan Arsip untuk pemulihan.
- Arsip IndexedDB tetap disimpan sampai Inspector menghapusnya sendiri. Database dan format arsip 5.2.C/5.2.E tidak berubah.
- Catatan: auto-download tidak menyelesaikan kegagalan MediaRecorder.onstop atau kehilangan data yang belum sempat ditulis ke penyimpanan. Untuk inspeksi penting, tetap lakukan pengujian lapangan dan dokumentasi cadangan.
