# PRD: AksesBogor MVP

| | |
| --- | --- |
| Status | Draft |
| Versi | 0.2 |
| Tanggal | 2026-10-21 |
| Pemilik | Tim AksesBogor |
| Issue | #14 |

Bagian yang ditandai *(usulan)* belum menjadi keputusan dan perlu disepakati lewat review PR atau ADR.

## 1. Ringkasan

AksesBogor adalah web mobile-first yang membantu difabel dan pendampingnya mengetahui aksesibilitas fasilitas publik di Kota Bogor sebelum berangkat. Data lokasi berasal dari laporan warga yang sudah login, lalu diverifikasi tim sebelum tayang. MVP mencakup empat kategori tempat (Kampus, Masjid, Halte, Rumah Sakit) dan empat fasilitas (Ramp, Toilet Difabel, Guiding Block, Lift), dibangun dengan design system kaca "Ungu Inklusi".

## 2. Masalah

- Difabel sulit mengetahui sebelum berangkat apakah sebuah tempat bisa dipakai: ada ramp atau tidak, toilet difabel, guiding block, atau lift *(asumsi, perlu divalidasi lewat riset pengguna)*.
- Akibatnya perjalanan bisa sia-sia, pengguna bergantung pada orang lain, atau terjebak di lokasi yang ternyata tidak aksesibel.
- Informasi yang ada tersebar dan tidak terverifikasi, sehingga sulit dipercaya *(asumsi)*.

## 3. Tujuan dan Non-tujuan

### Tujuan

| ID | Tujuan |
| --- | --- |
| G1 | Pengguna bisa menilai aksesibilitas sebuah tempat di Kota Bogor dalam waktu kurang dari satu menit sejak membuka situs. |
| G2 | Warga bisa melaporkan tempat dari ponsel, langsung di lokasi, dalam waktu kurang dari tiga menit. |
| G3 | Semua data yang tayang sudah diverifikasi tim, dan pengguna bisa melihat kapan terakhir diverifikasi. |
| G4 | Produk sendiri memenuhi WCAG 2.2 level AA *(usulan target, lihat PT-05)*. |

### Non-tujuan untuk MVP

- Wilayah di luar Kota Bogor ([ADR-0010](../decisions/0010-cakupan-wilayah-kota-bogor.md)).
- Aplikasi native Android atau iOS ([ADR-0012](../decisions/0012-web-mobile-first-pwa.md)).
- Navigasi rute langkah demi langkah.
- Ulasan bebas, rating bintang, komentar, atau forum.
- Kategori dan fasilitas di luar daftar MVP ([ADR-0004](../decisions/0004-empat-kategori-lokasi-untuk-mvp.md), [ADR-0005](../decisions/0005-empat-fasilitas-aksesibilitas.md)).
- Antarmuka dwibahasa.

## 4. Pengguna

| Peran | Kebutuhan utama | Kebutuhan aksesibilitas |
| --- | --- | --- |
| Pengguna kursi roda | Tahu ada ramp, lift, dan toilet difabel sebelum berangkat | Target sentuh besar; informasi tersedia di peta dan daftar |
| Pengguna tunanetra dan low vision | Tahu ada guiding block; memahami status tempat | Semua informasi berupa teks untuk pembaca layar; kontras AA; zoom 200% |
| Pendamping dan keluarga | Merencanakan kunjungan bersama difabel | Ringkasan cepat di halaman detail |
| Kontributor (warga, mahasiswa) | Melapor cepat dari ponsel di lokasi | Login dan formulir yang sederhana |
| Verifikator (tim AksesBogor) | Memeriksa, memperbaiki, dan menerbitkan laporan | Antrean yang jelas dan alasan penolakan yang terstandar |

## 5. Cakupan MVP

