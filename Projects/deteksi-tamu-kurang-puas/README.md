---
type: project
status: planned
owner: Inant Kharisma
department: Layanan Pelanggan
aliases: [Deteksi Tamu Kurang Puas]
tags: [ai, service-recovery, whatsapp, inovasi]
created: 2026-11-04
updated: 2026-11-04
---
## Overview
Selama trip berlangsung, AI memantau nada chat tamu (dengan admin dan crew-chat). Tanda kecewa memicu peringatan ke owner dan admin agar diperbaiki sebelum jadi ulasan buruk.

## Sinyal
Kata kunci komplain (sudah ada di handoff wa-inbox), klasifikasi LLM eskalasi (sudah ada), plus sinyal baru: nada menurun dibanding awal trip, keluhan berulang soal hal yang sama, pertanyaan "kapan" yang tidak dijawab lebih dari 30 menit.

## Alur
1. Peringatan langsung (push PWA wa-inbox + WA ke owner) dengan ringkasan masalah dan kutipan.
2. Admin menghubungi tamu atau crew, mencatat tindakan.
3. Setelah trip, kasus masuk [[Insight Review]] untuk dicocokkan dengan ulasan yang datang.

## Batasan
Hanya tamu yang chat dengan JVTO (bukan TWT). Peringatan tidak otomatis mengirim apa pun ke tamu.
