# ADR-0040: Pelapor Memberi Izin Luas atas Laporan, dan Syarat Layanan Disetujui lewat Tombol Lengkapi Akun

- Status: Accepted
- Tanggal: 2026-11-08
- Pengambil keputusan: Tim AksesBogor
- Issue: #62
- Melengkapi: [ADR-0011](0011-pelapor-wajib-login.md), [ADR-0024](0024-data-setelah-akun-dihapus.md), [ADR-0029](0029-akun-better-auth-dan-sesi-lewat-server-web.md)

## Konteks dan Masalah

FR-50 meminta Syarat Layanan tersedia sebelum peluncuran. Isinya sebagian besar menjelaskan aturan yang sudah diputuskan: laporan diverifikasi sebelum tayang ([ADR-0006](0006-laporan-warga-diverifikasi-sebelum-tayang.md)), pelapor wajib masuk ([ADR-0011](0011-pelapor-wajib-login.md)), dan data yang sudah terbit tetap tayang tanpa nama setelah akun dihapus ([ADR-0024](0024-data-setelah-akun-dihapus.md)). Dua hal belum diputuskan di ADR mana pun: hak apa yang diberikan pelapor atas isian dan foto laporannya, dan bagaimana pengguna menyetujui Syarat Layanan, karena API hanya menyimpan versi Kebijakan Privasi yang disetujui ([ADR-0029](0029-akun-better-auth-dan-sesi-lewat-server-web.md)).

## Decision Drivers

- Verifikator mengubah laporan sebelum terbit: memperbaiki isian, mengaburkan, memotong, dan menyimpan ulang foto ([ADR-0036](0036-keputusan-verifikasi-dan-klarifikasi-di-laporan-saya.md), [ADR-0037](0037-foto-dikaburkan-di-api-dan-asli-dihapus.md)).
- Data yang sudah terbit harus tetap tayang setelah pelapornya menghapus akun ([ADR-0024](0024-data-setelah-akun-dihapus.md)).
- Impor data awal dari OpenStreetMap (PT-12) berlisensi ODbL dan bisa mewajibkan data lokasi gabungannya dibuka dengan lisensi yang sama. Tim tidak ingin meminta izin ulang kepada setiap pelapor bila itu terjadi.
- UU No. 27 Tahun 2022 meminta persetujuan pemrosesan data pribadi bisa dibedakan dari hal lain, jadi kotak centang Kebijakan Privasi tidak digabung dengan Syarat Layanan.
- Tanpa perubahan API atau migrasi sebelum rilis pertama.

## Opsi yang Dipertimbangkan

Hak atas isi laporan:

1. Izin luas: pelapor tetap memiliki laporan dan fotonya, dan memberi Tim AksesBogor izin tanpa bayaran, tidak eksklusif, dan tidak bisa ditarik untuk menyimpan, menyunting, menayangkan, dan membagikannya, termasuk dengan lisensi data terbuka yang dipilih tim kemudian.
2. Izin tayang saja: izin terbatas untuk menampilkan di AksesBogor; membuka data nanti butuh Syarat versi baru dan persetujuan ulang.
3. Langsung terbuka: data lokasi berlisensi ODbL 1.0 dan foto CC BY-SA 4.0 sejak awal.

Cara menyetujui Syarat Layanan:

1. Kalimat di atas tombol "Simpan dan lanjut" di Lengkapi Akun, dengan tautan ke Syarat Layanan. Kotak centang Kebijakan Privasi tetap terpisah.
2. Kotak centang kedua yang hanya diperiksa web.
3. Versi Syarat Layanan disimpan API seperti versi Kebijakan Privasi.

## Keputusan

Opsi terpilih: "Izin luas" dan "Kalimat di atas tombol".

