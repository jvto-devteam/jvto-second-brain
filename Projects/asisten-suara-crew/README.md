---
type: project
status: planned
owner: David
department: Operasional
aliases: [Asisten Suara Crew]
tags: [ai, crew, voice, sop, inovasi]
created: 2026-11-04
updated: 2026-11-04
---
## Overview
Di [[Crew App v2]], crew bisa bertanya lewat suara dalam bahasa Indonesia dan melapor lewat pesan suara.

## Tanya
"Ijen tutup, Plan B-nya apa?" dijawab dari [[Plan B dan Penutupan]], SOP skrining Ijen, status gunung, itinerary trip itu. Jawaban suara + teks, dengan sumber.

## Lapor
| Jenis | Hasil terstruktur | Tujuan |
|---|---|---|
| Insiden | Waktu, lokasi, tamu, apa yang terjadi, tindakan, tingkat | `4-operations-core/incident-log` (sekarang kosong) + peringatan owner |
| Klaim biaya | Item, qty, harga, foto nota dibaca AI | Additional & Claims (sudah ada) |
| Refund | Item, alasan, tujuan | Refund (sudah ada) |
| Catatan trip | Kondisi jalan, hotel, cuaca | Booking notes |

## Aturan
Crew selalu melihat ringkasan sebelum dikirim. Insiden tingkat tinggi langsung menelepon atau WA owner.
