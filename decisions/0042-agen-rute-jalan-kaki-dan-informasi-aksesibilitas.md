# ADR-0042: Agen Rute Memberi Rute Jalan Kaki ke Pintu Masuk dan Informasi Aksesibilitas, Dibangun Tanpa Menunggu Penyedia Model

- Status: Proposed
- Tanggal: 2026-11-16
- Pengambil keputusan: Tim AksesBogor
- Issue: #73
- Melengkapi: [ADR-0016](0016-peta-dan-pencarian-mapbox.md), [ADR-0017](0017-empat-jawaban-ketersediaan-fasilitas.md), [ADR-0022](0022-tersedia-berarti-bisa-dipakai.md), [ADR-0032](0032-pencarian-tempat-lewat-api-mapbox-search-box.md), [ADR-0034](0034-batas-kecamatan-dari-openstreetmap.md)

## Konteks dan Masalah

Pengguna ingin bertanya kepada sebuah agen AI bagaimana cara sampai ke sebuah tempat dari posisinya sekarang, dan apa yang bisa diharapkan soal aksesibilitas di jalan dan di tujuan. PRD bagian 3 mencatat "Navigasi rute langkah demi langkah" sebagai non-tujuan. Penyedia model belum dipilih: model gratis lewat OpenRouter atau model berbayar seperti Claude. Apa cakupan agen ini, dari mana rute dan pengetahuannya diambil, bagaimana lisensi dan privasinya, dan bagian apa yang bisa dibangun sebelum model dipilih?

## Decision Drivers

- Pengguna kursi roda dan tunanetra perlu tahu sebelum berangkat apa yang menunggu di jalan dan di pintu masuk.
- Agen tidak boleh memberi rasa aman palsu. Rute yang disebut "aksesibel" padahal melewati tangga lebih berbahaya daripada tidak ada rute.
- Setiap klaim bisa ditelusuri ke sumbernya: jawaban terverifikasi AksesBogor, OpenStreetMap, atau pasal regulasi.
- Semua bagian berjalan dan bisa dites tanpa model, dan panggilan ke model nanti ada di balik satu antarmuka kecil.
- Biaya mendekati nol: kuota gratis Mapbox, dan model gratis bila memadai.
- Posisi pengguna adalah data pribadi (UU No. 27 Tahun 2022).
- Lisensi dan syarat setiap sumber data dipatuhi.

## Opsi yang Dipertimbangkan

Cakupan:

1. Agen yang memberi rute jalan kaki ke pin tujuan dan informasi aksesibilitas, tanpa navigasi langsung
2. Navigasi langsung belokan demi belokan yang mengikuti posisi pengguna
3. Tanpa agen

Rute:

1. Mapbox Directions profil `walking` dari API
2. Perute sendiri dari data OpenStreetMap, mis. Valhalla dengan profil kursi roda
3. Tanpa rute, hanya informasi tujuan

Retrieval:

1. Full-text search PostgreSQL dengan konfigurasi `simple`, ditambah `pg_trgm`
2. Embedding dari penyedia luar dengan pgvector
3. Seluruh teks dimasukkan ke prompt

Penyedia model:

1. Model gratis lewat OpenRouter
2. Model berbayar, mis. Claude lewat API Anthropic

## Keputusan

Opsi terpilih: "Agen yang memberi rute jalan kaki ke pin tujuan dan informasi aksesibilitas", dengan rute dari "Mapbox Directions profil `walking`" dan retrieval "Full-text search PostgreSQL", karena ketiganya bisa dibangun dan dites sekarang dengan data dan layanan yang sudah ada. Penyedia model belum diputuskan. Fase 1 membangun semua bagian kecuali panggilan ke model.

### Cakupan

- Pengguna yang sudah masuk dan melengkapi akun memilih tujuan dari lokasi di database AksesBogor, lalu bertanya cara ke sana dari posisinya.
- Agen menjawab dengan:
  - rute jalan kaki ke pin tujuan: jarak, perkiraan waktu, dan langkah;
  - status tujuan dan jawaban keempat fasilitas yang sudah diverifikasi, dengan tanggal verifikasi terakhir;
  - hambatan yang tercatat di OpenStreetMap di dekat rute, mis. tangga dan kerb tinggi;
  - kutipan regulasi, aturan AksesBogor, atau rute angkutan umum bila relevan, dengan sumbernya.