- Teks Syarat Layanan ada di [product/syarat-layanan.md](../product/syarat-layanan.md), sumber halaman `/syarat` di akses-bogor-web, dan memakai versi tanggal seperti Kebijakan Privasi.
- Izin berlaku untuk isian laporan, foto, dan jawaban klarifikasi. Izin tidak bisa ditarik untuk bagian yang sudah terbit, sejalan dengan [ADR-0024](0024-data-setelah-akun-dihapus.md). Data yang dibagikan tidak memuat nama atau email pelapor.
- Lisensi data terbuka belum dipilih. Bila tim membuka data, pilihan lisensinya dicatat di ADR baru dan diumumkan di halaman Tentang.
- Lengkapi Akun menautkan Kebijakan Privasi dan Syarat Layanan. Di atas tombol "Simpan dan lanjut" ada kalimat bahwa menekan tombol itu berarti menyetujui Syarat Layanan. API tidak menyimpan versi Syarat Layanan.
- Bila isi Syarat Layanan berubah, versinya naik. Perubahan yang mengubah hak atau kewajiban pelapor dirilis bersama kenaikan versi Kebijakan Privasi, supaya pengguna diminta menyetujui ulang di Lengkapi Akun.
- Pada keputusan yang sama, tim menetapkan masa simpan log server 30 hari dan menaikkan Kebijakan Privasi ke versi 2026-11-10.

## Konsekuensi

- Positif: verifikator boleh menyunting dan mengaburkan isi laporan, dan data terbit tetap sah dipakai setelah akun dihapus.
- Positif: membuka data lokasi kelak, misalnya karena impor OpenStreetMap, tidak butuh persetujuan ulang.
- Positif: tidak ada perubahan API atau migrasi.
- Negatif: tidak ada catatan di database tentang versi Syarat Layanan yang disetujui tiap pengguna; buktinya hanya waktu dan versi persetujuan Kebijakan Privasi.
- Negatif: izin yang tidak bisa ditarik perlu dijelaskan dengan jelas, karena pelapor tidak bisa menarik kembali data yang sudah terbit.
- Tindak lanjut: teks Syarat Layanan dan Kebijakan Privasi masih draf dan perlu ditinjau dosen pembimbing atau unit hukum kampus sebelum peluncuran. Kontak resmi dan identitas pengendali data belum ada.

## Pro & Kontra tiap Opsi

### Izin luas

- Pro: cukup untuk menyunting, mengaburkan, menayangkan, dan membuka data tanpa persetujuan ulang.
- Kontra: lebih luas dari yang dibutuhkan hari ini.

### Izin tayang saja

- Pro: paling sempit dan mudah dipahami.
- Kontra: membuka data atau impor OpenStreetMap butuh persetujuan ulang dari semua pelapor, termasuk yang akunnya sudah dihapus.

### Langsung terbuka

- Pro: paling jelas bagi pengguna data.
- Kontra: mengunci pilihan lisensi sebelum PT-12 diputuskan; CC BY-SA mewajibkan atribusi, padahal nama pelapor tidak ditampilkan.

### Kalimat di atas tombol

- Pro: tanpa perubahan API; persetujuan Kebijakan Privasi tetap terpisah.
- Kontra: tidak ada versi Syarat Layanan per pengguna di database.

### Kotak centang kedua

- Pro: tindakan menyetujui lebih jelas.
- Kontra: menambah langkah tanpa catatan apa pun di API.

### Versi disimpan API

- Pro: bukti persetujuan per pengguna.
- Kontra: butuh kolom, migrasi, dan perubahan API dan web sebelum rilis pertama.

## Referensi

- [PRD](../product/prd.md), FR-11 dan FR-50
- [Syarat Layanan](../product/syarat-layanan.md), [Kebijakan Privasi](../product/kebijakan-privasi.md)
- [ADR-0006](0006-laporan-warga-diverifikasi-sebelum-tayang.md), [ADR-0011](0011-pelapor-wajib-login.md), [ADR-0024](0024-data-setelah-akun-dihapus.md), [ADR-0029](0029-akun-better-auth-dan-sesi-lewat-server-web.md), [ADR-0034](0034-batas-kecamatan-dari-openstreetmap.md), [ADR-0036](0036-keputusan-verifikasi-dan-klarifikasi-di-laporan-saya.md), [ADR-0037](0037-foto-dikaburkan-di-api-dan-asli-dihapus.md)
- [Pertanyaan Terbuka PT-12](../product/pertanyaan-terbuka.md)
- UU No. 27 Tahun 2022 tentang Pelindungan Data Pribadi
