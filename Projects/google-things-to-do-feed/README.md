---
type: project
status: active
owner: David
department: Teknologi
aliases: [Google Things to Do Feed]
tags: [google, channel, direct-booking]
created: 2026-11-04
updated: 2026-11-04
---
## Overview
Feed produk JVTO ke Google Things to Do, terhubung dengan Google Business Profile ID. Tujuannya: tur JVTO tampil di Google sebagai kanal booking direct.

## Status
Feed live sejak 2026-10-31 di `/api/things-to-do-feed` (17 produk, sama persis dengan sumber ekosistem), tapi Google belum memakainya. Yang perlu dikoreksi: format menyimpang dari spec, `refund_percent 100` padahal kebijakannya credit, rating beku. `robots.ts` melarang `/api/` sehingga feed ikut terblokir bagi crawler.

## Next Steps
- Sesuaikan format feed dengan spec Google dan koreksi `refund_percent`.
- Pastikan Google bisa mengambil feed.

Mendukung O1 di [[strategy]].
