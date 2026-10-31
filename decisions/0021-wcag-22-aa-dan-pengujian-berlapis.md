# ADR-0021: WCAG 2.2 Level AA sebagai Syarat Rilis, dengan Pengujian Berlapis

- Status: Accepted
- Tanggal: 2026-10-21
- Pengambil keputusan: Tim AksesBogor
- Issue: #28

## Konteks dan Masalah

AksesBogor menjanjikan informasi aksesibilitas, sehingga produknya sendiri harus bisa dipakai difabel. Kontras warna sudah diselesaikan di [ADR-0009](0009-design-system-kaca-ungu-inklusi.md), tetapi standar yang menjadi syarat rilis dan cara pengujiannya belum ditetapkan ([PT-05](../product/pertanyaan-terbuka.md#pt-05-target-aksesibilitas-antarmuka)). UU No. 8 Tahun 2016 tentang Penyandang Disabilitas menjamin hak atas aksesibilitas dan informasi. Standar apa yang dipakai, dan bagaimana kepatuhannya diperiksa?

## Decision Drivers

- Kegagalan aksesibilitas merusak kepercayaan pada produk aksesibilitas.
- Standar harus bisa diuji, bukan sekadar niat.
- Kebanyakan pengguna di Indonesia memakai ponsel Android.
- Biaya pengujian harus sebanding dengan tim kecil.

## Opsi yang Dipertimbangkan

1. WCAG 2.2 level AA sebagai syarat rilis, dengan pengujian berlapis
2. WCAG 2.1 level AA
3. Upaya terbaik tanpa standar resmi

## Keputusan

Opsi terpilih: "WCAG 2.2 level AA, dengan pengujian berlapis". Banyak kriteria baru di WCAG 2.2 menyentuh keputusan yang sudah diambil, sehingga WCAG 2.1 akan melewatkannya.

### Standar

Semua halaman MVP memenuhi WCAG 2.2 level AA, ditambah target sentuh minimal 44 × 44 px yang sudah ada di PRD. Kriteria yang paling berkaitan:

| Kriteria | Penerapan di AksesBogor |
| --- | --- |
| 2.5.7 Gerakan menyeret | Pin lokasi bisa diatur tanpa menyeret: pencarian, lokasi perangkat, tombol arah ([ADR-0015](0015-input-lokasi-peta-gps-pencarian.md)) |
| 2.5.8 Ukuran target | Target minimal 44 × 44 px, di atas syarat 24 px |
| 2.4.11 Fokus tidak tertutup | Nav bar kaca dan panel mengambang tidak menutupi elemen yang sedang difokus |
| 3.3.7 Isian berulang | Formulir tiga langkah menyimpan isian saat kembali ke langkah sebelumnya atau setelah login (FR-20) |
| 3.3.8 Autentikasi yang aksesibel | Kolom kode email menerima tempel dan isi otomatis (`autocomplete="one-time-code"`), tanpa tes kognitif ([ADR-0014](0014-login-google-dan-kode-email.md)) |
| 1.4.1 Penggunaan warna | Status selalu berupa teks, ikon, dan warna ([ADR-0009](0009-design-system-kaca-ungu-inklusi.md)) |

Peta Mapbox tidak sepenuhnya terbaca pembaca layar. Tampilan "Lihat sebagai daftar" memuat informasi dan fungsi yang sama, sehingga menjadi alternatif yang memenuhi syarat ([ADR-0016](0016-peta-dan-pencarian-mapbox.md)).

### Lapisan pengujian

1. **Otomatis di CI** repo web: lint aksesibilitas untuk JSX, dan pemeriksaan axe-core dengan Playwright pada halaman dan alur utama. Pelanggaran tingkat serious atau critical menggagalkan build.
2. **Setiap PR** yang mengubah UI: daftar periksa manual di template PR, berisi navigasi keyboard, fokus terlihat dan tidak tertutup, nama dan label, tampilan di lebar 320 px dan zoom 200%, status teks + ikon + warna, serta pengurangan gerak.
3. **Setiap rilis**: alur A sampai D di PRD diuji dengan TalkBack + Chrome di Android (prioritas), NVDA di Windows, dan VoiceOver + Safari di iOS (cek singkat).
4. **Audit** seluruh kriteria WCAG 2.2 AA sebelum alpha dan sebelum beta. Rilis ditunda bila ada temuan level A atau AA yang kritis.
5. **Uji kegunaan** bersama mitra komunitas difabel sebelum beta, dengan peserta netra, low vision, pengguna kursi roda, dan Tuli. Peserta diberi honor.

### Tanggung jawab dan keterbukaan

- Satu orang di tim ditunjuk sebagai penanggung jawab aksesibilitas untuk setiap rilis.
- Halaman "Pernyataan Aksesibilitas" menyebut standar, cara pengujian, tanggal audit terakhir, keterbatasan yang diketahui (termasuk peta), dan cara melaporkan kendala.

## Konsekuensi

- Positif: syarat rilis jelas dan bisa diuji.
- Positif: masalah tertangkap lebih awal di CI dan PR, bukan saat audit.
- Negatif: setiap rilis butuh waktu untuk uji pembaca layar dan audit.
- Negatif: butuh perangkat Android dan iOS untuk pengujian, serta anggaran honor peserta.
- Tindak lanjut: repo web menyiapkan lint dan pemeriksaan axe di CI serta daftar periksa aksesibilitas di template PR. Desain mobile mengikuti kriteria di atas.

## Pro & Kontra tiap Opsi

### WCAG 2.2 AA, pengujian berlapis

- Pro: mencakup gerakan menyeret, ukuran target, dan autentikasi yang langsung relevan.
- Kontra: pengujian manual setiap rilis.

### WCAG 2.1 AA

- Pro: alat dan panduan sedikit lebih matang.
- Kontra: melewatkan kriteria 2.2 yang relevan dengan peta, formulir, dan login.

### Upaya terbaik

- Pro: paling murah.
- Kontra: tidak ada syarat rilis; risiko besar untuk produk aksesibilitas.

## Referensi

- [Pertanyaan Terbuka PT-05](../product/pertanyaan-terbuka.md#pt-05-target-aksesibilitas-antarmuka)
- [WCAG 2.2](https://www.w3.org/TR/WCAG22/)
- [ADR-0009](0009-design-system-kaca-ungu-inklusi.md), [ADR-0014](0014-login-google-dan-kode-email.md), [ADR-0015](0015-input-lokasi-peta-gps-pencarian.md), [ADR-0016](0016-peta-dan-pencarian-mapbox.md)
- [PRD](../product/prd.md), bagian 7 dan 10