| Area | Isi | Desain |
| --- | --- | --- |
| Beranda | Kategori, edukasi singkat, statistik dampak, ajakan melapor | Landing · DS v1 |
| Explore | Pencarian, filter, daftar lokasi | Explore · DS v1 |
| Peta | Marker per status, legenda, tampilan daftar | Map · DS v1 |
| Detail lokasi | Status, fasilitas, skor, foto, peta mini | Detail · DS v1 |
| Akun | Daftar, masuk, keluar, profil, hapus akun | Belum ada |
| Laporkan tempat | Formulir tiga langkah, edit lokasi, laporkan kekurangan | Laporkan · Langkah 1–3 · DS v1, Laporan Berhasil · DS v1 |
| Panel verifikator | Antrean, pemeriksaan, terbit atau tolak | Belum ada |
| Notifikasi | Email status laporan | Belum ada |
| Halaman statis | Tentang, Kebijakan Privasi, Syarat Layanan | Belum ada |

Semua layar dirancang mulai dari lebar 360 px ([ADR-0012](../decisions/0012-web-mobile-first-pwa.md)). Frame DS v1 yang ada saat ini baru versi desktop.

## 6. Kebutuhan Fungsional

Prioritas memakai MoSCoW: Must (wajib untuk MVP), Should (sebaiknya ada), Could (bila sempat).

### Penemuan lokasi

| ID | Kebutuhan | Prioritas |
| --- | --- | --- |
| FR-01 | Beranda menampilkan empat kategori dengan jumlah lokasi terverifikasi dan statistik dampak yang dihitung dari data, bukan angka tetap. | Must |
| FR-02 | Pengguna bisa mencari lokasi berdasarkan nama, termasuk kecocokan sebagian kata. | Must |
| FR-03 | Pengguna bisa memfilter berdasarkan kategori (boleh lebih dari satu), kecamatan, fasilitas yang tersedia, dan status. | Must |
| FR-04 | Hasil tampil sebagai Location card; jumlah hasil sesuai data yang tampil. | Must |
| FR-05 | Peta Kota Bogor menampilkan marker per status (warna, ikon, dan label di legenda), kontrol zoom, dan tombol ke tampilan daftar dengan filter yang sama. | Must |
| FR-06 | Detail lokasi menampilkan nama, kategori, kecamatan, status, keempat fasilitas beserta ketersediaannya, deskripsi, foto, peta mini, dan tanggal verifikasi terakhir. | Must |
| FR-07 | Detail menampilkan Accessibility Score setelah rumusnya diputuskan (PT-02). | Should |
| FR-08 | Pengguna bisa membagikan tautan lokasi. | Could |

### Akun ([ADR-0011](../decisions/0011-pelapor-wajib-login.md))

| ID | Kebutuhan | Prioritas |
| --- | --- | --- |
| FR-10 | Pengguna bisa daftar dan masuk dengan Google atau kode 6 digit lewat email ([ADR-0014](../decisions/0014-login-google-dan-kode-email.md)), tanpa tes kognitif seperti CAPTCHA teka-teki. | Must |
| FR-11 | Saat daftar, pengguna menyetujui Kebijakan Privasi. | Must |
| FR-12 | Profil minimal: nama tampilan dan email. | Must |
| FR-13 | Pengguna bisa menghapus akun beserta data pribadinya. | Must |
| FR-14 | Halaman "Laporan saya" menampilkan status setiap laporan: menunggu verifikasi, diterbitkan, atau ditolak beserta alasannya. | Should |

### Pelaporan ([ADR-0006](../decisions/0006-laporan-warga-diverifikasi-sebelum-tayang.md))

