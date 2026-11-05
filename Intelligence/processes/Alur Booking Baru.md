---
type: process
status: active
department: Layanan Pelanggan
tags: [booking, channel, sop]
updated: 2026-11-04
---
Semua booking dari semua channel berakhir di satu tempat: Booking Backoffice ([[new-backoffice]] dengan database MySQL yang sama dengan [[legacy]]). Jalan masuknya berbeda per channel.

| | JVTO | Klook | TWT |
|---|---|---|---|
| Pemicu | Tamu checkout di [[jvto-web]] | Email konfirmasi Klook | Email TWT dengan PDF booking |
| Jalur masuk | jvto-web lalu legacy (`CheckoutController::checkout`) | Gmail label "KLOOK EKSTRAKTOR", Apps Script, legacy, DeepSeek membaca email | Gmail label "TWT EKSTRAKTOR", Apps Script, new-backoffice (`twtExtractor`), DeepSeek membaca PDF |
| Status awal | `pending wise`, jadi `booked` saat pembayaran pertama | `booked` langsung | `booked` langsung |
| Penanda data | `agent_id = 2`, kategori bukan 3 | `agent_id = 2`, `booking_category_id = 3` | `agent_id = 1` |
| Pembayaran tamu | DP atau penuh lewat Xendit/transfer, pelunasan lewat portal | Tamu bayar ke Klook | Dipegang TWT |
| Uang masuk ke JVTO | Dari tamu | Klook bayar sebulan sekali sesudah trip | TWT bayar di muka untuk trip bulan berikutnya |
| Pesan ke tamu | WA dan email tiap tahap | Satu WA konfirmasi dengan tautan portal | Tidak ada (tamu TWT dihubungi TWT) |
| Expense otomatis | Ya | Ya | Tidak, rincian biaya ada di PDF |

Detail per channel: [[Alur Booking JVTO]], [[Alur Booking Klook]], [[Alur Booking TWT]]. Channel lain (GetYourGuide, Viator, trip.com, rujukan hotel) jarang dan diinput manual oleh admin di backoffice.

Sesudah tercatat, semua channel berjalan sama: tampil di booking-overview, [[Plotting Crew]], [[Expense Booking]], reservasi hotel, trip muncul di [[crew-portal]], lalu [[Pengingat WhatsApp Otomatis]]. Perubahan dan pembatalan semua channel dilakukan admin di backoffice.

Sumber: [[Ekosistem JVTO]].
