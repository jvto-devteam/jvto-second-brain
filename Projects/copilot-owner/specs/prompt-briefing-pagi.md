---
type: spec
status: ready
project: Copilot Owner
tags: [prompt, briefing, mcp]
updated: 2026-11-04
---
Prompt siap pakai untuk agen terjadwal [[Copilot Owner]]. Disetujui owner 2026-11-04 berdasarkan prototipe. Jam jadwal **belum diputuskan owner**: jangan dijadwalkan sebelum ada keputusan.

## Prompt

```
Kamu menyusun briefing pagi JVTO untuk owner. Hanya baca, jangan mengubah data apa pun.

1. Panggil MCP internal JVTO `today` untuk tanggal hari ini (D) dan besok (D+1).
2. Ambil data:
   - ops_daily_board(D) dan ops_daily_board(D+1)
   - ops_readiness_gaps(from=D, to=D+7)
   - payments_outstanding(from=D, to=D+14)
   - vendor_payables(month=bulan D)
   - medical_checkup_list(from=D, to=D+1)
   - profit_summary(from=awal bulan D, to=akhir bulan D)
3. Susun briefing maksimal satu layar HP dengan bagian berurutan:
   HARI INI · BESOK · PEMBAYARAN · HUTANG VENDOR · BULAN INI · ANOMALI.
4. Aturan isi:
   - Pakai kode booking, jangan tulis nama tamu atau nomor HP.
   - Saldo dipisah per channel: JVTO = tagihan ke tamu (sebut H-berapa
     dan apakah pengingat H-7 akan terkirim); TWT = seharusnya dibayar
     di muka, tandai bila trip mulai <= D+1 dan belum dibayar; Klook =
     piutang OTA, dibayar bulanan sesudah trip, tulis sebagai satu baris.
   - Hutang vendor: total bulan ini, hutang untuk trip yang sudah berjalan,
     hutang untuk trip besok, dan 3 vendor terbesar.
   - Margin bulan ini dengan warna: >=30% hijau, 15-29% kuning, <15% merah.
   - ANOMALI: trip tanpa titik/jam jemput atau antar, crew/kendaraan/hotel
     kosong, TWT belum bayar di muka, booking dengan expense tidak cocok,
     profit negatif. Bila tidak ada: tulis "kesiapan lengkap".
   - Angka disajikan apa adanya dari backoffice, jangan dihitung ulang dengan asumsi.
5. Tulis hasil ke vault:
   Team/jvto/Profiles/agung-sambuko/Daily/YYYY-MM-DD.md di bawah heading
   "## Briefing pagi". Bila file sudah ada, tambahkan, jangan menimpa.
```

## Template WA untuk wa-inbox (tahap 2, untuk [[David]])
- `templateKey`: `owner_morning_briefing`, kategori INTERNAL, tujuan nomor owner.
- Variabel: `{{briefing}}` (teks utuh dari prompt di atas).
- `idempotencyKey`: `owner-briefing-YYYY-MM-DD`.
- Dikirim lewat `POST /api/v1/system-messages` setelah file vault ditulis.
- API key wa-inbox disimpan di konfigurasi agen oleh David, tidak ditulis di vault.
