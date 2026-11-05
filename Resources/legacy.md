---
type: resource
status: active
department: Teknologi
tags: [sistem, laravel, booking, repo]
updated: 2026-11-04
---
Backend Laravel lama di legacy.javavolcano-touroperator.com. Repo `jvto-devteam/javavolcano-touroperator`, folder lokal `/Users/macbook/Code/javavolcano-touroperator`. Mesin transaksi JVTO: membuat booking dan invoice dari checkout [[jvto-web]], menerima webhook Xendit, mengirim email dan semua pengingat WA (lewat [[wa-inbox]]), menjalankan ekstraktor email Klook. Berbagi satu database MySQL dengan [[new-backoffice]], [[crew-portal]], [[jvto-partner]] dan [[Aplikasi TWT]].

## Isi utama
- **API website** (`api/web/*`): katalog, checkout, bank transfer, cek ISIC, login Google/OTP, review, blog, data booking tamu.
- **Halaman tamu:** `/my-booking/{url}`, short link, pickup sign PNG bernama tamu.
- **Webhook:** Xendit invoice paid, Typeform (consent), Klook email extractor, Watzap.
- **Klook OCTO supplier API** (`api/octo/*`): JVTO terdaftar sebagai supplier yang bisa dipanggil sistem Klook langsung (produk, ketersediaan, booking, batal).
- **API crew "Super Apps"** (`api/v1/*`): login crew, pengumuman, jadwal, surat tugas, ID card, push notification Firebase. Registrasi crew baru di-approve admin.
- **Backoffice lama:** sebagian besar sudah digantikan new-backoffice, tapi invoice PDF dan foto hotel/destinasi masih diambil dari legacy.

## Scheduler (semua tiap menit, gerbang jam masing-masing)
Lihat [[Pengingat WhatsApp Otomatis]]. Tambahan: `google:sync-reviews` (Senin 10:00) dan `ads:upload-conversions` (tiap 10 menit, upload konversi offline Google Ads untuk booking dari klik iklan; default `validate_only`). Produksi wajib menjalankan `schedule:run` tiap menit.

## Aturan bisnis di kode
- Kode booking `JVR/NNN/MM/YY`, urut per **bulan trip** (bukan bulan booking).
- Deposit 20% atau penuh; booking kurang dari 14 hari = 100%. Teks pelunasan: kartu paling lambat H-5, transfer/Wise H-3, cash hanya bila disetujui saat booking; telat bayar bisa batal tanpa refund.
- Fee kartu Xendit di-gross-up: `(nominal + 220 + 2000) / 0,96781`, selisihnya ditagih sebagai "TAX/fees".
- **Diskon grup/FOC:** 18-33 pax 1 FOC per 18 peserta, 34-49 pax 2 FOC, 50+ pax 3 FOC + 5% (berlaku pelajar dan staf).
- **ISIC:** kode per traveler, valid bila diverifikasi admin dan belum dipakai.
- Saat DP masuk: itinerary dari template paket, tanggal `at_bondowoso` (Ijen) dan `at_bromo`, kamar sesuai pax, makan siang dan malam otomatis di hotel Bondowoso (Baratha, Grand Padis, Riverside), reservasi hotel, konfirmasi, transport Bali, expense, EcosystemSync.
- **Voucher repeat order:** di akhir trip dibuat diskon Rp450.000 (kode `NAMATAMU450`), berlaku sampai 31 Desember tahun berjalan.
- **Health Screening Ijen** adalah lini pembayaran terpisah: invoice Xendit "Health Screening Payment" diteruskan ke `health.mountijen.com`.
- **Google Ads:** klik iklan disimpan di booking, konversi dicatat saat pembayaran pertama (nilai = grand total + add-on).

## Riwayat
2026-08-19 EcosystemSync; 2026-09-21 semua WA pindah ke wa-inbox; 2026-09-22/23 short link `jvto.me/b/` dan pickup sign; 2026-10-16 konversi Google Ads. Rilis keamanan 2026-10-18 (`release/security-p1`) ada di origin: token checkout, verifikasi webhook Xendit (mode log sampai `XENDIT_WEBHOOK_ENFORCE=true`), kredensial `cron.php` dipindah ke `.env`.

## Risiko keamanan (butuh owner)
- Nilai kredensial lama masih ada di git history: perlu rotasi.
- Di salinan lokal: `cron.php` berisi kredensial gateway WA, file dump `jvto (3).sql` (2,6 MB, data produksi) dan `firebase_credentials.json` di root repo.
- Nomor HP internal di-hardcode di kode.
- Salinan lokal tertinggal 4 commit dari origin dan working tree-nya kotor (cek 2026-11-04).
