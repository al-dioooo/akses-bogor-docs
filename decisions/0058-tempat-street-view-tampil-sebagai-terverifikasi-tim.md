# ADR-0058: Tempat dari Street View Tampil sebagai Terverifikasi Tim

- Status: Accepted
- Tanggal: 2026-11-27
- Pengambil keputusan: pemilik produk
- Issue: #144
- Men-supersede sebagian: [ADR-0057](0057-verifikasi-dengan-bukti-street-view.md) (bagian "Label publik")

## Konteks dan Masalah

[ADR-0057](0057-verifikasi-dengan-bukti-street-view.md) memeriksa data yang belum diperiksa dari tangkapan Street View, dan memberi tempat yang terjawab label publik "Diverifikasi dari Street View · gambar Januari 2026". Label itu membuat tempat tersebut terlihat berbeda dari tempat yang diverifikasi tim lewat laporan.

Pada 2026-11-27 pemilik produk memutuskan tempat dari Street View diperlakukan seperti data dari tim AksesBogor. Menurut pemilik, kebutuhan utamanya adalah data terverifikasi di peta tidak nol. Bagaimana tempat itu tampil di publik, apa yang dikatakan Agen AI, dan di mana jejak asal datanya tetap disimpan?

## Decision Drivers

- Pengunjung melihat data terverifikasi, bukan peta kosong.
- Tim memang memeriksa setiap tempat dari bukti gambar dengan aturan ADR-0057, jadi data itu sudah diperiksa tim.
- Tidak ada teks yang mengatakan seseorang datang ke lokasi bila itu tidak terjadi.
- Asal data tetap bisa ditelusuri tim dan admin, dan Google tetap diberi atribusi atas gambarnya.

## Opsi yang Dipertimbangkan

1. Label tim biasa di semua tampilan publik, asal data tetap di database, log audit, Kelola tempat, dan keterangan foto
2. Tetap memakai label ADR-0057

## Keputusan

Opsi terpilih: "Label tim biasa", sesuai keputusan pemilik produk.

### Tampilan publik

- Kartu Jelajahi, Detail lokasi, kartu Peta, gambar berbagi, deskripsi halaman, dan kartu serta sumber Agen AI memakai teks dan ikon yang sama dengan tempat yang diverifikasi tim, mis. "Terakhir diverifikasi 1 Januari 2026" di Detail.
- Tanggalnya adalah `last_verified_at`, yang untuk tempat Street View diisi awal bulan gambarnya (ADR-0057). Tanda "Perlu dicek ulang" tetap muncul 12 bulan setelah tanggal itu.
- Keterangan foto tetap "Sumber: Google Street View, gambar <bulan tahun>". Keterangan ini jejak asal data yang jujur, dan atribusi yang dibutuhkan Google.

### Agen AI

- Agen dan asisten rute memperlakukan tempat Street View seperti tempat terverifikasi lain, dengan sumber "data AksesBogor yang diperiksa tim, diverifikasi <tanggal>". Alat agen tidak lagi menerima sumber verifikasi atau bulan gambar.
- Agen tidak pernah mengatakan ada orang yang datang atau memeriksa di lokasi, untuk tempat mana pun. Bila pengunjung bertanya bagaimana data diperiksa, agen menjawab bahwa tim memeriksanya dari foto.

### Yang tetap

- API tetap mengirim `verificationSource` dan `streetViewImageDate` di daftar dan Detail tempat, dan `source` di setiap foto, untuk Kelola tempat.
- Kelola tempat tetap menulis asal "Dari Street View", dengan saringan asal tersendiri, dan riwayat tempat tetap menyebut tindakan alat Street View.
- Aturan bukti, menyembunyikan data yang tidak terjawab, laporan yang tidak disentuh, alat, dan risiko syarat Google dari ADR-0057 tidak berubah.

## Konsekuensi

- Positif: peta dan Jelajahi langsung punya data terverifikasi dari putaran Street View pertama.
- Negatif: label publik tidak lagi menyebut sumber buktinya. Pengunjung tidak bisa membedakan tempat yang diperiksa dari gambar Street View dari tempat yang diperiksa lewat laporan dan foto warga, kecuali dari keterangan fotonya. Bukti Street View lebih lemah: hanya pintu masuk yang terlihat dari jalan, dan gambarnya bisa berumur beberapa tahun.
- Negatif: tanggal di label adalah awal bulan gambar, bukan hari pemeriksaan. Hari tanggal itu tidak berarti apa-apa.
- Tetap: asal data tersimpan di kolom `places.verification_source` dan `places.street_view_image_date`, di log audit (`dibuat_street_view`, `diubah_street_view`), di Kelola tempat ("Dari Street View" dan saringannya), dan di keterangan setiap foto. Bila keputusan ini dibalik, label ADR-0057 bisa dipasang lagi tanpa mengubah data.

## Pro & Kontra tiap Opsi

### Label tim biasa

- Pro: satu label untuk semua data terverifikasi, dan data terverifikasi langsung terlihat.
- Kontra: pengunjung kehilangan informasi tentang jenis bukti di labelnya.

### Label ADR-0057

- Pro: pengunjung tahu buktinya dari Street View dan umur gambarnya.
- Kontra: tidak sesuai keputusan pemilik.

## Referensi

- [ADR-0057](0057-verifikasi-dengan-bukti-street-view.md), [ADR-0051](0051-kelola-tempat-oleh-admin-dan-terbit-langsung.md)
- [Runbook verifikasi Street View](../operations/verifikasi-street-view.md)
