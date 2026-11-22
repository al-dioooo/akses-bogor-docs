# ADR-0049: Lapisan Kandidat OpenStreetMap: Belum Ada Data

- Status: Accepted
- Tanggal: 2026-11-22
- Pengambil keputusan: Tim AksesBogor
- Issue: #103
- Melengkapi: [ADR-0006](0006-laporan-warga-diverifikasi-sebelum-tayang.md) dan [ADR-0019](0019-verifikasi-tim-dan-mitra-terlatih.md) (verifikasi), [ADR-0016](0016-peta-dan-pencarian-mapbox.md) (Peta), [ADR-0034](0034-batas-kecamatan-dari-openstreetmap.md) dan [ADR-0043](0043-agen-untuk-kontributor-dengan-model-gratis-openrouter.md) (data OpenStreetMap)

## Konteks dan Masalah

Peta di produksi kosong karena belum ada lokasi terverifikasi. Lokasi baru tayang hanya setelah laporan warga diverifikasi tim ([ADR-0006](0006-laporan-warga-diverifikasi-sebelum-tayang.md), [ADR-0019](0019-verifikasi-tim-dan-mitra-terlatih.md)), dan itu butuh waktu. Sementara itu database sudah berisi 644 kampus, masjid, halte, dan rumah sakit di Kota Bogor dari OpenStreetMap (tabel `osm_places`, sumber `osm-tempat-umum-kota-bogor`). Data itu dimuat untuk agen kontributor dan prioritas kecamatan ([ADR-0043](0043-agen-untuk-kontributor-dengan-model-gratis-openrouter.md)), tetapi tidak tampil di Peta.

Pengguna ingin melihat tempat nyata di Peta tanpa memalsukan verifikasi. Bagaimana tempat OSM ditampilkan supaya tidak pernah terlihat atau terhitung sebagai lokasi terverifikasi, dan kapan tempat itu hilang dari Peta?

## Decision Drivers

- Pengguna difabel memutuskan pergi atau tidak dari status di Peta. Tempat yang belum diperiksa tidak boleh terlihat punya status aksesibilitas, juga tidak secara tidak langsung.
- Peta yang kosong terlihat rusak dan tidak memberi pelapor titik awal.
- Data OSM berlisensi ODbL: atribusi wajib ikut setiap tampilan yang memakainya.
- Peta tetap memenuhi WCAG 2.2 AA ([ADR-0021](0021-wcag-22-aa-dan-pengujian-berlapis.md)). Semua yang ada di peta juga bisa dipakai lewat daftar dengan keyboard ([ADR-0016](0016-peta-dan-pencarian-mapbox.md)).
- Ratusan titik harus tetap ringan di ponsel kelas bawah.

## Opsi yang Dipertimbangkan

1. Lapisan kandidat "Belum ada data" yang terpisah dari lokasi AksesBogor
2. Mengimpor tempat OSM sebagai lokasi AksesBogor berstatus "Belum diketahui"
3. Peta tetap kosong sampai ada lokasi terverifikasi
4. Memakai tag `wheelchair` OSM sebagai status sementara

## Keputusan

Opsi terpilih: "Lapisan kandidat terpisah", karena hanya opsi ini yang menampilkan tempat nyata tanpa memberi tempat itu status atau menghitungnya sebagai data AksesBogor.

### Kandidat tidak pernah terverifikasi

- Kandidat tidak punya status aksesibilitas, ringkasan fasilitas, atau tanggal verifikasi, dan tidak pernah memakai empat warna atau ikon status.
- Kandidat tidak masuk `GET /places`, `GET /places/map`, `GET /places/stats`, jumlah lokasi per kecamatan, Beranda, atau Jelajahi. Jelajahi tetap hanya berisi lokasi terverifikasi. Bila Jelajahi kosong, halaman itu boleh menautkan ke kandidat di Peta.
- Teksnya selalu "Belum ada data" dan "belum diperiksa tim". Bila OSM punya tag `wheelchair`, nilainya hanya tampil sebagai "Menurut OpenStreetMap: …", terpisah dari data AksesBogor.
- Kandidat tidak tampil selama filter status atau fasilitas aktif, karena kandidat tidak punya keduanya. Filter nama, kategori, dan kecamatan tetap berlaku.

