# Kebijakan Privasi AksesBogor (Draf)

| | |
| --- | --- |
| Status | Draf, belum ditinjau |
| Versi | 2026-11-27 |
| Pemilik | Tim AksesBogor |
| Issue | #51, #55, #59, #62, #89, #99, #106, #124, #130, #150, #159 |

Dokumen ini adalah sumber teks halaman `/kebijakan-privasi` di akses-bogor-web. Pengguna menyetujui versi yang tertulis di atas saat Lengkapi Akun (FR-11), dan versi itu disimpan bersama waktu persetujuannya ([ADR-0029](../decisions/0029-akun-better-auth-dan-sesi-lewat-server-web.md)). Setiap perubahan isi menaikkan versi, dan pengguna diminta menyetujui ulang saat masuk berikutnya.

## Sebelum Rilis

Bagian ini untuk tim dan tidak tampil di web.

- [x] Isi identitas pengendali data (nama tim dan kampus) dan alamat email kontak resmi. Diisi di versi 2026-11-27, bersama anggota tim, penanggung jawab pelindungan data pribadi, dan alamat surat. `info@aksesbogor.id` di Figma masih placeholder dan bukan alamat kontak.
- [x] Tetapkan masa simpan log server: 30 hari, diputuskan 2026-11-08 ([ADR-0040](../decisions/0040-syarat-layanan-lisensi-laporan-dan-persetujuan.md)). journald diatur saat rilis pertama, waktu itu di Evergarden.
- [ ] Pastikan journald di server tim di Karawang (fatir-mitya), tempat produksi berjalan sejak 2026-11-26, juga menyimpan log server paling lama 30 hari.
- [ ] Minta dosen pembimbing atau unit hukum kampus meninjau teks ini bersama [Syarat Layanan](syarat-layanan.md), terutama dasar pemrosesan, transfer ke luar negeri, batas waktu 3 × 24 jam, dan bagian anak. Bawa juga [pemeriksaan UU PDP 2026-11-27](pemeriksaan-uu-pdp-2026-11-27.md) beserta catatan untuk peninjaunya, termasuk apakah kampus ikut menjadi pengendali bersama (Pasal 18) karena namanya kini disebut.
- [x] Tambahkan rincian foto dan lokasi laporan saat Laporkan (FR-20 sampai FR-27) dibangun.
- [ ] Ubah status menjadi final, hapus kata "Draf" di judul halaman web, dan naikkan versi bila isinya berubah.
- [x] Rilis versi 2026-11-22 (agen Bantu memetakan dengan Anthropic, [ADR-0047](../decisions/0047-model-agen-kontributor-claude-haiku-lewat-claude-api.md)) di API dan web bersamaan: `PRIVACY_POLICY_VERSION` di keduanya harus sama, karena API menolak persetujuan untuk versi lain.
- [x] Rilis versi 2026-11-23 (web di Evergarden, [ADR-0048](../decisions/0048-web-di-host-sendiri-di-evergarden-oci.md), dan Lokasi saya di Peta): web dulu, lalu `PRIVACY_POLICY_VERSION` di API beberapa menit kemudian. Bila API lebih dulu, semua akun diminta menyetujui ulang di web lama, yang masih mengirim versi 2026-11-22 dan ditolak dengan 409.
- [x] Rilis versi 2026-11-24 (asisten Tanya rute untuk semua pengunjung, [ADR-0050](../decisions/0050-asisten-rute-publik.md)): web dulu, lalu `PRIVACY_POLICY_VERSION` di API beberapa menit kemudian, lalu `ROUTE_ASSISTANT_ENABLED=true` di API. Versi 2026-11-23 sudah dipakai, jadi versi ini bertanggal 2026-11-24 walaupun dirilis lebih awal.
- [x] Rilis versi 2026-11-26 (Agen AI menggantikan Bantu memetakan dan Tanya rute, [ADR-0054](../decisions/0054-agen-ai-satu-pintu.md)): web dulu, lalu `PRIVACY_POLICY_VERSION` di API beberapa menit kemudian.
- [x] Rute dari data OpenStreetMap ([ADR-0055](../decisions/0055-rute-pejalan-kaki-aksesibel-dengan-valhalla.md)) ikut versi 2026-11-26 yang sama, karena versi itu belum dirilis. Bila 2026-11-26 sudah dirilis sebelum teks ini masuk, naikkan versinya ke 2026-11-27 di dokumen ini, web, dan API. `ACCESSIBLE_ROUTER_URL` di API baru diisi setelah web dengan teks ini dirilis.
- [x] Rilis versi 2026-11-27 ([pemeriksaan UU PDP](pemeriksaan-uu-pdp-2026-11-27.md)) bersama Syarat Layanan 2026-11-27: web dulu, lalu `PRIVACY_POLICY_VERSION` di API beberapa menit kemudian. Dirilis 2026-11-27: `release` web pukul 19.54 WIB, lalu `release` API pukul 19.57 WIB.
- [x] Pada hari rilis versi 2026-11-27, kirim email penjelasan ke semua pemilik akun, karena pemindahan server dan cadangan R2 sudah berlaku sebelum diberitahukan (Pasal 21 ayat (2)). Dikirim 2026-11-27 ke 6 pemilik akun.
- [ ] Siapkan orang dan skrip untuk janji 3 × 24 jam di bagian Hak Anda: ekspor JSON per akun, ganti email, tahan laporan, dan matikan email pemberitahuan per akun. Versi 2026-11-27 sudah dirilis, jadi janji ini sudah berlaku. Runbook `operations/permintaan-subjek-data.md` ada di akses-bogor-docs#167 dan skripnya di akses-bogor-api#190 sampai #195; centang setelah PR API itu dirilis.
- [ ] Pastikan alamat surat di bagian Kontak ke Program Studi Teknologi Rekayasa Perangkat Lunak. Situs sv.ipb.ac.id menulis dua alamat: Jl. Kumbang No. 14 di footer, dan Jl. Raya Pajajaran di halaman kontak. Bila alamat yang benar bukan yang tertulis sekarang, perbarui bagian Kontak di Kebijakan Privasi dan Syarat Layanan, di docs dan web, dengan kenaikan versi.
- [ ] Selesaikan tindakan bertanda wajib di [penilaian dampak PDP](penilaian-dampak-pdp.md) sebelum AksesBogor diumumkan ke publik: foto unggahan admin lewat Kelola tempat ikut dikaburkan sebelum tayang (sekarang tidak, padahal bagian "Siapa yang bisa melihat data Anda" menyebut hanya foto yang sudah dikaburkan yang tayang), enkripsi dump cadangan, dan penghapusan database lama di Evergarden.

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
| 2026-11-26 | Bantu memetakan dan Tanya rute menjadi satu Agen AI; percakapan disimpan di tab browser sampai tab ditutup; rute kartu rute dihitung di server kami dengan data OpenStreetMap, dan Mapbox hanya bila rute itu belum tersedia | #124, #130 |
| 2026-11-27 | Server pindah dari Oracle Cloud ke server tim di Karawang; cadangan harian database di Cloudflare R2; dasar pemrosesan per jenis data; hak subjek data lengkap dengan tenggat 3 × 24 jam; pemberitahuan kegagalan pelindungan ke lembaga; dasar transfer ke luar negeri; persetujuan orang tua atau wali; penyandang disabilitas dan pendamping; identitas pengendali, penanggung jawab, dan kontak. Syarat Layanan ikut naik ke 2026-11-27 | #150 |

