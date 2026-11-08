# Kebijakan Privasi AksesBogor (Draf)

| | |
| --- | --- |
| Status | Draf, belum ditinjau |
| Versi | 2026-11-09 |
| Pemilik | Tim AksesBogor |
| Issue | #51, #55, #59 |

Dokumen ini adalah sumber teks halaman `/kebijakan-privasi` di akses-bogor-web. Pengguna menyetujui versi yang tertulis di atas saat Lengkapi Akun (FR-11), dan versi itu disimpan bersama waktu persetujuannya ([ADR-0029](../decisions/0029-akun-better-auth-dan-sesi-lewat-server-web.md)). Setiap perubahan isi menaikkan versi, dan pengguna diminta menyetujui ulang saat masuk berikutnya.

## Sebelum Rilis

Bagian ini untuk tim dan tidak tampil di web.

- [ ] Isi identitas pengendali data (nama tim dan kampus) dan alamat email kontak resmi. `info@aksesbogor.id` di Figma masih placeholder.
- [ ] Tetapkan masa simpan log server di Evergarden. Saat ini journald hanya dibatasi ukuran, belum waktu; usulan 30 hari.
- [ ] Minta dosen pembimbing atau unit hukum kampus meninjau teks ini, terutama dasar pemrosesan, transfer ke luar negeri, batas waktu 3 × 24 jam, dan bagian anak.
- [x] Tambahkan rincian foto dan lokasi laporan saat Laporkan (FR-20 sampai FR-27) dibangun.
- [ ] Ubah status menjadi final, hapus kata "Draf" di judul halaman web, dan naikkan versi bila isinya berubah.

## Teks Kebijakan

### Siapa kami

AksesBogor adalah web yang mencatat aksesibilitas fasilitas publik di Kota Bogor bagi penyandang disabilitas dan pendampingnya. AksesBogor dikelola Tim AksesBogor sebagai proyek mahasiswa. Tim AksesBogor adalah pengendali data pribadi Anda sesuai UU No. 27 Tahun 2022 tentang Pelindungan Data Pribadi.

### Data yang kami kumpulkan

Anda bisa menjelajahi AksesBogor tanpa akun. Kami hanya mengumpulkan data pribadi bila Anda membuat akun atau melapor.

- **Akun**: alamat email, nama tampilan, peran (pengguna, verifikator, atau admin), serta waktu dan versi Kebijakan Privasi yang Anda setujui.
- **Masuk**: kode 6 digit yang kami kirim ke email Anda, disimpan dalam bentuk acak (hash) selama 10 menit; sesi masuk (token, waktu dibuat, dan waktu terakhir dipakai). Bila Anda masuk dengan Google, kami menyimpan nomor akun Google Anda untuk menyambungkannya ke akun AksesBogor. Kami tidak menyimpan foto profil, kontak, atau token akses Google.
- **Laporan**: isian laporan (nama tempat, kategori, patokan, jawaban fasilitas, catatan, dan deskripsi), titik yang Anda konfirmasi di peta, dan foto yang Anda kirim. Lokasi perangkat hanya dibaca saat Anda menekan "Gunakan lokasi saya" dan tidak disimpan. Foto diperkecil lalu disimpan ulang tanpa metadata (EXIF), termasuk lokasi GPS. Bila verifikator meminta klarifikasi, jawaban Anda dan foto tambahannya ikut disimpan sebagai bagian laporan.
- **Pencarian tempat**: kata kunci yang Anda ketik di pencarian formulir laporan diteruskan ke Mapbox lewat server kami, tanpa alamat IP Anda, dan tidak kami simpan.
- **Teknis**: alamat IP, alamat halaman yang dibuka, dan waktunya tercatat di log server untuk keamanan dan memperbaiki galat.
- **Di perangkat Anda**: pilihan tampilan (mode terang atau gelap, gerak) disimpan di browser Anda dan tidak dikirim ke kami. Draf laporan yang belum dikirim, termasuk fotonya, juga disimpan di browser Anda dan baru dikirim ke kami saat Anda menekan "Kirim laporan". Cookie sesi hanya dipakai untuk menjaga Anda tetap masuk. Kami tidak memakai cookie iklan atau analitik.

### Untuk apa data dipakai

- Memasukkan Anda ke akun dan menjaga akun tetap aman.
- Menampilkan "Laporan saya" dan mengirim email saat laporan Anda diterbitkan, ditolak beserta alasannya, atau butuh klarifikasi.
- Memeriksa laporan sebelum tayang, menghubungi Anda bila verifikator butuh klarifikasi, dan mencegah spam.
- Mencatat siapa mengubah data tempat dan kapan (log audit), supaya perubahan bisa ditelusuri. Log ini berisi akun verifikator dan perubahan data tempat, tanpa email, nama pelapor, atau isi klarifikasi.
- Menjaga layanan tetap berjalan dan aman.

Dasar pemrosesannya adalah persetujuan yang Anda berikan saat melengkapi akun. Anda bisa menarik persetujuan kapan saja dengan menghapus akun.