- Yang bukan cakupan:
  - navigasi langsung yang mengikuti posisi, petunjuk suara, atau rute ulang otomatis;
  - tujuan di luar database AksesBogor atau di luar Kota Bogor ([ADR-0010](0010-cakupan-wilayah-kota-bogor.md));
  - menilai apakah sebuah rute aksesibel;
  - rute kendaraan;
  - menyimpan percakapan. Bila nanti dibutuhkan, keputusannya lewat ADR baru.
- PRD: non-tujuan "Navigasi rute langkah demi langkah" diganti "Navigasi langsung belokan demi belokan", dan FR-60 (Could) ditambahkan.

### Rute jalan kaki dan aksesibilitas

- API memanggil Mapbox Directions profil `walking` dari posisi pengguna ke pin lokasi. Pin adalah titik yang dikonfirmasi pelapor di pintu masuk utama ([Panduan Verifikasi](../operations/panduan-verifikasi.md)). Tokennya token rahasia di `api.env` yang sudah dipakai untuk pencarian tempat ([ADR-0032](0032-pencarian-tempat-lewat-api-mapbox-search-box.md)); Directions tidak butuh scope tambahan.
- Posisi pengguna dibulatkan ke 4 desimal (sekitar 11 m) sebelum dikirim. Posisi harus di dalam kotak Kota Bogor dan paling jauh 10 km garis lurus dari tujuan.
- Jawaban Mapbox tidak disimpan di database atau di log. Product Terms Mapbox (versi 20 Oktober 2026, §2.10.1) melarang menyimpan hasil Navigation API.
- Profil `walking` tidak mengenal kursi roda: tangga, trotoar, kerb, dan kemiringan tidak dihitung. Karena itu:
  - agen tidak pernah menyebut rute "aksesibel" atau "ramah kursi roda";
  - setiap hasil rute membawa kalimat peringatan tetap dari API, yang juga ditampilkan antarmuka tanpa lewat model;
  - aksesibilitas tujuan hanya dari jawaban terverifikasi. "Belum tahu" tetap disebut belum diketahui ([ADR-0017](0017-empat-jawaban-ketersediaan-fasilitas.md), [ADR-0022](0022-tersedia-berarti-bisa-dipakai.md)) dan tidak ditebak dari tag OpenStreetMap atau sumber lain.
- Hambatan di jalan diambil dari fitur OpenStreetMap dalam jarak 20 m (paling jauh 50 m) dari garis rute: tangga, kerb, ramp, lift, pintu masuk, dan guiding block. Agen menyebutnya "tercatat di OpenStreetMap" dan menyampaikan bahwa daftar kosong bukan berarti jalannya bebas hambatan.
- Tag `wheelchair=*` di OpenStreetMap tidak mengubah status atau jawaban fasilitas AksesBogor.

### Sumber data dan lisensi

