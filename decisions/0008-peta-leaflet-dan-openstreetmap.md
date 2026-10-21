# ADR-0008: Peta Interaktif dengan Leaflet dan Tile Berbasis OpenStreetMap

- Status: Proposed
- Tanggal: 2026-10-21
- Pengambil keputusan: belum ditentukan
- Issue: #5

## Konteks dan Masalah

Frame Map di Figma adalah tangkapan prototipe yang memakai Leaflet dengan tile standar OpenStreetMap (atribusi "Leaflet | © OpenStreetMap contributors"), dengan marker berwarna per status dan tombol Filter. Halaman Detail juga punya kotak "Lokasi di Peta". Prototipe belum tentu mencerminkan pilihan produksi. Library peta dan penyedia tile apa yang dipakai di produksi?

## Decision Drivers

- Biaya nol atau rendah (proyek kampus).
- Data peta terbuka yang bisa diperkaya komunitas.
- Peta bisa dipakai dengan keyboard dan screen reader, atau punya alternatif setara.
- Ringan di ponsel kelas menengah.
- Patuh pada lisensi dan kebijakan penggunaan tile.

## Opsi yang Dipertimbangkan

1. Leaflet dengan raster tile berbasis OpenStreetMap dari penyedia tile atau server sendiri
2. MapLibre GL JS dengan vector tile berbasis OpenStreetMap
3. Google Maps Platform
4. Mapbox GL JS

## Keputusan

Belum diputuskan. Usulan penulis: opsi 1, karena sesuai prototipe, ringan, gratis, dan punya plugin yang matang (mis. marker clustering).

Apa pun opsinya, server tile resmi `tile.openstreetmap.org` tidak dipakai untuk produksi. [Tile Usage Policy](https://operations.osmfoundation.org/policies/tiles/) OpenStreetMap membatasi penggunaannya oleh aplikasi, jadi produksi memakai penyedia tile pihak ketiga atau server tile sendiri.

## Konsekuensi

Bila usulan diterima:

- Positif: biaya rendah dan transisi mulus dari prototipe.
- Positif: data OSM (tag `wheelchair=*`) bisa dipakai sebagai sumber awal. Lihat [PT-12](../product/pertanyaan-terbuka.md#pt-12-data-awal).
- Negatif: peta interaktif sulit dipakai pengguna screen reader. Explore harus tetap menjadi alternatif setara untuk semua informasi yang ada di peta.
- Negatif: atribusi OpenStreetMap (lisensi ODbL) wajib selalu terlihat.
- Negatif: gaya visual raster tile terbatas dibanding vector tile.

## Pro & Kontra tiap Opsi

### Leaflet + raster tile OSM

- Pro: ringan, sederhana, dan sudah dipakai prototipe.
- Kontra: styling peta terbatas; tetap butuh penyedia tile.

### MapLibre GL JS + vector tile OSM

- Pro: styling penuh; tampilan tajam di semua zoom.
- Kontra: lebih berat di perangkat lemah; setup tile lebih rumit.

### Google Maps Platform

- Pro: data tempat dan pencarian alamat yang lengkap di Indonesia.
- Kontra: berbayar setelah kuota gratis; data terikat ke Google; butuh API key dan akun penagihan.

### Mapbox GL JS

- Pro: styling dan performa sangat baik.
- Kontra: berbayar setelah kuota gratis; lisensi versi terbaru tidak open source.

## Referensi

- [Map (Figma 13:351)](https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=13-351)
- [Detail (Figma 13:1578)](https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=13-1578)
- [Pertanyaan Terbuka PT-11](../product/pertanyaan-terbuka.md#pt-11-tech-stack-produksi)
