---
type: project
status: active
owner: David
department: Teknologi
aliases: [Tour PDP v2]
tags: [website, tour-page]
created: 2026-11-04
updated: 2026-11-04
---
## Overview
Template halaman detail tur (PDP) versi 2 untuk 17 tur di [[jvto-web]], datanya dari [[jvto-ekosistem]].

## Status
Belum live. Riwayat:
- 2026-10-31: PDP v2 sempat diterapkan ke 17 tur lalu di-rollback ke situs 2026-10-25 (commit `8069b6df`).
- 2026-11-03: port template desain baru ("skin", kit "Berkas Lapangan") sempat live lalu di-revert atas keputusan owner (`b0bf1e3d`). Penyempurnaan lanjut di branch `design/skin-v2`.
- Bug konversi terkait: tombol **Skip** di modal add-on PDP hanya menutup modal, tidak lanjut ke checkout (`PDP_ADDON_MODAL_SKIP_DEAD_END`).

## Next Steps
- [[David]] perbaiki tombol Skip add-on (berlaku untuk PDP sekarang juga).
- Owner putuskan kapan skin v2 dan PDP v2 dicoba lagi.

Mendukung O1 di [[strategy]].
