---
type: process
status: active
department: Layanan Pelanggan
tags: [booking, twt, partner, sop]
updated: 2026-11-04
---
TWT (The Window Travel Sdn. Bhd., Malaysia, dipimpin SEAN) menjual paket miliknya sendiri. JVTO menjadi ground tour operator: tiket, kendaraan, hotel, crew. JVTO tidak mengatur harga TWT dan menerima komisi lewat **JVTO Service Fee**. Komunikasi dengan tamu dipegang penuh TWT.

## Langkah

1. TWT mengirim email booking singkat dengan PDF booking terlampir.
2. Gmail memberi label **TWT EKSTRAKTOR**; Apps Script mengirim email dan PDF (base64) ke [[new-backoffice]] `POST /twt-extractor`.
3. PDF disimpan, teksnya dibaca (Smalot PdfParser), lalu dikirim ke DeepSeek bersama master hotel, kamar dan aktivitas JVTO supaya hasilnya memakai ID master JVTO. Hotel dibatasi ke ID 59, 11, 60, 12, 63, 34, 56, 10.
4. Dibuat: pelanggan, booking (`agent_id = 1`, `booked`, `offline`, `pay later`), dokumen PDF (`attachment_type_id = 6`), detail kaos, itinerary per hari, hotel dan kamar. Nomor invoice TWT disimpan di `invoice_code_origin`.
5. `EcosystemSync::notify`. Tidak ada WA ke tamu. Hanya pengingat crew H-1 10:00 yang dijadwalkan.
6. Halaman unggah manual `/pdf-extractor` tersedia untuk PDF tanpa email.

## Isi PDF TWT

Halaman 1: paket, invoice, tamu, tanggal, pax, ukuran kaos, termasuk/tidak termasuk, itinerary dengan penerbangan dan jam jemput, akomodasi. Halaman 2: tabel biaya dalam enam kelompok: A. Accommodation, B. Activities, C. Transportation, D. Crews, E. Others, F. JVTO Service Fee.

Contoh: invoice TWT-0451, paket SUB5 (5D4N Surabaya private), 2 dewasa, total Rp11.277.000 termasuk JVTO Service Fee "4 Days or more", 1-3 pax, Rp460.000 per pax × 2 = Rp920.000. Tarif service fee mengikuti rumusan yang disepakati menurut lama trip dan jumlah pax.

## Pembayaran

`dp` dan `payment` = 0, `grand_total` dari PDF, `balance` = `grand_total`. Penagihan lewat [[Aplikasi TWT]]: lihat [[Penagihan TWT]]. Expense tidak dibuat otomatis.

Sumber: [[Ekosistem JVTO]].
