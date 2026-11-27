# Runbook Permintaan Hak Subjek Data

| | |
| --- | --- |
| Status | Draft, berlaku setelah skrip `hak-subjek:*` di akses-bogor-api ter-deploy |
| Tanggal | 2026-11-27 |
| Berlaku untuk | Permintaan atas data pribadi yang masuk lewat email kontak, sesuai bagian "Hak Anda" di [Kebijakan Privasi](../product/kebijakan-privasi.md) versi 2026-11-27 |
| Issue | #166, akses-bogor-api#188 |

Kebijakan Privasi menjanjikan bahwa permintaan lewat kontak dijawab dan dijalankan paling lambat 3 × 24 jam, dan setiap permintaan dicatat. Dasarnya UU No. 27 Tahun 2022 tentang Pelindungan Data Pribadi: permohonan tercatat (Pasal 14), memperbaiki data (Pasal 30), salinan data (Pasal 32) yang juga bisa dibawa ke layanan lain (Pasal 13), menunda dan membatasi pemrosesan (Pasal 40 dan 41), serta batas penolakan (Pasal 33).

Permintaan ditangani admin AksesBogor; penanggung jawabnya Aldio Lisafron. Perintah dijalankan di server produksi (fatir-mitya) oleh admin yang punya `sudo`. Nilai dalam `<…>` diisi sendiri. Jangan menyalin email, isi laporan, atau berkas ekspor ke chat, issue, PR, atau repo.

## 1. Sebelum Mulai

- [ ] Rilis API yang membawa migrasi 0012 sudah ter-deploy: `ls /srv/akses-bogor/current/scripts/hak-subjek/` menampilkan `catat.ts`, `ekspor.ts`, `ganti-email.ts`, `tahan.ts`, `email.ts`, dan `hapus.ts`.
- [ ] Akun AksesBogor Anda berperan admin. Emailnya dipakai di `--oleh`, dan tercatat sebagai pelaku di log audit.
- [ ] Anda bisa membuka kotak masuk <aliceevrasaldio@apps.ipb.ac.id> dan membalas dari alamat itu.
- [ ] Fungsi shell ini ada di shell admin. Argumen diteruskan apa adanya, jadi alasan yang berisi spasi cukup diberi tanda kutip biasa:

  ```bash
  hak() { sudo -u aksesbogor -H env COREPACK_ENABLE_DOWNLOAD_PROMPT=0 sh -c 'cd /srv/akses-bogor/current && exec corepack pnpm exec tsx --env-file=/etc/akses-bogor/api.env "$@"' hak "$@"; }
  ```

`sudo` mencatat baris perintah di log autentikasi server. Karena itu email hanya ditulis sekali, saat mencatat permintaan (langkah 4). Perintah berikutnya memakai id akun yang dicetak skrip. Awali perintah yang memuat email dengan satu spasi supaya tidak masuk riwayat shell.

## 2. Jam 3 × 24 Jam

- Jam mulai saat email masuk ke kotak kontak, menurut waktu di Gmail (WIB), bukan saat dibaca. Akhir pekan dan hari libur ikut dihitung.
- Catat permintaan dan kirim balasan "diterima" di hari yang sama.
- Jam tidak berhenti selama menunggu konfirmasi dari pengirim. Bila perlu konfirmasi, minta di balasan pertama.
- Setiap pagi jalankan `hak scripts/hak-subjek/catat.ts --daftar`. Skrip mencetak permintaan yang masih terbuka, tenggatnya, dan sisa jamnya. Kerjakan dulu yang bertanda `LEWAT`.
- Bila tenggat terlewat, tetap selesaikan dan kirim balasan secepatnya. Skrip mencatat bahwa permintaan itu terlambat saat ditutup.

## 3. Memeriksa Pengirim

Permintaan atas data akun hanya dijalankan bila dikirim dari email akun itu sendiri, seperti tertulis di bagian Kontak Kebijakan Privasi. Data tidak boleh jatuh ke orang lain.

