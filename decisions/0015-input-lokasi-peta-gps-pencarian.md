# ADR-0015: Lokasi Laporan Diisi lewat Peta, Lokasi Perangkat, atau Pencarian

- Status: Accepted
- Tanggal: 2026-10-21
- Pengambil keputusan: Tim AksesBogor
- Issue: #20

## Konteks dan Masalah

Langkah 1 formulir laporan di desain hanya meminta nama dan kategori, padahal peta, cek batas Kota Bogor ([ADR-0010](0010-cakupan-wilayah-kota-bogor.md), FR-22), filter kecamatan, dan cek laporan ganda (FR-23) membutuhkan koordinat ([PT-07](../product/pertanyaan-terbuka.md#pt-07-input-lokasi-pada-formulir-laporan)). Pelapor bisa berada di lokasi atau melapor belakangan dari rumah, dan sebagian pengguna tidak bisa menyeret pin di peta. Bagaimana pelapor menentukan lokasi?

## Decision Drivers

- Berfungsi saat pelapor berada di lokasi maupun saat melapor belakangan.
- Ada cara tanpa menyeret untuk pengguna pembaca layar dan pengguna dengan hambatan motorik (WCAG 2.2 kriteria 2.5.7 dan 2.1.1).
- Titik cukup tepat untuk menunjuk pintu masuk yang aksesibel, bukan hanya jalan di depannya.
- Lokasi pelapor sendiri tidak ikut tersimpan.

## Opsi yang Dipertimbangkan

1. Peta + lokasi perangkat + pencarian alamat
2. Pin di peta saja
3. Lokasi perangkat (GPS) saja
4. Pencarian alamat saja

## Keputusan

Opsi terpilih: "Peta + lokasi perangkat + pencarian alamat".

1. **Peta dengan pin tetap di tengah.** Pelapor menggeser peta, atau memakai tombol arah dan zoom, sampai pin berada di titik yang benar.
2. **"Gunakan lokasi saya"** membaca lokasi perangkat sekali saat tombol ditekan, memusatkan peta, dan menampilkan tingkat akurasinya.
3. **Pencarian nama tempat atau alamat** menampilkan daftar hasil yang bisa dipilih dengan keyboard dan pembaca layar. Memilih hasil hanya menggeser peta.
4. **Konfirmasi.** Pelapor menekan "Pakai lokasi ini". Koordinat yang disimpan selalu posisi pin yang dikonfirmasi, bukan hasil pencarian atau GPS mentah.
5. **Kecamatan dihitung otomatis** dari koordinat dengan PostGIS dan data batas kecamatan Kota Bogor (mis. dari OpenStreetMap atau Badan Informasi Geospasial). Titik di luar Kota Bogor ditolak dengan pesan yang menjelaskan cakupan wilayah.
6. **Cek laporan ganda.** Lokasi terdaftar dalam radius sekitar 50 m dengan nama mirip ditampilkan sebelum lanjut, beserta pilihan untuk melaporkan kekurangan pada lokasi itu.
7. **Patokan opsional**, teks pendek seperti "pintu samping dekat parkir motor", untuk membantu verifikator dan pengunjung.
8. **Privasi.** Lokasi perangkat hanya dibaca saat tombol ditekan dan tidak disimpan sebagai lokasi pelapor.

## Konsekuensi

- Positif: pelapor di lokasi cukup satu ketukan lalu konfirmasi; pelapor dari rumah memakai pencarian.
- Positif: ada dua jalur tanpa menyeret, dan titik bisa diarahkan ke pintu masuk yang aksesibel.
- Positif: kecamatan selalu konsisten karena tidak diisi manual.
- Negatif: paling banyak yang dibangun (pencarian, izin lokasi, konfirmasi) dan butuh layanan pencarian ([ADR-0016](0016-peta-dan-pencarian-mapbox.md)).
- Negatif: perlu data batas kecamatan yang dirawat.
- Tindak lanjut: desain langkah 1 Laporkan perlu ditambah pemilih lokasi; frame DS v1 saat ini belum memilikinya.

## Pro & Kontra tiap Opsi

### Peta + lokasi perangkat + pencarian

- Pro: berfungsi di semua situasi; aksesibel; tepat.
- Kontra: paling banyak dibangun.

### Pin di peta saja

- Pro: tepat dan tanpa layanan pencarian.
- Kontra: sulit menemukan titik dari tampilan seluruh kota; tanpa jalur lain, gagal WCAG 2.5.7.

### Lokasi perangkat saja

- Pro: satu ketukan saat di lokasi.
- Kontra: tidak berguna bila melapor belakangan; lemah di dalam gedung; gagal bila izin ditolak.

### Pencarian alamat saja

- Pro: aksesibel dan familiar.
- Kontra: sering jatuh di jalan, bukan di pintu masuk; data pencarian untuk tempat kecil di Bogor bisa kurang.

## Referensi

- [Pertanyaan Terbuka PT-07](../product/pertanyaan-terbuka.md#pt-07-input-lokasi-pada-formulir-laporan)
- [PRD](../product/prd.md), FR-21 sampai FR-23
- [WCAG 2.2, kriteria 2.5.7 Dragging Movements](https://www.w3.org/TR/WCAG22/#dragging-movements)
