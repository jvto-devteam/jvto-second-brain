---
type: process
status: active
department: Layanan Pelanggan
tags: [whatsapp, otomatisasi, reminder]
updated: 2026-11-04
---
Command [[legacy]] dijadwalkan tiap menit di `Kernel.php`. Tiap command mencari booking yang kolom `wa_schedule_*` cocok dengan jam sekarang dan `wa_status_*` masih 0, mengirim template lewat [[wa-inbox]], lalu menandai 1 (atau 00 bila gagal) dan mencatat `WaLog`.

## Ke tamu dan crew

| Penerima | Command | Waktu | Template |
|---|---|---|---|
| Tamu | `my:trip-information` | H-2 20:00 | `trip_information` (varian bandara, stasiun, hotel, umum) dengan pickup sign; lalu `trip_payment_arrangement` bila diisi admin |
| Tamu | `my:trip-media` | Hari terakhir 15:00 | `trip_concluded`: terima kasih, portal, ajakan ulasan Google/Trustpilot. Sekalian dibuat gift card Rp450.000 berlaku sampai 31 Desember tahun berjalan |
| Tamu | `my:reminder-payment` | H-7 15:00 | `payment_reminder_balance` (lihat [[Pelunasan dan Verifikasi Pembayaran]]) |
| Tiap crew | `my:trip-reminder-crew` | H-1 10:00 | `crew_reminder_personal` dengan tautan [[crew-portal]] |
| Grup crew | `my:trip-reminder-crew` | H-1 10:00 | `crew_reminder_group`, hanya booking TWT |
| Grup crew | `my:trip-media-crew` | H-1 15:00 | `crew_trip_media` |

`my:trip-itinerary` dan `my:trip-itinerary-crew` terdaftar tapi kodenya dikomentari, jadi tidak mengirim apa pun. Pesan tamu dan grup media hanya terkirim bila `is_send_wa = 1`; pengingat crew tidak.

## Siapa mengisi jadwal

| Sumber | Jadwal tamu dan media | Pengingat crew |
|---|---|---|
| Klook (email) | Diisi | Diisi |
| new-backoffice (simpan/ubah) | Bila kirim WA aktif | Selalu |
| Backoffice legacy | Bila kirim WA aktif | Tidak |
| TWT | Tidak | Diisi |
| Website JVTO | Baru terisi saat admin menyimpan dari form edit dengan kirim WA aktif | Saat disimpan dari form edit |

Booking website yang belum diedit admin masih `is_send_wa = 0` dengan jadwal kosong.

## Ke vendor

| Vendor | Template | Kapan |
|---|---|---|
| Agen transport Bali (Pak Agus Bali) | `internal_bali_transport` | Pembayaran pertama booking dari Bali atau dengan add-on transport |
| Agen transport Bali | `internal_bali_transport_reminder` | `my:reminder-bali` 15:15, untuk layanan besok |
| Vendor kaos | `vendor_tshirt_size_update` | Ukuran kaos berubah lewat edit admin atau quick update (bukan dari portal tamu) |

Kendaraan Bali: Avanza 1-3 pax, Elf Short 4-9, Elf Long 10+. Paket dari Bali: jemput di titik booking, antar Ketapang, porter dan tiket ferry. Add-on transport: jemput Gilimanuk.

## Lain

`hotel_room_reservation` ke grup tiap hotel, `internal_new_booking` ke nomor internal, `booking_confirmed_backoffice_jvto` / `_klook` untuk booking input admin. Tautan portal di produksi berbentuk `jvto.me/b/<kode>`.

Sumber: [[Ekosistem JVTO]].
