---
type: process
status: active
department: Layanan Pelanggan
tags: [booking, klook, ota, sop]
updated: 2026-11-04
---
Booking Klook masuk lewat email konfirmasi Klook ke Gmail JVTO, lalu diubah otomatis menjadi booking di backoffice. Tamu tidak tahu siapa JVTO saat memesan, tapi sesudah booking berkomunikasi langsung dengan JVTO.

## Langkah

1. Klook mengirim email konfirmasi.
2. Filter Gmail memberi label **KLOOK EKSTRAKTOR** pada email "confirmed".
3. Google Apps Script membaca email berlabel dan mengirim isinya ke [[legacy]] `POST /third-party/klook-extractor/webhook`.
4. DeepSeek (`deepseek-chat`) mengubah teks email jadi data: package_id, kode Klook, tanggal, tamu utama, negara, kontak, pax, jemput dan antar, info kedatangan.
5. Legacy membuat user, booking (`agent_id = 2`, `booking_category_id = 3`, `status = booked`, `payment_method = pay later`, `dp = 0`, `payment = 0`, `balance` = total), itinerary, hotel, kamar, dan expense (`generateExpense`).
6. Terkirim: WA `booking_confirmed_klook_email` ke tamu dengan tautan portal, WA `hotel_room_reservation` ke grup hotel, `EcosystemSync`.
7. Jadwal WA otomatis diisi: trip information H-2 20:00, trip media hari terakhir 15:00, media crew H-1 15:00, pengingat crew H-1 10:00.

## Pemetaan paket

| package_id | Paket Klook |
|---|---|
| 82 | 3D2N Bromo & Ijen |
| 83 | 4D3N dengan Surabaya Night Market |
| 84 | 4D3N Ijen, Papuma, Tumpak Sewu, Bromo |
| 94 | Bromo Sunrise Private Tour with Photographer from Surabaya (baru) |

## Perbedaan dengan booking direct

- JVTO tidak menagih tamu Klook: tidak ada invoice Xendit, pengingat H-7 mengecualikan kategori Klook, portal tidak menampilkan pembayaran dan dokumen.
- Uang dari Klook sebulan sekali sesudah trip.
- Aturan pembatalan dan perubahan mengikuti Klook. Detail paket dan harga Klook di [[services]].

Sumber: [[Ekosistem JVTO]].
