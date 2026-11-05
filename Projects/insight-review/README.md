---
type: project
status: planned
owner: Agung Sambuko
department: Operasional
aliases: [Insight Review]
tags: [ai, review, crew, kualitas, inovasi]
created: 2026-11-04
updated: 2026-11-04
---
## Overview
Setiap ulasan baru (Google harian sudah disinkron, Trustpilot, Tripadvisor, Klook) dibaca AI dan diubah jadi masukan, kritik dan penilaian crew.

## Output
| Laporan | Isi |
|---|---|
| Per ulasan | Sentimen, tema (crew, value, itinerary, keselamatan, hotel, makanan, kendaraan), kritik spesifik, crew yang disebut |
| Kartu crew bulanan | Jumlah disebut, kutipan pujian, kritik, rating rata-rata trip yang ia bawa, ketepatan waktu dari [[Crew App v2]] |
| Tren bulanan | Tema naik/turun, masalah berulang (hotel tertentu, jeep, makanan) |

## Fondasi
`5-experience-engine/reviews` sudah punya rekaman ulasan Google, rekonsiliasi alias crew, dan ulasan unggulan per crew. Snapshot 147 ulasan: crew 110, value 96, itinerary 81, keselamatan 71.

## Aturan
Kritik dipakai untuk pembinaan, bukan hukuman otomatis. Ulasan buruk langsung masuk briefing [[Copilot Owner]].
