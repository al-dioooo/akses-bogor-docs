# ADR-0053: Peta Tematik dan Batas Kota

- Status: Accepted
- Tanggal: 2026-11-26
- Pengambil keputusan: pemilik produk
- Issue: #125
- Melengkapi: [ADR-0016](0016-peta-dan-pencarian-mapbox.md) (Peta), [ADR-0034](0034-batas-kecamatan-dari-openstreetmap.md) (batas kecamatan), [ADR-0043](0043-agen-untuk-kontributor-dengan-model-gratis-openrouter.md) (data Dinsos dan prioritas kecamatan), [ADR-0049](0049-lapisan-kandidat-openstreetmap-belum-ada-data.md) (kandidat OSM), [ADR-0052](0052-petunjuk-aksesibilitas-dari-mapbox-dan-google.md) (petunjuk pihak ketiga)

## Konteks dan Masalah

QA rekan tim tentang Peta menemukan tiga hal. Batas Kota Bogor tidak terlihat, jadi sulit tahu tempat mana yang masih di dalam kota. Marker lokasi dibedakan oleh warna status, dan kategorinya hanya tampil di lencana kecil, sedangkan lingkaran kandidat OSM baru berikon mulai zoom 14. Mereka juga meminta dua peta tematik: persebaran penyandang disabilitas dan kecamatan yang paling aksesibel.

Peta sudah punya satu lapisan tematik, "Lapisan prioritas kecamatan" ([ADR-0043](0043-agen-untuk-kontributor-dengan-model-gratis-openrouter.md)). Data Dinas Sosial yang dipakainya hanya per kecamatan (enam kecamatan, tahun 2022–2024, empat jenis). Production belum punya lokasi terverifikasi tim, jadi keterangan aksesibilitas yang ada sekarang hanya dari pihak ketiga: tag `wheelchair` OSM dan salinan tersimpan `aksesbogor:google_maps` dan `aksesbogor:osm_aksesibilitas` ([ADR-0052](0052-petunjuk-aksesibilitas-dari-mapbox-dan-google.md)).

Bagaimana batas kota dan kategori ditampilkan, bagaimana beberapa lapisan tematik dipilih, dan bagaimana kecamatan diberi peringkat aksesibilitas tanpa membuat data yang belum diperiksa tim terlihat seperti data AksesBogor?

## Decision Drivers

- Tempat yang belum diperiksa tim tidak boleh terlihat atau terhitung sebagai data AksesBogor ([ADR-0006](0006-laporan-warga-diverifikasi-sebelum-tayang.md), [ADR-0049](0049-lapisan-kandidat-openstreetmap-belum-ada-data.md), [ADR-0052](0052-petunjuk-aksesibilitas-dari-mapbox-dan-google.md)). Setiap angka menyebut sumbernya.
- Angka disabilitas selalu datang bersama batas datanya: yang terdaftar di Dinas Sosial, bukan seluruh penduduk ([ADR-0043](0043-agen-untuk-kontributor-dengan-model-gratis-openrouter.md)). Istilahnya mengikuti [ADR-0041](0041-halaman-edukasi-sebagai-draf-sampai-ditinjau-komunitas.md).
- WCAG 2.2 AA ([ADR-0021](0021-wcag-22-aa-dan-pengujian-berlapis.md)): status dan kelas warna tidak hanya ditandai warna, dan setiap lapisan punya tabel atau daftar yang bisa dipakai dengan keyboard dan pembaca layar, juga di lebar 320 px.
- Peta tetap terbaca di tema terang dan gelap, dan di ponsel kelas bawah.
- Ukurannya bisa dijelaskan dalam satu kalimat.

## Opsi yang Dipertimbangkan

1. Pemilih "Peta tematik" (satu lapisan sekaligus), choropleth per kecamatan, dan ukuran "pintu masuk yang bisa dimasuki" dari semua sumber dengan rincian per sumber
2. Tombol lapisan terpisah yang bisa menyala bersamaan
3. Heatmap titik untuk data disabilitas, dan peringkat aksesibilitas dari data AksesBogor saja

