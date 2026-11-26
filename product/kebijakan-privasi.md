# Kebijakan Privasi AksesBogor (Draf)

| | |
| --- | --- |
| Status | Draf, belum ditinjau |
| Versi | 2026-11-26 |
| Pemilik | Tim AksesBogor |
| Issue | #51, #55, #59, #62, #89, #99, #106, #124 |

Dokumen ini adalah sumber teks halaman `/kebijakan-privasi` di akses-bogor-web. Pengguna menyetujui versi yang tertulis di atas saat Lengkapi Akun (FR-11), dan versi itu disimpan bersama waktu persetujuannya ([ADR-0029](../decisions/0029-akun-better-auth-dan-sesi-lewat-server-web.md)). Setiap perubahan isi menaikkan versi, dan pengguna diminta menyetujui ulang saat masuk berikutnya.

## Sebelum Rilis

Bagian ini untuk tim dan tidak tampil di web.

- [ ] Isi identitas pengendali data (nama tim dan kampus) dan alamat email kontak resmi. `info@aksesbogor.id` di Figma masih placeholder.
- [x] Tetapkan masa simpan log server di Evergarden: 30 hari, diputuskan 2026-11-08 ([ADR-0040](../decisions/0040-syarat-layanan-lisensi-laporan-dan-persetujuan.md)). journald diatur saat rilis pertama.
- [ ] Minta dosen pembimbing atau unit hukum kampus meninjau teks ini bersama [Syarat Layanan](syarat-layanan.md), terutama dasar pemrosesan, transfer ke luar negeri, batas waktu 3 × 24 jam, dan bagian anak.
- [x] Tambahkan rincian foto dan lokasi laporan saat Laporkan (FR-20 sampai FR-27) dibangun.
- [ ] Ubah status menjadi final, hapus kata "Draf" di judul halaman web, dan naikkan versi bila isinya berubah.
- [x] Rilis versi 2026-11-22 (agen Bantu memetakan dengan Anthropic, [ADR-0047](../decisions/0047-model-agen-kontributor-claude-haiku-lewat-claude-api.md)) di API dan web bersamaan: `PRIVACY_POLICY_VERSION` di keduanya harus sama, karena API menolak persetujuan untuk versi lain.
- [x] Rilis versi 2026-11-23 (web di Evergarden, [ADR-0048](../decisions/0048-web-di-host-sendiri-di-evergarden-oci.md), dan Lokasi saya di Peta): web dulu, lalu `PRIVACY_POLICY_VERSION` di API beberapa menit kemudian. Bila API lebih dulu, semua akun diminta menyetujui ulang di web lama, yang masih mengirim versi 2026-11-22 dan ditolak dengan 409.
- [ ] Rilis versi 2026-11-24 (asisten Tanya rute untuk semua pengunjung, [ADR-0050](../decisions/0050-asisten-rute-publik.md)): web dulu, lalu `PRIVACY_POLICY_VERSION` di API beberapa menit kemudian, lalu `ROUTE_ASSISTANT_ENABLED=true` di API. Versi 2026-11-23 sudah dipakai, jadi versi ini bertanggal 2026-11-24 walaupun dirilis lebih awal.
- [ ] Rilis versi 2026-11-26 (Agen AI menggantikan Bantu memetakan dan Tanya rute, [ADR-0054](../decisions/0054-agen-ai-satu-pintu.md)): web dulu, lalu `PRIVACY_POLICY_VERSION` di API beberapa menit kemudian.

## Riwayat Versi

Bagian ini untuk tim dan tidak tampil di web. Setiap versi baru meminta persetujuan ulang saat pengguna masuk berikutnya.

| Versi | Perubahan | Issue |
| --- | --- | --- |
| 2026-11-07 | Draf pertama untuk persetujuan saat Lengkapi Akun | #51 |
| 2026-11-08 | Foto laporan, draf laporan di perangkat, dan pencarian tempat lewat Mapbox | #55 |
| 2026-11-09 | Klarifikasi, moderasi foto, email status laporan, dan log audit | #59 |
| 2026-11-10 | Masa simpan log server 30 hari | #62 |
| 2026-11-22 | Bantu memetakan dengan Anthropic (Claude API), dan rute jalan kaki lewat Mapbox untuk kartu rute | #89 |
| 2026-11-23 | Situs web pindah dari Vercel ke server kami di Oracle Cloud (Batam); Cloudflare mem-proxy situs web dan API; Tampilkan lokasi saya di Peta | #99 |
| 2026-11-24 | Tanya rute: asisten rute untuk semua pengunjung, juga tanpa akun, dengan Anthropic (Claude API), titik awal yang hanya dipakai server kami dan Mapbox, dan jatah pertanyaan per alamat IP | #106 |
| 2026-11-26 | Bantu memetakan dan Tanya rute menjadi satu Agen AI; percakapan disimpan di tab browser sampai tab ditutup | #124 |

