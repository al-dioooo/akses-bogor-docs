# Decision Log (ADR)

Folder ini berisi catatan keputusan proyek AksesBogor dalam format [MADR](https://adr.github.io/madr/). Satu file berisi satu keputusan. Cakupannya semua jenis keputusan: produk, desain, teknis, dan proses.

## Kapan Menulis ADR

- Keputusan sulit dibalik atau berdampak ke lebih dari satu orang.
- Ada beberapa opsi yang masuk akal dan alasan memilih perlu diingat.
- Keputusan sudah tercermin di desain atau kode tetapi belum tercatat. Tulis secara retroaktif dan tandai demikian.

## Cara Menulis

1. Buat issue dengan template "Usulan keputusan (ADR)".
2. Salin [template.md](template.md) menjadi `NNNN-judul-singkat.md`, dengan `NNNN` nomor urut berikutnya (4 digit) dan judul dalam kebab-case.
3. Isi semua bagian. Status awal `Proposed`.
4. Buka pull request. Diskusi keputusan terjadi di PR.
5. Sebelum merge, ubah status menjadi `Accepted` atau `Rejected` sesuai hasil review.
6. Tambahkan baris di [index](#index).

## Status

| Status | Arti |
| --- | --- |
| `Proposed` | Diusulkan, belum diputuskan |
| `Accepted` | Disetujui dan berlaku |
| `Rejected` | Ditolak; tetap disimpan sebagai catatan |
| `Deprecated` | Tidak berlaku lagi, tanpa pengganti |
| `Superseded` | Digantikan ADR lain (tautkan penggantinya) |

ADR yang sudah `Accepted` tidak diubah isinya. Mengubah keputusan berarti menulis ADR baru yang men-supersede ADR lama, lalu memperbarui status ADR lama. Perbaikan typo dan link tetap boleh.

## Index

| No | Judul | Status | Tanggal |
| --- | --- | --- | --- |
| [0001](0001-mencatat-keputusan-dengan-madr.md) | Mencatat keputusan proyek dengan ADR format MADR | Accepted | 2026-10-21 |
| [0002](0002-bahasa-indonesia-untuk-dokumentasi.md) | Bahasa Indonesia sebagai bahasa dokumentasi | Accepted | 2026-10-21 |
| [0003](0003-github-issues-dan-alur-kontribusi.md) | GitHub Issues sebagai ticket tracker dan alur kontribusi repo docs | Accepted | 2026-10-21 |
| [0004](0004-empat-kategori-lokasi-untuk-mvp.md) | Empat kategori lokasi untuk MVP | Accepted | 2026-10-21 |
| [0005](0005-empat-fasilitas-aksesibilitas.md) | Empat fasilitas aksesibilitas dicatat sebagai tersedia atau tidak | Accepted | 2026-10-21 |
| [0006](0006-laporan-warga-diverifikasi-sebelum-tayang.md) | Laporan warga lewat formulir tiga langkah, diverifikasi sebelum tayang | Accepted | 2026-10-21 |
| [0007](0007-fondasi-visual-dari-desain-figma.md) | Fondasi visual mengikuti desain Figma, kecuali warna status | Superseded oleh 0009 | 2026-10-21 |
| [0008](0008-peta-leaflet-dan-openstreetmap.md) | Peta interaktif dengan Leaflet dan tile berbasis OpenStreetMap | Superseded oleh 0016 | 2026-10-21 |
| [0009](0009-design-system-kaca-ungu-inklusi.md) | Design system kaca dengan palet Ungu Inklusi | Accepted | 2026-10-21 |
| [0010](0010-cakupan-wilayah-kota-bogor.md) | Cakupan wilayah MVP adalah Kota Bogor | Accepted | 2026-10-21 |
| [0011](0011-pelapor-wajib-login.md) | Pelapor wajib login | Accepted | 2026-10-21 |
| [0012](0012-web-mobile-first-pwa.md) | Web mobile-first yang bisa dipasang (PWA) | Accepted | 2026-10-21 |
| [0013](0013-tech-stack-nextjs-fastify.md) | Tech stack Next.js + Fastify, Neon, Azure, dan Cloudflare R2 | Accepted | 2026-10-21 |
| [0014](0014-login-google-dan-kode-email.md) | Login dengan Google atau kode email | Accepted | 2026-10-21 |
| [0015](0015-input-lokasi-peta-gps-pencarian.md) | Lokasi laporan diisi lewat peta, lokasi perangkat, atau pencarian | Accepted | 2026-10-21 |
| [0016](0016-peta-dan-pencarian-mapbox.md) | Peta dan pencarian lokasi memakai Mapbox | Accepted | 2026-10-21 |