### API

Route baca publik `GET /places/candidates`, tanpa akun:

- Sumber: `osm_places`, yaitu empat kategori [ADR-0004](0004-empat-kategori-lokasi-untuk-mvp.md), dengan kecamatan dari batas [ADR-0034](0034-batas-kecamatan-dari-openstreetmap.md). Datanya dimuat ulang dengan `pnpm pengetahuan:fasilitas`.
- Filter `q`, `category`, dan `district`, dengan bentuk yang sama seperti `GET /places/map`.
- Jawaban berupa GeoJSON FeatureCollection. Id setiap fitur `node/123`. Propertinya: `osmType`, `osmId`, `name` (boleh null), `category`, `district`, dan `wheelchair` (nilai mentah tag OSM, atau null). Di tingkat koleksi ada `source`: judul, lisensi (ODbL 1.0), atribusi "© kontributor OpenStreetMap", tautan halaman hak cipta OSM, dan waktu unduh.
- Kandidat yang berjarak 50 m atau kurang dari lokasi AksesBogor terbit berkategori sama tidak ikut. Aturannya sama dengan "sudah punya data" di [ADR-0043](0043-agen-untuk-kontributor-dengan-model-gratis-openrouter.md) dan dihitung saat dibaca.
- `cache-control: public, max-age=300`.

### Peta

- Kandidat digambar sebagai satu lapisan GL Mapbox: lingkaran abu-abu berlubang yang lebih kecil dari marker status, di bawah marker lokasi terverifikasi. Bentuk dan warnanya berbeda dari keempat status. Lapisan GL dipilih karena 644 marker HTML terlalu berat di ponsel, dan ratusan tombol di peta membuat urutan Tab tidak terpakai.
- Lapisan menyala bila ada kandidat. Tombol untuk mematikan dan menyalakannya ada di legenda panel daftar, bersama entri "Belum ada data · OpenStreetMap, belum diperiksa tim". Tombol tidak ditaruh di atas peta, karena pita atas di layar 320 px sudah penuh.
- Atribusi "© kontributor OpenStreetMap", yang menaut ke halaman hak cipta OSM, tampil di atribusi peta selama lapisan menyala.
- Kartu kandidat di panel berisi nama (atau kategori dengan "tanpa nama", mis. "Halte tanpa nama"), kategori, kecamatan, dan "Belum ada data aksesibilitas", ditambah tag `wheelchair` OSM bila ada. Tombol utamanya "Laporkan tempat ini", dan ada tautan ke tempat itu di OSM.
- Alternatif untuk peta: bagian "Belum ada data (n)" di panel daftar, setelah daftar lokasi terverifikasi. Bagian ini mengikuti bagian peta yang terlihat, seperti daftar lokasi, dan setiap baris punya tombol "Laporkan tempat ini" dan "Tunjukkan di peta". Baris ditampilkan bertahap supaya urutan Tab tetap pendek.

### Laporkan dari kandidat

- "Laporkan tempat ini" membuka `/laporkan?osm=node/123&nama=…&kategori=…&lat=…&lng=…`. Laporan baru terisi nama dan kategori, dan titik OSM menjadi pin awal yang belum dikonfirmasi. Pelapor tetap memindahkan pin ke pintu masuk utama lalu menekan "Pakai lokasi ini" ([ADR-0015](0015-input-lokasi-peta-gps-pencarian.md)). Laporan lalu melewati verifikasi yang sama dengan laporan lain.
- Setiap kandidat punya draf sendiri di perangkat ([ADR-0033](0033-isi-dulu-masuk-saat-kirim-dan-draf-di-perangkat.md)), jadi draf laporan baru yang lain tidak tertimpa.
- Isian dari URL hanya isian awal. Nilai yang salah bentuk diabaikan, dan pelapor bisa mengubah semuanya.

