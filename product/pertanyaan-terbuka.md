# Pertanyaan Terbuka

Keputusan yang belum diambil, atau yang saling bertentangan, di [desain Figma](referensi-figma.md) per 2026-10-21. Setiap pertanyaan yang sudah diputuskan ditulis sebagai ADR di [decisions/](../decisions/README.md), lalu kolom "ADR" di tabel ini diisi.

"Usulan" adalah pendapat penulis untuk memulai diskusi, bukan keputusan.

## Ringkasan

| ID | Pertanyaan | Menghambat implementasi | ADR |
| --- | --- | --- | --- |
| [PT-01](#pt-01-cakupan-wilayah) | Cakupan wilayah: Kota Bogor atau Indonesia? | Ya | [0010](../decisions/0010-cakupan-wilayah-kota-bogor.md) |
| [PT-02](#pt-02-status-dan-accessibility-score) | Bagaimana status dan Accessibility Score ditentukan? | Ya | |
| [PT-03](#pt-03-akun-pengguna-dan-notifikasi) | Apakah pelapor perlu akun? Bagaimana notifikasi dikirim? | Ya | [0011](../decisions/0011-pelapor-wajib-login.md) |
| [PT-04](#pt-04-bahasa-antarmuka) | Bahasa antarmuka aplikasi | Tidak | |
| [PT-05](#pt-05-target-aksesibilitas-antarmuka) | Target aksesibilitas antarmuka (WCAG) | Ya | [0009](../decisions/0009-design-system-kaca-ungu-inklusi.md) (sebagian) |
| [PT-06](#pt-06-tampilan-mobile) | Tampilan mobile | Ya | [0012](../decisions/0012-web-mobile-first-pwa.md) |
| [PT-07](#pt-07-input-lokasi-pada-formulir-laporan) | Bagaimana lokasi dan kota diisi di formulir laporan? | Ya | |
| [PT-08](#pt-08-kondisi-fasilitas) | Apakah kondisi fasilitas dicatat, bukan hanya ada/tidak? | Tidak | |
| [PT-09](#pt-09-proses-verifikasi-dan-moderasi) | Proses verifikasi dan moderasi laporan | Ya | |
| [PT-10](#pt-10-halaman-yang-belum-didesain) | Halaman mana yang masuk MVP? | Tidak | |
| [PT-11](#pt-11-tech-stack-produksi) | Tech stack produksi | Ya | [0013](../decisions/0013-tech-stack-nextjs-fastify.md), [0014](../decisions/0014-login-google-dan-kode-email.md) |
| [PT-12](#pt-12-data-awal) | Sumber data awal dan pengganti data placeholder | Tidak | |

## PT-01: Cakupan Wilayah

- **Bukti di desain**: nama produk dan ajakan "Bantu Kami Memetakan Aksesibilitas di Kota Bogor" ([Landing 13:2][landing]) bertentangan dengan "fasilitas publik di Indonesia" (Landing, Explore, footer). Peta berpusat di Jakarta ([Map 13:351][map]), lokasi contoh berada di Depok dan Jakarta, dan alamat kontak "Jakarta, Indonesia".
- **Opsi**: Kota Bogor; Bogor Raya (Kota dan Kabupaten); Jabodetabek; seluruh Indonesia.
- **Dampak**: copy, pusat peta default, isi filter Kota, sumber data awal, dan beban verifikasi.
- **Usulan**: mulai dari Kota Bogor sesuai nama produk, dengan model data yang tetap mendukung kota lain.
- **Status**: diputuskan di [ADR-0010](../decisions/0010-cakupan-wilayah-kota-bogor.md): Kota Bogor (enam kecamatan).

## PT-02: Status dan Accessibility Score

- **Bukti di desain**: status yang terlihat hanya "Accessible" dan "Partial" ([Explore 13:528][explore]). Detail Halte TransJakarta Bundaran HI memiliki 3 dari 4 fasilitas dan skor 65/100 berlabel "Baik" ([Detail 13:1578][detail]). Dengan bobot sama, 3 dari 4 fasilitas menjadi 75, jadi rumusnya tidak sesederhana itu, atau angkanya hanya contoh.
- **Pertanyaan**:
  - Apa rumus skor, dan apakah dihitung otomatis atau dinilai verifikator?
  - Apa label untuk setiap rentang skor?
  - Apakah ada status ketiga untuk lokasi yang tidak aksesibel?
  - Apakah status diturunkan dari skor?
- **Catatan**: OpenStreetMap memakai tag `wheelchair=yes|limited|no`, yang sejalan dengan Accessible, Partial, dan status ketiga. Menyamakan skema memudahkan impor atau berbagi data dengan OSM.
- **Usulan**: skor diturunkan dari fasilitas yang sudah terverifikasi dengan bobot yang dipublikasikan, agar pengguna bisa memahami asal angkanya.

## PT-03: Akun Pengguna dan Notifikasi

- **Bukti di desain**: Laporan Berhasil menjanjikan "Anda akan mendapat notifikasi ketika lokasi telah dipublikasikan" ([Laporan Berhasil 13:1412][berhasil]), tetapi tidak ada layar login atau registrasi, dan formulir tidak meminta kontak. Landing menampilkan statistik "Kontributor Aktif".
- **Opsi**: laporan anonim tanpa notifikasi; laporan anonim dengan email opsional untuk notifikasi; wajib login (email atau akun Google).
- **Dampak**: spam dan moderasi, kewajiban pelindungan data pribadi (UU No. 27 Tahun 2022 tentang Pelindungan Data Pribadi), cara menghitung kontributor aktif.
- **Status**: diputuskan di [ADR-0011](../decisions/0011-pelapor-wajib-login.md): pelapor wajib login; menjelajah tetap tanpa login. Metode login diputuskan di [ADR-0014](../decisions/0014-login-google-dan-kode-email.md).

## PT-04: Bahasa Antarmuka

- **Bukti di desain**: copy bercampur. Bahasa Inggris: "Explore Locations", "Map View", "Accessibility Score", menu Home/Explore/Map/Accessibility/About. Bahasa Indonesia: "Laporkan Tempat", "Lihat Detail", "Kembali".
- **Opsi**: Indonesia penuh; Inggris penuh; dwibahasa dengan pilihan bahasa.
- **Usulan**: Indonesia penuh untuk MVP sesuai pengguna sasaran. Istilah seperti "ramp" dan "guiding block" dipertahankan bila lebih dikenal pengguna.

## PT-05: Target Aksesibilitas Antarmuka

- **Bukti di desain**: [audit kontras](../design/fondasi-visual.md#kontras-warna-wcag-22) menunjukkan semua warna status dan teks putih di tombol utama gagal WCAG AA. Badge status dan marker peta hanya dibedakan dengan warna.
- **Pertanyaan**: standar apa yang menjadi syarat rilis, dan bagaimana pengujiannya (otomatis, screen reader, pengujian bersama pengguna difabel)?
- **Usulan**: WCAG 2.2 level AA sebagai syarat minimum, dicatat sebagai ADR sebelum implementasi UI dimulai. Untuk produk aksesibilitas, kegagalan di sini merusak kepercayaan pengguna.
- **Status**: kontras warna sudah diselesaikan di [ADR-0009](../decisions/0009-design-system-kaca-ungu-inklusi.md); semua token teks lulus AA di mode Glass, Solid, dan Dark. Yang masih terbuka: standar resmi untuk rilis dan cara pengujiannya.

## PT-06: Tampilan Mobile

- **Bukti di desain**: semua frame berukuran desktop 1440 px.
- **Pertanyaan**: apakah desain mobile-first, cukup responsif, berupa PWA, atau aplikasi native?
- **Catatan** *(asumsi)*: pengguna kemungkinan besar membuka aplikasi dari ponsel saat bepergian atau saat melapor di lokasi.
- **Status**: diputuskan di [ADR-0012](../decisions/0012-web-mobile-first-pwa.md): web responsif mobile-first yang bisa dipasang sebagai PWA.

## PT-07: Input Lokasi pada Formulir Laporan

- **Bukti di desain**: langkah 1 hanya meminta Nama Tempat dan Kategori ([Laporkan 13:812][lapor1]), tetapi Ringkasan Laporan menampilkan "IPB, Dramaga" ([Laporkan (3) 13:1207][lapor3]). Explore memfilter berdasarkan Kota dan peta membutuhkan koordinat.
- **Opsi**: pilih titik di peta; cari alamat (geocoding); pakai lokasi perangkat (GPS); kombinasi ketiganya.
- **Pertanyaan tambahan**: bagaimana mencegah laporan ganda untuk tempat yang sama?

## PT-08: Kondisi Fasilitas

- **Bukti di desain**: fasilitas hanya bernilai tersedia atau tidak ([Laporkan (2) 13:1002][lapor2], [Detail 13:1578][detail]). Kondisi hanya muncul di teks bebas ("guiding block masih perlu ditingkatkan kondisinya").
- **Pertanyaan**: perlukah atribut kondisi (baik, rusak, terhalang), ukuran (lebar pintu, kemiringan ramp), dan foto per fasilitas?
- **Inkonsistensi**: Landing menyebut "Lift & eskalator", sedangkan formulir menyebut "Lift / Elevator". Eskalator tidak bisa dipakai pengguna kursi roda, sehingga istilahnya perlu disamakan.

## PT-09: Proses Verifikasi dan Moderasi

- **Bukti di desain**: "Tim kami akan memverifikasi informasi yang Anda kirim" ([Laporan Berhasil 13:1412][berhasil]). Tidak ada layar admin.
- **Pertanyaan**:
  - Siapa verifikatornya, dan apa kriterianya (kunjungan lapangan, foto, konfirmasi pengelola tempat)?
  - Berapa target waktu verifikasi?
  - Bagaimana perubahan dari "Edit Lokasi" dan "Laporkan Kekurangan" diverifikasi?
  - Bagaimana foto dimoderasi (wajah orang, pelat nomor)?

## PT-10: Halaman yang Belum Didesain

- **Daftar**: Accessibility, Difabel, Tips, About, Privacy Policy, Terms of Service, "Pelajari Aksesibilitas", panel Filter di Map, alur Edit Lokasi, dan alur Laporkan Kekurangan.
- **Pertanyaan**: mana yang masuk MVP, dan siapa pemilik kontennya? Privacy Policy wajib ada bila aplikasi mengumpulkan data pribadi (lihat PT-03).

## PT-11: Tech Stack Produksi

- **Bukti di desain**: frame Figma adalah hasil impor prototipe Lovable (lihat [Referensi Figma](referensi-figma.md#kondisi-file)).
- **Pertanyaan**:
  - Apakah kode prototipe Lovable dilanjutkan atau aplikasi dibangun ulang?
  - Framework frontend, backend dan database (termasuk dukungan data geospasial), penyimpanan foto, dan hosting.
  - Penyedia tile peta untuk produksi (lihat [ADR-0008](../decisions/0008-peta-leaflet-dan-openstreetmap.md)).
- **Status**: diputuskan di [ADR-0013](../decisions/0013-tech-stack-nextjs-fastify.md): aplikasi dibangun ulang dengan Next.js di Vercel, API Fastify di Azure App Service, Neon Postgres + PostGIS, dan Cloudflare R2. Metode login di [ADR-0014](../decisions/0014-login-google-dan-kode-email.md). Penyedia tile peta masih mengikuti ADR-0008 yang berstatus Proposed.

## PT-12: Data Awal

- **Bukti di desain**: semua data contoh adalah placeholder (lihat [Data Placeholder](referensi-figma.md#data-placeholder)).
- **Opsi sumber data awal**: survei lapangan tim; data pemerintah kota; tag `wheelchair=*` di OpenStreetMap; kerja sama dengan komunitas difabel.
- **Pertanyaan tambahan**: kontak resmi proyek, serta apakah statistik di Landing dihitung otomatis dari data.

[landing]: https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=13-2
[map]: https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=13-351
[explore]: https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=13-528
[detail]: https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=13-1578
[lapor1]: https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=13-812
[lapor2]: https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=13-1002
[lapor3]: https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=13-1207
[berhasil]: https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=13-1412
