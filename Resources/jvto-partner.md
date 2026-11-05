---
type: resource
status: active
department: Operasional
tags: [sistem, vendor, repo]
updated: 2026-11-04
---
Portal vendor di partner.javavolcano-touroperator.com. Repo GitHub `jvto-devteam/hotel`, folder lokal `/Users/macbook/Code/jvto-partner`. Laravel + Inertia/React, database booking bersama. Dibuka dari menu Portal Vendor di [[new-backoffice]]. Tidak ada commit sejak 2025-12-28.

**Tanpa login:** akses lewat slug URL. Tampilan admin memakai prefix `/admin/reservation/{slug}` (tombol tandai lunas).

| Halaman | Vendor | Fungsi |
|---|---|---|
| `/reservation/{slug}` | Hotel: Baratha Hotel, Riverside Homestay, Joglo Kecombrang, Grand Padis | Reservasi per hotel: New Booking, Confirmed, Ditolak. Hotel klik Room Available atau Room Full (wajib alasan). Admin tandai sudah dibayar. Filter hutang, PDF |
| `/ijen-ticket` (alias `/doggy-style`) | Pengurus tiket Ijen | Rekap tiket per tanggal Ijen: pax, biaya, jumlah grup |
| `/health-screening` | Dokter / medical check up | Tamu yang perlu cek H-1 Ijen di Baratha, Grand Padis, Riverside (hotel_id 1, 10, 34), dengan guide dan kontaknya |
| `/bromo-recap` | Widhi Bromo (tiket + jeep) | Rekap per booking per tanggal Bromo, filter channel (JVTO, KLOOK, TWT, STUDENT) dan hutang, PDF |
| `/garage-recap` | Garage | Rekap kendaraan garasi, garasi isi nama driver dan catatan, grafik, PDF |
| `/travel-tshirt/kubik` | Kubik (kaos) | Pesanan kaos per booking, status Proses atau Selesai, PDF |

## Aturan
- Hutang ke vendor = flag `is_debt` per baris. Hotel juga punya `is_paid` dan `paid_at`. Rekap PDF per vendor per bulan jadi dasar penagihan vendor ke JVTO.
- Saat hotel menjawab untuk booking TWT yang masih tahap Hotel Check, portal mengirim WA status ke grup internal (lewat WatZap langsung).

## Catatan teknis terbuka
Tidak ada autentikasi (siapa pun yang tahu URL bisa melihat nama tamu dan mengubah status); daftar hotel medical check up di-hardcode; WA masih lewat WatZap. Menambah login adalah keputusan owner (kebijakan akses vendor).
