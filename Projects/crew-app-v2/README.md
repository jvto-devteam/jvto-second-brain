---
type: project
status: planned
owner: David
department: Operasional
aliases: [Crew App v2]
tags: [crew, gps, mobile, inovasi]
created: 2026-11-04
updated: 2026-11-04
---
## Overview
Satukan [[crew-portal]] (PWA), API "Super Apps" di [[legacy]] (`api/v1/*`, surat tugas, ID card, jadwal) dan push Firebase jadi satu aplikasi crew. Fitur baru: status penjemputan untuk tamu, Guest Brief, asisten suara.

## Status penjemputan: bukan pelacakan, tapi linimasa
Crew biasanya sudah menunggu di bandara 30-60 menit sebelum tamu mendarat. Jadi yang dibutuhkan tamu bukan peta mobil bergerak, tapi kepastian: **"guide saya sudah di sana, di titik mana, dan saya mencari siapa"**.

| Status | Pemicu | Yang dilihat tamu (portal + WA) |
|---|---|---|
| 1. Crew ditetapkan | Plotting disimpan | Nama, foto, peran guide dan driver, kendaraan (sudah ada di portal) |
| 2. Berangkat ke titik jemput | Crew tekan "Berangkat" | "Guide Anda sedang menuju bandara" |
| 3. Tiba dan menunggu | Crew tekan "Sudah tiba" di dalam geofence titik jemput; GPS dicatat sebagai bukti | "Fauzi sudah menunggu di Pintu Kedatangan Internasional T1 sejak 18:20, memegang papan nama Anda" + **foto crew memegang pickup sign** di lokasi |
| 4. Status penerbangan | API status penerbangan dari nomor flight tamu | "Pesawat Anda mendarat 19:05 (terlambat 20 menit). Guide Anda tahu dan tetap menunggu" |
| 5. Bertemu tamu | Crew tekan "Bertemu tamu" atau scan QR booking di HP tamu | Trip dimulai |
| 6. Dalam perjalanan | Otomatis | Itinerary hari ini |

**Kapan live location dipakai:** hanya untuk **jemput di hotel** yang jamnya penting (Bromo midnight 00:30-01:00, berangkat Ijen 00:00): 30 menit sebelum jemput, aplikasi berbagi lokasi dan tamu melihat "Driver tiba sekitar 12 menit lagi". Di bandara dan stasiun cukup status "menunggu" + foto + titik temu.

**Geofence:** daftar titik jemput dari master area (Juanda T1/T2, Stasiun Gubeng, Pasar Turi, hotel). Tombol "Sudah tiba" hanya valid bila GPS di dalam radius (bandara sekitar 1 km, hotel sekitar 200 m). Untuk jemput hotel, alamat hotel tamu di-geocode saat booking.

**Batasan teknis:** PWA di iPhone tidak bisa melacak lokasi di latar belakang. Tahap 1 cukup lokasi saat crew menekan tombol (jalan di PWA sekarang). Tahap 2 (live location jemput hotel) butuh aplikasi native atau pembungkus seperti Capacitor, dengan Firebase yang sudah ada untuk push.

**Data baru:** tabel `trip_events` (booking_id, crew_id, tipe, lat, lng, akurasi, foto, waktu). Template WA baru di wa-inbox: `pickup_crew_waiting` (dengan foto), `pickup_crew_on_the_way`.

**Manfaat internal:** ketepatan waktu crew terukur (tiba berapa menit sebelum jadwal), masuk ke penilaian crew bersama [[Insight Review]]. Juga bukti bila ada komplain "guide tidak ada".

## Fitur lain di v2
[[Guest Brief]] H-1, [[Asisten Suara Crew]], foto trip ke Instagram (sudah ada), klaim dan refund (sudah ada), mode offline untuk Ijen dan Bromo.

## Keputusan terbuka
- Persetujuan crew untuk lokasi: hanya saat jendela trip, bukan sepanjang hari.
- PWA saja dulu atau langsung native.
- Penyedia API status penerbangan.

## Next Steps
- [ ] Tahap 1: tombol status + GPS saat tekan + foto pickup sign + template WA (PWA).
- [ ] Tambah status penerbangan dari nomor flight di booking.
- [ ] Tahap 2: aplikasi native untuk live location jemput hotel.
