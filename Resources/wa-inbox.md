---
type: resource
status: active
department: Layanan Pelanggan
tags: [sistem, whatsapp, chatbot, repo]
updated: 2026-11-04
---
Inbox omnichannel + chatbot internal JVTO di wa-inbox.javavolcano-touroperator.com. Repo `jvto-devteam/wa-inbox`, folder lokal `/Users/macbook/Code/wa-inbox`. Next.js 16, React 19, Prisma 7, PostgreSQL. 670 commit sejak 2026-07-26, terakhir 2026-10-31. "Bukan SaaS, tidak akan pernah." Prinsip: edit, simpan, langsung aktif. Aturan lengkap di `CLAUDE.md` repo (587 baris).

## Pengguna
Admin dan CS JVTO (peran ADMIN dan AGENT), login email + password, PWA dengan push notification.

## Menu
| Menu | Isi |
|---|---|
| Beranda | Funnel, kesehatan bot, outbound, reminder |
| Inbox | Semua kanal; panel kontak, booking, label, reminder, catatan, ringkasan chat, kartu Aksi Booking |
| Kontak | Booking, label, reminder, catatan, Consent |
| Summary Harian | Antrean tindakan harian |
| Template Pesan | Template Meta, quick reply, system templates |
| Chatbot | Auto-reply, lewati nomor Indonesia, handoff, jam kerja |
| Bot Control | Referensi (Flow Map 28 node, Rules), Alur Live, Knowledge, Decision Logs, Test Lab, Outbound Queue, Audit Logs |
| Kantor AI | Kantor 3D dengan 4 asisten: Cahyo (label topik), Tarjo (CS bot), Samsul (ops booking), Asep (ringkasan). Tindakan dijalankan setelah operator klik "Jalankan" |
| Pengaturan | Akun, kanal default, Gmail, safety outbound |

## Kanal
- WhatsApp Official: WhatsApp Cloud API Meta (WABA). Semua template lewat kanal ini.
- WhatsApp Unofficial: gateway wa-dashboard JVTO (kirim saja).
- Instagram, Facebook Messenger, Gmail (bot tidak pernah membalas email).
- Booking API JVTO (lookup per nomor HP) dan legacy `my-booking` untuk menulis perubahan.
- LLM: Ollama di VPS, model `gemma4:31b-cloud`. Tag cloud berarti teks pelanggan dan data booking diproses di ollama.com, keluar dari VPS (terverifikasi 2026-09-10).
- Pengetahuan: 14+ file `catalog/*.json` disinkron dari repo saudara, plus managed knowledge di database.

## Gateway pesan otomatis
`POST /api/v1/system-messages`: cara [[legacy]] dan [[new-backoffice]] mengirim semua WA otomatis (template, tujuan, variabel, kunci idempotensi). 28 system template, antara lain `booking_pending_payment`, `payment_received_first`, `payment_received_balance`, `payment_reminder_balance`, `trip_information` (4 varian), `trip_daily_itinerary`, `trip_concluded`, `hotel_room_reservation`, `internal_new_booking`, `internal_bali_transport*`, `vendor_tshirt_size_update`, `crew_reminder_personal`, `crew_reminder_group`, `crew_trip_media`, `internal_consent_completed` (Typeform). Detail jadwal di [[Pengingat WhatsApp Otomatis]].

## Chatbot
1. Kata kunci komplain ("komplain", "kecewa", "agent manusia", "frustrated", dll.) langsung handoff ke manusia.
2. Klasifikasi LLM paralel menangkap komplain, permintaan manusia dan inquiry B2B.
3. Mode 3: nomor yang punya booking dijawab hanya dari data booking.
4. Tanpa booking: jawaban dari katalog bila rilis katalog disetujui (deployment gate), lalu diverifikasi.

Bot jalan 24/7; nomor Indonesia dilewati bot bila setelan aktif. Bot tidak dipakai untuk tamu TWT.

## Chat jadi Aksi Booking (Oktober 2026)
Pesan tamu yang menyebut data booking jadi kartu: CHANGE, MISMATCH, TASK, DATA_REQUEST. Kelompok AI (jemput/antar Surabaya, kaos, catatan: bisa otomatis), BALI (transport Bali, vendor dikabari), HEAVY (tanggal, pax, hotel, Surabaya-Bali: tugas admin). Keputusan owner 2026-10-20: tulis langsung, admin dikabari. Bisa dibatalkan. Permintaan data otomatis H-7 sampai H-1, 08:00-20:00 WIB. Rollout `BOOKING_ACTIONS_ROLLOUT`: `test` (whitelist) atau `all`.

## Ringkasan harian
Cron 00:00 membuat antrean REPLY (belum dibalas), FOLLOW_UP (tamu diam), PROMISE (janji tim belum ditepati). Cron tiap 5 menit menaikkan pipeline stage: Baru, Negosiasi, booked, lunas, selesai. Data ringkasan dipangkas setelah 30 hari, audit log bot 365 hari.

## Deploy
VPS sendiri, PM2, nginx, Node 22. `prisma migrate dev` dilarang karena `DATABASE_URL` lokal menunjuk produksi. Crontab: tick tiap 5 menit, ringkasan 00:00, sync email tiap 15 menit.

## Keputusan owner yang masih terbuka
- Membuka rollout Aksi Booking ke semua nomor (`all`).
- Menjadwalkan cron retry outbound (`/api/outbound-jobs/process` belum punya cron di produksi).
- Migrasi app Meta "Wa Dashboard" ke "Wa Dashboard JVTO" (rencana 2026-09-25).
- LLM cloud memproses chat dan data booking di luar VPS.