## Teks Kebijakan

### Siapa kami

AksesBogor adalah web yang mencatat aksesibilitas fasilitas publik di Kota Bogor bagi penyandang disabilitas dan pendampingnya. AksesBogor dikelola Tim AksesBogor sebagai proyek mahasiswa. Tim AksesBogor adalah pengendali data pribadi Anda sesuai UU No. 27 Tahun 2022 tentang Pelindungan Data Pribadi.

### Data yang kami kumpulkan

Anda bisa menjelajahi AksesBogor tanpa akun. Kami hanya mengumpulkan data pribadi bila Anda membuat akun, melapor, atau bertanya kepada Agen AI.

- **Akun**: alamat email, nama tampilan, peran (pengguna, verifikator, atau admin), serta waktu dan versi Kebijakan Privasi yang Anda setujui.
- **Masuk**: kode 6 digit yang kami kirim ke email Anda, disimpan dalam bentuk acak (hash) selama 10 menit; sesi masuk (token, waktu dibuat, dan waktu terakhir dipakai). Bila Anda masuk dengan Google, kami menyimpan nomor akun Google Anda untuk menyambungkannya ke akun AksesBogor. Kami tidak menyimpan foto profil, kontak, atau token akses Google.
- **Laporan**: isian laporan (nama tempat, kategori, patokan, jawaban fasilitas, catatan, dan deskripsi), titik yang Anda konfirmasi di peta, dan foto yang Anda kirim. Lokasi perangkat hanya dibaca saat Anda menekan "Gunakan lokasi saya" di formulir laporan dan tidak disimpan. Foto diperkecil lalu disimpan ulang tanpa metadata (EXIF), termasuk lokasi GPS. Bila verifikator meminta klarifikasi, jawaban Anda dan foto tambahannya ikut disimpan sebagai bagian laporan.
- **Lokasi saya di Peta**: lokasi perangkat hanya dibaca setelah Anda menekan "Tampilkan lokasi saya". Bila izin lokasi belum diberikan, kami menjelaskan dulu kegunaannya, lalu browser meminta izin Anda. Lokasi itu hanya dipakai di browser Anda untuk menggeser peta dan menaruh titik di posisi Anda. Lokasi tidak dikirim ke server AksesBogor atau ke Anthropic, dan tidak disimpan. Bila Anda berada di luar wilayah peta Kota Bogor, peta tidak digeser. Seperti setiap tampilan peta, Mapbox menerima area peta yang dimuat, jadi setelah peta digeser, Mapbox menerima area di sekitar lokasi Anda.
- **Pencarian tempat**: kata kunci yang Anda ketik di pencarian formulir laporan diteruskan ke Mapbox lewat server kami, tanpa alamat IP Anda, dan tidak kami simpan.
- **Agen AI**: siapa pun, juga tanpa akun, bisa bertanya kepada Agen AI tentang tempat, rute, dan aksesibilitas di Kota Bogor. Bila Anda masuk, Agen AI juga membantu mencari tempat yang perlu dipetakan. Pertanyaan Anda, beserta paling banyak 6 giliran sebelumnya dalam percakapan itu, dikirim lewat server kami ke Anthropic (Claude API) untuk dijawab. Bersama pertanyaan ikut data tempat yang Anda pilih dan hasil pencarian agen di data AksesBogor, OpenStreetMap, dan basis pengetahuan.
  - Titik awal: lokasi perangkat hanya dibaca setelah kami menjelaskan kegunaannya dan browser meminta izin Anda; Anda juga bisa memilih titik di peta. Titik itu dikirim ke server kami bersama pertanyaan dan saat kartu rute dimuat, dibulatkan sampai sekitar 11 m, dan hanya dipakai untuk mencari tempat terdekat dan, lewat Mapbox, menghitung rute jalan kaki untuk kartu rute.
  - Anthropic tidak menerima titik awal, rute, email, nama, atau alamat IP Anda. Agen hanya menerima jarak garis lurus dari titik awal ke paling banyak 5 tempat, dibulatkan ke 100 m.
  - Untuk membatasi pemakaian, server kami menghitung jumlah pertanyaan per akun, atau per alamat IP bila Anda tidak masuk. Hitungan itu hanya ada di memori server sampai hari berganti.
  - Percakapan disimpan di browser Anda, hanya di tab itu, supaya tidak hilang saat halaman dimuat ulang atau saat Anda kembali dari tautan di percakapan. Percakapan hilang saat tab ditutup atau Anda memilih "Percakapan baru". Bila Anda memakai perangkat bersama, pilih "Percakapan baru" setelah selesai. Titik awal tidak ikut disimpan.
  - Pertanyaan, jawaban, dan titik awal tidak kami simpan di server.
