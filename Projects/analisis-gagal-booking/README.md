---
type: project
status: planned
owner: Agung Sambuko
department: Layanan Pelanggan
aliases: [Analisis Gagal Booking]
tags: [ai, analitik, sales, inovasi]
created: 2026-11-04
updated: 2026-11-04
---
## Overview
Setiap bulan AI membaca percakapan yang tidak berujung booking dan merekap alasannya per negara dan per paket.

## Kategori alasan
Harga, tanggal penuh atau tutup, ragu soal Ijen (fisik, kesehatan, surat sehat), memilih OTA, memilih operator lain, durasi tidak cocok, rute ke Bali, tidak membalas lagi, lain-lain. Setiap alasan disertai kutipan pendek sebagai bukti.

## Cara kerja
1. Ambil kontak di pipeline wa-inbox yang berhenti di Baru atau Negosiasi lebih dari 14 hari.
2. LLM mengklasifikasi alasan dari ringkasan chat (sudah ada) dan transkrip.
3. Laporan bulanan ke `Intelligence/market/` di vault ini + ringkasan di briefing [[Copilot Owner]].

## Hasil yang diharapkan
Masukan untuk harga, paket, FAQ dan copy website. Pertanyaan yang sering muncul diteruskan ke [[Mesin Konten Berbasis Fakta]].
