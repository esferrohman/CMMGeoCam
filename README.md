# CMM GeoCam · Prototype 5.2.D

## Pembaruan: rekam satu video utuh

Versi 5.2.D merekam video menggunakan **satu instance MediaRecorder untuk satu sesi REC**. Berbeda dengan versi 5.2.C, aplikasi tidak menghentikan lalu memulai recorder baru setiap 2 menit. Data dari *satu* MediaRecorder disimpan ke IndexedDB sesuai urutan event `dataavailable`. Setelah STOP, hasilnya disusun sebagai satu Blob video lengkap tanpa encoding ulang atau sambungan MP4 independen.

Fitur arsip **Siapkan Video Utuh → Unduh Video** menghasilkan satu file MP4 jika kamera/browser mendukung MediaRecorder MP4 (H.264/MP4), atau WebM jika tidak didukung. Jangan mengganti ekstensi WebM menjadi MP4. Mode ZIP dipertahankan untuk cadangan data dan arsip lama 5.2.C.

### Poin penting tentang checkpoint

- Aplikasi memanggil `requestData()` sekitar setiap **30 detik**. Event data yang dihasilkan ditulis ke IndexedDB untuk menjaga urutan stream.
- **Browser boleh menunda data hingga STOP, khususnya untuk MP4.** Karena itu, checkpoint tidak selalu merupakan berkas video siap putar dan **belum menjamin pemulihan video yang terhenti akibat browser ditutup paksa**.
- Hasil MP4 utuh berlaku untuk sesi yang selesai normal, lengkap, dan berurutan. Arsip sesi abnormal tidak otomatis dianggap valid.
- ZIP cadangan 5.2.D berisi *blok data*, bukan kumpulan video mandiri. Arsip 5.2.C lama tetap diekspor sebagai ZIP video-video terpisah, karena menggabungkan MP4 independen dengan menyambung byte tidak valid.
- Setelah ekspor, cek durasi dan pemutaran awal–akhir. **Jangan hapus data situs atau uninstall PWA sebelum file diverifikasi.**

## Cara menggunakan

1. Timpa berkas pada repo HTTPS yang sama dengan paket 5.2.D. Jangan pindah domain/origin bila ingin mempertahankan arsip IndexedDB lama.
2. Tutup aplikasi, buka ulang, lalu pastikan label **CMM GEOCAM 5.2.D** muncul di mode portrait.
3. Putar perangkat ke landscape, tekan Mulai Kamera. Saat `LAYAR: AKTIF`, permintaan Screen Wake Lock telah berhasil; jika gagal OS masih bisa mematikan layar.
4. Tunggu `READY` untuk memulai REC, rekam 9–10 menit, tekan STOP, lalu tunggu notifikasi finalisasi arsip.
5. Tekan **ARSIP VIDEO → Siapkan Video Utuh → Unduh Video Utuh**.
6. Buka berkas hasil di Download/Galeri, pastikan durasi utuh dan videonya dapat diputar.
7. Jika file utuh tidak siap, coba ZIP cadangan tanpa menghapus data browser, kemudian laporkan pesan error yang tampil.

## Status dan ketentuan sistem

| Komponen | Perilaku |
|---|---|
| GPS | `MAX_GPS_ACCURACY = 25` m sebagai syarat awal foto/REC, bukan bukti deviasi STA ≤25 m terhadap Ledger |
| GPS saat REC | Jika akurasi menurun saat REC berjalan, perekaman tidak dihentikan otomatis |
| Pengenalan jaringan | Mainline + corridor database seperti 5.2.C, tanpa perubahan data CSV |
| Jalur A/B | **Masih algoritma lama** (arah/tren) sampai database alignment spasial per jalur tersedia |
| Orientasi | Portrait untuk Auto-STA; landscape untuk foto/video 16:9 |
| Zoom | Tombol 1×/2×/4× dan pinch-to-zoom kontinu tetap |
| Wake Lock | Meminta layar terus menyala saat kamera landscape aktif, bergantung OS/browser |
| Video | 1280×720, target 30 fps, bitrate ~4 Mbps; hasil MP4/WebM sesuai dukungan browser |
| Arsip | IndexedDB (origin sama), tombol ekspor utuh 5.2.D, ZIP legacy 5.2.C |

## Uji verifikasi

1. Sesi 9–10 menit: rekaman utuh, durasi hasil, jumlah file, suara (aplikasi saat ini video tanpa audio), watermark awal/akhir.
2. Rekaman 20–30 menit: performa, ukuran file, daya baterai, kapasitas IndexedDB, memori ketika ekspor.
3. GPS melemah >25 m *ketika REC sudah berlangsung*: REC tidak berhenti otomatis; label GPS LOW boleh berubah.
4. Peralihan portrait, background, atau layar terkunci: finalisasi hanya jika browser masih sempat memproses event STOP.
5. Pastikan arsip 5.2.C lama tidak hilang setelah upgrade.

### Batas pengujian saat paket dibuat

Pengujian kode di browser Chromium simulasi berhasil membuat beberapa potongan dari satu MediaRecorder; gabungannya lolos pemeriksaan format/durasi menggunakan `ffprobe` dan dapat diidentifikasi sebagai MP4. **Pengujian hardware langsung untuk rekaman 9 menit maupun 30 menit masih harus dilakukan di Android.**

## Daftar berkas

- `index.html` — aplikasi utama
- `STA_Tamer.csv` dan `Corridor_Tamer.csv` — database Road Network (tidak diubah)
- `logo-mms.png`, `icon-192.png`, `icon-512.png` — logo/ikon (tidak diubah)
- `manifest.webmanifest` — metadata PWA 5.2.D
- `service-worker.js` — shell cache baru untuk 5.2.D
- `README.md` — panduan ini