1. **Alamat pengirim sama dengan email akun.** Bedanya huruf besar dan kecil tidak masalah.
2. **Alamatnya asli.** Di Gmail buka email itu, pilih ⋮ lalu "Tampilkan yang asli". SPF, DKIM, dan DMARC harus `PASS` untuk domain pengirim. Bila ada yang `FAIL` atau tidak ada, anggap belum terbukti: kirim email baru (bukan balasan) ke email akun dan minta pemiliknya mengonfirmasi dari alamat itu.
3. **Balasan pergi ke email akun.** Sebelum mengirim, periksa kolom Kepada. Header `Reply-To` bisa mengarahkan balasan ke alamat lain; ganti ke email akun bila berbeda.
4. **Jangan minta KTP atau dokumen lain.** Kepemilikan email sudah cukup, dan dokumen identitas menambah data yang harus kita jaga.

Permintaan dari alamat lain, mis. "email lama saya sudah tidak bisa dibuka, tolong ganti emailnya", tidak bisa dibuktikan. Jangan sebut apakah akunnya ada. Balas dengan templat "perlu dikirim dari email akun" (langkah 8). Bila pengirim tetap tidak bisa memakai email akunnya, tolak dengan alasan bahwa permintaan itu bisa mengungkap data pribadi orang lain (Pasal 33 huruf b).

## 4. Mencatat Permintaan

Satu jenis per catatan. Email yang meminta dua hal, mis. salinan data sekaligus berhenti menerima email, dicatat dua kali.

| Jenis | Untuk | Skrip yang menjalankan |
| --- | --- | --- |
| `salinan` | Salinan data atau portabilitas | `ekspor` |
| `perbaikan` | Mengganti email atau memperbaiki data lain | `ganti-email`, atau langkah 5.2 |
| `pembatasan` | Menahan laporan, atau berhenti mengirim email status laporan | `tahan`, `email` |
| `penghapusan` | Menarik persetujuan atau menghapus akun | `hapus` |
| `keberatan` | Keberatan atas pemrosesan atau keputusan otomatis | Tidak ada; dijawab lewat email |
| `pertanyaan` | Pertanyaan atau pengaduan | Tidak ada; dijawab lewat email |

```bash
 hak scripts/hak-subjek/catat.ts --jenis <jenis> --akun <email pengirim> --diterima "<YYYY-MM-DD HH:MM WIB saat email masuk>" --oleh <email admin>
```

Skrip mencetak nomor permintaan, id akun, dan tenggatnya. Bila skrip menjawab "Tidak ada akun dengan email atau id itu", catat tanpa `--akun` lalu pakai templat "tidak ada akun". Pakai nomor itu di subjek setiap balasan: `[Permintaan #<nomor>]`.

Skrip lain menolak nomor permintaan yang sudah ditutup, untuk akun lain, atau berjenis lain, supaya salah ketik nomor tidak menjalankan tindakan untuk orang lain.

## 5. Menjalankan Permintaan

Semua skrip di bawah mencatat admin yang menjalankannya dan nomor permintaannya di log audit, dan hanya mencetak id akun serta jumlah.

### 5.1 Salinan data

```bash
hak scripts/hak-subjek/ekspor.ts --akun <id akun> --permintaan <nomor> --oleh <email admin> --keluar /tmp/ekspor-<nomor>.json
```

Berkasnya JSON baru bermode 600 milik user `aksesbogor`; berkas yang sudah ada tidak ditimpa. Isinya akun (termasuk waktu dan versi persetujuan), tautan Google, sesi tanpa kuncinya, waktu kode masuk, laporan dengan isian, titik, foto, dan klarifikasinya, foto yang belum dipasang, antrean email status laporan, permintaan hak subjek data, log audit tentang akun dan laporannya, dan, untuk anggota tim, keputusan dan pertanyaan klarifikasi yang pernah dibuat. Setiap bagian dijelaskan di `catatan` di awal berkas.

Yang sengaja tidak ikut: kunci sesi dan hash kode masuk (bisa dipakai untuk masuk), catatan internal verifikator ([ADR-0036](../decisions/0036-keputusan-verifikasi-dan-klarifikasi-di-laporan-saya.md)), dan siapa anggota tim yang memproses laporan (data pribadi orang lain). Berkas foto juga tidak ikut. Bila pemilik akun memintanya, unduh foto laporannya dari R2 lewat id foto di berkas ekspor dan kirim dengan cara yang sama.