| ID | Kebutuhan | Prioritas |
| --- | --- | --- |
| FR-20 | Tombol "Laporkan tempat" ada di semua halaman. Pengguna yang belum login diarahkan ke login, lalu kembali ke formulir tanpa kehilangan isian. | Must |
| FR-21 | Langkah 1 meminta nama tempat, kategori (pilih satu), dan lokasi. Cara input lokasi mengikuti PT-07; *(usulan)* pilih titik di peta dengan opsi memakai lokasi perangkat. | Must |
| FR-22 | Lokasi di luar Kota Bogor ditolak dengan pesan yang menjelaskan cakupan wilayah. | Must |
| FR-23 | Sebelum lanjut, sistem menampilkan lokasi terdaftar di dekatnya yang namanya mirip untuk mencegah laporan ganda. | Should |
| FR-24 | Langkah 2 meminta ketersediaan keempat fasilitas. *(usulan)* Setiap fasilitas punya tiga pilihan: Tersedia, Tidak tersedia, Belum tahu, dengan bawaan Belum tahu. | Must |
| FR-25 | Langkah 3 menerima foto JPG atau PNG maksimal 5 MB per foto *(usulan: hingga 3 foto)*, deskripsi tambahan, dan menampilkan ringkasan sebelum dikirim. | Must |
| FR-26 | Setelah kirim, pengguna melihat konfirmasi dan langkah selanjutnya. | Must |
| FR-27 | "Edit lokasi" dan "Laporkan kekurangan" di halaman Detail membuka formulir yang sama, terisi data saat ini, dan masuk antrean verifikasi. | Must |

### Verifikasi ([ADR-0006](../decisions/0006-laporan-warga-diverifikasi-sebelum-tayang.md))

| ID | Kebutuhan | Prioritas |
| --- | --- | --- |
| FR-30 | Verifikator melihat antrean laporan baru dan laporan perubahan, diurutkan dari yang terlama. | Must |
| FR-31 | Halaman laporan menampilkan semua isian, foto, titik di peta, dan data lokasi saat ini untuk dibandingkan. | Must |
| FR-32 | Verifikator bisa menerbitkan, meminta klarifikasi, atau menolak dengan alasan dari daftar baku. | Must |
| FR-33 | Verifikator menetapkan status aksesibilitas. Setelah PT-02 diputuskan, status dan skor dihitung sesuai aturannya. | Must |
| FR-34 | Verifikator bisa menyembunyikan atau mengaburkan foto yang memperlihatkan wajah atau pelat nomor. | Should |
| FR-35 | Setiap perubahan data lokasi tercatat di log audit (siapa, kapan, apa). | Should |
| FR-36 | Peran verifikator hanya bisa diberikan oleh admin. | Must |

### Notifikasi dan konten

| ID | Kebutuhan | Prioritas |
| --- | --- | --- |
| FR-40 | Email dikirim saat laporan diterbitkan atau ditolak (beserta alasan). | Must |
| FR-41 | Email dikirim saat verifikator meminta klarifikasi. | Should |
| FR-50 | Halaman Tentang, Kebijakan Privasi, dan Syarat Layanan tersedia sebelum peluncuran. | Must |
| FR-51 | Halaman edukasi (Accessibility, Difabel, Tips). | Could |

## 7. Kebutuhan Non-fungsional

| Area | Kebutuhan |
| --- | --- |
| Aksesibilitas | Target WCAG 2.2 AA *(usulan, PT-05)*: navigasi keyboard penuh, label dan landmark yang benar, cincin fokus terlihat, status selalu berupa teks + ikon + warna, peta punya alternatif daftar, tampilan tetap utuh di lebar 320 px dan zoom 200%, target sentuh minimal 44 × 44 px. |
| Preferensi pengguna | `prefers-reduced-transparency` memakai mode Solid, `prefers-color-scheme: dark` memakai mode Dark, `prefers-reduced-motion` dihormati ([ADR-0009](../decisions/0009-design-system-kaca-ungu-inklusi.md)). |
| Pengujian aksesibilitas | Uji dengan TalkBack dan VoiceOver, plus uji kegunaan bersama pengguna difabel sebelum beta. |
| Mobile dan performa | Dirancang mulai 360 px. *(usulan)* Largest Contentful Paint ≤ 2,5 detik di jaringan 4G pada ponsel kelas menengah; gambar dikompres dan dimuat bertahap; blur dimatikan pada perangkat lemah. |
| PWA | Bisa dipasang ke layar utama. Halaman yang pernah dibuka tetap tampil saat offline (Could). |
| Privasi dan keamanan | Sesuai UU No. 27 Tahun 2022: data pribadi minimal, HTTPS, metadata lokasi (EXIF) dihapus dari foto, dan akun bisa dihapus. |
| Bahasa | Bahasa Indonesia *(usulan, PT-04)*. |
| Browser | Chrome Android dan Safari iOS dua versi terakhir; Chrome, Firefox, Safari, dan Edge versi terbaru di desktop. |
| Peta | Atribusi OpenStreetMap selalu tampil; tile dari penyedia yang sesuai kebijakan pemakaian ([ADR-0008](../decisions/0008-peta-leaflet-dan-openstreetmap.md)). |
| Konsistensi UI | Semua layar memakai komponen dan token dari design system Figma ([ADR-0009](../decisions/0009-design-system-kaca-ungu-inklusi.md)). |
| Tech stack | Next.js di Vercel, API Fastify di Azure App Service B1, Neon Postgres + PostGIS, foto di Cloudflare R2 ([ADR-0013](../decisions/0013-tech-stack-nextjs-fastify.md)). |

