CMM GeoCam - Prototype 7
STRICT LANDSCAPE
===========================

Perubahan dari Prototype 6 Corrected
------------------------------------
1. FOTO hanya dapat dibuat saat perangkat benar-benar LANDSCAPE.
2. VIDEO hanya dapat dimulai saat perangkat benar-benar LANDSCAPE.
3. Jika portrait:
   muncul pesan:
   "Putar perangkat ke mode landscape untuk melakukan dokumentasi."
4. Rotate prompt diperjelas:
   foto dan video menggunakan format dokumentasi 16:9.
5. Jika HP diputar dari landscape ke portrait SAAT VIDEO DIREKAM:
   - recording otomatis dihentikan
   - file sampai frame terakhir tetap difinalisasi/disimpan
   - user harus kembali landscape untuk recording berikutnya.
6. Semua fitur Prototype 6 Corrected tetap:
   - 1280x720
   - manual frame sync jika didukung
   - requestVideoFrameCallback
   - overlay cache
   - logo MMS burn-in
   - MP4-first, fallback WEBM
   - local-only
   - Auto-STA / Jalur / Segmen / Offset / GPS status

FILE YANG PERLU DI-UPDATE DI GITHUB
-----------------------------------
Cukup:
- index.html
- service-worker.js

Cara update:
1. Repo CMMGeoCam > Add file > Upload files.
2. Ganti index.html dan service-worker.js.
3. Commit changes.
4. Tunggu GitHub Pages deploy.
5. Tutup total tab/PWA lama.
6. Buka ulang dari Home Screen.
7. Pastikan Auto Rotate Android aktif.

PERILAKU YANG DIHARAPKAN
------------------------
Portrait:
- layar rotate prompt muncul
- capture diblokir

Landscape:
- UI kamera aktif
- FOTO / VIDEO dapat digunakan jika GPS READY

Saat recording lalu HP diputar portrait:
- recording berhenti otomatis
- hasil video tetap disimpan.
