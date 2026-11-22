# ADR-0047: Model Agen Kontributor: Claude Haiku 5.5 lewat Claude API

- Status: Accepted
- Tanggal: 2026-11-22
- Pengambil keputusan: Tim AksesBogor
- Issue: #89
- Men-supersede sebagian: [ADR-0043](0043-agen-untuk-kontributor-dengan-model-gratis-openrouter.md) (bagian "Model dan cadangan", baris permintaan OpenRouter di "Batas", dan "Jawaban sibuk") dan [ADR-0042](0042-agen-rute-jalan-kaki-dan-informasi-aksesibilitas.md) (penyedia model: "Model gratis lewat OpenRouter" dan bagian "Penyedia model"). Bagian lain kedua ADR itu tetap berlaku, termasuk alat, kartu, aturan jawaban, batas per akun, dan privasi rute.

## Konteks dan Masalah

Agen kontributor ("Bantu memetakan") memakai daftar model gratis OpenRouter ([ADR-0042](0042-agen-rute-jalan-kaki-dan-informasi-aksesibilitas.md), [ADR-0043](0043-agen-untuk-kontributor-dengan-model-gratis-openrouter.md)). Pada evaluasi 2026-11-22, setengah pertanyaan uji dijawab sibuk karena endpoint gratis dibatasi penyedianya, kuota hariannya 50 permintaan untuk seluruh akun, dan mutu tool calling berbeda antar model. Pada hari yang sama pengguna memutuskan memakai Claude ("Haiku maybe") dan menyerahkan API key Claude API. Model apa yang dipakai, lewat jalur apa, data apa yang dikirim ke Anthropic, berapa biayanya, dan bagaimana kembali ke OpenRouter?

## Decision Drivers