| Sumber | Isi | Lisensi dan kewajiban | Cara ambil |
| --- | --- | --- | --- |
| OpenStreetMap | Fitur bertag `wheelchair`, `toilets:wheelchair`, `ramp`, `kerb`, `tactile_paving`; tangga; lift; pintu masuk bertag akses; halte | ODbL 1.0. Atribusi "© kontributor OpenStreetMap" dengan tautan ke openstreetmap.org/copyright di setiap jawaban yang memakainya. Tabel OSM di API adalah database turunan: bila isinya dibagikan ke publik dalam jumlah besar, harus berlisensi ODbL | Satu kueri area Overpass untuk relasi Kota Bogor (14745927), tanpa `meta`, hanya tag dalam daftar izin, lalu dipotong dengan batas [ADR-0034](0034-batas-kecamatan-dari-openstreetmap.md) |
| Angkutan umum | Relasi `route=bus` OSM: BisKita Trans Pakuan dan angkot, dengan jalan yang dilewati dan halte | ODbL, sama dengan OSM | Kueri Overpass yang sama |
| Regulasi | UU No. 8 Tahun 2016; Permen PUPR No. 14/PRT/M/2017 beserta Lampiran I–IV | Tidak ada hak cipta atas peraturan perundang-undangan (UU No. 28 Tahun 2014, Pasal 42 huruf b) | PDF resmi di peraturan.go.id dan ZIP resmi di JDIH Kementerian PU, dipotong per pasal dan per bagian lampiran |
| Dokumen AksesBogor | Halaman edukasi Aksesibilitas, Disabilitas, dan Tips (masih draf, [ADR-0041](0041-halaman-edukasi-sebagai-draf-sampai-ditinjau-komunitas.md)); Panduan Verifikasi; ADR-0017, ADR-0018, ADR-0022 | Milik tim | Repo publik di GitHub, cabang `main` |

- Setiap baris data membawa sumber, lisensi, dan tanggal ambil, juga SHA-256 berkas yang diunduh.
- Tidak ada data pribadi: tidak ada nama penyunting atau changeset OSM, dan tag kontak dibuang.
- Situs Dishub Kota Bogor dan BisKita tidak dipakai. Per 2026-11-16 keduanya tidak menerbitkan data rute atau halte yang bisa diunduh, dan domain BisKita tidak bisa dihubungi. Feed GTFS "Bogor Angkots" di Mobility Database (CC0) juga tidak dipakai, karena bukan sumber resmi dan datanya dari 2016.
- Salinan Berita Negara Permen PUPR di peraturan.go.id tidak dipakai karena lampirannya gambar pindaian tanpa teks. ZIP di JDIH Kementerian PU berisi teks untuk batang tubuh dan keempat lampiran. Syarat situs JDIH melarang pemakaian komersial materi situsnya; AksesBogor tidak komersial, dan teks peraturannya sendiri tanpa hak cipta. Salinan tidak resmi tidak pernah dipakai sebagai pengganti.
- Overpass: kebijakan pemakaian server overpass-api.de meminta kurang dari 100 kueri dan 10 MB per hari untuk pemakaian rutin, satu koneksi pada satu waktu, dan User-Agent yang menyebut nama aplikasi. Satu kueri area untuk Kota Bogor sekitar 1,6 MB dan dijalankan manual.
- Pembaruan: data OSM dan angkutan sekitar sebulan sekali; regulasi bila ada perubahan; dokumen AksesBogor setiap kali dokumen sumbernya berubah. Semuanya lewat skrip yang bisa dijalankan ulang tanpa duplikat.

### Retrieval tanpa penyedia embedding

- Potongan teks disimpan di PostgreSQL dengan kolom `tsvector` berkonfigurasi `simple`, dihitung dari sitasi, judul, dan isi; sitasi dan judul berbobot lebih tinggi. PostgreSQL tidak punya stemmer bahasa Indonesia.
- Pertanyaan dipecah menjadi kata dan kata umum dibuang. Kata 4 huruf atau lebih dicocokkan sebagai awalan, jadi "akses" juga menemukan "aksesibilitas". Kata-kata digabung dengan OR, dan hasil diurutkan menurut jumlah kata yang cocok, lalu `ts_rank_cd`. Bila tidak ada yang cocok, misalnya karena salah ketik, dipakai kemiripan kata `pg_trgm`.
- Hasilnya paling banyak 8 potongan, masing-masing sekitar 1.500 karakter atau kurang, dengan sitasi (mis. "Permen PUPR No. 14/PRT/M/2017, Lampiran II, B.2 Ram"), sumber, lisensi, dan tanggal ambil.
- Kolom embedding (pgvector) bisa ditambah nanti tanpa mengubah pemanggil. Memilihnya butuh ADR baru, karena penyedia embedding juga menerima teks.

### Alat agen

