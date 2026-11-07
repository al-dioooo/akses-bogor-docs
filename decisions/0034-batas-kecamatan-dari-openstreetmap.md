# ADR-0034: Batas Kecamatan Kota Bogor dari OpenStreetMap

- Status: Accepted
- Tanggal: 2026-11-07
- Pengambil keputusan: Tim AksesBogor
- Issue: #54
- Melengkapi: [ADR-0010](0010-cakupan-wilayah-kota-bogor.md), [ADR-0015](0015-input-lokasi-peta-gps-pencarian.md)

## Konteks dan Masalah

FR-22 menghitung kecamatan dari koordinat dan menolak titik di luar Kota Bogor. [ADR-0015](0015-input-lokasi-peta-gps-pencarian.md) menyebut PostGIS dengan data batas dari OpenStreetMap atau Badan Informasi Geospasial (BIG), tetapi belum memilih. Kolom `districts.boundary` di API masih kosong. Data batas mana yang dipakai, dan bagaimana data itu masuk ke database?

## Decision Drivers

- Bisa diunduh tanpa akun dan diperbarui ulang dengan langkah yang tercatat.
- Lisensinya mengizinkan dipakai dan disimpan di repo.
- Keenam kecamatan tidak bertumpuk dan gabungannya sama dengan batas kota, supaya setiap titik di Kota Bogor mendapat tepat satu kecamatan.
- Cukup kecil untuk masuk ke migrasi.

## Opsi yang Dipertimbangkan

1. OpenStreetMap lewat Overpass API
2. Batas Wilayah Administrasi dari BIG
3. HDX COD-AB Indonesia (BPS dan BIG)
4. Digambar sendiri dari peta

## Keputusan

Opsi terpilih: "OpenStreetMap lewat Overpass API".

### Data

- Relasi OSM 14745927 (Kota Bogor, `admin_level=5`) dan enam relasi `admin_level=6` di dalamnya: Bogor Barat (19957685), Bogor Selatan (19957707), Bogor Tengah (19957682), Bogor Timur (19957675), Bogor Utara (19957676), dan Tanah Sareal (19957686).
- Diunduh 2026-11-07 dengan satu kueri Overpass. Poligon dibangun dari garis anggota relasi dengan PostGIS (`ST_BuildArea`, `ST_MakeValid`), tanpa penyederhanaan, dengan koordinat dibulatkan ke 6 desimal (sekitar 10 cm). Totalnya sekitar 3.900 titik.
- Hasil pemeriksaan: keenam poligon valid, tidak saling bertumpuk, dan gabungannya sama persis dengan batas kota. Luasnya 111,4 km²; BPS mencatat 118,5 km², jadi tepi batas OSM bisa berbeda dari batas resmi.

### Pemakaian

- Migrasi API mengisi `districts.boundary` untuk keenam kecamatan. Seed data contoh tidak lagi menghapus tabel `districts`, supaya batasnya tidak hilang.
- Kecamatan dihitung dengan `ST_Covers` dari pin yang dikonfirmasi pelapor, di API, saat pengecekan lokasi dan sekali lagi saat laporan dikirim. Kecamatan dari browser tidak dipercaya.
- Titik di luar keenam kecamatan ditolak dengan pesan yang menyebut cakupan, misalnya: "Titik ini berada di luar Kota Bogor. Untuk saat ini AksesBogor hanya mencatat tempat di enam kecamatan Kota Bogor." Verifikator tetap bisa menolak dengan R1 bila batasnya meleset.
- Kueri Overpass dan langkah pembuatannya ditulis di README API, sehingga pembaruan dilakukan lewat migrasi baru setelah dibandingkan dengan data lama.

### Lisensi

Data OSM berlisensi Open Database License (ODbL). Migrasi dan README API mencantumkan "Batas kecamatan © kontributor OpenStreetMap, ODbL", dan data batas di repo tetap berlisensi ODbL. Peta Mapbox sudah menampilkan atribusi OpenStreetMap ([ADR-0016](0016-peta-dan-pencarian-mapbox.md)).

## Konsekuensi

- Positif: kecamatan selalu konsisten dan pengecekan cakupan bekerja tanpa layanan luar.
- Positif: data kecil (sekitar 75 KB teks) dan tidak butuh akun untuk diunduh ulang.
- Negatif: batas OSM bukan batas resmi. Titik di dekat tepi kota atau tepi kecamatan bisa salah kecamatan, dan perlu diperbaiki verifikator.
- Negatif: perubahan batas di OSM tidak otomatis ikut; tim memeriksanya ulang sekitar setahun sekali.
- Negatif: kewajiban ODbL berlaku bila data batas dibagikan ulang.

## Pro & Kontra tiap Opsi

### OpenStreetMap lewat Overpass

- Pro: terbuka, kecil, mudah diunduh ulang, dan gabungannya rapi.
- Kontra: tidak resmi; kualitasnya bergantung pada kontributor.

### BIG

- Pro: batas resmi pemerintah.
- Kontra: unduhan butuh akun dan berkasnya per provinsi atau nasional, jadi lebih berat diproses.

### HDX COD-AB

- Pro: bersumber dari BPS dan BIG, lisensi terbuka.
- Kontra: berkas nasional yang besar dan versinya lebih lama.

### Digambar sendiri

- Pro: tanpa unduhan.
- Kontra: tidak akurat dan sulit diperiksa.

## Referensi

- [ADR-0010](0010-cakupan-wilayah-kota-bogor.md), [ADR-0015](0015-input-lokasi-peta-gps-pencarian.md), [ADR-0016](0016-peta-dan-pencarian-mapbox.md)
- [PRD](../product/prd.md), FR-22
- [OpenStreetMap: relasi 14745927](https://www.openstreetmap.org/relation/14745927), [Overpass API](https://wiki.openstreetmap.org/wiki/Overpass_API), [ODbL](https://opendatacommons.org/licenses/odbl/)
- [Panduan Verifikasi](../operations/panduan-verifikasi.md), alasan R1
