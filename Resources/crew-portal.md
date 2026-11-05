---
type: resource
status: active
department: Operasional
tags: [sistem, crew, repo]
updated: 2026-11-04
---
Aplikasi crew (PWA) di crew-portal.javavolcano-touroperator.com. Repo `github.com/davidsetyaahr/crew-portal`, folder lokal `/Users/macbook/Code/crew-portal`. Laravel + Inertia/React. Membaca database booking bersama dengan [[new-backoffice]] dan [[legacy]]. Commit terakhir 2026-09-04, penulis utama [[David]].

## Pengguna dan login
Guide/escort, guide Ijen, driver. Masuk dengan nomor HP + password (tabel `user_partners`), wajib ganti password pertama kali. Alternatif OTP 4 digit lewat WhatsApp (WatZap, bukan [[wa-inbox]]). Nomor tak terdaftar diminta menghubungi admin.

## Halaman
| Halaman | Isi |
|---|---|
| Home | Ringkasan bulan: trip escort dan Ijen, selesai/aktif/mendatang, 3 trip terdekat, label channel |
| Trips | Trip milik crew per bulan |
| All Trips | Semua booking `booked` bulan itu |
| Detail Trip | Tamu, booking, itinerary, hotel, plotting, dokumen (Trip Media, Crew Expense, Surat Tugas, Pickup Sign), Trip Photos, Refund, Additional & Claims |
| ID Card, Profile, Crew SOP | Identitas dan SOP crew |
| Map | Peta destinasi dari [[jvto-ekosistem]], di-cache agar tetap jalan saat database turun |
| Chat | Chat crew per booking ke crew-chat |

## Biaya tambahan dan refund
- **Additional & Claims:** `pending` (diminta, mis. oleh TWT) lalu `submitted` (crew isi harga, nota, foto) lalu `invoiced`. Tidak bisa diubah setelah `invoiced`.
- **Refund:** `refund_to` = office atau customer (customer wajib foto bukti). Hanya status `pending` yang bisa diubah.
- Tidak ada logika fee crew di repo ini.

## Trip Photos ke Instagram (fitur utama Agustus-September 2026)
Crew memilih foto per kategori dan submit sekali: Penjemputan (wajib), satu per destinasi (opsional), Medical Checkup (opsional, trip Ijen), Drop/Pemulangan (wajib). Maks 10 foto (batas carousel IG). Foto disimpan di host crew-portal, di-resize maks 1440 px. Caption ditulis DeepSeek (bahasa Inggris, suara operator, menyebut medical check pra-Ijen, tidak menyebut atraksi yang tutup). Satu baris masuk ke Google Sheet lewat Composio (antrean dengan kolom IG/FB). Poster harian (GitHub Actions di repo lain, jeda 4 hari) memposting carousel IG. **Trip TWT tidak diposting.** Panel sosmed-studio bisa menitip foto lewat `POST /api/trip-photos`.

## Catatan teknis terbuka
Chat crew belum memvalidasi hash booking; OTP tidak dihapus setelah dipakai; halaman vendor duplikat dengan [[jvto-partner]]; OTP masih lewat WatZap.
