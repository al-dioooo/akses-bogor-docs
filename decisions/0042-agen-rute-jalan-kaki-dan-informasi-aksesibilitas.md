# ADR-0042: Aplikasi Menampilkan Rute Jalan Kaki ke Pintu Masuk, dan Agen dengan Model Gratis OpenRouter Menjawab soal Aksesibilitas Tujuan

- Status: Accepted
- Tanggal: 2026-11-22 (diusulkan 2026-11-16)
- Pengambil keputusan: Tim AksesBogor
- Issue: #73
- Melengkapi: [ADR-0016](0016-peta-dan-pencarian-mapbox.md), [ADR-0017](0017-empat-jawaban-ketersediaan-fasilitas.md), [ADR-0022](0022-tersedia-berarti-bisa-dipakai.md), [ADR-0032](0032-pencarian-tempat-lewat-api-mapbox-search-box.md), [ADR-0034](0034-batas-kecamatan-dari-openstreetmap.md)

## Konteks dan Masalah

Pengguna ingin bertanya kepada sebuah agen AI bagaimana cara sampai ke sebuah tempat dari posisinya sekarang, dan apa yang bisa diharapkan soal aksesibilitas di jalan dan di tujuan. PRD bagian 3 mencatat "Navigasi rute langkah demi langkah" sebagai non-tujuan. Product Terms Mapbox §1.5 (versi 20 Oktober 2026) melarang memakai layanan Mapbox "to train, operate or improve machine-learning or artificial intelligence (AI) technologies", jadi hasil Directions tidak bisa begitu saja diberikan ke model. Apa cakupan agen ini, dari mana rute dan pengetahuannya diambil, siapa yang menerima rute, model apa yang dipakai, dan bagaimana lisensi dan privasinya?

## Decision Drivers

- Pengguna kursi roda dan tunanetra perlu tahu sebelum berangkat apa yang menunggu di jalan dan di pintu masuk.
- Agen tidak boleh memberi rasa aman palsu. Rute yang disebut "aksesibel" padahal melewati tangga lebih berbahaya daripada tidak ada rute.
- Setiap klaim bisa ditelusuri ke sumbernya: jawaban terverifikasi AksesBogor, OpenStreetMap, atau pasal regulasi.
- Biaya mendekati nol: kuota gratis Mapbox, dan model gratis.
- Posisi pengguna adalah data pribadi (UU No. 27 Tahun 2022).
- Lisensi dan syarat setiap sumber data dipatuhi, termasuk Product Terms Mapbox §1.5.

## Opsi yang Dipertimbangkan

Cakupan:

1. Agen yang memberi rute jalan kaki ke pin tujuan dan informasi aksesibilitas, tanpa navigasi langsung
2. Navigasi langsung belokan demi belokan yang mengikuti posisi pengguna
3. Tanpa agen

Rute:

1. Mapbox Directions profil `walking` dari API
2. Perute sendiri dari data OpenStreetMap, mis. Valhalla dengan profil kursi roda
3. Tanpa rute, hanya informasi tujuan

Siapa yang menerima rute Mapbox:

1. Aplikasi menampilkan rute langsung kepada pengguna, dan model tidak pernah menerima hasil Directions
2. Model menerima hasil Directions setelah ada konfirmasi tertulis dari Mapbox
3. Perute lain yang hasilnya boleh dikirim ke model, lewat ADR baru

Retrieval:

1. Full-text search PostgreSQL dengan konfigurasi `simple`, ditambah `pg_trgm`
2. Embedding dari penyedia luar dengan pgvector
3. Seluruh teks dimasukkan ke prompt

Penyedia model:

1. Model gratis lewat OpenRouter
2. Model berbayar, mis. Claude lewat API Anthropic

## Keputusan

Opsi terpilih: "Agen yang memberi rute jalan kaki ke pin tujuan dan informasi aksesibilitas", dengan rute dari "Mapbox Directions profil `walking`" yang "ditampilkan aplikasi langsung kepada pengguna", retrieval "Full-text search PostgreSQL", dan "Model gratis lewat OpenRouter".

Dua keputusan pengguna menentukan bentuknya:

