# CMM GeoCam 5.3.I — WYSIWYG (What You See Is What You Get)

## Perubahan utama

- **Preview sekarang berasal langsung dari canvas yang sama dengan sumber MediaRecorder** (`videoCanvas`, 1280×720), sehingga sudut pandang, crop digital, pergeseran Gyro EIS, logo, STA/Jalur, dan metadata watermark terlihat langsung sebagaimana dirender ke video.
- Output foto tetap 1920×1080. Perhitungan offset gyro pada `drawCameraFrame` diseragamkan terhadap referensi 1280×720, sehingga foto dan preview memilih area sumber kamera **yang sama** walaupun resolusinya berbeda.
- Rasio preview dikunci **16:9** dengan `object-fit:contain`, tanpa `object-fit:cover` atau CSS `scale()` tambahan. Sisi kanan dicadangkan bagi tombol foto/video, zoom, dan pinch. Pada layar yang lebih lebar/sempit bisa muncul bingkai hitam, **bukan cropping foto**.
- Tampilan teks watermark lama yang mengambang disembunyikan hanya ketika kamera landscape aktif, karena watermark yang benar sudah terlukis di canvas yang sama dengan hasil simpan. Kontrol status, diagnosa, dan pilihan CUACA tetap aktif sebagai UI, tidak ikut terekam.
- Loop komposisi frame berjalan sejak kamera dibuka, bukan hanya saat REC; berhenti saat layar tidak aktif/portrait dan dilanjutkan ketika kembali landscape. Ada timer fallback ketika callback frame browser terhenti.
- GPS, database jalan, stabilisasi, sistem arsip dan sistem unduhan Android/Safari **tidak diubah**. Tidak ada perubahan skema IndexedDB.

## Uji lapangan wajib

1. Di Android Chrome dan Safari iPhone, buka kamera landscape, foto objek pada **1×/2×/4×**, cocokkan batas kiri, kanan, atas, bawah preview dengan hasil foto.
2. Ulangi saat **STAB: GYRO** aktif, gerakkan kamera pelan: komposisi harus mengikuti preview tanpa loncatan crop.
3. Rekam video pendek 30–60 detik dan periksa frame pertama/terakhir, watermark, durasi, dan penyimpanan. Lanjutkan 5–10 menit jika berhasil.
4. Pastikan pinch-to-zoom, tombol cuaca, dan shutter masih berfungsi.

**Batasan:** Test sintaks dan logika dapat dilakukan di desktop; kinerja frame dan perilaku Safari/Chrome pada HP yang sebenarnya tetap membutuhkan uji perangkat. Pembaruan ini bukan perbaikan untuk kegagalan STOP/MediaRecorder. Arsip lama tetap berada di profil browser/domain yang sama; jangan hapus data situs sebelum ekspor.

---

# CMM GeoCam — Prototype 5.3.I (Safari Save)

## Perubahan pada 5.3.I

- **Safari iPhone/iPad:** setelah STOP berhasil, video lebih dulu difinalisasi dan ditulis ke **Arsip GeoCam (IndexedDB)**. Aplikasi menampilkan **Simpan Video ke iPhone** yang memanggil Share Sheet iOS melalui klik nyata; pilih **Simpan Video** atau **Simpan ke File**, jika pilihan tersedia. Safari tidak memberikan akses untuk menyimpan diam-diam langsung ke galeri.
- **Foto Safari:** tombol **Simpan Foto ke iPhone** menawarkan Share Sheet (atau unduh manual sebagai fallback). **Foto belum diarsipkan secara permanen** dalam aplikasi: jangan refresh sebelum disimpan.
- **Arsip lama:** sesi continuous yang statusnya completed dapat langsung memakai **Simpan ke iPhone** dari panel Arsip bila Web Share Files didukung, tanpa perlu tombol *Siapkan Video Utuh*.
- **Android/Chrome:** mekanisme otomatis meminta unduh setelah STOP tetap dipertahankan, dengan link fallback. Sistem tidak bisa mengonfirmasi file masuk ke Downloads.
- Database `CMMGeoCamRecordings`, skema v1, `sessions` / `segments` **tidak berubah**. Semua data lama tetap kompatibel selama URL/domain dan profil browser sama.
- **Batasan penting:** penyimpanan IndexedDB dapat dibersihkan iOS ketika storage penuh/tereviksi; ini bukan penyimpanan permanen Photos/Files. Versi ini juga belum menjamin pemulihan kalau MediaRecorder gagal STOP atau browser tertutup sebelum finalisasi. Uji langsung di Safari iOS masih diperlukan.
- **Penting untuk pengujian:** setelah STOP, pastikan status selesai, gunakan Simpan ke iPhone, lalu cek video benar-benar ada di Foto/Files beserta durasinya sebelum hapus arsip.