## 8. Alur Utama

### A. Menemukan tempat

1. Pengguna membuka Beranda atau Explore.
2. Mencari nama tempat atau memfilter kategori, kecamatan, dan fasilitas.
3. Membuka Detail dan membaca status, fasilitas, foto, serta tanggal verifikasi.

### B. Melaporkan tempat baru

1. Pengguna menekan "Laporkan tempat" dan login bila belum.
2. Mengisi tiga langkah: informasi dan lokasi, fasilitas, foto dan deskripsi.
3. Mengirim laporan dan melihat konfirmasi.
4. Menerima email saat laporan diterbitkan atau ditolak.

### C. Melaporkan kekurangan

1. Dari Detail, pengguna memilih "Laporkan kekurangan".
2. Formulir terisi data saat ini; pengguna mengubah fasilitas atau menambah foto dan catatan.
3. Laporan masuk antrean verifikasi; data lama tetap tayang sampai perubahan diterbitkan.

### D. Memverifikasi laporan

1. Verifikator membuka antrean dan memilih laporan terlama.
2. Membandingkan laporan dengan data saat ini dan foto.
3. Menerbitkan, meminta klarifikasi, atau menolak dengan alasan.

## 9. Model Data (ringkas)

| Entitas | Atribut utama |
| --- | --- |
| Pengguna | id, nama tampilan, email, peran (pengguna, verifikator, admin), waktu persetujuan privasi |
| Lokasi | id, nama, kategori, kecamatan, kota, koordinat, status, skor, deskripsi, tanggal verifikasi terakhir |
| Fasilitas lokasi | lokasi, jenis fasilitas, ketersediaan (Tersedia, Tidak tersedia, Belum diketahui) |
| Laporan | id, pelapor, jenis (baru, perubahan, kekurangan), lokasi terkait, isian, status (menunggu, klarifikasi, diterbitkan, ditolak), alasan, waktu |
| Foto | id, laporan, lokasi, berkas, status moderasi |
| Log audit | entitas, pengubah, waktu, perubahan |

## 10. Metrik Keberhasilan

Target *(usulan)* tiga bulan setelah beta dibuka:

| Metrik | Target |
| --- | --- |
| Lokasi terverifikasi di Kota Bogor (metrik utama) | ≥ 150 |
| Kontributor dengan minimal satu laporan diterbitkan | ≥ 50 |
| Median waktu dari laporan dikirim sampai diputuskan | ≤ 3 hari kerja |
| Proporsi laporan yang diterbitkan | ≥ 70% |
| Keberhasilan tugas "temukan tempat X dan sebutkan fasilitasnya" dalam uji kegunaan dengan minimal lima pengguna difabel | ≥ 80% |
| Temuan WCAG level A atau AA yang kritis saat audit sebelum rilis | 0 |

## 11. Tahapan Rilis

Jadwal ada di rencana proyek terpisah; dokumen ini hanya mengatur urutan.

