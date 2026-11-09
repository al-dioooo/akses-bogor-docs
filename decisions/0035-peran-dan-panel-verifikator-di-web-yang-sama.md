# ADR-0035: Peran Disimpan di Akun, Admin Pertama lewat Skrip Server, Panel Verifikator di Web yang Sama

- Status: Accepted
- Tanggal: 2026-11-08
- Pengambil keputusan: Tim AksesBogor
- Issue: #58
- Melengkapi: [ADR-0019](0019-verifikasi-tim-dan-mitra-terlatih.md)

## Konteks dan Masalah

[ADR-0019](0019-verifikasi-tim-dan-mitra-terlatih.md) menetapkan bahwa peran verifikator hanya diberikan admin (FR-36), verifikator tidak memverifikasi laporannya sendiri, dan verifikator hanya melihat nama tampilan pelapor (FR-37). Tabel `users` sudah punya kolom `role` (pengguna, verifikator, admin), tetapi belum ada cara membuat admin pertama, memberi peran, atau membuka antrean. Panel verifikator juga belum didesain di Figma. Bagaimana peran diberikan, dan di mana panel verifikator berada?

## Decision Drivers

- Tidak ada route publik yang bisa membuat admin.
- Admin bisa menambah dan mencabut verifikator tanpa akses ke server.
- Data pribadi yang terlihat oleh tim sesedikit mungkin (UU No. 27 Tahun 2022).
- Tidak ada biaya atau proyek hosting baru.
- Panel verifikator memenuhi WCAG 2.2 AA seperti halaman lain ([ADR-0021](0021-wcag-22-aa-dan-pengujian-berlapis.md)).

## Opsi yang Dipertimbangkan

1. Admin pertama lewat skrip di server, admin memberi peran verifikator di halaman Tim verifikator, panel di aplikasi web yang sama
2. Semua perubahan peran lewat skrip di server
3. Route publik dengan kode undangan
4. Aplikasi panel terpisah di subdomain sendiri

## Keputusan

Opsi terpilih: "Skrip untuk admin, halaman tim untuk verifikator, panel di web yang sama".

### Peran

- Peran tetap disimpan di `users.role` dan dibaca dari database di setiap permintaan, jadi pencabutan peran langsung berlaku tanpa menunggu sesi berakhir.
- Hanya akun yang sudah dilengkapi (nama tampilan dan persetujuan Kebijakan Privasi) yang bisa diberi peran.
- **Admin pertama** dibuat dengan skrip `pnpm peran` di akses-bogor-api, dijalankan di Evergarden sebagai user `aksesbogor` dengan berkas environment produksi. Skrip ini juga satu-satunya cara memberi atau mencabut peran admin. Tidak ada route API yang bisa membuat admin.
- **Verifikator** diberikan dan dicabut admin di halaman Tim verifikator dengan memasukkan email akun yang tepat. Tidak ada daftar atau pencarian semua pengguna. Admin hanya melihat nama tampilan dan email anggota tim verifikator.
- Admin juga bisa memverifikasi laporan. Setiap perubahan peran masuk log audit ([ADR-0038](0038-log-audit-perubahan-data.md)).

### Panel

- Panel berada di aplikasi Next.js yang sama, di bawah `/verifikasi`: antrean, halaman pemeriksaan laporan, daftar "Perlu dicek ulang", dan Tim verifikator (khusus admin).
- Panel tidak ada di navigasi publik. Tautannya muncul di Akun saya untuk verifikator dan admin. Halamannya diberi `noindex`.
- Server web memeriksa peran untuk menampilkan halaman, tetapi pemeriksaan yang mengikat ada di API: setiap route verifikator dan admin memeriksa sesi dan peran, dan menjawab 403 bila tidak sesuai.
- Panel memakai komponen dan token design system yang sudah ada ([ADR-0027](0027-design-system-datar-dan-ilustrasi-3d.md)), bisa dipakai dengan keyboard saja, di lebar 320 px, dan di mode terang dan gelap.

### Laporan sendiri dan nama pelapor (FR-37)

- Antrean tidak memuat laporan milik verifikator itu. Membuka atau memutuskan laporan sendiri lewat API menjawab 403.
- Verifikator melihat nama tampilan pelapor, tidak pernah emailnya. Laporan terbit yang pelapornya sudah menghapus akun ditulis "akun dihapus".

### Dua verifikator pada laporan yang sama

- Tidak ada penguncian atau "ambil laporan". Keputusan pertama yang tersimpan berlaku; keputusan kedua menjawab 409 `sudah_diputuskan`, dan halaman memuat ulang laporan itu.
- Panduan Verifikasi meminta verifikator melewati laporan dengan konflik kepentingan; cukup tidak membukanya.

### Perlu dicek ulang (FR-38)

- Daftar lokasi yang terakhir diverifikasi lebih dari 12 bulan lalu, terlama dulu, dengan tautan ke Detail. Tidak ada laporan yang dibuat otomatis; pengecekan ulang memakai "Edit lokasi" seperti warga lain.

## Konsekuensi

- Positif: admin pertama hanya bisa dibuat oleh orang yang punya akses ke server.
- Positif: tim bisa mengelola verifikator tanpa menyentuh server, dan pencabutan peran langsung berlaku.
- Positif: tidak ada proyek Vercel atau domain baru.
- Negatif: email anggota tim terlihat oleh admin. Ini dibatasi ke anggota tim saja.
- Negatif: halaman panel ikut dalam bundel dan deploy web publik. Tidak ada data di dalamnya tanpa sesi verifikator, karena semua data dibaca dari API.
- Negatif: tanpa penguncian, dua verifikator bisa sempat memeriksa laporan yang sama. Dengan tim kecil hal ini jarang, dan hasilnya tidak bertabrakan karena keputusan kedua ditolak.

## Pro & Kontra tiap Opsi

### Skrip untuk admin, halaman tim untuk verifikator

- Pro: aman untuk peran tertinggi, praktis untuk peran yang sering berubah.
- Kontra: satu halaman admin tambahan.

### Semua lewat skrip

- Pro: tanpa halaman admin.
- Kontra: setiap verifikator baru butuh orang yang punya akses ke server; peran admin di aplikasi tidak berarti apa-apa.

### Route publik dengan kode undangan

- Pro: tidak butuh akses server sama sekali.
- Kontra: kode bisa bocor atau ditebak; ada jalan publik ke peran yang memberi akses data laporan.

### Aplikasi panel terpisah

- Pro: kode panel tidak ikut di web publik.
- Kontra: proyek, domain, dan sesi kedua; komponen harus dibagi antar repo.

## Referensi

- [ADR-0019](0019-verifikasi-tim-dan-mitra-terlatih.md), [ADR-0021](0021-wcag-22-aa-dan-pengujian-berlapis.md), [ADR-0024](0024-data-setelah-akun-dihapus.md), [ADR-0027](0027-design-system-datar-dan-ilustrasi-3d.md), [ADR-0029](0029-akun-better-auth-dan-sesi-lewat-server-web.md)
- [PRD](../product/prd.md), FR-30, FR-36 sampai FR-38
- [Panduan Verifikasi](../operations/panduan-verifikasi.md), bagian 2
