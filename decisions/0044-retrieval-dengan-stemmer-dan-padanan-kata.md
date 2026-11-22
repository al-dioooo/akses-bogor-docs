# ADR-0044: Retrieval Basis Pengetahuan Memakai Stemmer Bahasa Indonesia, Padanan Kata, dan Peringkat yang Diperbaiki

- Status: Proposed
- Tanggal: 2026-11-22
- Pengambil keputusan: Tim AksesBogor
- Issue: #79
- Melengkapi: [ADR-0042](0042-agen-rute-jalan-kaki-dan-informasi-aksesibilitas.md). Mengubah cara pencocokan dan pengurutan di bagian "Retrieval tanpa penyedia embedding"; sumber data, potongan teks, batas 8 potongan, sitasi, dan pilihan tanpa penyedia embedding tetap berlaku. Aturan 8 [ADR-0043](0043-agen-untuk-kontributor-dengan-model-gratis-openrouter.md) (regulasi hanya dari hasil `search_knowledge`) tidak berubah.

## Konteks dan Masalah

[ADR-0042](0042-agen-rute-jalan-kaki-dan-informasi-aksesibilitas.md) memilih full-text search PostgreSQL berkonfigurasi `simple` untuk `search_knowledge`, dan mencatat kelemahannya: tanpa stemmer bahasa Indonesia dan tanpa sinonim. Karena agen hanya boleh menjawab regulasi dari hasil `search_knowledge`, potongan yang tidak ditemukan berarti jawaban "belum ada di basis pengetahuan" padahal teksnya ada.