### Siapa yang bisa melihat data Anda

- **Verifikator AksesBogor** melihat nama tampilan pelapor, bukan email, beserta isian, foto, dan jawaban klarifikasi saat memeriksanya. Sebelum diverifikasi, laporan dan foto tidak bisa dilihat publik. Sebelum foto terbit, verifikator mengaburkan wajah dan pelat nomor atau menyembunyikan fotonya; hanya hasil yang sudah dikaburkan yang tayang.
- **Admin AksesBogor** melihat nama tampilan dan email anggota tim verifikator untuk mengatur peran mereka.
- **Pengunjung AksesBogor** melihat informasi tempat dan foto yang sudah diterbitkan, tanpa nama pelapor.
- **Penyedia layanan** yang memproses data atas nama kami:
  - Oracle Cloud (server dan database di Batam, Indonesia).
  - Cloudflare (jaringan menuju server, dan penyimpanan foto di Cloudflare R2).
  - Vercel (menjalankan situs web).
  - Resend (mengirim email kode masuk dan pemberitahuan).
  - Google (hanya bila Anda memakai tombol "Masuk dengan Google"; halaman Masuk memuat tombol dari Google).
  - Mapbox (menampilkan peta dan mencari tempat; menerima alamat IP dan area peta yang dilihat dari browser Anda, serta kata kunci pencarian dari server kami).

Kami tidak menjual data pribadi dan tidak membagikannya untuk iklan.

### Data yang diproses di luar Indonesia

Vercel, Resend, Google, Mapbox, dan Cloudflare bisa memproses data di luar Indonesia, termasuk di Amerika Serikat. Kami hanya mengirim data yang dibutuhkan layanan itu: email dan isi email ke Resend, alamat IP ke penyedia jaringan dan peta, kata kunci pencarian ke Mapbox, dan foto laporan ke Cloudflare R2.

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
| Laporan yang sudah terbit | Tetap tayang tanpa nama pelapor setelah akun dihapus |
| Log audit perubahan data tempat | Tanpa batas waktu; akun verifikator yang dihapus ditulis "verifikator dihapus" |
| Cadangan database | Paling lama 30 hari |
| Log server | Belum ditetapkan |

### Hak Anda

- **Melihat data**: nama tampilan, email, dan laporan Anda ada di halaman Akun Saya.
- **Memperbaiki data**: ubah nama tampilan di Akun Saya. Untuk mengganti email, buat akun baru dengan email itu.
- **Menghapus akun**: pilih "Hapus akun" di Akun Saya. Penghapusan langsung berjalan dan tidak bisa dibatalkan. Nama, email, waktu persetujuan, semua sesi, dan laporan yang belum terbit beserta fotonya dihapus. Informasi tempat dan foto yang sudah terbit tetap tayang tanpa nama Anda. Kami mengirim satu email terakhir sebagai konfirmasi. Salinan di cadangan database hilang paling lama 30 hari kemudian.
- **Bertanya atau mengadu**: hubungi kami lewat kontak di bawah. Kami menjawab paling lambat 3 × 24 jam.

### Keamanan

Semua koneksi memakai HTTPS. Kode masuk disimpan dalam bentuk hash dan sesi memakai token bertanda tangan. Hanya anggota tim yang mengelola server yang bisa membuka database. Bila terjadi kebocoran data pribadi, kami memberi tahu Anda paling lambat 3 × 24 jam setelah mengetahuinya.

### Anak

Bila Anda berusia di bawah 18 tahun, minta orang tua atau wali Anda membaca kebijakan ini dan menyetujuinya bersama Anda sebelum membuat akun.

### Perubahan kebijakan

Bila kebijakan ini berubah, kami menaikkan nomor versinya dan meminta persetujuan Anda lagi saat Anda masuk berikutnya.

### Kontak

Email: (diisi tim sebelum rilis).

## Referensi

- UU No. 27 Tahun 2022 tentang Pelindungan Data Pribadi
- [PRD](prd.md), FR-11, FR-13, FR-20 sampai FR-27, FR-50, dan kebutuhan non-fungsional Privasi dan keamanan
- [ADR-0011](../decisions/0011-pelapor-wajib-login.md), [ADR-0014](../decisions/0014-login-google-dan-kode-email.md), [ADR-0024](../decisions/0024-data-setelah-akun-dihapus.md), [ADR-0026](../decisions/0026-api-dan-postgresql-di-evergarden-oci.md), [ADR-0029](../decisions/0029-akun-better-auth-dan-sesi-lewat-server-web.md), [ADR-0030](../decisions/0030-email-transaksional-resend.md), [ADR-0031](../decisions/0031-foto-laporan-di-bucket-r2-privat.md), [ADR-0032](../decisions/0032-pencarian-tempat-lewat-api-mapbox-search-box.md), [ADR-0033](../decisions/0033-isi-dulu-masuk-saat-kirim-dan-draf-di-perangkat.md)
