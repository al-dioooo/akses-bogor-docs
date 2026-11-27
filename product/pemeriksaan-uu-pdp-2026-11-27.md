# Pemeriksaan Kebijakan Privasi dan Syarat Layanan AksesBogor terhadap UU No. 27 Tahun 2022

| | |
| --- | --- |
| Tanggal | 27 November 2026 |
| Yang diperiksa | Kebijakan Privasi versi 2026-11-26 dan Syarat Layanan versi 2026-11-10. Teks `KebijakanPrivasi`, `SyaratLayanan`, `Lengkapi`, dan `Agen` sama persis di cabang `main` dan `release` akses-bogor-web, jadi yang diperiksa adalah yang tayang |
| Sumber teks | akses-bogor-web `messages/id.json` (namespace `KebijakanPrivasi`, `SyaratLayanan`, `Lengkapi`, `Agen`) di `main` (`5200d5e`); akses-bogor-docs `product/kebijakan-privasi.md` dan `product/syarat-layanan.md` di `main` (`be3bd65`); runbook cadangan di PR akses-bogor-docs #149 (belum digabung) |
| Sumber hukum | UU No. 27 Tahun 2022 tentang Pelindungan Data Pribadi (UU PDP) lewat pasal.id, sumber resmi peraturan.go.id, terverifikasi |
| Sifat | Pengecekan teks undang-undang, bukan tinjauan ahli hukum |
| Tindak lanjut | Teks pengganti diterapkan, dengan placeholder yang sudah diisi tim, di Kebijakan Privasi dan Syarat Layanan versi 2026-11-27 (#150). Dokumen ini disimpan apa adanya sebagai catatan internal |

> **Bukan nasihat hukum.** Dokumen ini membandingkan teks kebijakan dengan teks UU PDP, pasal demi pasal. Penafsirannya (dasar pemrosesan mana yang tepat, titik mulai hitungan 3 × 24 jam, kedudukan Tim AksesBogor sebagai pengendali, dan cukup tidaknya perlindungan transfer ke luar negeri) tetap perlu ditinjau dosen pembimbing atau unit hukum kampus, seperti yang sudah tercatat di daftar "Sebelum Rilis" `product/kebijakan-privasi.md`. Peraturan pelaksana UU PDP (Peraturan Pemerintah yang disebut di Pasal 10, 12, 13, 16, 34, 48, 54, 56, dan Perpres lembaga di Pasal 58) tidak ditemukan di korpus pasal.id saat pencarian, jadi rinciannya tidak ikut diperiksa. Itu tidak berarti peraturannya belum ada.

## Ringkasan

Kebijakan Privasi AksesBogor sudah jauh di atas rata-rata: daftar datanya rinci, tujuan per data jelas, tabel masa simpannya lengkap, dan Agen AI dirancang dengan data minimal. Masa penyesuaian UU PDP sudah lewat sejak 17 Oktober 2024 (Pasal 74, dua tahun sejak diundangkan), dan situs sudah berjalan di akses-bogor.aldio.dev serta menyimpan persetujuan pengguna, jadi kekurangan di bawah berlaku sekarang, bukan baru saat "peluncuran".

Temuan utama, urut dari yang paling mendesak:

1. **Kontak dan identitas pengendali kosong** (Pasal 5, Pasal 14). Bagian Kontak masih "Email kontak resmi diisi tim sebelum peluncuran", padahal semua hak di bagian Hak Anda diarahkan ke "kontak di bawah". Tanpa itu, permintaan tercatat (Pasal 14) tidak bisa diajukan.
2. **Fakta penyedia sudah usang** (Pasal 16 ayat (2) huruf d, Pasal 27). Kebijakan masih menyebut Oracle Cloud di Batam, padahal produksi pindah ke server fatir-mitya pada 26 November 2026, dan API serta web di Evergarden OCI dihentikan. Sejak 27 November 2026 cadangan harian seluruh database (email, nama, laporan) juga dikirim ke Cloudflare R2, dan ini belum disebut sama sekali, padahal itu transfer data paling lengkap yang dilakukan AksesBogor.
3. **Pemberitahuan kegagalan pelindungan belum sesuai Pasal 46.** Kebijakan hanya menjanjikan pemberitahuan ke pengguna. Pasal 46 juga mewajibkan pemberitahuan tertulis ke lembaga, isi minimal (data apa, kapan dan bagaimana, upaya penanganan), dan pengumuman ke masyarakat dalam hal tertentu.
4. **Transfer ke luar negeri belum menyebut dasarnya** (Pasal 56). Negara tujuan sebagian disebut, tetapi tidak dijelaskan apakah perlindungannya setara, diikat perjanjian, atau bergantung pada persetujuan pengguna.
5. **Dasar pemrosesan hanya "persetujuan"** (Pasal 20, 22, 24). Itu tidak mencakup log IP dan Cloudflare untuk semua pengunjung, email yang diproses sebelum Lengkapi Akun, dan Agen AI tanpa akun, yang persetujuannya tidak terekam.
6. **Hak subjek data belum lengkap** (Pasal 6–14, 30, 32, 41). Belum ada hak salinan dan rekam jejak, pembatasan pemrosesan, portabilitas, gugatan dan ganti rugi, aduan ke lembaga, serta tenggat 3 × 24 jam untuk perbaikan dan akses.
7. **Pemberitahuan perubahan datang setelah perubahan berlaku** (Pasal 21 ayat (2)). Persetujuan ulang baru diminta saat masuk berikutnya.
8. **Anak dan penyandang disabilitas** (Pasal 25, 26). Persetujuan orang tua atau wali tidak terekam, dan kebijakan belum menyebut penanganan khusus data penyandang disabilitas, padahal mereka pengguna utama AksesBogor.
9. **Syarat Layanan**: izin "tidak bisa ditarik kembali" atas laporan dan foto perlu dipisahkan tegas dari persetujuan atas data pribadi (Pasal 8, 9, 23).

Data jumlah penyandang disabilitas dari Dinas Sosial adalah agregat per kecamatan, bukan data pribadi (Pasal 1 angka 1), jadi Pasal 4 tidak berlaku untuknya.

**Perlu naik versi dan persetujuan ulang: ya.** Usulan: Kebijakan Privasi dan Syarat Layanan naik bersama ke `2026-11-27` (atau tanggal rilisnya), web dirilis dulu lalu `PRIVACY_POLICY_VERSION` di API. Alasannya ada di bagian [Perubahan versi](#perubahan-versi-dan-persetujuan-ulang).

Hitungan status (29 butir Kebijakan Privasi): 5 Terpenuhi, 13 Sebagian, 6 Belum, 5 Tidak berlaku. Syarat Layanan: 1 Terpenuhi, 1 Sebagian, 1 Belum, 1 Tidak berlaku.

## Tabel temuan

### Kebijakan Privasi

| No | Ketentuan UU PDP | Isi kebijakan sekarang | Status | Alasan |
| --- | --- | --- | --- | --- |
| K1 | Identitas dan akuntabilitas pengendali, Pasal 5; pengendali, Pasal 1 angka 4, 7, 8; permohonan tercatat, Pasal 14 | "Tim AksesBogor adalah pengendali data pribadi Anda…"; Kontak: "Email kontak resmi diisi tim sebelum peluncuran." | Belum | Tim yang tidak berbadan hukum tetap bisa menjadi pengendali, karena "Korporasi" mencakup "kumpulan orang … baik yang berbadan hukum maupun tidak berbadan hukum" (Pasal 1 angka 8). Namun Pasal 5 memberi hak atas "kejelasan identitas" dan "akuntabilitas pihak yang meminta Data Pribadi", sedangkan nama kampus, penanggung jawab, dan kontak masih kosong. Hak di Pasal 6–11 diajukan "melalui permohonan tercatat" (Pasal 14), tetapi alamatnya belum ada. |
| K2 | Akurat, tidak menyesatkan, mutakhir, Pasal 16 ayat (2) huruf a dan d; transparan, Pasal 27 | "Oracle Cloud: menyediakan server di region Indonesia North, Batam…"; "Kami hanya mengumpulkan data pribadi bila Anda membuat akun, melapor, atau bertanya…" | Belum | Server produksi sudah pindah ke fatir-mitya (26 Nov 2026). Cadangan harian database ke bucket R2 `akses-bogor-backup` (sejak 27 Nov 2026) tidak disebut. Kalimat pembuka bertentangan dengan item Teknis, karena alamat IP termasuk data pribadi ("nomor telepon seluler dan IP Address", Penjelasan Pasal 4 ayat (3) huruf f) dan dicatat untuk setiap pengunjung. Nama dari akun Google juga dipakai (usulan nama tampilan, `src/auth/auth.ts`) tetapi tidak disebut. |
| K3 | Dasar pemrosesan, Pasal 20 ayat (1) dan (2) | "Dasar pemrosesannya adalah persetujuan yang Anda berikan saat melengkapi akun… Bila Anda memakai Agen AI tanpa akun, dasarnya adalah persetujuan Anda saat mengirim pertanyaan…" | Sebagian | Persetujuan baru ada setelah Lengkapi Akun, padahal email atau token Google sudah diproses saat Masuk. Log IP, lalu lintas Cloudflare, IP yang diterima Mapbox, hitungan pertanyaan per IP, dan log audit diproses untuk semua pengunjung tanpa persetujuan. Untuk Agen AI tanpa akun, persetujuan harus "tertulis atau terekam" (Pasal 22 ayat (1)) dan pengendali wajib bisa "menunjukkan bukti persetujuan" (Pasal 24); keduanya tidak ada. Pasal 20 ayat (2) huruf b dan f menyediakan dasar lain yang lebih cocok. |
| K4 | Informasi yang wajib disampaikan saat memakai persetujuan, Pasal 21 ayat (1) | Seluruh dokumen | Sebagian | Huruf b–f (tujuan, jenis data, retensi, rincian, jangka waktu) dipenuhi dengan baik. Huruf a ("legalitas dari pemrosesan") belum lengkap (lihat K3), dan huruf g ("hak Subjek Data Pribadi") belum lengkap (lihat K6). |
| K5 | Pemberitahuan sebelum perubahan, Pasal 21 ayat (2) | "Bila kebijakan ini berubah, kami menaikkan nomor versinya dan meminta persetujuan Anda lagi saat Anda masuk berikutnya." | Sebagian | Pasal 21 ayat (2) mewajibkan pemberitahuan "sebelum terjadi perubahan Informasi". Persetujuan ulang saat masuk berikutnya terjadi sesudahnya. Pemindahan server dan cadangan R2 sudah berlaku tanpa pemberitahuan. |
| K6a | Hak akses dan salinan, Pasal 7; akses dan rekam jejak dalam 3 × 24 jam, Pasal 32 | "Melihat data: nama tampilan, email, dan laporan Anda ada di halaman Akun Saya." | Sebagian | Akun Saya tidak menampilkan semua data (waktu dan versi persetujuan, sesi, nomor akun Google, jawaban klarifikasi). Belum ada cara meminta salinan beserta "rekam jejak pemrosesan", juga tenggat 3 × 24 jam. |
| K6b | Hak memperbaiki, Pasal 6; tenggat 3 × 24 jam dan pemberitahuan hasil, Pasal 30 | "Untuk mengganti email, buat akun baru dengan email itu." | Sebagian | Membuat akun baru tidak memperbaiki email di akun lama, dan laporan lama tetap di akun lama. Belum ada jalur permintaan, tenggat 3 × 24 jam, dan pemberitahuan hasil (Pasal 30 ayat (2)). |
| K6c | Hak menghapus, Pasal 8; wajib menghapus dan memberi tahu, Pasal 43, 45 | "Menghapus akun: pilih Hapus akun… Kami mengirim satu email terakhir sebagai konfirmasi. Salinan di cadangan database hilang paling lama 30 hari kemudian." | Terpenuhi | Penghapusan langsung berjalan, ada email konfirmasi (Pasal 45), dan sisa di cadangan diungkap. |
| K6d | Hak menarik persetujuan, Pasal 9; berhenti dalam 3 × 24 jam, Pasal 40 | "Anda bisa menarik persetujuan kapan saja dengan menghapus akun." | Terpenuhi | Penghapusan langsung, jauh di bawah 3 × 24 jam. Teks pengganti di K6 menambah jalur lewat kontak bagi yang tidak bisa memakai Akun Saya. |
| K6e | Keberatan atas keputusan otomatis, Pasal 10 | Tidak disebut | Tidak berlaku | AksesBogor tidak mengambil keputusan tentang orang yang "menimbulkan akibat hukum atau berdampak signifikan". Status tempat dihitung otomatis, tetapi itu penilaian tempat. Satu kalimat penjelasan tetap disarankan supaya daftar hak lengkap (Pasal 21 ayat (1) huruf g). |
| K6f | Hak menunda atau membatasi, Pasal 11; tenggat 3 × 24 jam, Pasal 41 | Tidak disebut | Belum | Hak ini belum diinformasikan. |
| K6g | Hak menggugat dan ganti rugi, Pasal 12; aduan ke lembaga, Pasal 60 huruf i; sengketa, Pasal 64 | Tidak disebut | Belum | Pasal 21 ayat (1) huruf g mewajibkan informasi tentang hak subjek. Lembaga berwenang "menerima aduan dan/atau laporan" (Pasal 60 huruf i), dan sengketa diselesaikan "melalui arbitrase, pengadilan, atau lembaga penyelesaian sengketa alternatif lainnya" (Pasal 64 ayat (1)). |
| K6h | Hak portabilitas, Pasal 13 | Tidak disebut | Belum | Ayat (1), yaitu mendapat data "dalam bentuk … yang lazim digunakan atau dapat dibaca oleh sistem elektronik", sudah berlaku. Rincian pengiriman ke pengendali lain menunggu PP (ayat (3)). |
| K6i | Cara mengajukan hak, Pasal 14 | "hubungi kami lewat kontak di bawah" | Belum | Kontak kosong (lihat K1). |
| K7 | Bentuk dan bukti persetujuan, Pasal 22, 24 | Lengkapi Akun: "Saya sudah membaca dan menyetujui Kebijakan Privasi AksesBogor."; waktu dan versi disimpan | Terpenuhi | Untuk pemilik akun, persetujuan terekam secara elektronik (Pasal 22 ayat (1) dan (2)) dan buktinya disimpan (Pasal 24). Syarat Layanan disetujui dengan tombol terpisah, jadi dapat dibedakan (Pasal 22 ayat (4) huruf a). Saran: ringkasan satu paragraf di Lengkapi Akun untuk memenuhi "format yang dapat dipahami" dan "bahasa yang sederhana" (huruf b dan c). Untuk Agen AI tanpa akun, lihat K3. |
| K8 | Retensi dan penghapusan, Pasal 16 ayat (2) huruf g, Pasal 21 ayat (1) huruf d dan f, Pasal 42–44 | Tabel "Berapa lama data disimpan" | Sebagian | Tabelnya rinci. Kurang: (1) cadangan disimpan di dua tempat (7 dump terakhir di server dan R2 selama 30 hari); (2) masa simpan email di Resend dan log di Cloudflare tidak disebut; (3) akun yang lama tidak dipakai disimpan tanpa batas. Butir 3 hanya saran, karena Pasal 42 ayat (1) huruf b ("tujuan pemrosesan … telah tercapai") jarang terpicu selama akun masih bisa dipakai. |
| K9 | Keamanan, Pasal 35, 36, 39 | "Semua koneksi memakai HTTPS. Kode masuk disimpan dalam bentuk hash…" | Terpenuhi | Langkah teknis disebut. Saran: sebut bahwa cadangan ada di bucket privat dengan kunci terbatas. Enkripsi dump adalah urusan internal (lihat bagian Kewajiban di luar teks). |
| K10 | Pemberitahuan kegagalan pelindungan, Pasal 46 | "Bila terjadi kebocoran data pribadi, kami memberi tahu Anda paling lambat 3 × 24 jam setelah mengetahuinya." | Sebagian | Pasal 46 ayat (1): "pemberitahuan secara tertulis paling lambat 3 x 24 … jam kepada: a. Subjek Data Pribadi; dan b. lembaga". Ayat (2): isi minimal (data yang terungkap, kapan dan bagaimana, upaya penanganan). Ayat (3): masyarakat "dalam hal tertentu". Kebijakan hanya menyebut subjek. "Kebocoran" juga lebih sempit dari "kegagalan", yang menurut Penjelasan mencakup "perusakan, kehilangan, perubahan, pengungkapan, atau akses yang tidak sah". "Setelah mengetahuinya" tidak tertulis di pasal. |
| K11 | Prosesor, Pasal 51, 52; pengawasan, Pasal 37 | Daftar "Penyedia layanan berikut memproses data atas nama kami" | Sebagian | Oracle sudah tidak dipakai, dan server baru tidak disebut. Google dimasukkan ke daftar "atas nama kami", padahal Google Sign-In diproses menurut kebijakan Google sendiri dan Google juga menerima IP pengunjung halaman Masuk. Resend tidak menyebut data yang diterima. Catatan: `AGENT_PROVIDER=openrouter` masih bisa dipakai di API; bila diaktifkan, OpenRouter menjadi penerima baru yang belum ada di kebijakan. |
| K12 | Transfer ke luar wilayah RI, Pasal 56 | "Resend, Google, Mapbox, Cloudflare, dan Anthropic bisa memproses data di luar Indonesia, termasuk di Amerika Serikat…" | Sebagian | Pasal 56 ayat (2)–(4) berjenjang: negara dengan pelindungan "setara atau lebih tinggi"; bila tidak, "Pelindungan Data Pribadi yang memadai dan bersifat mengikat"; bila tidak juga, "persetujuan Subjek Data Pribadi". Kebijakan tidak menyebut jenjang mana yang dipakai. Cadangan seluruh database ke R2 tidak disebut. Resend mengirim dari Tokyo, Jepang, juga tidak disebut. |
| K13a | Data spesifik: kesehatan, Pasal 4 ayat (2) huruf a | Agen AI: "Pertanyaan Anda… dikirim lewat server kami ke Anthropic…"; pemberitahuan Agen: "Jangan tulis data pribadi, mis. nama, nomor telepon, atau alamat rumah." | Sebagian | Pertanyaan seperti "saya pakai kursi roda, rute ke…" bisa memuat "keterangan individu yang berkaitan dengan kesehatan fisik" (Penjelasan Pasal 4 ayat (2) huruf a). Datanya tidak disimpan dan tidak dikirim bersama identitas ke Anthropic, jadi risikonya kecil, tetapi kebijakan belum menyebutnya. |
| K13b | Data spesifik: data Dinas Sosial | Peta tematik: "Penyandang disabilitas yang terdaftar di Dinas Sosial per kecamatan, menurut jenis dan tahun." | Tidak berlaku | Jumlah per kecamatan, jenis, dan tahun tidak menunjuk orang yang "teridentifikasi atau dapat diidentifikasi" (Pasal 1 angka 1), jadi bukan data pribadi. Data itu juga sudah dipublikasikan Dinsos di Open Data Kota Bogor. Catatan kecil: angka yang sangat kecil (1–2 orang) untuk jenis yang jarang lebih mudah dikaitkan ke orang tertentu bila digabung dengan pengetahuan lokal; pertimbangkan menampilkannya sebagai "kurang dari 5". |
| K13c | Foto laporan: gambar wajah (contoh data biometrik di Penjelasan Pasal 4 ayat (2) huruf b) | "Sebelum foto terbit, verifikator mengaburkan wajah dan pelat nomor atau menyembunyikan fotonya…" | Terpenuhi | Foto tidak dipakai untuk mengenali orang. Wajah dan pelat dikaburkan sebelum tayang, dan foto asli dihapus. |
| K13d | Foto Street View (ADR-0057): data pihak ketiga | Tidak disebut | Sebagian | Foto pintu masuk dari Google Street View diterbitkan sebagai foto AksesBogor tanpa langkah pengaburan oleh verifikator (runbook verifikasi Street View tidak memuat langkah itu). Orang yang terekam adalah subjek data juga (Pasal 2 ayat (1)). Google memang mengaburkan wajah dan pelat, tetapi kebijakan belum menyebut sumber ini atau cara meminta foto dikaburkan. |
| K14 | Data anak, Pasal 4 ayat (2) huruf e; persetujuan orang tua atau wali, Pasal 25 | "Bila Anda berusia di bawah 18 tahun, minta orang tua atau wali Anda membaca kebijakan ini dan menyetujuinya bersama Anda…" | Sebagian | Batas 18 tahun sesuai Pasal 1 angka 1 UU No. 23 Tahun 2002 tentang Perlindungan Anak ("seseorang yang belum berusia 18 (delapan belas) tahun"). Namun Pasal 25 ayat (2) mewajibkan persetujuan orang tua atau wali, dan Pasal 24 mewajibkan buktinya. Kotak centang sekarang tidak merekam bahwa wali ikut menyetujui. |
| K15 | Data penyandang disabilitas, Pasal 26 | Tidak disebut | Sebagian | Pasal 26: pemrosesan "diselenggarakan secara khusus", "melalui komunikasi dengan menggunakan cara tertentu", dan memerlukan persetujuan "penyandang disabilitas dan/atau wali". AksesBogor tidak mencatat status disabilitas, tetapi penggunanya memang terutama penyandang disabilitas. Halaman yang memenuhi WCAG 2.2 AA (ADR-0021) sudah menjadi "cara tertentu" yang baik, tetapi kebijakan belum menyebut cara lain, peran pendamping, atau wali. |
| K16 | Pejabat atau petugas PDP, Pasal 53 | Tidak ada | Tidak berlaku | Kewajiban ini hanya untuk pelayanan publik, pemantauan sistematis berskala besar, atau data spesifik berskala besar (ayat (1)). AksesBogor kemungkinan besar tidak termasuk. Perlu dinilai ulang bila AksesBogor resmi menjadi layanan kampus atau pemerintah. Teks K1 tetap menunjuk satu penanggung jawab sebagai narahubung. |
| K17 | Penghentian dan pengalihan data, Pasal 48; penghapusan setelah tujuan selesai, Pasal 16 ayat (2) huruf g | Tidak ada; Syarat: "layanan bisa terhenti sementara, berubah, atau dihentikan" | Tidak berlaku | Pasal 48 hanya untuk pengendali berbentuk badan hukum. Namun proyek mahasiswa kemungkinan berhenti suatu saat, jadi disarankan kebijakan menyebut apa yang terjadi pada data saat itu. |
| K18 | Alat pemroses data visual di tempat umum, Pasal 17 | — | Tidak berlaku | AksesBogor tidak memasang kamera di tempat umum. Foto dikirim pengguna atau diambil dari Street View. |

### Syarat Layanan

| No | Ketentuan UU PDP | Isi sekarang | Status | Alasan |
| --- | --- | --- | --- | --- |
| S1 | Hak menghapus dan menarik persetujuan, Pasal 8, 9; klausul perjanjian tanpa persetujuan eksplisit batal demi hukum, Pasal 23 | "…izin tanpa bayaran, tidak eksklusif, berlaku di mana pun, dan tidak bisa ditarik kembali…" | Sebagian | Izin atas isi laporan dan foto sah sebagai izin hak cipta. Namun bila isi yang terbit memuat data pribadi (wajah yang lolos, nama, nomor telepon di catatan), klausul "tidak bisa ditarik kembali" bisa dibaca sebagai pemrosesan data pribadi tanpa persetujuan yang bisa ditarik. Perlu pengecualian tegas untuk data pribadi. |
| S2 | Identitas dan kontak, Pasal 5, 14 | "Email kontak resmi diisi tim sebelum peluncuran." | Belum | Sama dengan K1. |
| S3 | Penghentian layanan, Pasal 48 (analogi) | "Kami berusaha menjaganya tetap berjalan, tetapi layanan bisa terhenti sementara, berubah, atau dihentikan." | Tidak berlaku | Disarankan merujuk ke Kebijakan Privasi soal nasib data (lihat K17). |
| S4 | Anak, Pasal 25 | "Bila Anda berusia di bawah 18 tahun, minta orang tua atau wali Anda membaca syarat ini…" | Terpenuhi | Sejalan dengan Kebijakan Privasi. Perekaman persetujuannya ditangani lewat K14. |

### Teks lain di aplikasi

| No | Kunci | Isi sekarang | Status | Alasan |
| --- | --- | --- | --- | --- |
| A1 | `Agen.awal.privacy` | "Server AksesBogor memakainya untuk jarak ke tempat terdekat, dan Mapbox untuk menghitung rute." | Belum | Sejak Valhalla (ADR-0055), rute dihitung di server sendiri dan Mapbox hanya cadangan. Kebijakan sudah benar, tetapi pemberitahuan di layar menyesatkan (Pasal 16 ayat (2) huruf d). |

## Teks pengganti per temuan

Aturan umum untuk pengembang:

- Web: semua teks ada di `messages/id.json`, namespace `KebijakanPrivasi` atau `SyaratLayanan`, array `sections`. Bentuk tiap bagian mengikuti tipe `LegalSection` di `src/components/legal/legal-document.tsx` (`paragraphs`, `items`, `after`, `table`). Komponen memakai isi teks sebagai `key`, jadi dua paragraf dalam satu bagian tidak boleh sama persis. Begitu juga kolom pertama baris tabel.
- Docs: teks yang sama masuk ke `product/kebijakan-privasi.md` atau `product/syarat-layanan.md` di bawah judul `###` yang sama. Untuk daftar, docs memakai gaya `- **Label**: teks` (label sebelum titik dua ditebalkan) dan memberi tanda kutip pada nama tombol, mis. "Hapus akun".
- Semua `[ISI: …]` adalah placeholder yang harus diisi tim. Jangan menerbitkan teks yang masih memuat placeholder. Daftar lengkapnya ada di bagian [Placeholder](#placeholder-yang-harus-diisi-tim).
- `e2e/halaman-legal.spec.ts` mencari sel definisi berisi "30 hari" di tabel masa simpan. Baris "Log server | 30 hari" tetap ada, jadi tes itu tetap lolos.

### K1 dan S2: identitas pengendali dan kontak

**Web** `KebijakanPrivasi.sections[0]` (`id: "siapa-kami"`), ganti seluruh bagian:

```json
{
  "id": "siapa-kami",
  "title": "Siapa kami",
  "paragraphs": [
    "AksesBogor adalah web yang mencatat aksesibilitas fasilitas publik di Kota Bogor bagi penyandang disabilitas dan pendampingnya. AksesBogor dikelola Tim AksesBogor, kelompok mahasiswa [ISI: program studi dan nama kampus], sebagai proyek mahasiswa. Tim AksesBogor adalah pengendali data pribadi Anda sesuai UU No. 27 Tahun 2022 tentang Pelindungan Data Pribadi (UU PDP): kami yang menentukan data apa yang dikumpulkan, untuk apa, dan siapa yang ikut memprosesnya.",
    "Penanggung jawab pelindungan data pribadi di Tim AksesBogor adalah [ISI: nama dan peran di tim]. Semua pertanyaan dan permintaan tentang data pribadi Anda ditangani lewat kontak di bagian Kontak."
  ]
}
```

**Web** `KebijakanPrivasi.sections[10]` (`id: "kontak"`), ganti seluruh bagian:

```json
{
  "id": "kontak",
  "title": "Kontak",
  "paragraphs": [
    "Email: [ISI: email kontak resmi tim]. Penanggung jawab pelindungan data pribadi: [ISI: nama dan peran di tim]. Alamat surat: [ISI: alamat kampus atau sekretariat tim].",
    "Untuk permintaan atas data Anda, kirim email dari alamat email akun AksesBogor Anda supaya kami bisa memastikan permintaan itu dari Anda. Kami membalas paling lambat 3 × 24 jam."
  ]
}
```

**Web** `SyaratLayanan.sections[13]` (`id: "kontak"`), `paragraphs[0]`:

```json
{
  "id": "kontak",
  "title": "Kontak",
  "paragraphs": [
    "Email: [ISI: email kontak resmi tim]. Alamat surat: [ISI: alamat kampus atau sekretariat tim]."
  ]
}
```

**Docs**: `### Siapa kami` dan `### Kontak` di kedua dokumen, teks yang sama. Centang butir pertama "Sebelum Rilis" setelah terisi.

Catatan untuk tim: `info@aksesbogor.id` di Figma masih placeholder (daftar "Sebelum Rilis"). Pakai hanya alamat yang benar-benar dibaca tim. Bila AksesBogor resmi berada di bawah kampus, mis. sebagai tugas mata kuliah dengan nama kampus di situs, pastikan dengan peninjau apakah kampus ikut menjadi pengendali bersama (Pasal 18), karena kalimat "Tim AksesBogor adalah pengendali" akan berubah.

### K2: fakta yang usang

**Web** `KebijakanPrivasi.sections[1]` (`id: "data"`), ganti `paragraphs[0]`:

```json
{
  "paragraphs": [
    "Anda bisa menjelajahi AksesBogor tanpa akun. Setiap kali Anda membuka halaman, alamat IP Anda tercatat di log server dan diterima Cloudflare (lihat Teknis di bawah). Data pribadi lain hanya kami kumpulkan bila Anda membuat akun, melapor, atau bertanya kepada Agen AI."
  ]
}
```

**Web** `KebijakanPrivasi.sections[1].items[1]` (Masuk): sisipkan kalimat ini setelah "…untuk menyambungkannya ke akun AksesBogor.":

> Nama akun Google hanya dipakai sebagai usulan nama tampilan di Lengkapi Akun.

Fakta server dan cadangan diperbaiki di K11 (bagian `penerima`), K12 (`luar-negeri`), dan K8 (`masa-simpan`).

### A1: pemberitahuan titik awal di Agen AI

**Web** `Agen.awal.privacy`, ganti:

```json
{
  "privacy": "Titik awal dibulatkan sampai sekitar 11 m. Server AksesBogor memakainya untuk jarak ke tempat terdekat dan untuk menghitung rute; Mapbox hanya menerimanya bila rute dari server kami belum tersedia. Titik ini tidak disimpan dan tidak pernah dikirim ke model AI."
}
```

Perubahan ini tidak menaikkan versi kebijakan, karena hanya menyelaraskan layar dengan kebijakan yang sudah benar.

### K3 dan K4: dasar pemrosesan

**Web** `KebijakanPrivasi.sections[2]` (`id: "tujuan"`), ganti `after` seluruhnya (`items` tetap):

```json
{
  "after": [
    "Dasar pemrosesan data akun dan laporan adalah persetujuan yang Anda berikan saat melengkapi akun (Pasal 20 ayat (2) huruf a UU PDP). Kami menyimpan waktu dan versi persetujuan itu sebagai buktinya. Anda bisa menarik persetujuan kapan saja dengan menghapus akun di Akun Saya, atau meminta kami menghapusnya lewat kontak di bawah; permintaan lewat kontak kami jalankan paling lambat 3 × 24 jam.",
    "Sebelum Anda melengkapi akun, email yang Anda ketik di halaman Masuk, atau nomor akun, email, dan nama dari Google bila Anda masuk dengan Google, hanya dipakai untuk mengirim kode dan memasukkan Anda, karena Anda memintanya (Pasal 20 ayat (2) huruf b). Bila akun tidak dilengkapi, data itu dihapus dalam 24 jam.",
    "Bila Anda bertanya kepada Agen AI tanpa akun, pertanyaan Anda diproses untuk menjawabnya, karena Anda memintanya saat memakai AksesBogor sesuai Syarat Layanan (Pasal 20 ayat (2) huruf b). Berhenti bertanya berarti tidak ada data lagi yang dikirim.",
    "Alamat IP dan data permintaan di log server, lalu lintas yang melewati Cloudflare, alamat IP yang diterima Mapbox saat peta dimuat, hitungan pertanyaan per alamat IP, dan log audit perubahan data tempat diproses berdasarkan kepentingan yang sah (Pasal 20 ayat (2) huruf f): menjaga layanan tetap aman dan bisa dipakai semua orang, serta menelusuri siapa mengubah data tempat. Ini juga berlaku bila Anda tidak punya akun. Kami hanya memproses data ini seperti yang tertulis di kebijakan ini dan selama masa simpan di bawah."
  ]
}
```

**Docs**: `### Untuk apa data dipakai`, ganti paragraf "Dasar pemrosesannya adalah…" dengan empat paragraf di atas.

Catatan untuk peninjau hukum: pemakaian huruf b untuk Agen AI tanpa akun menganggap pemakaian situs sesuai Syarat Layanan sebagai perjanjian. Alternatifnya huruf f (kepentingan yang sah), atau tetap memakai persetujuan tetapi merekamnya, yang bertentangan dengan prinsip data minimal ADR-0029. Pilihan ini perlu dikonfirmasi peninjau.

### K5 dan K17: perubahan kebijakan dan penghentian layanan

**Web** `KebijakanPrivasi.sections[9]` (`id: "perubahan"`), ganti seluruh bagian:

```json
{
  "id": "perubahan",
  "title": "Perubahan kebijakan",
  "paragraphs": [
    "Bila kebijakan ini berubah, kami menaikkan nomor versinya. Bila perubahan itu menyangkut data yang kami proses, tujuannya, penerimanya, tempat data diproses, atau masa simpannya, kami memberi tahu pemilik akun lewat email paling lambat [ISI: jumlah hari, mis. 7] hari sebelum perubahan berlaku, lalu meminta persetujuan Anda lagi saat Anda masuk berikutnya. Bila Anda tidak setuju, Anda bisa menghapus akun sebelum perubahan berlaku.",
    "Bila Anda memakai Agen AI tanpa akun, versi yang berlaku adalah versi yang tertaut di halaman itu saat Anda bertanya.",
    "Bila AksesBogor dihentikan, kami memberi tahu pemilik akun lewat email paling lambat [ISI: jumlah hari, mis. 30] hari sebelumnya, lalu menghapus akun, sesi, dan laporan yang belum terbit beserta fotonya. Informasi tempat dan foto yang sudah terbit bisa tetap dibagikan sebagai data terbuka, tanpa nama atau email pelapor."
  ]
}
```

**Web** `SyaratLayanan.sections[9]` (`id: "ketersediaan"`), ganti `paragraphs`:

```json
{
  "paragraphs": [
    "AksesBogor dikerjakan tim mahasiswa. Kami berusaha menjaganya tetap berjalan, tetapi layanan bisa terhenti sementara, berubah, atau dihentikan.",
    "Bila AksesBogor dihentikan, kami memberi tahu pemilik akun lewat email paling lambat [ISI: jumlah hari yang sama dengan Kebijakan Privasi] hari sebelumnya. Apa yang terjadi pada data Anda dijelaskan di Kebijakan Privasi."
  ]
}
```

Catatan untuk tim: Pasal 21 ayat (2) tidak memberi pengecualian untuk perubahan mendesak. Pemberitahuan email sebelum perubahan berarti tim perlu mengirim email massal ke pemilik akun. Karena pemindahan server dan cadangan R2 sudah terjadi, kirim email penjelasan bersamaan dengan rilis versi baru (lihat [Perubahan versi](#perubahan-versi-dan-persetujuan-ulang)).

### K6: hak subjek data

**Web** `KebijakanPrivasi.sections[6]` (`id: "hak"`), ganti seluruh bagian:

```json
{
  "id": "hak",
  "title": "Hak Anda",
  "items": [
    "Mendapat informasi: kebijakan ini menjelaskan siapa kami, data apa yang kami proses, untuk apa, dasar pemrosesannya, siapa yang menerimanya, di mana diproses, dan berapa lama disimpan.",
    "Melihat dan meminta salinan data: nama tampilan, email, dan laporan Anda ada di halaman Akun Saya. Untuk salinan lengkap, termasuk waktu dan versi persetujuan, sesi masuk, jawaban klarifikasi, dan catatan kapan data Anda diproses, kirim permintaan ke kontak di bawah. Kami mengirimkannya paling lambat 3 × 24 jam dalam berkas yang bisa dibaca komputer (JSON), yang juga bisa Anda bawa ke layanan lain.",
    "Memperbaiki data: ubah nama tampilan di Akun Saya. Untuk mengganti email atau memperbaiki data lain, hubungi kami. Kami memperbaikinya paling lambat 3 × 24 jam dan memberi tahu Anda hasilnya.",
    "Menghapus akun: pilih Hapus akun di Akun Saya. Penghapusan langsung berjalan dan tidak bisa dibatalkan. Nama, email, waktu persetujuan, semua sesi, dan laporan yang belum terbit beserta fotonya dihapus. Informasi tempat dan foto yang sudah terbit tetap tayang tanpa nama Anda. Kami mengirim satu email terakhir sebagai konfirmasi. Salinan di cadangan database hilang paling lama 30 hari kemudian.",
    "Menarik persetujuan: hapus akun di Akun Saya, atau minta kami menghapusnya lewat kontak di bawah. Permintaan lewat kontak kami jalankan paling lambat 3 × 24 jam.",
    "Menunda atau membatasi pemrosesan: mis. meminta kami menahan laporan Anda yang belum diperiksa, atau berhenti mengirim email pemberitahuan laporan. Kami melakukannya paling lambat 3 × 24 jam dan memberi tahu Anda, kecuali peraturan perundang-undangan tidak memungkinkannya atau penundaan itu membahayakan keselamatan orang lain.",
    "Keputusan otomatis: kami tidak mengambil keputusan tentang Anda yang hanya didasarkan pada pemrosesan otomatis. Status tempat dihitung otomatis dari jawaban fasilitas, tetapi itu penilaian tentang tempat, bukan tentang Anda. Bila Anda merasa terdampak keputusan otomatis, ajukan keberatan lewat kontak di bawah.",
    "Bertanya atau mengadu: hubungi kami lewat kontak di bawah. Kami menjawab paling lambat 3 × 24 jam.",
    "Mengadu ke lembaga atau menggugat: bila jawaban kami tidak memuaskan, Anda bisa mengadu ke lembaga penyelenggara pelindungan data pribadi yang ditetapkan Presiden, menyelesaikan sengketa lewat arbitrase, pengadilan, atau lembaga penyelesaian sengketa lainnya, dan menggugat serta meminta ganti rugi atas pelanggaran pemrosesan data Anda sesuai peraturan perundang-undangan."
  ],
  "after": [
    "Setiap permintaan kami catat. Kami boleh menolak permintaan yang akan mengungkap data pribadi orang lain atau membahayakan keselamatan atau kesehatan seseorang, dan kami memberi tahu alasannya."
  ]
}
```

**Docs**: `### Hak Anda`, dengan gaya `- **Melihat dan meminta salinan data**: …` dan seterusnya, ditambah paragraf penutup.

Catatan untuk tim: teks ini menjanjikan pekerjaan manual dalam 3 × 24 jam, yaitu ekspor JSON, ganti email lewat database, menahan laporan, dan mematikan email pemberitahuan per akun. Itu tenggat dari Pasal 30, 32, 40, dan 41, bukan pilihan. Pastikan ada orang dan skrip untuk mengerjakannya sebelum teks terbit. Daftar penolakan di paragraf penutup mengikuti Pasal 33.

### K8: masa simpan

**Web** `KebijakanPrivasi.sections[5].table.rows` (`id: "masa-simpan"`), ganti baris `"Cadangan database"` dan tambahkan dua baris setelah `"Log server"`:

```json
[
  ["Cadangan database", "Tujuh salinan harian terakhir di server kami; salinan di Cloudflare R2 dihapus otomatis setelah 30 hari"],
  ["Log server", "30 hari"],
  ["Email yang dikirim lewat Resend", "[ISI: masa simpan log dan isi email di akun Resend tim]"],
  ["Log permintaan di Cloudflare", "[ISI: masa simpan log menurut paket Cloudflare yang dipakai]"]
]
```

**Docs**: tabel di `### Berapa lama data disimpan`, baris yang sama.

Saran tambahan, yang hanya dipakai bila fiturnya dibangun: `["Akun yang tidak dipakai", "Dihapus setelah [ISI] tahun tanpa masuk, setelah kami beri tahu lewat email 30 hari sebelumnya"]`.

### K9 dan K10: keamanan dan kegagalan pelindungan

**Web** `KebijakanPrivasi.sections[7]` (`id: "keamanan"`), ganti seluruh bagian:

```json
{
  "id": "keamanan",
  "title": "Keamanan",
  "paragraphs": [
    "Semua koneksi memakai HTTPS. Kode masuk disimpan dalam bentuk hash dan sesi memakai token bertanda tangan. Hanya anggota tim yang mengelola server yang bisa membuka database. Cadangan database disimpan di bucket Cloudflare R2 privat dengan kunci akses yang hanya berlaku untuk bucket itu.",
    "Bila terjadi kegagalan pelindungan data pribadi, yaitu data pribadi bocor, hilang, rusak, diubah, atau dibuka pihak yang tidak berhak, kami memberi tahu Anda secara tertulis lewat email paling lambat 3 × 24 jam. Pemberitahuan itu menyebut data apa yang terungkap, kapan dan bagaimana data itu terungkap, serta apa yang kami lakukan untuk menangani dan memulihkannya. Dalam waktu yang sama kami memberi tahu lembaga penyelenggara pelindungan data pribadi.",
    "Bila kami tidak punya email Anda, mis. Anda memakai AksesBogor tanpa akun, atau bila kegagalan itu berdampak serius bagi masyarakat, kami juga mengumumkannya di Beranda AksesBogor."
  ]
}
```

**Docs**: `### Keamanan`, tiga paragraf yang sama.

Catatan untuk peninjau: Pasal 46 ayat (1) tidak menyebut kapan 3 × 24 jam mulai dihitung. Teks lama memakai "setelah mengetahuinya". Teks baru mengikuti bunyi pasal tanpa titik mulai, jadi lebih ketat bagi tim. Bila peninjau setuju hitungan dimulai sejak tim mengetahui kegagalan, tambahkan kembali "sejak kami mengetahuinya".

### K11 dan K13d: penerima data, prosesor, dan foto Street View

**Web** `KebijakanPrivasi.sections[3]` (`id: "penerima"`), ganti seluruh bagian:

```json
{
  "id": "penerima",
  "title": "Siapa yang bisa melihat data Anda",
  "paragraphs": [
    "Verifikator AksesBogor melihat nama tampilan pelapor, bukan email, beserta isian, foto, dan jawaban klarifikasi saat memeriksanya. Sebelum diverifikasi, laporan dan foto tidak bisa dilihat publik. Sebelum foto terbit, verifikator mengaburkan wajah dan pelat nomor atau menyembunyikan fotonya; hanya hasil yang sudah dikaburkan yang tayang. Sebagian foto pintu masuk diambil tim dari Google Street View dan diberi keterangan sumbernya; Google sudah mengaburkan wajah dan pelat nomor di gambar itu. Bila Anda melihat wajah atau pelat nomor yang masih jelas di foto mana pun, beri tahu kami, dan kami mengaburkan atau menghapus foto itu. Admin AksesBogor melihat nama tampilan dan email anggota tim verifikator untuk mengatur peran mereka. Pengunjung AksesBogor melihat informasi tempat dan foto yang sudah diterbitkan, tanpa nama pelapor. Penyedia layanan berikut menerima data Anda. Kecuali Google, mereka memprosesnya atas nama kami:"
  ],
  "items": [
    "Server AksesBogor: situs web, API, database, cadangan harian database, log server, dan penghitung rute pejalan kaki berjalan di server yang dikelola tim kami di [ISI: kota], Indonesia. [ISI bila server disewa: Server itu disewa dari (nama penyedia), yang memproses data di server itu atas nama kami untuk menjalankan layanannya. Hapus kalimat ini bila server milik tim sendiri.]",
    "Cloudflare: semua halaman dan permintaan ke API AksesBogor melewati jaringan Cloudflare, lalu diteruskan lewat Cloudflare Tunnel ke server kami. Cloudflare menerima alamat IP Anda dan data tentang permintaan, mis. halaman yang dibuka dan waktunya. Cloudflare juga menyimpan foto laporan dan cadangan harian database di Cloudflare R2. Cadangan itu berisi seluruh isi database, termasuk email, nama tampilan, dan laporan, disimpan di bucket privat yang terpisah dari foto, dan dihapus otomatis setelah 30 hari. Menurut Kebijakan Privasi Cloudflare dan perjanjian pemrosesan datanya, Cloudflare memproses data ini atas nama kami dan tidak boleh memakainya untuk pemasaran atau iklan.",
    "Resend: mengirim email kode masuk dan pemberitahuan. Resend menerima alamat email Anda dan isi email itu, lalu mengirimnya dari region Tokyo, Jepang.",
    "Google: halaman Masuk memuat tombol dari Google, jadi Google menerima alamat IP Anda saat halaman itu dibuka. Bila Anda memakai Masuk dengan Google, Google memproses data masuk Anda menurut Kebijakan Privasi Google sendiri, bukan atas nama kami, lalu mengirimi kami nomor akun, email, dan nama akun Google Anda.",
    "Mapbox: menampilkan peta, mencari tempat, dan menghitung rute jalan kaki untuk kartu rute di Agen AI bila rute dari data OpenStreetMap di server kami belum tersedia. Mapbox menerima alamat IP dan area peta yang dilihat dari browser Anda, termasuk area di sekitar lokasi Anda setelah Tampilkan lokasi saya, serta kata kunci pencarian dan, untuk rute yang dihitung Mapbox, titik awal rute yang sudah dibulatkan dari server kami.",
    "Anthropic: menjawab pertanyaan di Agen AI lewat Claude API. Anthropic menerima pertanyaan, giliran sebelumnya, data tempat yang dipilih, hasil pencarian agen, dan jarak garis lurus yang dibulatkan ke tempat terdekat, tanpa posisi, email, nama, atau alamat IP Anda, juga bila Anda tidak punya akun. Menurut Ketentuan Komersial Anthropic, Anthropic tidak boleh memakai data ini untuk melatih model."
  ],
  "after": [
    "Kami tidak menjual data pribadi dan tidak membagikannya untuk iklan."
  ]
}
```

**Docs**: `### Siapa yang bisa melihat data Anda`. Ganti butir Oracle Cloud dengan butir server, perbarui butir Cloudflare, Resend, dan Google, lalu pindahkan tautan Oracle di bagian Referensi ke penyedia server baru bila ada.

Catatan untuk tim:

- Kalimat Street View menyatakan bahwa Google mengaburkan wajah dan pelat. Itu kebijakan Google sendiri. Bila tim ingin menjanjikan pemeriksaan ulang sebelum terbit, tambahkan langkah itu ke runbook `operations/verifikasi-street-view.md` dulu, baru ke kebijakan.
- `AGENT_PROVIDER` di API masih menerima `openrouter`. Jangan mengaktifkannya sebelum OpenRouter dan model yang dipakai masuk ke daftar ini dan ke bagian luar negeri.

### K12: transfer ke luar negeri

**Web** `KebijakanPrivasi.sections[4]` (`id: "luar-negeri"`), ganti seluruh bagian:

```json
{
  "id": "luar-negeri",
  "title": "Data yang diproses di luar Indonesia",
  "paragraphs": [
    "Situs web, API, database, dan log server kami berjalan di server di Indonesia. Resend, Google, Mapbox, Cloudflare, dan Anthropic bisa memproses data di luar Indonesia, termasuk di Amerika Serikat. Resend mengirim email kami dari Tokyo, Jepang. Anthropic menyimpan data di Amerika Serikat dan bisa menjalankan modelnya di negara lain. Foto laporan dan cadangan database di Cloudflare R2 disimpan di [ISI: lokasi bucket akses-bogor, akses-bogor-foto, dan akses-bogor-backup menurut dasbor Cloudflare].",
    "Kami hanya mengirim data yang dibutuhkan layanan itu: email dan isi email ke Resend, lalu lintas situs web dan API lewat Cloudflare, alamat IP, area peta, kata kunci pencarian, dan titik awal rute yang dihitung Mapbox ke Mapbox, pertanyaan Agen AI ke Anthropic, serta foto laporan dan cadangan harian database ke Cloudflare R2.",
    "UU PDP mewajibkan kami memastikan data yang dikirim ke luar Indonesia tetap terlindungi setara dengan UU PDP. [ISI: daftar penyedia yang perjanjian pemrosesan datanya benar-benar berlaku untuk akun tim, mis. Cloudflare dan Anthropic] terikat perjanjian pemrosesan data yang mewajibkan mereka melindungi data dan hanya memprosesnya atas nama kami. Bila pelindungan itu belum memadai, dasar pengiriman ini adalah persetujuan yang Anda berikan saat melengkapi akun."
  ]
}
```

**Docs**: `### Data yang diproses di luar Indonesia`, tiga paragraf yang sama.

Catatan untuk tim: paragraf ketiga mengikuti jenjang Pasal 56 ayat (2)–(4). Isi daftar penyedia hanya dengan yang perjanjian pemrosesan datanya sudah diperiksa, misalnya DPA Cloudflare dan DPA dalam Ketentuan Komersial Anthropic. Periksa juga apakah Resend dan Mapbox punya DPA yang berlaku untuk paket tim. Persetujuan sebagai cadangan hanya berlaku bagi pemilik akun, jadi transfer untuk pengunjung tanpa akun (IP ke Cloudflare dan Mapbox, pertanyaan ke Anthropic) bergantung pada perjanjian tadi.

### K13a: keterangan kesehatan di Agen AI

**Web** `KebijakanPrivasi.sections[1].items[5]` (Agen AI): sisipkan dua kalimat ini setelah kalimat yang berakhir "…OpenStreetMap, dan basis pengetahuan.":

> Pertanyaan Anda bisa memuat keterangan tentang kebutuhan Anda, mis. bahwa Anda memakai kursi roda. Keterangan seperti itu bisa termasuk data kesehatan, jadi tulis seperlunya dan jangan sertakan nama atau kontak.

**Docs**: butir **Agen AI**, setelah kalimat "Bersama pertanyaan ikut data tempat…".

**Web** `Agen.kosong.notice` (opsional, tidak menaikkan versi):

```json
{
  "notice": "Pertanyaan dikirim lewat server AksesBogor ke Anthropic (Claude API) dan tidak disimpan AksesBogor. Jangan tulis data pribadi, mis. nama, nomor telepon, atau alamat rumah. Keterangan tentang kebutuhan Anda, mis. memakai kursi roda, cukup ditulis seperlunya. Baca <link>Kebijakan Privasi</link>."
}
```

### K14: anak

**Web** `KebijakanPrivasi.sections[8]` (`id: "anak"`), ganti seluruh bagian:

```json
{
  "id": "anak",
  "title": "Anak",
  "paragraphs": [
    "Bila Anda berusia di bawah 18 tahun, data pribadi Anda termasuk data anak. UU PDP mewajibkan data anak diproses secara khusus dan dengan persetujuan orang tua atau wali.",
    "Minta orang tua atau wali Anda membaca kebijakan ini dan mencentang persetujuan bersama Anda sebelum membuat akun. Kotak persetujuan memuat pernyataan bahwa orang tua atau wali Anda ikut menyetujuinya, dan pernyataan itu kami simpan bersama waktu persetujuan. Bila Anda bertanya kepada Agen AI, lakukan bersama orang tua atau wali Anda dan jangan menulis data pribadi di pertanyaan.",
    "Bila kami mengetahui sebuah akun dibuat oleh anak tanpa persetujuan orang tua atau wali, kami menghapus akun itu dan memberi tahu pemiliknya lewat email."
  ]
}
```

**Web** `Lengkapi.consentLabel`, ganti:

```json
{
  "consentLabel": "Saya sudah membaca dan menyetujui Kebijakan Privasi AksesBogor, termasuk pengiriman data ke penyedia layanan di luar Indonesia. Bila saya berusia di bawah 18 tahun, orang tua atau wali saya ikut menyetujuinya."
}
```

Label baru ini juga merekam persetujuan transfer ke luar negeri (K12). Karena teks label berubah, versi kebijakan yang disimpan bersama persetujuan menjadi bukti label mana yang dicentang. Simpan teks label lama dan baru di Riwayat Versi docs.

Opsional (Pasal 22 ayat (4) huruf b dan c): kunci baru `Lengkapi.privacySummary` di atas kotak centang. Ini butuh perubahan komponen Lengkapi Akun:

> Ringkasnya: kami menyimpan email, nama tampilan, dan laporan Anda untuk menjalankan akun dan memeriksa laporan. Email dikirim lewat Resend, foto dan cadangan disimpan di Cloudflare R2, dan sebagian penyedia ini berada di luar Indonesia. Anda bisa menghapus akun kapan saja di Akun Saya.

### K15: penyandang disabilitas

**Web** `KebijakanPrivasi.sections`: bagian baru, disisipkan setelah `anak` (indeks 9, sehingga `perubahan` dan `kontak` bergeser menjadi 10 dan 11):

```json
{
  "id": "penyandang-disabilitas",
  "title": "Penyandang disabilitas dan pendamping",
  "paragraphs": [
    "Banyak pengguna AksesBogor adalah penyandang disabilitas. Kami tidak menanyakan apakah Anda penyandang disabilitas dan tidak menyimpan keterangan itu di akun Anda.",
    "Halaman ini dibuat supaya bisa dibaca dengan pembaca layar dan diperbesar. Bila Anda butuh penjelasan kebijakan ini dengan cara lain, mis. [ISI: cara yang benar-benar disediakan tim, mis. penjelasan lewat email dengan bahasa yang lebih sederhana], hubungi kami lewat kontak di bawah. Pendamping boleh membantu Anda membaca kebijakan ini, mencentang persetujuan, dan mengajukan permintaan atas data Anda. Bila Anda didampingi wali, persetujuan diberikan oleh Anda bersama wali Anda."
  ]
}
```

**Docs**: `### Penyandang disabilitas dan pendamping` setelah `### Anak`.

Id `penyandang-disabilitas` dipilih supaya tidak tertukar dengan halaman `/disabilitas`.

### S1: izin atas laporan dan foto di Syarat Layanan

**Web** `SyaratLayanan.sections[5]` (`id: "hak"`), ganti `after`:

```json
{
  "after": [
    "Izin ini tetap berlaku untuk bagian yang sudah terbit setelah Anda menghapus akun, sehingga informasi tempat tetap bisa dipakai orang lain. Anda menyatakan bahwa Anda berhak memberi izin ini, misalnya karena foto itu Anda ambil sendiri.",
    "Izin ini hanya untuk laporan dan foto sebagai informasi tempat. Izin ini bukan persetujuan atas data pribadi Anda: pemrosesan data pribadi diatur di Kebijakan Privasi, dan persetujuan itu bisa Anda tarik kapan saja. Bila laporan atau foto yang sudah terbit ternyata memuat data pribadi Anda atau orang lain, mis. wajah, pelat nomor, nama, atau nomor telepon, hubungi kami dan kami mengaburkan atau menghapus bagian itu."
  ]
}
```

**Docs**: `product/syarat-layanan.md`, bagian hak atas laporan dan foto.

Perubahan ini menyentuh hak pelapor, jadi menurut ADR-0040 `TERMS_VERSION` ikut naik dan dirilis bersama kenaikan versi Kebijakan Privasi.

## Perubahan versi dan persetujuan ulang

**Ya, perlu naik versi dan meminta persetujuan ulang.** Alasannya:

1. **Pasal 20 ayat (2) huruf a**: persetujuan sah untuk tujuan "yang telah disampaikan" kepada subjek. Pengguna yang menyetujui versi 2026-11-26 menyetujui server Oracle di Batam dan tidak tahu ada cadangan seluruh database di Cloudflare R2. Penerima dan tempat pemrosesan baru perlu disetujui ulang.
2. **Pasal 21 ayat (2)**: perubahan informasi wajib diberitahukan "sebelum terjadi perubahan". Untuk server dan cadangan R2 itu sudah terlambat. Langkah terbaik sekarang: rilis versi baru, kirim email penjelasan ke semua pemilik akun pada hari yang sama, dan mulai sekarang beri tahu sebelum perubahan (teks K5).
3. **Pasal 56 ayat (4)**: bila perlindungan setara atau perjanjian mengikat tidak cukup, transfer bergantung pada persetujuan. Label persetujuan baru (K14) menyebut transfer itu secara tegas.
4. **Pasal 25 dan 24**: pernyataan orang tua atau wali baru terekam setelah pengguna mencentang label baru.
5. Aturan proyek sendiri: setiap perubahan isi menaikkan versi (`product/kebijakan-privasi.md`), dan perubahan hak pelapor di Syarat Layanan dirilis bersama kenaikan versi Kebijakan Privasi (ADR-0040).

Cara merilis:

- Gabungkan semua perubahan K1–K15 dan S1–S3 dalam satu kenaikan versi, supaya pengguna hanya sekali diminta menyetujui ulang.
- Versi: `2026-11-27`, atau tanggal rilis bila lebih lambat. Catatan docs menyebut `2026-11-27` sebagai cadangan untuk rute OSM (ADR-0055), tetapi rute itu sudah masuk ke `2026-11-26`, jadi tanggal ini belum terpakai. Naikkan `PRIVACY_POLICY_VERSION` (web `src/lib/kebijakan.ts`, API, docs) dan `TERMS_VERSION` ke tanggal yang sama.
- Urutan: web dulu, lalu `PRIVACY_POLICY_VERSION` di API beberapa menit kemudian. Bila API lebih dulu, web lama mengirim versi lama dan ditolak dengan 409.
- Tambahkan baris ke Riwayat Versi docs, mis.: `| 2026-11-27 | Server pindah dari Oracle Cloud ke server tim di Indonesia; cadangan harian database di Cloudflare R2; dasar pemrosesan per jenis data; hak subjek data lengkap dengan tenggat 3 × 24 jam; pemberitahuan kegagalan pelindungan ke lembaga; dasar transfer ke luar negeri; persetujuan orang tua atau wali; penyandang disabilitas; kontak | [ISI: nomor issue] |`.
- Perubahan yang tidak perlu menaikkan versi bila dirilis sendiri: A1 (`Agen.awal.privacy`) dan `Agen.kosong.notice`. A1 sebaiknya dirilis secepatnya.
- Label "Draf" di kedua halaman ("Teks ini masih ditinjau tim dan bisa berubah sebelum AksesBogor diluncurkan") sebaiknya dicabut setelah peninjauan hukum. Persetujuan atas versi berlabel draf tetap terekam, tetapi label itu membuat pengguna ragu teks mana yang berlaku.

## Perbedaan web dan docs

Isi Kebijakan Privasi di web (`messages/id.json`) dan docs (`product/kebijakan-privasi.md`) sama untuk versi 2026-11-26, dan sama di cabang `main` dan `release` web. Perbedaannya:

| Hal | Web | Docs |
| --- | --- | --- |
| Format | Satu butir panjang per jenis data, tanpa huruf tebal; butir Agen AI satu paragraf | Label tebal, butir Agen AI dipecah menjadi sub-butir |
| Tautan | Nama dokumen penyedia tanpa tautan | Tautan ke Kebijakan Privasi Oracle dan Cloudflare, DPA Cloudflare, dan Ketentuan Komersial Anthropic |
| Kalimat kecil | "Anda juga bisa memilih titik awal di peta." | "Anda juga bisa memilih titik di peta." |
| Kontak | "Email kontak resmi diisi tim sebelum peluncuran." | "Email: (diisi tim sebelum rilis)." |
| Bagian khusus tim | Tidak ada | "Sebelum Rilis", "Riwayat Versi", "Referensi" |

Yang usang di docs, di luar teks kebijakan itu sendiri:

- "Sebelum Rilis" masih menyebut "log server di Evergarden". Butir rilis versi 2026-11-24, 2026-11-26, dan rute OSM belum dicentang, padahal versi 2026-11-26 sudah berjalan di `release`.
- Referensi masih memuat Oracle dan ADR-0026/ADR-0048 (Evergarden). Belum ada ADR untuk pemindahan produksi ke fatir-mitya.
- Status dokumen "Draf, belum ditinjau".

Syarat Layanan di web dan docs juga sama (versi 2026-11-10). Bedanya hanya kalimat placeholder kontak ("Email kontak resmi diisi tim sebelum peluncuran." lawan "(diisi tim sebelum rilis).").

Di luar kedua dokumen: `Agen.awal.privacy` di web tidak sesuai dengan kebijakan (lihat A1).

## Kewajiban di luar teks kebijakan

Butir berikut tidak bisa dipenuhi dengan mengubah teks, tetapi muncul saat pemeriksaan:

- **Pasal 31, catatan kegiatan pemrosesan**: "Pengendali Data Pribadi wajib melakukan perekaman terhadap seluruh kegiatan pemrosesan". Daftar data dan penerima di kebijakan bisa menjadi dasarnya. Simpan sebagai dokumen internal di docs `operations/`.
- **Pasal 34, penilaian dampak**: wajib bila risikonya tinggi, antara lain "penggunaan teknologi baru" (huruf f, Agen AI dengan model bahasa) dan "Data Pribadi yang bersifat spesifik" (huruf b, data anak dan keterangan kesehatan di pertanyaan). Rinciannya menunggu PP (ayat (3)), tetapi penilaian singkat sebaiknya dibuat sekarang.
- **Pasal 35 dan 39, keamanan cadangan**: dump harian di R2 berisi seluruh data pribadi. Token R2 yang terbatas melindungi dari kebocoran token, tetapi tidak dari akun Cloudflare yang dibobol. Pertimbangkan mengenkripsi dump sebelum diunggah, mis. dengan `age`, dengan kunci privat di luar Cloudflare. Runbook di docs PR #149 belum menyebut enkripsi.
- **Pasal 37 dan 51 ayat (5), pengawasan prosesor**: periksa dan arsipkan DPA yang berlaku untuk Cloudflare, Anthropic, Resend, Mapbox, dan penyedia server fatir-mitya bila disewa, termasuk aturan subprosesornya.
- **Pasal 46, prosedur kegagalan**: tulis runbook berisi siapa yang menilai kejadian, templat email ke pengguna, cara menghubungi lembaga, dan cara mengumumkan di Beranda, supaya 3 × 24 jam bisa dipenuhi.
- **Pasal 30, 32, 40, 41, tenggat 3 × 24 jam**: siapkan skrip ekspor JSON per akun, ganti email, tahan laporan, dan matikan email pemberitahuan per akun (lihat catatan K6).
- **OpenRouter**: kunci `AGENT_PROVIDER=openrouter` sebaiknya tidak dipakai di produksi tanpa memperbarui kebijakan dulu (lihat K11).

Di luar cakupan UU PDP, tetapi terlihat: Syarat Layanan belum menyebut Agen AI. Contohnya, "Akun hanya dibutuhkan untuk melapor" padahal Agen AI punya fitur tambahan untuk yang masuk, dan belum ada batas tanggung jawab untuk jawaban AI.

## Placeholder yang harus diisi tim

| Placeholder | Dipakai di |
| --- | --- |
| Program studi dan nama kampus | K1 `siapa-kami` |
| Nama dan peran penanggung jawab pelindungan data pribadi | K1 `siapa-kami`, `kontak` |
| Email kontak resmi | K1 `kontak`, S2 |
| Alamat surat | K1 `kontak`, S2 |
| Kota lokasi server fatir-mitya, dan penyedia server bila disewa | K11 `penerima.items[0]` |
| Lokasi bucket R2 (`akses-bogor`, `akses-bogor-foto`, `akses-bogor-backup`) | K12 `luar-negeri` |
| Penyedia dengan DPA yang berlaku | K12 `luar-negeri` |
| Masa simpan log dan email di Resend | K8 tabel |
| Masa simpan log Cloudflare | K8 tabel |
| Jumlah hari pemberitahuan sebelum perubahan | K5 `perubahan` |
| Jumlah hari pemberitahuan sebelum layanan dihentikan | K17 `perubahan`, S3 |
| Cara penjelasan alternatif bagi penyandang disabilitas | K15 |
| Nomor issue untuk Riwayat Versi | Perubahan versi |

## Pasal yang dibaca

Sumber: UU No. 27 Tahun 2022 lewat pasal.id (`law_id` 16), sumber resmi [peraturan.go.id/id/uu-no-27-tahun-2022](https://peraturan.go.id/id/uu-no-27-tahun-2022), status berlaku, teks terverifikasi (tingkat "golden_certified"). Halaman baca: [pasal.id/peraturan/uu/uu-no-27-tahun-2022](https://pasal.id/peraturan/uu/uu-no-27-tahun-2022).

| Pasal | Isi singkat | Tautan |
| --- | --- | --- |
| Pasal 1 | Definisi: Data Pribadi, Pengendali, Prosesor, Setiap Orang, Korporasi | [#pasal-1](https://pasal.id/peraturan/uu/uu-no-27-tahun-2022#pasal-1) |
| Pasal 2 | Ruang lingkup berlaku | [#pasal-2](https://pasal.id/peraturan/uu/uu-no-27-tahun-2022#pasal-2) |
| Pasal 4 dan Penjelasan | Data spesifik (kesehatan, biometrik, anak) dan umum; IP sebagai data pribadi | [#pasal-4](https://pasal.id/peraturan/uu/uu-no-27-tahun-2022#pasal-4) |
| Pasal 5–15 | Hak subjek data dan pengecualiannya | [#pasal-5](https://pasal.id/peraturan/uu/uu-no-27-tahun-2022#pasal-5) s.d. [#pasal-15](https://pasal.id/peraturan/uu/uu-no-27-tahun-2022#pasal-15) |
| Pasal 16 | Ruang lingkup dan prinsip pemrosesan | [#pasal-16](https://pasal.id/peraturan/uu/uu-no-27-tahun-2022#pasal-16) |
| Pasal 17–19 | Data visual di tempat umum, pengendali bersama, bentuk pengendali | [#pasal-17](https://pasal.id/peraturan/uu/uu-no-27-tahun-2022#pasal-17) |
| Pasal 20 dan Penjelasan | Dasar pemrosesan | [#pasal-20](https://pasal.id/peraturan/uu/uu-no-27-tahun-2022#pasal-20) |
| Pasal 21 | Informasi wajib dan pemberitahuan perubahan | [#pasal-21](https://pasal.id/peraturan/uu/uu-no-27-tahun-2022#pasal-21) |
| Pasal 22–24 | Bentuk, syarat, dan bukti persetujuan | [#pasal-22](https://pasal.id/peraturan/uu/uu-no-27-tahun-2022#pasal-22) |
| Pasal 25–26 dan Penjelasan | Anak dan penyandang disabilitas | [#pasal-25](https://pasal.id/peraturan/uu/uu-no-27-tahun-2022#pasal-25), [#pasal-26](https://pasal.id/peraturan/uu/uu-no-27-tahun-2022#pasal-26) |
| Pasal 27–45 | Kewajiban pengendali: terbatas, akurat, perbaikan, akses, penilaian dampak, keamanan, penghentian, penghapusan, pemusnahan, pemberitahuan | [#pasal-27](https://pasal.id/peraturan/uu/uu-no-27-tahun-2022#pasal-27) s.d. [#pasal-45](https://pasal.id/peraturan/uu/uu-no-27-tahun-2022#pasal-45) |
| Pasal 46 dan Penjelasan | Pemberitahuan kegagalan pelindungan dalam 3 × 24 jam | [#pasal-46](https://pasal.id/peraturan/uu/uu-no-27-tahun-2022#pasal-46) |
| Pasal 47–50 | Akuntabilitas, pengalihan saat pembubaran, perintah lembaga, pengecualian | [#pasal-47](https://pasal.id/peraturan/uu/uu-no-27-tahun-2022#pasal-47) |
| Pasal 51–52 | Prosesor | [#pasal-51](https://pasal.id/peraturan/uu/uu-no-27-tahun-2022#pasal-51) |
| Pasal 53–54 dan Penjelasan Pasal 53 | Pejabat atau petugas PDP | [#pasal-53](https://pasal.id/peraturan/uu/uu-no-27-tahun-2022#pasal-53) |
| Pasal 55–56 dan Penjelasan Pasal 56 | Transfer di dalam dan ke luar wilayah RI | [#pasal-56](https://pasal.id/peraturan/uu/uu-no-27-tahun-2022#pasal-56) |
| Pasal 58–60 | Lembaga, tugas, dan wewenang (termasuk menerima aduan) | [#pasal-58](https://pasal.id/peraturan/uu/uu-no-27-tahun-2022#pasal-58), [#pasal-60](https://pasal.id/peraturan/uu/uu-no-27-tahun-2022#pasal-60) |
| Pasal 64 | Penyelesaian sengketa | [#pasal-64](https://pasal.id/peraturan/uu/uu-no-27-tahun-2022#pasal-64) |
| Pasal 74 | Masa penyesuaian 2 tahun | [#pasal-74](https://pasal.id/peraturan/uu/uu-no-27-tahun-2022#pasal-74) |

Peraturan lain:

- Pasal 1 angka 1 UU No. 23 Tahun 2002 tentang Perlindungan Anak (definisi anak, belum berusia 18 tahun): [pasal.id/peraturan/uu/uu-no-23-tahun-2002#pasal-1](https://pasal.id/peraturan/uu/uu-no-23-tahun-2002#pasal-1). Teks di pasal.id untuk UU ini ditandai **belum terverifikasi** terhadap sumber resmi (`source.verified: false`), dan status keberlakuannya di pasal.id tidak pasti karena UU ini sudah diubah (UU No. 35 Tahun 2014). Definisi anak di Pasal 1 angka 1 tidak termasuk yang diubah menurut teks yang dibaca, tetapi ini tidak diperiksa terhadap naskah perubahannya.
- Pencarian pasal.id untuk peraturan pelaksana UU PDP (PP, Perpres, Permen sejak 2022) tidak menemukan PP pelaksana. Rincian yang didelegasikan ke PP tidak ikut diperiksa.
