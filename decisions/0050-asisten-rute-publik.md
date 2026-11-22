# ADR-0050: Asisten Rute Publik untuk Semua Pengunjung, dengan Kartu Rute dari Aplikasi dan Jatah per IP

- Status: Accepted
- Tanggal: 2026-11-23
- Pengambil keputusan: pemilik produk
- Issue: #105

## Konteks dan Masalah

[ADR-0042](0042-agen-rute-jalan-kaki-dan-informasi-aksesibilitas.md) membuat rute jalan kaki dan agen hanya untuk akun yang sudah masuk. [ADR-0043](0043-agen-untuk-kontributor-dengan-model-gratis-openrouter.md) lalu mengarahkan agen ke kontributor ("Bantu memetakan"), dan [ADR-0047](0047-model-agen-kontributor-claude-haiku-lewat-claude-api.md) memindahkan modelnya ke Claude Haiku 5.5 lewat Claude API. Pemilik produk sekarang ingin siapa pun, termasuk pengunjung tanpa akun, bisa bertanya cara ke sebuah tempat dan mendapat jawaban AI beserta kartu rute. Bagaimana membuka asisten itu tanpa melanggar syarat Mapbox, tanpa menjanjikan rute yang aksesibel, dan dengan biaya yang terkendali?

## Decision Drivers

- Pengunjung tanpa akun juga perlu tahu cara ke sebuah tempat dan apa yang sudah diketahui tentang aksesibilitasnya.
- Product Terms Mapbox §1.5 tetap dipatuhi: hasil Mapbox tidak masuk ke model ([ADR-0042](0042-agen-rute-jalan-kaki-dan-informasi-aksesibilitas.md)).
- Rute jalan kaki Mapbox tidak tahu apa-apa soal tangga, kerb, atau kemiringan. Menyebut rute "ramah kursi roda" bisa membahayakan orang.
- Banyak tempat belum diperiksa tim. Jawaban tidak boleh menebak aksesibilitasnya.
- Biaya Claude API dan Mapbox Directions harus terbatas walaupun siapa pun bisa bertanya.
- Pengunjung tanpa akun tidak melewati Lengkapi Akun, jadi pemberitahuan privasi harus tampil di halaman sebelum pertanyaan pertama.

## Opsi yang Dipertimbangkan

1. Asisten rute publik dengan instruksi dan alatnya sendiri, memakai adapter Claude, putaran alat, dan kartu rute Bantu memetakan
2. Membuka agen Bantu memetakan untuk semua orang
3. Tetap hanya untuk akun yang sudah masuk

## Keputusan

Opsi terpilih: "Asisten rute publik dengan instruksi dan alatnya sendiri", karena pengunjung mendapat jawaban dan kartu rute tanpa akun, sedangkan agen kontributor, alat OpenStreetMap-nya, dan data prioritas kecamatan tetap hanya untuk akun.

Keputusan ini menggantikan aturan "hanya akun yang sudah masuk dan dilengkapi" di [ADR-0042](0042-agen-rute-jalan-kaki-dan-informasi-aksesibilitas.md) (bagian batas dan keamanan) dan di [ADR-0043](0043-agen-untuk-kontributor-dengan-model-gratis-openrouter.md) (halaman agen), khusus untuk asisten ini dan rute jalan kaki ke lokasi AksesBogor. Bantu memetakan tetap hanya untuk akun yang sudah masuk.

### Siapa yang memakai

- Semua pengunjung, masuk atau tidak, di halaman web `/tanya-rute` ("Tanya rute").
- Akun yang sudah masuk dan melengkapi akun memakai jatah per akun. Pengunjung tanpa akun, atau dengan akun yang belum dilengkapi, memakai jatah per IP.
- Tujuan hanya lokasi di database AksesBogor. Tempat OpenStreetMap tanpa data AksesBogor tetap hanya untuk Bantu memetakan.

### API

- `POST /route-assistant/ask`, dengan atau tanpa sesi. Badannya:
  - `question`, dan `history` (paling banyak 6 giliran) yang disimpan browser selama halaman terbuka;
  - `placeId` opsional, bila pengunjung datang dari "Tanya rute ke sini";
  - `from` opsional, posisi pengunjung dari browser atau titik yang dipilih di peta.
- Jawabannya sama bentuknya dengan `POST /agent/ask`: jawaban, sumber, model, dan kartu.
- `GET /route-assistant/status` memberi tahu web apakah asisten dibuka dan berapa jatah hariannya, tanpa sesi.
- `ROUTE_ASSISTANT_ENABLED` mati secara bawaan, terpisah dari `ROUTE_AGENT_ENABLED`. Selama mati, kedua route asisten menjawab 503 `asisten_rute_belum_aktif`.
- `POST /route/walking` dan `POST /route/obstacles` bisa dipakai tanpa sesi selama asisten dibuka, hanya untuk lokasi AksesBogor. Tujuan OpenStreetMap tetap butuh akun dan `ROUTE_AGENT_ENABLED`.
- Model, effort, dan penyedianya sama dengan agen kontributor ([ADR-0047](0047-model-agen-kontributor-claude-haiku-lewat-claude-api.md)).