| Alat | Isi |
| --- | --- |
| `find_place` | Lokasi AksesBogor bernama mirip, paling banyak 5 |
| `place_accessibility` | Status, jawaban fasilitas terverifikasi dengan alasan dan catatan, dan tanggal verifikasi terakhir |
| `walking_route` | Rute jalan kaki Mapbox: jarak, waktu, langkah, garis rute, dan peringatan tetap |
| `route_obstacles` | Fitur OSM di dekat garis rute, urut dari awal rute, dengan tautan ke OSM |
| `search_knowledge` | Potongan regulasi, dokumen AksesBogor, dan rute angkutan dengan sitasi dan lisensi |

- Nama, deskripsi, skema input, dan skema output setiap alat ada di satu tempat di API. Definisi JSON Schema untuk penyedia model yang mendukung tool calling dibuat dari skema itu.
- Kelima alat juga tersedia sebagai route API untuk akun yang sudah masuk dan dilengkapi, dengan tes.
- Panggilan ke model nanti ada di balik satu antarmuka dengan satu fungsi: instruksi sistem, percakapan, dan definisi alat masuk; jawaban atau permintaan memanggil alat keluar.
- Aturan jawaban, apa pun penyedia modelnya: bahasa Indonesia sederhana ([ADR-0020](0020-antarmuka-bahasa-indonesia-siap-i18n.md)); rute tidak pernah disebut aksesibel dan peringatannya selalu disampaikan; "Belum tahu" tetap belum diketahui; sumber dan atribusi disebut; tujuan di luar database AksesBogor ditolak dengan sopan.

### Privasi

- Saat model dipakai, posisi pengguna dan pertanyaannya dikirim ke penyedia model, dan posisi yang sudah dibulatkan dikirim ke Mapbox Directions bersama pin tujuan. Kebijakan Privasi versi 2026-11-10 belum menyebut keduanya.
- Syarat sebelum peluncuran: Kebijakan Privasi versi baru menyebut penyedia model (nama, data yang dikirim, negara pemrosesan, lama penyimpanan, dan apakah dipakai untuk pelatihan) serta Mapbox Directions. Versi baru memicu persetujuan ulang lewat Lengkapi Akun, seperti versi sebelumnya. ADR ini tidak mengubah Kebijakan Privasi.
- Server tidak menyimpan posisi, pertanyaan, jawaban model, atau jawaban Mapbox, dan log tidak mencatat badan permintaan.
- Route alat agen mati di production (`ROUTE_AGENT_ENABLED=false`) sampai syarat peluncuran terpenuhi.

### Biaya dan penyalahgunaan

- Hanya akun yang sudah masuk dan dilengkapi ([ADR-0011](0011-pelapor-wajib-login.md), [ADR-0029](0029-akun-better-auth-dan-sesi-lewat-server-web.md)).
- Batas per akun: 120 panggilan alat per jam untuk semua alat bersama, dan 30 rute jalan kaki per jam.
- Batas untuk semua pengguna: 1.000 rute Mapbox per hari, jauh di bawah kuota gratis Directions 100.000 permintaan per bulan. Batas per IP 120 permintaan per menit tetap berlaku.
- Batas pertanyaan ke model per akun per hari diputuskan bersama penyedia model, karena tergantung kuota dan biayanya.

### Penyedia model belum diputuskan

| | Model gratis lewat OpenRouter | Model berbayar, mis. Claude |
| --- | --- | --- |
| Biaya | Nol per token | Dibayar per token |
| Batas permintaan | 20 per menit; 50 per hari tanpa kredit, atau 1.000 per hari setelah membeli kredit minimal 10 USD (per 2026-11-16). Satu pertanyaan bisa butuh 3 sampai 5 permintaan karena pemanggilan alat | Menurut tier akun |
| Keandalan | Model gratis bisa dicabut atau berganti tanpa pemberitahuan; mutu tool calling dan bahasa Indonesia berbeda-beda | Stabil |
| Tool calling | Tergantung model; hanya model dengan dukungan `tools` yang bisa dipakai | Didukung |
| Pemakaian data | Banyak endpoint gratis mencatat prompt, sebagian untuk melatih model. OpenRouter punya pengaturan untuk tidak memakai penyedia yang melatih dan parameter permintaan `data_collection: "deny"` | Data API tidak dipakai melatih secara bawaan dan dihapus dalam 30 hari; tanpa penyimpanan (ZDR) lewat kesepakatan |

