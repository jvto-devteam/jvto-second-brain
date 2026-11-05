---
type: resource
status: active
department: Keuangan
tags: [sistem, erp, backoffice, repo]
updated: 2026-11-04
---
ERP internal JVTO di new-backoffice.javavolcano-touroperator.com. Repo `jvto-devteam/new-backoffice`, folder lokal `/Users/macbook/Code/new-backoffice`. Laravel 11 + Inertia + React/TS, MySQL bersama [[legacy]]. Login lewat tabel `accounts` (tabel `users` = tamu). Disebut "Booking Backoffice" di [[Ekosistem JVTO]].

## Menu
| Grup | Menu | Fungsi |
|---|---|---|
| | Homepage | KPI booking, revenue, profit |
| | Portal Vendor | Pintu ke [[jvto-partner]] |
| Bookings | Pending Booking | Approve bukti transfer (lihat [[Pelunasan dan Verifikasi Pembayaran]]) |
| | Booking Overview (tabel/kanban) | Papan operasional: channel, pax, jemput/antar, hotel, crew, mobil, kesiapan, keuangan, catatan (General, Important, Follow-up, Completed, In Progress, Special). Aksi: plotting, edit, bayar manual, trip media, quick update |
| | Booking Analist | Rekap bulanan per channel: kaos, Bromo, Ijen, Tumpak Sewu |
| | Client Management, CRM | Pelanggan, insight per negara/paket |
| Finance | Finance Manager, Finance Hub, Rekap Hutang, Channel Revenue Report, Payout | Lihat [[Expense Booking]]. Channel Revenue Report: JVTO, TWT, KLOOK, GetYourGuide, Viator + tagihan Google Cloud dan Ads |
| Data Master | Package Inventory (JVTO/Klook), Hotels, Activities, Transportation, Miscellaneous, Crew (rekening, Instagram), Vendor | |
| Tools | Short Link, Flipbook, Article Management, Crew Assignment | |
| Tersembunyi | `/pdf-extractor` (TWT), `/task-management`, `/calendar` (prototipe), `/wa-chat`, `/daily-chat-summary`, `/finance/bca-transfers`, `/screening` | |

**Di origin, belum di salinan lokal** (overhaul 2026-10-14): role `admin` dan `owner` (owner = read-only), audit log, Pembayaran Masuk, Piutang OTA, Hutang Vendor per vendor, Setoran Cash Crew, Buku TWT, Pembayaran Keluar, Refund, Biaya operasional, Laporan Laba, Papan Operasional, Penugasan Crew (`crew_unavailabilities`), Jadwal Trip readiness. 2026-10-19: approve transfer di server.

## Scheduler
| Command | Jadwal | Fungsi |
|---|---|---|
| `bca:sync-transfers` | Tiap 30 menit | Baca email KlikBCA Bisnis, cocokkan kata pertama keterangan ke kode booking. **Konvensi: keterangan transfer ke crew harus diawali kode booking.** |
| `wa:generate-daily-summaries` | 23:59 | Ringkasan chat WA harian per kontak dengan DeepSeek |
| `finance:poll-payouts` | Tiap 10 menit (origin) | Sinkron status Xendit payout |

## Integrasi
[[wa-inbox]], DeepSeek (pembaca PDF TWT, ringkasan WA), Xendit Payouts (crew/vendor, minimal Rp10.000), Gmail API, Google Drive (folder Trip Media per booking), Klook API, EcosystemSync, dan **Internal API** (`/internal-api/*`) untuk MCP internal JVTO: bookings, medical-checkups, vendor-totals, crew-recap.

## Data master
- **Channel** diturunkan: `agent_id 1` TWT; `agent_id 2` + kategori 3 KLOOK; selain itu JVTO. Sub-tag OTA `channel_tag`: klook, gyg, viator.
- **Agents:** 1 The Window (MY), 2 JVTO, 3 DH Trans, 4 Union (SG), 5 Java Land Tour, 6 Igo Tours, 7 JVR 2.
- **Kategori booking:** Reguler, Student, Klook.
- **Status booking:** `pending wise`, `pending pay later`, `reject pay later`, `booked`, `pending canceled`, `canceled`, `finished`.
- **Tarif peran crew per hari:** Driver cum guide Rp313.000, Escort Guide (Sr) Rp275.000, Escort Guide (Jr) Rp250.000, Driver + Guide Rp400.000. Kolom `rate_twt` untuk tarif TWT.
- **Konfigurasi mobil per hari:** mobil kecil 2-3 pax Rp480.000 + driver cum guide; Hiace 4-10 pax Rp1.100.000 + escort guide.
- **Area:** Bondowoso, Bromo, Lumajang, Surabaya, Malang, Ketapang, Bandara T1/T2, Bali, St. Gubeng, Jember, Yogyakarta.

## Aturan di kode
- Profit = invoice (grand total + add-on) dikurangi `expense_internal_total`. Finance Hub: margin 30%+ hijau, 15-29% kuning, kurang dari 15% merah. "Kebutuhan Dana Mendesak" = hutang vendor + transfer crew pending.
- Plotting: driver dan escort dari tanggal mulai sampai selesai (shuttle: minus 1 hari); guide Ijen 1 hari di `at_bondowoso`. Auto-plot: 3 pax atau kurang ke driver cum guide yang tersedia.
- Fee crew dibagi rata ke semua orang di penugasan yang sama; sisa rupiah ke orang pertama.
- Medical checkup = `at_bondowoso` minus 1 hari, hanya hotel Bondowoso.

## Pengetahuan quotation (skill `tour-quotation` di origin)
- Identitas: sebut **NIB** (jangan "License No."), email `hello@`, rekening **BCA saja**, quotation tanpa masa berlaku.
- Tiga alur booking: A website instant book (kanonik), B WA/email ke invoice ke link DP (custom, hanya atas instruksi), C last-minute 100% transfer BCA. Keputusan standar untuk alur B masih terbuka.
- Aturan rute: mendarat 18:00 atau lebih, Bromo dulu; Madakaripura, menginap sisi Probolinggo; Ijen berangkat 00:00-00:30, puncak sekitar 04:00; kursi = tamu + guide + driver; jeep Bromo maks 4 pax; cek event Bromo (mis. Jazz Gunung); hotel milik tamu, budget hotel dikembalikan cash.
- Transfer Bali dari Gilimanuk: tarif flat Rp500 rb-1,05 jt per zona.

## Risiko (butuh owner)
Route publik tanpa login (`export-data/*` termasuk data pelanggan, `/finance/rekap-hutang`, `/booking-overview/api`); `service-account-credentials.json` di working tree lokal; notifikasi plotting crew mati; salinan lokal tertinggal 24 commit dari origin (cek 2026-11-04). Tidak ada CI; aset `public/build` di-commit manual; alur `dev` ke PR `main`.
