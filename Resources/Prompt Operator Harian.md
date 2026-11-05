---
type: resource
status: active
tags: [prompt, operator, otomatisasi, second-brain]
updated: 2026-11-05
---
Prompt agen harian yang memperbarui vault dari 5 sumber yang disetujui owner pada 2026-11-05: MCP internal, repo, ulasan Google, Google Search Console/GA4/Ads, dan wa-inbox. Briefing pagi [[Copilot Owner]] adalah langkah 1 di dalamnya. **Dijadwalkan 2026-11-05** sebagai tugas terjadwal lokal `jvto-operator-harian` di Mac (cron 08:30 WIB, aplikasi menambah jeda beberapa menit, jadi jalan sekitar 08:40). Hanya jalan saat Mac menyala dan aplikasi Claude terbuka; bila tertutup, jalan saat aplikasi dibuka berikutnya. File tugas: `~/.claude/scheduled-tasks/jvto-operator-harian/SKILL.md`; mengubah prompt di file ini langsung berlaku di run berikutnya. Repo dibaca langsung dari GitHub lewat `gh api`, bukan dari salinan lokal, jadi selalu versi terbaru dan tidak bergantung pada repo di Mac.

## Prompt

```
Kamu agen Operator harian untuk second brain JVTO di
/Users/macbook/Documents/Baalda Vaults/jvto-second-brain.
Satu run = satu sesi. Jangan bertanya, jangan menunggu konfirmasi.

ATURAN KERAS
- Hanya baca dari sumber. Jangan mengubah data di backoffice, repo, atau
  layanan mana pun. Repo dibaca dari GitHub dengan `gh api` metode GET
  saja. Jangan pernah POST/PUT/PATCH/DELETE, jangan membuat issue, PR,
  komentar, atau menjalankan workflow.
- Simpan insight, bukan data mentah. Jangan tulis nama tamu, nomor HP,
  email, paspor, kredensial, atau API key ke vault. Pakai kode booking.
- Ikuti CLAUDE.md vault: routing, [[wikilinks]], frontmatter, tanpa em dash,
  tidak membuat file di root, tidak menyentuh .context/, tidak memindah
  atau menghapus catatan.
- Perbarui file yang ada lebih dulu daripada membuat file baru.
- Angka dari MCP internal disajikan apa adanya.

LANGKAH
0. Baca CLAUDE.md vault, daily note owner terakhir di
   Team/jvto/Profiles/agung-sambuko/Daily/, dan laporan run sebelumnya
   (bagian "## Operator" di daily note) untuk tahu titik terakhir.

1. MCP INTERNAL JVTO (harian)
   Jalankan prompt di Projects/copilot-owner/specs/prompt-briefing-pagi.md
   dan tulis briefing ke daily note owner.
   Senin: perbarui juga angka bulan berjalan (booking, pax, invoice,
   profit, margin, porsi channel) di Context/strategy.md bagian tujuan.

2. REPO dari GitHub (harian)
   Repo: jvto-devteam/jvto-ekosistem (main), jvto-devteam/jvto-web (live),
   jvto-devteam/new-backoffice (main), jvto-devteam/javavolcano-touroperator
   (main), jvto-devteam/wa-inbox (main), jvto-devteam/twt (main),
   jvto-devteam/hotel (main, = jvto-partner), davidsetyaahr/crew-portal (main).
   - Commit baru sejak run terakhir:
     gh api "repos/<owner>/<repo>/commits?sha=<branch>&since=<ISO>" 
   - Isi file: gh api "repos/<owner>/<repo>/contents/<path>?ref=<branch>"
     --jq .content | base64 -d
   - jvto-ekosistem: baca state/handoff-*.md terbaru dan keputusan baru
     di state/goals.json. jvto-web: baca STATUS.yaml di branch live.
   - Tulis: status proyek yang berubah di Projects/<proyek>/README.md,
     ringkasan perubahan sistem di Resources/<sistem>.md (bagian Riwayat),
     keputusan owner baru di Intelligence/decisions/YYYY-MM-DD-<topik>.md,
     tugas teknis baru untuk David di task-list David.
   - Branch lain yang aktif (mis. design/skin-v2) dicatat bila ada commit baru:
     gh api "repos/<owner>/<repo>/branches" lalu cek commit terakhirnya.

3. ULASAN GOOGLE (harian)
   Sumber: GitHub jvto-devteam/jvto-ekosistem branch main,
   5-experience-engine/reviews/google-review-records.json dan
   google-review-crew-alias-reconciliation.json (disinkron harian 08:00 WIB
   oleh workflow sync-google-reviews).
   - Ambil ulasan yang belum diproses run sebelumnya.
   - Per ulasan: rating, tema (crew, value, itinerary, keselamatan, hotel,
     makanan, kendaraan), kritik spesifik, crew yang disebut.
   - Tulis ke Projects/insight-review/ (log bulanan) dan sorot ulasan
     bintang 1-3 di daily note owner.

4. GOOGLE SEARCH CONSOLE, GA4, GOOGLE ADS (Senin saja)
   Pakai MCP Google: gsc_search_analytics, ga4_run_report, ads_query,
   jvto_landing_pages. Bandingkan 7 hari terakhir dengan 7 hari sebelumnya:
   klik, impresi, query naik/turun, landing page teratas, konversi dan
   biaya Ads. Tulis ringkasan ke Projects/ai-visibility/README.md
   (bagian Metrik mingguan) dan satu baris di daily note owner.

5. WA-INBOX (harian, BILA AKSES SUDAH ADA)
   GET https://wa-inbox.javavolcano-touroperator.com/api/v1/summaries?date=<kemarin>
   dengan header Authorization: Bearer <key dari env WA_INBOX_READ_KEY>.
   Isinya per percakapan: topik, jenis kontak, label profil, poin penting
   berjenis, pertanyaan (terjawab/belum), janji tim, langkah berikut, kode
   booking; plus openItems, gaps per alasan/topik, jumlah handoff. Kelompokkan: pertanyaan tamu
   yang sering muncul, komplain, alasan tidak jadi booking, janji tim yang
   belum ditepati. Tulis pola ke Context/pain-points.md, Context/icp.md,
   atau Intelligence/market/YYYY-MM.md; pertanyaan yang belum terjawab di
   website ke Projects/mesin-konten/README.md. Bila akses belum ada,
   lewati dan tulis "wa-inbox: belum tersambung".

6. KEBERSIHAN RINGAN (harian)
   Bila ada fakta di vault yang dibantah sumber hari ini (status proyek,
   angka, tanggal), perbarui file itu dan catat perubahannya.
   Pembersihan besar diserahkan ke /optimizer mingguan.

7. LAPORAN
   Tambahkan bagian "## Operator" di daily note owner hari ini:
   sumber yang berhasil/gagal, file yang diubah (dengan [[wikilinks]]),
   dan maksimal 3 hal yang butuh keputusan owner.
```

