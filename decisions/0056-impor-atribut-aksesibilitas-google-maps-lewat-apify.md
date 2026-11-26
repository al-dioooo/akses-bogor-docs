# ADR-0056: Impor Atribut Aksesibilitas Google Maps lewat Apify

- Status: Accepted
- Tanggal: 2026-11-26
- Pengambil keputusan: pemilik produk
- Issue: #126
- Melengkapi: [ADR-0049](0049-lapisan-kandidat-openstreetmap-belum-ada-data.md) (kandidat OSM), [ADR-0052](0052-petunjuk-aksesibilitas-dari-mapbox-dan-google.md) (petunjuk pihak ketiga dan salinan tersimpan)

## Konteks dan Masalah

Peta menampilkan 644 kandidat OSM (kampus, masjid, halte, rumah sakit) yang belum punya data AksesBogor ([ADR-0049](0049-lapisan-kandidat-openstreetmap-belum-ada-data.md)). Kartu kandidat sudah bisa menampilkan salinan atribut aksesibilitas Google Maps dari tag `aksesbogor:google_maps`, dengan tanggal pengambilan dan label "belum diperiksa tim" ([ADR-0052](0052-petunjuk-aksesibilitas-dari-mapbox-dan-google.md)). Pada 2026-11-23 salinan itu diisi dengan tangan untuk sebagian kecil kandidat: nama mirip, kategori sama, paling jauh 400 m, dan yang terdekat menang.

Pengguna ingin semua kandidat diisi dengan cara yang sama, memakai pendekatan alat ulasan Google Maps miliknya (`al-dioooo/google-maps-review-analytics-tool`): aktor Apify dijalankan dengan token sendiri, lalu dataset mentahnya disimpan. Aktor `compass/crawler-google-places` mengembalikan atribut tempat di `additionalInfo`, termasuk grup "Aksesibilitas". Bagaimana tempat dicari, dicocokkan, disimpan, diberi label, dan diperbarui, berapa biayanya, dan risiko apa yang diterima?

## Decision Drivers

- Data pihak ketiga tidak boleh terlihat atau terhitung sebagai data terverifikasi ([ADR-0006](0006-laporan-warga-diverifikasi-sebelum-tayang.md), [ADR-0052](0052-petunjuk-aksesibilitas-dari-mapbox-dan-google.md)).
- Tidak ada tempat baru dari sumber luar. Setiap salinan menyimpan sumber, tautan, dan tanggal ambilnya.
- Nama masjid sering sama: di OSM Kota Bogor ada 10 "Masjid Al Ikhlas", 9 "Masjid At Taqwa", dan 9 "Masjid Nurul Iman". Pencarian harus menemukan tempat di titik kita, bukan yang paling terkenal.
- Biaya per run kecil, diketahui sebelum run, dan dibatasi.
- Impor bisa dijalankan ulang tanpa duplikat, dan bisa diulang dari berkas yang sama setelah data OSM dimuat ulang.
- Bentuk tag tetap terbaca oleh `src/hints/stored.ts` dan peringkat kecamatan ([ADR-0053](0053-peta-tematik-dan-batas-kota.md)).

## Opsi yang Dipertimbangkan

1. Aktor Apify `compass/crawler-google-places`, satu URL pencarian Google Maps per kandidat di sekitar titik OSM-nya
2. Aktor yang sama, pencarian nama + "Bogor" dengan lokasi Kota Bogor
3. Google Places API (New) resmi, seperti petunjuk langsung [ADR-0052](0052-petunjuk-aksesibilitas-dari-mapbox-dan-google.md)
4. Tidak mengimpor; hanya petunjuk langsung

## Keputusan

Opsi terpilih: "Aktor Apify dengan URL pencarian per kandidat", karena hanya opsi ini yang memakai alat pilihan pengguna dan tetap menemukan masjid yang namanya sama di titik yang benar.

### Pengambilan

