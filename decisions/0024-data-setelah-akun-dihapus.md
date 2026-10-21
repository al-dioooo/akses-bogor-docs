# ADR-0024: Data Pribadi Dihapus, Data Lokasi yang Sudah Terbit Tetap Tayang tanpa Nama

- Status: Accepted
- Tanggal: 2026-10-22
- Pengambil keputusan: Tim AksesBogor
- Issue: #37

## Konteks dan Masalah

FR-13 mewajibkan pengguna bisa menghapus akun beserta data pribadinya, dan UU No. 27 Tahun 2022 tentang Pelindungan Data Pribadi memberi hak untuk menghapus data pribadi. Di sisi lain, laporan yang sudah diverifikasi menjadi informasi aksesibilitas yang dipakai difabel lain ([PT-13](../product/pertanyaan-terbuka.md#pt-13-data-setelah-akun-dihapus)). Apa yang terjadi pada laporan dan foto saat pelapornya menghapus akun?

## Decision Drivers

- Hak pengguna atas data pribadinya dipenuhi sepenuhnya.
- Informasi aksesibilitas yang sudah terverifikasi tidak hilang tiba-tiba bagi pengguna lain.
- Aturannya bisa dijelaskan dalam satu layar Hapus Akun.
- Penghapusan bisa dijalankan otomatis tanpa keputusan manual.

## Opsi yang Dipertimbangkan

1. Hapus data pribadi dan laporan yang belum terbit; data lokasi dan foto yang sudah terbit tetap tayang tanpa nama
2. Hapus semua laporan dan foto, termasuk yang sudah terbit
3. Tanyakan ke pengguna saat menghapus akun

## Keputusan

Opsi terpilih: "Hapus data pribadi dan laporan yang belum terbit; data yang sudah terbit tetap tanpa nama". Data lokasi yang sudah terbit adalah hasil verifikasi tentang tempat umum, bukan data tentang pelapor, sehingga tetap berguna setelah tautannya ke pelapor diputus.

### Yang dihapus

- Nama tampilan, email, waktu persetujuan Kebijakan Privasi, dan semua sesi di semua perangkat.
- Laporan yang belum terbit, yaitu yang menunggu verifikasi, sedang diklarifikasi, atau ditolak, beserta foto dan catatannya. Berkas fotonya juga dihapus dari Cloudflare R2.

### Yang tetap tayang

- Informasi lokasi yang sudah terbit: jawaban fasilitas, alasan, catatan, status, dan foto yang sudah lolos moderasi ([ADR-0019](0019-verifikasi-tim-dan-mitra-terlatih.md), [ADR-0023](0023-hingga-sepuluh-foto-bertag-per-laporan.md)).
- Tautan ke pelapor diputus: kolom pelapor dikosongkan, sehingga laporan itu tidak lagi bisa dihubungkan ke siapa pun.
- Log audit tetap mencatat perubahan, dengan pelapor ditulis sebagai "akun dihapus".

### Cara dan waktu

- Penghapusan berjalan segera setelah pengguna mencentang konfirmasi dan menekan "Hapus akun saya". Tidak ada masa tunggu atau pemulihan.
- Email terakhir dikirim sebelum alamatnya dihapus, berisi konfirmasi bahwa akun sudah dihapus.
- Salinan di cadangan database ikut hilang mengikuti masa simpan cadangan, yang dicantumkan di Kebijakan Privasi.
- Masuk lagi dengan email yang sama membuat akun baru yang kosong; laporan lama tidak tersambung kembali.
- Bila pengguna yang dihapus adalah verifikator, keputusan verifikasinya tetap berlaku dan log audit menulis "verifikator dihapus".

## Konsekuensi

- Positif: data pribadi benar-benar hilang, sementara pengguna lain tetap mendapat informasi lokasi yang sudah terverifikasi.
- Positif: aturannya sesuai dengan layar Hapus Akun di Figma.
- Negatif: model data harus mengizinkan laporan tanpa pelapor, dan penghapusan perlu menjalankan pembersihan berkas di R2.
- Negatif: pengguna tidak bisa menarik informasi lokasi yang sudah terbit dengan menghapus akun. Bila ada yang keliru, jalurnya tetap "Laporkan kekurangan".
- Tindak lanjut: Kebijakan Privasi menjelaskan aturan ini dan masa simpan cadangan.

## Pro & Kontra tiap Opsi

### Data terbit tetap tanpa nama

- Pro: memenuhi hak penghapusan data pribadi tanpa merusak data lokasi.
- Kontra: pengguna tidak bisa menghapus kontribusi yang sudah terbit.

### Hapus semuanya

- Pro: paling sederhana dijelaskan.
- Kontra: status lokasi bisa berubah tiba-tiba bagi pengguna lain, dan verifikasi yang sudah dikerjakan hilang.

### Tanyakan ke pengguna

- Pro: memberi pilihan.
- Kontra: menambah keputusan di saat yang sudah berat; hasilnya tidak konsisten antar lokasi.

## Referensi

- [Pertanyaan Terbuka PT-13](../product/pertanyaan-terbuka.md#pt-13-data-setelah-akun-dihapus)
- [PRD](../product/prd.md), FR-13
- [ADR-0011](0011-pelapor-wajib-login.md), [ADR-0019](0019-verifikasi-tim-dan-mitra-terlatih.md), [ADR-0023](0023-hingga-sepuluh-foto-bertag-per-laporan.md)
- [Referensi Figma: Frame Akun](../product/referensi-figma.md#frame-akun)