Pada 2026-11-22 retrieval diukur tanpa model dengan 30 pertanyaan uji, masing-masing dengan sitasi yang harus ditemukan (al-dioooo/akses-bogor-api#71). Hasil awalnya recall@8 73%: delapan pertanyaan tidak menemukan targetnya sama sekali. Penyebabnya tiga:

- Imbuhan: "miring" tidak menemukan "kemiringan", "landai" tidak menemukan "kelandaian".
- Padanan kata: Permen PUPR menulis "ram", "lif", dan "paling besar", sedangkan orang bertanya "ramp", "lift", dan "maksimal"; UU No. 8 Tahun 2016 menulis "transportasi publik", orang bertanya "angkutan umum".
- Rumus peringkat: `ts_rank_cd` tanpa normalisasi bernilai sampai sekitar 14 di potongan panjang, jadi pasal panjang yang mengulang satu kata mengalahkan pasal pendek yang cocok dengan lebih banyak kata. Urutan "jumlah kata yang cocok dulu" yang ditulis ADR-0042 tidak terjadi.

Bagaimana retrieval diperbaiki tanpa layanan luar?

## Decision Drivers

- Tanpa layanan luar dan tanpa biaya: tetap di PostgreSQL yang sama ([ADR-0025](0025-api-dan-postgresql-di-vps-sendiri.md)).
- Tidak menambah berkas ke direktori sistem PostgreSQL di VPS, supaya rilis tetap lewat migrasi dan skrip biasa.
- Hasilnya deterministik dan bisa diuji; daftar kata bisa ditinjau tim di pull request.
- Lisensi setiap kamus atau daftar kata yang dibawa di repo harus permisif. AksesBogor tidak komersial, tetapi syarat non-komersial dan share-alike pada daftar kata membatasi cara daftar itu dipakai ulang dan dibagikan bersama kode.
- Kata yang persis sama dan sitasi tetap menang atas kecocokan bentuk dasar.
- Setiap perubahan diukur, dan yang tidak membantu tidak dipakai.

## Opsi yang Dipertimbangkan

1. Tetap seperti ADR-0042
2. Stemmer di API (TypeScript) dengan kolom `tsvector` bentuk dasar, daftar padanan kata yang ditinjau, dan peringkat yang diperbaiki
3. Kamus PostgreSQL: `ispell` dengan kamus Hunspell bahasa Indonesia untuk bentuk dasar, `synonym` atau `dict_xsyn` untuk padanan
4. Paket npm port Sastrawi (`sastrawijs`) sebagai stemmer
5. Embedding dengan pgvector

## Keputusan

Opsi terpilih: "Stemmer di API dengan kolom bentuk dasar, daftar padanan kata, dan peringkat yang diperbaiki", karena menaikkan recall@3 dari 63% ke 93% dan MRR dari 0,561 ke 0,870 tanpa layanan luar, dan semua aturannya ada di repo API.

### Peringkat

- `ts_rank_cd` dinormalisasi (`rank / (rank + 1)`), jadi nilainya selalu di bawah 1. Urutan menjadi seperti yang dimaksud ADR-0042: jumlah hal dari pertanyaan yang cocok lebih dulu, lalu `ts_rank_cd`. Sitasi dan judul tetap berbobot A, isi berbobot B.
- "belum" tidak lagi dibuang sebagai kata umum, karena "Belum tahu" adalah jawaban fasilitas AksesBogor ([ADR-0017](0017-empat-jawaban-ketersediaan-fasilitas.md)).

### Stemmer

- Stemmer ditulis sendiri di `src/knowledge/stemmer.ts`: urutan Nazief–Adriani (partikel dan "-nya", akhiran -kan/-an/-i, lalu sampai tiga awalan) dengan aturan awalan Enhanced Confix Stripping untuk awalan nasal, mis. "menyediakan" → "sedia", "pengguna" → "guna".
- Daftar kata dasar di `src/knowledge/kata-dasar.ts` kecil (184 kata) dan ditulis tim: hanya kata yang tanpa daftar akan salah potong, mis. "pakai" (bukan "paka" + -i), "rusak" dalam "kerusakan", "nilai" dalam "menilai", dan kata tugas seperti "termasuk" supaya tidak bercampur dengan "masuk". Bila tidak ada calon di daftar, dipakai calon pertama yang lolos syarat: bentuk dasarnya paling sedikit dua suku kata, akhiran -i hanya dibuang bersama awalan, dan "-si" serapan ("verifikasi") tidak dipotong.
- Hasilnya tidak harus kata baku. Yang penting, bentuk-bentuk satu kata menjadi sama di teks dan di pertanyaan.
- Kolom `search_stem` (migrasi 0009) berisi sitasi, judul, dan isi dalam bentuk dasar, dihitung API saat potongan disimpan. `pnpm pengetahuan:indeks` menghitung ulang semua potongan tanpa mengunduh, dan dijalankan setelah migrasi dan setiap kali stemmer atau daftar kata dasarnya berubah.
- Setiap kata pertanyaan cocok bila kata itu cocok apa adanya di kolom `search` (kata 4 huruf atau lebih sebagai awalan, seperti sebelumnya) atau bentuk dasarnya cocok di `search_stem`.

### Padanan kata

- Satu berkas `src/knowledge/sinonim.ts` berisi kelompok padanan, mis. ram/ramp/jalur landai/bidang miring, toilet/WC/kamar mandi/kloset, guiding block/ubin pemandu/jalur pemandu, lift/lif/elevator, halte/shelter, angkot/angkutan kota, BisKita/Trans Pakuan, angkutan umum/transportasi publik, dan bahasa peraturan: "minimal" = "paling sedikit", "maksimal" = "paling besar". "Penyandang cacat" ada hanya untuk menemukan teks lama dan tidak dipakai AksesBogor ([ADR-0041](0041-halaman-edukasi-sebagai-draf-sampai-ditinjau-komunitas.md)).
- Ungkapan di pertanyaan dikenali dalam bentuk dasarnya, yang terpanjang lebih dulu, lalu menjadi satu hal yang dicari dengan semua padanannya. Ungkapan beberapa kata dicocokkan sebagai frasa.
- Menambah padanan berarti mengubah berkas itu lewat pull request dan menjalankan `pnpm pengetahuan:evaluasi`.

### Hasil pengukuran

`pnpm pengetahuan:evaluasi` di akses-bogor-api, basis pengetahuan 434 potongan dari salinan 2026-11-16. Set utama dipakai untuk menyetel; set terpisah (12 pertanyaan) ditulis setelah penyetelan selesai dan hanya untuk memeriksa.

| Langkah | R@3 | R@5 | R@8 | MRR | Set terpisah: R@3 / MRR |
| --- | --- | --- | --- | --- | --- |
| Awal (ADR-0042) | 63% | 63% | 73% | 0,561 | 92% / 0,767 |
| `ts_rank_cd` dinormalisasi | 73% | 87% | 90% | 0,615 | 92% / 0,819 |
| "belum" bukan kata umum | 73% | 87% | 90% | 0,632 | 92% / 0,819 |
| Stemmer | 77% | 87% | 90% | 0,695 | 100% / 0,917 |
| Padanan kata | 93% | 93% | 100% | 0,870 | 100% / 0,903 |

Dua pertanyaan set utama masih di luar 3 besar, walau tetap di 8 besar: R13 "Kapan guiding block dinilai tidak tersedia oleh verifikator?" (peringkat 8) dan R21 "Verifikator harus bagaimana kalau jawaban pelapor tidak bisa dibuktikan?" (peringkat 7). Keduanya memakai kata yang ada di hampir semua bagian Panduan Verifikasi ("verifikator", "jawaban", "pelapor", "tersedia"), sehingga potongan yang tepat hanya unggul lewat satu atau dua kata.

Yang diukur dan tidak dipakai, karena tidak membantu: bobot kata jarang (IDF) sebagai pengganti jumlah kata cocok (MRR 0,768 dengan padanan kata), bonus untuk kecocokan di judul (judul Permen berupa jalur bab yang panjang, jadi bonusnya menurunkan MRR ke 0,818–0,844), nilai setengah untuk kecocokan bentuk dasar saja, dan menambah kata tanya ("kapan", "seberapa") ke kata umum. Pemotongan potongan teks tidak diubah karena tidak ada pertanyaan yang gagal karena batas potongan. Koreksi salah ketik per kata tidak dibuat: dua pertanyaan bersalah ketik di set uji sudah ditemukan lewat kata lain, dan `pg_trgm` tetap dipakai bila tidak ada yang cocok.

## Konsekuensi

- Positif: potongan yang dibutuhkan agen jauh lebih sering ada di hasil teratas, terutama pertanyaan dengan kata sehari-hari atau berimbuhan.
- Positif: tetap tanpa layanan luar, tanpa biaya, dan tanpa berkas sistem di server.
- Negatif: daftar kata dasar dan padanan perlu dirawat. Stemmer bisa menyamakan kata yang berbeda arti (mis. "alasan" dan "alas"); yang mengganggu pencarian dimasukkan ke daftar kata dasar.
- Negatif: setelah rilis yang membawa migrasi 0009, `pnpm pengetahuan:indeks` wajib dijalankan sekali di production. Sebelum itu, `search_stem` kosong dan pencarian berjalan dengan kata apa adanya saja; padanan kata tetap bekerja.
- Negatif: set uji ditulis oleh orang yang menyetel, jadi angka set utama bisa terlalu optimis. Set terpisah mengurangi risiko itu, dan tambahan pertanyaan dari tim membantu.
- Embedding (Opsi 5) tidak dibutuhkan saat ini: set utama sudah mencapai recall@8 100%. Bila nanti pertanyaan nyata menunjukkan full-text search tidak cukup, embedding tetap butuh ADR sendiri.

## Pro & Kontra tiap Opsi

### Tetap seperti ADR-0042

- Pro: tanpa pekerjaan dan tanpa migrasi.
- Kontra: recall@3 63%, dan urutan hasil tidak sesuai yang tertulis di ADR-0042.

### Stemmer di API dengan kolom bentuk dasar, padanan kata, dan peringkat yang diperbaiki

- Pro: angka di atas; semua aturan bisa dibaca, diuji, dan ditinjau di repo.
- Pro: tidak butuh berkas atau ekstensi baru di server PostgreSQL.
- Kontra: satu migrasi, satu skrip yang wajib dijalankan setelah rilis, dan dua daftar kata yang perlu dirawat.

### Kamus PostgreSQL (`ispell`, `synonym`, `dict_xsyn`)

- Pro: stemming dan padanan terjadi di PostgreSQL, termasuk saat indeks dihitung.
- Kontra: berkas kamus harus dipasang di direktori sistem PostgreSQL (`tsearch_data`) di VPS dengan akses root, di luar migrasi, dan tes PGlite butuh cara sendiri untuk memuat berkas yang sama.
- Kontra: kamus dan lisensinya masih harus dipilih; daftar padanan tetap perlu ditulis tim.

### Paket npm port Sastrawi (`sastrawijs`)

- Pro: algoritme Enhanced Confix Stripping yang sudah lengkap.
- Kontra: menurut README Sastrawi dan `sastrawijs`, kamus kata dasarnya dari Kateglo dengan lisensi CC-BY-NC-SA 3.0, sedangkan kodenya MIT. Kamus itu sekitar 30.000 kata di dalam paket. Repo Sastrawi bertanda tidak aktif, dan `sastrawijs` terakhir rilis Maret 2024.

### Embedding dengan pgvector

- Pro: memahami makna dan padanan tanpa daftar.
- Kontra: teks dikirim ke penyedia embedding, ekstensi pgvector perlu dipasang, penyedianya belum dipilih, dan butuh ADR sendiri (ADR-0042).

## Referensi

- [ADR-0042](0042-agen-rute-jalan-kaki-dan-informasi-aksesibilitas.md), bagian "Retrieval tanpa penyedia embedding"
- [ADR-0043](0043-agen-untuk-kontributor-dengan-model-gratis-openrouter.md), aturan 8
- Issue al-dioooo/akses-bogor-api#71 dan PR di tumpukannya
- [Sastrawi](https://github.com/sastrawi/sastrawi) dan [sastrawijs](https://github.com/damzaky/sastrawijs), bagian lisensi README
- Asian, J., Williams, H. E., & Tahaghoghi, S. M. M. (2005). Stemming Indonesian. Proceedings of the 28th Australasian Computer Science Conference.
- Arifin, A. Z., Mahendra, I. P. A. K., & Ciptaningtyas, H. T. (2009). Enhanced Confix Stripping Stemmer and Ants Algorithm for Classifying News Document in Indonesian Language. International Conference on Information & Communication Technology and Systems.