## Keputusan

Opsi terpilih: "Pemilih Peta tematik dengan choropleth per kecamatan", karena data disabilitas hanya ada per kecamatan, dua lapisan berwarna yang bertumpuk tidak terbaca, dan tanpa data pihak ketiga peringkat aksesibilitas kosong sampai ada lokasi terverifikasi.

### Batas Kota Bogor

- `GET /districts/boundaries` ikut membawa `outline`: gabungan (`ST_Union`) batas keenam kecamatan [ADR-0034](0034-batas-kecamatan-dari-openstreetmap.md). Lubang dan potongan kecil di antara batas kecamatan dibuang, lalu garisnya disederhanakan (toleransi 0,0001°, kira-kira 11 m) dengan 5 desimal.
- Web memuat batas ini setiap kali peta dibuka, dan browser menyimpannya sehari seperti sebelumnya.
- Garisnya selalu tampil: garis putus-putus tebal berwarna teks utama (gelap di peta terang, terang di peta gelap) di atas garis halo selebar dua kali berwarna latar. Area di luar kota diberi lapisan tipis berwarna latar supaya sedikit pudar.
- Urutan lapisan, dari bawah: isi lapisan tematik, garis kecamatan, area luar kota, batas kota, label peta dasar, lingkaran kandidat, lalu marker lokasi (elemen HTML).
- Garis kecamatan tipis hanya tampil selama ada lapisan tematik.
- Legenda menyebut garis ini "Batas Kota Bogor". Bila API belum mengirim `outline`, garisnya tidak tampil dan bagian lain Peta tetap bekerja.

### Ikon kategori di setiap marker

- Marker lokasi AksesBogor: lingkaran berwarna status berisi ikon kategori, yaitu ikon yang sama dengan chip filter. Lencana kecil berwarna status dengan ikon status ada di kanan bawahnya, jadi status tetap ditandai warna dan ikon, dan nama tombol marker tetap menyebut nama, kategori, dan status.
- Lingkaran kandidat OSM: tetap berlubang dan bergaris warna kategori, tanpa warna atau ikon status. Ikon kategori tampil mulai zoom 12 (lingkaran lebih kecil sampai zoom 14). Di bawah zoom 12, yaitu tampilan seluruh kota, lingkarannya tetap kecil tanpa ikon supaya ratusan titik tidak saling menutup.
- Legenda menjelaskan bagian marker: warna dan lencana untuk status, ikon di tengah untuk kategori.

### Pemilih Peta tematik

- Tombol "Lapisan prioritas kecamatan" diganti tombol "Peta tematik" yang membuka pilihan: Tidak ada, Prioritas pemetaan, Persebaran disabilitas, dan Kecamatan paling aksesibel. Hanya satu lapisan yang tampil sekaligus. Pilihan tidak disimpan di URL.
- Prioritas pemetaan dan Persebaran disabilitas hanya ada selama `DISABILITY_DATA_ENABLED` menyala.
- Setiap lapisan punya kunci yang mengambang di atas peta, dengan perilaku yang sama dengan kunci prioritas sekarang: ringkas satu baris di ponsel, tombol "Catatan", tombol "Tabel" yang membuka panel daftar di tabel atau daftar lapisan itu, dan disembunyikan bila tidak ada ruang.
- Semua lapisan tematik memakai skala ungu yang sama (violet-100, 300, 500, 800), terpisah dari warna status. Setiap kelas warna ditulis sebagai teks di kunci dan di tabel.

### Persebaran disabilitas