---

# CMM GeoCam — Prototype 5.3.I

Basis 5.3.G berasal dari `index(6).html` dengan kompensasi GYRO EIS saat zoom. Rilis 5.3.I menambahkan **jalur ekspor iOS/iPadOS via Share Sheet untuk foto dan video**. Auto-download Chrome dan penyimpanan IndexedDB untuk video tetap dipertahankan. Tidak ada perubahan skema database atau alignment GPS.

## Baru di 5.3.I: EIS Zoom Compensation

- Perubahan sumber: `const eisZoomComp=Math.max(1,zoomRequested);` dan `shiftX/shiftY` sekarang mengalikan koreksi dengan `eisZoomComp`.
- Pada 1×, rumus tetap setara dengan sebelumnya; pada 2×, 4×, dan pinch zoom, koreksi gyro disesuaikan dengan zoom yang dipilih.
- Hanya aktif jika **GYRO EIS** digunakan; tidak menyatakan Native EIS telah diperbaiki.
- Koreksi tetap dibatasi `maxShiftX/maxShiftY` agar pergeseran tidak melebihi ruang crop. Zoom besar tidak menjamin stabilisasi lebih baik karena ruang crop makin sempit.
- Kinerja stabilisasi saat zoom **belum teruji langsung pada smartphone**; perlu perbandingan video 1×/2×/4× di kondisi inspeksi yang sama.


## Perbaikan arsip yang dipertahankan

Menangani error Chrome Android: `Failed to execute 'transaction' on 'IDBDatabase': The database connection is closing.` Jika koneksi IndexedDB sudah ditutup oleh browser, aplikasi menginvalidasi koneksi lama dan mencoba membuka koneksi baru. Saat pembuatan transaksi gagal karena koneksi sedang ditutup, sistem mencoba sekali lagi dengan koneksi yang baru. Panel Arsip dilengkapi opsi **Coba Buka Ulang Arsip** saat pembacaan gagal.

**Kompatibilitas data:** Nama database `CMMGeoCamRecordings`, versi skema `1`, dan object store `sessions` / `segments` tetap sama. Rekaman tersimpan pada origin/domain dan profil browser yang sama; perbarui situs pada URL yang sama agar arsip 5.2.C/5.2.D tetap dapat diakses. Jangan memilih Hapus data situs, membersihkan penyimpanan Chrome, atau uninstall PWA sebelum semua arsip penting diekspor dan diverifikasi.

## Cara kerja video

- Satu sesi REC menggunakan satu `MediaRecorder` berkelanjutan, sehingga setelah STOP normal, potongan data hasil satu recorder dapat disusun menjadi **satu file video** tanpa re-encoding.
- Browser diminta mengeluarkan checkpoint setiap ~30 detik. Browser, terutama pada format MP4, dapat menunda data sampai STOP. **Checkpoint bukan jaminan pemulihan jika browser ditutup paksa.**
- Setelah STOP normal, aplikasi mencoba **unduh otomatis satu video utuh**. Jika browser tidak memulai unduhan, gunakan **Unduh Ulang Video** yang tampil langsung, atau masuk ke Arsip Video dan gunakan **Siapkan Video Utuh → Unduh Video** sebagai cadangan. Format keluaran MP4 atau WebM mengikuti dukungan browser; jangan sekadar mengubah ekstensi.
- Arsip lama 5.2.C masih diekspor sebagai ZIP berisi segmen video mandiri. Menyatukan byte dari MP4 independen bukan metode penggabungan yang valid.
- Rekaman yang dihentikan abnormal belum tentu lengkap. Periksa durasi dan pemutaran dari awal hingga akhir sebelum menghapus arsip lokal.

## Cara memasang versi 5.3.I

