---
type: resource
status: active
department: Teknologi
tags: [sistem, website, repo]
updated: 2026-11-04
---
Website javavolcano-touroperator.com. Repo `jvto-devteam/jvto-web`, folder lokal `/Users/macbook/Code/jvto-web`. Next.js 16, hanya **renderer**: semua konten datang dari [[jvto-ekosistem]] ("web-renders-never-authors"), booking dan pembayaran dari [[legacy]]. Database hanya menyimpan login pelanggan (NextAuth: Google SSO + magic link). Desain di `DESIGN.md` (ringkasan di [[brand]]), positioning di `PRODUCT.md`.

## Arsitektur
```
jvto-ekosistem (penulis konten, VPS)
   -> /api/file, /api/website/page, /api/schema/page, /llms.txt
jvto-web (renderer)
   -> proxy server ke legacy (booking, invoice, Xendit, WA)
```

## Peta situs (sekitar 334 rute di sitemap)
| Kelompok | Rute |
|---|---|
| Tur | `/tours`, `/tours/from-bali/[slug]`, `/tours/from-surabaya/[slug]` (17 PDP) |
| Destinasi dan guide | `/destinations/[slug]`, `/travel-guide/[slug]` (11 guide + FAQ) |
| Verifikasi | `/verify-jvto`, `/verify-jvto/legal`, `/police-safety`, `/press-recognition`, `/history-artifacts` |
| Why JVTO | `/our-story`, `/the-jvto-difference`, `/community-standards`, `/our-team/[slug]`, `/reviews/[id]` (sekitar 228 permalink ulasan) |
| Policy | `/booking-payment-cancellation`, `/inclusions-exclusions`, `/privacy` |
| Pasar | `/markets/malaysia`, `/markets/singapore`, `/isic/student-package` |
| Transaksi | `/checkout`, `/my-booking`, `/my-booking/[slug]`, `/my-booking/[slug]/bank-transfer`, `/b/[code]` (short link `jvto.me/b/`) |
| Tool | `/3d/[slug]` (rute 3D Mapbox), `/entity` |

Tidak ada CMS lagi: editor Tiptap dihapus 2026-09-02, TinaCMS pensiun.

## API penting
Proxy checkout, bank transfer, pay-balance, update pickup/drop/kaos ke legacy; `check-isic`; feed `tours-feed`, `things-to-do-feed` (live 2026-10-31), `reviews-xml`; `indexnow`; `revalidate/ecosystem-content` (dipanggil CI ekosistem).

## Checkout
PDP (tanggal, pax, add-on "Enhance your trip") lalu `/checkout`. Aturan bayar ada di [[Alur Booking JVTO]]. Payload ke legacy: kontak, pilihan booking, kode ISIC, atribusi first-touch (gclid/gbraid/wbraid + utm). Data perjalanan (jemput, penerbangan, antar) diisi belakangan di portal. Verifikasi harga server masih mode log (`CHECKOUT_PRICE_ENFORCE`).

## SEO dan AI
JSON-LD dibangun di ekosistem dan disuntik server-side. Rating publik hanya Google Maps. `/llms.txt` diproksi dari ekosistem (187 baris), semua crawler AI diizinkan. Status gunung ditarik harian 07:00 WIB dari MAGMA/PVMBG.

## Deploy dan aturan tim
- Branch `live` = produksi dan default. **Push ke `live` = deploy produksi** (SSH, `npm ci`, `prisma generate`, build, pm2 restart). `main` dihapus 2026-08-28.
- CI hanya pada PR ke `live`. Tes pakai `node --test`.
- Jangan deploy bersamaan dengan ekosistem (satu VPS). Gabungkan commit, satu push.
- Guardrail: jangan menulis ulang copy, jangan menghapus rute lama, jangan menyentuh workflow atau menambah dependency tanpa izin. FAQ baru tidak boleh ditulis di web.
- Keputusan terkunci: tahun marketing 2015, TDUP 2023, tahun pendirian PT kosong, Madakaripura 200 m, dokumen SIP dokter tetap tayang.
- Keputusan 2026-09-02: jvto-web akan keluar dari VPS (belum dikerjakan).
- Infra: Cloudflare (Full Strict), Nginx, Next.js di PM2, PostgreSQL 17 + PgBouncer + pgBackRest.

## Status terbuka (STATUS.yaml, 2026-11-01)
Lihat [[Tour PDP v2]], [[Google Things to Do Feed]], dan daftar tugas [[David]]. Butuh owner: apakah login pelanggan masih dipakai (`PRISMA_AUTH_ONLY`), subdomain `mail.` yang masih menyajikan situs lama dan dipakai Google Ads AI Max, review keamanan legacy, SSH timeout VPS berulang, foto ulasan Google kedaluwarsa (403), klik "Validate Fix" review snippet di GSC.

Catatan: checkout lokal di Mac ini tertinggal 72 commit dari `origin/live` (cek 2026-11-04).