Kirim berkasnya dengan langkah 6.

### 5.2 Perbaikan data

Mengganti email:

```bash
 hak scripts/hak-subjek/ganti-email.ts --akun <id akun> --email-baru <email baru> --permintaan <nomor> --oleh <email admin>
```

Skrip menolak email yang sudah dipakai akun lain. Semua sesi berakhir dan tautan Google dilepas, karena tautan itu dibuat untuk email lama. Pemilik akun lalu masuk dengan kode yang dikirim ke email baru. Setelah itu, masuk dengan Google untuk email baru menaut lagi seperti biasa, sesuai cara Better Auth menautkan akun. Masuk dengan Google untuk email lama membuat akun baru yang kosong.

Data lain:

- **Nama tampilan**: pemilik akun bisa mengubahnya sendiri di Akun Saya. Sebutkan itu di balasan.
- **Laporan yang belum diperiksa**: isian laporan tidak bisa diubah, tetapi verifikator menetapkan nama, titik, deskripsi, dan jawaban fasilitas akhir saat menerbitkan. Teruskan koreksinya ke verifikator di diskusi tim tanpa menyebut email pelapor, balas pelapor bahwa koreksinya akan dipakai saat laporan diperiksa, lalu tutup permintaannya.
- **Data tempat yang sudah terbit**: perbaiki lewat Kelola tempat ([ADR-0051](../decisions/0051-kelola-tempat-oleh-admin-dan-terbit-langsung.md)), atau minta pengirim memakai "Laporkan perubahan" di halaman tempat itu.

### 5.3 Pembatasan pemrosesan

Menahan laporan yang belum diperiksa:

```bash
hak scripts/hak-subjek/tahan.ts --akun <id akun> --permintaan <nomor> --oleh <email admin> --alasan "Permintaan pembatasan #<nomor>"
```

Laporan berstatus menunggu atau klarifikasi tidak muncul di antrean, verifikator yang membuka tautannya mendapat "Laporan ini sedang ditahan", dan laporan klarifikasi tidak ditutup otomatis saat tenggatnya lewat. Laporan yang dikirim setelah ini tidak ikut ditahan; jalankan lagi bila diminta. Alasan hanya dibaca tim dan tidak boleh berisi data pribadi.

Melepas tahanan, bila pemilik akun memintanya. Catat dulu permintaan `pembatasan` baru, lalu:

```bash
hak scripts/hak-subjek/tahan.ts --akun <id akun> --permintaan <nomor baru> --oleh <email admin> --lepas
```

Laporan kembali ke antrean di posisi semula, dan tenggat klarifikasinya diperpanjang selama masa tahan.

Berhenti mengirim email status laporan:

```bash
hak scripts/hak-subjek/email.ts --akun <id akun> --permintaan <nomor> --oleh <email admin> --mati
```

Email terbit, ditolak, dan klarifikasi tidak dikirim lagi ke akun itu, dan yang masih antre dibuang. Kode masuk tetap dikirim karena diminta pemilik akun sendiri. `--nyala` menyalakannya lagi, juga dengan nomor permintaan baru. Sampaikan di balasan bahwa pertanyaan klarifikasi tetap muncul di Akun Saya, dan laporan ditutup bila pertanyaannya tidak dijawab dalam 7 hari.

### 5.4 Penghapusan dan penarikan persetujuan

Pemilik akun bisa menghapus akunnya sendiri lewat Hapus akun di Akun Saya, dan mendapat email konfirmasi otomatis. Bila ia meminta kami yang menghapus, jalankan:

```bash
hak scripts/hak-subjek/hapus.ts --akun <id akun> --permintaan <nomor> --oleh <email admin>
```

Hasilnya sama dengan Hapus akun ([ADR-0024](../decisions/0024-data-setelah-akun-dihapus.md)). Berkas foto yang ikut terhapus dibersihkan dari R2 oleh tugas berkala API dalam satu jam. Skrip tidak mengirim email; balasan "akun dihapus" dari kotak kontak menjadi konfirmasinya. Akun admin tidak bisa dihapus dengan skrip ini; turunkan perannya dulu dengan `scripts/peran.ts`.

