# ADR-0011: Pelapor Wajib Login

- Status: Accepted
- Tanggal: 2026-10-21
- Pengambil keputusan: Tim AksesBogor
- Issue: #13

## Konteks dan Masalah

Layar Laporan Berhasil menjanjikan notifikasi saat lokasi dipublikasikan, dan Landing menampilkan jumlah "Kontributor Aktif", tetapi desain tidak punya layar login dan formulir tidak meminta kontak ([PT-03](../product/pertanyaan-terbuka.md#pt-03-akun-pengguna-dan-notifikasi)). Apakah pelapor perlu akun?

## Decision Drivers

- Laporan palsu dan spam harus bisa dicegah dan ditelusuri.
- Janji notifikasi butuh kontak pelapor yang tervalidasi.
- Verifikator perlu bisa menghubungi pelapor untuk klarifikasi.
- Data pribadi harus dikelola sesuai UU No. 27 Tahun 2022 tentang Pelindungan Data Pribadi.

## Opsi yang Dipertimbangkan

1. Laporan anonim dengan email opsional untuk notifikasi
2. Wajib login sebelum melapor
3. Laporan anonim tanpa notifikasi

## Keputusan

Opsi terpilih: "Wajib login sebelum melapor".

- Login wajib untuk membuat laporan baru, mengedit lokasi, dan melaporkan kekurangan.
- Menjelajah (Beranda, Explore, Peta, Detail) tetap terbuka tanpa login.
- Metode login (mis. kode sekali pakai lewat email atau akun Google) diputuskan bersama tech stack ([PT-11](../product/pertanyaan-terbuka.md#pt-11-tech-stack-produksi)).

## Konsekuensi

- Positif: laporan bisa ditelusuri ke akun, spam lebih mudah ditangani, dan notifikasi bisa dikirim.
- Positif: statistik "Kontributor Aktif" bisa dihitung dari akun nyata.
- Negatif: ada hambatan tambahan bagi pelapor sekali lewat, sehingga jumlah laporan bisa lebih sedikit.
- Negatif: perlu layar daftar, masuk, profil, dan hapus akun, yang belum didesain.
- Negatif: Privacy Policy dan persetujuan pemrosesan data wajib ada sebelum peluncuran.
- Wajib: proses login harus aksesibel. Tidak boleh ada tes kognitif seperti CAPTCHA teka-teki tanpa alternatif (WCAG 2.2 kriteria 3.3.8), dan pengelola kata sandi serta tempel kode harus didukung.

## Pro & Kontra tiap Opsi

### Anonim dengan email opsional

- Pro: hambatan paling rendah; data pribadi minimal.
- Kontra: spam lebih sulit dicegah; email tidak tervalidasi.

### Wajib login

- Pro: akuntabilitas, notifikasi andal, dan kontributor terhitung.
- Kontra: hambatan lebih tinggi; kewajiban pelindungan data lebih besar.

### Anonim tanpa notifikasi

- Pro: paling sederhana.
- Kontra: janji notifikasi di desain harus dihapus; spam sulit ditangani.

## Referensi

- [Pertanyaan Terbuka PT-03](../product/pertanyaan-terbuka.md#pt-03-akun-pengguna-dan-notifikasi)
- [ADR-0006](0006-laporan-warga-diverifikasi-sebelum-tayang.md)
- UU No. 27 Tahun 2022 tentang Pelindungan Data Pribadi
- [WCAG 2.2, kriteria 3.3.8 Accessible Authentication (Minimum)](https://www.w3.org/TR/WCAG22/#accessible-authentication-minimum)