Label kotak persetujuan di Lengkapi Akun berubah di versi 2026-11-27. Versi yang disimpan bersama persetujuan menunjukkan label mana yang dicentang:

- Sampai 2026-11-26: "Saya sudah membaca dan menyetujui Kebijakan Privasi AksesBogor."
- Mulai 2026-11-27: "Saya sudah membaca dan menyetujui Kebijakan Privasi AksesBogor, termasuk pengiriman data ke penyedia layanan di luar Indonesia. Bila saya berusia di bawah 18 tahun, orang tua atau wali saya ikut menyetujuinya."

## Teks Kebijakan

### Siapa kami

AksesBogor adalah web yang mencatat aksesibilitas fasilitas publik di Kota Bogor bagi penyandang disabilitas dan pendampingnya. AksesBogor dikelola Tim AksesBogor, kelompok mahasiswa Program Studi Teknologi Rekayasa Perangkat Lunak, Sekolah Vokasi IPB University, sebagai proyek mahasiswa. Tim AksesBogor adalah pengendali data pribadi Anda sesuai UU No. 27 Tahun 2022 tentang Pelindungan Data Pribadi (UU PDP): kami yang menentukan data apa yang dikumpulkan, untuk apa, dan siapa yang ikut memprosesnya.

Anggota Tim AksesBogor adalah Aldio Lisafron, Arief Adhitya Ramadhan, Badzlan Nur Dhabith, Muhammad Rifal Dzulfikar, dan Rizky Ramadhan Agmer. Penanggung jawab pelindungan data pribadi di Tim AksesBogor adalah Aldio Lisafron, anggota tim yang mengelola server dan menjadi admin AksesBogor. Semua pertanyaan dan permintaan tentang data pribadi Anda ditangani lewat kontak di bagian Kontak.