### 5.5 Keberatan dan pertanyaan

Tidak ada skrip. Jawab isi emailnya, catat permintaannya, dan tutup setelah dijawab. Untuk keberatan atas keputusan otomatis, jelaskan bahwa status tempat dihitung dari jawaban fasilitas dan bukan penilaian tentang orang. Bila jawabannya butuh keputusan tim, bawa ke diskusi tim sebelum tenggat.

## 6. Mengirim Salinan Data dengan Aman

Salinan dikirim sebagai lampiran balasan ke email akun itu sendiri, tidak ke alamat lain walaupun diminta, lalu semua salinan lokal dihapus.

1. Pindahkan berkas dari `/tmp` ke folder rumah admin:

   ```bash
   sudo install -m 600 -o "$USER" -g "$USER" /tmp/ekspor-<nomor>.json ~/ekspor-<nomor>.json && sudo rm /tmp/ekspor-<nomor>.json
   ```

2. Di laptop, salin lalu hapus dari server:

   ```bash
   scp <server>:ekspor-<nomor>.json ./aksesbogor-data-akun.json && ssh <server> rm ekspor-<nomor>.json
   ```

3. Buka berkasnya sekali dan pastikan `akun.email` sama dengan alamat yang akan dikirimi.
4. Balas email permintaan dari <aliceevrasaldio@apps.ipb.ac.id> dengan templat "salinan data" dan berkas itu sebagai lampiran. Periksa kolom Kepada (langkah 3).
5. Setelah terkirim, hapus `aksesbogor-data-akun.json` dari laptop dan kosongkan Sampah. Hapus juga email keluar itu dari Terkirim dan Sampah di kotak kontak setelah pemilik akun mengonfirmasi, atau paling lambat 7 hari kemudian.

## 7. Menutup Permintaan

```bash
hak scripts/hak-subjek/catat.ts --selesai <nomor> --oleh <email admin>
hak scripts/hak-subjek/catat.ts --tolak <nomor> --alasan "<alasan tanpa data pribadi>" --oleh <email admin>
```

Tutup setelah balasan "selesai" atau "ditolak" terkirim. Penolakan wajib menyebut alasannya, dan alasan yang sama ditulis di balasan.

## 8. Templat Balasan

Subjek: `Re: <subjek asli> [Permintaan #<nomor>]`. Tulis apa adanya, tanpa data yang tidak perlu. Tutup setiap balasan dengan:

```text
Salam,
Tim AksesBogor
```

### Diterima

```text
Halo,

Permintaan Anda sudah kami terima pada <tanggal jam> WIB dan kami catat dengan nomor <nomor>. Kami menyelesaikannya paling lambat <tanggal jam tenggat> WIB.
```

### Perlu dikirim dari email akun

```text
Halo,

Permintaan Anda sudah kami terima dan kami catat dengan nomor <nomor>. Supaya data tidak jatuh ke orang lain, kami hanya menjalankan permintaan atas data akun yang dikirim dari alamat email akun AksesBogor itu sendiri. Mohon kirim ulang permintaan ini dari alamat tersebut. Kami tidak meminta KTP atau dokumen lain.
```

### Tidak ada akun

```text
Halo,

Kami tidak menemukan akun AksesBogor dengan alamat email ini, jadi kami tidak menyimpan akun, laporan, atau foto atas nama alamat ini. Log server (alamat IP, halaman yang dibuka, dan waktunya) disimpan paling lama 30 hari dan tidak dikaitkan dengan email.
```

### Selesai: salinan data

```text
Halo,

Terlampir salinan data akun AksesBogor Anda dalam berkas JSON yang bisa dibaca komputer dan dibawa ke layanan lain. Bagian "catatan" di awal berkas menjelaskan isi setiap bagian. Berkas foto tidak ikut; balas email ini bila Anda juga ingin salinan foto laporan Anda.

Catatan internal verifikator dan nama anggota tim yang memeriksa laporan tidak kami sertakan, karena berisi data pribadi orang lain. Salinan berkas ini sudah kami hapus dari komputer kami.
```

### Selesai: ganti email

