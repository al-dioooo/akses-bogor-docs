# Penilaian Dampak Pelindungan Data Pribadi AksesBogor

| | |
| --- | --- |
| Status | Berlaku, belum ditinjau peninjau hukum |
| Tanggal | 2026-11-27 |
| Sesuai | Kebijakan Privasi versi 2026-11-27 |
| Disusun | Tim AksesBogor; penanggung jawab Aldio Lisafron |
| Tinjau ulang | Saat versi Kebijakan Privasi naik, saat ada fitur atau penyedia baru yang memproses data pribadi, dan paling lambat 2027-05-27 |
| Issue | #158 |

Pasal 34 UU No. 27 Tahun 2022 tentang Pelindungan Data Pribadi (UU PDP) mewajibkan penilaian dampak bila pemrosesan berpotensi berisiko tinggi, antara lain karena "penggunaan teknologi baru" (ayat (2) huruf f) dan "Data Pribadi yang bersifat spesifik" (huruf b). Rinciannya diatur Peraturan Pemerintah (ayat (3)), yang belum diperiksa tim. Penilaian ini dibuat singkat sekarang supaya risikonya tercatat, dan akan diperbarui bila PP-nya diperiksa. Dokumen ini untuk tim, bukan nasihat hukum.

Skala saat ini kecil: pada uji pemulihan 2026-11-27 database berisi 6 akun dan 1 laporan ([runbook cadangan](../operations/cadangan-database.md)). Penilaian di bawah tetap memakai keadaan saat AksesBogor dipakai luas, karena mitigasinya harus siap sebelum itu.

Kemungkinan dan dampak dinilai **rendah**, **sedang**, atau **tinggi**, dari sudut orang yang datanya diproses, bukan dari sudut tim. Daftar kegiatan lengkapnya ada di [catatan kegiatan pemrosesan](catatan-kegiatan-pemrosesan.md).

## Ringkasan

