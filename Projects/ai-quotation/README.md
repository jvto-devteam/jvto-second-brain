---
type: project
status: planned
owner: David
department: Layanan Pelanggan
aliases: [AI Quotation]
tags: [ai, sales, quotation, direct-booking, inovasi]
created: 2026-11-04
updated: 2026-11-04
---
## Overview
Tamu bertanya trip custom lewat WA ("4 hari, mendarat Surabaya jam 19:00, lanjut ke Bali"). AI menyusun draf itinerary dan harga, admin menyetujui, tamu menerima link bayar.

## Bahan yang sudah ada
- Aturan rute dan rekomendasi: `trip-readiness/recommendation-rules.json`, `road-situation-profiles.json`, `accommodation-logic.json` di [[jvto-ekosistem]].
- Aturan quotation (skill `tour-quotation` di [[new-backoffice]]): mendarat 18:00 atau lebih berarti Bromo dulu, Ijen berangkat 00:00-00:30, jeep maks 4 pax, kursi = tamu + guide + driver, transfer Bali flat per zona.
- Harga master: hotel, kamar, aktivitas, kendaraan, crew (`export:cost-reference-rates`), 17 paket di [[Itinerary Paket]].
- Penutupan: Rijik, `departure-closures.json`, status gunung. Lihat [[Plan B dan Penutupan]].

## Alur
1. wa-inbox mendeteksi permintaan custom (job sales J-series).
2. AI mengambil syarat: tanggal, jam mendarat, titik akhir, pax, minat, batas fisik.
3. Cocokkan dulu ke 17 paket standar; bila ada yang pas, langsung [[Checkout di WhatsApp]].
4. Bila custom: susun itinerary dari aturan rute, hitung biaya dari harga master + margin standar, cek tanggal tutup dan Rijik.
5. Validator menolak draf yang melanggar aturan (bolak-balik, air terjun sebelum penerbangan, 3 destinasi dalam 2 hari).
6. Kartu draf muncul di wa-inbox untuk admin: setujui, ubah, atau tolak.
7. Disetujui: tamu menerima PDF quotation + link DP.

## Keputusan terbuka
- Rumus margin untuk trip custom.
- Standar alur B (custom lewat WA) yang belum diputuskan.
- Masa berlaku quotation (aturan sekarang: tanpa masa berlaku).