- `pnpm petunjuk:google-ambil` membaca kandidat di `osm_places` yang bernama dan punya kata pembeda menurut aturan nama ADR-0052. Dengan data OSM 2026-11-22, itu 528 dari 644 kandidat: 109 tanpa nama dan 7 bernama umum (mis. "Mushola") dilewati karena tidak akan pernah lolos syarat nama.
- Setiap kandidat menjadi satu URL `https://www.google.com/maps/search/<nama>/@<lat>,<lng>,17z?hl=id`. Google mengurutkan hasil di sekitar titik itu, seperti orang yang mencari nama tempat di peta sekitar lokasinya. Pencarian nama + "Bogor" (opsi 2) mengembalikan "Masjid Al Ikhlas" yang paling menonjol di Bogor, dan kolom lokasi aktor memecah kota menjadi petak yang urutannya tidak mengikuti jarak.
- Satu hasil teratas per pencarian (bawaan), bahasa Indonesia. Semua tambahan berbayar dan data pribadi mati: tanpa ulasan, foto, kontak, atau data pengulas.
- Dataset mentah dan `.meta.json` (run, biaya, daftar pencarian) disimpan di luar repo, di `~/data/akses-bogor/google-maps/<waktu>.json` milik user yang menjalankan.
- Token dari env `APIFY_TOKEN`, atau berkas `~/credentials/akses-bogor/apify.env`. Di produksi, berkasnya `/etc/akses-bogor/apify.env` lewat `APIFY_ENV_FILE`, terpisah dari `api.env` karena server API tidak memakainya. Token tidak pernah dicetak.
- `--rencana` mencetak jumlah pencarian dan perkiraan biaya tanpa memanggil Apify. `--limit N` membatasi run, bergiliran per kategori. Setiap run diberi batas biaya Apify (`maxTotalChargeUsd`), bawaannya perkiraan ditambah 50%.

### Pencocokan

`pnpm petunjuk:google-impor` mencocokkan setiap hasil Google ke `osm_places` seperti cara 2026-11-23:

- Kategori sama. Kategori Google (bahasa Indonesia atau Inggris) dipetakan ke kategori kita: rumah sakit (bukan rumah sakit hewan); masjid, musala, atau tempat ibadah; universitas, perguruan tinggi, sekolah tinggi, institut, politeknik, atau akademi; halte, stasiun bus, atau terminal bus.
- Nama mirip menurut aturan ADR-0052: kata pembeda yang sama minimal separuh kata nama yang lebih panjang, angka dan huruf tunggal sama persis, dan kata jenis tempat di nama Google sesuai.
- Jarak 400 m atau kurang, dihitung PostGIS (`ST_DWithin` dan `ST_Distance` pada geography). Batas ini lebih longgar dari 75 m untuk petunjuk langsung, karena titik OSM kampus dan rumah sakit ada di tengah area, sedangkan pin Google sering di gedung utama atau gerbang. Syarat nama yang ketat menutup risikonya, dan dry run mencetak jarak setiap pasangan.
- Yang terdekat menang: semua pasangan yang lolos diurutkan dari yang terdekat dan diambil satu per satu, jadi satu hasil Google paling banyak untuk satu kandidat, dan sebaliknya.
- Hasil yang tidak cocok tidak pernah ditambahkan sebagai tempat. Hasil itu dilaporkan dengan alasannya: kategori lain, tidak ada kandidat berkategori sama dalam 400 m, nama tidak mirip, atau ambigu (kandidatnya sudah diambil hasil lain yang lebih dekat). Tempat yang tutup permanen dibuang.

### Bentuk dan Label

