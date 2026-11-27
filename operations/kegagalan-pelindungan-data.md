# Runbook Kegagalan Pelindungan Data Pribadi

| | |
| --- | --- |
| Status | Berlaku |
| Tanggal | 2026-11-27 |
| Berlaku untuk | Semua data pribadi yang diproses AksesBogor: server produksi (fatir-mitya), cadangan dan foto di Cloudflare R2, dan data yang dikirim ke penyedia di [Kebijakan Privasi](../product/kebijakan-privasi.md) |
| Penanggung jawab | Aldio Lisafron, penanggung jawab pelindungan data pribadi |
| Issue | #156 |

Runbook ini dipakai saat data pribadi pengguna AksesBogor bocor, hilang, rusak, diubah, atau dibuka pihak yang tidak berhak, atau saat ada dugaan ke arah itu. Dasarnya Pasal 46 UU No. 27 Tahun 2022 tentang Pelindungan Data Pribadi (UU PDP) dan janji di Kebijakan Privasi bagian Keamanan:

- Ayat (1): pemberitahuan **tertulis** paling lambat **3 × 24 jam** kepada subjek data pribadi dan kepada lembaga.
- Ayat (2): isi minimal pemberitahuan: data pribadi yang terungkap, kapan dan bagaimana data itu terungkap, serta upaya penanganan dan pemulihannya.
- Ayat (3): dalam hal tertentu, masyarakat juga diberi tahu.

Penjelasan Pasal 46 memakai kata "kegagalan", yang lebih luas dari kebocoran: perusakan, kehilangan, perubahan, pengungkapan, atau akses yang tidak sah. Data yang hilang karena database rusak tanpa cadangan juga termasuk.