1. Ekstrak ZIP dan **timpa seluruh berkas** di direktori GitHub Pages yang sama. Pastikan `index.html`, `manifest.webmanifest`, dan `service-worker.js` ikut terganti dan deploy telah berhasil.
2. Buka kembali situs di Chrome. Periksa tampilan portrait: **CMM GEOCAM 5.3.I**. Pembaruan service worker dapat memerlukan buka ulang tab. Jangan hapus data situs untuk memaksa pembaruan.
3. Pilih **Arsip Video** untuk memastikan sesi lama masih muncul dan dapat diekspor.
4. Untuk uji baru, masuk landscape, aktifkan kamera, tunggu status READY, rekam 9–10 menit, lalu tekan STOP **satu kali**. Tunggu finalisasi. Periksa apakah unduhan otomatis muncul; jika tidak, tekan **Unduh Ulang Video** pada panel yang muncul. Jika perlu, gunakan ekspor dari Arsip.
5. Cek durasi, seluruh isi video, logo/watermark, dan format file hasil ekspor. Ulangi uji 20–30 menit bila uji 10 menit berhasil.

## Perilaku yang tidak berubah

| Komponen | Kondisi 5.3.I |
|---|---|
| GPS capture gate | `MAX_GPS_ACCURACY = 25` m saat memulai foto/video; bukan jaminan deviasi STA terhadap Ledger ≤25 m |
| GPS saat REC | Penurunan kualitas GPS tidak otomatis menghentikan REC berjalan |
| Jalur A/B mainline | Masih logika arah/tren lama; belum pemisahan alignment spasial per jalur |
| Mode | Portrait = Auto-STA Locator; landscape = GeoCam 16:9 |
| Kamera | Foto, video, watermark, stabilisasi GYRO EIS dengan kompensasi zoom, zoom 1×/2×/4× dan pinch-to-zoom |
| Screen Wake Lock | Diminta saat kamera landscape aktif; tergantung kemampuan Android/browser |
| Arsip | IndexedDB pada domain/profil Chrome yang sama, dengan ekspor video utuh dan ZIP cadangan |
| Database jalan | `STA_Tamer.csv` dan `Corridor_Tamer.csv` tidak berubah |

## Batasan validasi

Kode 5.3.I telah diperiksa sintaksnya dan integritas paket ZIP; perubahan EIS diverifikasi secara statis terhadap 5.2.F. Pengujian simulasi STOP yang disebut pada rilis 5.2.F adalah hasil versi sebelumnya, **bukan pengujian smartphone baru untuk 5.3.I**. **Kestabilan REC panjang, pengunduhan otomatis, dan EIS saat zoom masih perlu diuji langsung pada Android.** Auto-download tidak menyelesaikan kegagalan STOP/finalisasi pada MediaRecorder. Jika error berulang, jangan bersihkan data browser; dokumentasikan pesan error, durasi REC, dan jumlah segmen.

## Isi paket

`index.html`, `manifest.webmanifest`, `service-worker.js`, `STA_Tamer.csv`, `Corridor_Tamer.csv`, `logo-mms.png`, `icon-192.png`, `icon-512.png`, `README.md`.


## Fungsi 5.2.F yang dipertahankan — Otomatis unduh setelah STOP

- Tekan STOP satu kali. MediaRecorder difinalisasi dan seluruh blok video ditulis dulu ke IndexedDB.
- Setelah status sesi `completed`, sistem menyusun satu berkas MP4/WebM dan mencoba mengunduh otomatis ke browser.
- Karena unduhan setelah event asinkron tidak selalu diizinkan browser, panel **Unduh Ulang Video** tetap tersedia untuk satu ketukan manual.
- Jangan menganggap unduhan otomatis berhasil hanya karena aplikasi meminta Chrome mengunduh. Periksa Downloads, durasi, dan pemutaran.
- Jika IndexedDB gagal menyimpan, aplikasi TIDAK mengklaim unduhan sukses, dan menawarkan Arsip untuk pemulihan.
- Arsip IndexedDB tetap disimpan sampai Inspector menghapusnya sendiri. Database dan format arsip 5.2.C/5.2.E tidak berubah.
- Catatan: auto-download tidak menyelesaikan kegagalan MediaRecorder.onstop atau kehilangan data yang belum sempat ditulis ke penyimpanan. Untuk inspeksi penting, tetap lakukan pengujian lapangan dan dokumentasi cadangan.

## Catatan kompatibilitas khusus

Jangan hapus data situs/PWA ketika memperbarui dari 5.2.C–5.2.F. Tetap gunakan origin GitHub Pages dan profil Chrome yang sama untuk mengakses rekaman tersimpan. 5.3.I tidak memperbaiki sepenuhnya kegagalan STOP/finalisasi `MediaRecorder` yang dilaporkan pada uji lapangan; rekaman penting masih sebaiknya dicadangkan memakai kamera bawaan hingga ketahanan aplikasi teruji.