- Tool calling yang andal untuk tujuh alat dan aturan jawaban ADR-0043, dalam bahasa Indonesia sederhana.
- Tidak ada jawaban sibuk karena kuota bersama penyedia gratis.
- Biaya per pertanyaan jauh di bawah satu sen dolar AS, cocok untuk proyek mahasiswa.
- Desain privasi ADR-0042 tetap: alat berjalan di API AksesBogor, dan model tidak pernah menerima koordinat pengguna, rute, atau keluaran Mapbox ([Product Terms Mapbox §1.5](https://www.mapbox.com/legal/product-terms)).
- Penyedia bisa dikembalikan lewat konfigurasi, tanpa mengubah alat atau data.

## Opsi yang Dipertimbangkan

Model:

1. Claude Haiku 5.5 lewat Claude API
2. Claude Sonnet 5.5 lewat Claude API
3. Tetap model gratis OpenRouter

Jalur pemanggilan:

1. SDK resmi `@anthropic-ai/sdk` dengan putaran alat tetap di API AksesBogor
2. Claude Managed Agents
3. Endpoint kompatibel OpenAI, termasuk Claude lewat OpenRouter

## Keputusan

Opsi terpilih: "Claude Haiku 5.5 lewat Claude API" dengan "SDK resmi dan putaran alat tetap di API AksesBogor", karena tool calling-nya lolos semua pertanyaan uji tanpa jawaban sibuk, biayanya kurang dari US$0,001 per pertanyaan, dan alat tetap dijalankan di server kita sehingga model tidak pernah melihat posisi pengguna atau rute.

### Model dan parameter

- Model `claude-haiku-5-5`, bisa diganti lewat `ANTHROPIC_MODEL`.
- Effort `low` (`output_config.effort`), bisa diganti lewat `ANTHROPIC_EFFORT` ke `medium` atau `high`. Pada evaluasi, `low` memberi jawaban setara `medium` dengan token output sekitar 27% lebih sedikit (lihat Evaluasi).
- Thinking adaptif menyala sebagai bawaan Haiku 5.5. `max_tokens` 4.096, karena thinking ikut dihitung di dalamnya.
- Tanpa `temperature`, `top_p`, `top_k`, dan prefill jawaban; Haiku 5.5 menolak semuanya dengan galat 400.
- `tool_choice` `auto`, dan `none` di langkah terakhir (langkah ke-4), seperti ADR-0043.
- Isi setiap giliran asisten, termasuk blok thinking dan `tool_use`, dikirim kembali apa adanya bersama hasil alat. Riwayat dalam satu pertanyaan hanya bertambah di belakang, karena Haiku 5.5 menolak blok thinking bila awal percakapannya berubah.
- Semua hasil alat dari satu putaran dikirim dalam satu pesan; galat alat dan argumen yang ditolak skema dikirim dengan `is_error: true`.
- Bila Claude menolak (`stop_reason: "refusal"`), jawabannya terpotong batas token (`max_tokens`), atau tidak ada teks, agen membalas dengan kalimat tetap yang sama dengan jawaban setelah batas putaran: "Maaf, agen belum bisa menjawab pertanyaan ini. …". Haiku 5.5 tidak punya cadangan model dari server untuk penolakan, dan AksesBogor tidak menambahkannya.
- Prompt caching: penanda cache di alat terakhir, di instruksi sistem, dan otomatis di ujung percakapan. Awalan alat dan instruksi sistem sekitar 4.350 token, di atas minimum 512 token untuk Haiku 5.5. Pada evaluasi, baca cache naik di setiap langkah satu pertanyaan, mis. 4.254 → 4.366 → 4.712 token.
- SDK mengulang sekali (`maxRetries: 1`) untuk 429, 5xx, dan galat koneksi, dengan batas waktu 30 detik per permintaan. Tenggat 60 detik per pertanyaan dari ADR-0043 tetap berlaku lewat `AbortSignal`.

### Penyedia dan jawaban sibuk

- `AGENT_PROVIDER=anthropic|openrouter`, bawaan `anthropic`. Hanya satu penyedia yang dipakai. Tidak ada cadangan otomatis ke OpenRouter, supaya pertanyaan tidak pernah diam-diam pindah ke endpoint gratis. Adapter OpenRouter ADR-0043 tetap ada dan bisa dipilih.
- `ANTHROPIC_API_KEY` kosong berarti agen menjawab sibuk; API tetap jalan.
- Galat Claude API menjadi jawaban 503 `agen_sibuk` seperti di ADR-0043, dengan alasan yang dicatat di log:

| Galat | Alasan |
| --- | --- |
| 401, 403, 404 (API key salah, tanpa izin, atau model tidak tersedia) | `belum_diatur` |
| 402 `billing_error`, atau batas pengeluaran akun tercapai | `kredit` |
| 429 batas laju | `kuota` |
| 5xx termasuk 529 overloaded, koneksi gagal, batas waktu, dan 400 yang tidak terduga | `semua_gagal` |
| Tenggat 60 detik habis | `tenggat` |

Batas pengeluaran yang diatur sendiri di Claude Console dijawab Claude API dengan 400 tanpa kode khusus ([galat Claude API](https://platform.claude.com/docs/en/api/errors)), jadi agen menjawabnya sibuk dengan alasan `semua_gagal`, dan status 400 serta id permintaannya tercatat di log.

### Batas

- 5 pertanyaan per akun per hari WIB, batas putaran dan panggilan alat, panjang pertanyaan, dan giliran sebelumnya tetap seperti ADR-0043.
- Batas permintaan yang berhasil untuk semua pengguna per hari UTC sekarang diatur lewat `AGENT_REQUESTS_PER_DAY`, bawaannya tetap 45. Pada evaluasi satu pertanyaan memakai rata-rata 2,1 permintaan, jadi 45 cukup untuk sekitar 20 pertanyaan per hari. Menaikkannya hanya mengubah biaya (lihat Biaya).
- Batas dan saldo akun di Claude Console menjadi pengaman terakhir. Tim bisa memasang batas pengeluaran bulanan di sana.

### Data yang dikirim ke Anthropic

Dikirim, untuk setiap langkah model:

- instruksi sistem dan definisi tujuh alat (sama untuk semua pengguna);
- pertanyaan pengguna dan paling banyak 6 giliran sebelumnya yang dikirim browser;
- bila ditanya dari halaman sebuah lokasi: nama lokasi, status, jawaban keempat fasilitas, alasan, catatan, dan tanggal verifikasi terakhir, tanpa koordinat dan foto;
- hasil alat: potongan basis pengetahuan dengan sitasi dan lisensinya, lokasi AksesBogor yang cocok (id, nama, kategori, status, kecamatan), tempat OpenStreetMap tanpa data (id OSM, nama, kategori, kecamatan, peringkat prioritas), prioritas kecamatan, dan konfirmasi singkat dari alat kartu.

Tidak pernah dikirim: koordinat atau posisi pengguna, garis atau langkah rute jalan kaki, hambatan di rute, keluaran Mapbox (Directions maupun Search Box), isi kartu, email, nama tampilan, id akun, dan alamat IP pengguna.

Server AksesBogor tetap tidak menyimpan pertanyaan atau jawaban. Log hanya mencatat model, alasan berhenti, jumlah token (input, output, baca dan tulis cache), dan untuk galat: status, jenis galat, kode, dan id permintaan Claude API. Isi pertanyaan, jawaban, dan API key tidak pernah dicatat.

### Penanganan data oleh Anthropic

Hanya yang tertulis di halaman Anthropic sendiri, dibaca 2026-11-22:

- [Commercial Terms of Service](https://www.anthropic.com/legal/commercial-terms) (berlaku 17 Juni 2025): "Anthropic may not train models on Customer Content from Services." Data yang dikirim lewat layanan diproses sesuai Data Processing Addendum Anthropic. Pelanggan memiliki output-nya. Halaman ini tidak menyebut lama penyimpanan.
- [Privacy Policy](https://www.anthropic.com/legal/privacy) (berlaku 10 September 2026): kebijakan ini "does not apply to content that we process on behalf of customers of our business offerings"; pemakaian data itu diatur perjanjian pelanggan.
- [API and data retention](https://platform.claude.com/docs/en/manage-claude/api-and-data-retention): untuk Claude API, Anthropic adalah pemroses data, dan "Retained data is never used for model training without your express permission." Claude Haiku 5.5 tidak termasuk "Covered Models" yang wajib disimpan 30 hari. Data bisa disimpan bila diwajibkan hukum atau ditandai sistem trust and safety otomatis Anthropic, sampai 2 tahun.
- [How long do you store my organization's data?](https://privacy.claude.com/en/articles/7996866-how-long-do-you-store-my-organization-s-data) (diperbarui 1 Juli 2026): input dan output dihapus otomatis dari backend paling lambat 30 hari setelah diterima atau dibuat, kecuali bila ditandai melanggar Kebijakan Penggunaan (sampai 2 tahun) atau diwajibkan hukum.
- [Data residency](https://platform.claude.com/docs/en/manage-claude/data-residency): inferensi bawaan (`inference_geo: "global"`) "may run in any available geography", dan lokasi penyimpanan (workspace geo) saat ini hanya `us`. AksesBogor memakai bawaan `global`.

Kebijakan Privasi AksesBogor versi 2026-11-22 menyebut Anthropic sebagai penyedia model agen dan data di atas. Syarat peluncuran ADR-0042 tetap berlaku: `ROUTE_AGENT_ENABLED` baru dinyalakan setelah versi itu berlaku dan pengguna diminta persetujuan ulang.

### Biaya

Harga Claude Haiku 5.5 untuk prompt sampai 100.000 token ([harga Claude API](https://platform.claude.com/docs/en/about-claude/pricing), dicek 2026-11-22): US$0,10 per juta token input, US$0,50 output, US$0,125 tulis cache 5 menit, dan US$0,01 baca cache. Prompt agen sekitar 4.000 sampai 6.000 token, jauh di bawah ambang harga yang lebih tinggi.

Dari empat kali evaluasi (67 permintaan):

- rata-rata sekitar US$0,00025 per permintaan, dan US$0,0002 sampai US$0,0011 per pertanyaan; yang termahal adalah pertanyaan pertama setelah cache kosong, yang menulis awalan alat dan instruksi sistem;
- dengan bawaan 45 permintaan per hari, paling banyak sekitar US$0,35 per bulan; bila dinaikkan ke 500 per hari, sekitar US$4 per bulan;
- keempat evaluasi menghabiskan sekitar US$0,017.

### Kembali ke OpenRouter atau ganti model

- Ke OpenRouter: isi `AGENT_PROVIDER=openrouter` dan `OPENROUTER_API_KEY` di `api.env`, lalu restart API. Tidak perlu deploy kode. Sebelum agen dibuka lagi untuk pengguna, Kebijakan Privasi harus kembali menyebut OpenRouter dan penyedia modelnya, dengan versi baru dan persetujuan ulang.
- Model Claude lain: isi `ANTHROPIC_MODEL`, lalu jalankan `pnpm agen:evaluasi` sebelum dipakai, karena aturan parameter dan effort bisa berbeda per model.

### Evaluasi

`pnpm agen:evaluasi` menjalankan 8 pertanyaan uji ADR-0043 ke Claude API sungguhan, dua kali untuk setiap effort, dengan database pengembangan. Semua 67 permintaan berhasil; tidak ada jawaban sibuk.

| Jalan | Effort | Lolos pemeriksaan otomatis | Permintaan | Token output | Baca cache | Tulis cache | Perkiraan biaya |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | `low` | 8 dari 8 | 17 | 3.821 | 67.314 | 19.240 | US$0,0050 |
| 2 | `medium` | 7 dari 8 | 17 | 4.907 | 76.148 | 10.608 | US$0,0046 |
| 3 | `low` | 7 dari 8 | 16 | 3.649 | 80.211 | 1.817 | US$0,0029 |
| 4 | `medium` | 7 dari 8 | 17 | 4.591 | 77.895 | 8.951 | US$0,0042 |

- Setiap jalan memakai sekitar 50 token input di luar cache. Jalan pertama mulai dengan cache kosong; jalan berikutnya masih menemukan isi cache dari jalan sebelumnya (masa simpan 5 menit), jadi tulis cache dan biayanya turun. Perbandingan yang adil antar effort adalah token output.
- Semua "perlu dicek" ada di K02. Dibaca manusia, keempat jawaban K02 benar: dibuka dengan "Maaf, data itu belum tersedia di AksesBogor." dan menolak menyebut rute aksesibel. Pemeriksaan otomatis salah menandai kalimat penolakan seperti "Saya tidak bisa menyebutkan rute yang aksesibel", karena kata ingkarnya ada sebelum kata "rute".
- Kesalahan kecil yang terlihat saat dibaca: dua kali agen menyebut kartu yang tidak tampil ("Anda bisa melihat kartu tiap tempat di aplikasi" di jalan 1, "Anda bisa membuka kartu rute" di jalan 2). Pemeriksa server yang memotong klaim rute aksesibel tetap bekerja di K05.
- K04 (regulasi) memanggil `search_knowledge` di keempat jalan dan mengutip Permen PUPR No. 14/PRT/M/2017, Lampiran II, B.2 Ram dengan angka yang benar.

## Konsekuensi

- Positif: tidak ada jawaban sibuk karena kuota penyedia gratis; 67 dari 67 permintaan evaluasi berhasil.
- Positif: biaya kurang dari satu dolar per bulan pada batas bawaan, dan batas harian bisa dinaikkan tanpa deploy kode.
- Positif: alat, kartu, aturan jawaban, dan privasi rute tidak berubah; model tetap tidak menerima posisi atau rute.
- Negatif: agen sekarang memakai saldo berbayar di akun Claude API; saldo habis berarti agen menjawab sibuk sampai diisi.
- Negatif: pertanyaan pengguna dan data terverifikasi tempat dikirim ke Anthropic, yang menyimpan data di Amerika Serikat, sehingga Kebijakan Privasi berubah dan semua pengguna diminta persetujuan ulang.
- Negatif: satu penyedia tanpa cadangan otomatis. Bila Claude API gangguan, agen menjawab sibuk sampai pulih atau penyedianya diganti lewat konfigurasi.
- Negatif: penolakan oleh pengaman Claude bisa terjadi tanpa cadangan model; pengguna mendapat kalimat tetap.
- Tindak lanjut: isi variabel environment production dan saldo Claude API; pertimbangkan batas pengeluaran bulanan di Claude Console; rilis Kebijakan Privasi versi 2026-11-22 di API dan web bersamaan; perbaiki pola K02 di `scripts/agen/pertanyaan-uji.json` supaya kalimat penolakan tidak ditandai.

## Pro & Kontra tiap Opsi

### Claude Haiku 5.5 lewat Claude API

- Pro: lolos semua pertanyaan uji dengan biaya kecil, dan kuotanya milik akun sendiri, tidak dibagi dengan pengguna lain seperti endpoint gratis.
- Pro: thinking adaptif dengan effort yang bisa diatur, dan prompt caching memotong biaya awalan yang sama di setiap langkah.
- Kontra: berbayar, dan data dikirim ke Anthropic.

### Claude Sonnet 5.5 lewat Claude API

- Pro: lebih kuat untuk pekerjaan yang panjang.
- Kontra: harga per token 20 kali Haiku 5.5, padahal Haiku 5.5 sudah lolos semua pertanyaan uji.

### Tetap model gratis OpenRouter

- Pro: tanpa biaya per token.
- Kontra: setengah pertanyaan dijawab sibuk, kuota 50 permintaan per hari untuk seluruh akun, dan model gratis bisa hilang tanpa pemberitahuan (ADR-0043).

### Claude Managed Agents

- Pro: Anthropic menjalankan putaran agen.
- Kontra: riwayat sesi disimpan Anthropic sampai dihapus ([API and data retention](https://platform.claude.com/docs/en/manage-claude/api-and-data-retention)), padahal ADR-0043 memutuskan tidak ada percakapan yang disimpan. Putaran alat, batas putaran, dan pemeriksaan jawaban di `ask.ts` harus ditulis ulang di atas event sesi, dan ada biaya waktu sesi.

### Endpoint kompatibel OpenAI, termasuk Claude lewat OpenRouter

- Pro: adapter OpenRouter yang ada bisa dipakai ulang.
- Kontra: menambah perantara yang juga menerima pertanyaan pengguna dan punya kebijakan datanya sendiri.

## Referensi

- [ADR-0042](0042-agen-rute-jalan-kaki-dan-informasi-aksesibilitas.md), [ADR-0043](0043-agen-untuk-kontributor-dengan-model-gratis-openrouter.md)
- [Kebijakan Privasi](../product/kebijakan-privasi.md) versi 2026-11-22
- Kode: al-dioooo/akses-bogor-api#86 (adapter `src/agent/anthropic.ts`) dan al-dioooo/akses-bogor-api#85 (`src/agent/provider.ts`, variabel env, evaluasi); versi Kebijakan Privasi di al-dioooo/akses-bogor-api#87 dan al-dioooo/akses-bogor-web#128
- Anthropic: [Commercial Terms of Service](https://www.anthropic.com/legal/commercial-terms), [Privacy Policy](https://www.anthropic.com/legal/privacy), [API and data retention](https://platform.claude.com/docs/en/manage-claude/api-and-data-retention), [lama penyimpanan data organisasi](https://privacy.claude.com/en/articles/7996866-how-long-do-you-store-my-organization-s-data), [data residency](https://platform.claude.com/docs/en/manage-claude/data-residency), [harga](https://platform.claude.com/docs/en/about-claude/pricing), [galat](https://platform.claude.com/docs/en/api/errors)