Keputusan penyedia diambil lewat ADR fase 2, setelah kedua jenis dicoba dengan alat ini dan sekumpulan pertanyaan uji, termasuk tujuan dengan jawaban "Belum tahu", rute yang melewati tangga, dan tujuan di luar database.

### Syarat sebelum agen dibuka untuk pengguna

1. Penyedia model dipilih lewat ADR.
2. Kebijakan Privasi versi baru berlaku dan pengguna sudah diminta persetujuan ulang.
3. Syarat Mapbox untuk AI terjawab. Product Terms Mapbox §1.5 melarang memakai layanan Mapbox untuk "train, operate or improve" teknologi machine learning atau AI, dan §1.10 melarang mengungkapkan hasilnya kepada pihak lain. Sebelum hasil Directions dikirim ke model, tim memilih salah satu: konfirmasi tertulis dari Mapbox; rancangan yang tidak mengirim hasil Directions ke model (antarmuka menampilkan rute langsung, model hanya menerima data AksesBogor, OSM, dan retrieval); atau perute lain lewat ADR baru.
4. Antarmuka menampilkan atribusi Mapbox dan OpenStreetMap bersama rute dan hambatan.

## Konsekuensi

- Positif: semua bagian selain model sudah bisa dites, dan penyedia model bisa diganti tanpa mengubah alat atau data.
- Positif: jawaban agen bisa ditelusuri ke pasal, dokumen, atau fitur OSM tertentu.
- Positif: retrieval tidak butuh layanan luar dan berjalan di PostgreSQL yang sama.
- Negatif: rute Mapbox tidak memperhitungkan kursi roda; agen hanya bisa memperingatkan dan menyebut hambatan yang tercatat. Data OSM untuk Kota Bogor masih sedikit: per 2026-11-16 ada 57 tangga, 3 kerb, 1 guiding block, dan 104 tempat bertag `wheelchair`.
- Negatif: full-text search tanpa stemmer melewatkan sebagian bentuk kata, mis. kata berawalan me- atau ber-, dan tidak memahami sinonim.
- Negatif: syarat Mapbox untuk AI belum terjawab dan bisa memaksa perubahan rancangan sebelum peluncuran.
- Negatif: tabel OSM di API ikut ODbL bila isinya dibagikan ke publik.
- Tindak lanjut: ADR fase 2 untuk penyedia model, batas pertanyaan, dan aturan jawaban; Kebijakan Privasi versi baru; pertanyaan ke Mapbox tentang §1.5 dan §1.10.

## Pro & Kontra tiap Opsi

### Agen rute jalan kaki dan informasi aksesibilitas

- Pro: menjawab pertanyaan pengguna dengan data yang sudah ada, tanpa melacak posisi terus-menerus.
- Kontra: rutenya tidak memperhitungkan kursi roda.

### Navigasi langsung

- Pro: paling lengkap.
- Kontra: butuh pelacakan posisi terus-menerus, rute ulang, dan data trotoar yang tidak ada untuk Kota Bogor; berisiko bagi keselamatan pengguna dan jauh di luar kemampuan tim.

### Tanpa agen

- Pro: tanpa biaya model dan tanpa risiko privasi baru.
- Kontra: tidak menjawab permintaan pengguna.

### Mapbox Directions profil `walking`

- Pro: akun dan token sudah ada, kuota gratis besar, dan langkahnya dalam bahasa Indonesia.
- Kontra: tidak mengenal kursi roda, hasilnya tidak boleh disimpan, dan ada syarat untuk AI.

### Perute sendiri dari OpenStreetMap

- Pro: profil kursi roda, mis. di Valhalla, bisa menghindari tangga yang tercatat, dan data ODbL tidak punya syarat AI.
- Kontra: butuh server dan memori tambahan di Evergarden, dan data trotoar dan kerb OSM di Kota Bogor terlalu sedikit sehingga profil kursi roda pun belum bisa dipercaya.