- `GET /districts/disability` mengirim semua jumlah terdaftar per kecamatan, jenis, dan tahun, bersama kalimat batas data dan sumbernya. Selama `DISABILITY_DATA_ENABLED` mati, jawabannya 503 `data_disabilitas_belum_aktif`, sama seperti `/districts/priority`.
- Choropleth per kecamatan, bukan heatmap, karena datanya tidak punya titik.
- Filter di kunci: jenis (Semua jenis, Disabilitas fisik, Disabilitas netra, Disabilitas mental, Jenis lain) dan tahun, dengan tahun terbaru sebagai bawaan. Label "cacat" dari sumber tidak pernah ditampilkan.
- Empat kelas dengan lebar sama, dari 0 sampai angka terbesar pilihan itu yang dibulatkan ke angka bulat. Kunci menulis rentang angkanya, mis. "400–799".
- Kunci dan tabel selalu memuat kalimat "Angka ini jumlah penyandang disabilitas yang terdaftar di Dinas Sosial Kota Bogor, bukan jumlah seluruh penyandang disabilitas" dan sumber "Open Data Kota Bogor, Dinas Sosial Kota Bogor" dengan tahunnya.
- Tabel di panel: kecamatan, jumlah terdaftar untuk pilihan itu, dan kelas warnanya, urut dari yang terbanyak.

### Kecamatan paling aksesibel

Tempat yang dihitung di setiap kecamatan adalah lokasi AksesBogor yang terbit dan kandidat OSM yang belum punya data AksesBogor ([ADR-0049](0049-lapisan-kandidat-openstreetmap-belum-ada-data.md)). Setiap tempat dihitung sekali, dengan keterangan pintu masuk dari sumber pertama yang punya keterangan:

| Urutan | Sumber | Bisa dimasuki | Terbatas | Tidak bisa |
| --- | --- | --- | --- | --- |
| 1 | Data AksesBogor: jawaban Ramp / Jalur Landai ([ADR-0018](0018-status-pintu-masuk-dulu-dan-ringkasan-fasilitas.md)) | Tersedia, Tidak perlu | | Tidak tersedia |
| 2 | Tag `wheelchair` OSM tempat itu | `yes`, `designated` | `limited` | `no` |
| 3 | Keterangan OSM tersimpan `aksesbogor:osm_aksesibilitas` | `yes`, `designated` | `limited` | `no` |
| 4 | Salinan Google Maps tersimpan `aksesbogor:google_maps`, opsi pintu masuk | ada di `aksesibilitas` | | ada di `tidak_tersedia` |

Data AksesBogor didahulukan dan mencakup lokasi terverifikasi maupun yang diterbitkan langsung oleh admin ([ADR-0051](0051-kelola-tempat-oleh-admin-dan-terbit-langsung.md)). Sumber 3 dan 4 hanya dihitung selama `PROVIDER_HINTS_ENABLED` menyala, karena saklar itu juga berlaku untuk salinan tersimpan ([ADR-0052](0052-petunjuk-aksesibilitas-dari-mapbox-dan-google.md)).

Ukurannya:

> skor = 100 × tempat yang pintu masuknya bisa dimasuki kursi roda ÷ tempat yang punya keterangan pintu masuk, dibulatkan.

- "Terbatas" ikut di penyebut tetapi tidak di pembilang, jadi skornya tidak melebih-lebihkan.
- Kecamatan dengan kurang dari 5 tempat ber-keterangan ditulis "Data belum cukup", tanpa skor, warna, atau peringkat. Di peta, kecamatan itu diarsir.
- Peringkat: skor tertinggi dulu, lalu tempat ber-keterangan terbanyak, lalu nama.
- Kelas warna: 0–24, 25–49, 50–74, dan 75–100.
- `GET /districts/accessibility` mengirim per kecamatan: jumlah tempat, tempat ber-keterangan, bisa, terbatas, dan tidak bisa, skor dan peringkat (atau null), serta rincian per sumber (AksesBogor, OpenStreetMap, Google Maps). Jawabannya memakai `cache-control: private, no-store` seperti petunjuk penyedia.

### Label data yang belum diperiksa tim