### Kapan kandidat hilang

Begitu laporan dari sebuah kandidat terbit sebagai lokasi AksesBogor berkategori sama dalam 50 m, kandidat itu tidak ikut lagi di jawaban berikutnya, paling lama 5 menit kemudian karena cache. Tempat yang sudah tidak ada di OSM hilang saat data dimuat ulang.

## Konsekuensi

- Positif: Peta langsung berisi tempat nyata, dan setiap kandidat menjadi jalan pintas untuk melapor.
- Positif: aturan verifikasi tidak berubah. Kandidat dan lokasi terverifikasi tidak pernah berbagi status, angka, atau halaman.
- Negatif: kelengkapan kandidat bergantung pada OSM. Tempat yang tidak ada di OSM tidak muncul, dan banyak halte OSM tidak bernama.
- Negatif: titik OSM gedung besar (kampus, rumah sakit) adalah titik tengahnya. Bila pintu masuk utama yang dikonfirmasi pelapor lebih dari 50 m dari titik itu, kandidat tetap tampil di samping lokasi terverifikasinya sampai jaraknya disesuaikan.
- Negatif: kandidat di lapisan GL tidak bisa difokus di peta. Pengguna keyboard dan pembaca layar memakai bagian "Belum ada data" di panel daftar.
- Lanjutan: id OSM dari URL Laporkan belum dikirim ke API. Bila verifikator perlu melihatnya, laporan butuh kolom baru.

## Pro & Kontra tiap Opsi

### Lapisan kandidat terpisah

- Pro: tempat nyata tampil tanpa status, dan setiap kandidat mengajak orang melapor.
- Kontra: route API, lapisan peta, dan bagian daftar baru, dan dua jenis titik di peta yang harus mudah dibedakan.

### Impor sebagai lokasi "Belum diketahui"

- Pro: tidak ada komponen baru; marker dan daftar yang ada langsung terisi.
- Kontra: tempat yang belum diperiksa tayang sebagai lokasi AksesBogor, terhitung di Beranda, Jelajahi, dan prioritas kecamatan, dan punya status. Ini sama dengan opsi "langsung tayang dengan label belum terverifikasi" yang ditolak di [ADR-0006](0006-laporan-warga-diverifikasi-sebelum-tayang.md).

### Peta tetap kosong

- Pro: tidak ada risiko salah tafsir.
- Kontra: Peta terlihat rusak, dan pelapor tidak punya titik awal.

### Tag `wheelchair` OSM sebagai status

- Pro: sebagian tempat langsung punya keterangan aksesibilitas.
- Kontra: tag itu belum diperiksa, kriterianya berbeda dari status pintu masuk dan fasilitas AksesBogor ([ADR-0018](0018-status-pintu-masuk-dulu-dan-ringkasan-fasilitas.md)), dan kebanyakan tempat tidak memilikinya.

## Referensi

- [ADR-0004](0004-empat-kategori-lokasi-untuk-mvp.md), [ADR-0006](0006-laporan-warga-diverifikasi-sebelum-tayang.md), [ADR-0015](0015-input-lokasi-peta-gps-pencarian.md), [ADR-0016](0016-peta-dan-pencarian-mapbox.md), [ADR-0018](0018-status-pintu-masuk-dulu-dan-ringkasan-fasilitas.md), [ADR-0019](0019-verifikasi-tim-dan-mitra-terlatih.md), [ADR-0021](0021-wcag-22-aa-dan-pengujian-berlapis.md), [ADR-0033](0033-isi-dulu-masuk-saat-kirim-dan-draf-di-perangkat.md), [ADR-0034](0034-batas-kecamatan-dari-openstreetmap.md), [ADR-0043](0043-agen-untuk-kontributor-dengan-model-gratis-openrouter.md)
- al-dioooo/akses-bogor-web#110
- [ODbL 1.0](https://opendatacommons.org/licenses/odbl/1-0/) dan [pedoman atribusi OSMF](https://osmfoundation.org/wiki/Licence/Attribution_Guidelines)