### Alat model

Semua alat dijalankan di server dan argumennya diperiksa skema Zod, seperti di [ADR-0043](0043-agen-untuk-kontributor-dengan-model-gratis-openrouter.md).

| Alat | Yang dikembalikan ke model |
| --- | --- |
| `find_place` | Lokasi AksesBogor menurut nama, kategori, atau kecamatan, paling banyak 5: id, nama, kategori, kecamatan, status. Tanpa koordinat |
| `place_accessibility` | Status, jawaban keempat fasilitas, tanggal verifikasi, apakah tim sudah memeriksanya, dan sumber datanya |
| `search_knowledge` | Potongan regulasi, aturan AksesBogor, tips, dan rute angkutan, dengan sitasi ([ADR-0044](0044-retrieval-dengan-stemmer-dan-padanan-kata.md)) |
| `nearby_places` | Lokasi AksesBogor terdekat dari posisi pengunjung, paling banyak 5: id, nama, kategori, kecamatan, status, dan jarak garis lurus dari PostGIS yang dibulatkan ke 100 m. Tanpa koordinat |
| `show_route` | Kartu rute jalan kaki ke lokasi AksesBogor. Model hanya menerima konfirmasi singkat |

- Posisi pengunjung hanya dipakai server untuk `nearby_places` dan rute. Model hanya tahu apakah posisi ada, tidak pernah nilainya. Posisi dibulatkan ke 4 desimal (sekitar 11 m), harus di dalam Kota Bogor, tidak disimpan, dan tidak dicatat di log.
- Jarak garis lurus berasal dari database AksesBogor, bukan dari Mapbox, jadi boleh dikirim ke model. Jarak dibatasi 10 km, sama dengan rute jalan kaki.
- Model tidak pernah menerima hasil Mapbox (Directions, garis rute, langkah, hasil geocoding), koordinat pengguna, atau hambatan yang dicari dari garis rute. Web menggambar rute dan hambatannya dengan kartu rute yang sama dengan Bantu memetakan.

### Aturan kejujuran

Di instruksi sistem, dan dijaga juga di kode:

- Rute tidak pernah disebut aksesibel, ramah kursi roda, aman, atau bebas hambatan. Pemeriksaan `guardRouteClaims` yang sudah ada membuang kalimat seperti itu dan menambahkan kalimat penjelas.
- Aksesibilitas tempat hanya dijelaskan dari jawaban fasilitas yang diperiksa tim. Untuk tempat yang belum diperiksa tim, termasuk tempat hasil impor, jawaban menyebut "belum diperiksa tim" dan menyarankan menelepon dulu atau bertanya kepada orang yang pernah ke sana. Pemeriksaan baru di kode membuang kalimat yang menyebut sebuah tempat aksesibel atau ramah kursi roda bila pertanyaan itu tidak membaca data terperiksa tim, lalu menambahkan kalimat penjelas.
- Tanpa saran medis. Bahasa Indonesia, istilah dari [ADR-0041](0041-halaman-edukasi-sebagai-draf-sampai-ditinjau-komunitas.md), dan tidak pernah kata "cacat". Kode mengganti kata itu dengan "disabilitas" bila model tetap menuliskannya.

### Batas

| Batas | Nilai | Bila habis |
| --- | --- | --- |
| Pengunjung tanpa akun | 5 pertanyaan per IP per hari WIB | 429 `kuota_habis`, "Jatah 5 pertanyaan hari ini sudah habis. Coba lagi besok, atau masuk untuk memakai jatah akun Anda." |
| Akun yang sudah masuk | 5 pertanyaan per akun per hari WIB, satu hitungan dengan Bantu memetakan | 429 `kuota_habis` |
| Semua pengunjung | `ROUTE_ASSISTANT_QUESTIONS_PER_DAY`, bawaan 300 pertanyaan per hari WIB | 503 `agen_sibuk`, "Asisten sedang sibuk. Coba lagi nanti." |
| Permintaan per jam | 120 untuk semua route agen dan rute, dan 30 rute jalan kaki, per akun atau per IP | 429 `agen_terlalu_sering` |
| Rute jalan kaki | 1.000 per hari untuk semua pengguna ([ADR-0042](0042-agen-rute-jalan-kaki-dan-informasi-aksesibilitas.md)) | 503 `rute_penuh` |

- IP pengunjung dibaca dengan kunci rate limit yang sudah ada: `x-visitor-ip` dari server web dengan `WEB_SERVER_SECRET`, lalu `cf-connecting-ip` dari Cloudflare ([ADR-0048](0048-web-di-host-sendiri-di-evergarden-oci.md)).
- Hitungan disimpan di memori proses, seperti batas lain, jadi kembali ke nol saat API di-restart. IP tidak disimpan ke database atau log.
- Satu pertanyaan memakai paling banyak 4 permintaan ke model, jadi 300 pertanyaan paling banyak 1.200 permintaan per hari. Asisten publik memakai model dengan hitungan sendiri, jadi tidak menghabiskan `AGENT_REQUESTS_PER_DAY` milik Bantu memetakan.
- Pertanyaan yang dijawab sibuk tidak memakai jatah pengunjung.

