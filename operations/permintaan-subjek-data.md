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
