---
type: process
status: active
department: Layanan Pelanggan
tags: [payment, xendit, pelunasan, sop]
updated: 2026-11-04
---
Hanya berlaku untuk channel JVTO. Klook dibayar Klook, TWT lewat [[Penagihan TWT]].

## Pengingat pelunasan H-7

Command [[legacy]] `my:reminder-payment` (jalan tiap menit, bekerja pukul 15.00 WIB) mencari booking `booked`, `agent_id = 2`, bukan Klook, belum diingatkan, dan tepat 7 hari sebelum trip. Ia membuat invoice Xendit `#Outstanding Payment` (kartu saja, berlaku 48 jam, plus fee) dan mengirim WA `payment_reminder_balance` sekali. Bila `balance = 0`, booking hanya ditandai.

## Pelunasan lewat customer portal

1. Tamu menekan Select Payment Method di `/my-booking/<slug>`: kartu lewat Xendit (dikenai biaya), Wise/transfer bank, atau tunai saat tiba.
2. [[jvto-web]] `/api/booking/pay-balance` meneruskan ke legacy `POST /booking/set-payment-method/<slug>`.
3. Legacy mengirim WA `payment_method_confirmed_card` / `_wise` / `_cash` berisi sisa tagihan (kartu: dengan tautan Xendit).
4. Transfer: tamu membuka `/my-booking/<slug>/bank-transfer`, melihat rekening BCA, mengunggah bukti. Status portal: Payment Under Review.
5. Xendit sukses: `payment` bertambah, `balance` berkurang, `booking_payments` "Payment (Paid in Full)" (dipecah "Payment Add On" bila ada add-on), email receipt, WA `payment_received_balance`, `EcosystemSync`.

## Pending Booking: verifikasi transfer

Menu `/pending-booking` di [[new-backoffice]] menampilkan booking `pending wise`, metode `pay later`, dengan bukti transfer yang belum disetujui. Admin cek bukti lalu Approve. Server memanggil webhook sukses Xendit di legacy dengan token backoffice, jadi booking menjadi `booked` dan semua pesan konfirmasi ikut terkirim seperti pembayaran Xendit.

## Pembayaran manual (Add Payment)

Di halaman detail booking (`/bookings/details/<id>`), bagian Financial Data: nominal, metode, referensi, catatan, item (Package atau Add-on), Send Receipt (aktif bawaan), bukti opsional (JPG/PNG/PDF maks 5 MB). `PaymentService` mencatat dalam satu transaksi dan menulis audit log. Booking Klook ditolak. Lebih bayar hanya diterima bila dicentang dan diberi alasan. Pembayaran bisa dibatalkan (void) dengan alasan wajib dari halaman Incoming Payments; barisnya ditandai batal, tidak dihapus.

Ganti metode pelunasan: `POST /bookings/payment-method/<id>`. Memilih cash membuat tugas setoran cash crew (CashCollectionService).

## Status di customer portal

Confirmed & Paid, Confirmed (Balance Due), Awaiting Payment, Payment Under Review.

Sumber: [[Ekosistem JVTO]].
