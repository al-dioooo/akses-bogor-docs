# ADR-0016: Peta dan Pencarian Lokasi Memakai Mapbox

- Status: Accepted
- Tanggal: 2026-10-21
- Pengambil keputusan: Tim AksesBogor
- Issue: #20
- Men-supersede: [ADR-0008](0008-peta-leaflet-dan-openstreetmap.md)

## Konteks dan Masalah

[ADR-0008](0008-peta-leaflet-dan-openstreetmap.md) mengusulkan Leaflet dengan tile berbasis OpenStreetMap, tetapi belum diputuskan. [ADR-0015](0015-input-lokasi-peta-gps-pencarian.md) kini membutuhkan pencarian tempat dengan saran saat mengetik, dan layanan pencarian publik OpenStreetMap (Nominatim) melarang penggunaan seperti itu. Tim sudah memakai Mapbox di lokerin, termasuk token yang dibagikan dari backend. Penyedia peta dan pencarian apa yang dipakai?

## Decision Drivers

- Pengalaman tim dan kode yang bisa dipakai ulang dari lokerin.
- Pencarian dengan saran saat mengetik yang cukup baik untuk tempat di Bogor.
- Peta yang bisa dipakai dengan keyboard, dengan daftar sebagai alternatif.
- Biaya masih dalam kuota gratis untuk skala Kota Bogor.
- Atribusi dan lisensi data dipatuhi.

## Opsi yang Dipertimbangkan

1. Mapbox (peta Mapbox GL JS dan pencarian Mapbox)
2. Leaflet + tile OSM dari penyedia pihak ketiga + layanan pencarian terpisah (ADR-0008)
3. MapLibre GL + MapTiler (tile vektor OSM dan pencarian)

## Keputusan

Opsi terpilih: "Mapbox", mengikuti lokerin.

- **Peta**: Mapbox GL JS untuk halaman Peta, peta mini di Detail, dan pemilih lokasi di formulir laporan.
- **Pencarian**: layanan pencarian Mapbox, dibatasi dan diprioritaskan ke wilayah Kota Bogor.
- **Token**: Fastify membagikan token baca berumur pendek ke browser, seperti route `GET /peta/kunci` di lokerin. Token rahasia tidak pernah sampai ke browser.
- **Marker**: memakai komponen Map marker dari design system sebagai marker HTML, dengan nama aksesibel (nama tempat dan status).
- **Hasil pencarian tidak disimpan**: hasil hanya menggeser peta, dan yang disimpan adalah pin yang dikonfirmasi pelapor (ADR-0015). Ini juga menghindari aturan Mapbox yang melarang penyimpanan hasil pencarian sementara.
- **Atribusi**: logo Mapbox dan "© Mapbox © OpenStreetMap" selalu terlihat.
- **Alternatif daftar**: Explore tetap menjadi alternatif setara untuk semua informasi di peta.

## Konsekuensi

- Positif: kode peta dan route token dari lokerin bisa dipakai ulang.
- Positif: pencarian dengan saran saat mengetik dan peta vektor yang tajam di ponsel.
- Negatif: terikat ke Mapbox; pemakaian di atas kuota gratis berbayar. Pantau pemakaian bulanan dan pasang batas pada token.
- Negatif: Mapbox GL JS membutuhkan WebGL; di perangkat yang tidak mendukung, pengguna diarahkan ke Explore.
- Tindak lanjut: frame Map · DS v1 di Figma masih memakai tile raster OSM dan atribusi Leaflet, sehingga perlu diperbarui.

## Pro & Kontra tiap Opsi

### Mapbox

- Pro: sudah dikuasai tim; pencarian bagus; satu vendor untuk peta dan pencarian.
- Kontra: vendor lock-in; biaya di atas kuota gratis.

### Leaflet + OSM + layanan pencarian terpisah

- Pro: data terbuka; Leaflet ringan.
- Kontra: butuh dua penyedia tambahan (tile dan pencarian); pencarian publik OSM tidak boleh dipakai untuk saran saat mengetik.

### MapLibre + MapTiler

- Pro: renderer open source dengan data OSM; satu penyedia untuk tile dan pencarian.
- Kontra: tim belum berpengalaman; kode lokerin tidak langsung terpakai.

## Referensi

- [ADR-0008](0008-peta-leaflet-dan-openstreetmap.md) (di-supersede), [ADR-0013](0013-tech-stack-nextjs-fastify.md), [ADR-0015](0015-input-lokasi-peta-gps-pencarian.md)
- Repo lokerin: `al-dioooo/lokerin-web` (Peta dengan `mapbox-gl`, token dari backend)
- [Kebijakan pemakaian Nominatim](https://operations.osmfoundation.org/policies/nominatim/)