- **Teknis**: alamat IP, alamat halaman yang dibuka, dan waktunya tercatat di log server untuk keamanan dan memperbaiki galat.
- **Di perangkat Anda**: pilihan tampilan (mode terang atau gelap, gerak) disimpan di browser Anda dan tidak dikirim ke kami. Draf laporan yang belum dikirim, termasuk fotonya, juga disimpan di browser Anda dan baru dikirim ke kami saat Anda menekan "Kirim laporan". Cookie sesi hanya dipakai untuk menjaga Anda tetap masuk. Kami tidak memakai cookie iklan atau analitik.

### Untuk apa data dipakai

- Memasukkan Anda ke akun dan menjaga akun tetap aman.
- Menampilkan "Laporan saya" dan mengirim email saat laporan Anda diterbitkan, ditolak beserta alasannya, atau butuh klarifikasi.
- Memeriksa laporan sebelum tayang, menghubungi Anda bila verifikator butuh klarifikasi, dan mencegah spam.
- Menjawab pertanyaan Anda di Agen AI, mencari tempat terdekat dari titik awal yang Anda pilih, dan menghitung rute jalan kaki untuk kartu rute.
- Membatasi jumlah pertanyaan per akun atau per alamat IP supaya layanan tetap bisa dipakai semua orang.
- Mencatat siapa mengubah data tempat dan kapan (log audit), supaya perubahan bisa ditelusuri. Log ini berisi akun verifikator dan perubahan data tempat, tanpa email, nama pelapor, atau isi klarifikasi.
- Menjaga layanan tetap berjalan dan aman.

Dasar pemrosesannya adalah persetujuan yang Anda berikan saat melengkapi akun. Anda bisa menarik persetujuan kapan saja dengan menghapus akun. Bila Anda memakai Agen AI tanpa akun, dasarnya adalah persetujuan Anda saat mengirim pertanyaan setelah membaca pemberitahuan di halaman itu; berhenti bertanya berarti tidak ada data lagi yang dikirim.

### Siapa yang bisa melihat data Anda

