# ADR-0006: Laporan Warga Lewat Formulir Tiga Langkah, Diverifikasi Sebelum Tayang

- Status: Accepted
- Tanggal: 2026-10-21
- Pengambil keputusan: Tim AksesBogor
- Issue: #5

> Dicatat retroaktif dari desain Figma. Opsi alternatif di bawah adalah analisis penulis, bukan catatan diskusi tim.

## Konteks dan Masalah

Data lokasi AksesBogor berasal dari laporan warga. Tombol "Laporkan Tempat" ada di header setiap halaman dan di ajakan Landing. Setelah laporan dikirim, layar Laporan Berhasil menjelaskan bahwa tim akan memverifikasi laporan, lokasi baru muncul di peta setelah diverifikasi, dan pelapor mendapat notifikasi saat lokasi dipublikasikan. Halaman Detail juga punya tombol "Edit Lokasi" dan "Laporkan Kekurangan". Bagaimana data dari warga masuk dan kapan data itu ditayangkan?

## Decision Drivers

- Informasi yang salah bisa membuat pengguna difabel terjebak di lokasi yang ternyata tidak aksesibel *(asumsi)*, jadi akurasi lebih penting daripada kecepatan tayang.
- Warga perlu bisa berkontribusi dengan mudah.
- Spam dan laporan palsu harus bisa dicegah.

## Opsi yang Dipertimbangkan

1. Laporan warga, diverifikasi tim sebelum tayang
2. Laporan warga langsung tayang dengan label "belum terverifikasi"
3. Data hanya diisi tim, tanpa laporan warga

## Keputusan

Opsi terpilih: "Laporan warga, diverifikasi tim sebelum tayang", sesuai desain.

Alur formulir:

1. **Informasi**: Nama Tempat (wajib), Kategori (wajib, lihat [ADR-0004](0004-empat-kategori-lokasi-untuk-mvp.md)).
2. **Fasilitas**: fasilitas yang tersedia (lihat [ADR-0005](0005-empat-fasilitas-aksesibilitas.md)).
3. **Detail**: foto (PNG/JPG, maksimal 5 MB, tidak bertanda wajib), deskripsi tambahan, dan ringkasan sebelum "Kirim Laporan".

Laporan tidak tampil di peta atau Explore sebelum diverifikasi. Perubahan lewat "Edit Lokasi" dan "Laporkan Kekurangan" diasumsikan melewati verifikasi yang sama *(asumsi, alurnya belum didesain)*.

## Konsekuensi

- Positif: data yang tayang lebih tepercaya.
- Positif: formulir pendek dan bertahap, sehingga beban pelapor rendah.
- Negatif: butuh kapasitas tim verifikator dan alat admin yang belum didesain. Lihat [PT-09](../product/pertanyaan-terbuka.md#pt-09-proses-verifikasi-dan-moderasi).
- Negatif: pelapor harus menunggu sebelum laporannya terlihat.
- Negatif: janji notifikasi membutuhkan kontak pelapor, padahal belum ada akun. Lihat [PT-03](../product/pertanyaan-terbuka.md#pt-03-akun-pengguna-dan-notifikasi).
- Negatif: formulir belum meminta lokasi atau kota. Lihat [PT-07](../product/pertanyaan-terbuka.md#pt-07-input-lokasi-pada-formulir-laporan).

## Pro & Kontra tiap Opsi

### Diverifikasi sebelum tayang

- Pro: akurasi terjaga; spam tidak pernah tayang.
- Kontra: butuh tim dan alat moderasi; ada jeda sebelum data tampil.

### Langsung tayang dengan label

- Pro: data cepat bertambah; tidak ada antrean verifikasi.
- Kontra: pengguna bisa mengandalkan data yang salah; spam terlihat publik.

### Hanya diisi tim

- Pro: akurasi paling tinggi.
- Kontra: cakupan tumbuh lambat; bertentangan dengan ajakan "Bantu Kami Memetakan Aksesibilitas".

## Referensi

- [Laporkan langkah 1–3 (Figma 13:812, 13:1002, 13:1207)](https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=13-812)
- [Laporan Berhasil (Figma 13:1412)](https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=13-1412)
- [Detail (Figma 13:1578)](https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=13-1578)
