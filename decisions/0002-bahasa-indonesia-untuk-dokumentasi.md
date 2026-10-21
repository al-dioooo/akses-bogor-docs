# ADR-0002: Bahasa Indonesia sebagai Bahasa Dokumentasi

- Status: Accepted
- Tanggal: 2026-10-21
- Pengambil keputusan: Tim AksesBogor
- Issue: #2

## Konteks dan Masalah

Tim dan pengguna AksesBogor berbahasa Indonesia. Guideline tim ditulis campuran Indonesia–Inggris, dan copy di desain Figma juga campuran ("Explore Locations" dan "Accessibility Score" di samping "Laporkan Tempat"). Repo docs butuh satu aturan bahasa agar dokumen konsisten dan mudah dicari.

## Decision Drivers

- Mudah dipahami seluruh tim dan pemangku kepentingan non-teknis (mis. dosen pembimbing, komunitas difabel).
- Istilah teknis tetap dikenali dan tidak diterjemahkan secara paksa.
- Konsisten dengan Conventional Commits dan contoh di guideline tim.

## Opsi yang Dipertimbangkan

1. Bahasa Indonesia, dengan istilah teknis tetap dalam bahasa Inggris
2. Seluruhnya bahasa Inggris
3. Campuran per jenis dokumen (produk/UX dalam bahasa Indonesia, teknis dalam bahasa Inggris)

## Keputusan

Opsi terpilih: "Bahasa Indonesia, dengan istilah teknis tetap dalam bahasa Inggris", karena seluruh pembaca utama berbahasa Indonesia dan istilah teknis bahasa Inggris sudah lazim dipakai tim.

| Bagian | Bahasa |
| --- | --- |
| Isi dokumen (README, ADR, referensi produk) | Indonesia |
| Istilah teknis (ramp, guiding block, pull request, endpoint, design token) | Inggris, tidak diterjemahkan |
| Commit message, nama branch, judul PR | Inggris (Conventional Commits, mengikuti contoh guideline) |
| Heading template PR | Inggris, sesuai PR guideline |
| Nama folder | Inggris (`decisions/`, `guidelines/`, `product/`, `design/`) |
| Nama file dokumen | Indonesia, kebab-case, mengikuti judul dokumen |
| Guideline yang diimpor | Bahasa aslinya, tidak diterjemahkan |

Keputusan ini hanya berlaku untuk dokumentasi. Bahasa antarmuka aplikasi diputuskan secara terpisah.

## Konsekuensi

- Positif: dokumen mudah dibaca seluruh tim dan mitra lokal.
- Negatif: kontributor yang tidak berbahasa Indonesia membutuhkan terjemahan.
- Negatif: perlu disiplin memilih antara istilah Inggris dan padanan Indonesia; aturannya, bila istilah Inggris lebih umum dipakai tim, gunakan istilah Inggris.

## Pro & Kontra tiap Opsi

### Bahasa Indonesia dengan istilah teknis Inggris

- Pro: paling mudah dipahami tim dan pemangku kepentingan lokal.
- Pro: selaras dengan bahasa produk dan sebagian besar guideline.
- Kontra: membatasi kontributor internasional.

### Seluruhnya bahasa Inggris

- Pro: terbuka untuk kontributor internasional dan selaras dengan istilah teknis.
- Kontra: menyulitkan pembaca non-teknis lokal; tidak selaras dengan bahasa produk.

### Campuran per jenis dokumen

- Pro: tiap dokumen memakai bahasa yang paling cocok untuk pembacanya.
- Kontra: batas "produk" dan "teknis" sering kabur (mis. ADR peta), sehingga aturan sulit diterapkan konsisten.

## Referensi

- [ADR-0001](0001-mencatat-keputusan-dengan-madr.md)
- [Github Repository Guideline](../guidelines/github-repository-guideline.md), bagian 2 "Commit Guidelines"