- Tag `aksesbogor:google_maps` berisi `sumber` ("Google Maps"), `nama`, `url` (tautan Google Maps dengan `place_id`), `place_id`, `aksesibilitas` dan `tidak_tersedia` (label grup Aksesibilitas apa adanya, mis. "Pintu masuk khusus pengguna kursi roda"), `diambil` (waktu ambil dari Apify), `metode` (`apify:compass/crawler-google-places`), `jarak_titik_m`, dan `belum_diperiksa_tim: true`. Bentuk ini sama dengan salinan 2026-11-23, jadi kartu kandidat dan peringkat kecamatan tidak perlu diubah.
- Hanya grup Aksesibilitas yang disimpan. Nilai, ulasan, jam buka, telepon, dan foto tidak. Tempat yang cocok tetapi tanpa grup Aksesibilitas tidak ditulis.
- Label di web tetap dari ADR-0052: bagian "Petunjuk dari sumber lain (belum diperiksa tim)" dengan baris "Menurut Google Maps (diambil 26 Nov 2026): …" yang menaut ke Google Maps. Artinya sama dengan "Sumber: Google Maps, diambil (tanggal), belum diperiksa tim". Saklar `PROVIDER_HINTS_ENABLED` tetap berlaku.
- Salinan ini tidak diberikan ke AI ([ADR-0052](0052-petunjuk-aksesibilitas-dari-mapbox-dan-google.md)). Tes API hanya mengizinkan kedua skrip ini memakai modul petunjuk.

### Apa yang Boleh Ditimpa

- Satu transaksi. `--dry-run` mencetak setiap pasangan dan statistik (cocok, ambigu, tidak cocok, atribut per kategori) tanpa menulis.
- Hanya tag `aksesbogor:google_maps` di baris `osm_places` yang sudah ada yang diubah. Tabel `places` tidak disentuh.
- Salinan baru ditulis. Salinan dari impor ini diperbarui bila isinya berubah, dan dibiarkan bila sama atau bila salinan yang ada lebih baru dari berkas yang dimuat.
- Salinan yang `belum_diperiksa_tim`-nya bukan `true`, atau bentuknya tidak dikenal, tidak pernah disentuh.
- Salinan dari cara lain (mis. salinan 2026-11-23) dibiarkan, kecuali impor dijalankan dengan `--timpa-cara-lain`. Salinan itu pun hanya diganti bila belum diperiksa tim.

### Pembaruan

- Dijalankan tangan oleh admin, bukan timer, karena setiap run berbayar dan dry run perlu diperiksa orang.
- Ambil ulang setiap tiga bulan, dan setelah `pnpm pengetahuan:fasilitas` mengunduh data OSM baru, karena kandidat baru bisa muncul.
- Memuat ulang OSM mengganti semua baris `osm_places`, jadi salinan hilang. Setelah itu, impor lagi berkas terakhir; tidak perlu mengambil ulang dari Apify.
- Langkah produksinya ada di [runbook impor atribut Google Maps](../operations/impor-atribut-google-maps.md).

### Biaya

Aktor ini berbayar per event (harga 2026-11-26): $0,004 per tempat di paket Free Apify dan $0,003 di paket Starter, lebih murah di paket lebih tinggi. Biaya mulai run sekitar $0,0002.

| Run | Paket Free | Paket Starter |
| --- | --- | --- |
| 528 pencarian, 1 hasil (bawaan) | paling banyak $2,11 | paling banyak $1,58 |
| 528 pencarian, 2 hasil | paling banyak $4,22 | paling banyak $3,17 |
| `--limit 20` | paling banyak $0,08 | paling banyak $0,06 |

Bila Apify menghitung URL pencarian sebagai tambahan "Additional place details" ($0,002 per tempat), biaya run penuh bertambah sekitar $1,06. Run kecil pertama menjawabnya: `.meta.json` mencatat biaya dan event yang ditagih.

### Risiko Syarat Google

