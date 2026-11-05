---
type: project
status: planned
owner: Agung Sambuko
department: Teknologi
aliases: [Roadmap Inovasi AI]
tags: [roadmap, ai, inovasi]
created: 2026-11-04
updated: 2026-11-05
---
## Overview
11 inisiatif AI yang dipilih owner pada 2026-11-04. Semuanya dibangun di atas fondasi yang sudah ada: [[wa-inbox]] (ringkasan chat, pipeline, Summary Harian, kartu Aksi Booking), MCP internal JVTO, [[crew-portal]], [[jvto-ekosistem]].

| Fase | Inisiatif | Kenapa urutan ini |
|---|---|---|
| 1. Kemenangan cepat (data sudah ada) | [[Copilot Owner]], [[Insight Review]], [[Guest Brief]] | Hampir seluruhnya membaca data yang sudah tersedia; nilai langsung terasa |
| 2. Mesin pendapatan direct | [[Checkout di WhatsApp]], [[AI Quotation]], [[Prediksi Lead dan Follow-up]], [[Analisis Gagal Booking]] | Langsung mendukung O1 (direct mengungguli OTA) di [[strategy]] |
| 3. Crew dan layanan saat trip | [[Crew App v2]], [[Deteksi Tamu Kurang Puas]], [[Asisten Suara Crew]] | Butuh perubahan aplikasi crew dan persetujuan crew |
| Berjalan terus | [[Mesin Konten Berbasis Fakta]] | Makan masukan dari fase 1 dan 2 |

## Detail tiap inisiatif

Hub ini hanya ringkasan. Detail lengkap (cara kerja, fondasi yang sudah ada, aturan, keputusan terbuka, langkah berikut) ada di `README.md` folder masing-masing di `Projects/`:

| # | Inisiatif | Folder | Inti | Status |
|---|---|---|---|---|
| 1 | [[Copilot Owner]] | `Projects/copilot-owner/` | Briefing pagi ke owner: trip, pembayaran, hutang vendor, anomali | **Berjalan**: routine 08:30 WIB sejak 2026-11-05 |
| 2 | [[Insight Review]] | `Projects/insight-review/` | Ulasan jadi masukan, kritik, kartu crew bulanan | Rencana. Catatan: data ulasan Google di ekosistem basi sejak 7 Agustus |
| 3 | [[Guest Brief]] | `Projects/guest-brief/` | Ringkasan satu halaman tentang tamu di crew-portal H-1 | Rencana |
| 4 | [[Checkout di WhatsApp]] | `Projects/checkout-whatsapp/` | Bot mengirim link checkout terisi paket, tanggal, pax | Rencana |
| 5 | [[AI Quotation]] | `Projects/ai-quotation/` | Draf itinerary + harga trip custom dari aturan rute, admin menyetujui | Rencana |
| 6 | [[Prediksi Lead dan Follow-up]] | `Projects/prediksi-lead/` | Skor lead, waktu follow-up, draf pesan sesuai keberatan | Rencana |
| 7 | [[Analisis Gagal Booking]] | `Projects/analisis-gagal-booking/` | Alasan tidak jadi booking per negara per bulan | Rencana. Bahan sudah mengalir lewat API ringkasan wa-inbox |
| 8 | [[Crew App v2]] | `Projects/crew-app-v2/` | Satu aplikasi crew, status penjemputan bandara (bukan pelacakan), live location jemput hotel | Rencana |
| 9 | [[Deteksi Tamu Kurang Puas]] | `Projects/deteksi-tamu-kurang-puas/` | Peringatan ke owner/admin saat nada chat tamu menurun selama trip | Rencana |
| 10 | [[Asisten Suara Crew]] | `Projects/asisten-suara-crew/` | Tanya SOP/Plan B lewat suara, lapor insiden dan klaim jadi laporan terstruktur | Rencana |
| 11 | [[Mesin Konten Berbasis Fakta]] | `Projects/mesin-konten/` | Foto, ulasan, pertanyaan tamu jadi artikel/FAQ yang dicek ke ekosistem | Rencana |

Fondasi teknis yang sudah dibangun untuk roadmap ini: endpoint `GET /api/v1/summaries` di [[wa-inbox]] (PR #2 dan #3) dan [[Prompt Operator Harian]].

## Syarat sebelum mulai
1. **Keputusan privasi LLM.** Chat dan data booking sekarang diproses LLM cloud di luar VPS. Hampir semua inisiatif ini menambah data tamu yang dikirim ke LLM: pilih model lokal, penyedia tanpa retensi data, atau sensor data pribadi dulu.
2. **Pengamanan dasar** dari daftar tugas owner (repo legacy, portal vendor, route publik).
3. **Manusia menyetujui** setiap pesan ke tamu, quotation, dan perubahan booking berat.
4. **Satu sumber fakta:** semua jawaban dari ekosistem, tanpa klaim baru.
5. **Uji rutin:** kumpulan kasus uji dari chat nyata untuk setiap fitur AI.
