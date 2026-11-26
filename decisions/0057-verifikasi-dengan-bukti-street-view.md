# ADR-0057: Verifikasi dengan Bukti Street View

- Status: Accepted
- Tanggal: 2026-11-27
- Pengambil keputusan: pemilik produk
- Issue: #140
- Men-supersede sebagian: [ADR-0006](0006-laporan-warga-diverifikasi-sebelum-tayang.md) (data hanya tayang lewat laporan warga yang diverifikasi) dan [ADR-0052](0052-petunjuk-aksesibilitas-dari-mapbox-dan-google.md) (salinan tersimpan untuk kandidat OSM)
- Melengkapi: [ADR-0049](0049-lapisan-kandidat-openstreetmap-belum-ada-data.md), [ADR-0051](0051-kelola-tempat-oleh-admin-dan-terbit-langsung.md), [ADR-0056](0056-impor-atribut-aksesibilitas-google-maps-lewat-apify.md)

## Konteks dan Masalah

Pada 2026-11-26 dan 27, pemilik produk memutuskan semua data "belum diperiksa" di production harus hilang dari tampilan publik. Di skema, data itu ada dalam empat bentuk:

1. Salinan petunjuk di `osm_places.tags`: `aksesbogor:google_maps` (atribut aksesibilitas Google Maps, [ADR-0052](0052-petunjuk-aksesibilitas-dari-mapbox-dan-google.md) dan [ADR-0056](0056-impor-atribut-aksesibilitas-google-maps-lewat-apify.md)) dan `aksesbogor:osm_aksesibilitas` (keterangan tag `wheelchair` OSM). Keduanya tampil di kartu kandidat dan dihitung di peringkat kecamatan ([ADR-0053](0053-peta-tematik-dan-batas-kota.md)).
2. Tempat yang diterbitkan langsung oleh admin (`places.admin_published_at` terisi, [ADR-0051](0051-kelola-tempat-oleh-admin-dan-terbit-langsung.md)), tanpa pemeriksaan orang kedua.
3. Laporan berstatus `menunggu` atau `klarifikasi`.
4. Kandidat OSM di lapisan "Belum ada data" ([ADR-0049](0049-lapisan-kandidat-openstreetmap-belum-ada-data.md)), 644 tempat dengan data 2026-11-22.

Tim tidak punya kapasitas memeriksa semuanya di lapangan. Pemilik produk memilih memeriksa setiap data dari tangkapan layar Google Street View. Kapan tangkapan itu boleh dianggap bukti, bagaimana menilainya, bagaimana hasilnya diberi label, dan apa yang terjadi pada data yang tidak bisa dijawab?

## Decision Drivers

- Tidak ada data yang belum diperiksa yang tampil sebagai informasi aksesibilitas, juga lewat kartu kandidat dan peringkat kecamatan.
- Label harus jujur: data dari Street View tidak sama dengan pemeriksaan di lokasi dan tidak boleh tertulis sebagai "Diverifikasi" saja.
- Setiap keputusan punya bukti gambar dan tanggal gambarnya, dan bisa ditelusuri di log audit ([ADR-0038](0038-log-audit-perubahan-data.md)).
- Data yang tidak terjawab disembunyikan, tidak dihapus, dan bisa dikembalikan dengan satu perintah.
- Laporan warga tidak boleh ditolak otomatis, karena penolakan mengirim email ke pelapor ([ADR-0039](0039-email-status-laporan.md)).

## Opsi yang Dipertimbangkan

1. Verifikasi per data dari bukti Street View, yang tidak terjawab disembunyikan
2. Pemeriksaan lapangan oleh tim untuk semua data
3. Mematikan semua petunjuk sekaligus (`PROVIDER_HINTS_ENABLED=false`) dan menurunkan semua tempat admin tanpa melihat buktinya

## Keputusan

Opsi terpilih: "Verifikasi per data dari bukti Street View", karena hanya opsi ini yang bisa dikerjakan tim sekarang dan tetap menyimpan data yang memang terlihat jelas.

### Cara mengambil bukti

