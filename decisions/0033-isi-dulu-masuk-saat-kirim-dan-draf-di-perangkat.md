# ADR-0033: Formulir Laporan Diisi Dulu, Masuk saat Kirim, dan Draf Disimpan di Perangkat

- Status: Accepted
- Tanggal: 2026-11-07
- Pengambil keputusan: Tim AksesBogor
- Issue: #54
- Melengkapi: [ADR-0006](0006-laporan-warga-diverifikasi-sebelum-tayang.md), [ADR-0011](0011-pelapor-wajib-login.md)

## Konteks dan Masalah

FR-20 meminta pengguna yang belum masuk diarahkan ke Masuk, lalu kembali ke formulir tanpa kehilangan isian. PRD mencatat risiko bahwa kewajiban login menurunkan jumlah laporan ([ADR-0011](0011-pelapor-wajib-login.md)). Isian juga tidak boleh hilang saat pelapor menekan Kembali di browser, memuat ulang halaman, atau sesinya berakhir di tengah jalan. Kapan pelapor diminta masuk, di mana draf disimpan sementara itu, dan kapan foto dikirim ke server?

## Decision Drivers

- Pelapor di lokasi bisa mulai mengisi tanpa hambatan (G2: kurang dari tiga menit).
- Tidak ada isian atau foto yang hilang di perjalanan ke Masuk dan kembali, termasuk saat sesi berakhir (WCAG 2.2 kriteria 2.2.5 dan 3.3.7).
- Data yang belum dikirim tidak tersimpan di server.
- Antrean verifikasi terlindung dari spam ([ADR-0006](0006-laporan-warga-diverifikasi-sebelum-tayang.md)).

## Opsi yang Dipertimbangkan

1. Isi dulu, masuk saat kirim; draf di perangkat; foto diunggah saat kirim
2. Masuk dulu sebelum formulir; draf di perangkat
3. Masuk dulu; draf disimpan di server setiap langkah

## Keputusan

Opsi terpilih: "Isi dulu, masuk saat kirim".

### Alur

1. "Laporkan tempat" selalu membuka formulir. Ketiga langkah dan ringkasan bisa diisi tanpa akun.
2. Pengecekan lokasi (kecamatan, cakupan Kota Bogor, dan lokasi mirip di sekitar) dan pencarian tempat tidak butuh akun, karena hanya membaca data publik.
3. "Kirim laporan" tanpa sesi yang berlaku membawa pelapor ke Masuk dengan `kembali` ke ringkasan. Setelah masuk, dan setelah Lengkapi Akun untuk akun baru, pelapor kembali ke ringkasan yang masih utuh dan menekan "Kirim laporan" sekali lagi. Laporan tidak terkirim tanpa tindakan pelapor.
4. Saat kirim, foto diunggah satu per satu dengan indikator kemajuan ([ADR-0031](0031-foto-laporan-di-bucket-r2-privat.md)). Foto yang gagal bisa dicoba ulang atau dilepas. Setelah semua foto terunggah, laporan dikirim bersama daftar fotonya dan masuk antrean dengan status menunggu verifikasi.
5. Setiap draf punya kunci acak. Bila koneksi putus setelah laporan diterima, mengirim ulang dengan kunci yang sama mengembalikan laporan yang sudah ada, bukan membuat laporan kedua.

### Draf di perangkat

- Draf disimpan di IndexedDB browser setiap kali isian berubah: isian ketiga langkah, langkah terakhir, foto yang sudah diperkecil, tag, dan keterangannya.
- Ada satu draf untuk laporan tempat baru dan satu draf per lokasi untuk "Edit lokasi" atau "Laporkan kekurangan".
- Langkah tercatat di URL, jadi tombol Kembali dan Maju browser berpindah antarlangkah tanpa kehilangan isian.
- Draf dihapus setelah laporan terkirim, saat pengguna Keluar atau menghapus akun di perangkat itu, saat pelapor menekan "Hapus draf", dan otomatis 7 hari setelah terakhir diubah.
- Draf tidak dikirim ke server dan tidak terikat ke akun. Di perangkat bersama, draf terlihat oleh pengguna berikutnya sampai salah satu hal di atas terjadi.

### Edit lokasi dan Laporkan kekurangan (FR-27)

- "Edit lokasi" membuat laporan berjenis perubahan: semua langkah terisi data lokasi saat ini dan semuanya bisa diubah, termasuk pin.
- "Laporkan kekurangan" membuat laporan berjenis kekurangan: nama, kategori, dan lokasi ditampilkan tanpa bisa diubah, lalu pelapor mengubah jawaban fasilitas, menambah foto, dan menulis catatan.
- Data lama tetap tayang sampai laporan diterbitkan ([Panduan Verifikasi](../operations/panduan-verifikasi.md#7-perubahan-pada-lokasi-yang-sudah-tayang)).

### Batas dan email

- Per akun: 20 laporan per 24 jam, paling banyak 30 laporan yang menunggu verifikasi atau klarifikasi, dan 100 unggahan foto per jam.
- Pengecekan lokasi memakai batas umum API, 120 permintaan per menit per klien. Batas pencarian ada di [ADR-0032](0032-pencarian-tempat-lewat-api-mapbox-search-box.md).
- Tidak ada email "laporan diterima". Layar konfirmasi dan "Laporan saya" sudah menunjukkan statusnya. Email baru dikirim saat laporan diterbitkan, ditolak, atau butuh klarifikasi (FR-40, FR-41).

## Konsekuensi

- Positif: pelapor bisa mulai seketika, dan akun baru diminta saat isiannya sudah ada.
- Positif: foto dan isian yang tidak jadi dikirim tidak pernah sampai ke server.
- Positif: jalur yang sama menangani sesi yang berakhir saat kirim.
- Negatif: unggahan foto terjadi di akhir, jadi pelapor menunggu di layar ringkasan, sekitar 10 sampai 20 detik untuk 10 foto di 4G.
- Negatif: draf tidak ikut pindah ke perangkat lain, dan bisa hilang bila pengguna membersihkan data browser.
- Negatif: pengecekan lokasi terbuka tanpa akun, jadi hanya dilindungi batas umum per klien.
- Tindak lanjut: tombol "Keluar" dan "Hapus akun" di web ikut menghapus draf di perangkat itu.

## Pro & Kontra tiap Opsi

### Isi dulu, masuk saat kirim

- Pro: hambatan paling rendah; data yang tidak dikirim tetap di perangkat.
- Kontra: unggahan menumpuk di akhir; draf hanya ada di satu perangkat.

### Masuk dulu

- Pro: paling sederhana; semua pengecekan bisa dibatasi per akun.
- Kontra: sebagian pelapor berhenti sebelum mengetik apa pun.

### Draf di server

- Pro: draf ikut pindah perangkat.
- Kontra: menyimpan isian dan foto yang belum tentu dikirim; butuh endpoint dan pembersihan tambahan.

## Referensi

- [ADR-0006](0006-laporan-warga-diverifikasi-sebelum-tayang.md), [ADR-0011](0011-pelapor-wajib-login.md), [ADR-0029](0029-akun-better-auth-dan-sesi-lewat-server-web.md), [ADR-0031](0031-foto-laporan-di-bucket-r2-privat.md), [ADR-0032](0032-pencarian-tempat-lewat-api-mapbox-search-box.md)
- [PRD](../product/prd.md), FR-20 sampai FR-27, FR-40, FR-41, dan bagian 12 (Risiko)
- [WCAG 2.2, kriteria 3.3.7 Redundant Entry](https://www.w3.org/TR/WCAG22/#redundant-entry)
