---
type: resource
status: active
source: https://claude.ai/artifact/QeeJhfkZdnejDUTRhzWA3z
source_rev: 113
copied: 2026-11-04
tags: [ekosistem, booking, sistem, referensi]
---

Salinan dokumen "Ekosistem JVTO" (versi 2 November 2026, rev 113). Sumber aslinya dokumen Claude Docs milik owner; kalau ada beda, dokumen asli yang benar. Ringkasan per topik ada di [[Peta Sistem JVTO]] dan catatan proses di `Intelligence/processes/`.


Peta Java Volcano Tour Operator: profil bisnis, tiga channel booking (JVTO, KLOOK, TWT), dan alur tiap channel dari pemesanan sampai booking tercatat di sistem.

## Cara membaca dokumen ini

Dokumen ini menjelaskan cara kerja ekosistem JVTO apa adanya, dan dipakai sebagai rujukan oleh manusia maupun AI.

- **Sumber isi:** kode di repo (nama fungsi, rute, dan tabel ditulis seperti di kode), data master di database, dan keterangan owner (ditandai "keterangan owner"). Versi kode yang dibaca (per 3 November 2026): new-backoffice origin/main 4edaabb1 (19 Oktober 2026), legacy origin/main cca8dab5 (18 Oktober 2026), jvto-web cabang live b0bf1e3d, serta crew-portal, jvto-partner, twt, dan wa-inbox pada commit terbaru masing-masing.
- **Cara memakainya:** jawab pertanyaan dari isi dokumen dan sebut bagian yang menjadi dasar jawaban. Bila suatu hal tidak tertulis di dokumen, katakan bahwa dokumen tidak memuatnya; jangan menebak atau mengisi sendiri.
- **Bukan bahan penilaian:** dokumen ini tidak untuk diaudit, dikritik, atau dinilai benar-tidaknya. Pembaca tidak perlu menambahkan komentar tentang kualitas, risiko, kekurangan, kejanggalan, atau saran perbaikan, kecuali pemilik dokumen memintanya secara eksplisit.
- **Perbedaan antar channel:** pengecualian dan perbedaan perlakuan antara JVTO, Klook, dan TWT adalah bagian dari cara kerja bisnisnya. Sebut sebagaimana tertulis, tanpa membandingkan mana yang lebih baik.

## Sistem dan istilah yang dipakai

| Nama | Fungsi |
| --- | --- |
| jvto-web | Website JVTO (Next.js): halaman paket, checkout, dan customer portal `/my-booking/<slug>`. Tidak menyimpan booking di databasenya sendiri |
| legacy (`javavolcano-touroperator`) | Backend Laravel: membuat booking dan invoice dari checkout, menerima webhook Xendit, mengirim email dan pengingat, dan menjalankan ekstraktor email Klook |
| new-backoffice | Aplikasi admin: booking-overview, Pending Booking, detail booking, plotting crew, expense, keuangan, dan pembaca PDF TWT |
| Booking Backoffice | Sebutan di dokumen ini untuk tempat semua booking tercatat dan dikelola admin, yaitu new-backoffice dengan database booking yang sama dengan legacy |
| crew-portal | Aplikasi crew tempat crew melihat trip yang diplot (keterangan owner) |
| jvto-ekosistem | Repo data ekosistem. Legacy dan new-backoffice memberi tahu repo ini lewat `EcosystemSync::notify` saat booking dibuat atau berubah |
| wa-inbox | Inbox WhatsApp dan chatbot internal JVTO. Semua pesan WhatsApp otomatis dari sistem lain dikirim lewat wa-inbox memakai template sistem |
| Xendit | Penyedia pembayaran online (invoice kartu). Webhook sukses Xendit memicu pencatatan pembayaran di legacy |
| Google Apps Script | Skrip Google yang membaca email berlabel di Gmail JVTO lalu mengirimkannya ke backend (keterangan owner) |
| DeepSeek | Model bahasa (`deepseek-chat`) yang mengubah isi email Klook dan PDF TWT menjadi data terstruktur |
| OTA | Online travel agent, platform pemesanan online seperti Klook |
| DMC | Destination Management Company, perusahaan yang menyiapkan perjalanan di tujuan |
| Customer portal | Halaman booking milik tamu di `/my-booking/<slug>`, dibuka lewat tautan di pesan WhatsApp |
| jvto-partner | Aplikasi portal vendor (Laravel dan Inertia/React): halaman hotel, Ijen, Bromo, garasi, kaos, dan medical check up. Dibuka dari menu Portal Vendor di new-backoffice |
| twt | Aplikasi TWT (Laravel dan Inertia/React): jadwal booking TWT, hotel check, penagihan, dan pembayaran |

### Repo dan alamat

Setiap sistem di atas adalah satu repo di GitHub dan satu folder di `/Users/macbook/Code`. Alamat produksi diambil dari kode.

| Sistem | Repo GitHub | Folder lokal | Alamat produksi |
| --- | --- | --- | --- |
| jvto-web | https://github.com/jvto-devteam/jvto-web | `jvto-web` | `javavolcano-touroperator.com` |
| legacy | https://github.com/jvto-devteam/javavolcano-touroperator | `javavolcano-touroperator` | `legacy.javavolcano-touroperator.com` |
| new-backoffice | https://github.com/jvto-devteam/new-backoffice | `new-backoffice` | `new-backoffice.javavolcano-touroperator.com` |
| crew-portal | https://github.com/davidsetyaahr/crew-portal | `crew-portal` | `crew-portal.javavolcano-touroperator.com` |
| jvto-partner (portal vendor) | https://github.com/jvto-devteam/hotel (nama repo di GitHub: hotel) | `jvto-partner` | `partner.javavolcano-touroperator.com` |
| twt | https://github.com/jvto-devteam/twt | `twt` | `twt.javavolcano-touroperator.com` |
| jvto-ekosistem | https://github.com/jvto-devteam/jvto-ekosistem | `jvto-ekosistem` |  |
| wa-inbox | https://github.com/jvto-devteam/wa-inbox | `wa-inbox` |  |

## Profil bisnis dan channel

JVTO adalah DMC (Destination Management Company) inbound: perusahaan yang menyiapkan dan menjalankan perjalanan wisatawan di tujuan. Karena itu tamu JVTO tidak hanya datang dari website JVTO. Ada tiga channel utama, dan di tiap channel pembagian tugas serta arus uangnya berbeda.

### Perusahaan

- Nama usaha: Java Volcano Tour Operator (JVTO)
- Pendiri: SAM
- Badan hukum: PT Java Volcano Rendezvous (PT JVR)
- Bentuk usaha: DMC, inbound tour operator

### Tiga channel utama

Semua booking dari semua channel, termasuk TWT, tercatat di Booking Backoffice.

|  | JVTO | KLOOK | TWT (The Window Travel) |
| --- | --- | --- | --- |
| Siapa mereka | Website JVTO sendiri | OTA (platform pemesanan online) tempat JVTO memasang listing | Rekanan tur di Malaysia, dipimpin SEAN |
| Paket | Paket JVTO | Paket khusus Klook: package ID 82, 83, 84, dan 94 (baru) | Paket milik TWT |
| Harga | Diatur JVTO | Diatur JVTO | Tidak diatur JVTO, karena produknya milik TWT. JVTO menerima komisi dari setiap booking TWT |
| Tamu memesan di | Website JVTO | Klook | TWT; TWT lalu menginfokan booking yang sudah fix ke JVTO (tanggal trip, dll.) |
| Yang dikenal tamu | JVTO | Klook. Tamu tidak tahu siapa JVTO saat memesan | TWT |
| Komunikasi dengan tamu | JVTO | Setelah booking, tamu berkomunikasi langsung dengan JVTO | Dipegang penuh oleh TWT |
| Yang menyiapkan operasional | JVTO | JVTO | JVTO sebagai ground tour operator: tiket, kendaraan, hotel, dan lainnya |
| Cara JVTO menerima uang | Pembayaran dari tamu | Klook membayar JVTO sebulan sekali, setelah trip terlaksana | TWT membayar di muka: di akhir bulan dihitung jumlah trip bulan berikutnya, dan itu yang dibayarkan. Trip tengah bulan atau trip dadakan dibayar lebih dulu atau menunggu sesudahnya, tergantung kondisi |
| Aturan pembayaran dan kredit paket | Berlaku | Mengikuti aturan Klook | Mengikuti aturan TWT |
| Bot dan chat wa-inbox | Direncanakan dipakai | Direncanakan dipakai | Tidak dipakai, karena JVTO tidak punya akses ke tamu TWT |

Urutan pembayaran per channel:

1. **JVTO:** tamu bayar, trip jalan.
2. **KLOOK:** tamu bayar ke Klook, trip jalan, Klook membayar JVTO bulan berikutnya. Contoh: 20 trip terlaksana di satu bulan, dibayar Klook di bulan sesudahnya.
3. **TWT:** TWT membayar di muka untuk trip bulan berikutnya, lalu trip jalan.

Singkatnya, KLOOK membayar setelah trip, TWT membayar sebelum trip.

### Channel lain

Get Your Guide, Viator, dan trip.com juga channel JVTO, tetapi jarang menghasilkan order. Booking juga bisa datang lewat hotel: tamu yang menginap menanyakan tour operator (mis. ingin ke Ijen), lalu hotel merekomendasikan JVTO. Booking dari channel lain diproses manual oleh admin, yang memasukkannya sendiri ke Booking Backoffice.

### Dampaknya pada sistem dan aturan

- JVTO hanya mengontrol platformnya sendiri. Karena itu aturan pembayaran dan kredit paket hanya berlaku di channel JVTO; channel lain mengikuti aturan masing-masing.
- Siapa yang bicara dengan tamu berbeda per channel. Tamu TWT dihubungi TWT, bukan JVTO.
- Harga paket JVTO dan KLOOK diatur JVTO. Harga paket TWT tidak, dan JVTO hanya menerima komisinya.
- Angka booking, pendapatan, atau tunggakan perlu disebut menurut channel-nya. Waktu uang masuk berbeda: sesudah trip (KLOOK), sebelum trip (TWT), atau dari tamu (JVTO).
- Bot dan chat wa-inbox direncanakan untuk semua tamu kecuali tamu TWT.

Penanda channel di data: di repo jvto-ekosistem tiap pembayaran membawa `orderChannel` bernilai JVTO, KLOOK, atau TWT (`payments/payment-summary.json`), dan package ID 82, 83, 84 ditandai sebagai paket KLOOK di `booking/product-reconciliation.json`. Di database booking, website JVTO dan Klook sama-sama memakai `agent_id = 2` (Klook dibedakan oleh `booking_category_id = 3`), sedangkan TWT memakai `agent_id = 1`.

### Ringkasan: dari pesanan sampai tercatat di sistem

|  | JVTO | Klook | TWT |
| --- | --- | --- | --- |
| Pemicu | Tamu checkout di website JVTO | Email konfirmasi dari Klook | Email dari TWT dengan PDF booking |
| Jalur masuk | jvto-web, lalu legacy (`CheckoutController::checkout`) | Gmail label "KLOOK EKSTRAKTOR", Apps Script, legacy (`KlookEmailExtractorController::webhook`), DeepSeek membaca email | Gmail label "TWT EKSTRAKTOR", Apps Script, new-backoffice (`BookingController::twtExtractor`), DeepSeek membaca PDF |
| Status awal | `pending wise`, menjadi `booked` saat pembayaran pertama masuk | `booked` langsung | `booked` langsung |
| Penanda di data | `agent_id = 2`, kategori bukan 3 | `agent_id = 2`, `booking_category_id = 3` | `agent_id = 1` |
| Pembayaran tamu | DP atau penuh lewat Xendit atau transfer; pelunasan lewat portal; admin dapat mencatat manual | Dibayar tamu ke Klook; `dp` dan `payment` bernilai 0 | Dipegang TWT; `dp` dan `payment` bernilai 0 |
| Pesan ke tamu | WhatsApp dan email saat checkout dan tiap pembayaran | Satu WhatsApp konfirmasi dengan tautan portal | Tidak ada |
| Expense otomatis | Ya, saat pembayaran pertama diproses | Ya | Tidak; rincian biaya ada di PDF |
| Tampil di booking-overview | Ya | Ya | Ya |
| Perubahan dan pembatalan | Admin di backoffice | Admin di backoffice | Admin di backoffice |

Rincian tiap kolom ada di bagian-bagian berikut.

## Alur booking channel JVTO

Booking dari website JVTO lahir di jvto-web, dibayar dan diproses di legacy (javavolcano-touroperator), lalu dioperasikan di new-backoffice. Legacy dan new-backoffice memakai satu database MySQL yang sama, jadi booking yang dibuat di satu sistem langsung terbaca di yang lain. Untuk aksi tertentu new-backoffice juga memanggil endpoint legacy (mengirim receipt, membuat tautan bayar Xendit, dan menyetujui pembayaran di Pending Booking). jvto-web tidak menyimpan booking di databasenya sendiri.

