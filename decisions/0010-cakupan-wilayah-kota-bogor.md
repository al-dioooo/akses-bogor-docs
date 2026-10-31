# ADR-0010: Cakupan Wilayah MVP adalah Kota Bogor

- Status: Accepted
- Tanggal: 2026-10-21
- Pengambil keputusan: Tim AksesBogor
- Issue: #13

## Konteks dan Masalah

Desain awal tidak konsisten soal wilayah: nama produk dan ajakan "Bantu Kami Memetakan Aksesibilitas di Kota Bogor" bertentangan dengan copy "fasilitas publik di Indonesia", peta yang berpusat di Jakarta, dan lokasi contoh di Depok serta Jakarta ([PT-01](../product/pertanyaan-terbuka.md#pt-01-cakupan-wilayah)). Wilayah mana yang dicakup MVP?

## Decision Drivers

- Nama produk "AksesBogor" harus sesuai dengan isinya.
- Tim kampus harus sanggup memverifikasi setiap laporan.
- Data harus cukup padat agar produk berguna sejak awal.
- Batas wilayah administratif yang jelas memudahkan kerja sama dengan pemerintah kota dan komunitas.

## Opsi yang Dipertimbangkan

1. Kota Bogor
2. Bogor Raya (Kota dan Kabupaten Bogor)
3. Jabodetabek
4. Seluruh Indonesia

## Keputusan

Opsi terpilih: "Kota Bogor", yaitu enam kecamatan: Bogor Barat, Bogor Selatan, Bogor Tengah, Bogor Timur, Bogor Utara, dan Tanah Sareal. Model data tetap menyimpan kota dan kecamatan setiap lokasi agar wilayah bisa diperluas lewat ADR baru.

## Konsekuensi

- Positif: nama, copy, dan isi produk selaras; verifikasi realistis untuk tim kecil.
- Positif: data lebih padat per area, sehingga peta dan Explore langsung terasa berguna.
- Negatif: copy "di Indonesia" di Landing, Explore, dan footer harus diganti "di Kota Bogor".
- Negatif: peta dibuka dengan pusat dan batas Kota Bogor; filter "Kota" di Explore diganti "Kecamatan".
- Negatif: semua lokasi contoh di desain (Jakarta, Depok, Bandung) harus diganti data Kota Bogor.
- Negatif: laporan untuk tempat di luar Kota Bogor perlu ditolak dengan pesan yang jelas. Kampus IPB Dramaga, misalnya, berada di Kabupaten Bogor sehingga di luar cakupan MVP.

## Pro & Kontra tiap Opsi

### Kota Bogor

- Pro: sesuai nama produk; wilayah kecil dan jelas batasnya.
- Kontra: sebagian tempat yang sering dikunjungi warga Bogor (mis. kampus di Dramaga) berada di luar cakupan.

### Bogor Raya

- Pro: mencakup lebih banyak tempat yang relevan bagi warga.
- Kontra: wilayah Kabupaten jauh lebih luas, sehingga beban verifikasi berlipat.

### Jabodetabek

- Pro: cocok dengan data contoh di desain awal.
- Kontra: nama "AksesBogor" tidak lagi tepat; data tersebar tipis.

### Seluruh Indonesia

- Pro: cocok dengan sebagian copy desain.
- Kontra: tidak realistis untuk diverifikasi dan diisi data awal oleh tim kampus.

## Referensi

- [Pertanyaan Terbuka PT-01](../product/pertanyaan-terbuka.md#pt-01-cakupan-wilayah)
- [Referensi Figma: Data Placeholder](../product/referensi-figma.md#data-placeholder)
