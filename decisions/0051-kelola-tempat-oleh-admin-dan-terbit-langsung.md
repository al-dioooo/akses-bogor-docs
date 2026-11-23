# ADR-0051: Kelola Tempat oleh Admin dan Terbit Langsung

- Status: Proposed
- Tanggal: 2026-11-23
- Pengambil keputusan: Tim AksesBogor
- Issue: #107
- Melengkapi: [ADR-0035](0035-peran-dan-panel-verifikator-di-web-yang-sama.md), [ADR-0036](0036-keputusan-verifikasi-dan-klarifikasi-di-laporan-saya.md), [ADR-0038](0038-log-audit-perubahan-data.md), [ADR-0046](0046-dasbor-akun-di-route-group-dengan-ringkasan-per-peran.md)

## Konteks dan Masalah

Setiap data tempat sekarang hanya bisa berubah lewat laporan warga yang diperiksa orang kedua ([ADR-0006](0006-laporan-warga-diverifikasi-sebelum-tayang.md), FR-37). Tim belum punya cara memperbaiki data yang salah, menurunkan tempat yang tidak semestinya tayang, atau mengisi tempat yang sudah diperiksa tim sendiri tanpa menunggu verifikator lain. Pengguna meminta "bypass" Laporkan untuk admin dan satu halaman berisi daftar tempat dengan tambah, ubah, dan hapus. Bagaimana admin mengelola tempat tanpa membuat data yang belum diperiksa orang kedua tampak terverifikasi?

## Decision Drivers

- Pengunjung harus bisa membedakan data yang diperiksa orang kedua dari data yang diterbitkan admin sendiri.
- Aturan bukti tetap sama dengan verifikasi ([Panduan Verifikasi](../operations/panduan-verifikasi.md), bagian 2 dan 5).
- Setiap tindakan admin bisa ditelusuri: siapa, kapan, apa yang berubah, dan alasannya (FR-35, [ADR-0038](0038-log-audit-perubahan-data.md)).
- Peran tetap diperiksa di API ([ADR-0035](0035-peran-dan-panel-verifikator-di-web-yang-sama.md)).
- Laporan yang sudah merujuk sebuah tempat tidak boleh rusak.
- Tidak ada dependensi atau layanan baru.

## Opsi yang Dipertimbangkan

1. Admin mengelola tempat dan boleh menerbitkan laporannya sendiri, dengan label publik "Diterbitkan langsung oleh admin"
2. Admin hanya boleh menerbitkan laporan orang lain, seperti verifikator (tanpa jalan pintas)
3. Admin mengubah data tanpa label publik
4. Admin menghapus tempat secara permanen

## Keputusan

Opsi terpilih: "Kelola tempat dengan label publik", karena tim bisa memperbaiki dan mengisi data tanpa menunggu orang kedua, dan pengunjung tetap tahu bahwa data itu belum diperiksa orang kedua.

### Halaman Kelola tempat

- Halaman khusus admin di dasbor, `/verifikasi/tempat` ("Kelola tempat"), dengan menu yang hanya tampil untuk admin ([ADR-0046](0046-dasbor-akun-di-route-group-dengan-ringkasan-per-peran.md)).
- Daftar tempat dengan pencarian nama, saring kategori, kecamatan, status, asal (diperiksa tim atau langsung oleh admin), dan keadaan tayang (tayang atau diturunkan), berhalaman 25 baris.
- Tambah dan ubah memakai bagian formulir Laporkan: informasi dan pemilih pin, jawaban fasilitas dengan alasan dan catatan, dan foto bertag lewat jalur unggah yang sama ([ADR-0031](0031-foto-laporan-di-bucket-r2-privat.md)).
- Setiap tempat punya riwayat dari log audit: tindakan, nama tampilan anggota tim yang bertindak, waktu, kolom yang berubah, dan alasannya.
- Kandidat OpenStreetMap ([ADR-0049](0049-lapisan-kandidat-openstreetmap-belum-ada-data.md)) tetap bukan tempat. Admin bisa membuka kandidat dan membuat tempat darinya: nama, kategori, dan pin awal terisi, lalu admin memeriksa pin di pintu masuk utama. Tempat yang dibuat dengan cara ini adalah tempat admin, dan kandidatnya hilang dengan aturan 50 m yang sama.

### Terbit langsung

- Di langkah ringkasan Laporkan, admin melihat pilihan "Terbitkan langsung (admin)". Laporan itu disimpan seperti laporan lain, lalu langsung diterbitkan lewat validasi yang sama dengan verifikasi (titik di Kota Bogor, alasan untuk setiap Tidak tersedia). Ini pengecualian FR-37 khusus peran admin; verifikator tetap tidak bisa memeriksa laporannya sendiri.
- API hanya menerima `publishDirectly` dari admin. Akun lain mendapat 403.

### Aturan bukti

- Jawaban Tersedia dan Tidak perlu butuh foto bertag fasilitas itu (Pintu masuk dan ramp untuk ramp, Toilet difabel, Guiding block, Lift), dari foto tempat yang sudah tayang atau foto baru. Tanpa foto, jawaban itu disimpan sebagai Belum tahu, sama seperti Tanpa bukti saat verifikasi ([ADR-0036](0036-keputusan-verifikasi-dan-klarifikasi-di-laporan-saya.md)). Formulir memperingatkan sebelum disimpan; API yang menerapkannya.
- Setiap Tidak tersedia tetap butuh alasan ([ADR-0022](0022-tersedia-berarti-bisa-dipakai.md)).
- Foto dari admin tidak melewati penyunting kaburkan. Admin bertanggung jawab tidak mengunggah wajah, pelat nomor, atau dokumen yang terbaca, dan bisa menghapus foto tempat dari halaman ubah.

