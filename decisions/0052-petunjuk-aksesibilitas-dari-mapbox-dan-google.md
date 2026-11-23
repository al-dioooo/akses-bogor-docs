# ADR-0052: Petunjuk Aksesibilitas dari Mapbox dan Google

- Status: Accepted
- Tanggal: 2026-11-23
- Pengambil keputusan: pemilik produk
- Issue: #113
- Melengkapi: [ADR-0016](0016-peta-dan-pencarian-mapbox.md) dan [ADR-0032](0032-pencarian-tempat-lewat-api-mapbox-search-box.md) (Mapbox), [ADR-0049](0049-lapisan-kandidat-openstreetmap-belum-ada-data.md) (kandidat OSM)

## Konteks dan Masalah

Data AksesBogor baru tayang setelah laporan warga diverifikasi ([ADR-0006](0006-laporan-warga-diverifikasi-sebelum-tayang.md), [ADR-0019](0019-verifikasi-tim-dan-mitra-terlatih.md)), jadi kebanyakan tempat belum punya data. Dua penyedia peta punya petunjuk aksesibilitas sendiri:

- Mapbox Search Box API: `retrieve` memberi `metadata.wheelchair_accessible` (ya atau tidak) untuk sebagian tempat. Pada 2026-11-23, Siloam Hospitals Bogor bernilai `true`. `forward` tidak membawa kolom ini.
- Google Places API (New): `accessibilityOptions` berisi `wheelchairAccessibleEntrance`, `wheelchairAccessibleRestroom`, `wheelchairAccessibleParking`, dan `wheelchairAccessibleSeating`.

Pengguna ingin petunjuk ini tampil di Detail lokasi dan kartu Peta, dengan label yang jelas, tanpa disimpan, dan tanpa diberikan ke AI. Bagaimana petunjuk diambil, dicocokkan, ditampilkan, dan dibatasi?

## Decision Drivers

- Petunjuk penyedia belum diperiksa tim dan kriterianya berbeda dari status dan jawaban fasilitas AksesBogor ([ADR-0018](0018-status-pintu-masuk-dulu-dan-ringkasan-fasilitas.md), [ADR-0022](0022-tersedia-berarti-bisa-dipakai.md)). Petunjuk tidak boleh terlihat seperti verifikasi.
- Syarat penyedia: Product Terms Mapbox §1.5 melarang data Mapbox dipakai untuk melatih atau menjadi masukan AI ([ADR-0043](0043-agen-untuk-kontributor-dengan-model-gratis-openrouter.md), [ADR-0050](0050-asisten-rute-publik.md)). Syarat Google Maps Platform membatasi apa yang boleh di-cache dan mewajibkan atribusi.
- Kunci API tetap di server.
- Setiap pencarian ke penyedia berbayar, jadi jumlahnya harus terkendali.
- Fitur ini harus bisa dimatikan tanpa mencabut token Mapbox yang dipakai peta.

## Opsi yang Dipertimbangkan

1. Petunjuk langsung dari API penyedia lewat server API, cache memori singkat, tampil terpisah dari status
2. Menyimpan petunjuk penyedia di database sebagai data awal
3. Tidak memakai sumber pihak ketiga

## Keputusan

Opsi terpilih: "Petunjuk langsung, cache memori singkat", karena hanya opsi ini yang menampilkan petunjuk yang ada tanpa menyimpan data penyedia atau mencampurnya dengan data AksesBogor.

### API

- `GET /places/{id}/hints` untuk lokasi AksesBogor dan `GET /places/candidates/{osmType}/{osmId}/hints` untuk kandidat OSM ([ADR-0049](0049-lapisan-kandidat-openstreetmap-belum-ada-data.md)), keduanya publik.
- Saklar `PROVIDER_HINTS_ENABLED`, mati bawaan. Mapbox memakai `MAPBOX_SECRET_TOKEN` yang sudah ada. Google hanya menyala bila `GOOGLE_MAPS_API_KEY` diisi. Selama mati, jawabannya `{ "hints": [] }` dan web tidak menampilkan bagiannya. Saklar ini juga berlaku untuk salinan tersimpan (lihat di bawah).
- Jawaban hanya berisi nama penyedia, nama tempat di penyedia, jaraknya, tanda aksesibilitas, dan tautan ke halaman tempat itu bila ada (Google Maps). Mapbox tidak punya halaman tempat publik.
- Header `cache-control: private, no-store`, supaya browser dan Cloudflare tidak menyimpan isi penyedia.
- Batas 30 permintaan per menit per pengunjung, dan batas harian per penyedia. Setelah batas harian tercapai, penyedia itu dilaporkan tidak tersedia sampai tengah malam WIB.

