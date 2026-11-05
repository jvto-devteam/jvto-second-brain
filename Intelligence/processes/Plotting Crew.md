---
type: process
status: active
department: Operasional
tags: [crew, plotting, kendaraan, sop]
updated: 2026-11-04
---
Admin memplot kendaraan dan crew dari booking-overview [[new-backoffice]] (`POST /plotting`, `PlottingService::sync`).

## Isian

Vehicles, Drivers, Escort Guide, Ijen Guide (muncul bila booking punya tanggal Ijen `at_bondowoso`), Notes. Daftar crew disaring menurut channel (kolom `tags` crew: JVTO, KLOOK, TWT).

## Status ketersediaan

| Status | Arti |
|---|---|
| Tersedia | Tidak ada plotting lain yang bentrok |
| Tidak Tersedia | Sudah diplot di booking lain pada tanggal beririsan |
| Terplotting | Sudah diplot di booking ini |

Satu guide tidak bisa jadi Ijen Guide di dua booking dengan tanggal Ijen sama. Ijen Guide hanya satu hari. Booking shuttle selesai sehari sebelum tanggal selesai.

## Selalu bisa diplot

- Driver GARAGE (ID 9).
- Kendaraan Garage: Hiace Commuter (ID 5), Premio (ID 21), Innova Reborn (ID 2), Avanza Sub (ID 4). MPV (ID 1) juga selalu tersedia.
- Local Guide (ID 56, "Local Ijen") untuk Ijen.

## Saat disimpan

Satu transaksi: crew yang dicabut dihapus, yang baru ditambah, tanggal disesuaikan, audit log ditulis. Bila "Beritahu crew" dicentang dan `WA_NOTIFY_CREW_PLOTTING` aktif (bawaannya mati), crew baru mendapat WA lewat API watzap (bukan wa-inbox) dengan tautan [[crew-portal]]. Crew juga diingatkan H-1 (lihat [[Pengingat WhatsApp Otomatis]]).

Sumber: [[Ekosistem JVTO]].