1. **Persiapan**: putuskan PT-02, PT-07, dan PT-09; buat desain mobile untuk delapan layar serta layar akun dan panel verifikator; kumpulkan data awal lewat survei lapangan.
2. **Alpha internal**: semua kebutuhan Must untuk penemuan, akun, pelaporan, dan verifikasi; diuji oleh tim.
3. **Beta terbuka di Kota Bogor**: notifikasi dan kebutuhan Should; uji kegunaan bersama komunitas difabel.
4. **Setelah MVP**: kebutuhan Could dan evaluasi perluasan wilayah lewat ADR baru.

## 12. Risiko dan Mitigasi

| Risiko | Mitigasi |
| --- | --- |
| Data awal terlalu sedikit sehingga produk terasa kosong | Survei lapangan oleh tim, kerja sama komunitas, dan impor data `wheelchair=*` dari OpenStreetMap (PT-12) |
| Kewajiban login menurunkan jumlah laporan | Login cepat, isian formulir tidak hilang saat diarahkan ke login |
| Antrean verifikasi menumpuk | Target waktu verifikasi, jadwal verifikator, dan alasan penolakan baku |
| Data cepat usang | Tanggal verifikasi terakhir di Detail dan alur "Laporkan kekurangan" |
| Efek kaca berat atau kontras turun di perangkat tertentu | Mode Solid dan audit kontras di setiap rilis |
| Foto memuat data pribadi orang lain | Moderasi foto dan penghapusan EXIF |
| Pemakaian tile OSM melanggar kebijakan | Penyedia tile pihak ketiga atau server sendiri |
| Kredit Azure habis lebih cepat bila langganannya dipakai bersama lokerin | Budget alert di Azure dan pemantauan pemakaian bulanan |

## 13. Keputusan Terkait

| ADR | Ringkasan |
| --- | --- |
| [0004](../decisions/0004-empat-kategori-lokasi-untuk-mvp.md) | Empat kategori lokasi |
| [0005](../decisions/0005-empat-fasilitas-aksesibilitas.md) | Empat fasilitas, tersedia atau tidak |
| [0006](../decisions/0006-laporan-warga-diverifikasi-sebelum-tayang.md) | Laporan warga diverifikasi sebelum tayang |
| [0008](../decisions/0008-peta-leaflet-dan-openstreetmap.md) | Peta Leaflet + OpenStreetMap (Proposed) |
| [0009](../decisions/0009-design-system-kaca-ungu-inklusi.md) | Design system kaca, palet Ungu Inklusi |
| [0010](../decisions/0010-cakupan-wilayah-kota-bogor.md) | Cakupan wilayah Kota Bogor |
| [0011](../decisions/0011-pelapor-wajib-login.md) | Pelapor wajib login |
| [0012](../decisions/0012-web-mobile-first-pwa.md) | Web mobile-first (PWA) |
| [0013](../decisions/0013-tech-stack-nextjs-fastify.md) | Next.js + Fastify, Neon, Azure, Cloudflare R2 |
| [0014](../decisions/0014-login-google-dan-kode-email.md) | Login dengan Google atau kode email |

## 14. Pertanyaan Terbuka

- **PT-02**: rumus status dan Accessibility Score.
- **PT-04**: bahasa antarmuka.
- **PT-05**: target WCAG resmi dan cara pengujian.
- **PT-07**: cara input lokasi di formulir.
- **PT-08**: pencatatan kondisi fasilitas.
- **PT-09**: proses dan target waktu verifikasi.
- **PT-12**: sumber data awal.
- **Baru**: pilihan tiga status per fasilitas di FR-24 mengubah [ADR-0005](../decisions/0005-empat-fasilitas-aksesibilitas.md). Bila disetujui, perlu ADR baru yang men-supersede ADR-0005.
- **Baru**: jumlah maksimum foto per laporan.

Rincian setiap pertanyaan ada di [Pertanyaan Terbuka](pertanyaan-terbuka.md).

## 15. Referensi

- [Ringkasan Produk](ringkasan-produk.md)
- [Referensi Figma](referensi-figma.md)
- [Fondasi Visual](../design/fondasi-visual.md)
- [Decision log](../decisions/README.md)
- [Figma: AksesBogor](https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor)