- [gosom/google-maps-scraper](https://github.com/gosom/google-maps-scraper) mencari tempat di Google Maps dan memberi id pano Street View-nya.
- Titik pintu masuk diambil dari node OSM `entrance=*` di dalam atau pada bangunan tempat itu, lalu node `barrier=gate`, lalu pin Google Maps.
- Hanya pano resmi Google yang dipakai, bukan photosphere unggahan pengguna. Titik di jalan dekat pintu masuk dicoba satu per satu, dan pano dipilih dari yang bergambar 2022 atau lebih baru, paling jauh 40 m, terbaru, lalu terdekat.
- Setiap data mendapat satu gambar lebar (fov 80, menghadap pintu masuk) dan satu gambar dekat (fov 32, pitch −7). Gambar tambahan dari pano lain boleh diambil bila pandangan terhalang.
- Gambar dengan tanggal sebelum 1 Januari 2022 tetap diambil, tetapi tidak dihitung sebagai bukti.
- Tidak ada bagian gambar yang dipotong atau disembunyikan, termasuk logo dan atribusi Google.
- Gambar, hasil penilaian, dan manifest disimpan di luar repo, di host yang menjalankan pengambilan. Bentuk manifest dan langkahnya ada di [runbook verifikasi Street View](../operations/verifikasi-street-view.md).

### Aturan penilaian

- Hanya yang terlihat jelas di gambar yang dihitung.
- `toilet_difabel` dan `lift` selalu Belum tahu, karena Street View tidak masuk ke dalam gedung.
- Pintu masuk yang rata, tanpa anak tangga dari tempat turun kendaraan atau trotoar, berarti ramp Tidak perlu ([ADR-0017](0017-empat-jawaban-ketersediaan-fasilitas.md)). Ramp yang terlihat dan bisa dipakai berarti Tersedia.
- Tidak tersedia hanya bila titiknya terlihat jelas. Yang tidak terlihat karena jauh, buram, atau terhalang adalah Belum tahu. Setiap Tidak tersedia tetap butuh alasan ([ADR-0022](0022-tersedia-berarti-bisa-dipakai.md)).
- Guiding block di trotoar umum tidak dihitung sebagai guiding block tempat itu. Yang dihitung hanya yang ada di dalam pagar, dari gerbang atau tempat turun kendaraan ke pintu masuk. Pengecualiannya halte, karena halte tidak punya halaman sendiri.
- Sebuah data **terjawab** bila jawaban pintu masuk (ramp) bukan Belum tahu dan semua gambar yang dipasang bertanggal 2022 atau lebih baru. Selain itu data **tidak terjawab**.
- Penilaian ditulis orang di manifest. Pemilik produk atau verifikator memeriksa hasil dry run sebelum diterapkan.

### Data yang terjawab

- Tempat dibuat dari kandidat OSM, atau tempat yang sudah ada diperbarui: nama dan kategori dari manifest, pin di titik pintu masuk yang dinilai, dan jawaban fasilitas persis seperti penilaian. Status dan ringkasan dihitung dengan aturan biasa ([ADR-0018](0018-status-pintu-masuk-dulu-dan-ringkasan-fasilitas.md)).
- Gambar lebar dan dekat dipasang sebagai foto bertag Pintu masuk lewat jalur foto yang sama (diproses ulang menjadi WebP, lalu disimpan di bucket publik R2, [ADR-0031](0031-foto-laporan-di-bucket-r2-privat.md)). Teks alternatifnya menyebut pintu masuk dan nama tempat, dan keterangannya "Sumber: Google Street View, gambar Januari 2026".
- Tempat menyimpan sumber verifikasinya, `street_view`, dan bulan gambarnya. Tanggal verifikasi terakhir diisi awal bulan gambar itu, jadi tanda "Perlu dicek ulang" muncul 12 bulan setelah gambar dibuat, bukan setelah alat dijalankan.
- Salinan petunjuk di kandidat itu ditandai sudah diperiksa (`belum_diperiksa_tim: false`, dengan catatan hasil Street View) dan tidak lagi tampil sebagai petunjuk.
- Tempat admin yang terjawab menjadi tempat Street View; label admin tidak berlaku lagi.
- Tempat yang sudah diverifikasi lewat laporan tidak diubah oleh Street View.
- Bila verifikator kemudian menerbitkan laporan warga tentang tempat itu, sumbernya kembali `laporan`.

### Label publik

- Di mana pun web menulis "Diverifikasi" (kartu Jelajahi, Detail, kartu Peta, gambar berbagi, deskripsi halaman), tempat bersumber `street_view` ditulis **"Diverifikasi dari Street View · gambar Januari 2026"**, tidak pernah "Diverifikasi" saja.
- Keterangan foto menyebut sumbernya.
- API mengirim `verificationSource` (`laporan` atau `street_view`) dan `streetViewImageDate` (bulan gambar, `YYYY-MM`) di daftar dan Detail tempat, serta `source` di setiap foto.

### Data yang tidak terjawab: disembunyikan, tidak dihapus

- **Salinan petunjuk** dipindahkan dari `aksesbogor:google_maps` dan `aksesbogor:osm_aksesibilitas` ke `aksesbogor:disembunyikan`, lengkap dengan isinya, alasan, tanggal, dan nomor data di manifest. Pembaca petunjuk dan peringkat kecamatan tidak membaca kunci itu lagi.
- **Tempat admin** diturunkan dengan alasan lewat mekanisme [ADR-0051](0051-kelola-tempat-oleh-admin-dan-terbit-langsung.md). Admin bisa menayangkannya lagi dari Kelola tempat.
- **Kandidat OSM** tidak disembunyikan secara bawaan. Pemilik produk belum memutuskan apakah kandidat termasuk data "belum diperiksa"; kartunya tidak punya status. Alat bisa menyembunyikannya dengan opsi eksplisit, yang menulis `aksesbogor:kandidat_disembunyikan` dan membuatnya hilang dari Peta, daftar kandidat, hitungan kecamatan, dan alat agen.
- Semua yang disembunyikan bisa dikembalikan dengan menjalankan alat yang sama dengan `--batalkan`.

### Laporan tidak disentuh

Laporan yang menunggu tidak tampil untuk publik ([ADR-0006](0006-laporan-warga-diverifikasi-sebelum-tayang.md)) dan tetap diproses verifikator lewat antrean biasa. Alat Street View tidak pernah menulis tabel `reports`: tidak menolak, tidak menerbitkan, dan tidak membuat laporan. Daftar data hanya memuat id, jenis, dan status laporan, tanpa data pelapor dan tanpa isian teks.

### Alat

- `pnpm verifikasi:daftar` membaca keempat bentuk data dalam transaksi read-only dan menulis CSV beserta jumlahnya.
- `pnpm verifikasi:street-view <manifest.json>` menerapkan satu manifest dalam satu transaksi. Aman dijalankan ulang. `--dry-run` mencetak setiap perubahan tanpa menulis, dan `--batalkan` mengembalikan yang dibuat manifest itu. Setiap perubahan dicatat di log audit dengan pelaku `skrip`.
- Langkah production ada di [runbook verifikasi Street View](../operations/verifikasi-street-view.md).

### Risiko syarat Google

Gambar diambil dari Google Maps lewat browser otomatis, dan pano dicari dengan alat scraping. Gambar itu lalu disimpan dan diterbitkan sebagai foto AksesBogor. Ini bertentangan dengan [Syarat Layanan Tambahan Google Maps](https://www.google.com/help/terms_maps), sama seperti impor [ADR-0056](0056-impor-atribut-aksesibilitas-google-maps-lewat-apify.md). Pemilik produk menerima risiko ini.

Akibat terburuknya, Google meminta gambar itu dihapus. Karena itu:

- setiap foto Street View diberi sumber `street_view` di database dan atribusi Google di keterangannya;
- setiap manifest bisa dibatalkan dengan `--batalkan`, dan semua foto bersumber `street_view` bisa didaftar dan dihapus dengan langkah di runbook.

### Hasil uji 2026-11-26

Uji putaran kedua memeriksa 10 data: lima salinan Google Maps dan lima keterangan OSM, di rumah sakit, kampus, masjid, dan halte. Hanya 2 yang terjawab:

| Tempat | Klaim | Hasil |
| --- | --- | --- |
| Siloam Hospitals Bogor | Google: pintu masuk ramah kursi roda | Terjawab. Pintu masuk rata dari tempat turun kendaraan (gambar Januari 2026). |
| IBI Kesatuan (OSM: STIE Kesatuan Bogor) | OSM: `wheelchair=no` | Terjawab. Pintu masuk utama hanya bertangga (gambar Februari 2026). |
| RSUD Kota Bogor, RS PMI Bogor, Rumah Sakit Islam Bogor | Google dan OSM | Tidak terjawab. Pintu gedung ada di dalam kompleks yang tidak terjangkau pano jalan, atau terhalang pagar, papan nama, pohon, dan mobil. |
| Kampus IPB Baranangsiang (dua data) | Google dan OSM | Tidak terjawab. Gerbang rata, tetapi klaimnya tentang gedung di dalam kampus. |
| Masjid Raya Bogor | Google | Tidak terjawab. Gambar teras depan hanya dari 2018; gambar baru terhalang pagar. |
| Masjid Attaqwa VBI | OSM: `wheelchair=limited` | Tidak terjawab. Tidak ada gambar pintu masuk yang bisa dipakai. |
| Halte Botani Square | OSM: `wheelchair=limited` | Tidak terjawab. Lantai halte rata di balik kerb tinggi, tetapi akses naik bus dan ram kerb tidak terlihat. |

Sebabnya hampir selalu sama: pintu masuk rumah sakit, kampus, dan masjid ada di dalam halaman yang tidak dilewati mobil Street View.

## Konsekuensi

- Positif: tidak ada data belum diperiksa yang tampil, dan setiap data yang tersisa punya bukti gambar, tanggal gambar, dan label sumbernya.
- Positif: semua yang disembunyikan bisa dikembalikan, dan laporan warga tetap berjalan seperti biasa.
- Negatif: sebagian besar data akan hilang dari tampilan. Dengan hasil uji, kira-kira 8 dari 10 salinan petunjuk disembunyikan, dan peringkat kecamatan kehilangan sumber Google Maps dan keterangan OSM.
- Negatif: karena toilet dan lift selalu Belum tahu, tempat yang terjawab sering tetap berstatus Belum diketahui walau pintu masuknya sudah jelas. Contohnya Siloam: ramp Tidak perlu, tiga fasilitas lain Belum tahu.
- Negatif: gambar Street View bisa berumur sampai beberapa tahun. Bulan gambar selalu tampil di label.
- Negatif: risiko syarat Google di atas.
- Negatif: memuat ulang data OSM (`pnpm pengetahuan:fasilitas`) mengganti semua baris `osm_places`, dan impor Google Maps menulis salinan baru. Keduanya bisa memunculkan lagi salinan yang sudah disembunyikan. Setelah keduanya, semua manifest harus diterapkan lagi (langkahnya di runbook).

## Pro & Kontra tiap Opsi

### Verifikasi per data dari Street View

- Pro: bisa dikerjakan dari jauh, setiap keputusan punya bukti gambar, dan yang terlihat jelas tetap tayang.
- Kontra: hanya pintu masuk yang terlihat dari jalan yang bisa dinilai; risiko syarat Google.

### Pemeriksaan lapangan

- Pro: bukti paling kuat, termasuk toilet dan lift.
- Kontra: tim tidak punya kapasitas untuk ratusan tempat sekarang.

### Matikan semua sekaligus

- Pro: paling cepat dan tanpa risiko syarat baru.
- Kontra: data yang sebenarnya bisa dibuktikan ikut hilang, dan tempat admin turun tanpa ada yang melihat buktinya.

## Pertanyaan Terbuka

- Apakah kandidat OSM ([ADR-0049](0049-lapisan-kandidat-openstreetmap-belum-ada-data.md)) termasuk data belum diperiksa? Kartunya menampilkan tag `wheelchair` OSM apa adanya dengan label "belum diperiksa tim".
- Apakah petunjuk langsung Mapbox dan Google ([ADR-0052](0052-petunjuk-aksesibilitas-dari-mapbox-dan-google.md)) juga harus dimatikan? Isinya tidak disimpan, tetapi sama-sama belum diperiksa.
- Apakah impor Google Maps berikutnya ([ADR-0056](0056-impor-atribut-aksesibilitas-google-maps-lewat-apify.md)) masih dijalankan, mengingat setiap salinan baru harus lewat putaran Street View sebelum boleh tayang?

## Referensi

- [ADR-0006](0006-laporan-warga-diverifikasi-sebelum-tayang.md), [ADR-0017](0017-empat-jawaban-ketersediaan-fasilitas.md), [ADR-0018](0018-status-pintu-masuk-dulu-dan-ringkasan-fasilitas.md), [ADR-0022](0022-tersedia-berarti-bisa-dipakai.md), [ADR-0031](0031-foto-laporan-di-bucket-r2-privat.md), [ADR-0038](0038-log-audit-perubahan-data.md), [ADR-0039](0039-email-status-laporan.md), [ADR-0049](0049-lapisan-kandidat-openstreetmap-belum-ada-data.md), [ADR-0051](0051-kelola-tempat-oleh-admin-dan-terbit-langsung.md), [ADR-0052](0052-petunjuk-aksesibilitas-dari-mapbox-dan-google.md), [ADR-0053](0053-peta-tematik-dan-batas-kota.md), [ADR-0056](0056-impor-atribut-aksesibilitas-google-maps-lewat-apify.md)
- [Runbook verifikasi Street View](../operations/verifikasi-street-view.md)
- [Syarat Layanan Tambahan Google Maps](https://www.google.com/help/terms_maps)