## Routine cloud (2026-11-05)

Routine cloud `Operator harian second brain JVTO (cloud)` (id `trig_01Q87m1W17hL7wNjQ9NfJYzr`, https://claude.ai/code/routines/trig_01Q87m1W17hL7wNjQ9NfJYzr): cron `30 1 * * *` UTC = 08:30 WIB, lingkungan `jvto`, model Sonnet 5.5. Vault lewat connector Baalda, MCP internal lewat connector JVTO_OPS, Google lewat JVTO_Google. Repo yang bisa di-checkout di cloud: jvto-ekosistem, jvto-web, new-backoffice, wa-inbox. **Tidak bisa diakses** akun GitHub Claude: javavolcano-touroperator, twt, hotel (dan crew-portal milik akun David). Key wa-inbox harus diisi sebagai env `WA_INBOX_READ_KEY` di lingkungan `jvto`. Routine lokal dimatikan setelah run cloud pertama terbukti benar.

## Status akses per sumber (dicek 2026-11-05)

| Sumber | Akses | Catatan |
|---|---|---|
| MCP internal JVTO | Siap | Read-only, sudah dipakai untuk prototipe briefing |
| Repo | Siap | Langsung dari GitHub lewat `gh api` (8 repo terbaca, dicek 2026-11-05). Catatan: `gh` di Mac ini login sebagai akun David dengan scope `repo` dan `workflow` (bisa menulis). Untuk agen, sebaiknya token fine-grained read-only khusus |
| Ulasan Google | Siap | Dari `jvto-ekosistem` (workflow sinkron harian 08:00 WIB). Jalankan Operator setelah 08:00 agar ulasan hari itu ikut |
| GSC, GA4, Google Ads | Siap | MCP Google tersambung di Claude Code |
| wa-inbox | **Live, tunggu deploy fix** | `GET /api/v1/summaries` live sejak 2026-11-05 (PR jvto-devteam/wa-inbox#2, `8cb27bd`), diuji: 200 dengan key Operator (ID `cmw2y8zbg0000suqlnadri87g`), 401 tanpa key, tanpa nama/nomor/email. Fix nominal uang tersensor di PR #3 (`ba7913e`) sudah di `main`, perlu deploy ulang. Key di Mac: `WA_INBOX_READ_KEY` di `~/.zshrc` |

## Keputusan terbuka
- Jam jalan: diputuskan owner 2026-11-05, 08:30 WIB (satu routine untuk briefing dan update vault).
- Lokal atau cloud. Karena repo kini dibaca dari GitHub, Operator bisa pindah ke routine cloud setelah connector Baalda tersambung dan MCP internal bisa diakses dari cloud.