- **Verifikator AksesBogor** melihat nama tampilan pelapor, bukan email, beserta isian, foto, dan jawaban klarifikasi saat memeriksanya. Sebelum diverifikasi, laporan dan foto tidak bisa dilihat publik. Sebelum foto terbit, verifikator mengaburkan wajah dan pelat nomor atau menyembunyikan fotonya; hanya hasil yang sudah dikaburkan yang tayang.
- **Admin AksesBogor** melihat nama tampilan dan email anggota tim verifikator untuk mengatur peran mereka.
- **Pengunjung AksesBogor** melihat informasi tempat dan foto yang sudah diterbitkan, tanpa nama pelapor.
- **Penyedia layanan** yang memproses data atas nama kami:
  - Oracle Cloud (menyediakan server di region Indonesia North, Batam, tempat situs web, API, database, dan log server AksesBogor berjalan; tim kami yang mengelola server dan database). Menurut [Kebijakan Privasi Layanan Oracle](https://www.oracle.com/legal/privacy/services-privacy-policy.html), Oracle memproses data di layanannya atas nama kami untuk menjalankan layanan itu.
  - Cloudflare (semua halaman dan permintaan ke API AksesBogor melewati jaringan Cloudflare, lalu diteruskan lewat Cloudflare Tunnel ke server kami. Cloudflare menerima alamat IP Anda dan data tentang permintaan, mis. halaman yang dibuka dan waktunya. Cloudflare juga menyimpan foto laporan di Cloudflare R2). Menurut [Kebijakan Privasi Cloudflare](https://www.cloudflare.com/privacypolicy/) dan [perjanjian pemrosesan datanya](https://www.cloudflare.com/cloudflare-customer-dpa/), Cloudflare memproses data ini atas nama kami dan tidak boleh memakainya untuk pemasaran atau iklan.
  - Resend (mengirim email kode masuk dan pemberitahuan).
  - Google (hanya bila Anda memakai tombol "Masuk dengan Google"; halaman Masuk memuat tombol dari Google).
  - Mapbox (menampilkan peta, mencari tempat, dan menghitung rute jalan kaki untuk kartu rute di Agen AI; menerima alamat IP dan area peta yang dilihat dari browser Anda, termasuk area di sekitar lokasi Anda setelah "Tampilkan lokasi saya", serta kata kunci pencarian dan titik awal rute yang sudah dibulatkan dari server kami).
  - Anthropic (menjawab pertanyaan di Agen AI lewat Claude API; menerima pertanyaan, giliran sebelumnya, data tempat yang dipilih, hasil pencarian agen, dan jarak garis lurus yang dibulatkan ke tempat terdekat, tanpa posisi, email, nama, atau alamat IP Anda, juga bila Anda tidak punya akun). Menurut [Ketentuan Komersial Anthropic](https://www.anthropic.com/legal/commercial-terms), Anthropic tidak boleh memakai data ini untuk melatih model.

Kami tidak menjual data pribadi dan tidak membagikannya untuk iklan.

### Data yang diproses di luar Indonesia

Resend, Google, Mapbox, Cloudflare, dan Anthropic bisa memproses data di luar Indonesia, termasuk di Amerika Serikat. Anthropic menyimpan data di Amerika Serikat dan bisa menjalankan modelnya di negara lain. Situs web, API, database, dan log server kami berjalan di Batam, Indonesia, tetapi menurut kebijakan privasinya, Oracle bisa memproses data di layanannya di negara lain bila diperlukan untuk menjalankan layanan itu. Kami hanya mengirim data yang dibutuhkan layanan itu: email dan isi email ke Resend, lalu lintas situs web dan API lewat Cloudflare, alamat IP, area peta, kata kunci pencarian, dan titik awal rute ke Mapbox, pertanyaan Agen AI ke Anthropic, dan foto laporan ke Cloudflare R2.

### Berapa lama data disimpan

| Data | Masa simpan |
| --- | --- |
| Akun | Sampai Anda menghapusnya |
| Akun yang tidak dilengkapi | 24 jam |
| Kode masuk | 10 menit, atau sampai dipakai |
| Sesi masuk | 30 hari sejak terakhir dipakai, paling lama 90 hari |
| Laporan yang menunggu verifikasi atau klarifikasi | Sampai diputuskan, atau sampai akun dihapus |
| Laporan yang ditolak | Isian dan alasan sampai akun dihapus; fotonya dihapus saat laporan ditolak |
| Foto yang disembunyikan verifikator | Dihapus saat laporan diterbitkan |
| Foto asli sebelum dikaburkan | Dihapus saat laporan diterbitkan; hanya versi yang dikaburkan yang tayang |
| Foto yang terunggah tetapi laporannya tidak jadi terkirim | 24 jam |
| Draf laporan di browser Anda | Sampai terkirim, sampai Anda keluar, atau 7 hari setelah terakhir diubah |
| Pertanyaan dan jawaban Agen AI | Tidak disimpan di server kami; percakapan ada di tab browser Anda sampai tab ditutup atau Anda memilih "Percakapan baru". Anthropic menghapusnya paling lama 30 hari setelah diterima, kecuali ditandai melanggar kebijakan penggunaannya (sampai 2 tahun) atau diwajibkan hukum |
| Titik awal dan rute jalan kaki | Tidak disimpan |
| Jumlah pertanyaan Agen AI per akun atau alamat IP | Di memori server sampai hari berganti (WIB) |
| Lokasi perangkat untuk Tampilkan lokasi saya di Peta | Tidak dikirim ke kami dan tidak disimpan |
| Laporan yang sudah terbit | Tetap tayang tanpa nama pelapor setelah akun dihapus |
| Log audit perubahan data tempat | Tanpa batas waktu; akun verifikator yang dihapus ditulis "verifikator dihapus" |
| Cadangan database | Paling lama 30 hari |
| Log server | 30 hari |

### Hak Anda

- **Melihat data**: nama tampilan, email, dan laporan Anda ada di halaman Akun Saya.
- **Memperbaiki data**: ubah nama tampilan di Akun Saya. Untuk mengganti email, buat akun baru dengan email itu.
- **Menghapus akun**: pilih "Hapus akun" di Akun Saya. Penghapusan langsung berjalan dan tidak bisa dibatalkan. Nama, email, waktu persetujuan, semua sesi, dan laporan yang belum terbit beserta fotonya dihapus. Informasi tempat dan foto yang sudah terbit tetap tayang tanpa nama Anda. Kami mengirim satu email terakhir sebagai konfirmasi. Salinan di cadangan database hilang paling lama 30 hari kemudian.
- **Bertanya atau mengadu**: hubungi kami lewat kontak di bawah. Kami menjawab paling lambat 3 × 24 jam.

### Keamanan

Semua koneksi memakai HTTPS. Kode masuk disimpan dalam bentuk hash dan sesi memakai token bertanda tangan. Hanya anggota tim yang mengelola server yang bisa membuka database. Bila terjadi kebocoran data pribadi, kami memberi tahu Anda paling lambat 3 × 24 jam setelah mengetahuinya.

### Anak

Bila Anda berusia di bawah 18 tahun, minta orang tua atau wali Anda membaca kebijakan ini dan menyetujuinya bersama Anda sebelum membuat akun atau bertanya kepada Agen AI.

### Perubahan kebijakan

Bila kebijakan ini berubah, kami menaikkan nomor versinya dan meminta persetujuan Anda lagi saat Anda masuk berikutnya. Bila Anda memakai Agen AI tanpa akun, versi yang berlaku adalah versi yang tertaut di halaman itu saat Anda bertanya.

### Kontak

Email: (diisi tim sebelum rilis).

## Referensi

- UU No. 27 Tahun 2022 tentang Pelindungan Data Pribadi
- [PRD](prd.md), FR-11, FR-13, FR-20 sampai FR-27, FR-50, dan kebutuhan non-fungsional Privasi dan keamanan
- [Syarat Layanan](syarat-layanan.md)
- Oracle: [Kebijakan Privasi Layanan Oracle](https://www.oracle.com/legal/privacy/services-privacy-policy.html), [daftar region OCI](https://docs.oracle.com/en-us/iaas/Content/General/Concepts/regions.htm) (Indonesia North, Batam, `ap-batam-1`)
- Cloudflare: [Kebijakan Privasi](https://www.cloudflare.com/privacypolicy/), [perjanjian pemrosesan data (DPA)](https://www.cloudflare.com/cloudflare-customer-dpa/), [Cloudflare Tunnel](https://developers.cloudflare.com/cloudflare-one/networks/connectors/cloudflare-tunnel/)
- Anthropic: [Ketentuan Komersial](https://www.anthropic.com/legal/commercial-terms), [lama penyimpanan data organisasi](https://privacy.claude.com/en/articles/7996866-how-long-do-you-store-my-organization-s-data), [data residency](https://platform.claude.com/docs/en/manage-claude/data-residency)
- [ADR-0011](../decisions/0011-pelapor-wajib-login.md), [ADR-0014](../decisions/0014-login-google-dan-kode-email.md), [ADR-0024](../decisions/0024-data-setelah-akun-dihapus.md), [ADR-0026](../decisions/0026-api-dan-postgresql-di-evergarden-oci.md), [ADR-0029](../decisions/0029-akun-better-auth-dan-sesi-lewat-server-web.md), [ADR-0030](../decisions/0030-email-transaksional-resend.md), [ADR-0031](../decisions/0031-foto-laporan-di-bucket-r2-privat.md), [ADR-0032](../decisions/0032-pencarian-tempat-lewat-api-mapbox-search-box.md), [ADR-0033](../decisions/0033-isi-dulu-masuk-saat-kirim-dan-draf-di-perangkat.md), [ADR-0040](../decisions/0040-syarat-layanan-lisensi-laporan-dan-persetujuan.md), [ADR-0042](../decisions/0042-agen-rute-jalan-kaki-dan-informasi-aksesibilitas.md), [ADR-0043](../decisions/0043-agen-untuk-kontributor-dengan-model-gratis-openrouter.md), [ADR-0047](../decisions/0047-model-agen-kontributor-claude-haiku-lewat-claude-api.md), [ADR-0048](../decisions/0048-web-di-host-sendiri-di-evergarden-oci.md), [ADR-0050](../decisions/0050-asisten-rute-publik.md)