### Pencocokan

- Penyedia dicari dengan nama tempat di sekitar titiknya: Mapbox lewat `forward` lalu `retrieve`, Google lewat Text Search dengan field mask `places.id`, `places.displayName`, `places.location`, `places.googleMapsUri`, dan `places.accessibilityOptions`.
- Tempat penyedia diterima hanya bila jaraknya 75 m atau kurang dan namanya cocok: setelah kata umum (mis. "rumah sakit", "RS", "masjid", "kampus", "halte", "Bogor") dibuang, minimal satu kata sama, dan minimal separuh kata nama yang lebih pendek ada di nama lain. Bila beberapa cocok, yang paling mirip lalu paling dekat menang. Selain itu tidak ada petunjuk.
- Tempat tanpa nama (banyak halte OSM) tidak dicocokkan.

### Tidak disimpan

- Hasil pencarian langsung ke penyedia tidak pernah ditulis ke database.
- Mapbox: hasil di memori proses API selama 10 menit, paling banyak 500 entri, lalu hilang saat proses berhenti.
- Google: hanya `place_id` yang di-cache di memori (syarat Places API mengizinkan `place_id` disimpan). Isi lain, termasuk `accessibilityOptions`, diambil ulang lewat Place Details setiap kali diminta dan tidak di-cache.

### Salinan tersimpan untuk kandidat OSM

Pengecualian dari aturan di atas: pada 2026-11-23, 38 baris kandidat di `osm_places` diberi salinan Google Maps di tag `aksesbogor:google_maps` (`nama`, `url`, `place_id`, opsi aksesibilitas yang ada di `aksesibilitas`, yang tidak ada di `tidak_tersedia`, tanggal `diambil`, `metode`, dan `jarak_titik_m`), dan 8 baris diberi keterangan OSM di `aksesbogor:osm_aksesibilitas` (`wheelchair` dan `arti`).

- `GET /places/candidates/{osmType}/{osmId}/hints` ikut mengirim salinan ini dengan `result: "stored"`, selama saklar menyala. Salinan Google tidak dipakai bila Google langsung menemukan tempat itu.
- Salinan Google tanpa tanggal atau tanpa satu pun tanda aksesibilitas tidak dikirim. Tautannya hanya diteruskan bila berupa https ke Google Maps.
- Keterangan OSM hanya dikirim bila nilainya berbeda dari tag `wheelchair` tempat itu sendiri, yang sudah ditampilkan kartu kandidat.
- Memuat ulang data OSM (`pnpm pengetahuan:fasilitas`) mengganti semua baris `osm_places`, jadi kedua tag ini ikut hilang.

### Tampilan

- Bagian "Petunjuk dari sumber lain (belum diperiksa tim)" ada di Detail lokasi dan di kartu lokasi serta kartu kandidat Peta, terpisah dari status dan fasilitas.
- Setiap baris menyebut penyedianya, mis. "Menurut Mapbox: bisa dimasuki kursi roda" dan "Menurut Google: pintu masuk ramah kursi roda · toilet ramah kursi roda: tidak diketahui", lengkap dengan nama tempat di penyedia dan atribusinya ("© Mapbox"; "Google Maps" yang menaut ke halaman tempat itu).
- Salinan tersimpan menyebut tanggalnya, mis. "Menurut Google Maps (diambil 23 Nov 2026): …", dan keterangan OSM ditulis "Menurut OpenStreetMap: …" dengan "© kontributor OpenStreetMap". Keduanya ada di bagian yang sama, dengan label "belum diperiksa tim" yang sama.
- Petunjuk tidak pernah mengubah warna status, jawaban fasilitas, ringkasan "X dari Y", atau urutan daftar, dan tidak pernah ditulis sebagai data terverifikasi.
- Ada keadaan memuat, galat, dan "tidak ada petunjuk".
- Di Peta, petunjuk baru diminta 400 ms setelah kartu terbuka dan dibatalkan bila kartu ditutup lebih dulu, jadi tidak pernah ada permintaan per marker.

### Tidak diberikan ke AI

