---
type: project
status: planned
owner: David
department: Teknologi
aliases: [Checkout di WhatsApp]
tags: [ai, checkout, whatsapp, direct-booking, inovasi]
created: 2026-11-04
updated: 2026-11-04
---
## Overview
Saat bot [[wa-inbox]] menjawab harga paket standar, ia langsung mengirim link checkout [[jvto-web]] yang sudah terisi paket, tanggal, pax dan add-on. Tamu tinggal mengisi kontak dan membayar.

## Cara kerja
1. Bot mengenali paket, tanggal dan pax dari chat (sudah ada di klasifikasi sales).
2. Cek tanggal tutup dan Rijik sebelum mengirim link.
3. Buat link bertanda tangan, misalnya `/checkout?pid=..&date=..&pax=..&src=wa&lead=<id>` (sekarang checkout membaca dari `localStorage`, jadi perlu diubah agar bisa dari parameter URL).
4. Atribusi `src=wa` dan id lead tersimpan di booking, supaya tahap pipeline wa-inbox otomatis naik ke `booked`.
5. Bila tamu tidak menyelesaikan checkout dalam 24 jam, masuk [[Prediksi Lead dan Follow-up]].

## Syarat
Perbaiki dulu tombol Skip add-on yang buntu (lihat [[Tour PDP v2]]) dan nyalakan verifikasi harga server.