### Web

- Halaman publik `/tanya-rute`, ditautkan dari navigasi utama, dan aksi "Tanya rute ke sini" di Detail lokasi dan kartu tempat di Peta yang langsung memilih tempat itu.
- Obrolan memakai komponen Bantu memetakan: live region, pengelolaan fokus, keyboard, dan pembaca layar ([ADR-0021](0021-wcag-22-aa-dan-pengujian-berlapis.md)).
- Titik awal: izin lokasi hanya diminta setelah penjelasan. Bila ditolak, pengunjung memilih titik di peta. Titik yang sama dipakai untuk pertanyaan berikutnya dan kartu rute.
- Sebelum pertanyaan pertama tampil pemberitahuan singkat: pertanyaan dikirim ke Anthropic (Claude API), lokasi hanya ke server AksesBogor dan Mapbox, dengan tautan ke Kebijakan Privasi.

### Kebijakan Privasi

Versi baru menyebut asisten ini, termasuk pengunjung tanpa akun, data yang dikirim ke Anthropic, posisi yang hanya dipakai server AksesBogor dan Mapbox, dan IP untuk jatah harian. Urutan rilisnya sama dengan versi sebelumnya: web dulu, lalu API.

### Evaluasi

Pertanyaan rute ditambahkan ke evaluasi agen, mis. rute ke rumah sakit dari posisi pengunjung, masjid terdekat yang aksesibel, apakah sebuah halte ramah kursi roda, dan satu pertanyaan di luar topik. Lulus berarti kartu rute muncul bila diminta, dan jawaban tidak pernah menyebut rute aksesibel.

## Konsekuensi

- Positif: pengunjung tanpa akun bisa bertanya rute dan mendapat kartu rute dengan peringatan dan hambatan yang sama dengan Bantu memetakan.
- Positif: model tidak pernah melihat posisi atau rute, jadi syarat Mapbox dan privasi posisi tetap terjaga.
- Positif: dua flag terpisah, jadi asisten publik bisa dimatikan tanpa mematikan Bantu memetakan.
- Negatif: jatah per IP dibagi oleh semua orang di balik satu IP, mis. Wi-Fi kampus atau jaringan seluler. Mereka bisa masuk untuk memakai jatah akun.
- Negatif: siapa pun bisa memakai kuota harian. Batas 300 pertanyaan membatasi biaya, tetapi juga bisa membuat asisten sibuk bagi orang lain.
- Negatif: selama database belum punya banyak lokasi terperiksa, asisten sering menjawab bahwa tempat belum tercatat atau belum diperiksa tim.
- Negatif: jarak yang dibulatkan ke beberapa tempat tetap memberi petunjuk kasar tentang posisi pengunjung kepada model. Pembulatan 100 m dan paling banyak 5 tempat membuat petunjuk itu kasar.

## Pro & Kontra tiap Opsi

### Asisten rute publik dengan instruksi dan alatnya sendiri

- Pro: alat dan instruksinya pas untuk orang yang bertanya rute, tanpa alat kontributor dan data disabilitas.
- Pro: memakai ulang adapter Claude, putaran alat, pemeriksaan jawaban, dan kartu rute yang sudah teruji.
- Kontra: dua instruksi sistem dan dua daftar alat yang harus dirawat.

### Membuka agen Bantu memetakan untuk semua orang

- Pro: tidak ada kode baru di API.
- Kontra: agen itu menolak pertanyaan rute dari penyandang disabilitas ([ADR-0043](0043-agen-untuk-kontributor-dengan-model-gratis-openrouter.md)), jadi tidak menjawab kebutuhan ini.
- Kontra: alat OpenStreetMap dan prioritas kecamatan terbuka untuk semua orang.

### Tetap hanya untuk akun yang sudah masuk

- Pro: penyalahgunaan dan biaya lebih mudah dibatasi.
- Kontra: pengunjung harus membuat akun hanya untuk bertanya rute.

## Referensi

- Issue #105
- [ADR-0021](0021-wcag-22-aa-dan-pengujian-berlapis.md), [ADR-0041](0041-halaman-edukasi-sebagai-draf-sampai-ditinjau-komunitas.md), [ADR-0042](0042-agen-rute-jalan-kaki-dan-informasi-aksesibilitas.md), [ADR-0043](0043-agen-untuk-kontributor-dengan-model-gratis-openrouter.md), [ADR-0044](0044-retrieval-dengan-stemmer-dan-padanan-kata.md), [ADR-0047](0047-model-agen-kontributor-claude-haiku-lewat-claude-api.md), [ADR-0048](0048-web-di-host-sendiri-di-evergarden-oci.md)
- [Product Terms Mapbox](https://www.mapbox.com/legal/product-terms)