### Tanpa rute

- Pro: paling sederhana.
- Kontra: pertanyaan "bagaimana ke sana" tidak terjawab.

### Full-text search PostgreSQL

- Pro: tanpa layanan luar, berjalan di PostgreSQL produksi dan PGlite untuk tes, dan urutan hasilnya bisa dijelaskan.
- Kontra: tanpa stemmer bahasa Indonesia dan tanpa sinonim.

### Embedding dengan pgvector

- Pro: memahami makna dan sinonim.
- Kontra: teks dikirim ke penyedia embedding, ekstensi pgvector perlu dipasang di server, dan penyedianya belum dipilih.

### Seluruh teks di prompt

- Pro: tanpa retrieval.
- Kontra: ratusan ribu karakter di setiap pertanyaan, mahal, dan melebihi konteks banyak model gratis.

### Model gratis lewat OpenRouter

- Pro: tanpa biaya per token.
- Kontra: batas harian kecil, model bisa hilang, dan penyedia endpoint bisa memakai prompt.

### Model berbayar, mis. Claude

- Pro: tool calling andal, bahasa Indonesia baik, dan data API tidak dipakai melatih secara bawaan.
- Kontra: biaya per token, dan tim perlu metode pembayaran.

## Referensi

- [PRD](../product/prd.md), bagian 3 dan FR-60
- [Kebijakan Privasi](../product/kebijakan-privasi.md)
- [ADR-0010](0010-cakupan-wilayah-kota-bogor.md), [ADR-0011](0011-pelapor-wajib-login.md), [ADR-0016](0016-peta-dan-pencarian-mapbox.md), [ADR-0017](0017-empat-jawaban-ketersediaan-fasilitas.md), [ADR-0018](0018-status-pintu-masuk-dulu-dan-ringkasan-fasilitas.md), [ADR-0020](0020-antarmuka-bahasa-indonesia-siap-i18n.md), [ADR-0022](0022-tersedia-berarti-bisa-dipakai.md), [ADR-0029](0029-akun-better-auth-dan-sesi-lewat-server-web.md), [ADR-0032](0032-pencarian-tempat-lewat-api-mapbox-search-box.md), [ADR-0034](0034-batas-kecamatan-dari-openstreetmap.md), [ADR-0041](0041-halaman-edukasi-sebagai-draf-sampai-ditinjau-komunitas.md)
- Implementasi fase 1: al-dioooo/akses-bogor-api#49
- [Overpass API](https://wiki.openstreetmap.org/wiki/Overpass_API) dan [syarat server FOSSGIS](https://www.fossgis.de/arbeitsgruppen/osm-server/nutzungsbedingungen/)
- [Mapbox Product Terms](https://www.mapbox.com/legal/product-terms), [Directions API](https://docs.mapbox.com/api/navigation/directions/), [harga Mapbox](https://www.mapbox.com/pricing)
- [ODbL 1.0](https://opendatacommons.org/licenses/odbl/1-0/), [pedoman atribusi OSMF](https://osmfoundation.org/wiki/Licence/Attribution_Guidelines), [pedoman collective database](https://osmfoundation.org/wiki/Licence/Community_Guidelines/Collective_Database_Guideline_Guideline)
- [UU No. 28 Tahun 2014 tentang Hak Cipta](https://peraturan.go.id/files/uu28-2014bt.pdf), Pasal 42
- [UU No. 8 Tahun 2016](https://peraturan.go.id/files/uu8-2016bt.pdf) dan [Permen PUPR No. 14/PRT/M/2017 di JDIH Kementerian PU](https://jdih.pu.go.id/)
- [Batas OpenRouter](https://openrouter.ai/docs/api-reference/limits), [pencatatan oleh penyedia di OpenRouter](https://openrouter.ai/docs/guides/privacy/provider-logging), [penyimpanan data API Anthropic](https://privacy.claude.com/en/articles/7996866-how-long-do-you-store-my-organization-s-data)
