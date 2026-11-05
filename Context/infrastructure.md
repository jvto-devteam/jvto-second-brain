---
type: context
tags: [infrastructure, stack]
updated: 2026-11-04
---

Peta lengkap 8 sistem dan alurnya: [[Peta Sistem JVTO]]. Catatan per sistem: [[jvto-web]], [[legacy]], [[new-backoffice]], [[crew-portal]], [[jvto-partner]], [[Aplikasi TWT]], [[jvto-ekosistem]], [[wa-inbox]].

## Komunikasi

- WhatsApp: tamu, grup ops, grup crew, grup tiap hotel, vendor. Semua WA otomatis dikirim lewat [[wa-inbox]] (28 system template); pengecualian: OTP crew-portal dan status hotel di jvto-partner (WatZap), Aplikasi TWT (wa-dashboard).
- WhatsApp Official = Meta Cloud API (WABA); Unofficial = gateway wa-dashboard JVTO.
- Chatbot wa-inbox 24/7, ringkasan chat harian, Kantor AI. Lihat [[Pengingat WhatsApp Otomatis]].
- Email: hello@javavolcano-touroperator.com.

## Booking & CRM

- **Sumber kebenaran booking:** new-backoffice (MySQL hosting) plus aplikasi Laravel lama (javavolcano-touroperator).
- OTA: Klook (konektor OCTO), TWT, GetYourGuide, Viator.
- Sync booking ke [[jvto-ekosistem]] dimatikan sejak 2026-10-14.
- Akses baca-saja lewat MCP internal JVTO (booking, operasional, expense, hutang vendor, rekap crew).

## Kalender & Manajemen Tugas

Belum dipakai secara resmi. Owner menilai ini sangat perlu ke depannya. Yang sudah ada di sistem: `/calendar` (prototipe) dan `/task-management` tersembunyi di new-backoffice, Penugasan Crew di versi origin, Summary Harian dan Reminder di wa-inbox. Sementara ini tugas dicatat di `task-list/Tasks.md` masing-masing profil di vault ini.

## Konten & Website

- [[jvto-ekosistem]]: satu-satunya penulis fakta publik ("ekosistem satu-satunya penulis, web juru tampil"). Disajikan di ekosistem.javavolcano-touroperator.com.
- [[jvto-web]]: Next.js + Prisma di VPS (Nginx, PM2), Cloudflare di depan.
- Konten diedit langsung di file JSON sumber. TinaCMS pensiun.
- SEO: Google Search Console, Google Business Profile API, llms.txt, JSON-LD.

## Keuangan

- Pembayaran tamu: Xendit (kartu), transfer BCA / Wise, tunai hanya dengan persetujuan. Rekening perusahaan BCA saja.
- Klook bayar bulanan sesudah trip; TWT bayar di muka lewat [[Aplikasi TWT]].
- Expense, hutang vendor, payout crew: [[new-backoffice]]. Profit = invoice dikurangi expense internal. Lihat [[Expense Booking]].
- Transfer ke crew dicocokkan otomatis dari email KlikBCA: keterangan transfer harus diawali kode booking.

## Dev

- GitHub (org jvto-devteam), GitHub Actions untuk deploy ke VPS.
- Push ke `main` = deploy produksi, wajib approval owner.
- Target migrasi: PostgreSQL di VPS. Lihat [[Migrasi PostgreSQL]].

## Alur Kerja yang Menyakitkan

| Saat | Yang dilakukan | Hasil | Yang diinginkan |
|---|---|---|---|
| Booking baru dibayar | Pesan WA ke grup ops dan grup hotel, jadwal pengingat | Pesan WA tersebar | Communication log per booking (belum ada) |
| Menjelang trip | Cek kesiapan manual | Dari 74 booking: 49 masih ada sisa bayar, 19 belum ada drop-off, 5 belum ada guide atau hotel | Status kesiapan eksplisit (missing / partner_managed / pending) |
| Hitung profit | Ambil dari backoffice | Profit 2,92%, direct -9,47% | Rincian komisi dan fee per kanal |

Lihat [[Alur Booking Baru]].