- Skor tidak pernah tampil sendirian. Di kunci dan di daftar, setiap skor ditemani jumlah tempat ber-keterangan, cakupannya ("13 dari 110 tempat punya keterangan"), dan berapa yang belum diperiksa tim beserta sumbernya ("12 belum diperiksa tim: OpenStreetMap 8, Google Maps 4").
- Selama ada keterangan dari pihak ketiga, judul kunci diikuti label "belum diperiksa tim", dan kata "terverifikasi" tidak dipakai untuk lapisan ini.
- Sumber selalu disebut dengan atribusinya: "© kontributor OpenStreetMap" yang menaut ke halaman hak cipta OSM, dan "Google Maps" dengan tanggal pengambilan salinan terbarunya.
- Seperti petunjuk penyedia, angka ini tidak diberikan ke agen kontributor atau asisten rute. Modul penghitungnya ada di modul petunjuk di API, jadi tes "tidak untuk AI" yang sudah ada ikut menjaganya.
- Daftar peringkat di panel, di samping peta, adalah alternatif lapisan ini: urut peringkat, lalu kecamatan dengan data belum cukup.

## Konsekuensi

- Positif: batas kota, kategori, dan dua peta tematik baru menjawab QA tanpa menambah tombol di pita atas ponsel.
- Positif: peringkat aksesibilitas sudah berisi sebelum ada lokasi terverifikasi, dan sumbernya terlihat di setiap angka.
- Negatif: hampir semua keterangan sekarang dari pihak ketiga dengan kriteria yang berbeda dari AksesBogor, dan jumlahnya sedikit per kecamatan. Peringkat bisa berubah besar setiap kali data bertambah. Ambang 5 tempat dan rincian per sumber membatasi salah baca, tetapi tidak menghilangkannya.
- Negatif: kandidat OSM yang sudah punya data AksesBogor di dekatnya tidak dihitung terpisah, jadi keterangan OSM-nya tidak dipakai lagi setelah ada lokasi AksesBogor. Ini disengaja: data AksesBogor yang utama.
- Negatif: batas kota menambah satu permintaan kecil setiap kali Peta dibuka (disimpan browser sehari).
- Negatif: syarat tampilan Google untuk salinan tersimpan, termasuk pemakaiannya bersama peta non-Google, tetap harus masuk cakupan kemitraan yang disebut di [ADR-0052](0052-petunjuk-aksesibilitas-dari-mapbox-dan-google.md). Bila tidak, mematikan `PROVIDER_HINTS_ENABLED` juga mengeluarkan Google dari skor.

## Pro & Kontra tiap Opsi

### Pemilih Peta tematik, choropleth, semua sumber dengan rincian

- Pro: satu lapisan sekaligus selalu terbaca; ukurannya sederhana dan sumbernya terlihat.
- Kontra: satu ketukan lagi untuk berganti lapisan; skor bergantung pada data pihak ketiga yang sedikit.

### Tombol lapisan terpisah

- Pro: lapisan bisa dibandingkan langsung.
- Kontra: warna yang bertumpuk tidak terbaca, dan pita atas ponsel tidak muat tombol tambahan.

### Heatmap dan data AksesBogor saja

- Pro: heatmap terlihat menarik; data AksesBogor saja tidak butuh label.
- Kontra: heatmap dari data per kecamatan memalsukan sebaran di dalam kecamatan; peringkat dari data AksesBogor saja kosong sampai ada lokasi terverifikasi.

## Referensi

- [ADR-0006](0006-laporan-warga-diverifikasi-sebelum-tayang.md), [ADR-0016](0016-peta-dan-pencarian-mapbox.md), [ADR-0018](0018-status-pintu-masuk-dulu-dan-ringkasan-fasilitas.md), [ADR-0021](0021-wcag-22-aa-dan-pengujian-berlapis.md), [ADR-0034](0034-batas-kecamatan-dari-openstreetmap.md), [ADR-0041](0041-halaman-edukasi-sebagai-draf-sampai-ditinjau-komunitas.md), [ADR-0043](0043-agen-untuk-kontributor-dengan-model-gratis-openrouter.md), [ADR-0049](0049-lapisan-kandidat-openstreetmap-belum-ada-data.md), [ADR-0051](0051-kelola-tempat-oleh-admin-dan-terbit-langsung.md), [ADR-0052](0052-petunjuk-aksesibilitas-dari-mapbox-dan-google.md)
- [Key:wheelchair](https://wiki.openstreetmap.org/wiki/Key:wheelchair) di wiki OpenStreetMap
- Issue api#145, web#221, web#222
