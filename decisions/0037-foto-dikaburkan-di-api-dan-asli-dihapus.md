# ADR-0037: Foto Dikaburkan API saat Terbit, dan Foto Asli Dihapus

- Status: Accepted
- Tanggal: 2026-11-08
- Pengambil keputusan: Tim AksesBogor
- Issue: #58
- Melengkapi: [ADR-0031](0031-foto-laporan-di-bucket-r2-privat.md)

## Konteks dan Masalah

FR-34 meminta verifikator bisa menyembunyikan atau mengaburkan foto yang memperlihatkan wajah atau pelat nomor. [ADR-0031](0031-foto-laporan-di-bucket-r2-privat.md) menyimpan foto laporan di bucket R2 privat dan menyalin foto yang lolos moderasi ke bucket publik saat terbit, tetapi belum menetapkan cara mengaburkan, apakah foto asli disimpan, dan berapa lama foto yang disembunyikan atau ditolak disimpan. Bagaimana foto dimoderasi?

## Decision Drivers

- Wajah dan pelat nomor tidak pernah sampai ke bucket publik, dan hasil kaburan tidak bisa dikembalikan.
- Moderasi bisa dikerjakan dengan keyboard saja (WCAG 2.2 kriteria 2.1.1 dan 2.5.7).
- Verifikator melihat hasil akhir sebelum terbit.
- Server tidak memercayai piksel dari browser.
- Tanpa dependensi baru; `sharp` sudah ada ([ADR-0031](0031-foto-laporan-di-bucket-r2-privat.md)).

## Opsi yang Dipertimbangkan

1. Verifikator menandai area di browser, API mengaburkan dengan `sharp` saat terbit, foto asli dihapus
2. Dikaburkan di browser dengan canvas, lalu diunggah ulang
3. Hanya disembunyikan, tanpa mengaburkan

## Keputusan

Opsi terpilih: "Area ditandai di browser, dikaburkan API saat terbit".

### Melihat foto yang belum terbit

- Verifikator dan admin membuka foto laporan lewat API, diteruskan server web dengan sesi verifikator. Jawabannya `Cache-Control: private, no-store`, jadi tidak tersimpan di cache bersama. Foto laporan sendiri tidak bisa dibuka (FR-37).

### Moderasi per foto

- Untuk setiap foto, verifikator bisa memperbaiki tag ([ADR-0023](0023-hingga-sepuluh-foto-bertag-per-laporan.md)), menulis keterangan, menyembunyikan foto, atau menandai area yang dikaburkan.
- Area berupa persegi panjang dalam persen lebar dan tinggi foto, paling banyak 20 per foto. Perubahan moderasi langsung tersimpan di API, sebelum keputusan laporan.
- Area dibuat dengan menyeret di atas foto, atau dengan keyboard: "Tambah area" membuat kotak di tengah foto, tombol panah menggeser kotak, Shift + panah mengubah ukurannya, dan setiap kotak punya kolom angka posisi dan ukuran. Setiap kotak bisa dihapus.
- API membuat pratinjau dengan cara yang sama seperti saat terbit, jadi yang dilihat verifikator sama dengan yang akan tayang.

### Cara mengaburkan

- Setiap area diperkecil sampai sisi terpanjangnya paling banyak 8 piksel, diperbesar lagi, lalu dihaluskan. Detail wajah atau huruf pelat hilang, bukan sekadar buram.
- Hasilnya disimpan sebagai WebP baru tanpa metadata. Foto asli tidak ikut ke bucket publik.

### Saat terbit, ditolak, atau disembunyikan

| Keadaan | Foto |
| --- | --- |
| Terbit, tidak disembunyikan | Disalin ke bucket publik sebagai `lokasi/<id lokasi>/<id foto>.webp`, dengan area yang dikaburkan; teks alternatif dibuat ulang dari tag dan nama lokasi akhir. Berkas di bucket privat dihapus. |
| Terbit, disembunyikan | Baris foto dan berkasnya dihapus. Tidak pernah tayang. |
| Laporan ditolak (termasuk R6) | Semua foto laporan dan berkasnya dihapus saat ditolak. |

- Berkas dihapus lewat antrean `photo_deletions` yang sudah ada. Penerbit langsung mencoba menghapus berkas privat; yang gagal dicoba lagi oleh pembersihan setiap jam.
- Bila menyalin foto ke bucket publik gagal, laporan tidak diterbitkan (502 `foto_gagal_diterbitkan`) dan data lokasi tidak berubah. Salinan yang sempat dibuat masuk antrean hapus.
- Foto lokasi yang sudah tayang tidak diubah oleh laporan baru.

## Konsekuensi

- Positif: hanya foto yang sudah dimoderasi dan dikaburkan yang pernah publik, dan aslinya tidak disimpan setelah terbit.
- Positif: area bisa ditandai tanpa mouse, dan pratinjau sama dengan hasil akhir.
- Positif: foto laporan yang ditolak tidak disimpan lebih lama dari yang dibutuhkan.
- Negatif: setiap foto yang terbit diproses ulang `sharp` di Evergarden, sekitar satu detik per foto, dibatasi satu proses sekaligus.
- Negatif: kaburan tidak bisa dibatalkan setelah terbit. Bila area terlalu luas, foto baru harus dilaporkan lagi.
- Negatif: verifikator tidak bisa melihat foto laporan yang sudah ditolak, mis. untuk menilai laporan ulang.

## Pro & Kontra tiap Opsi

### Area ditandai di browser, dikaburkan API

- Pro: piksel diproses server; pratinjau sama dengan hasil.
- Kontra: editor area dan route pratinjau harus dibuat.

### Dikaburkan di browser

- Pro: tidak membebani server saat menandai.
- Kontra: API harus memeriksa ulang berkas dari browser, dan editor keyboard tetap dibutuhkan; hasilnya tidak lebih baik.

### Hanya disembunyikan

- Pro: paling sedikit kerja.
- Kontra: foto pintu masuk yang tidak sengaja memotret orang hilang sebagai bukti di Detail.

## Referensi

- [ADR-0019](0019-verifikasi-tim-dan-mitra-terlatih.md), [ADR-0021](0021-wcag-22-aa-dan-pengujian-berlapis.md), [ADR-0023](0023-hingga-sepuluh-foto-bertag-per-laporan.md), [ADR-0024](0024-data-setelah-akun-dihapus.md), [ADR-0031](0031-foto-laporan-di-bucket-r2-privat.md), [ADR-0036](0036-keputusan-verifikasi-dan-klarifikasi-di-laporan-saya.md)
- [PRD](../product/prd.md), FR-34
- [Panduan Verifikasi](../operations/panduan-verifikasi.md), bagian 8
- [WCAG 2.2, kriteria 2.5.7 Dragging Movements](https://www.w3.org/TR/WCAG22/#dragging-movements)
