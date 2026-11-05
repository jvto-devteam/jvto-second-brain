---
type: process
status: active
department: Layanan Pelanggan
tags: [booking, jvto-direct, xendit, sop]
updated: 2026-11-04
---
Booking direct lahir di [[jvto-web]], dibayar dan diproses di [[legacy]], lalu dioperasikan di [[new-backoffice]]. jvto-web tidak menyimpan booking di databasenya sendiri.

## Aturan pembayaran (kode jvto-web, commit 2ceef8b0, 2026-10-31)

| Jarak ke tanggal trip | Dibayar saat booking | Cara bayar |
|---|---|---|
| 14 hari atau lebih | DP 20% | Xendit (kartu) |
| 6 sampai 13 hari | Penuh 100% | Xendit (kartu) |
| Kurang dari 6 hari | Penuh 100% | Transfer bank manual |

Aturan ada di `src/lib/checkout/pricing.ts` (`downPaymentForDays`, `paymentMethodForDays`) dan dicek ulang di server (`verifyCheckoutPricing`). Catatan: teks kebijakan di [[jvto-ekosistem]] menulis "sampai 100% bila Day 1 kurang dari 14 hari" dan pelunasan "5 hari sebelum (kartu), 3 hari (transfer/Wise)". Yang berjalan di sistem: pengingat pelunasan H-7.

## Langkah

1. Tamu mengisi checkout (`TourDetail.tsx`, lalu `/checkout?pid=`). DP dan cara bayar dihitung otomatis.
2. `/api/checkout` di jvto-web cek harga terhadap data ekosistem, lalu meneruskan ke legacy `POST /api/web/checkout` dengan header token checkout.
3. Legacy membuat user, booking (kode `JVR/nnn/mm/yy`, status `pending wise`, batas bayar 1 hari), short link `jvto.me/b/`. Invoice Xendit hanya untuk kartu (berlaku 24 jam, nominal (dp + 2220) / 0,96781). WA `booking_pending_payment` dan email `NewReservationMail` dikirim.
4. Xendit mengabari pembayaran DP (`XenditController::invoiceSuccess`). Legacy membuat ulang aktivitas crew, mobil, kamar, makan, destinasi, hotel dan itinerary dari template paket. Booking jadi `booked`, `generateExpense` jalan.
5. Pesan terkirim: email `ConfirmedMail`, WA `hotel_room_reservation` ke grup tiap hotel, `internal_new_booking`, `payment_received_first` ke tamu dan grup internal, `internal_bali_transport` bila dari Bali atau ada add-on transport, lalu `EcosystemSync::notify`.
6. Booking muncul di booking-overview. Transfer bank masuk ke Pending Booking. Lihat [[Pelunasan dan Verifikasi Pembayaran]].
7. Admin mengedit, mencatat pembayaran manual, membuka invoice PDF, mengunggah dokumen, lalu [[Plotting Crew]].

## Status kunci

| Field | Nilai | Arti |
|---|---|---|
| `bookings.status` | `pending wise` | Belum bayar |
| `bookings.status` | `booked` | Pembayaran pertama masuk, tetap `booked` setelah lunas |
| `payment`, `balance` | angka | Lunas = `balance = 0` |
| `outstanding_payment_*` | link, amount, fees, method | Invoice pelunasan terakhir |
| `wa_status_reminder_payment` | 0 atau terisi | Pengingat H-7 sudah/belum |
| `booking_payments.is_paid` | 0 atau 1 | Backoffice hanya menampilkan 1 |

Sumber: [[Ekosistem JVTO]].