Sumber: aturan pembayaran dari kode jvto-web (commit 2ceef8b0, 31 Oktober 2026); langkah lainnya dari dokumen "Alur Booking JVTO" (diverifikasi ke kode pada 28 September 2026).

### Aturan pembayaran

JVTO dimulai dengan DP 20%, lalu pelunasan. Aturannya ditentukan dari jumlah hari antara hari pemesanan dan tanggal trip:

| Jarak ke tanggal trip | Yang dibayar saat booking | Cara bayar |
| --- | --- | --- |
| 14 hari atau lebih | DP 20% dari total | Xendit (kartu) |
| 6 sampai 13 hari | Pembayaran penuh 100% | Xendit (kartu) |
| Kurang dari 6 hari | Pembayaran penuh 100% | Transfer bank manual |

- Aturan ini ada di `src/lib/checkout/pricing.ts` (`downPaymentForDays`, `paymentMethodForDays`) dan dipakai oleh halaman checkout. Saat booking masuk, server jvto-web memeriksa kesesuaian DP dan cara bayar dengan aturan yang sama (`verifyCheckoutPricing`).
- Bila DP 20% dibayar, sisanya dilunasi lewat invoice pelunasan. Sistem mengirim pengingat pelunasan tepat 7 hari sebelum trip (lihat langkah Pelunasan).
- Aturan yang dipakai di checkout adalah batas 14 hari seperti pada tabel di atas.

### Langkah demi langkah

1. **Tamu mengisi checkout di jvto-web.** `TourDetail.tsx` menyimpan data pilihan ke `localStorage`, lalu membuka `/checkout?pid=`. Halaman `CheckoutInner.tsx` menghitung DP dan cara bayar menurut aturan di atas. Data baru ditulis ke database pada langkah berikutnya.
2. **jvto-web meneruskan ke legacy.** Tombol bayar memanggil `/api/checkout` di jvto-web, yang memeriksa harga terhadap data ekosistem lalu meneruskan ke `${NEXT_PUBLIC_LEGACY_URL}/checkout`. Pada file .env jvto-web di mesin lokal (file itu tidak ada di repo), nilai variabel itu berakhir di `/api/web`, sehingga tujuannya `POST /api/web/checkout` di legacy (`Api\Web\CheckoutController::checkout`). Legacy hanya menerima kiriman yang membawa header `X-Checkout-Token` sama dengan `WEB_CHECKOUT_TOKEN`-nya (token itu dikirim jvto-web dari variabel `LEGACY_CHECKOUT_TOKEN`); tanpa token yang cocok jawabannya 403 dan dicatat di log. Bila `WEB_CHECKOUT_TOKEN` kosong di legacy, pemeriksaan itu tidak berjalan. Rute `POST /api/web/checkout/bank-transfer` dilindungi dengan cara yang sama. Isi kiriman: `contactDetails` (nama, email, telepon, catatan); `bookingSelection` (package\_id, travel\_date\_start, numb\_of\_pax, payment\_method `cc` atau `bank`, down\_payment, total\_package, total\_addons, grand\_total, total\_discount, addons); `isic_codes`; `travelerDetails`.
3. **Legacy membuat booking dan invoice.**
   - Menulis `users` (dicari lewat email, lalu telepon; dibuat baru bila belum ada), `bookings`, `booking_details`, `book_add_ons`, `user_logs`; kode ISIC di `discounts` ditandai terpakai.
   - Booking: kode `JVR/nnn/mm/yy`, `agent_id = 2`, status `pending wise`, `payment_method` `cc` (kartu) atau `pay later` (transfer bank), batas bayar `due_date` 1 hari, `dp` sesuai kiriman.
   - `url_short` (jvto.me/b/) dibuat otomatis oleh hook `Booking::saving`.
   - Invoice Xendit hanya untuk kartu: berlaku 24 jam, nominal (dp + 2220) / 0,96781, lalu diarahkan ke `/my-booking/{url}`. Untuk transfer bank, tautan bayar adalah `/my-booking/{url}/bank-transfer`.
   - Mengirim WA `booking_pending_payment` ke tamu lewat wa-inbox, dan email `NewReservationMail` ke tamu dengan salinan ke Gmail admin.
   - jvto-web lalu mengarahkan tamu ke tautan bayar.
4. **Xendit mengabari pembayaran DP.** `POST /third-party/xendit/webhook/invoice/success` di `XenditController::invoiceSuccess` (cabang DP: status belum `booked` dan payment kosong). Di awal fungsi, XenditWebhookVerifier memeriksa pemanggil. Pemanggil yang sah ada dua: new-backoffice (header X-Backoffice-Token sama dengan BACKOFFICE\_APPROVE\_TOKEN, dipakai untuk Approve Pending Booking) atau Xendit (x-callback-token sama dengan XENDIT\_CALLBACK\_TOKEN, payload memuat id invoice, dan lookup GET /v2/invoices/\<id> ke Xendit menjawab PAID atau SETTLED dengan external\_id yang sama). Mode ditentukan XENDIT\_WEBHOOK\_ENFORCE, bawaannya false: pemanggil yang tidak terverifikasi hanya dicatat di log dan diproses seperti biasa. Bila true, pemanggil yang tidak terverifikasi ditolak dengan 401 (atau 503 bila hanya lookup ke Xendit yang gagal, supaya Xendit mengulang), dan payment\_method\_id dari webhook Xendit dibuang karena hanya new-backoffice yang menentukan metode bayar.
   - Membuat ulang `book_crew_activities`, `book_car_activities`, `book_room_hotels`, `book_hotel_meals`, `book_destination_activities`, `book_others_activities`, `book_hotels`, `booking_itineraries` dari template paket.
   - Mengubah booking jadi `booked`: `payment` = DP, `balance` = grand total + add-on dikurangi DP.
   - Menulis `booking_payments` (metode 3 "Xendit", deskripsi "Down Payment" atau "(Full Payment)") dan menjalankan `generateExpense` (baris expense di tabel `book_*`, status `unpaid`, beberapa langsung `is_debt = 1`).
   - Mengirim email `ConfirmedMail` ke tamu dan Gmail perusahaan; WA `hotel_room_reservation` ke grup tiap hotel; `internal_new_booking` ke nomor internal; `payment_received_first` ke tamu dan grup internal; `internal_bali_transport` bila trip dari Bali atau ada add-on transport; lalu `EcosystemSync::notify` (GitHub `repository_dispatch` "booking-changed").
5. **Booking muncul di new-backoffice.** `/booking-overview` (`ScheduleController::index`) membaca tabel yang sama. Tampilan bawaan hanya status `booked` pada bulan berjalan menurut `travel_date_start`. Booking transfer bank (`pending wise`) ada di `/pending-booking`. Pembayaran tampil sebagai `financial.payment`, `financial.balance`, dan `paymentHistory`; new-backoffice hanya menampilkan `booking_payments` dengan `is_paid = 1`.
6. **Admin mengelola booking.** Dari menu di booking-overview: edit booking (`BookingController::edit` dan `update`), catat pembayaran manual (`storePayment`, menulis `booking_payments`, `bookings.payment`, `bookings.balance`), buka invoice PDF (dibuat legacy), dan unggah dokumen (`booking_documents`).
7. **Plotting crew.** `POST /plotting` (`ScheduleController::plotting`) menghapus lalu membuat ulang `book_cars` dan `book_guide_drivers` booking itu dalam satu transaksi (guide\_id, type driver atau guide, guide\_ijen, start\_date, end\_date). Data dropdown diambil dari legacy `/backoffice/plotting/get-plotting`.

### Pelunasan

1. **Pengingat H-7.** Command legacy `my:reminder-payment` dijadwalkan tiap menit di `Kernel.php`, dan hanya bekerja pukul 15.00 WIB. Sasarannya: status `booked`, `agent_id = 2`, bukan kategori Klook (`booking_category_id != 3`), `wa_status_reminder_payment = 0`, dan tepat 7 hari sebelum `travel_date_start`.
   - Membuat invoice Xendit baru (deskripsi `#Outstanding Payment`, hanya kartu kredit, berlaku 48 jam, plus fee), disimpan di `bookings.outstanding_payment_link`, `outstanding_payment_amount`, `outstanding_payment_fees`.
   - Mengirim WA `payment_reminder_balance` ke tamu, sekali saja. Bila `balance = 0`, booking hanya ditandai.
2. **Jalur lain membuat invoice pelunasan:** tamu sendiri lewat `/booking/set-payment-method/{url}` atau `api/generate-xendit/{bookingUrl}` (`CheckoutController::setPaymentMethod`), dipanggil dari `api/booking/pay-balance` di jvto-web. Pilihan cash cukup menyimpan `outstanding_payment_method = cash`.
3. **Xendit mengabari pelunasan.** Handler webhook yang sama dengan DP, dengan syarat: booking sudah `booked`, deskripsi memuat `#Outstanding Payment`, dan `balance > 0`.
   - `payment` bertambah sebesar `outstanding_payment_amount`, `balance` berkurang sebesar itu.
   - Menulis `booking_payments` "Payment (Paid in Full)", dipecah jadi baris "Payment Add On" (`is_add_on = 1`) bila ada add-on.
   - Mengirim email receipt (`ConfirmedMail` dengan `isPartial`), WA `payment_received_balance` ke tamu saja, dan `EcosystemSync::notify`.
4. Pembayaran manual oleh admin dicatat di langkah 6.

### Status kunci

| Field | Nilai | Arti |
| --- | --- | --- |
| `bookings.status` | `pending wise` | Booking baru, belum bayar |
| `bookings.status` | `booked` | Pembayaran pertama (DP atau penuh) masuk; tetap `booked` setelah lunas |
| `bookings.payment`, `balance` | angka | Total dibayar, sisa tagihan. Lunas ditandai `balance = 0` |
| `bookings.outstanding_payment_*` | link, amount, fees, method | Invoice pelunasan terakhir |
| `bookings.wa_status_reminder_payment` | `0` atau terisi | Pengingat H-7 sudah terkirim atau belum |
| `booking_payments.is_paid` | `0` atau `1` | new-backoffice hanya menampilkan `1` |

## Paket JVTO di Klook

