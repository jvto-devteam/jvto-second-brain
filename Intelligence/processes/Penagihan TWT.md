---
type: process
status: active
department: Keuangan
tags: [twt, invoice, partner, sop]
updated: 2026-11-04
---
TWT membayar di muka: di akhir bulan dihitung jumlah trip bulan berikutnya dan itu yang dibayar. Trip tengah bulan atau dadakan dibayar lebih dulu atau sesudahnya, tergantung kondisi. Semua lewat [[Aplikasi TWT]].

| Langkah | Pelaku | Di sistem |
|---|---|---|
| 1. Minta biaya tambahan | TWT | Supplemental Expense Requests: item, qty, tanggal, peminta. Status `pending` |
| 2. Lengkapi biaya tambahan | JVTO | Harga, qty, foto, bill, tanggal submit. Status `submitted` |
| 3. Refund dan penalti | JVTO | Financial Adjustments, status `pending` |
| 4. Revisi harga | JVTO | Invoice Revision dengan bukti. `pending`, lalu `approved` atau `rejected` |
| 5. Submit invoice | JVTO | Pilih booking TWT belum ditagih per bulan trip + biaya tambahan + refund/penalti. Nomor `JVR-INV-TWT-<tahun>-<bulan>-<urutan>`. Status `unpaid` |
| 6. Invoice terbit | Sistem | Booking ditandai `is_invoiced_twt = 1`, `grand_total` diisi nilai tagihan |
| 7. Pembayaran | TWT | Catat tanggal, jumlah, bukti (maks 5 MB) |
| 8. Status | Sistem | `paid`, `paid_partially`, atau kembali dihitung bila pembayaran dihapus |

## Hotel Check

Sebelum booking, TWT mengisi tamu, tanggal, pax, durasi, hotel dan kamar per hari. Sistem membuat booking awal tanpa status, mengirim WA "Room Reservation" ke grup hotel dan "Waiting Hotel Confirmation" ke grup internal. Hotel menjawab Room Available atau Room Full di portal hotel ([[jvto-partner]]). Status: Waiting, Available, Room Full.

Sumber: [[Ekosistem JVTO]].
