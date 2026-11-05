---
type: resource
status: active
department: Keuangan
tags: [sistem, twt, partner, repo]
updated: 2026-11-04
---
Portal untuk partner TWT di twt.javavolcano-touroperator.com. Repo `jvto-devteam/twt`, folder lokal `/Users/macbook/Code/twt`. Laravel + Inertia/React-TS, database booking bersama. Commit terakhir 2026-08-08.

## Akun
Username + password di tabel `agents`: ID 1 = TWT (username `admin` dipetakan ke akun `sean`), ID 2 = JVTO. Tidak ada akun lain.

## Menu
| Menu | Isi |
|---|---|
| Dashboard | Booking dan pax TWT bulan ini, aktif/selesai/mendatang, crew yang diplot dan rekap trip mereka |
| Bookings | Jadwal booking TWT `booked`: jemput, antar, itinerary, akomodasi, kaos, kendaraan dan crew, catatan. TWT bisa menulis catatan (otomatis WA "Note for {tamu}" ke grup internal), mengubah tautan Trip Media, mengunggah dokumen |
| Hotel Check | Cek kamar sebelum booking. Booking draft (`agent_id 1`, status kosong), harga kamar `rate_twt` (atau `rate`). WA "Room Reservation" ke grup tiap hotel dan "Waiting Hotel Confirmation" ke grup internal. Status: Waiting, Available, Room Full |
| Master Price | Harga khusus TWT: kamar (`rate_twt`), makan (`lunch_rate_twt`, `dinner_rate_twt`), aktivitas `is_twt = 1`, kendaraan, peran crew. Hotel TWT: ID 59, 11, 60, 12, 38, 34, 56, 10, 66. Ekspor Excel |
| Partnership Agreement | PDF perjanjian (`/assets/agreement.pdf`) |
| Finance | Invoices, Invoice Revision, Supplemental Expense Requests, Financial Adjustments. Lihat [[Penagihan TWT]] |
| Crew SOP | Checklist SOP tur per trip |

## Aturan
- Invoice `JVR-INV-TWT-{yy}-{mm}-{n}`: `unpaid`, `paid_partially`, `paid`. `grand_total` booking TWT diisi dari nilai invoice.
- Harga TWT memakai kolom `*_twt` (net rate) ditambah perjanjian. Rumus komisi tidak ada di kode; contoh nyata JVTO Service Fee ada di [[Alur Booking TWT]].
- WA lewat gateway `wa-dashboard` milik JVTO (kompatibel WatZap).

## Catatan teknis terbuka
ID grup WA internal di-hardcode; Hotel Check membuat user tamu dengan password bawaan; ada file sisa (`Index copy.tsx`, `Dashboard.tsx_old`).
