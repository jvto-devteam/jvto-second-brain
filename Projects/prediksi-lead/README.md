---
type: project
status: planned
owner: Inant Kharisma
department: Layanan Pelanggan
aliases: [Prediksi Lead dan Follow-up]
tags: [ai, crm, follow-up, inovasi]
created: 2026-11-04
updated: 2026-11-04
---
## Overview
AI menilai peluang setiap lead di pipeline wa-inbox (Baru, Negosiasi, booked, lunas, selesai), menentukan waktu follow-up terbaik, dan menyusun draf pesan sesuai keberatan tamu.

## Sinyal penilaian
Tanggal trip sudah pasti, jumlah pax, negara, lead time (median booking 36 hari), sudah menerima harga, keberatan yang muncul (harga, keamanan, fisik), kecepatan balasan, pernah membuka link checkout.

## Cara kerja
1. Skor harian dihitung di tick wa-inbox (cron tiap 5 menit sudah ada).
2. Lead yang diam masuk antrean FOLLOW_UP Summary Harian (sudah ada) dengan draf pesan yang disesuaikan, misalnya untuk keberatan Ijen: jelaskan skrining dokter dan "hanya pendakian tamu itu yang batal".
3. Admin [[Inant Kharisma]] menyetujui satu klik. Maksimal 2 follow-up per lead.

## Aturan
Tidak ada diskon tanpa persetujuan. Tidak ada klaim baru. Hormati jam kirim 08:00-20:00 waktu tamu.