### Data yang kami kumpulkan

Anda bisa menjelajahi AksesBogor tanpa akun. Setiap kali Anda membuka halaman, alamat IP Anda tercatat di log server dan diterima Cloudflare (lihat Teknis di bawah). Data pribadi lain hanya kami kumpulkan bila Anda membuat akun, melapor, atau bertanya kepada Agen AI.

- **Akun**: alamat email, nama tampilan, peran (pengguna, verifikator, atau admin), serta waktu dan versi Kebijakan Privasi yang Anda setujui.
- **Masuk**: kode 6 digit yang kami kirim ke email Anda, disimpan dalam bentuk acak (hash) selama 10 menit; sesi masuk (token, waktu dibuat, dan waktu terakhir dipakai). Bila Anda masuk dengan Google, kami menyimpan nomor akun Google Anda untuk menyambungkannya ke akun AksesBogor. Nama akun Google hanya dipakai sebagai usulan nama tampilan di Lengkapi Akun. Kami tidak menyimpan foto profil, kontak, atau token akses Google.
- **Laporan**: isian laporan (nama tempat, kategori, patokan, jawaban fasilitas, catatan, dan deskripsi), titik yang Anda konfirmasi di peta, dan foto yang Anda kirim. Lokasi perangkat hanya dibaca saat Anda menekan "Gunakan lokasi saya" di formulir laporan dan tidak disimpan. Foto diperkecil lalu disimpan ulang tanpa metadata (EXIF), termasuk lokasi GPS. Bila verifikator meminta klarifikasi, jawaban Anda dan foto tambahannya ikut disimpan sebagai bagian laporan.
- **Lokasi saya di Peta**: lokasi perangkat hanya dibaca setelah Anda menekan "Tampilkan lokasi saya". Bila izin lokasi belum diberikan, kami menjelaskan dulu kegunaannya, lalu browser meminta izin Anda. Lokasi itu hanya dipakai di browser Anda untuk menggeser peta dan menaruh titik di posisi Anda. Lokasi tidak dikirim ke server AksesBogor atau ke Anthropic, dan tidak disimpan. Bila Anda berada di luar wilayah peta Kota Bogor, peta tidak digeser. Seperti setiap tampilan peta, Mapbox menerima area peta yang dimuat, jadi setelah peta digeser, Mapbox menerima area di sekitar lokasi Anda.
- **Pencarian tempat**: kata kunci yang Anda ketik di pencarian formulir laporan diteruskan ke Mapbox lewat server kami, tanpa alamat IP Anda, dan tidak kami simpan.
- **Agen AI**: siapa pun, juga tanpa akun, bisa bertanya kepada Agen AI tentang tempat, rute, dan aksesibilitas di Kota Bogor. Bila Anda masuk, Agen AI juga membantu mencari tempat yang perlu dipetakan. Pertanyaan Anda, beserta paling banyak 6 giliran sebelumnya dalam percakapan itu, dikirim lewat server kami ke Anthropic (Claude API) untuk dijawab. Bersama pertanyaan ikut data tempat yang Anda pilih dan hasil pencarian agen di data AksesBogor, OpenStreetMap, dan basis pengetahuan. Pertanyaan Anda bisa memuat keterangan tentang kebutuhan Anda, mis. bahwa Anda memakai kursi roda. Keterangan seperti itu bisa termasuk data kesehatan, jadi tulis seperlunya dan jangan sertakan nama atau kontak.
  - Titik awal: lokasi perangkat hanya dibaca setelah kami menjelaskan kegunaannya dan browser meminta izin Anda; Anda juga bisa memilih titik di peta. Titik itu dikirim ke server kami bersama pertanyaan dan saat kartu rute dimuat, dibulatkan sampai sekitar 11 m, dan hanya dipakai untuk mencari tempat terdekat dan menghitung rute untuk kartu rute. Rute dihitung di server kami sendiri dengan data OpenStreetMap; bila rute itu belum tersedia, rute jalan kaki dihitung lewat Mapbox.
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