Agen kontributor dan asisten rute publik tidak menerima petunjuk ini, termasuk salinan tersimpan. Modul petunjuk tidak dipakai alat agen mana pun, alat asisten tidak pernah membaca tag `aksesbogor:*`, dan tes API memeriksa keduanya. Mapbox dilarang oleh §1.5, dan Google ikut dikeluarkan supaya aturannya sama untuk semua penyedia.

### Syarat tampilan Google

Pengguna menyatakan kemitraan kampusnya dengan Google mencakup tampilan ini. Syarat Google Maps Platform yang perlu tetap dipatuhi: atribusi Google Maps di dekat isinya, tidak menyimpan isi selain yang diizinkan, dan batasan pemakaian isi Google bersama peta non-Google. Kartu Peta memakai peta Mapbox, jadi batasan terakhir itu harus dipastikan masuk cakupan kemitraan. Bila tidak, Google dimatikan dengan mengosongkan `GOOGLE_MAPS_API_KEY`.

Salinan tersimpan di `aksesbogor:google_maps` menyimpan isi Google selain `place_id`, sesuatu yang biasanya tidak diizinkan syarat Places API. Salinan ini juga harus dipastikan masuk cakupan kemitraan. Bila tidak, isi tag dikurangi menjadi `place_id` saja, dan petunjuk Google diambil langsung.

## Konsekuensi

- Positif: tempat yang belum punya data AksesBogor bisa langsung punya petunjuk, dan pengguna tahu sumbernya.
- Positif: tidak ada data penyedia di database, jadi tidak ada data lama yang harus dihapus bila syarat atau kunci berubah.
- Negatif: setiap kartu yang dibuka berarti satu sampai dua panggilan berbayar per penyedia, dan Google tidak bisa di-cache selain `place_id`.
- Negatif: pencocokan nama dan jarak bisa meleset, terutama untuk nama yang sangat umum. Karena itu batasnya ketat, dan tempat tanpa nama tidak dicocokkan.
- Negatif: petunjuk penyedia bisa bertentangan dengan data AksesBogor. Data AksesBogor tetap yang utama, dan petunjuk selalu diberi label belum diperiksa.
- Negatif: salinan Google yang tersimpan bisa usang. Karena itu tanggal pengambilannya selalu ditampilkan, dan salinan ini hilang saat data OSM dimuat ulang.

## Pro & Kontra tiap Opsi

### Petunjuk langsung, cache memori singkat

- Pro: sesuai syarat penyedia, tidak ada data penyedia yang tertinggal, dan bisa dimatikan dengan satu saklar.
- Kontra: biaya per kartu yang dibuka, dan sedikit jeda sebelum petunjuk tampil.

### Disimpan di database

- Pro: cepat dan murah setelah diambil sekali.
- Kontra: melanggar batas cache Google, dan data Mapbox yang disimpan mudah terbawa ke basis pengetahuan agen.

### Tanpa sumber pihak ketiga

- Pro: tidak ada risiko syarat atau salah cocok.
- Kontra: petunjuk yang sudah ada di penyedia tidak sampai ke pengguna.

## Referensi

- [ADR-0006](0006-laporan-warga-diverifikasi-sebelum-tayang.md), [ADR-0016](0016-peta-dan-pencarian-mapbox.md), [ADR-0018](0018-status-pintu-masuk-dulu-dan-ringkasan-fasilitas.md), [ADR-0019](0019-verifikasi-tim-dan-mitra-terlatih.md), [ADR-0022](0022-tersedia-berarti-bisa-dipakai.md), [ADR-0032](0032-pencarian-tempat-lewat-api-mapbox-search-box.md), [ADR-0043](0043-agen-untuk-kontributor-dengan-model-gratis-openrouter.md), [ADR-0049](0049-lapisan-kandidat-openstreetmap-belum-ada-data.md), [ADR-0050](0050-asisten-rute-publik.md)
- [Mapbox Search Box API](https://docs.mapbox.com/api/search/search-box/) dan [Mapbox Product Terms](https://www.mapbox.com/legal/product-terms)
- [Places API (New): Text Search](https://developers.google.com/maps/documentation/places/web-service/text-search), [kebijakan Places API](https://developers.google.com/maps/documentation/places/web-service/policies), dan [Google Maps Platform Service Specific Terms](https://cloud.google.com/maps-platform/terms/maps-service-terms)
