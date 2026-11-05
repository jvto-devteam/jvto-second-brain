---
type: project
status: planned
owner: Agung Sambuko
department: Teknologi
aliases: [Copilot Owner]
tags: [ai, briefing, whatsapp, inovasi]
created: 2026-11-04
updated: 2026-11-04
---
## Overview
Briefing pagi ke WhatsApp owner, plus tanya-jawab bahasa biasa ("berapa hutang vendor bulan ini?"). Gabungan MCP internal JVTO, agen Operator (dibuat lewat `/operator`) di vault ini, dan [[wa-inbox]] sebagai pengirim.

## Isi briefing (setiap pagi, misalnya 06:00 WIB)
| Bagian | Sumber |
|---|---|
| Trip hari ini dan besok: tamu, paket, crew, hotel, jam jemput | MCP `ops_daily_board`, `today` |
| Kesiapan yang bolong: guide, hotel, drop-off belum terisi, screening Ijen | MCP `ops_readiness_gaps`, `medical_checkup_list` |
| Pembayaran tertunda: sisa tagihan, H-7 yang belum lunas, transfer menunggu approve | MCP `payments_outstanding` |
| Hutang vendor jatuh tempo | MCP `vendor_payables`, `vendor_totals` |
| Anomali: profit negatif per booking, klaim crew di atas harga master, booking tanpa plotting H-2, chat tamu bernada kecewa | MCP `profit_summary`, `expense_trip` + [[Deteksi Tamu Kurang Puas]] |
| Tugas owner dari vault | `Team/jvto/Profiles/agung-sambuko/task-list/Tasks.md` |

## Arsitektur
1. Jadwal pagi (scheduled task lokal, atau cron di VPS).
2. Agen membaca MCP internal (read-only) + vault, menyusun briefing maksimal 1 layar HP.
3. Kirim lewat wa-inbox `POST /api/v1/system-messages` dengan template baru `owner_morning_briefing` ke nomor owner.
4. Briefing juga disimpan di `Team/jvto/Profiles/agung-sambuko/Daily/YYYY-MM-DD.md`.
5. Tahap 2: owner membalas WA dengan pertanyaan, wa-inbox meneruskan ke agen yang punya akses MCP.

## Fondasi yang sudah ada
MCP internal JVTO (read-only, angka dihitung backoffice), Internal API new-backoffice, template sistem wa-inbox, Kantor AI.

## Aturan dari prototipe
- Saldo dipisah per channel: JVTO (tagihan ke tamu), TWT (seharusnya dibayar di muka), Klook (piutang OTA, dibayar bulanan sesudah trip).
- Kode booking saja, tanpa nama tamu dan nomor HP.
- Maksimal satu layar HP, anomali selalu di bagian akhir.

## Keputusan terbuka
- Jam kirim dan siapa saja penerimanya (owner saja, atau juga [[Inant Kharisma]]).
- Briefing memuat nama tamu atau cukup kode booking (data pribadi lewat WA).

## Next Steps
- [x] Buat prototipe briefing dari MCP (2026-11-04). Hasil di `Team/jvto/Profiles/agung-sambuko/Daily/2026-11-04.md`.
- [x] Owner menilai isi dan panjang briefing: sudah pas (2026-11-04). Prompt di `specs/prompt-briefing-pagi.md`.
- [x] Jalur pertama: tulis ke vault dulu, WA menyusul (keputusan owner 2026-11-04).
- [x] Jam briefing 08:30 WIB, digabung dengan Operator harian (2026-11-05).
- [ ] Tetapkan definisi "hutang jatuh tempo" per vendor.
- [ ] [[David]] menambah template `owner_morning_briefing` di wa-inbox (spesifikasi di `specs/prompt-briefing-pagi.md`).
- [x] Dijadwalkan sebagai tugas lokal `jvto-operator-harian` (2026-11-05). Lihat [[Prompt Operator Harian]].