Dasar pemrosesan data akun dan laporan adalah persetujuan yang Anda berikan saat melengkapi akun (Pasal 20 ayat (2) huruf a UU PDP). Kami menyimpan waktu dan versi persetujuan itu sebagai buktinya. Anda bisa menarik persetujuan kapan saja dengan menghapus akun di Akun Saya, atau meminta kami menghapusnya lewat kontak di bawah; permintaan lewat kontak kami jalankan paling lambat 3 × 24 jam.

Sebelum Anda melengkapi akun, email yang Anda ketik di halaman Masuk, atau nomor akun, email, dan nama dari Google bila Anda masuk dengan Google, hanya dipakai untuk mengirim kode dan memasukkan Anda, karena Anda memintanya (Pasal 20 ayat (2) huruf b). Bila akun tidak dilengkapi, data itu dihapus dalam 24 jam.

Bila Anda bertanya kepada Agen AI tanpa akun, pertanyaan Anda diproses untuk menjawabnya, karena Anda memintanya saat memakai AksesBogor sesuai Syarat Layanan (Pasal 20 ayat (2) huruf b). Berhenti bertanya berarti tidak ada data lagi yang dikirim.

Alamat IP dan data permintaan di log server, lalu lintas yang melewati Cloudflare, alamat IP yang diterima Mapbox saat peta dimuat, hitungan pertanyaan per alamat IP, dan log audit perubahan data tempat diproses berdasarkan kepentingan yang sah (Pasal 20 ayat (2) huruf f): menjaga layanan tetap aman dan bisa dipakai semua orang, serta menelusuri siapa mengubah data tempat. Ini juga berlaku bila Anda tidak punya akun. Kami hanya memproses data ini seperti yang tertulis di kebijakan ini dan selama masa simpan di bawah.

### Siapa yang bisa melihat data Anda