Repo ini publik. Jangan menulis email, nama pengguna, isi laporan, rahasia, atau rincian celah yang belum ditutup di dokumen ini, di issue, di PR, atau di chat grup yang tidak tertutup. Rincian kejadian disimpan di catatan privat tim (lihat [bagian 8](#8-catatan-kejadian)).

## 1. Contoh Kejadian

| Kejadian | Contoh di AksesBogor |
| --- | --- |
| Rahasia bocor | Isi `api.env`, `web.env`, atau `backup.env` (mis. `BETTER_AUTH_SECRET`, `RESEND_API_KEY`, `R2_SECRET_ACCESS_KEY`, `WEB_SERVER_SECRET`) masuk ke repo, issue, tangkapan layar, atau chat |
| Cadangan dibuka pihak lain | Dump di `/var/backups/akses-bogor` atau di bucket R2 `akses-bogor-backup` diunduh orang yang tidak berhak. Dump berisi seluruh database, termasuk email, nama tampilan, dan laporan |
| Akun penyedia dibobol | Akun Cloudflare (tunnel, R2, DNS), Resend, Mapbox, Anthropic, atau GitHub tim dimasuki orang lain |
| Server dibobol atau hilang | Ada login atau proses asing di fatir-mitya, atau perangkat server dicuri atau rusak |
| Salinan lama | Database atau dump lama di Evergarden (OCI) dibuka pihak lain sebelum dihapus ([ADR-0059](../decisions/0059-produksi-pindah-ke-server-tim-di-karawang.md)) |
| Bug yang membuka data | Endpoint API atau halaman web menampilkan email, nama, atau laporan yang belum terbit kepada orang yang tidak berhak |
| Salah kirim | Email status laporan atau kode masuk terkirim ke alamat yang salah, atau email ke banyak pengguna memperlihatkan alamat penerima lain |
| Foto | Foto terbit dengan wajah atau pelat nomor yang masih jelas |
| Data hilang atau berubah | Database rusak dan dump terakhir tidak bisa dipulihkan, atau akun verifikator dipakai orang lain untuk mengubah data |
| Pemberitahuan dari penyedia | Cloudflare, Resend, Mapbox, Anthropic, atau Google memberi tahu ada kegagalan di sistem mereka yang menyangkut data AksesBogor |

Yang bukan kegagalan pelindungan, tetapi tetap dicatat di [bagian 8](#8-catatan-kejadian): situs mati tanpa data yang hilang, dan serangan yang tercatat di log tetapi terbukti tidak berhasil.

## 2. Peran

- **Siapa pun yang menemukan** (anggota tim, verifikator, atau pengguna lewat email kontak) langsung memberi tahu Aldio Lisafron lewat kanal tim yang tertutup dan lewat email kontak. Catat jam saat pertama tahu.
- **Penanggung jawab**: Aldio Lisafron. Memimpin penanganan, mengelola server dan database, memutuskan bersama penilai kedua, dan mengirim pemberitahuan.
- **Penilai kedua**: paling sedikit satu anggota tim lain (Arief Adhitya Ramadhan, Badzlan Nur Dhabith, Muhammad Rifal Dzulfikar, atau Rizky Ramadhan Agmer), siapa yang paling cepat bisa. Ikut menilai apakah ini kegagalan pelindungan dan siapa yang terdampak, lalu membaca ulang semua teks pemberitahuan sebelum dikirim.
- Keputusan "ini kegagalan pelindungan atau bukan" diambil penanggung jawab dan penilai kedua bersama. Bila keduanya tidak sepakat, perlakukan sebagai kegagalan pelindungan.
- Bila Aldio tidak bisa dihubungi dalam 6 jam, dua anggota lain menjalankan penilaian dan pemberitahuan. Langkah yang butuh akses server menunggu Aldio, tetapi hitungan 3 × 24 jam tetap berjalan. Email kontak adalah akun kampus Aldio, jadi pemberitahuan dikirim dari email kampus anggota yang menangani, dengan menyebut bahwa balasan juga bisa dikirim ke email kontak.

## 3. Hitungan 3 × 24 Jam

Pasal 46 tidak menyebut kapan 3 × 24 jam mulai dihitung, dan Kebijakan Privasi mengikuti bunyi pasal itu. Sampai peninjau hukum memutuskan lain, **jam ke-0 adalah saat anggota tim mana pun pertama kali tahu atau menduga ada kegagalan**, bukan saat penyebabnya pasti. Bila kejadiannya diketahui lewat pemberitahuan penyedia, jam ke-0 adalah saat email penyedia itu diterima.

| Jam | Langkah |
| --- | --- |
| 0–1 | Catat jam ke-0. Hubungi penanggung jawab dan penilai kedua. Buka catatan privat kejadian dan baris baru di [bagian 8](#8-catatan-kejadian) |
| 0–4 | Simpan bukti, lalu hentikan kebocoran ([bagian 4](#4-menghentikan-dan-menyimpan-bukti)) |
| 4–24 | Nilai kejadiannya: data apa, siapa yang terdampak, sejak kapan, lewat apa, dan apakah masih berlangsung. Buat daftar akun terdampak di catatan privat |
| 24–48 | Tulis email ke pengguna ([bagian 5](#5-email-ke-pengguna)), surat ke lembaga ([bagian 6](#6-pemberitahuan-ke-lembaga)), dan bila perlu pengumuman Beranda ([bagian 7](#7-pengumuman-di-beranda)). Penilai kedua membaca ulang |
| Paling lambat 72 | Kirim semua pemberitahuan, juga bila penyelidikan belum selesai. Tulis apa yang sudah diketahui dan sebut bahwa kabar lanjutan akan menyusul |
| Setelah 72 | Kirim kabar lanjutan saat penyebab dan pemulihan sudah jelas. Tutup baris di bagian 8. Perbarui runbook, ADR, atau kode yang perlu diubah |

## 4. Menghentikan dan Menyimpan Bukti

Simpan bukti dulu, lalu ubah sistem. Bukti yang hilang membuat isi pemberitahuan ("kapan dan bagaimana") tidak bisa dijawab.

- Bukti: ekspor journal yang relevan (`journalctl -u akses-bogor-api -u akses-bogor-web --since "<waktu>" > <berkas>`), daftar objek R2 beserta waktunya (perintah di [runbook cadangan](cadangan-database.md), bagian 3), log aktivitas atau audit di dashboard penyedia, tangkapan layar, dan email penyedia. Simpan di folder yang hanya bisa dibaca root di server, atau di penyimpanan privat tim, bukan di repo.
- Catat setiap tindakan beserta jamnya di catatan privat.

| Kejadian | Langkah pertama |
| --- | --- |
| Rahasia di `api.env` atau `web.env` bocor | Buat nilai baru di penyedia (Resend, Mapbox, Anthropic, R2) atau buat acak baru (`BETTER_AUTH_SECRET`, `WEB_SERVER_SECRET`), cabut nilai lama, perbarui berkas, restart service. Bila sesi mungkin dicuri, hapus semua baris tabel `sessions` supaya semua orang masuk ulang |
| Token R2 cadangan bocor | Hapus token di dashboard Cloudflare, buat token baru khusus bucket `akses-bogor-backup`, perbarui `backup.env`, jalankan cadangan dan pastikan log menulis "disalin ke r2:…" |
| Akun Cloudflare atau penyedia lain dibobol | Ganti kata sandi, nyalakan atau setel ulang verifikasi dua langkah, cabut sesi dan token API yang tidak dikenal, tinjau anggota akun. Untuk Cloudflare periksa juga tunnel, public hostname, DNS, dan bucket R2 |
| Server fatir-mitya dibobol | Putuskan dari internet dengan menghentikan `cloudflared` (satu-satunya jalan masuk dari luar). Jangan pasang ulang server sebelum bukti disimpan. Ganti semua rahasia dari tempat lain, lalu bangun ulang dan pulihkan dari dump terakhir yang bersih |
| Bug yang membuka data | Matikan fiturnya (flag di `api.env` bila ada) atau revert di `release`. Bila revert belum masuk, ikuti rollback manual di README akses-bogor-api atau akses-bogor-web |
| Salah kirim email | Minta penerima menghapus email itu. Periksa apakah salahnya di data akun atau di kode |
| Foto dengan wajah atau pelat jelas | Sembunyikan atau kaburkan foto itu lewat Kelola tempat, lalu nilai apakah orang di foto bisa dikenali |
| Data hilang atau rusak | Pulihkan dump terakhir yang utuh (README akses-bogor-api, bagian Cadangan dan Pemulihan). Laporan dan akun yang dibuat setelah dump itu ikut hilang, jadi pemiliknya termasuk yang terdampak |
| Pemberitahuan dari penyedia | Minta rincian ke penyedia: data apa, rentang waktu, dan apakah data AksesBogor ikut. Jam ke-0 sudah berjalan sejak email penyedia diterima |

## 5. Email ke Pengguna

Kirim ke setiap pemilik akun yang datanya terdampak. Pasal 46 meminta pemberitahuan tertulis, dan Kebijakan Privasi menjanjikannya lewat email.

- Kirim dari alamat email kontak di [Kebijakan Privasi](../product/kebijakan-privasi.md#kontak), supaya pengguna bisa membalas.
- Satu email per penerima, atau semua alamat di BCC. Jangan pernah menaruh lebih dari satu alamat pengguna di To atau CC, karena itu sendiri membuka email pengguna ke pengguna lain.
- Bahasa sederhana, kalimat pendek, tanpa lampiran, dan tanpa tautan selain alamat AksesBogor, supaya mudah dibaca pembaca layar dan tidak mirip email penipuan.
- Simpan salinan email yang terkirim beserta waktunya sebagai bukti.

Templat (isi semua `<…>`, hapus baris yang tidak berlaku):

```text
Subjek: Pemberitahuan kegagalan pelindungan data pribadi Anda di AksesBogor

Halo <nama tampilan>,

Kami memberi tahu Anda bahwa data pribadi Anda di AksesBogor terdampak kegagalan pelindungan data pribadi. Email ini kami kirim sesuai Pasal 46 UU No. 27 Tahun 2022 tentang Pelindungan Data Pribadi.

Data yang terdampak:
<mis. alamat email dan nama tampilan akun Anda. Sebut juga data yang TIDAK terdampak, mis. "Kata sandi tidak terdampak karena AksesBogor tidak memakai kata sandi.">

Kapan dan bagaimana:
<mis. "Pada 3 Desember 2026 sekitar pukul 21.00 WIB, salinan cadangan database terbuka bagi pihak yang tidak berhak karena kunci akses penyimpanan cadangan bocor. Kami mengetahuinya pada 4 Desember 2026 pukul 08.15 WIB.">

Yang sudah kami lakukan:
<mis. "Kami mencabut kunci akses itu pukul 08.40 WIB, membuat kunci baru, dan memeriksa bahwa salinan cadangan tidak lagi bisa dibuka. Semua sesi masuk kami akhiri.">

Yang masih kami kerjakan:
<mis. "Kami masih memeriksa apakah salinan itu sempat diunduh. Kami akan mengabari Anda lagi paling lambat <tanggal>.">

Yang bisa Anda lakukan:
- Waspadai email yang mengaku dari AksesBogor dan meminta kode masuk. Kami tidak pernah meminta kode masuk lewat email atau telepon.
- Bila Anda sedang masuk, Anda akan diminta masuk lagi.
- <saran lain yang sesuai kejadian>

Bila ada pertanyaan, atau Anda ingin akun Anda dihapus, balas email ini. Kami menjawab paling lambat 3 × 24 jam.

Kami mohon maaf atas kejadian ini.

Aldio Lisafron
Penanggung jawab pelindungan data pribadi, Tim AksesBogor
https://akses-bogor.aldio.dev
```

Kabar lanjutan memakai susunan yang sama dengan subjek "Kabar lanjutan: kegagalan pelindungan data pribadi di AksesBogor".

## 6. Pemberitahuan ke Lembaga

Pasal 46 ayat (1) huruf b mewajibkan pemberitahuan ke lembaga, yaitu lembaga penyelenggara pelindungan data pribadi yang ditetapkan Presiden (Pasal 58).

**Keadaan per 2026-11-27: tim belum mengetahui apakah lembaga itu sudah beroperasi atau saluran resmi mana yang menerima pemberitahuan kegagalan pelindungan.** Pemeriksaan UU PDP 2026-11-27 juga tidak menemukan Perpres pembentukannya di korpus pasal.id. Itu tidak berarti lembaganya belum ada. Karena itu, setiap kali ada kejadian:

1. Periksa dulu apakah lembaga sudah ada dan punya saluran pemberitahuan: cari Perpres lembaga PDP di [peraturan.go.id](https://peraturan.go.id), dan periksa situs resmi Kementerian Komunikasi dan Digital (Komdigi, [komdigi.go.id](https://www.komdigi.go.id)). Bila sudah ada, kirim ke saluran itu dan perbarui bagian ini.
2. Bila lembaga belum ada atau salurannya tidak ditemukan, kirim pemberitahuan tertulis yang sama ke saluran Komdigi untuk pelindungan data pribadi bila ada, atau ke saluran pengaduan resmi Komdigi bila tidak ada. Komdigi adalah kementerian yang selama ini mengurus pelindungan data pribadi di sistem elektronik.
3. Bila tidak ada saluran yang bisa dipakai, tulis suratnya tetap dalam 3 × 24 jam, simpan, dan catat di bagian 8 bahwa saluran tidak ditemukan. Kirim begitu saluran tersedia.
4. Dalam semua keadaan, simpan bukti: teks yang dikirim, waktu kirim, alamat atau formulir tujuan, tangkapan layar halaman saluran yang dicari, dan balasan yang diterima. Bukti ini menunjukkan tim sudah berusaha memenuhi Pasal 46 (akuntabilitas, Pasal 47).
5. Beri tahu juga dosen pembimbing atau program studi. Ini tidak diwajibkan Pasal 46, tetapi AksesBogor adalah proyek mahasiswa dan namanya disebut di Kebijakan Privasi.

Templat surat (email atau formulir):

```text
Perihal: Pemberitahuan kegagalan pelindungan data pribadi, AksesBogor

Pengendali data pribadi: Tim AksesBogor, kelompok mahasiswa Program Studi Teknologi Rekayasa Perangkat Lunak, Sekolah Vokasi IPB University
Layanan: https://akses-bogor.aldio.dev
Penanggung jawab dan kontak: Aldio Lisafron, <email kontak di Kebijakan Privasi>
Alamat surat: <alamat surat di Kebijakan Privasi>

1. Data pribadi yang terungkap: <jenis data> milik <jumlah> subjek data pribadi.
2. Kapan dan bagaimana: <waktu kejadian, waktu diketahui, dan penyebab yang sudah diketahui>.
3. Upaya penanganan dan pemulihan: <langkah dan waktunya>.
4. Pemberitahuan ke subjek data pribadi: dikirim lewat email pada <tanggal dan jam> ke <jumlah> penerima.<Pengumuman di situs pada <tanggal>, bila ada.>
5. Kabar lanjutan: <tanggal perkiraan>.

<tanggal>
Aldio Lisafron
```

## 7. Pengumuman di Beranda

Ayat (3) mewajibkan pemberitahuan ke masyarakat "dalam hal tertentu", dan Kebijakan Privasi menjanjikan pengumuman di Beranda. Pasang pengumuman bila salah satu ini benar:

- Ada subjek terdampak yang emailnya tidak kami punya, mis. pengunjung tanpa akun (alamat IP di log server atau di Cloudflare), atau orang yang wajahnya ada di foto.
- Ada email pemberitahuan yang tidak sampai (ditolak atau terpental).
- Kegagalannya berdampak serius bagi masyarakat, mis. seluruh atau sebagian besar akun terdampak, atau data pribadi sudah tersebar di tempat umum.

Beranda belum punya komponen pengumuman. Pengumuman dipasang lewat PR di akses-bogor-web: teks di `messages/id.json` (namespace `Beranda`) dan komponen di bagian atas Beranda, lalu merge ke `main` dan `release`. Timer deploy memasangnya paling lama sekitar 5 menit setelah `release` berubah, ditambah waktu build. Mulai PR-nya paling lambat jam ke-48.

Isi pengumuman mengikuti tiga butir isi minimal, tanpa data pribadi siapa pun, ditambah alamat kontak dan tanggal. Biarkan tayang paling sedikit 30 hari, atau sampai kabar lanjutan terakhir.

## 8. Catatan Kejadian

Setiap kejadian dan dugaan dicatat di sini, juga yang ternyata bukan kegagalan pelindungan. Tabel ini publik: tulis ringkasan tanpa data pribadi, tanpa rahasia, dan tanpa rincian celah yang belum ditutup. Rincian lengkap (daftar akun terdampak, bukti, salinan pemberitahuan) disimpan di catatan privat tim, dan kolom terakhir hanya menunjuk tempatnya.

| No | Jam ke-0 (WIB) | Ringkasan | Kegagalan pelindungan? | Subjek terdampak | Pengguna diberi tahu | Lembaga diberi tahu | Beranda | Ditutup | Catatan privat |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| — | — | Belum ada kejadian | — | — | — | — | — | — | — |

## 9. Latihan

Sekali setiap semester, atau setelah server, penyedia, atau anggota tim berganti, jalankan satu kejadian pura-pura dari bagian 1 bersama paling sedikit dua anggota: tentukan jam ke-0, tulis email dan surat dari templat, lalu periksa apakah semua akses yang dibutuhkan (server, dashboard Cloudflare, email kontak) memang dimiliki orang yang bertugas. Catat hasilnya di bagian 8 dengan ringkasan "Latihan".

## Referensi

- UU No. 27 Tahun 2022 Pasal 46 dan Penjelasannya, Pasal 47, Pasal 58 ([peraturan.go.id](https://peraturan.go.id/id/uu-no-27-tahun-2022))
- [Kebijakan Privasi](../product/kebijakan-privasi.md), bagian Keamanan dan Kontak
- [Pemeriksaan UU PDP 2026-11-27](../product/pemeriksaan-uu-pdp-2026-11-27.md), temuan K10 dan bagian "Kewajiban di luar teks kebijakan"
- [Runbook cadangan database](cadangan-database.md), [ADR-0059](../decisions/0059-produksi-pindah-ke-server-tim-di-karawang.md) (server produksi)
- [Catatan kegiatan pemrosesan](../product/catatan-kegiatan-pemrosesan.md) (data apa ada di mana, untuk menilai siapa yang terdampak)
