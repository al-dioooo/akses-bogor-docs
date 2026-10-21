# ADR-0020: Antarmuka Berbahasa Indonesia dengan String Siap I18n

- Status: Accepted
- Tanggal: 2026-10-21
- Pengambil keputusan: Tim AksesBogor
- Issue: #28

## Konteks dan Masalah

Copy di desain bercampur: menu dan judul berbahasa Inggris ("Home", "Explore", "Map View"), sedangkan tombol dan formulir berbahasa Indonesia ([PT-04](../product/pertanyaan-terbuka.md#pt-04-bahasa-antarmuka)). PRD sudah menempatkan antarmuka dwibahasa di luar MVP. Bahasa apa yang dipakai antarmuka, dan bagaimana teksnya disimpan di kode?

## Decision Drivers

- Pengguna sasaran adalah warga Kota Bogor, termasuk difabel dan pendamping.
- Pembaca layar membacakan teks dengan suara sesuai bahasa halaman.
- Bahasa sederhana membantu pengguna dengan disabilitas intelektual atau literasi rendah.
- Bahasa lain mungkin dibutuhkan setelah MVP tanpa menulis ulang komponen.

## Opsi yang Dipertimbangkan

1. Bahasa Indonesia penuh, string disimpan di berkas pesan (siap i18n)
2. Bahasa Indonesia penuh, string ditulis langsung di komponen
3. Dwibahasa Indonesia dan Inggris sejak peluncuran

## Keputusan

Opsi terpilih: "Bahasa Indonesia penuh, string disimpan di berkas pesan".

### Bahasa

- Semua teks antarmuka, pesan galat, email, dan halaman hukum berbahasa Indonesia.
- Bahasa sederhana: kalimat pendek, kata sehari-hari, tanpa singkatan yang tidak umum. Pesan galat menjelaskan cara memperbaikinya.
- Istilah yang lebih dikenal pengguna dipertahankan: ramp (ditulis "Ramp / Jalur Landai" di formulir), guiding block, dan lift.
- Antarmuka memakai kata "difabel", sesuai desain dan yang umum dipakai komunitas. Halaman hukum memakai "penyandang disabilitas", istilah di UU No. 8 Tahun 2016. Pilihan ini dicek ulang bersama mitra komunitas saat uji kegunaan sebelum beta.

### Label navigasi

| Desain sekarang | Menjadi |
| --- | --- |
| Home | Beranda |
| Explore, Explore Lokasi | Jelajahi, Jelajahi Lokasi |
| Map, Map View | Peta |
| Accessibility | Aksesibilitas |
| About, About Us | Tentang |
| Privacy Policy | Kebijakan Privasi |
| Terms of Service | Ketentuan Layanan |

"Difabel" dan "Tips" tetap.

### Di kode

- String disimpan di berkas pesan per bahasa, dibaca lewat pustaka i18n untuk Next.js. Pustakanya dipilih saat implementasi.
- Tag `html` memakai `lang="id"`.
- Tanggal, angka, dan jam diformat dengan locale `id-ID`, misalnya "21 Oktober 2026", "1.250", dan "14.30 WIB".
- Tidak ada pemilih bahasa di MVP.

## Konsekuensi

- Positif: satu bahasa yang konsisten untuk pengguna dan pembaca layar.
- Positif: menambah bahasa lain nanti cukup menambah berkas pesan dan pemilih bahasa.
- Negatif: sedikit kerja tambahan di awal untuk menyiapkan berkas pesan.
- Negatif: pengunjung yang tidak berbahasa Indonesia belum terlayani.
- Tindak lanjut: label navigasi, footer, dan judul di Figma diganti ke bahasa Indonesia.

## Pro & Kontra tiap Opsi

### Indonesia penuh, siap i18n

- Pro: konsisten sekarang, mudah diperluas nanti.
- Kontra: sedikit kerja tambahan di awal.

### Indonesia penuh, string di komponen

- Pro: paling cepat ditulis.
- Kontra: menambah bahasa berarti mengubah setiap komponen.

### Dwibahasa sejak peluncuran

- Pro: menjangkau wisatawan dan warga asing.
- Kontra: copy, review terjemahan, dan uji pembaca layar menjadi dua kali; bertentangan dengan non-tujuan di PRD.

## Referensi

- [Pertanyaan Terbuka PT-04](../product/pertanyaan-terbuka.md#pt-04-bahasa-antarmuka)
- [ADR-0002](0002-bahasa-indonesia-untuk-dokumentasi.md), [ADR-0021](0021-wcag-22-aa-dan-pengujian-berlapis.md)
- [PRD](../product/prd.md), bagian 3 dan 7
