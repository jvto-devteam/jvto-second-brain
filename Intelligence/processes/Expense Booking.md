---
type: process
status: active
department: Keuangan
tags: [expense, hutang-vendor, payout, sop]
updated: 2026-11-04
---
Expense = biaya operasional satu booking. Dibuat otomatis (`generateExpense`) atau manual, lalu diedit di Expense Manager [[new-backoffice]] `/finance/expense-manager/<id>/edit`.

## Kapan dibuat otomatis

| Sumber booking | Otomatis? |
|---|---|
| Klook (webhook email) | Ya |
| JVTO (webhook Xendit pembayaran pertama) | Ya |
| Admin simpan booking baru | Ya, kecuali TWT |
| Admin ubah booking | Ya, kecuali TWT atau bila file expense internal sudah ada |
| TWT (pembaca PDF) | Tidak |

## Kategori

| Kategori | Isi | Qty | Harga |
|---|---|---|---|
| Accommodation | Kamar, makan siang, makan malam per hotel | Kamar / pax | Tarif master hotel |
| Destination | Tiket, pemandu lokal, jeep, sarapan per destinasi | Rumus aktivitas | Master |
| Others | Uang transport, air mineral, kaos, tol-parkir | Rumus item | Master |
| Transport | Kendaraan | Jumlah hari | Menurut pax (konfigurasi mobil) |
| Resource | Peran crew | Hari | Tarif peran per channel |
| AddOn | Add-on tamu | Jumlah | `price_expense` |

Rumus memakai `pax`, `day`, `Math.ceil`. Item default dipilih per channel (`is_default_jvto`, `is_default_klook`, `is_default_twt`).

## Harga master (3 November 2026)

| Item | Rumus | Harga |
|---|---|---|
| Bromo Ticket | `pax` | Rp160.000 |
| Ijen Ticket | `pax` | Rp75.000 |
| Bromo Jeep (JVTO, Klook) | `Math.ceil(pax/4)` | Rp500.000 |
| Bromo Jeep TWT | `Math.ceil(pax/4)` | Rp550.000 |
| Transport Allowance | `1` | Rp100.000 |
| Mineral Water | `Math.ceil(pax/3)` | Rp40.000 |
| T-shirt (Kubik) | `pax` | Rp60.000 |
| Toll & Parking | `1` | Rp200.000 |
| Driver cum guide per hari | hari × qty × tarif | JVTO Rp250.000, Klook Rp250.000, TWT Rp313.000 |

## Empat kartu ringkasan

| Kartu | Isi | Kolom |
|---|---|---|
| Total Expenses | Semua item | `expense_internal_total` |
| Crew Expense | Belum dibayar dan bukan pay later, ditransfer ke crew | `total_expense_crew` |
| Already Paid | Sudah dibayar langsung ke vendor | `total_expense_paid` |
| Pay Later | Hutang ke vendor | `total_expense_debt` |

Contoh booking Klook 2 pax 3 hari: total Rp5.015.000 = Pay Later Rp3.715.000 + Crew Expense Rp1.300.000.

Pay later sejak dibuat: JVTO: Ijen Ticket, Mineral Water, T-shirt. Klook: Baratha dan Joglo Kecombrang, Bromo Ticket, Ijen Ticket, Bromo Jeep, Mineral Water, T-shirt, kendaraan. TWT: tidak ada.

## Vendor

| Kategori | Contoh |
|---|---|
| Accommodation | Baratha Hotel, Joglo Kecombrang, Riverside |
| Activities | Widhi Bromo (tiket dan jeep Bromo), Ijen Ticket |
| Transportation | Garage, Ertiga, Boy, Yandi, Pak Yus |
| Others | Mineral Water, Kubik (kaos) |

## Membayar

- **Crew:** Transfer Expense lewat Xendit payout (`/finance/xendit-payouts`), status `crew_transfer_status` pending/transferred di Finance Hub. Transfer BCA tercatat di Crew Transfer Records.
- **Hutang vendor:** per vendor lewat Finance Hub atau `/finance/vendor-debts` (`DebtPaymentService`). Nomor `JVR/PAY/MM/YY/0001`. Bisa di-void dengan alasan.
- **Rekap Hutang:** hutang belum lunas per vendor per bulan trip, ekspor PDF dan Excel.
- Lainnya di Expense Manager: Additional Requests dari crew, Refund, Riwayat Payout Xendit.

Item yang sudah dibayar atau terikat payout aktif terkunci sampai pembayarannya di-void. Semua perubahan tercatat di audit log.

Sumber: [[Ekosistem JVTO]].
