---
type: resource
status: active
department: Teknologi
tags: [sistem, arsitektur, peta]
updated: 2026-11-04
---
Delapan sistem, satu database booking MySQL bersama (kecuali [[jvto-web]], [[jvto-ekosistem]] dan [[wa-inbox]]). Semua repo di GitHub `jvto-devteam` (crew-portal di akun `davidsetyaahr`) dan di `/Users/macbook/Code/`.

| Sistem | Alamat | Peran | Pengguna |
|---|---|---|---|
| [[jvto-web]] | javavolcano-touroperator.com | Website, checkout, customer portal | Tamu |
| [[legacy]] | legacy.javavolcano-touroperator.com | Booking, invoice, Xendit, email, pengingat WA, ekstraktor Klook, API crew | Sistem |
| [[new-backoffice]] | new-backoffice.javavolcano-touroperator.com | ERP: booking, plotting, expense, keuangan, pembaca PDF TWT | Admin, owner |
| [[crew-portal]] | crew-portal.javavolcano-touroperator.com | Jadwal dan detail trip, klaim, foto trip | Crew |
| [[jvto-partner]] | partner.javavolcano-touroperator.com | Portal vendor | Hotel, tiket Ijen, Bromo, garasi, kaos, dokter |
| [[Aplikasi TWT]] | twt.javavolcano-touroperator.com | Jadwal, hotel check, invoice TWT | TWT, JVTO |
| [[jvto-ekosistem]] | ekosistem.javavolcano-touroperator.com | Sumber kebenaran konten publik | Website, AI |
| [[wa-inbox]] | wa-inbox.javavolcano-touroperator.com | Inbox omnichannel, chatbot, gateway semua WA otomatis | Admin, CS |

## Alur besar
```
Tamu -> jvto-web -> legacy (booking + Xendit) -> database bersama
Klook email -> Apps Script -> legacy (DeepSeek) -> database bersama
TWT email+PDF -> Apps Script -> new-backoffice (DeepSeek) -> database bersama
database bersama -> new-backoffice (admin) / crew-portal (crew) / jvto-partner (vendor) / Aplikasi TWT
legacy + new-backoffice -> wa-inbox -> WhatsApp tamu, crew, hotel, vendor
legacy + new-backoffice -> EcosystemSync -> jvto-ekosistem -> jvto-web
```

## Layanan pihak ketiga
Xendit (invoice kartu, payout crew/vendor), DeepSeek (baca email Klook dan PDF TWT, caption IG, ringkasan WA), Google Apps Script + Gmail (label ekstraktor, email KlikBCA), Google Drive (Trip Media), Meta WhatsApp Cloud API, WatZap/wa-dashboard (WA tidak resmi), Ollama cloud (LLM chatbot), Composio + Google Sheet (antrean Instagram), Firebase (push crew), Google Ads (konversi offline), Typeform (consent), Cloudflare, VPS (Nginx, PM2).

## Lini lain
Health Screening Ijen (`health.mountijen.com`, `ijen-health.id`) berbagi akun Xendit dengan JVTO.

Proses bisnisnya: [[Alur Booking Baru]]. Referensi lengkap: [[Ekosistem JVTO]].