Halaman Klook adalah rujukan resmi untuk paket JVTO di channel KLOOK: jawaban chatbot tidak boleh menyimpang darinya. Sumber: [Klook activity 99790](https://www.klook.com/en-US/activity/99790-3-day-mount-bromo-sunrise-ijen-madakaripura-trekking-trip-surabaya/), dibaca lewat browser pada 2 November 2026. Judul di halaman: "Multiday Tour Mount Bromo Sunrise, Ijen Crater from Surabaya". Rating 4,9 dari 5 (69 ulasan), 800+ booked. Badge: Klook's choice, Private group, Hotel pick up. Bahasa: Chinese, English, Bahasa Indonesia. Berangkat dari Surabaya.

### Tiga jenis paket dan dua jenis tarif

- **Jenis paket:** (1) 3D2N Mount Bromo & Ijen Crater Tour; (2) 4D3N Mount Bromo, Ijen Crater & Surabaya Night Market; (3) 4D3N Ijen Crater, Papuma Beach, Tumpak Sewu Waterfall & Mount Bromo.
- **Jenis tarif (fare type):** International ID Card Holder dan Indonesian ID Card Holder. Pembagian jumlah peserta (tier) berbeda antar tarif, seperti di tabel harga.

### Harga (USD per orang, tanggal 4 November 2026)

Harga berubah menurut tanggal, tarif, dan promo. Angka di bawah adalah harga yang tampil di halaman untuk tanggal 4 November 2026, dibaca pada 2 November 2026.

**Indonesian ID Card Holder**

| Paket | Group 4-5 | Group 3 | Group 2 | Solo |
| --- | --- | --- | --- | --- |
| 3D2N | 142,19 (Group of 5); 155,75 (Group of 4) | 167,59 | 221,75 | 390,19 |
| 4D3N Night Market | 237,00 | 253,95 | 287,79 | 541,75 |
| 4D3N Ijen, Papuma, Tumpak Sewu, Bromo | 245,49 | 262,39 | 296,25 | 550,19 |

Catatan: untuk 3D2N, Group of 5 dan Group of 4 punya harga sendiri-sendiri. Untuk dua paket 4D3N, Group of 4-5 satu harga.

**International ID Card Holder** (harga diskon, tertulis "Was" di halaman; hitung mundur "Discount ends in 6 day(s)" saat dibaca)

| Jumlah | 3D2N | 4D3N Night Market | 4D3N Ijen, Papuma, Tumpak Sewu, Bromo |
| --- | --- | --- | --- |
| 8-20 orang | 161,05 (was 169,49) | 194,55 (was 204,75) | 194,55 (was 204,75) |
| 6-7 orang | 168,89 (was 177,75) | 223,15 (was 234,89) | 241,25 (was 253,95) |
| 5 orang | 180,95 (was 190,45) | 241,25 (was 253,95) | 241,25 (was 253,95) |
| 3-4 orang | 194,55 (was 204,75) | 259,35 (was 272,99) | 259,35 (was 272,99) |
| 2 orang | 233,69 (was 245,99) | 282,75 (was 297,59) | 282,75 (was 297,59) |
| Solo | 411,59 (was 433,25) | 503,20 (was 529,69) | 503,20 (was 529,69) |

Ketersediaan 3D2N, November 2026: Indonesian tersedia mulai 3 November; International tersedia mulai 4 November. Kalender 3D2N International menampilkan US$161,1 per hari dari 4 sampai 30 November.

### Itinerary 3D2N

1. **Hari 1.** 08:00 jemput, naik mobil. Makan siang atas biaya sendiri di Gubug Makan Mang Engking (perkiraan IDR 50.000-200.000 per orang). 17:00 check-in Baratha Hotel & Resto; 17:30 cek kesehatan untuk Ijen di hotel (IDR 35.000 per orang), surat sehat wajib untuk trekking Ijen. 18:00 makan malam tidak termasuk.
2. **Hari 2.** 00:00 naik mobil 2 jam. 02:00 trekking Ijen dari Paltuding, sekitar 6 jam, termasuk blue flame bila cuaca mengizinkan dan danau kawah; bila api biru tidak terlihat, menikmati danau kawah dan sunrise. 08:00 sarapan dan rehat kopi di Avrila Ijen Cafe, Paltuding. Makan siang tidak termasuk (IDR 50.000-150.000). 17:00 check-in Joglo Kecombrang Bromo (resort bintang 3, kamar mandi pribadi). Makan malam tidak termasuk (IDR 50.000-200.000, Bromo Escapes).
3. **Hari 3.** 02:00 naik mobil 1 jam 30 menit. 03:30 tur Bromo sekitar 5 jam 30 menit (sunrise dan kawah). 09:00 sarapan termasuk, lalu check-out. 12:00 naik mobil 45 menit. 13:00 air terjun Madakaripura, tur berpemandu. 16:00 naik mobil 2 jam. 18:00 kembali ke Surabaya.

### Itinerary 4D3N Night Market

Beda dengan 3D2N: makan malam hari 1 termasuk di Baratha Hotel & Resto; makan malam hari 2 tidak termasuk (IDR 50.000-150.000). Hari 3 sama sampai Madakaripura, lalu 16:00 naik mobil 2 jam dan 18:00 check-in Holiday Inn Express Surabaya. Hari 4: 09:00 sarapan termasuk dan check-out, 11:00 selesai.

### Itinerary 4D3N Ijen, Papuma, Tumpak Sewu, Bromo

1. **Hari 1.** Sama dengan 4D3N Night Market (makan malam termasuk di Baratha).
2. **Hari 2.** 00:00 naik mobil 2 jam, 02:00 trekking Ijen sekitar 6 jam, 08:00 sarapan di Avrila Ijen Cafe. 13:00 makan siang tidak termasuk (IDR 50.000-150.000). 16:00 Papuma Beach (sunset). 18:00 makan malam tidak termasuk (IDR 50.000-150.000). 20:00 check-in Doho Homestay, Jember.
3. **Hari 3.** 03:30 naik mobil 3 jam. 06:30 sarapan. 07:00 Tumpak Sewu, tur berpemandu. 12:00 makan siang tidak termasuk (IDR 50.000-100.000). 17:00 check-in Joglo Kecombrang Bromo. 18:00 makan malam tidak termasuk (IDR 50.000-150.000).
4. **Hari 4.** 02:00 naik mobil 1 jam 30 menit. 03:30 tur Bromo sekitar 5 jam 30 menit. 12:00 naik mobil 3 jam. 15:00 selesai (kembali).

Semua itinerary memuat catatan: jadwal bisa berubah karena lalu lintas dan cuaca.

### Termasuk dan tidak termasuk

- **Termasuk (daftar di halaman):** tiket masuk Mount Ijen, Mount Bromo, dan Madakaripura Waterfall; pemandu profesional berbahasa English, Bahasa Indonesia, atau Malay; sarapan; transport; biaya tol, parkir, jasa tiket, dan bahan bakar.
- **Tidak termasuk:** pengeluaran pribadi lain; tip.
- **Highlights:** bagian Highlights di halaman menyebut paket "all-inclusive ... with accommodation, entrance fees, travel insurance". Daftar termasuk di atas berlaku untuk semua paket; makan malam termasuk tertulis di itinerary 4D3N.
- **Biaya pihak ketiga:** cek kesehatan Ijen IDR 35.000 per orang, di hotel Bondowoso malam sebelum trekking.

### Jemput, syarat, dan aturan

- **Jemput:** 08:00-15:00 di seluruh Surabaya dan Sidoarjo: bandara, stasiun, atau hotel. Nama dan alamat hotel diisi saat checkout; operator mengonfirmasi ulang jam jemput.
- **Kembali (3D2N):** 18:00 ke hotel atau alamat pribadi, kota Surabaya (sekitar 2 jam). Halaman menyarankan tiket pesawat atau kereta sesudah pukul 19:00. 4D3N Ijen-Papuma-Tumpak Sewu selesai sekitar 15:00 hari ke-4; 4D3N Night Market 11:00 hari ke-4.
- **Usia:** minimal 10 tahun dan di bawah 70 tahun. Bayi dan anak masuk hitungan peserta. Tidak ada child seat.
- **Tidak dianjurkan:** mobilitas terbatas, ibu hamil, tekanan darah tinggi atau epilepsi. Tidak cocok untuk kursi roda atau stroller.
- **Makanan:** halal dan kebutuhan diet khusus (alergi, gluten, vegetarian) bisa diakomodasi. Hewan peliharaan dilarang.
- **Bawa:** ganti baju, sunscreen, kacamata hitam, topi, jas hujan atau ponco, uang tunai, senter atau headlamp, ID yang valid.
- **Ijen:** tutup setiap Jumat pertama setiap bulan (Ijen Rijik); itinerary disesuaikan agar Ijen tetap dikunjungi di hari lain. Blue fire tidak dijamin: bergantung kebijakan otoritas, cuaca, dan kadar gas belerang.

### Pembatalan dan perubahan

- **Pembatalan penuh:** refund penuh jika dibatalkan minimal 48 jam sebelum tanggal kegiatan (waktu lokal tujuan). Panel Package details di dua paket 4D3N menampilkan "Free cancellation (24 hours notice)", dan paket 3D2N menampilkan 48 jam. Teks Terms & Conditions di halaman menyebut 48 jam.
- **Cuaca ekstrem:** operator berhak membatalkan tur; tamu boleh reschedule.
- **Tidak ada refund:** voucher sudah ditukar atau kedaluwarsa; sudah mengikuti satu aktivitas atau memakai satu tiket; tiket hilang, dicuri, atau rusak; peserta terlambat atau tidak datang. Refund penuh hanya untuk booking yang gagal atau ditolak.
- **Perubahan booking:** boleh sampai 48 jam sebelum kegiatan; tidak bisa jika voucher belum terbit.
- **Voucher:** berlaku 4 hari sejak tanggal partisipasi, sampai 23:59 hari terakhir. Konfirmasi dalam hitungan menit; ada "Book now for tomorrow".

## Alur booking Klook: dari email ke backoffice

Booking Klook masuk lewat email konfirmasi. Klook mengirim email ke email JVTO, lalu sebuah skrip dan backend legacy mengubah email itu menjadi booking di Booking Backoffice. Alur ini dibaca dari kode `javavolcano-touroperator` (commit terakhir yang menyentuh controller: a9f8259f, 25 September 2026) dan dari keterangan owner untuk bagian Gmail dan Apps Script.

### Alur langkah demi langkah

1. **Klook mengirim email konfirmasi** ke Gmail JVTO saat ada booking (keterangan owner).
2. **Filter Gmail** memberi label **KLOOK EKSTRAKTOR** pada pesan baru dari Klook yang bernada "confirmed" (keterangan owner).
3. **Google Apps Script** membaca label itu, membuka pesan, membaca isinya, lalu mengirimkannya ke backend legacy (keterangan owner). Skrip ini berjalan di Google Apps Script.
4. **Backend legacy** menerima `POST /third-party/klook-extractor/webhook` di `KlookEmailExtractorController::webhook` (`routes/web.php:197-198`).
5. **DeepSeek** (model `deepseek-chat`) membaca teks email dan mengembalikan data terstruktur. Parsing dilakukan oleh LLM.
6. **Legacy membuat** user baru, booking, detail booking, itinerary, hotel, kamar, dan biaya (expense).
7. **Sesudahnya:** WA konfirmasi ke tamu, WA reservasi ke grup tiap hotel, dan pemberitahuan ke repo `jvto-ekosistem` (EcosystemSync).

### Data yang diekstrak dari email

| Field | Isi |
| --- | --- |
| package\_id | Paket JVTO (lihat pemetaan di bawah) |
| booking\_reference\_id | Kode booking Klook, mis. EZQ625258 |
| date\_request, end\_date, package\_duration | Tanggal dan lama trip |
| lead\_participant, country | Nama tamu utama tanpa gelar, negara |
| lead\_person\_email, lead\_person\_mobile | Kontak (nomor dengan kode negara) |
| participant | Jumlah peserta (angka pertama saja) |
| pick\_up, drop\_off | Titik jemput dan antar |
| arrival\_date, arrival\_time, flight\_number | Info kedatangan |

**Pemetaan paket** (ditulis di teks prompt ke LLM):

| package\_id | Paket Klook |
| --- | --- |
| 82 | 3D2N |
| 83 | 4D3N dengan Surabaya Night Market |
| 84 | 4D3N tanpa Surabaya Night Market (Ijen, Papuma, Tumpak Sewu, Bromo) |
| 94 | Bromo Sunrise Private Tour with Photographer from Surabaya |

Paket 94 adalah paket di luar tiga paket pada halaman Klook 99790 yang dibahas di atas.

### Apa yang dibuat di database

- **users:** satu user baru dibuat untuk setiap booking.
- **bookings:** `agent_id = 2`, `booking_category_id = 3` (kanal Klook), `type = offline`, `status = booked`, `payment_method = pay later`. Kode Klook disimpan di `invoice_code_origin`. Kode invoice JVTO berformat `JVR/NNN/MM/YY`, nomor urut dihitung dari booking `agent_id = 2` pada bulan trip. `dp = 0`, `payment = 0`, `balance` = total harga. Harga = harga paket (price plan 2, tier menurut jumlah pax) dikali pax; 0 bila harga tidak ditemukan.
- **Turunan:** `booking_details`, `booking_itineraries`, `book_hotels`, `book_room_hotels`, lalu lewat `generateExpense`: `book_hotel_meals`, `book_destination_activities`, `book_others_activities`, `book_car_activities`, `book_crew_activities`. Total expense dihitung dari rumus di kolom `formula` yang dijalankan dengan `eval()`.
- **Jadwal WA otomatis** di booking: informasi trip H-2 pukul 20:00, media trip hari terakhir pukul 15:00, media untuk crew H-1 pukul 15:00, pengingat crew H-1 pukul 10:00.

Catatan: `agent_id = 2` dipakai juga oleh booking checkout website (lihat bagian Alur booking channel JVTO); yang membedakan booking Klook adalah `booking_category_id = 3`.

### Pesan yang terkirim

- **Ke tamu:** template WA `booking_confirmed_klook_email` lewat wa-inbox berisi detail booking, konfirmasi, dan tautan portal (variabel: nama, paket, tanggal trip, jumlah pax, slug dan short link booking). Kunci idempotensi memakai id booking yang baru.
- **Ke grup hotel:** template `hotel_room_reservation`, satu per hotel per tanggal check-in.
- **EcosystemSync:** `notify(booking, 'created')` ke repo `jvto-ekosistem` bila konfigurasinya terisi.

### Setelah masuk sistem: sama dengan booking JVTO

Sesudah tercatat di Booking Backoffice, booking Klook berjalan seperti booking JVTO. Bedanya hanya di asal pemesanan dan pembayaran: pesanan datang dari luar (OTA Klook), dan JVTO tidak mengurus pembayaran tamu Klook.

| Tahap | Booking JVTO | Booking Klook |
| --- | --- | --- |
| Asal pesanan | Website JVTO | Klook (email konfirmasi) |
| Tampil di booking-overview | Ya | Ya |
| Dikelola admin (edit, dokumen) | Ya | Ya |
| Plotting crew | Ya | Ya |
| Crew melihat trip di aplikasi crew (crew-portal) | Ya | Ya |
| Reservasi hotel dan expense | Ya | Ya |
| Pembayaran tamu | JVTO menagih lewat Xendit atau transfer bank | Tamu membayar ke Klook; JVTO tidak menagih tamu |
| Invoice Xendit dan pengingat pelunasan ke tamu | Ya | Tidak ada; pengingat H-7 mengecualikan kategori Klook |
| Uang masuk ke JVTO | Dari tamu, sesuai DP dan pelunasan | Dari Klook, sebulan sekali sesudah trip |

Di data, booking Klook ditandai `booking_category_id = 3`. Karena JVTO tidak menagih tamu Klook, `dp` dan `payment` booking Klook bernilai 0.

## Alur booking TWT: dari email dan PDF ke backoffice

Booking TWT masuk lewat email. TWT mengirim email berisi info booking yang singkat, dan detail lengkapnya ada di file PDF booking yang dilampirkan. Sistem membaca PDF itu untuk membentuk booking di Booking Backoffice (repo new-backoffice).

### Langkah demi langkah

1. TWT mengirim email booking ke JVTO. Isi email hanya info booking sederhana; rincian ada di PDF lampiran.
2. Email diberi label Gmail "TWT EKSTRAKTOR" (keterangan owner).
3. Google Apps Script membaca email berlabel itu, lalu mengirim isi email dan file PDF-nya ke backend (keterangan owner; skripnya tidak ada di repo).
4. Backend new-backoffice menerima kiriman itu di `POST /twt-extractor`, ditangani `BookingController::twtExtractor` (`routes/web.php`).
5. Fungsi menyimpan PDF di `public/assets/customer-document/` dengan nama berupa cap waktu, lalu mengambil teks PDF dengan pustaka Smalot PdfParser.
6. Teks PDF dikirim ke DeepSeek (`deepseek-chat`) bersama data master hotel, kamar, dan aktivitas awal/akhir, supaya hasilnya langsung memakai ID master milik JVTO. DeepSeek mengembalikan satu objek JSON.
7. Dari JSON itu fungsi membuat pelanggan, booking, dokumen, detail kaos, itinerari per hari, hotel, dan kamar.
8. Backend membalas JSON berisi kode booking, nama tamu, tanggal trip, jumlah pax, dan grand total. Log tiap proses disimpan sebagai `twt_<tanggal-jam>.log` di storage.

### Yang dikirim ke backend

| Bagian | Isi | Dipakai untuk |
| --- | --- | --- |
| `emailData` | Data email (info booking singkat) | Dicatat di log |
| `fileData` | Nama file, tipe, ukuran, dan isi file dalam base64 | Sumber seluruh data booking. File harus berekstensi PDF |

Bila kiriman tidak membawa `fileData`, backend hanya membalas "Hanya data email yang diterima" dan tidak membuat booking.

### Data yang dibaca dari PDF

| Data | Catatan |
| --- | --- |
| Nomor dan tanggal invoice | Nomor invoice TWT disimpan sebagai `invoice_code_origin` |
| Nama pelanggan | Dibuat sebagai pelanggan baru |
| Tanggal mulai trip | Tanggal selesai dihitung dari jumlah hari itinerari |
| Durasi trip | Dihitung dari jumlah hari di itinerari, bukan dari nama paket |
| Jumlah pax | Angka saja |
| Ukuran kaos | XSS sampai XXXL; nilai kosong dibaca 0 |
| Jemput hari pertama | Lokasi, jam (bila ada dua jam, dipakai jam kedatangan), nomor penerbangan |
| Antar hari terakhir | Lokasi, jam, nomor penerbangan |
| Itinerari per hari | Tanggal, hari ke-, aktivitas awal dan akhir (dicocokkan ke master), hotel, kamar dan jumlahnya, makan pagi/siang/malam |
| Grand total | Angka Total di bagian bawah PDF halaman 2 (sudah termasuk JVTO Service Fee) |
| Tanggal di Ijen dan di Bromo | Diturunkan dari tanggal menginap di hotel destinasi Ijen (destination\_id 2) atau Bromo (destination\_id 1), ditambah satu hari |

Daftar hotel yang diberikan ke DeepSeek dibatasi pada hotel dengan ID 59, 11, 60, 12, 63, 34, 56, dan 10.

### Apa yang dibuat di database

| Tabel | Isi |
| --- | --- |
| Pelanggan (`User`) | Satu pelanggan baru dari nama di PDF |
| `Booking` | `agent_id = 1` (agen TWT), `booking_category_id` kosong, status `booked`, tipe `offline`, metode bayar `pay later`. Kode `JVR/<nomor urut>/<bulan>/<tahun>`, nomor urut dihitung per agen per bulan keberangkatan. Titik jemput dan antar bertipe `Others`. Pengingat WA crew dijadwalkan H-1 pukul 10:00 |
| `BookingDocument` | PDF asli, dilampirkan ke booking dengan `attachment_type_id = 6` |
| `BookingDetail` | Jumlah pax dan rincian ukuran kaos |
| `BookingItinerary` | Satu baris per hari: aktivitas, aktivitas awal/akhir, makan |
| `BookHotel` dan `BookRoomHotel` | Hotel per hari dan kamar beserta jumlahnya; subtotal kamar = jumlah × tarif kamar di master |

Sesudah tersimpan, fungsi memanggil `EcosystemSync::notify(<id booking>, 'created')`, seperti jalur booking lain. Tamu TWT dihubungi oleh TWT, sehingga alur ini tidak mengirim pesan WhatsApp dari JVTO.

### Pembayaran pada booking TWT

`dp` dan `payment` bernilai 0, `grand_total` diisi dari PDF, dan `balance` sama dengan `grand_total`. Penagihan dan pembayaran TWT dilakukan lewat aplikasi TWT (lihat bagian Aplikasi TWT): di akhir bulan dihitung jumlah trip bulan berikutnya, dan itu yang dibayarkan TWT. Trip yang jatuh di tengah bulan atau trip dadakan dibayar lebih dulu atau menunggu sesudahnya, tergantung kondisi (keterangan owner). Harga dan paket mengikuti PDF dari TWT, bukan pengaturan harga JVTO.

### Isi PDF dari TWT: rincian biaya dan JVTO Service Fee

PDF dikirim TWT (The Window Travel Sdn. Bhd.) ke JVTO per booking, dan terdiri dari dua halaman. Contoh yang dipakai di sini: invoice TWT-0451, paket SUB5 (5 hari 4 malam, Surabaya private trip), 2 dewasa.

| Halaman | Isi |
| --- | --- |
| 1 | Kop TWT; nama paket TWT, nomor dan tanggal invoice, nama tamu, tanggal trip, jumlah pax, ukuran kaos; daftar termasuk dan tidak termasuk; itinerari per hari lengkap dengan nomor penerbangan dan jam jemput; akomodasi (tanggal, hotel, tipe kamar, jumlah kamar) |
| 2 | Tabel biaya dengan kolom Description, Unit, Day, Quantity, Rate, Amount, dan baris Total di bawah |

Tabel biaya di halaman 2 dibagi dalam enam kelompok:

| Kelompok | Isi pada contoh |
| --- | --- |
| A. Accommodation | Hotel per wilayah: Ijen, Bromo, Lumajang/Malang/Jember, Surabaya |
| B. Activities | Tiket dan kegiatan per lokasi: Ijen (tiket, masker gas, cek medis, kopi dan Indomie, pemandu lokal, makan homestay), Bromo (tiket, jeep), Madakaripura, Tumpak Sewu/Kapas Biru/Panorama/Coban Sriti, Taman Langit |
| C. Transportation | Kendaraan (hari × jumlah), parkir dan tol, shuttle |
| D. Crews | Driver merangkap pemandu, uang makan, uang perjalanan |
| E. Others | Air mineral, kaos |
| F. JVTO Service Fee | Biaya jasa JVTO per pax |

JVTO Service Fee adalah komisi JVTO dari booking TWT. Besarnya mengikuti rumusan yang sudah disepakati menurut lama trip dan jumlah pax (misalnya 1-3 pax satu tarif, 4-8 pax tarif lain). PDF hanya menampilkan baris yang berlaku untuk booking itu. Pada contoh: baris "4 Days or more", 1-3 pax, Rp 460.000 per pax × 2 pax = Rp 920.000.

Pada contoh, jumlah semua baris adalah Rp 11.277.000, sama dengan angka Total di PDF. Total itu sudah termasuk JVTO Service Fee, dan angka itulah yang dibaca pembaca PDF sebagai `grand_total` booking.

Hotel pada contoh (Harris Malang, Manis Ae Bromo, Riverside Homestay, Harris Satelit Surabaya) dicocokkan ke master hotel JVTO, dan kamar per hotel dibaca dari baris Accommodation di halaman 1.

### Perubahan dan pembatalan

Perubahan, pembatalan, dan penghapusan booking TWT dilakukan oleh admin langsung di backoffice (keterangan owner). Pembaca PDF membuat booking baru; perubahan dan pembatalan dilakukan admin. Hal yang sama berlaku untuk booking Klook.

### Setelah masuk sistem

Sama seperti booking JVTO dan Klook: tampil di booking-overview, diplot, dan tripnya muncul di aplikasi crew. Yang membedakan adalah asal pesanan (email dan PDF dari TWT) dan pihak yang menagih tamu (TWT).

Ada pula halaman unggah manual `/pdf-extractor` di new-backoffice (`twtExtractorFileIndex` dan `twtExtractorFileProcess`) untuk memproses PDF TWT tanpa lewat email.

## Setelah booking tercatat: booking-overview dan expense otomatis

Bagian ini berlaku untuk booking yang dibuat otomatis (Klook dan TWT) dan yang diinput admin.

### Tampil di booking-overview

Halaman booking-overview (`ScheduleController::index` di new-backoffice) tidak punya tabel jadwal sendiri. Halaman ini membaca tabel `bookings`: booking dengan `status = booked` dan `travel_date_start` pada bulan atau rentang tanggal yang dipilih, diurutkan menurut tanggal trip. Karena itu booking Klook dan TWT langsung muncul begitu barisnya tersimpan dengan status `booked`, tanpa langkah tambahan.

Filter channel di halaman itu memakai penanda yang sama dengan yang dibahas di atas:

| Channel | Kondisi di tabel `bookings` |
| --- | --- |
| KLOOK | `agent_id = 2` dan `booking_category_id = 3` |
| JVTO | `agent_id = 2` dan `booking_category_id` bukan 3 |
| TWT | `agent_id = 1` |

### Jadwal pesan WhatsApp yang diisi saat booking dibuat

Selain itu, saat booking dibuat otomatis, kolom jadwal pesan WhatsApp pada baris booking ikut terisi (relatif terhadap tanggal mulai trip):

| Kolom | Klook | TWT |
| --- | --- | --- |
| `wa_schedule_trip_information` | H-2 pukul 20:00 | tidak diisi |
| `wa_schedule_trip_media` | hari terakhir trip, pukul 15:00 | tidak diisi |
| `wa_schedule_trip_media_crew` | H-1 pukul 15:00 | tidak diisi |
| `wa_schedule_reminder_crew` | H-1 pukul 10:00 | H-1 pukul 10:00 |

Hari terakhir trip dihitung dari tanggal mulai ditambah (durasi − 1) hari.

### Expense otomatis

Expense adalah biaya operasional booking: makan di hotel, aktivitas di destinasi, biaya lain, kendaraan, dan crew. Fungsi `generateExpense` menyusunnya dari item bawaan (default):

| Komponen | Sumber item bawaan |
| --- | --- |
| Makan siang dan makan malam di hotel | Dari hotel booking yang ditandai makan siang atau makan malam, dengan tarif makan di master hotel × jumlah pax |
| Aktivitas destinasi | Dari itinerari paket; hanya aktivitas yang ditandai default untuk channel booking itu: `is_default_jvto`, `is_default_klook`, atau `is_default_twt` |
| Aktivitas lain | Master aktivitas lain yang ditandai `is_default` |
| Kendaraan dan crew | Dari konfigurasi mobil menurut jumlah pax, dengan peran crew yang berbeda untuk JVTO, Klook, dan TWT |

Hasilnya dijumlahkan ke kolom total expense pada booking (`expense_internal_total` dan `total_expense_crew`). Tiap komponen hanya dibuat bila booking itu belum punya barisnya, jadi menjalankan ulang fungsi ini tidak menimpa yang sudah ada.

| Cara booking dibuat | Expense otomatis |
| --- | --- |
| Klook (webhook email) | Ya. `generateExpense` dipanggil di akhir `webhook` |
| JVTO (checkout website, lewat Xendit) | Ya. `XenditController` memanggil `generateExpense` sesudah mengirim reservasi hotel, saat webhook Xendit memproses pembayaran |
| Admin menyimpan booking baru di backoffice (`BookingController::store`) | Ya, kecuali channel TWT |
| Admin mengubah booking (`BookingController::update`) | Dibuat atau diperbarui, kecuali channel TWT atau bila file expense internal sudah ada |
| TWT (pembaca PDF, `twtExtractor`) | Tidak dibuat otomatis; rincian biaya TWT ada di PDF-nya |

## Pesan otomatis ke tamu dan customer portal

Tamu JVTO dan Klook menerima pesan otomatis dari JVTO, dan keduanya diberi tautan ke customer portal. Tamu TWT tidak menerima pesan apa pun dari JVTO; booking TWT hanya masuk ke sistem dan tampil di booking-overview.

### Pesan otomatis per channel

| Saat | JVTO | Klook | TWT |
| --- | --- | --- | --- |
| Tamu checkout / booking diterima backend | WhatsApp `booking_pending_payment`: ringkasan booking (kode, paket, tanggal, pax), jumlah yang harus dibayar, jenis dan metode bayar, tautan pembayaran, batas waktu bayar. Email `NewReservationMail` dengan invoice PDF terlampir | WhatsApp `booking_confirmed_klook_email`: detail booking (paket dan pax), konfirmasi, tautan portal | Tidak ada |
| Pembayaran pertama (DP atau penuh) diterima | WhatsApp `payment_received_first`: konfirmasi booking, ringkasan booking dan pembayaran, tautan portal. Teks yang sama diteruskan ke grup internal JVTO. Email "Payment Confirmed \<nama> \<kode>" (ConfirmedMail) dengan lampiran PDF detail booking dan PDF receipt; salinannya dikirim ke email JVTO | Tidak berlaku, tamu membayar ke Klook | Tidak ada |
| Tamu memilih metode untuk pelunasan | WhatsApp `payment_method_confirmed_card`, `_wise`, atau `_cash` berisi sisa tagihan (dan tautan bayar untuk kartu) | Tidak berlaku | Tidak ada |
| Pelunasan diterima | WhatsApp `payment_received_balance`: konfirmasi pelunasan dan tautan portal. Email "Payment Confirmed \<nama> \<kode>" dengan lampiran PDF receipt pembayaran itu; salinannya dikirim ke email JVTO. Pembayaran add-on dicatat terpisah dan mendapat receiptnya sendiri | Tidak berlaku | Tidak ada |

Setiap pembayaran tamu JVTO, baik DP maupun pelunasan, menghasilkan dua hal otomatis: pesan WhatsApp konfirmasi dan email receipt. Isi pesan WhatsApp ada di wa-inbox (`src/lib/system-templates/seed-data.ts`) dan semuanya dikirim lewat wa-inbox. Di produksi, tautan portal pada pesan-pesan ini berbentuk tautan pendek `jvto.me/b/<kode>` (teks template di database diganti oleh `scripts/switch-booking-short-link.ts` dari `my-booking/<slug>`; teks awal di seed masih memakai tautan panjang). Template yang tautannya diganti: booking dari backoffice (JVTO dan Klook), konfirmasi Klook dari email, pembayaran pertama dan pelunasan diterima, pengingat pelunasan, trip information, dan trip concluded. Email dikirim dari legacy (`XenditController`, `sendEmailConfirm` untuk pembayaran pertama dan `sendEmailReceipt` untuk pelunasan).

Booking yang diinput admin di backoffice (misalnya dari channel lain): bila opsi kirim WA diaktifkan dan booking bukan TWT, tamu menerima WhatsApp `booking_confirmed_backoffice_jvto` (atau `booking_confirmed_backoffice_klook` untuk kategori Klook) berisi tautan portal. Expense dibuat otomatis kecuali untuk channel TWT.

### Customer portal

Tautan portal membuka halaman `/my-booking/<slug>` di jvto-web. Halaman ini mengambil datanya dari legacy (`/bookings/details/<slug>?json=true`) dan dibuka lewat tautan yang memuat kode booking. Isinya:

| Bagian | Isi | Untuk Klook |
| --- | --- | --- |
| Kepala halaman | Kode booking dan lencana status: Confirmed & Paid, Confirmed (Balance Due), Awaiting Payment, atau Payment Under Review | Tanpa lencana status |
| Trip Details | Tanggal, durasi, jumlah pax, titik awal | Sama |
| Kartu pembayaran | Sisa tagihan, metode yang dipilih, tombol Select Payment Method atau Pay Balance (booking terkonfirmasi), atau Pay Now dengan batas waktu (booking belum dibayar) | Tidak tampil |
| Add-ons | Add-on yang dibeli, jumlah, harga, total | Sama |
| Itinerary | Itinerari per hari (khusus booking, atau dari paket bila tidak ada) dan akomodasi per hari beserta kamarnya | Sama |
| Crew dan kendaraan | Foto dan peran guide dan driver yang diplot, serta spesifikasi dan foto kendaraan menurut jumlah pax | Sama |
| Edit booking | Tamu dapat mengubah ukuran kaos, titik dan jam jemput, serta titik dan jam antar | Tanpa kaos |
| Informasi | FAQ (sebelum, selama tur, dan per aktivitas), rekomendasi barang bawaan, tautan Trip Media | Sama |
| Sisi samping | Trip Media, tautan review Trustpilot dan Google, kontak email JVTO, tombol Chat with Us ke `chat.javavolcano-touroperator.com/chat/<slug>` | Sama |
| Dokumen | Invoice, Receipt, dan Add-on Invoice (bila ada add-on) | Tidak tampil |
| Grand Total | Total biaya trip | Tidak tampil |
| Payment History | Metode, tanggal, dan nominal tiap pembayaran | Tidak tampil |

Untuk Klook, bagian pembayaran dan dokumen tidak ditampilkan, sebab JVTO tidak menagih tamu Klook. Tampilan di ponsel memakai komponen terpisah (`MobileBookingView`) dengan isi yang sama.

### Pelunasan lewat portal

Tamu JVTO yang sudah membayar DP dapat melunasi sisa tagihannya sendiri lewat portal:

1. Di kartu pembayaran, tamu menekan Select Payment Method dan memilih salah satu: kartu kredit/debit lewat Xendit (dikenai biaya), Wise atau transfer bank, atau bayar tunai saat tiba.
2. Portal memanggil `/api/booking/pay-balance` di jvto-web, yang meneruskannya ke legacy `POST /booking/set-payment-method/<slug>`.
3. Legacy menyimpan metode pelunasan, lalu mengirim WhatsApp `payment_method_confirmed_*` berisi sisa tagihan. Untuk kartu, pesan itu membawa tautan bayar Xendit, dan tombol di portal berubah menjadi Pay Balance.
4. Untuk transfer bank, tamu membuka halaman `/my-booking/<slug>/bank-transfer`, membaca informasi rekening (BCA), dan mengunggah bukti transfer. Status portal menjadi Payment Under Review sampai admin memverifikasinya di menu Pending Booking (lihat bagian berikut).
5. Bila bayar lewat Xendit, webhook Xendit menandai pelunasan dan mengirim WhatsApp `payment_received_balance`.

Selain lewat portal, tamu juga dikirimi pengingat pelunasan H-7 (lihat bagian Pelunasan di alur booking JVTO).

## Verifikasi dan pencatatan pembayaran oleh admin

Tidak semua pembayaran lewat Xendit. Admin menangani dua jalur di backoffice (new-backoffice): memverifikasi transfer yang diunggah tamu, dan mencatat pembayaran secara manual.

### Pending Booking: verifikasi transfer bank

Menu Pending Booking (`/pending-booking`, `ScheduleController::pendingBooking`) menampilkan booking yang menunggu verifikasi transfer: status `pending wise`, metode bayar `pay later`, dan ada pembayaran yang belum ditandai lunas (bukti transfer yang diunggah tamu). Tiap baris memuat nama dan kontak tamu, tanggal trip, paket, jumlah pax, dan tautan bukti transfer.

Admin memeriksa bukti, lalu menekan Approve. Tombol itu mengirim `POST /pending-booking/<id>/approve` ke new-backoffice (`ScheduleController::approvePendingBooking`, wajib login). Server memeriksa bahwa booking masih berstatus `pending wise`, metode `pay later`, dan punya pembayaran yang belum lunas, lalu memanggil webhook sukses invoice Xendit di legacy (`/third-party/xendit/webhook/invoice/success`) dari server ke server, dengan header `X-Backoffice-Token`, `external_id` berisi ID booking, dan `payment_method_id = 1`. Persetujuan manual diproses lewat jalur yang sama dengan pembayaran Xendit yang berhasil, sehingga booking menjadi `booked` dan pesan serta email konfirmasi di atas ikut terkirim.

### Halaman detail booking: pencatatan pembayaran manual

Dari booking-overview, setiap booking punya tautan ke halaman detail (`/bookings/details/<id>`, `ScheduleController::details`, tampilan `Schedule/Details.tsx`). Halaman ini punya menu samping dengan tujuh bagian:

| Bagian | Isi |
| --- | --- |
| Client Information | Data tamu |
| Booking Information | Data booking: tanggal, pax, jemput, antar, kaos, tautan Trip Media (dapat diedit) |
| Package Details | Itinerari paket per hari |
| Activities | Aktivitas per hari |
| Accommodation | Hotel dan kamar per hari |
| Resource Allocation | Kendaraan, driver, dan guide yang diplot |
| Financial Data | Total invoice (dengan tautan invoice), total pembayaran, balance, metode bayar, tautan pembayaran pelunasan, expense, profit, dan riwayat pembayaran |

Booking Big Group mendapat satu bagian tambahan untuk peserta. Di Booking Information, admin dapat mengubah cepat titik jemput, titik antar, dan ukuran kaos (`/bookings/quick-update/<id>`).

Untuk channel JVTO yang masih punya balance, bagian Financial Data menampilkan Payment History dan tombol Add Payment. Form Add Payment berisi:

| Isian | Keterangan |
| --- | --- |
| Nominal | Jumlah yang diterima |
| Payment method | Pilihan dari master metode pembayaran |
| Reference | Nomor referensi pembayaran |
| Note | Catatan, disimpan sebagai deskripsi pembayaran |
| Invoice Item | Package atau Add-on; muncul bila booking punya add-on dan masih ada balance |
| Send Receipt | Aktif secara bawaan; bila aktif, receipt dikirim ke tamu |

Saat disimpan (`BookingController::storePayment`, `POST /bookings/payment/<id>`), sistem memvalidasi isian (nominal lebih dari 0, metode pembayaran terdaftar, dan bukti pembayaran opsional berupa JPG, PNG, atau PDF hingga 5 MB), lalu `PaymentService` mencatat pembayaran dalam satu transaksi: satu baris `BookingPayment` (ditandai lunas, dengan sumber pembayaran, pembuat, dan file bukti bila ada), mengurangi `balance` booking sebesar nominal, menambah `payment`, dan menulis audit log. Booking Klook ditolak karena dibayar channel per bulan, dan nominal yang melebihi sisa tagihan hanya diterima bila kelebihan bayar dicentang dan alasan diisi. Sesudah itu sistem memanggil `EcosystemSync::notify(<id>, 'updated')`. Bila Send Receipt aktif, backoffice memanggil legacy `POST /api/new-backoffice/send-receipt/<id>/<paymentId>` (`InvoiceController::sendEmailReceipt`).

Di bagian yang sama admin juga dapat mengubah metode pembayaran pelunasan (`POST /bookings/payment-method/<id>`). Bila memilih kartu dan opsi buat tautan Xendit aktif, backoffice meminta legacy membuat tautan bayar kartu untuk booking itu. Perubahan metode dicatat di audit log, dan memilih cash membuat tugas setoran cash crew (CashCollectionService). Pembayaran yang sudah tercatat dapat dibatalkan lewat POST /bookings/payment/\<id>/void dengan alasan wajib: barisnya tidak dihapus melainkan ditandai batal, dan payment serta balance booking dikembalikan. Tombol pembatalan ada di halaman Incoming Payments.

## Struktur expense booking

Expense adalah biaya operasional satu booking. Item expense dibuat otomatis (lihat bagian Expense otomatis) atau ditambah admin secara manual, lalu semuanya dapat diedit di halaman Expense Manager: `/finance/expense-manager/<id booking>/edit` (`FinanceController::editExpense`, tampilan `Finance/EditExpenseManager.tsx`). Halaman ini dibuka dari tautan Expense di bagian Financial Data pada halaman detail booking di booking-overview. Perubahan disimpan lewat `POST /finance/expense-manager/<id>/update` (`updateExpense`).

Bila booking belum punya item destinasi, aktivitas lain, atau kendaraan, halaman edit membuat item bawaan saat dibuka, dengan aturan yang sama dengan `generateExpense` (untuk TWT memakai flag `is_default_twt`).

### Kategori dan sub kategori

Item dikelompokkan dalam enam kategori, dengan urutan tampil sebagai berikut:

| Kategori | Sub kategori | Isi item | Unit | Qty | Harga satuan |
| --- | --- | --- | --- | --- | --- |
| Accommodation | Nama hotel | Kamar (tipe kamar), makan siang, makan malam | Kamar: No; makan: Pax | Kamar: jumlah kamar. Makan: jumlah pax | Kamar: tarif kamar di master. Makan: tarif makan siang atau malam hotel |
| Destination | Nama destinasi (mis. Ijen, Bromo, Tumpak Sewu) | Aktivitas destinasi: tiket, pemandu lokal, jeep, sarapan, dan sebagainya | Pax | Hasil rumus aktivitas | Harga aktivitas di master |
| Others | Additional | Aktivitas lain: uang transport, air mineral, kaos, tol dan parkir, dan sebagainya | Item | Hasil rumus item | Harga item di master |
| Transport | Airport Transfer | Kendaraan | Unit | Jumlah hari | Harga kendaraan menurut jumlah pax (konfigurasi mobil) |
| Resource | Crew | Peran crew (mis. driver merangkap pemandu) | Person | Jumlah hari | Tarif peran crew di master |
| AddOn | Additional Service | Add-on yang dibeli tamu | Item | Jumlah add-on | Harga expense add-on (`price_expense`) |

Subtotal tiap item adalah qty × harga satuan. Qty dan harga tiap item dapat diedit langsung di tabel, dan item dapat dihapus atau ditambah dari master (Add Destination, Add Others, Add Transportation, Add Crew, Add On). Item destinasi atau aktivitas lain yang belum ada di master dapat dibuat baru dari form tambah; item baru itu masuk master dengan rumus 1 dan tidak ditandai default.

### Rumus per item

Tiap aktivitas destinasi dan tiap aktivitas lain di master punya kolom `formula`, `unit`, dan `price`. Rumus ditulis dengan variabel `pax` (jumlah peserta), `day` (lama trip dalam hari), dan fungsi `Math.ceil`. Saat expense dibuat, rumus itu dihitung (`pax` menjadi `$pax`, `day` menjadi `$day`, `Math.ceil` menjadi `ceil`) dan hasilnya menjadi qty. Setiap item punya rumus sendiri, jadi qty tiap item bisa berbeda. Contoh hasil pada satu booking 2 pax selama 3 hari: tiket Ijen dan tiket Bromo qty 2 (mengikuti pax), kaos qty 2 (mengikuti pax), pemandu lokal dan jeep Bromo qty 1, kendaraan dan driver qty 3 (mengikuti hari), uang transport dan tol-parkir qty 1.

Penyimpanan Expense Manager (`ExpenseService::save`) berjalan dalam satu transaksi dengan booking dikunci: tiap perubahan baris dicatat di audit log (nilai sebelum dan sesudah), dan item yang sudah dilunasi lewat pembayaran hutang atau terikat pembayaran keluar aktif terkunci, jadi nominal, status, dan penghapusannya baru dapat diubah setelah pembayaran itu dibatalkan (void). Total booking dihitung dari baris di database, bukan dari angka kiriman browser.

Makan hotel dihitung jumlah pax × tarif makan hotel. Kamar dihitung jumlah kamar × tarif kamar di master.

Contoh rumus dan harga dari master (dibaca dari database pada 3 November 2026):

| Item | Kategori | Unit | Rumus qty | Harga master |
| --- | --- | --- | --- | --- |
| Bromo Ticket (ID 1) | Destination, Mount Bromo | pax | `pax` | Rp 160.000 |
| Ijen Ticket (ID 3) | Destination, Mount Ijen | pax | `pax` | Rp 75.000 |
| Bromo Jeep (ID 17, default JVTO dan Klook) | Destination, Mount Bromo | no | `Math.ceil(pax/4)` | Rp 500.000 |
| Bromo Jeep TWT (ID 2, default TWT) | Destination, Mount Bromo | no | `Math.ceil(pax/4)` | Rp 550.000 |
| Transport Allowance (ID 2) | Others | trip | `1` | Rp 100.000 |
| Mineral Water (ID 3, vendor Mineral Water) | Others | bottle | `Math.ceil(pax/3)` | Rp 40.000 |
| T-shirt (ID 4, vendor Kubik) | Others | piece | `pax` | Rp 60.000 |
| Toll & Parking (ID 5) | Others | trip | `1` | Rp 200.000 |
| Peran crew (mis. Driver cum guide) | Resource | Day x Qty | `days * qty * rate` | Tarif per channel: JVTO Rp 250.000, Klook Rp 250.000, TWT Rp 313.000 |

Tiap peran crew memiliki satu baris per channel di master (JVTO, TWT, Klook), dengan tarif sendiri.

Item bawaan tiap channel berbeda. Aktivitas destinasi dipilih menurut flag `is_default_jvto`, `is_default_klook`, atau `is_default_twt`. Kendaraan dan crew diambil dari konfigurasi mobil menurut jumlah pax, dan peran crew-nya berbeda untuk JVTO, Klook, dan TWT.

### Vendor

Setiap item hotel, aktivitas destinasi, kendaraan, dan aktivitas lain terhubung ke vendor lewat master-nya (kolom `vendor_id` pada hotel, aktivitas destinasi, kendaraan, dan aktivitas lain). Vendor dikelompokkan dalam empat kategori:

| Kategori vendor | Item yang dimiliki | Contoh vendor di data |
| --- | --- | --- |
| 1. Accommodation | Hotel (kamar dan makan) | Baratha Hotel, Joglo Kecombrang, Riverside |
| 2. Activities | Aktivitas destinasi | Widhi Bromo (tiket dan jeep Bromo), Ijen Ticket |
| 3. Transportation | Kendaraan | Garage, Ertiga, Boy, Yandi, Pak Yus |
| 4. Others | Aktivitas lain | Mineral Water, Kubik (kaos) |

Item crew dan add-on tidak punya vendor. Beberapa item dari master (misalnya pemandu lokal, uang transport, tol dan parkir) juga tidak punya vendor; item seperti ini, bila belum dibayar dan bukan pay later, masuk ke Crew Expense.

### Ringkasan di bagian atas halaman

Bagian atas halaman menampilkan empat kartu. Saat Expense Manager disimpan, server menghitung ulang angka kartu dari data di database (ExpenseService::summary), dengan urutan prioritas paid, lalu pay later, lalu belum dibayar: item yang ditandai paid masuk Already Paid; item bukan paid tetapi pay later masuk Pay Later; sisanya masuk Crew Expense. Di layar, Pay Later mematikan Paid pada item yang sama:

| Kartu | Isi | Disimpan di kolom booking |
| --- | --- | --- |
| Total Expenses | Total semua item expense | `expense_internal_total` |
| Crew Expense | Item yang belum dibayar dan bukan pay later, yaitu expense yang akan ditransfer JVTO ke crew | `total_expense_crew` |
| Already Paid | Item yang sudah ditransfer atau dibayar langsung JVTO ke vendor | `total_expense_paid` |
| Pay Later | Item yang ditunda sebagai hutang untuk dibayar nanti | `total_expense_debt` |

Contoh pada satu booking Klook 2 pax selama 3 hari: Total Expenses Rp 5.015.000, Already Paid Rp 0, Pay Later Rp 3.715.000 (hotel, tiket, jeep, kendaraan, air mineral, dan kaos), dan Crew Expense Rp 1.300.000 (pemandu lokal Ijen, driver 3 hari, uang transport, dan tol-parkir). Total Rp 5.015.000 sama dengan jumlah ketiganya.

Kartu Crew Expense punya dua ikon: unggah atau lihat bukti pembayaran expense (`payment_proof_expense`) dan unduh daftar expense crew. Kartu lain juga punya tautan unduh (`/paid`, `/internal`, `/pay-later`). Bila booking punya file expense internal (`expense_file_internal`), angka ringkasan di booking tidak diperbarui oleh penyimpanan halaman ini.

### Status paid dan pay later per item

| Bagian | Cara status diatur |
| --- | --- |
| Hotel | Status paid dan pay later berlaku per hotel, untuk semua kamar dan makan di hotel itu (`is_paid`, `is_debt` pada `BookHotel`). Saat ditandai paid, waktu bayar dicatat |
| Destinasi, aktivitas lain, kendaraan, crew | Status per item (`status_paid`, `is_debt`); saat ditandai paid, tanggal bayar dicatat |
| Add-on | Add-on tidak punya status paid atau pay later (kolomnya tidak ada di `book_add_ons`); yang disimpan hanya qty dan harga expense, dan nilainya selalu dihitung sebagai Crew Expense |

Item bawaan yang langsung ditandai pay later sejak dibuat mengikuti generator tiap channel:

| Channel | Generator | Item yang ditandai pay later sejak dibuat |
| --- | --- | --- |
| JVTO | `XenditController::generateExpense` (legacy) | Aktivitas destinasi Ijen Ticket (ID 3); aktivitas lain Mineral Water (ID 3) dan T-shirt (ID 4) |
| Klook | `KlookEmailExtractorController::generateExpense` (legacy) | Hotel Baratha Hotel and Resto (ID 1) dan Joglo Kecombrang Bromo (ID 11); aktivitas destinasi Bromo Ticket (ID 1), Ijen Ticket (ID 3), dan Bromo Jeep (ID 17); aktivitas lain Mineral Water (ID 3) dan T-shirt (ID 4); kendaraan |
| TWT | Halaman edit Expense Manager (`FinanceController::editExpense`) | Tidak ada; item bawaan dibuat dengan status belum dibayar dan bukan pay later |

Item bawaan lainnya, termasuk crew, dibuat belum dibayar dan bukan pay later, sehingga masuk ke Crew Expense.

### Membayar crew, vendor, dan hutang

| Kebutuhan | Cara di sistem |
| --- | --- |
| Transfer expense ke crew | Tombol Transfer Expense membuka form berisi crew, bank, nomor rekening, nama pemilik rekening, jumlah (bawaan: nilai Crew Expense), dan catatan. Pengiriman memakai Xendit payout (`POST /finance/xendit-payouts`, lewat XenditPayoutSync yang selalu mengambil ulang status dari API Xendit); riwayatnya tampil di Riwayat Payout (Xendit), dan Xendit juga memberi kabar status lewat webhook POST /third-party/webhook/xendit-payout. Payout yang masih berstatus ACCEPTED dapat dibatalkan dengan alasan. Catatan transfer BCA ke crew tampil di Crew Transfer Records |
| Status transfer crew | `crew_transfer_status` bernilai pending atau transferred, dapat diganti dari Finance Hub (`toggleCrewTransfer`) |
| Melunasi hutang (Pay Later) ke vendor | Dilakukan per vendor lewat Finance Hub (`recordDebtPayment`) atau halaman Hutang Vendor (`/finance/vendor-debts`, tombol bayar), keduanya lewat `DebtPaymentService`. Admin memilih item hutang vendor (hotel, aktivitas destinasi, kendaraan, atau aktivitas lain), tanggal bayar, metode, sumber dana, dan bukti; sistem membuat nomor pembayaran berformat `JVR/`PAY/\<bulan 2 digit>/\<tahun 2 digit>/\<urutan 4 digit> (contoh `JVR/PAY/11/26/0001`), mengisi `debt_payment_id` pada tiap item, dan menghitung ulang total hutang booking. Pembayaran hutang dapat dibatalkan lewat `POST /finance/debt-payments/<id>/void` dengan alasan wajib; `debt_payment_id` item dikosongkan lagi |
| Total hutang booking | `total_expense_debt` adalah hutang yang belum dilunasi (item pay later tanpa `debt_payment_id`); `total_expense_debt_paid` adalah hutang yang sudah dilunasi (item pay later dengan `debt_payment_id`). Keduanya dihitung ulang oleh `BookingExpenseService::recalculate` |
| Rekap hutang | Menu Rekap Hutang menjumlahkan hutang belum lunas per vendor menurut bulan trip, dan dapat diekspor ke PDF dan Excel |

### Bagian lain di halaman Expense Manager

| Bagian | Isi |
| --- | --- |
| Booking Summary dan harga | Info booking dan riwayat pembayaran tamu beserta tautan receipt |
| Additional Requests | Permintaan biaya tambahan dari crew: item, qty, harga, subtotal, peminta, tanggal, foto dan bill, tanggal submit, dan crew yang mengajukan |
| Refund | Item refund: tujuan refund (bawaan: office), bukti, harga, qty, subtotal, dan status |
| Crew Transfer Records | Catatan transfer BCA ke crew: tanggal, jam, jumlah, rekening tujuan, bank, nomor referensi, dan catatan |
| Riwayat Payout (Xendit) | Payout Xendit untuk crew atau vendor: penerima, bank, rekening, jumlah, status, kode gagal, dan tautan receipt |

## Pesan otomatis ke vendor

Selain ke tamu, sistem mengirim WhatsApp otomatis (lewat wa-inbox) ke dua vendor: agen transport Bali (Pak Agus Bali, keterangan owner) dan vendor kaos.

### Agen transport Bali

Pesan ini dikirim untuk booking yang melibatkan transfer ke atau dari Bali. Ada dua jenis pesan, dengan isi yang sama:

| Jenis | Template | Kapan terkirim |
| --- | --- | --- |
| Pesan saat booking terkonfirmasi | `internal_bali_transport` | Saat pembayaran pertama diproses oleh webhook Xendit di legacy (`XenditController::invoiceSuccess`, fungsi `sendBaliNotif`) |
| Pengingat H-1 | `internal_bali_transport_reminder` | Command legacy `my:reminder-bali` (`ReminderBali`, dijadwalkan tiap menit di `Kernel.php`) bekerja pukul 15:15 dan mengirim untuk layanan yang jatuh besok |

Dua kondisi yang memicu pesan, pada kedua jenis di atas:

| Kondisi | Tanggal layanan | Jemput | Antar | Tambahan |
| --- | --- | --- | --- | --- |
| Paket berangkat dari Bali (`start_destination_id = 3`) | Tanggal mulai trip | Titik jemput booking (atau TO BE CONFIRMED) | Ketapang | Porter × 1 dan tiket ferry × jumlah pax |
| Booking punya add-on transport (`is_transport = 1`) | Tanggal selesai trip | Gilimanuk | Nama add-on dan titik antar booking (atau TO BE CONFIRMED) | Porter × 1 |

Isi pesan: tanggal layanan, jam jemput (atau TO BE CONFIRMED), nama tamu, jumlah pax, kendaraan, titik jemput, titik antar, dan tambahan. Kendaraan dipilih dari jumlah pax untuk paket dari Bali (Avanza untuk 1-3 pax, Elf Short untuk 4-9 pax, Elf Long untuk 10 pax atau lebih) dan dari ukuran add-on untuk add-on transport (small: Avanza, medium: Elf Short, lainnya: Elf Long). Nomor tujuan tertulis di kode legacy. Kunci idempotensi memuat id booking (atau add-on), tanggal, dan data pesan, sehingga pesan yang sama tidak terkirim dua kali.

Pengingat H-1 memeriksa booking berstatus `booked` tanpa membedakan channel: untuk paket dari Bali yang mulai besok, dan untuk booking dengan add-on transport yang selesai besok. Pesan saat booking terkonfirmasi dikirim dari webhook Xendit, yaitu jalur pembayaran booking JVTO.

### Vendor kaos

Bila rincian ukuran kaos sebuah booking berubah di new-backoffice, vendor kaos menerima WhatsApp `vendor_tshirt_size_update` (`BookingController::notifyTshirtVendor`). Nomor vendor diambil dari konfigurasi `WA_TSHIRT_VENDOR_NUMBER`. Isi pesan: nama tamu, tanggal trip, channel (JVTO, KLOOK, atau TWT), dan ukuran kaos baru (misalnya "XL x 2; M x 1"). Pesan hanya terkirim bila jumlah salah satu ukuran benar-benar berubah, dan kunci idempotensinya memuat id booking serta isi ukuran.

| Perubahan kaos lewat | Mengirim pesan ke vendor kaos |
| --- | --- |
| Edit booking oleh admin (`BookingController::update`) | Ya |
| Quick update kaos di halaman detail booking dari booking-overview (`BookingController::quickUpdate`, `POST /bookings/quick-update/<id>`) | Ya |
| Customer portal (`/my-booking/<slug>`, disimpan lewat legacy `UserBookingsController::updateTshirt`) | Perubahan disimpan dan jvto-ekosistem diberi tahu lewat `EcosystemSync`; kode fungsi ini tidak memanggil pengiriman ke vendor kaos |

## Plotting crew dan resource

Sesudah booking tercatat dan expense dibuat, admin memplot crew dan resource (kendaraan) untuk booking itu dari halaman booking-overview di new-backoffice. Form plotting (`Schedule/Index.tsx`) menyimpan lewat `POST /plotting` (`ScheduleController::plotting`).

### Isi form plotting

| Isian | Keterangan |
| --- | --- |
| Vehicles | Kendaraan (tabel `cars`) |
| Drivers | Driver (tabel `guide_drivers`, `is_driver = 1`) |
| Escort Guide | Guide pendamping selama trip |
| Ijen Guide | Guide khusus untuk hari Ijen; muncul bila booking punya tanggal di Ijen (`at_bondowoso`) |
| Notes | Catatan booking |

Daftar driver dan guide disaring menurut channel booking (kolom `tags` crew memuat JVTO, KLOOK, atau TWT). Tiap pilihan diberi status:

| Status | Arti |
| --- | --- |
| Tersedia | Tidak ada plotting lain yang bentrok tanggal |
| Tidak Tersedia | Sudah diplot di booking lain pada tanggal yang beririsan; info booking dan tamu yang bentrok ditampilkan |
| Terplotting | Sudah diplot di booking ini |

Ketersediaan dihitung dari irisan rentang tanggal plotting (mulai trip sampai selesai trip; untuk booking shuttle sampai sehari sebelum tanggal selesai) dengan plotting booking lain. Untuk Ijen Guide, ketersediaan dihitung terhadap tanggal Ijen: satu guide tidak dapat menjadi Ijen Guide pada dua booking dengan tanggal Ijen yang sama, dan guide dapat dipakai sebagai Escort atau Ijen menurut peran yang masih tersedia. Aturan yang sama diperiksa lagi di server saat plotting disimpan.

### Yang selalu dapat diplot

Vendor transport Garage menyediakan Hiace dan kendaraan lain. Garage dan crew Local Guide dianggap selalu dapat diplot, sedangkan yang lain mengikuti status ketersediaan (keterangan owner). Di form plotting, aturan ini tertulis pada ID berikut. Di server, kendaraan milik garasi (garage\_id terisi) atau bernama Hiace Commuter, Hiace Premio, Premio, atau Innova Reborn yang bentrok ditampilkan sebagai Tersedia (vendor), dan driver GARAGE (ID 9) yang bentrok juga ditampilkan sebagai Tersedia (vendor):

| Yang selalu dapat dipilih | ID di kode |
| --- | --- |
| Driver GARAGE | Driver GARAGE (ID 9, milik garasi) |
| Kendaraan | Hiace Commuter (ID 5), Premio (ID 21), MPV (ID 1), Innova Reborn (ID 2), dan Avanza Sub (ID 4); semuanya milik Garage kecuali MPV |
| Local Guide | Local Guide (ID 56, di data bernama Local Ijen): tidak ikut pembatasan status dan peran di daftar Ijen Guide, dan tidak ditampilkan di daftar Escort Guide |

### Yang terjadi saat plotting disimpan

Dalam satu transaksi (`PlottingService::sync`), sistem lebih dulu menolak crew atau kendaraan yang berstatus Tidak Tersedia atau yang perannya tidak tersedia pada tanggal itu, lalu menyamakan plotting dengan isian form: crew yang dicabut dihapus, crew yang baru ditambah, dan baris crew yang tetap dipertahankan (hanya tanggalnya disesuaikan bila tanggal trip berubah). Kendaraan dibuat ulang hanya bila daftarnya berubah. Tanggal mulai dan selesai tiap baris mengikuti tanggal trip (shuttle: selesai sehari sebelum tanggal selesai); Ijen Guide hanya satu hari, pada tanggal Ijen (`guide_ijen = 1`). Catatan disimpan di booking, dan perubahan plotting dicatat di audit log. Fungsi `reminderPlotting` mengirim WhatsApp (lewat API watzap, bukan wa-inbox) ke crew yang baru ditambah, berisi nama crew, tamu, tanggal dan durasi trip, tanggal Ijen, dan tautan crew-portal. Pesan ini hanya terkirim bila admin mencentang "Beritahu crew" di form plotting dan konfigurasi `WA_NOTIFY_CREW_PLOTTING` aktif (bawaannya mati). Crew juga diberi tahu lewat pengingat H-1 dan melihat jadwalnya di crew-portal.

## Aplikasi crew (crew-portal)

crew-portal adalah aplikasi web (Laravel dan Inertia/React, `crew-portal.javavolcano-touroperator.com`) tempat crew melihat jadwal dan detail trip yang sudah diplot. Aplikasi ini membaca database booking yang sama dengan new-backoffice dan legacy.

### Login

Crew masuk dengan nomor HP yang terdaftar di tabel partner. Sistem mengirim kode OTP 4 digit lewat WhatsApp; crew juga dapat masuk dengan password dan mengatur ulang password sendiri. Nomor yang belum terdaftar ditolak dengan pesan untuk menghubungi admin.

### Halaman

| Halaman | Isi |
| --- | --- |
| Home (`/home`) | Ringkasan bulan berjalan: jumlah trip escort dan Ijen (untuk driver: jumlah trip), trip selesai, aktif, dan mendatang; trip yang sedang berjalan; tiga trip mendatang. Tiap trip menampilkan tamu, channel (JVTO, KLOOK, TWT), durasi, tanggal, jumlah pax, dan jenis tugas (escort atau Ijen) |
| Trips (`/trips`) | Daftar trip crew itu sendiri menurut bulan, dikelompokkan selesai, aktif, dan mendatang |
| All Trips (`/trips/all`) | Semua booking berstatus booked pada bulan yang dipilih, dapat dicari menurut nama tamu |
| Detail Trip (`/trips/details/<id>`) | Lihat tabel di bawah |
| Profile, ID Card, Crew SOP | Profil crew, kartu identitas, dan SOP crew |
| Map (`/map`) | Peta lokasi (data lokasi dari jvto-ekosistem) |
| Notifications dan Settings | Notifikasi dan pengaturan akun |

### Detail Trip

| Bagian | Isi |
| --- | --- |
| Client Information | Nama tamu, kewarganegaraan (tidak untuk TWT), dan tombol Chat Now ke crew-chat (`crew-chat.javavolcano-touroperator.com/chat/<url booking>/<id crew>`) |
| Booking Information | ID booking dan channel, paket, jumlah peserta, tanggal trip, ukuran kaos, titik dan jam jemput, titik dan jam antar, permintaan khusus, dan catatan |
| Itinerary Information | Itinerari per hari dengan jam, aktivitas, lokasi, dan catatan (untuk paket JVTO dan Klook; bila booking lain memakai aktivitas awal yang sama pada hari itu, nama tamu, jumlah pax, dan hotelnya ikut ditampilkan) |
| Accommodation | Hotel per hari beserta kamar |
| Resource Allocation | Kendaraan dan crew yang diplot: driver, escort, dan Ijen guide |
| Trip Documents | Empat tombol: Trip Media (tautan media), Crew Expense (daftar expense crew di new-backoffice), Surat Tugas (dokumen dari legacy), dan Pickup Sign (PNG papan jemput bernama tamu, dari legacy) |
| Trip Photos | Unggah foto trip untuk diposting ke Instagram JVTO, dengan tag Instagram crew; tidak untuk trip TWT |
| Refund Items | Crew mengajukan refund per item expense: harga, qty, tujuan refund (office atau customer; untuk customer wajib foto bukti), dan status |
| Additional & Claims | Permintaan biaya tambahan: crew mengisi qty, harga, subtotal, serta foto bill dan foto; status menjadi submitted dan tidak dapat diubah lagi setelah invoiced |

Daftar Crew Expense berisi makan di hotel, aktivitas destinasi, dan aktivitas lain booking itu, lengkap dengan qty, harga, dan penanda pay later.

Repo crew-portal juga memuat rute `/reservation/<slug>`, `/travel-tshirt/kubik`, dan `/ijen-ticket`, yang sama dengan halaman di bagian Portal vendor.

## Pengingat otomatis ke crew dan tamu

Pengingat dijalankan oleh command legacy (`javavolcano-touroperator/app/Console/Commands`) yang dijadwalkan tiap menit di `Kernel.php`. Tiap command mencari booking yang kolom jadwalnya (`wa_schedule_*`) cocok dengan jam saat ini dan statusnya (`wa_status_*`) masih 0, mengirim template lewat wa-inbox, lalu mengubah status menjadi 1 (atau 00 bila gagal) dan mencatat `WaLog`.

### Daftar pengingat

| Penerima | Command | Waktu | Template dan isi |
| --- | --- | --- | --- |
| Tamu | `my:trip-information` | H-2 pukul 20:00 | `trip_information` (bervariasi menurut tipe jemput), berisi jam dan lokasi jemput, serta tautan portal. Gambar yang dilampirkan: pickup sign bernama tamu. Bila admin mengisi isi pembayaran (`wa_schedule_payment_content`), pesan `trip_payment_arrangement` dikirim sesudahnya |
| Tamu | `my:trip-media` | Hari terakhir trip pukul 15:00 | `trip_concluded`: ucapan terima kasih, tautan customer portal tempat foto trip dan Trip Media tersedia, serta ajakan memberi ulasan di Google atau Trustpilot. Bersamaan dengan itu sistem membuat satu diskon untuk tamu itu (nominal Rp 450.000, gift card acak, berlaku sampai 31 Desember tahun berjalan) bila belum ada |
| Crew (tiap nomor) | `my:trip-reminder-crew` | H-1 pukul 10:00 | `crew_reminder_personal` ke setiap crew yang diplot dan punya nomor: nama tamu, titik dan jam jemput, dan tautan crew-portal |
| Grup crew | `my:trip-reminder-crew` | H-1 pukul 10:00 | `crew_reminder_group`: tamu, titik dan jam jemput, daftar crew, dan tautan crew-portal. Dikirim ke satu grup WhatsApp tetap (grup JVTO, keterangan owner), hanya untuk booking TWT (`agent_id = 1`) |
| Grup crew | `my:trip-media-crew` | H-1 pukul 15:00 | `crew_trip_media`: nama tamu, tautan media trip, dan daftar crew, dikirim ke grup WhatsApp JVTO yang diatur di konfigurasi (keterangan owner), untuk booking yang jadwal media crew-nya terisi |

Tipe jemput menentukan template `trip_information`: lokasi yang mengandung kata bandara (airport, juanda, ngurah rai, dan sebagainya) memakai `trip_information_airport`, stasiun (station, gubeng, pasar turi, dan sebagainya) memakai varian stasiun, hotel (hotel, villa, homestay, resort, dan sebagainya) memakai varian hotel, dan lokasi lain memakai template umum `trip_information`. Pengingat crew H-1 tidak mensyaratkan opsi kirim WA aktif, sedangkan pesan tamu dan pesan grup media hanya terkirim bila `is_send_wa = 1`.

Command `my:trip-itinerary` dan `my:trip-itinerary-crew` terdaftar di jadwal; kode pengirimannya saat ini dikomentari, jadi keduanya tidak mengirim pesan.

### Siapa yang mengisi jadwal

Kolom jadwal diisi saat booking dibuat atau diubah:

| Sumber booking | `wa_schedule_trip_information`, `_trip_media`, `_trip_media_crew` | `wa_schedule_reminder_crew` |
| --- | --- | --- |
| Klook (email) | Diisi (`is_send_wa = 1`) | Diisi |
| Booking dari new-backoffice (simpan atau ubah) | Diisi bila opsi kirim WA aktif | Selalu diisi |
| Booking dari backoffice legacy | Diisi bila opsi kirim WA aktif | Tidak diisi di form ini |
| TWT (`twtExtractor`) | Tidak diisi | Diisi |
| Booking website JVTO | Terisi saat admin menyimpan booking dari form edit dengan opsi kirim WA aktif. Pada data, booking website yang belum diedit admin masih berstatus is\_send\_wa 0 dan kolom jadwalnya kosong | Terisi saat disimpan dari form edit di new-backoffice |

## Portal vendor

Menu Portal Vendor di new-backoffice (`/portal-vendor`, `DashboardController::portalVendor`, tampilan `PortalVendor.tsx`) berisi kartu tautan ke halaman tiap vendor. Halaman-halaman itu ada di `partner.javavolcano-touroperator.com`, yaitu aplikasi jvto-partner (Laravel dan Inertia/React). Tiap vendor membuka halamannya masing-masing untuk melihat rekap dan jadwal pekerjaannya. Data diambil dari database booking yang sama; status hutang pada tiap halaman berasal dari penanda pay later pada item expense booking.

### Kartu dan halaman

| Kartu di menu | Halaman | Keterangan di menu |
| --- | --- | --- |
| Portal Hotel | `/reservation/<slug hotel>`, tersedia untuk Baratha Hotel, Riverside Homestay, Joglo Kecombrang, dan Grand Padis | Rekap dan schedule hotel serta pantau status hutang |
| Portal Ijen | `/doggy-style` (juga `/ijen-ticket`) | Rekap tiket Ijen dan pantau kerawanan destinasi Ijen |
| Portal Bromo | `/bromo-recap` | Schedule dan rekap tiket, jeep, serta kerawanan destinasi Bromo |
| Portal Garasi | `/garage-recap` | Rekap dan schedule garasi serta rekapitulasi hutang |
| Portal Tshirt | `/travel-tshirt/kubik` | Rekap tshirt dan pantau status proses pembuatan tshirt |
| Portal Medical Check Up | `/health-screening` | Jadwal dan rekap pemeriksaan kesehatan di masing-masing hotel |

### Isi tiap halaman

| Halaman | Isi dan aksi vendor |
| --- | --- |
| Hotel | Daftar reservasi kamar di hotel itu menurut tahun, bulan, status (New Booking, Confirmed, Ditolak), dan status hutang. Tiap baris memuat nama tamu, check-in, check-out, jumlah pax, kamar dan biaya kamar, makan dan biaya makan, subtotal, status, dan penanda hutang. Hotel menekan Room Available (status Confirmed) atau Room Full dengan alasan (status Ditolak), dan menandai pembayaran sudah diterima (tanggal bayar dicatat). Halaman menampilkan total nilai dan total hutang, dan dapat diunduh sebagai PDF |
| Ijen | Rekap tiket Ijen untuk booking berstatus booked yang tanggal Ijen-nya (`at_bondowoso`) jatuh di bulan terpilih. Sumbernya aktivitas destinasi Ijen Ticket (ID 3 dan 144). Per tanggal Ijen ditampilkan total pax, total biaya, dan jumlah grup |
| Bromo | Rekap tiket dan jeep Bromo per booking untuk tanggal Bromo (`at_bromo`) di bulan terpilih. Sumbernya aktivitas destinasi Bromo Ticket (ID 1 dan 150), Bromo Jeep (ID 17 dan 151), Bromo Jeep TWT (ID 2), dan Bromo Jeep Student (ID 142). Dapat disaring menurut channel (JVTO, KLOOK, TWT) dan status hutang. Kolom: tamu, channel, hotel di Bromo, tanggal, jumlah pax (dari tiket), biaya tiket, unit jeep, biaya jeep, total, dan status hutang. Dapat diunduh sebagai PDF |
| Garasi | Item kendaraan expense (`BookCarActivity`) untuk kendaraan milik garasi (`garage_id = 1`) pada booking booked di bulan terpilih, dapat disaring menurut status hutang. Kolom: tamu, pax, channel (TWT atau JVTO), kendaraan, jumlah hari, tanggal mulai dan selesai, titik jemput, qty per hari, harga, subtotal, driver, catatan, dan status hutang. Garasi dapat mengisi driver dan catatan tiap baris (`update-driver`, `update-note`). Halaman memuat grafik sebaran kendaraan dan jumlah booking per tanggal, total, total hutang, dan PDF |
| Tshirt | Pesanan kaos menurut bulan, vendor (JVTO untuk `agent_id = 2`, termasuk Klook, atau TWT untuk `agent_id = 1`), dan status Proses atau Selesai. Tiap pesanan memuat tamu dan rincian ukuran (XSS sampai XXXL). Vendor mengubah status pesanan (`PUT /travel-tshirt/kubik/<id>`), dan halaman dapat diunduh sebagai PDF landscape |
| Medical Check Up | Jadwal pemeriksaan kesehatan di hotel Baratha Hotel, Grand Padis, dan Riverside Homestay untuk booking dengan tanggal Ijen. Tanggal pemeriksaan adalah sehari sebelum tanggal Ijen. Tiap baris memuat tamu, hotel, tanggal, jumlah pax, dan guide yang diplot beserta nomornya. Dapat disaring menurut rentang tanggal dan hotel |

## Aplikasi TWT

TWT punya aplikasi web sendiri (repo `twt`, `twt.javavolcano-touroperator.com`; Laravel dan Inertia/React). Di aplikasi ini TWT melihat jadwal booking-nya, JVTO menagih (submit invoice), dan TWT membayar serta mengunggah bukti pembayaran. Aplikasi memakai database booking yang sama dengan new-backoffice dan legacy, sehingga booking TWT yang dibuat oleh `twtExtractor` langsung tampil di sana.

### Akun

Login memakai username dan password milik tabel agen. Akun agen dengan ID 1 adalah TWT, dan akun agen dengan ID 2 adalah JVTO; tidak ada akun lain. Pada permintaan biaya tambahan, akun TWT mengisi permintaan awal dan akun JVTO melengkapinya (lihat Alur penagihan dan pembayaran).

### Menu

| Menu | Isi |
| --- | --- |
| Dashboard | Ringkasan booking TWT bulan terpilih: total booking, total pax, booking aktif, selesai, dan mendatang, beserta daftar trip dengan tanggal, jumlah pax, status, dan crew yang diplot (nama, peran, foto, rekap trip bulan itu) |
| Bookings (jadwal) | Booking-overview khusus channel TWT: booking berstatus booked menurut bulan atau rentang tanggal, dapat dicari menurut nama tamu. Kolom yang dapat ditampilkan: jemput, antar, itinerari, akomodasi, ukuran kaos, kendaraan dan crew, dan catatan. TWT dapat mengubah catatan booking (dengan opsi mengirim catatan itu ke grup WhatsApp), mengubah tautan Trip Media, dan mengunggah atau menghapus dokumen booking (PDF, JPG, PNG hingga 10 MB) |
| Hotel Check | TWT menanyakan ketersediaan kamar hotel sebelum booking (lihat di bawah) |
| Reference Data | Master Price (harga TWT untuk hotel, kamar, makan siang dan malam, aktivitas destinasi dan aktivitas lain yang ditandai untuk TWT, kendaraan, dan peran crew; dapat diekspor ke Excel) dan Partnership Agreement |
| Finance | Invoices, Invoice Revision, Supplemental Expense Requests, dan Financial Adjustments (lihat di bawah) |
| Crew SOP | Halaman SOP crew |

### Hotel Check

TWT mengisi nama tamu, tanggal mulai, jumlah pax, durasi, dan pilihan hotel serta kamar untuk tiap hari. Sistem membuat booking awal tanpa status (`agent_id = 1`) dengan itinerari, hotel, dan kamar (harga kamar memakai tarif TWT bila ada), lalu mengirim WhatsApp "Room Reservation" ke grup tiap hotel dan pesan "Waiting Hotel Confirmation" ke grup internal. Hotel menjawab Room Available atau Room Full di portal hotel (lihat Portal vendor), dan status tiap hari tampil di daftar Hotel Check: Waiting, Available, atau Room Full beserta alasannya.

### Alur penagihan dan pembayaran

| Langkah | Pelaku | Di sistem |
| --- | --- | --- |
| 1. Permintaan biaya tambahan | TWT | Di Supplemental Expense Requests, akun TWT mengisi item, qty, tanggal, dan peminta. Status `pending`; harga dan subtotal bernilai 0 |
| 2. Melengkapi biaya tambahan | JVTO | Akun JVTO mengisi harga, qty, foto, bill, dan tanggal submit; status `submitted`. Akun TWT tidak dapat mengubah status selain `pending` |
| 3. Refund dan penalti | JVTO | Di Financial Adjustments, item bertipe refund atau penalty dengan harga dan qty; status `pending` |
| 4. Revisi harga | JVTO | Di Invoice Revision: booking, item, qty, harga awal, harga revisi JVTO, total, dan dokumen bukti (PDF, JPG, PNG hingga 10 MB); status `pending`, lalu `approved` atau `rejected` |
| 5. Submit invoice | JVTO | Di Invoices > Create, JVTO memilih booking TWT yang belum ditagih (per bulan trip), biaya tambahan berstatus submitted, dan refund atau penalti berstatus pending. Nomor invoice otomatis berformat `JVR-INV-TWT-<tahun>-<bulan>-<urutan>` dan agennya TWT (ID 1). Status invoice `unpaid` |
| 6. Invoice terbit | Sistem | Booking di invoice ditandai `is_invoiced_twt = 1` dan `grand_total` booking diisi dengan nilai yang ditagihkan; biaya tambahan dan refund berubah menjadi `invoiced`. Total invoice = booking + biaya tambahan + refund dan penalti. Invoice dapat dilihat, diubah, diunduh sebagai PDF, atau dihapus (item dikembalikan ke status `submitted` atau `pending`) |
| 7. Pembayaran | TWT | Di halaman invoice, TWT mencatat pembayaran: tanggal, jumlah (tidak boleh melebihi sisa tagihan), file bukti (JPG, PNG, PDF, DOC, DOCX hingga 5 MB), dan catatan. Tanggal bayar disimpan di tiap booking pada invoice (`date_paid_invoiced_twt`) |
| 8. Status invoice | Sistem | Sisa tagihan nol menjadi `paid`; ada pembayaran tetapi masih ada sisa menjadi `paid_partially`; menghapus pembayaran menghitung ulang status |

Bukti pembayaran dapat diunduh dari halaman invoice, dan tiap pembayaran dapat dihapus.
