---
type: resource
status: active
department: Teknologi
tags: [sistem, repo, source-of-truth]
updated: 2026-11-04
---
Repo `jvto-devteam/jvto-ekosistem`, folder lokal `/Users/macbook/Code/jvto-ekosistem`. Satu-satunya penulis fakta publik JVTO: [[jvto-web]] hanya menampilkan. Disajikan oleh `server.mjs` di ekosistem.javavolcano-touroperator.com (PM2): `/api/file`, `/api/website/page`, `/api/schema/page`, `/llms.txt`, `/health`. Legacy dan new-backoffice memberi tahu repo ini lewat `EcosystemSync::notify` saat booking berubah (sync booking dimatikan sejak 2026-10-14).

| Folder | Isi |
|---|---|
| 1-knowledge-and-evidence-core | Organisasi, orang dan crew, kredensial, klaim, kebijakan, destinasi, travel guide, FAQ |
| 2-product-and-commercial-core | 17 kontrak produk, harga, 115 add-on, deposit, kebijakan credit |
| 3-booking-and-journey-core | Timeline perjalanan tamu |
| 4-operations-core | Kendaraan, crew, hotel, screening, Plan B, status gunung, expense |
| 5-experience-engine | Output: website, JSON-LD, llms feed, template WA/email, partner feed, analytics |

## Render dan gerbang
- `render:web-content` lalu **wajib** `render:llms`. Output dirender ulang, jangan diedit tangan.
- File tulisan tangan: `organization.json`, `credentials.json`, `narrative-claims.json`, `destination-knowledge/*`, `*.product-contract.json`, `4-operations-core/**`, `trust-claims.json`.
- Gerbang: validate schema, tes, `check:single-home` (satu fakta satu rumah), `reconcile:facts`, `verify:live-parity`, `check:live-jsonld`, `verify:evidence-hashes`, `audit:website-live`. Tes baru harus ditambah manual ke rantai `&&` di `package.json`.

## Workflow
| Workflow | Jadwal | Fungsi |
|---|---|---|
| `deploy-vps.yml` | Push ke `main` | Deploy produksi + revalidasi sekitar 294 rute web + verify live parity |
| `sync-google-reviews.yml` | Harian 08:00 WIB | Tarik ulasan Google, render ulang schema dan llms.txt |
| `sync-booking-data.yml` | Dinonaktifkan 2026-10-14 | |
| `verify-evidence-hashes.yml` | Senin 09:00 WIB | Cek hash dokumen bukti |
| `graph-integrity-live.yml` | Senin 10:00 WIB | Checker dari repo privat `jvto-ops` |

## Aturan kerja
Baca `state/handoff-*.md` terbaru dulu; cari di `state/goals.json` dan git log sebelum bertanya; push ke `main` wajib approval owner; rajut semua bukti, jangan buang bukti. Istilah wajib: "Lifetime Package Credit". SE.1658 (pembukaan kawah) berbeda dari SE.35 (dasar surat sehat). Jangan publikasikan masa berlaku surat sehat.

Turunan di vault: [[Itinerary Paket]], [[Pengetahuan Destinasi]], [[Plan B dan Penutupan]].