```text
Halo,

Email akun AksesBogor Anda sudah kami ganti ke <email baru>. Semua sesi masuk sudah berakhir. Silakan masuk lagi dengan kode yang kami kirim ke email baru.

Bila sebelumnya Anda masuk dengan Google, tautan itu sudah kami lepas. Setelah sekali masuk dengan kode, Anda bisa memakai tombol Google lagi bila email baru itu akun Google.
```

### Selesai: laporan ditahan

```text
Halo,

<jumlah> laporan Anda yang belum diperiksa sudah kami tahan sejak <tanggal jam> WIB. Selama ditahan, laporan itu tidak diperiksa verifikator dan tidak ditutup otomatis. Laporan yang Anda kirim setelah ini tidak ikut ditahan.

Balas email ini bila Anda ingin pemeriksaan dilanjutkan. Tenggat menjawab pertanyaan klarifikasi akan kami perpanjang selama masa tahan.
```

### Selesai: email status laporan dihentikan

```text
Halo,

Kami sudah berhenti mengirim email status laporan ke akun Anda. Status laporan dan pertanyaan dari verifikator tetap bisa Anda lihat di Akun Saya. Laporan yang pertanyaannya tidak dijawab dalam 7 hari akan ditutup, jadi periksa Akun Saya sesekali. Kode masuk tetap kami kirim setiap kali Anda masuk.
```

### Selesai: akun dihapus

```text
Halo,

Akun AksesBogor Anda sudah kami hapus. Nama, email, waktu persetujuan, semua sesi, dan laporan yang belum terbit beserta fotonya sudah dihapus. Informasi tempat dan foto yang sudah terbit tetap tayang tanpa nama Anda. Salinan di cadangan database hilang paling lama 30 hari lagi.

Email ini yang terakhir dari kami tentang akun tersebut. Bila Anda masuk lagi dengan email ini, akun baru yang kosong akan dibuat.
```

### Ditolak

Hanya untuk alasan di Pasal 33: permintaan itu membahayakan keamanan, kesehatan fisik, atau kesehatan mental pemilik data atau orang lain; mengungkap data pribadi orang lain; atau bertentangan dengan kepentingan pertahanan dan keamanan nasional. Bila hanya sebagian yang ditolak, jalankan sisanya dan sebutkan keduanya.

```text
Halo,

Kami tidak bisa menjalankan permintaan nomor <nomor> <seluruhnya / bagian ...> karena <alasan, mis. permintaan ini akan mengungkap data pribadi orang lain>. UU No. 27 Tahun 2022 Pasal 33 mewajibkan kami menolak permintaan seperti ini.

Bila Anda tidak setuju, balas email ini. Anda juga bisa mengadu ke lembaga penyelenggara pelindungan data pribadi, seperti tertulis di bagian Hak Anda pada Kebijakan Privasi.
```

## 9. Catatan Teknis

- Permintaan dicatat di tabel `data_subject_requests`: jenis, id akun, waktu diterima, admin yang menangani, waktu ditutup, hasil (`selesai` atau `ditolak`), dan alasan penolakan. Tanpa email, nama, atau isi permintaan. Id akun sengaja tanpa foreign key, jadi catatan tetap ada setelah akunnya dihapus.
- Log audit mencatat `permintaan_dicatat`, `permintaan_selesai`, `permintaan_ditolak`, `data_diekspor`, `email_diubah`, `laporan_ditahan`, `laporan_dilepas`, `email_laporan_dimatikan`, `email_laporan_dinyalakan`, dan `akun_dihapus`, masing-masing dengan nomor permintaan dan admin pelakunya ([ADR-0038](../decisions/0038-log-audit-perubahan-data.md)). Email, alasan penahanan, dan isi permintaan tidak masuk log audit.
- Laporan yang ditahan memakai kolom `reports.held_at` dan `reports.hold_reason`; email status laporan yang dimatikan memakai `users.report_emails_off_at`. Keduanya kolom nullable dari migrasi 0012, jadi versi API sebelumnya tetap berjalan.
- Skrip hanya mencetak id akun, nomor permintaan, dan jumlah. Galat database dicetak tanpa parameter kuerinya.