| Hal | Mengapa berisiko tinggi | Risiko tersisa |
| --- | --- | --- |
| [Agen AI](#1-agen-ai) | Teknologi baru (model bahasa di luar negeri); pertanyaan bisa memuat keterangan kesehatan; dipakai tanpa akun | Sedang |
| [Data anak](#2-data-anak) | Data spesifik; usia tidak diperiksa | Sedang |
| [Foto yang bisa memuat wajah](#3-foto-yang-bisa-memuat-wajah) | Wajah adalah contoh data biometrik di Penjelasan Pasal 4; orang di foto tidak pernah memberi persetujuan | Sedang |
| [Cadangan database](#4-cadangan-database) | Satu berkas berisi seluruh data pribadi, disimpan di luar server tim | Sedang, turun ke rendah setelah enkripsi dan penghapusan salinan Evergarden |

Kesimpulan: keempat pemrosesan boleh terus berjalan dengan mitigasi yang ada, dengan syarat tindakan bertanda **wajib** di bawah dikerjakan sebelum AksesBogor diumumkan ke publik.

## 1. Agen AI

Siapa pun, juga tanpa akun dan termasuk anak, bisa bertanya kepada Agen AI. Pertanyaan dan paling banyak 6 giliran sebelumnya dikirim ke Anthropic (Claude API), yang menyimpan data di Amerika Serikat ([ADR-0054](../decisions/0054-agen-ai-satu-pintu.md)).

| Risiko | Kemungkinan | Dampak |
| --- | --- | --- |
| Pengguna menulis keterangan kesehatan (mis. "saya pakai kursi roda", jenis disabilitas) atau identitas (nama, nomor telepon) di pertanyaan, lalu teks itu tersimpan di Anthropic sampai 30 hari, atau sampai 2 tahun bila ditandai melanggar kebijakan penggunaannya | Sedang | Sedang |
| Posisi pengguna bocor ke Anthropic atau tersimpan | Rendah | Sedang |
| Orang lain di perangkat bersama membaca percakapan | Sedang | Rendah |
| Penyedia model diganti (mis. OpenRouter) tanpa memperbarui kebijakan, sehingga ada penerima baru yang tidak diketahui pengguna | Rendah | Sedang |

Mitigasi yang sudah berjalan:

- Anthropic tidak menerima posisi, email, nama, atau alamat IP, juga untuk pengguna tanpa akun. Titik awal dibulatkan sekitar 11 m dan hanya dipakai server kami; Anthropic hanya menerima jarak garis lurus yang dibulatkan ke 100 m.
- Pertanyaan dan jawaban tidak disimpan di server kami. Pertanyaan dikirim di badan permintaan supaya tidak tercatat di log, dan logger API menyamarkan header cookie dan otorisasi.
- Halaman Agen meminta pengguna tidak menulis nama, nomor telepon, atau alamat rumah, dan menulis kebutuhannya "seperlunya". Kebijakan Privasi menyebut bahwa keterangan itu bisa termasuk data kesehatan.
- Percakapan hanya disimpan di tab browser, hilang saat tab ditutup, dan ada tombol "Percakapan baru".
- Alat agen hanya mencari di data tempat AksesBogor, OpenStreetMap, dan basis pengetahuan, bukan data akun.
- Ketentuan Komersial dan perjanjian pemrosesan data Anthropic melarang data dipakai untuk melatih model.
- Jumlah pertanyaan per akun atau per alamat IP hanya dihitung di memori sampai hari berganti.

Tindakan yang tersisa:

- [ ] Pertimbangkan peringatan sebelum kirim bila pertanyaan tampak memuat nomor telepon atau email, atau menyamarkannya sebelum dikirim ke Anthropic.
- [ ] Jangan nyalakan `AGENT_PROVIDER=openrouter` di produksi sebelum OpenRouter dan modelnya masuk ke Kebijakan Privasi (temuan K11 pemeriksaan UU PDP).
- [ ] Peninjau hukum: dasar pemrosesan untuk pengguna tanpa akun (huruf b) dan transfer ke Amerika Serikat untuk mereka, yang tidak bisa bergantung pada persetujuan saat Lengkapi Akun.

Risiko tersisa: **sedang**, karena isi pertanyaan sepenuhnya ditentukan pengguna dan tetap keluar dari Indonesia.

## 2. Data Anak

Data anak adalah data pribadi yang bersifat spesifik (Pasal 4 ayat (2) huruf e), dan pemrosesannya butuh persetujuan orang tua atau wali (Pasal 25). AksesBogor tidak ditujukan khusus untuk anak, tetapi tidak menghalangi anak memakai akun, melapor, atau bertanya kepada Agen AI.

| Risiko | Kemungkinan | Dampak |
| --- | --- | --- |
| Anak membuat akun dan melapor tanpa orang tua atau wali | Sedang | Sedang |
| Anak menulis data pribadi di pertanyaan Agen AI | Sedang | Sedang |
| Foto laporan dari anak memuat dirinya atau anak lain | Rendah | Tinggi |

Mitigasi yang sudah berjalan:

- Sejak versi 2026-11-27, kotak persetujuan di Lengkapi Akun memuat pernyataan bahwa orang tua atau wali ikut menyetujui bila pengguna di bawah 18 tahun, dan versi itu disimpan bersama waktu persetujuan (Pasal 24).
- Kebijakan Privasi bagian Anak meminta anak bertanya kepada Agen AI bersama orang tua atau wali, dan menjanjikan penghapusan akun yang dibuat anak tanpa persetujuan.
- Data yang dikumpulkan sudah minimal: tidak ada tanggal lahir, foto profil, atau kontak selain email.
- Foto anak di laporan melewati pengaburan wajah oleh verifikator yang sama dengan foto lain.

Tindakan yang tersisa:

- [ ] Tulis langkah menangani akun yang diketahui dibuat anak tanpa persetujuan di runbook permintaan subjek data (`operations/permintaan-subjek-data.md`, dikerjakan terpisah).
- [ ] Peninjau hukum: apakah pernyataan di kotak centang cukup sebagai bukti persetujuan wali (Pasal 24 dan 25).

Tim sengaja tidak menambah pemeriksaan usia, karena itu berarti mengumpulkan tanggal lahir atau dokumen dari semua pengguna. Risiko tersisa: **sedang**.

## 3. Foto yang Bisa Memuat Wajah

Foto tempat bisa merekam orang yang lewat, pengguna kursi roda, atau pelat nomor kendaraan. Orang itu adalah subjek data juga (Pasal 2 ayat (1)) dan tidak pernah memberi persetujuan. Ada tiga sumber foto: laporan warga, unggahan admin lewat Kelola tempat ([ADR-0051](../decisions/0051-kelola-tempat-oleh-admin-dan-terbit-langsung.md)), dan Street View ([ADR-0057](../decisions/0057-verifikasi-dengan-bukti-street-view.md)).

| Risiko | Kemungkinan | Dampak |
| --- | --- | --- |
| Wajah atau pelat lolos dari pengaburan pada foto laporan | Rendah | Sedang |
| Foto unggahan admin tayang dengan wajah atau pelat, karena langsung tayang tanpa dikaburkan | Sedang | Sedang |
| Foto Street View tayang tanpa diperiksa ulang oleh tim | Rendah | Sedang |
| Lokasi pemotretan atau perangkat terbaca dari metadata foto | Rendah | Sedang |

Mitigasi yang sudah berjalan:

- Foto laporan diperkecil dan disimpan ulang tanpa EXIF, termasuk GPS. Sebelum terbit, verifikator mengaburkan wajah dan pelat atau menyembunyikan fotonya, lalu foto asli dihapus ([ADR-0037](../decisions/0037-foto-dikaburkan-di-api-dan-asli-dihapus.md)).
- Foto laporan disimpan di bucket R2 privat sampai terbit.
- Formulir Kelola tempat memperingatkan admin: "Foto langsung tayang tanpa dikaburkan. Jangan unggah wajah, pelat nomor, atau dokumen yang terbaca."
- Google mengaburkan wajah dan pelat di Street View, dan foto Street View diberi keterangan sumbernya. Semua foto Street View bisa dihapus per putaran atau sekaligus.
- Kebijakan Privasi meminta siapa pun yang melihat wajah atau pelat jelas memberi tahu tim, lalu foto dikaburkan atau dihapus.

Tindakan yang tersisa:

- [ ] **Wajib**: jadikan foto unggahan admin lewat Kelola tempat sesuai janji Kebijakan Privasi, yaitu ikut dikaburkan sebelum tayang, atau ubah kebijakan supaya menyebut pengecualian ini.
- [ ] Tambahkan langkah pemeriksaan wajah dan pelat sebelum terbit di [runbook verifikasi Street View](../operations/verifikasi-street-view.md) (temuan K13d).
- [ ] Tetapkan masa simpan folder putaran Street View di host pekerja dan di server.
- [ ] Kelola tempat hanya bisa menghapus foto yang sudah terbit, belum bisa mengaburkannya. Pertimbangkan alat kabur yang sama dengan panel verifikator.

Risiko tersisa: **sedang** sampai foto admin ikut dikaburkan, lalu **rendah**.

## 4. Cadangan Database

Dump malam berisi seluruh database: email, nama tampilan, sesi, laporan, jawaban klarifikasi, dan log audit. Tujuh dump terakhir ada di server, dan sejak 2026-11-27 salinannya disimpan 30 hari di bucket R2 `akses-bogor-backup`, lokasi Asia-Pasifik ([runbook cadangan](../operations/cadangan-database.md)).

| Risiko | Kemungkinan | Dampak |
| --- | --- | --- |
| Token R2 atau akun Cloudflare dibobol, lalu dump diunduh | Rendah | Tinggi |
| Database dan dump lama di Evergarden (OCI) dibuka pihak lain | Rendah | Tinggi |
| Berkas uji pemulihan tertinggal di server | Rendah | Tinggi |
| Memulihkan dump lama menghidupkan lagi akun yang sudah dihapus setelah dump itu dibuat | Sedang | Sedang |

Mitigasi yang sudah berjalan:

- Bucket privat yang terpisah dari foto, token R2 hanya untuk bucket itu, dan `backup.env` hanya bisa dibaca root.
- Lifecycle rule menghapus salinan R2 setelah 30 hari, dan Kebijakan Privasi menyebut bahwa data akun yang dihapus bisa tetap ada di cadangan sampai 30 hari ([ADR-0024](../decisions/0024-data-setelah-akun-dihapus.md)).
- Folder dump di server hanya bisa dibaca user `postgres`.
- Uji pemulihan mengunduh dump ke folder yang hanya bisa dibaca `postgres` dan menghapus berkas serta database ujinya di akhir.
- Enkripsi dump sebelum diunggah sedang ditambahkan (lihat runbook cadangan dan [ADR-0025](../decisions/0025-api-dan-postgresql-di-vps-sendiri.md)).

Tindakan yang tersisa:

- [ ] **Wajib**: selesaikan enkripsi dump dengan kunci privat yang disimpan di luar Cloudflare, sehingga akun Cloudflare yang dibobol tidak cukup untuk membaca isinya.
- [ ] **Wajib**: hapus database dan dump lama di Evergarden ([ADR-0059](../decisions/0059-produksi-pindah-ke-server-tim-di-karawang.md), tindakan tersisa).
- [ ] Pastikan akun Cloudflare tim memakai verifikasi dua langkah, dan catat siapa saja anggotanya.
- [ ] Tambahkan ke langkah pemulihan produksi di README akses-bogor-api: setelah memulihkan dump, hapus lagi akun yang dihapus setelah dump itu dibuat, berdasarkan catatan permintaan subjek data dan email konfirmasi hapus akun.

Risiko tersisa: **sedang**, turun ke **rendah** setelah dua tindakan wajib selesai.

## Referensi

- UU No. 27 Tahun 2022 Pasal 2, 4 dan Penjelasannya, 24, 25, 34, 56 ([peraturan.go.id](https://peraturan.go.id/id/uu-no-27-tahun-2022))
- [Kebijakan Privasi](kebijakan-privasi.md) versi 2026-11-27, [catatan kegiatan pemrosesan](catatan-kegiatan-pemrosesan.md), [runbook kegagalan pelindungan](../operations/kegagalan-pelindungan-data.md)
- [Pemeriksaan UU PDP 2026-11-27](pemeriksaan-uu-pdp-2026-11-27.md), temuan K11, K13a, K13c, K13d, K14, dan bagian "Kewajiban di luar teks kebijakan"