Mengambil isi Google Maps lewat aktor scraping bertentangan dengan [Syarat Layanan Tambahan Google Maps](https://www.google.com/help/terms_maps), yang melarang menyalin isi Google Maps tanpa izin, mengunduhnya secara massal, dan memakainya untuk membuat kumpulan data pengganti Google Maps. Yang menjalankan scraping memang Apify, tetapi AksesBogor yang menyimpan dan menampilkan isinya, jadi risikonya ada di AksesBogor. Pengguna memilih alat ini dengan risiko itu. Kemitraan kampus yang disebut di ADR-0052 dibuat untuk Google Maps Platform, dan belum tentu mencakup isi yang diambil dengan scraping.

Akibat terburuknya, Google meminta isi itu dihapus. Karena itu:

- yang disimpan hanya grup Aksesibilitas, nama, `place_id`, dan tautan, selalu dengan atribusi dan tanggal;
- semua salinan dari impor ini bisa dihapus dengan satu perintah SQL (berdasarkan `metode`), berkas mentah bisa dihapus dari folder datanya, dan `PROVIDER_HINTS_ENABLED=false` menyembunyikan semuanya sekaligus (langkahnya di runbook);
- bila kemitraan atau tinjauan kampus menyatakan cara ini tidak boleh, salinan dihapus dan petunjuk Google kembali hanya lewat Places API langsung (ADR-0052).

## Konsekuensi

- Positif: sebagian besar kandidat bernama bisa punya petunjuk aksesibilitas dengan sumber dan tanggalnya, dengan biaya sekitar dua dolar per run penuh.
- Positif: dataset mentah tersimpan, jadi impor bisa diulang dan diperiksa tanpa mengambil ulang.
- Negatif: risiko syarat Google di atas.
- Negatif: dokumentasi aktor menyebut Google menampilkan sebagian besar `additionalInfo` hanya untuk pengguna yang masuk, jadi banyak tempat mungkin tanpa grup Aksesibilitas. Run kecil pertama menunjukkan seberapa banyak.
- Negatif: pencocokan 400 m bisa salah pasang untuk nama yang mirip. Dry run harus diperiksa orang sebelum impor, dan pasangan yang salah bisa dikecualikan.
- Negatif: salinan bisa usang. Tanggalnya selalu tampil, dan pengambilan ulang tiga bulanan memperbaruinya.

## Pro & Kontra tiap Opsi

### Aktor Apify dengan URL pencarian per kandidat

- Pro: hasil diurutkan Google di sekitar titik kandidat; satu run untuk semua kandidat; biaya kecil dan dibatasi; sama dengan alat yang sudah dipakai pengguna.
- Kontra: bertentangan dengan syarat Google; bergantung pada aktor pihak ketiga yang bisa berubah.

### Aktor Apify dengan nama + "Bogor"

- Pro: input paling sederhana.
- Kontra: untuk nama yang sama, hasil teratas bukan tempat di titik kita, jadi banyak yang tidak cocok atau salah pasang.

### Google Places API (New)

- Pro: jalur resmi dengan kunci sendiri.
- Kontra: syarat Places API tidak mengizinkan isi selain `place_id` disimpan, jadi impor seperti ini tetap tidak boleh; pengguna memilih alat Apify.

### Tanpa impor

- Pro: tidak ada risiko syarat atau salah pasang.
- Kontra: kandidat tetap tanpa petunjuk sampai petunjuk langsung dinyalakan dan kuncinya ada.

## Referensi

- [ADR-0006](0006-laporan-warga-diverifikasi-sebelum-tayang.md), [ADR-0049](0049-lapisan-kandidat-openstreetmap-belum-ada-data.md), [ADR-0052](0052-petunjuk-aksesibilitas-dari-mapbox-dan-google.md), [ADR-0053](0053-peta-tematik-dan-batas-kota.md)
- [Runbook impor atribut Google Maps](../operations/impor-atribut-google-maps.md)
- Aktor [compass/crawler-google-places](https://apify.com/compass/crawler-google-places) dan alat pengguna [google-maps-review-analytics-tool](https://github.com/al-dioooo/google-maps-review-analytics-tool)
- [Syarat Layanan Tambahan Google Maps](https://www.google.com/help/terms_maps)