- **Verifikator AksesBogor** melihat nama tampilan pelapor, bukan email, beserta isian, foto, dan jawaban klarifikasi saat memeriksanya. Sebelum diverifikasi, laporan dan foto tidak bisa dilihat publik. Sebelum foto terbit, verifikator mengaburkan wajah dan pelat nomor atau menyembunyikan fotonya; hanya hasil yang sudah dikaburkan yang tayang. Sebagian foto pintu masuk diambil tim dari Google Street View dan diberi keterangan sumbernya; Google sudah mengaburkan wajah dan pelat nomor di gambar itu. Bila Anda melihat wajah atau pelat nomor yang masih jelas di foto mana pun, beri tahu kami, dan kami mengaburkan atau menghapus foto itu.
- **Admin AksesBogor** melihat nama tampilan dan email anggota tim verifikator untuk mengatur peran mereka.
- **Pengunjung AksesBogor** melihat informasi tempat dan foto yang sudah diterbitkan, tanpa nama pelapor.
- **Penyedia layanan** berikut menerima data Anda. Kecuali Google, mereka memprosesnya atas nama kami:
  - **Server AksesBogor**: situs web, API, database, cadangan harian database, log server, dan penghitung rute pejalan kaki berjalan di server milik tim kami di Karawang, Jawa Barat, Indonesia. Tim kami yang mengelola server dan database.
  - **Cloudflare**: semua halaman dan permintaan ke API AksesBogor melewati jaringan Cloudflare, lalu diteruskan lewat Cloudflare Tunnel ke server kami. Cloudflare menerima alamat IP Anda dan data tentang permintaan, mis. halaman yang dibuka dan waktunya. Cloudflare juga menyimpan foto laporan dan cadangan harian database di Cloudflare R2. Cadangan itu berisi seluruh isi database, termasuk email, nama tampilan, dan laporan, disimpan di bucket privat yang terpisah dari foto, dan dihapus otomatis setelah 30 hari. Menurut [Kebijakan Privasi Cloudflare](https://www.cloudflare.com/privacypolicy/) dan [perjanjian pemrosesan datanya](https://www.cloudflare.com/cloudflare-customer-dpa/), Cloudflare memproses data ini atas nama kami dan tidak boleh memakainya untuk pemasaran atau iklan.
  - **Resend**: mengirim email kode masuk dan pemberitahuan. Resend menerima alamat email Anda dan isi email itu, lalu mengirimnya dari region Tokyo, Jepang.
  - **Google**: halaman Masuk memuat tombol dari Google, jadi Google menerima alamat IP Anda saat halaman itu dibuka. Bila Anda memakai "Masuk dengan Google", Google memproses data masuk Anda menurut [Kebijakan Privasi Google](https://policies.google.com/privacy) sendiri, bukan atas nama kami, lalu mengirimi kami nomor akun, email, dan nama akun Google Anda.
  - **Mapbox**: menampilkan peta, mencari tempat, dan menghitung rute jalan kaki untuk kartu rute di Agen AI bila rute dari data OpenStreetMap di server kami belum tersedia. Mapbox menerima alamat IP dan area peta yang dilihat dari browser Anda, termasuk area di sekitar lokasi Anda setelah "Tampilkan lokasi saya", serta kata kunci pencarian dan, untuk rute yang dihitung Mapbox, titik awal rute yang sudah dibulatkan dari server kami.
  - **Anthropic**: menjawab pertanyaan di Agen AI lewat Claude API. Anthropic menerima pertanyaan, giliran sebelumnya, data tempat yang dipilih, hasil pencarian agen, dan jarak garis lurus yang dibulatkan ke tempat terdekat, tanpa posisi, email, nama, atau alamat IP Anda, juga bila Anda tidak punya akun. Menurut [Ketentuan Komersial Anthropic](https://www.anthropic.com/legal/commercial-terms), Anthropic tidak boleh memakai data ini untuk melatih model.

Kami tidak menjual data pribadi dan tidak membagikannya untuk iklan.

### Data yang diproses di luar Indonesia

Situs web, API, database, dan log server kami berjalan di server di Indonesia. Resend, Google, Mapbox, Cloudflare, dan Anthropic bisa memproses data di luar Indonesia, termasuk di Amerika Serikat. Resend mengirim email kami dari Tokyo, Jepang. Anthropic menyimpan data di Amerika Serikat dan bisa menjalankan modelnya di negara lain. Foto laporan dan cadangan database di Cloudflare R2 disimpan di lokasi Asia-Pasifik (APAC) Cloudflare.

Kami hanya mengirim data yang dibutuhkan layanan itu: email dan isi email ke Resend, lalu lintas situs web dan API lewat Cloudflare, alamat IP, area peta, kata kunci pencarian, dan titik awal rute yang dihitung Mapbox ke Mapbox, pertanyaan Agen AI ke Anthropic, serta foto laporan dan cadangan harian database ke Cloudflare R2.

UU PDP mewajibkan kami memastikan data yang dikirim ke luar Indonesia tetap terlindungi setara dengan UU PDP. [Anthropic](https://www.anthropic.com/legal/data-processing-addendum), [Resend](https://resend.com/legal/dpa), dan [Mapbox](https://www.mapbox.com/legal/dpa) terikat perjanjian pemrosesan data yang mewajibkan mereka melindungi data yang mereka proses atas nama kami. Untuk penyedia lain, atau bila pelindungan itu belum memadai, dasar pengiriman ini adalah persetujuan yang Anda berikan saat melengkapi akun.

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
| Cadangan database | Tujuh salinan harian terakhir di server kami; salinan di Cloudflare R2 dihapus otomatis setelah 30 hari |
| Log server | 30 hari |
| Email yang dikirim lewat Resend | 30 hari di Resend, termasuk isi email dan log pengirimannya |
| Log permintaan di Cloudflare | Menurut kebijakan penyedia, yaitu Kebijakan Privasi Cloudflare |

### Hak Anda

- **Mendapat informasi**: kebijakan ini menjelaskan siapa kami, data apa yang kami proses, untuk apa, dasar pemrosesannya, siapa yang menerimanya, di mana diproses, dan berapa lama disimpan.
- **Melihat dan meminta salinan data**: nama tampilan, email, dan laporan Anda ada di halaman Akun Saya. Untuk salinan lengkap, termasuk waktu dan versi persetujuan, sesi masuk, jawaban klarifikasi, dan catatan kapan data Anda diproses, kirim permintaan ke kontak di bawah. Kami mengirimkannya paling lambat 3 × 24 jam dalam berkas yang bisa dibaca komputer (JSON), yang juga bisa Anda bawa ke layanan lain.
- **Memperbaiki data**: ubah nama tampilan di Akun Saya. Untuk mengganti email atau memperbaiki data lain, hubungi kami. Kami memperbaikinya paling lambat 3 × 24 jam dan memberi tahu Anda hasilnya.
- **Menghapus akun**: pilih "Hapus akun" di Akun Saya. Penghapusan langsung berjalan dan tidak bisa dibatalkan. Nama, email, waktu persetujuan, semua sesi, dan laporan yang belum terbit beserta fotonya dihapus. Informasi tempat dan foto yang sudah terbit tetap tayang tanpa nama Anda. Kami mengirim satu email terakhir sebagai konfirmasi. Salinan di cadangan database hilang paling lama 30 hari kemudian.
- **Menarik persetujuan**: hapus akun di Akun Saya, atau minta kami menghapusnya lewat kontak di bawah. Permintaan lewat kontak kami jalankan paling lambat 3 × 24 jam.
- **Menunda atau membatasi pemrosesan**: mis. meminta kami menahan laporan Anda yang belum diperiksa, atau berhenti mengirim email pemberitahuan laporan. Kami melakukannya paling lambat 3 × 24 jam dan memberi tahu Anda, kecuali peraturan perundang-undangan tidak memungkinkannya atau penundaan itu membahayakan keselamatan orang lain.
- **Keputusan otomatis**: kami tidak mengambil keputusan tentang Anda yang hanya didasarkan pada pemrosesan otomatis. Status tempat dihitung otomatis dari jawaban fasilitas, tetapi itu penilaian tentang tempat, bukan tentang Anda. Bila Anda merasa terdampak keputusan otomatis, ajukan keberatan lewat kontak di bawah.
- **Bertanya atau mengadu**: hubungi kami lewat kontak di bawah. Kami menjawab paling lambat 3 × 24 jam.
- **Mengadu ke lembaga atau menggugat**: bila jawaban kami tidak memuaskan, Anda bisa mengadu ke lembaga penyelenggara pelindungan data pribadi yang ditetapkan Presiden, menyelesaikan sengketa lewat arbitrase, pengadilan, atau lembaga penyelesaian sengketa lainnya, dan menggugat serta meminta ganti rugi atas pelanggaran pemrosesan data Anda sesuai peraturan perundang-undangan.

Setiap permintaan kami catat. Kami boleh menolak permintaan yang akan mengungkap data pribadi orang lain atau membahayakan keselamatan atau kesehatan seseorang, dan kami memberi tahu alasannya.

### Keamanan

Semua koneksi memakai HTTPS. Kode masuk disimpan dalam bentuk hash dan sesi memakai token bertanda tangan. Hanya anggota tim yang mengelola server yang bisa membuka database. Cadangan database disimpan di bucket Cloudflare R2 privat dengan kunci akses yang hanya berlaku untuk bucket itu.

Bila terjadi kegagalan pelindungan data pribadi, yaitu data pribadi bocor, hilang, rusak, diubah, atau dibuka pihak yang tidak berhak, kami memberi tahu Anda secara tertulis lewat email paling lambat 3 × 24 jam. Pemberitahuan itu menyebut data apa yang terungkap, kapan dan bagaimana data itu terungkap, serta apa yang kami lakukan untuk menangani dan memulihkannya. Dalam waktu yang sama kami memberi tahu lembaga penyelenggara pelindungan data pribadi.

Bila kami tidak punya email Anda, mis. Anda memakai AksesBogor tanpa akun, atau bila kegagalan itu berdampak serius bagi masyarakat, kami juga mengumumkannya di Beranda AksesBogor.

### Anak

Bila Anda berusia di bawah 18 tahun, data pribadi Anda termasuk data anak. UU PDP mewajibkan data anak diproses secara khusus dan dengan persetujuan orang tua atau wali.

Minta orang tua atau wali Anda membaca kebijakan ini dan mencentang persetujuan bersama Anda sebelum membuat akun. Kotak persetujuan memuat pernyataan bahwa orang tua atau wali Anda ikut menyetujuinya, dan pernyataan itu kami simpan bersama waktu persetujuan. Bila Anda bertanya kepada Agen AI, lakukan bersama orang tua atau wali Anda dan jangan menulis data pribadi di pertanyaan.

Bila kami mengetahui sebuah akun dibuat oleh anak tanpa persetujuan orang tua atau wali, kami menghapus akun itu dan memberi tahu pemiliknya lewat email.

### Penyandang disabilitas dan pendamping

Banyak pengguna AksesBogor adalah penyandang disabilitas. Kami tidak menanyakan apakah Anda penyandang disabilitas dan tidak menyimpan keterangan itu di akun Anda.

Halaman ini dibuat supaya bisa dibaca dengan pembaca layar dan diperbesar. Bila Anda butuh penjelasan kebijakan ini dengan cara lain, hubungi kami lewat kontak di bawah, dan kami menjelaskannya lewat email dengan bahasa yang lebih sederhana. Pendamping boleh membantu Anda membaca kebijakan ini, mencentang persetujuan, dan mengajukan permintaan atas data Anda. Bila Anda didampingi wali, persetujuan diberikan oleh Anda bersama wali Anda.

### Perubahan kebijakan

Bila kebijakan ini berubah, kami menaikkan nomor versinya. Bila perubahan itu menyangkut data yang kami proses, tujuannya, penerimanya, tempat data diproses, atau masa simpannya, kami memberi tahu pemilik akun lewat email paling lambat 7 hari sebelum perubahan berlaku, lalu meminta persetujuan Anda lagi saat Anda masuk berikutnya. Bila Anda tidak setuju, Anda bisa menghapus akun sebelum perubahan berlaku.

Bila Anda memakai Agen AI tanpa akun, versi yang berlaku adalah versi yang tertaut di halaman itu saat Anda bertanya.

Bila AksesBogor dihentikan, kami memberi tahu pemilik akun lewat email paling lambat 30 hari sebelumnya, lalu menghapus akun, sesi, dan laporan yang belum terbit beserta fotonya. Informasi tempat dan foto yang sudah terbit bisa tetap dibagikan sebagai data terbuka, tanpa nama atau email pelapor.

### Kontak

Email: <aliceevrasaldio@apps.ipb.ac.id>. Penanggung jawab pelindungan data pribadi: Aldio Lisafron, anggota tim yang mengelola server dan menjadi admin AksesBogor. Alamat surat: Tim AksesBogor, d.a. Sekolah Vokasi IPB University, Jl. Kumbang No. 14, Kelurahan Babakan, Kecamatan Bogor Tengah, Kota Bogor, Jawa Barat 16128.

Untuk permintaan atas data Anda, kirim email dari alamat email akun AksesBogor Anda supaya kami bisa memastikan permintaan itu dari Anda. Kami membalas paling lambat 3 × 24 jam.

## Referensi

- UU No. 27 Tahun 2022 tentang Pelindungan Data Pribadi ([peraturan.go.id](https://peraturan.go.id/id/uu-no-27-tahun-2022))
- [Pemeriksaan UU PDP 2026-11-27](pemeriksaan-uu-pdp-2026-11-27.md): temuan K1 sampai K18 dan teks pengganti untuk versi 2026-11-27 (dokumen internal, bukan nasihat hukum)
- Dokumen UU PDP di luar teks kebijakan (internal): [catatan kegiatan pemrosesan](catatan-kegiatan-pemrosesan.md) (Pasal 31), [penilaian dampak PDP](penilaian-dampak-pdp.md) (Pasal 34), dan [runbook kegagalan pelindungan data pribadi](../operations/kegagalan-pelindungan-data.md) (Pasal 46)
- [PRD](prd.md), FR-11, FR-13, FR-20 sampai FR-27, FR-50, dan kebutuhan non-fungsional Privasi dan keamanan
- [Syarat Layanan](syarat-layanan.md)
- Cloudflare: [Kebijakan Privasi](https://www.cloudflare.com/privacypolicy/), [perjanjian pemrosesan data (DPA)](https://www.cloudflare.com/cloudflare-customer-dpa/), [Cloudflare Tunnel](https://developers.cloudflare.com/cloudflare-one/networks/connectors/cloudflare-tunnel/), [Self-Serve Subscription Agreement](https://www.cloudflare.com/terms/) (pasal 6.1 memasukkan DPA hanya untuk data pribadi subjek Eropa dan Inggris serta informasi pribadi menurut CCPA, jadi Cloudflare tidak disebut sebagai penyedia dengan DPA di bagian luar negeri)
- Anthropic: [Ketentuan Komersial](https://www.anthropic.com/legal/commercial-terms), [Data Processing Addendum](https://www.anthropic.com/legal/data-processing-addendum) (bagian dari Ketentuan Komersial), [lama penyimpanan data organisasi](https://privacy.claude.com/en/articles/7996866-how-long-do-you-store-my-organization-s-data), [data residency](https://platform.claude.com/docs/en/manage-claude/data-residency)
- Resend: [Data Processing Addendum](https://resend.com/legal/dpa), [Ketentuan Layanan](https://resend.com/legal/terms-of-service), [halaman GDPR](https://resend.com/security/gdpr) (DPA berlaku untuk setiap akun di semua paket), [kuota dan batas akun](https://resend.com/docs/knowledge-base/account-quotas-and-limits) (data email disimpan 30 hari di paket Free, Pro, dan Scale)
- Mapbox: [Ketentuan Layanan](https://www.mapbox.com/legal/tos) (memasukkan DPA lewat rujukan), [Data Processing Addendum](https://www.mapbox.com/legal/dpa)
- Google: [Kebijakan Privasi Google](https://policies.google.com/privacy)
- [ADR-0011](../decisions/0011-pelapor-wajib-login.md), [ADR-0014](../decisions/0014-login-google-dan-kode-email.md), [ADR-0024](../decisions/0024-data-setelah-akun-dihapus.md), [ADR-0026](../decisions/0026-api-dan-postgresql-di-evergarden-oci.md), [ADR-0029](../decisions/0029-akun-better-auth-dan-sesi-lewat-server-web.md), [ADR-0030](../decisions/0030-email-transaksional-resend.md), [ADR-0031](../decisions/0031-foto-laporan-di-bucket-r2-privat.md), [ADR-0032](../decisions/0032-pencarian-tempat-lewat-api-mapbox-search-box.md), [ADR-0033](../decisions/0033-isi-dulu-masuk-saat-kirim-dan-draf-di-perangkat.md), [ADR-0040](../decisions/0040-syarat-layanan-lisensi-laporan-dan-persetujuan.md), [ADR-0042](../decisions/0042-agen-rute-jalan-kaki-dan-informasi-aksesibilitas.md), [ADR-0043](../decisions/0043-agen-untuk-kontributor-dengan-model-gratis-openrouter.md), [ADR-0047](../decisions/0047-model-agen-kontributor-claude-haiku-lewat-claude-api.md), [ADR-0048](../decisions/0048-web-di-host-sendiri-di-evergarden-oci.md), [ADR-0050](../decisions/0050-asisten-rute-publik.md), [ADR-0054](../decisions/0054-agen-ai-satu-pintu.md), [ADR-0055](../decisions/0055-rute-pejalan-kaki-aksesibel-dengan-valhalla.md), [ADR-0057](../decisions/0057-verifikasi-dengan-bukti-street-view.md), [ADR-0059](../decisions/0059-produksi-pindah-ke-server-tim-di-karawang.md)