1. **Penyedia model: model gratis lewat OpenRouter** (dipilih 2026-11-16). Alasannya biaya nol per token dan tanpa metode pembayaran, cukup untuk proyek mahasiswa dengan pengguna sedikit. API OpenRouter mengikuti format chat completions OpenAI dengan tool calling, jadi adapternya kecil dan model bisa diganti lewat konfigurasi. Akibatnya (batas harian, model cadangan, endpoint yang mungkin mencatat prompt) ada di bagian [Penyedia model](#penyedia-model-model-gratis-lewat-openrouter). Daftar model, urutan cadangan, dan batas pertanyaan diputuskan di ADR fase 2.
2. **Aplikasi yang menampilkan rute** (dicatat 2026-11-22). Antarmuka menampilkan rute jalan kaki Mapbox dan hambatan OpenStreetMap di dekatnya langsung kepada pengguna: peta, langkah, kalimat peringatan tetap, dan atribusi. Model tidak pernah menerima hasil Directions, garis rute, langkah, hambatan OSM yang dicari dari garis rute, atau koordinat pengguna. Alasannya:
   - §1.5 Product Terms Mapbox melarang memakai layanan Mapbox untuk menjalankan teknologi AI. Rute yang hanya dilihat pengguna tidak menyentuh model, jadi tidak butuh konfirmasi dari Mapbox, yang belum tentu dijawab dan bisa lama.
   - Posisi pengguna tidak pernah keluar ke penyedia model, termasuk endpoint gratis yang mungkin mencatat prompt.
   - Rute, langkah, dan peringatan sampai ke pengguna persis seperti dari API, tanpa risiko model mengubah atau melunakkan peringatan.

### Cakupan

- Pengguna yang sudah masuk dan melengkapi akun memilih tujuan dari lokasi di database AksesBogor.
- Aplikasi menampilkan:
  - rute jalan kaki dari posisi pengguna ke pin tujuan: jarak, perkiraan waktu, langkah, dan garis di peta;
  - hambatan yang tercatat di OpenStreetMap di dekat rute, mis. tangga dan kerb tinggi;
  - kalimat peringatan tetap dan atribusi Mapbox dan OpenStreetMap.
- Agen menjawab pertanyaan pengguna tentang:
  - status tujuan dan jawaban keempat fasilitas yang sudah diverifikasi, dengan tanggal verifikasi terakhir;
  - regulasi, aturan AksesBogor, rute angkutan umum, dan tips dari halaman edukasi, dengan sumbernya.
- Yang bukan cakupan:
  - navigasi langsung yang mengikuti posisi, petunjuk suara, atau rute ulang otomatis;
  - tujuan di luar database AksesBogor atau di luar Kota Bogor ([ADR-0010](0010-cakupan-wilayah-kota-bogor.md));
  - menilai apakah sebuah rute aksesibel;
  - rute kendaraan;
  - menyimpan percakapan. Bila nanti dibutuhkan, keputusannya lewat ADR baru.
- PRD: non-tujuan "Navigasi rute langkah demi langkah" diganti "Navigasi langsung belokan demi belokan", dan FR-60 (Could) ditambahkan.

### Rute jalan kaki dan aksesibilitas

- Browser pengguna meminta rute ke API. API memanggil Mapbox Directions profil `walking` dari posisi pengguna ke pin lokasi. Pin adalah titik yang dikonfirmasi pelapor di pintu masuk utama ([Panduan Verifikasi](../operations/panduan-verifikasi.md)). Tokennya token rahasia di `api.env` yang sudah dipakai untuk pencarian tempat ([ADR-0032](0032-pencarian-tempat-lewat-api-mapbox-search-box.md)); Directions tidak butuh scope tambahan.
- Posisi pengguna dibulatkan ke 4 desimal (sekitar 11 m) sebelum dikirim. Posisi harus di dalam kotak Kota Bogor dan paling jauh 10 km garis lurus dari tujuan.
- Jawaban Mapbox tidak disimpan di database atau di log. Product Terms Mapbox (versi 20 Oktober 2026, §2.10.1) melarang menyimpan hasil Navigation API.
- Profil `walking` tidak mengenal kursi roda: tangga, trotoar, kerb, dan kemiringan tidak dihitung. Karena itu:
  - setiap hasil rute membawa kalimat peringatan tetap dari API, dan antarmuka selalu menampilkannya;
  - agen tidak pernah menyebut rute "aksesibel" atau "ramah kursi roda", walaupun ditanya;
  - aksesibilitas tujuan hanya dari jawaban terverifikasi. "Belum tahu" tetap disebut belum diketahui ([ADR-0017](0017-empat-jawaban-ketersediaan-fasilitas.md), [ADR-0022](0022-tersedia-berarti-bisa-dipakai.md)) dan tidak ditebak dari tag OpenStreetMap atau sumber lain.
- Hambatan di jalan diambil dari fitur OpenStreetMap dalam jarak 20 m (paling jauh 50 m) dari garis rute: tangga, kerb, ramp, lift, pintu masuk, dan guiding block. Antarmuka menyebutnya "tercatat di OpenStreetMap" dan menyampaikan bahwa daftar kosong bukan berarti jalannya bebas hambatan.
- Tag `wheelchair=*` di OpenStreetMap tidak mengubah status atau jawaban fasilitas AksesBogor.

### Sumber data dan lisensi

| Sumber | Isi | Lisensi dan kewajiban | Cara ambil |
| --- | --- | --- | --- |
| OpenStreetMap | Fitur bertag `wheelchair`, `toilets:wheelchair`, `ramp`, `kerb`, `tactile_paving`; tangga; lift; pintu masuk bertag akses; halte | ODbL 1.0. Atribusi "© kontributor OpenStreetMap" dengan tautan ke openstreetmap.org/copyright di setiap tampilan atau jawaban yang memakainya. Tabel OSM di API adalah database turunan: bila isinya dibagikan ke publik dalam jumlah besar, harus berlisensi ODbL | Satu kueri area Overpass untuk relasi Kota Bogor (14745927), tanpa `meta`, hanya tag dalam daftar izin, lalu dipotong dengan batas [ADR-0034](0034-batas-kecamatan-dari-openstreetmap.md) |
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

### Alat untuk model dan route untuk aplikasi

Ada dua jenis pemanggil, dan keduanya dipisah di API.

Alat untuk model:

| Alat | Isi |
| --- | --- |
| `find_place` | Lokasi AksesBogor bernama mirip, paling banyak 5 |
| `place_accessibility` | Status, jawaban fasilitas terverifikasi dengan alasan dan catatan, dan tanggal verifikasi terakhir, tanpa koordinat tempat |
| `search_knowledge` | Potongan regulasi, dokumen AksesBogor, dan rute angkutan dengan sitasi dan lisensi |

- Nama, deskripsi, skema input, dan skema output setiap alat ada di satu daftar di API. Larik `tools` format OpenAI (`type: "function"`, nama, deskripsi, dan JSON Schema parameter) dibuat dari skema itu.
- Server memeriksa argumen setiap panggilan alat dengan skema Zod yang sama sebelum menjalankannya.
- Tidak ada alat yang menerima koordinat pengguna atau mengembalikan rute.

Route untuk aplikasi, dipanggil dari browser pengguna sendiri dan tidak pernah oleh model:

| Route | Isi |
| --- | --- |
| Rute jalan kaki | Menerima posisi pengguna dan id lokasi; mengembalikan jarak, waktu, langkah, garis rute, kalimat peringatan tetap, dan atribusi |
| Hambatan di rute | Menerima garis rute dari route di atas; mengembalikan fitur OSM di dekatnya, urut dari awal rute, dengan tautan ke OSM dan atribusi |

- Aturan jawaban model, apa pun modelnya: bahasa Indonesia sederhana ([ADR-0020](0020-antarmuka-bahasa-indonesia-siap-i18n.md)); rute tidak pernah disebut aksesibel; "Belum tahu" tetap belum diketahui; sumber dan atribusi disebut; tujuan di luar database AksesBogor ditolak dengan sopan. Rinciannya di ADR fase 2.

### Privasi

- Model hanya menerima pertanyaan pengguna, giliran percakapan sebelumnya yang dikirim browser, dan tempat yang dipilih (id dan nama). Model tidak pernah menerima koordinat pengguna, rute, atau hambatan di rute.
- Posisi pengguna yang sudah dibulatkan hanya dikirim ke Mapbox Directions bersama pin tujuan, dari route rute jalan kaki.
- Pertanyaan pengguna tetap keluar ke OpenRouter dan penyedia model di belakangnya, dan endpoint gratis bisa mencatat prompt. Karena itu syarat sebelum peluncuran tetap berlaku: Kebijakan Privasi versi baru menyebut OpenRouter dan penyedia model (data yang dikirim, negara pemrosesan, lama penyimpanan, dan apakah dipakai untuk pelatihan) serta Mapbox Directions. Versi baru memicu persetujuan ulang lewat Lengkapi Akun, seperti versi sebelumnya. ADR ini tidak mengubah Kebijakan Privasi. Antarmuka juga meminta pengguna tidak menulis data pribadi di pertanyaan.
- Server tidak menyimpan posisi, pertanyaan, jawaban model, atau jawaban Mapbox, dan log tidak mencatat badan permintaan.
- Route agen dan route rute jalan kaki mati di production (`ROUTE_AGENT_ENABLED=false`) sampai syarat peluncuran terpenuhi.

### Biaya dan penyalahgunaan

- Hanya akun yang sudah masuk dan dilengkapi ([ADR-0011](0011-pelapor-wajib-login.md), [ADR-0029](0029-akun-better-auth-dan-sesi-lewat-server-web.md)).
- Batas per akun: 120 permintaan per jam untuk semua route agen dan rute bersama, dan 30 rute jalan kaki per jam.
- Batas untuk semua pengguna: 1.000 rute Mapbox per hari, jauh di bawah kuota gratis Directions 100.000 permintaan per bulan. Batas per IP 120 permintaan per menit tetap berlaku.
- Batas pertanyaan ke model per akun per hari dan batas harian untuk semua pengguna diputuskan di ADR fase 2, karena tergantung kuota OpenRouter.

### Penyedia model: model gratis lewat OpenRouter

Angka di bawah dicek 2026-11-22 dan bisa berubah; sumbernya ditautkan.

- **Batas permintaan.** Model gratis (id berakhiran `:free`) dibatasi 20 permintaan per menit, dan 50 permintaan per hari selama akun belum pernah membeli kredit minimal 10 USD. Setelah pernah membeli 10 USD kredit sekali, batasnya 1.000 permintaan per hari ([batas OpenRouter](https://openrouter.ai/docs/api-reference/limits), [FAQ](https://openrouter.ai/docs/faq)). Batas berlaku untuk seluruh akun: membuat API key tambahan tidak menambah batas. Satu pertanyaan bisa butuh 2 sampai 4 permintaan karena pemanggilan alat, jadi 50 permintaan cukup untuk belasan pertanyaan per hari untuk semua pengguna.
- **Keandalan.** Model gratis bisa dicabut, berganti, atau dibatasi penyedianya sewaktu-waktu (galat 429 "rate-limited upstream"). Mutu tool calling dan bahasa Indonesia berbeda-beda. Karena itu dipakai daftar model berurutan dengan cadangan, bukan satu model. OpenRouter bisa mencoba model berikutnya dalam satu permintaan lewat parameter `models` ([model fallbacks](https://openrouter.ai/docs/guides/routing/model-fallbacks)).
- **Tool calling.** Hanya model yang mendukung parameter `tools` yang dipakai ([tool calling](https://openrouter.ai/docs/features/tool-calling)). Parameter `provider.require_parameters` memastikan permintaan hanya diarahkan ke penyedia yang mendukung semua parameter yang dikirim ([provider routing](https://openrouter.ai/docs/features/provider-routing)).
- **Pemakaian data.** OpenRouter sendiri tidak menyimpan prompt dan jawaban kecuali pemilik akun mengaktifkannya, tetapi menyimpan metadata seperti jumlah token ([pengumpulan data OpenRouter](https://openrouter.ai/docs/guides/privacy/data-collection)). Penyedia di belakangnya punya kebijakan sendiri, dan endpoint gratis bisa mencatat prompt atau memakainya untuk pelatihan ([pencatatan oleh penyedia](https://openrouter.ai/docs/guides/privacy/provider-logging)). Parameter `provider.data_collection: "deny"` membatasi permintaan ke penyedia yang tidak mengumpulkan data pengguna.
- **Konfigurasi.** API key ada di `OPENROUTER_API_KEY` dan daftar model berurutan di `OPENROUTER_MODELS`, keduanya di `api.env`. Keduanya tidak wajib: tanpa API key, API tetap jalan dan agen menjawab sibuk.

### Syarat sebelum agen dibuka untuk pengguna

1. ADR fase 2 (daftar model, cadangan, batas pertanyaan, dan aturan jawaban) diterima.
2. Kebijakan Privasi versi baru berlaku dan pengguna sudah diminta persetujuan ulang.
3. Antarmuka menampilkan atribusi Mapbox dan OpenStreetMap bersama rute dan hambatan.

## Konsekuensi

- Positif: posisi pengguna, rute, dan hambatan di rute tidak pernah sampai ke model, jadi §1.5 Mapbox dipatuhi tanpa menunggu jawaban Mapbox, dan endpoint model gratis tidak pernah menerima koordinat.
- Positif: peringatan rute sampai ke pengguna persis seperti dari API.
- Positif: penyedia dan model bisa diganti lewat konfigurasi tanpa mengubah alat atau data.
- Positif: jawaban agen bisa ditelusuri ke pasal, dokumen, atau jawaban fasilitas tertentu.
- Positif: retrieval tidak butuh layanan luar dan berjalan di PostgreSQL yang sama.
- Negatif: agen tidak bisa menjelaskan rute atau hambatan di rute dengan kata-katanya sendiri; pengguna membaca langkah dan daftar hambatan di layar.
- Negatif: rute Mapbox tidak memperhitungkan kursi roda; aplikasi hanya bisa memperingatkan dan menampilkan hambatan yang tercatat. Data OSM untuk Kota Bogor masih sedikit: per 2026-11-16 ada 57 tangga, 3 kerb, 1 guiding block, dan 104 tempat bertag `wheelchair`.
- Negatif: model gratis punya batas harian kecil dan bisa hilang tanpa pemberitahuan; agen kadang menjawab sibuk.
- Negatif: full-text search tanpa stemmer melewatkan sebagian bentuk kata, mis. kata berawalan me- atau ber-, dan tidak memahami sinonim.
- Negatif: tabel OSM di API ikut ODbL bila isinya dibagikan ke publik.
- Tindak lanjut: ADR fase 2 untuk daftar model, cadangan, batas pertanyaan, dan aturan jawaban; Kebijakan Privasi versi baru.

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
- Kontra: tidak mengenal kursi roda, hasilnya tidak boleh disimpan, dan tidak boleh dipakai untuk menjalankan AI.

### Perute sendiri dari OpenStreetMap

- Pro: profil kursi roda, mis. di Valhalla, bisa menghindari tangga yang tercatat, dan data ODbL tidak punya syarat AI.
- Kontra: butuh server dan memori tambahan di Evergarden, dan data trotoar dan kerb OSM di Kota Bogor terlalu sedikit sehingga profil kursi roda pun belum bisa dipercaya.

### Tanpa rute

- Pro: paling sederhana.
- Kontra: pertanyaan "bagaimana ke sana" tidak terjawab.

### Aplikasi menampilkan rute, model tidak menerima hasil Directions

- Pro: mematuhi §1.5 tanpa menunggu Mapbox, posisi pengguna tidak sampai ke model, dan peringatan tidak bisa diubah model.
- Kontra: agen tidak bisa membahas rute atau hambatan di rute.

### Model menerima hasil Directions setelah konfirmasi Mapbox

- Pro: agen bisa menjelaskan rute dan hambatan dalam satu jawaban.
- Kontra: tergantung jawaban tertulis Mapbox yang belum tentu datang, dan koordinat atau garis rute sampai ke penyedia model.

### Perute lain yang hasilnya boleh dikirim ke model

- Pro: tanpa syarat AI.
- Kontra: sama dengan "Perute sendiri dari OpenStreetMap", dan garis rute tetap sampai ke penyedia model.

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

- Pro: tanpa biaya per token dan tanpa metode pembayaran; satu API untuk banyak model.
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
- OpenRouter: [batas](https://openrouter.ai/docs/api-reference/limits), [FAQ](https://openrouter.ai/docs/faq), [model fallbacks](https://openrouter.ai/docs/guides/routing/model-fallbacks), [provider routing](https://openrouter.ai/docs/features/provider-routing), [tool calling](https://openrouter.ai/docs/features/tool-calling), [pengumpulan data](https://openrouter.ai/docs/guides/privacy/data-collection), [pencatatan oleh penyedia](https://openrouter.ai/docs/guides/privacy/provider-logging)
- [Penyimpanan data API Anthropic](https://privacy.claude.com/en/articles/7996866-how-long-do-you-store-my-organization-s-data)