### Label publik

- Tempat yang dibuat, diubah, atau diterbitkan langsung oleh admin menyimpan waktu dan akun admin itu. Detail, Jelajahi, dan Peta menulis "Diterbitkan langsung oleh admin · tanggal", tidak pernah "Diverifikasi" atau kalimat verifikasi lain. Nama admin tidak tampil untuk publik.
- Bila laporan warga tentang tempat itu kemudian diterbitkan verifikator, tempat kembali berlabel verifikasi biasa.

### Menurunkan, bukan menghapus

- "Hapus" menurunkan tempat dari tayang dengan alasan wajib. Tempat yang diturunkan hilang dari semua route publik, angka, prioritas kecamatan, agen, dan pencarian lokasi mirip, dan kandidat OpenStreetMap di dekatnya kembali tampil. Admin bisa menayangkannya lagi.
- Hapus permanen tidak tersedia: laporan perubahan dan kekurangan merujuk tempat, dan aturan skema laporan tidak mengizinkan rujukan itu kosong.

### Log audit

- Setiap tindakan admin ditulis ke log audit di transaksi yang sama: dibuat, diubah, diturunkan, ditayangkan lagi, dan terbit langsung, dengan perbedaan sebelum dan sesudah dan alasan 5 sampai 500 karakter. Alasan wajib untuk semua tindakan itu. Isi log tetap tanpa email dan nama pelapor ([ADR-0038](0038-log-audit-perubahan-data.md)).

### API

| Route | Isi |
| --- | --- |
| `GET /admin/places` | Daftar berhalaman dengan saring `q`, `category`, `district`, `status`, `origin` (`tim`, `admin`), dan `published` (`ya`, `tidak`) |
| `GET /admin/places/{id}` | Data lengkap untuk formulir ubah, termasuk foto tayang |
| `GET /admin/places/{id}/history` | Riwayat dari log audit |
| `POST /admin/places` | Tempat baru langsung tayang sebagai tempat admin |
| `PATCH /admin/places/{id}` | Ubah data, jawaban, foto baru, dan hapus foto |
| `DELETE /admin/places/{id}` | Turunkan dengan alasan |
| `POST /admin/places/{id}/restore` | Tayangkan lagi dengan alasan |
| `GET /admin/candidates` | Kandidat OpenStreetMap berhalaman untuk dibuat menjadi tempat |
| `POST /reports` dengan `publishDirectly` | Terbit langsung, khusus admin |

Semua route `/admin/*` memeriksa sesi dan peran admin. Data publik mendapat `verifiedBy` (`tim` atau `admin`) di samping `lastVerifiedAt`.

## Konsekuensi

- Positif: tim bisa memperbaiki, menurunkan, dan mengisi data tanpa menunggu orang kedua, dan setiap tindakan bisa ditelusuri.
- Positif: pengunjung tetap tahu data mana yang diperiksa orang kedua.
- Positif: laporan lama tetap utuh karena tempat tidak pernah dihapus permanen.
- Negatif: satu akun admin bisa menayangkan data tanpa pemeriksaan kedua. Ini diterima karena admin dibuat lewat skrip server ([ADR-0035](0035-peran-dan-panel-verifikator-di-web-yang-sama.md)), labelnya terlihat publik, dan log audit mencatat semuanya.
- Negatif: `lastVerifiedAt` untuk tempat admin berarti waktu admin menerbitkan, bukan waktu verifikasi. Web lama yang belum membaca `verifiedBy` akan menulis "Diverifikasi" untuk tempat itu, jadi API dan web dirilis bersama, dan tempat admin baru ada setelah web memakai halaman ini.
- Negatif: setiap query publik tempat harus menyaring tempat yang diturunkan. Satu kolom yang sama dipakai di semua query supaya tidak ada yang terlewat, dan tesnya memeriksa route publik satu per satu.

## Pro & Kontra tiap Opsi

### Kelola tempat dengan label publik

- Pro: cepat untuk tim, jujur untuk pengunjung.
- Kontra: satu halaman admin dan beberapa route baru.

### Admin seperti verifikator

- Pro: tidak ada pengecualian FR-37.
- Kontra: tim kecil harus selalu menunggu orang kedua, juga untuk perbaikan kecil seperti salah ketik.

### Ubah tanpa label

- Pro: tampilan publik tidak berubah.
- Kontra: data yang belum diperiksa orang kedua tampak terverifikasi, bertentangan dengan [ADR-0006](0006-laporan-warga-diverifikasi-sebelum-tayang.md).

### Hapus permanen

- Pro: data benar-benar hilang.
- Kontra: laporan yang merujuk tempat itu rusak, dan kesalahan admin tidak bisa dibatalkan.

## Referensi

- [ADR-0006](0006-laporan-warga-diverifikasi-sebelum-tayang.md), [ADR-0019](0019-verifikasi-tim-dan-mitra-terlatih.md), [ADR-0022](0022-tersedia-berarti-bisa-dipakai.md), [ADR-0031](0031-foto-laporan-di-bucket-r2-privat.md), [ADR-0035](0035-peran-dan-panel-verifikator-di-web-yang-sama.md), [ADR-0036](0036-keputusan-verifikasi-dan-klarifikasi-di-laporan-saya.md), [ADR-0038](0038-log-audit-perubahan-data.md), [ADR-0046](0046-dasbor-akun-di-route-group-dengan-ringkasan-per-peran.md), [ADR-0049](0049-lapisan-kandidat-openstreetmap-belum-ada-data.md)
- [PRD](../product/prd.md), FR-35 sampai FR-37
- [Panduan Verifikasi](../operations/panduan-verifikasi.md), bagian 2 dan 5
