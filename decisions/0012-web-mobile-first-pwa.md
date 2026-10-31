# ADR-0012: Web Mobile-first yang Bisa Dipasang (PWA)

- Status: Accepted
- Tanggal: 2026-10-21
- Pengambil keputusan: Tim AksesBogor
- Issue: #13

## Konteks dan Masalah

Semua layar desain berukuran desktop 1440 px ([PT-06](../product/pertanyaan-terbuka.md#pt-06-tampilan-mobile)), padahal pengguna kemungkinan besar mengecek aksesibilitas saat bepergian dan melapor langsung di lokasi dengan kamera ponsel. Platform apa yang dibangun lebih dulu?

## Decision Drivers

- Pengguna mengakses dari ponsel, sering di jalan dan dengan koneksi seluler.
- Melapor di lokasi membutuhkan kamera dan lokasi perangkat.
- Satu codebase dan tanpa biaya toko aplikasi (proyek kampus).
- Harus bekerja baik dengan pembaca layar bawaan ponsel (TalkBack, VoiceOver).

## Opsi yang Dipertimbangkan

1. Web responsif mobile-first yang bisa dipasang sebagai PWA
2. Web desktop dulu, mobile menyusul
3. Aplikasi native Android dan iOS

## Keputusan

Opsi terpilih: "Web responsif mobile-first yang bisa dipasang sebagai PWA". Desain dan pengembangan dimulai dari lebar 360 px, lalu melebar ke tablet dan desktop. Frame desktop yang sudah ada menjadi acuan breakpoint lebar.

## Konsekuensi

- Positif: satu codebase untuk semua perangkat; tanpa proses toko aplikasi.
- Positif: kamera (unggah foto) dan lokasi perangkat tersedia lewat API browser, dengan izin pengguna.
- Negatif: kedelapan layar perlu desain mobile (360–430 px); frame DS v1 di Figma baru tersedia untuk desktop.
- Negatif: blur latar berat di ponsel kelas bawah, sehingga mode Solid juga dipakai sebagai fallback performa.
- Wajib: target sentuh minimal 44 × 44 px di mobile. Tombol Small (tinggi 36 px) butuh area sentuh yang diperluas.
- Netral: dukungan pemasangan PWA di iOS lebih terbatas dibanding Android, tetapi situs tetap bisa dipakai lewat browser.

## Pro & Kontra tiap Opsi

### Web mobile-first (PWA)

- Pro: menjangkau semua perangkat; murah dirawat; mudah dibagikan lewat tautan.
- Kontra: akses fitur perangkat terbatas dibanding native; performa efek kaca perlu dijaga.

### Web desktop dulu

- Pro: desain desktop sudah siap.
- Kontra: tidak cocok dengan cara pengguna mengakses dan melapor; mobile jadi pekerjaan ulang.

### Aplikasi native

- Pro: akses penuh ke fitur perangkat dan performa terbaik.
- Kontra: dua codebase, biaya toko aplikasi, dan hambatan unduh bagi pengguna sekali lewat.

## Referensi

- [Pertanyaan Terbuka PT-06](../product/pertanyaan-terbuka.md#pt-06-tampilan-mobile)
- [ADR-0009](0009-design-system-kaca-ungu-inklusi.md)
- [WCAG 2.2, kriteria 2.5.8 Target Size (Minimum)](https://www.w3.org/TR/WCAG22/#target-size-minimum)
