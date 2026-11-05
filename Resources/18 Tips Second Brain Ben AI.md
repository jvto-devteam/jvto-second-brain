---
type: resource
status: active
source: https://www.youtube.com/watch?v=pXHpUyC54uQ
tags: [second-brain, panduan, ai]
updated: 2026-11-05
---
Ringkasan video "18 Tips to Set Up Your Ultimate AI Second Brain for 2027" (Ben AI, pembuat Baalda), yang ditonton owner sebelum membangun vault ini. Tiga skill yang dipasang (`/setup`, `/operator`, `/optimizer`) berasal dari video ini: tip 1, tip 9 dan tip 10.

Inti: second brain = satu folder lokal berisi file teks yang memberi AI konteks bisnis. 80% nilainya datang dari 5-10 file (strategi, ICP, brand voice, offer). Gagalnya bukan saat setup, tapi beberapa minggu kemudian karena tidak diperbarui.

| # | Tip | Status di vault JVTO (2026-11-05) |
|---|---|---|
| **Setup** | | |
| 1 | Mulai dengan brain dump 30 menit | Selesai: dari repo, dokumen Ekosistem JVTO dan jawaban owner |
| 2 | Simpan lokal, bukan Notion/Drive/memori bawaan | Selesai: folder lokal Baalda |
| 3 | Visualisasikan (Obsidian/Baalda) | Selesai: dibuka di Baalda |
| 4 | AGENTS.md/CLAUDE.md = peta routing, bukan manual, di bawah 200 baris | Ada: `CLAUDE.md` 121 baris, tapi masih template umum |
| 5 | Satu folder untuk semua | Selesai |
| 6 | Kunci struktur folder, jangan sering diubah | Struktur ditetapkan |
| 7 | File index di tiap subfolder (ide Karpathy) | Belum: `CLAUDE.md` subfolder masih template, belum mendaftar file yang ada |
| **Tetap berfungsi** | | |
| 8 | Simpan insight, bukan data mentah | Sebagian besar sudah. Pengecualian: salinan utuh [[Ekosistem JVTO]] (85 KB) |
| 9 | Perbarui harian lewat scheduled task (`/operator`) | Belum: Step 2 walkthrough; prompt [[Copilot Owner]] siap tapi belum dijadwalkan |
| 10 | Bersihkan mingguan (`/optimizer`, skor kesehatan 0-100) | Belum: Step 3 walkthrough |
| 11 | Dashboard di atas second brain (umum, per bagian, per masalah) | Belum. Briefing pagi adalah versi teksnya |
| 12 | Skill membaca file referensi dari second brain, bukan salinan | Belum: skill JVTO (mis. `jvto-audit`) belum menunjuk ke [[brand]] dan [[services]]; folder `Skills/` masih contoh LinkedIn/newsletter |
| **Lanjutan / tim** | | |
| 13 | Tetap update saat laptop tertutup: routine cloud + connector Baalda resmi di Claude (Customize, cari Baalda) | Belum tersambung |
| 14 | Sinkron satu brain untuk tim (Baalda Sync) | Belum perlu: vault dipakai owner sendiri |
| 15 | Rollout bertahap, satu pemilik | Sesuai: owner dulu, lalu [[Inant Kharisma]] dan [[David]] |
| 16 | Atur siapa bisa lihat/edit per folder/file | Wajib sebelum berbagi: vault berisi NPWP, profit, dan risiko keamanan |
| 17 | Dashboard tim per departemen | Nanti |
| 18 | Satu aturan "utamakan kepentingan bisnis" di AGENTS.md agar AI tidak sekadar setuju dengan penggunanya (institutional AI, a16z) | Belum ada |

## Catatan teknis
- Video menyarankan nama `AGENTS.md`. Claude Code memuat `CLAUDE.md` secara otomatis, jadi jangan sekadar mengganti nama. Cara aman: tetap `CLAUDE.md`, atau buat `AGENTS.md` lalu `CLAUDE.md` berisi `@AGENTS.md`. Jangan simpan dua isi berbeda.
- Video: aturan di Anthropic menyebut file ini dibaca pertama setiap sesi, idealnya kurang dari 200 baris.
