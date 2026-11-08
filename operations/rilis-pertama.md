# Daftar Periksa Rilis Pertama

| | |
| --- | --- |
| Status | Draft |
| Tanggal | 2026-11-08 |
| Berlaku untuk | Rilis `main` → `release` pertama yang membawa akun, Laporkan, verifikasi, dan halaman kebijakan |
| Issue | #63 |

Produksi masih menjalankan rilis pertama: API dengan 7 lokasi contoh dan web tanpa akun. Rilis ini membawa akun ([ADR-0029](../decisions/0029-akun-better-auth-dan-sesi-lewat-server-web.md), [ADR-0030](../decisions/0030-email-transaksional-resend.md)), Laporkan ([ADR-0031](../decisions/0031-foto-laporan-di-bucket-r2-privat.md) sampai [ADR-0034](../decisions/0034-batas-kecamatan-dari-openstreetmap.md)), verifikasi ([ADR-0035](../decisions/0035-peran-dan-panel-verifikator-di-web-yang-sama.md) sampai [ADR-0039](../decisions/0039-email-status-laporan.md)), dan halaman Tentang, Kebijakan Privasi, serta Syarat Layanan ([ADR-0040](../decisions/0040-syarat-layanan-lisensi-laporan-dan-persetujuan.md)).

Kerjakan dari atas ke bawah. Nilai dalam `<…>` diisi sendiri. Secret hanya ditulis di `/etc/akses-bogor/*.env` dan di pengaturan Vercel, tidak pernah di chat, issue, PR, atau repo. Perintah `sudo` dijalankan admin Evergarden.

## 1. Sebelum mulai

### PR yang harus sudah ada di `main`

Merge dengan squash, sesuai urutan di setiap repo:

- [ ] akses-bogor-docs: #64 (Syarat Layanan, ADR-0040, Kebijakan Privasi 2026-11-10), lalu #65 (daftar ini).
- [ ] akses-bogor-api: #17 (rate limit per pengunjung), lalu #46 (Kebijakan Privasi 2026-11-10). Keduanya membawa perbaikan tes urutan log audit yang sama, jadi PR kedua tetap bersih setelah PR pertama masuk.
- [ ] akses-bogor-web: #27 (rahasia server web dan IP pengunjung), lalu stack #83 → #84 (Syarat Layanan, Kebijakan Privasi 2026-11-10, Tentang).

API #46 dan web #83 sama-sama membawa Kebijakan Privasi 2026-11-10, jadi keduanya harus sudah di `main` sebelum rilis.

### Butir yang masih terbuka

Butir ini tidak menghalangi deploy, tetapi harus selesai sebelum AksesBogor diumumkan ke publik:

- [ ] Email kontak resmi serta nama tim dan kampus. Saat ini placeholder di Kebijakan Privasi, Syarat Layanan, dan Tentang, di docs maupun di `messages/id.json` web.
- [ ] Tinjauan hukum Kebijakan Privasi dan Syarat Layanan oleh dosen pembimbing atau unit hukum kampus. Selama belum ditinjau, kedua halaman menampilkan tanda draf.
- [ ] Rencana untuk 7 lokasi contoh. Nama lokasinya fiktif ("Halte contoh" dan seterusnya), dan semuanya tetap tayang setelah rilis sampai data awal (PT-12) masuk.

## 2. Layanan luar

### 2.1 Cloudflare R2 untuk foto

- [ ] Bucket privat untuk foto yang belum terbit, misalnya `akses-bogor-foto-laporan`. Tanpa akses publik.
- [ ] Bucket publik untuk foto yang sudah terbit, misalnya `akses-bogor-foto`. Pasang custom domain, misalnya `foto.yorhagarden.xyz`. Alamat `r2.dev` dibatasi Cloudflare dan tidak untuk produksi. Alamat ini menjadi `PHOTO_PUBLIC_URL`.
- [ ] Jangan pasang lifecycle rule di kedua bucket foto. API sendiri yang menghapus foto yang tidak terpakai, ditolak, atau disembunyikan ([ADR-0031](../decisions/0031-foto-laporan-di-bucket-r2-privat.md)).
- [ ] Token R2 (R2 → Manage API tokens) dengan izin "Object Read & Write" untuk kedua bucket ini saja. Catat Access Key ID, Secret Access Key, dan Account ID (32 karakter heksadesimal).

### 2.2 Cloudflare R2 untuk cadangan

Cadangan malam ini belum disalin ke R2. Log `akses-bogor-backup` setiap malam menulis "BACKUP_R2_REMOTE kosong; salinan R2 dilewati".

- [ ] Bucket privat terpisah, misalnya `akses-bogor-backup`, dengan lifecycle rule yang menghapus objek setelah 30 hari ([ADR-0025](../decisions/0025-api-dan-postgresql-di-vps-sendiri.md), [ADR-0026](../decisions/0026-api-dan-postgresql-di-evergarden-oci.md)).
- [ ] Token R2 terpisah dengan izin "Object Read & Write" untuk bucket itu saja.
- [ ] Berkas `/etc/akses-bogor/backup.env` dibuat dari contoh di akses-bogor-api:

  ```bash
  sudo install -m 0600 -o root -g root deploy/backup.env.example /etc/akses-bogor/backup.env
  sudo -e /etc/akses-bogor/backup.env
  ```

  Isi `BACKUP_R2_REMOTE=r2:akses-bogor-backup/postgres`, `RCLONE_CONFIG_R2_ENDPOINT` (dari dashboard R2), dan kedua kunci token. `rclone` sudah terpasang di Evergarden.

### 2.3 Klien OAuth Google

- [ ] Google Cloud Console → APIs & Services → Credentials → Create OAuth client ID, tipe "Web application" ([ADR-0029](../decisions/0029-akun-better-auth-dan-sesi-lewat-server-web.md)).
- [ ] Authorized JavaScript origins: `https://akses-bogor.vercel.app` dan `http://localhost:3000`. Tidak perlu redirect URI dan client secret, karena tombol Google mengirim ID token yang diperiksa API.
- [ ] OAuth consent screen: nama aplikasi AksesBogor, tautan `https://akses-bogor.vercel.app/kebijakan-privasi` dan `https://akses-bogor.vercel.app/syarat`, dan publishing status "In production". Dalam status "Testing", hanya akun test user yang bisa masuk.
- [ ] Client ID yang sama dipakai di dua tempat: `GOOGLE_CLIENT_ID` di API dan `NEXT_PUBLIC_GOOGLE_CLIENT_ID` di Vercel.

### 2.4 Resend

- [ ] Tambah domain pengirim di Resend, berupa subdomain yang dikelola tim, misalnya `kirim.yorhagarden.xyz` ([ADR-0030](../decisions/0030-email-transaksional-resend.md)).
- [ ] Salin record SPF dan DKIM yang ditampilkan Resend ke DNS Cloudflare, lalu tunggu sampai statusnya Verified.
- [ ] Tambah record DMARC, misalnya TXT `_dmarc.kirim.yorhagarden.xyz` berisi `v=DMARC1; p=none;`. Perketat setelah email terbukti sampai.
- [ ] API key dengan izin "Sending access" untuk domain itu saja.

### 2.5 Mapbox

- [ ] Token rahasia (`sk.`) dengan scope `tokens:write`, `styles:read`, `styles:tiles`, dan `fonts:read`, serta username akunnya ([ADR-0016](../decisions/0016-peta-dan-pencarian-mapbox.md)).
- [ ] Token yang sama dipakai untuk pencarian tempat di Laporkan ([ADR-0032](../decisions/0032-pencarian-tempat-lewat-api-mapbox-search-box.md)). Apakah Search Box butuh scope tambahan belum dicek dengan token produksi. Uji di langkah 7.3; bila pencarian menjawab galat, periksa scope tokennya.
- [ ] Pantau pemakaian bulanan di dashboard Mapbox. API membatasi pencarian 1.500 per hari untuk semua pengguna.

## 3. Secret API

Ubah dengan `sudo -e /etc/akses-bogor/api.env`. Berkas ini milik `root:aksesbogor` dengan mode `0640`. Contoh lengkapnya ada di `deploy/api.env.example` di akses-bogor-api.

| Variabel | Nilai | Catatan |
| --- | --- | --- |
| `NODE_ENV` | `production` | Sudah ada. Tanpa ini API berjalan sebagai development: rahasia akun tetap, kode masuk dicetak ke log, dan foto disimpan di folder lokal |
| `FRONTEND_ORIGINS` | `https://akses-bogor.vercel.app` | Origin web produksi harus di urutan pertama, karena dipakai untuk tautan di email status laporan. Domain lain ditambahkan sesudahnya, dipisah koma |
| `BETTER_AUTH_SECRET` | hasil `openssl rand -base64 48` | Minimal 32 karakter. Mengganti nilainya mengakhiri semua sesi |
| `BETTER_AUTH_URL` | `https://akses-bogor-api.yorhagarden.xyz` | Alamat publik API |
| `GOOGLE_CLIENT_ID` | `<…>.apps.googleusercontent.com` | Dari langkah 2.3 |
| `RESEND_API_KEY` | `re_…` | Dari langkah 2.4 |
| `EMAIL_FROM` | `"AksesBogor <masuk@kirim.yorhagarden.xyz>"` | Wajib diberi tanda kutip, lihat di bawah |
| `R2_ACCOUNT_ID` | 32 karakter heksadesimal | Kelima variabel R2 diisi bersama |
| `R2_ACCESS_KEY_ID`, `R2_SECRET_ACCESS_KEY` | dari token langkah 2.1 | |
| `R2_BUCKET_LAPORAN` | `akses-bogor-foto-laporan` | Bucket privat |
| `R2_BUCKET_PUBLIK` | `akses-bogor-foto` | Bucket publik |
| `PHOTO_PUBLIC_URL` | `https://foto.yorhagarden.xyz` | Custom domain bucket publik |
| `MAPBOX_SECRET_TOKEN` | `sk.…` | Diisi bersama `MAPBOX_USERNAME` |
| `MAPBOX_USERNAME` | username akun Mapbox | |
| `WEB_SERVER_SECRET` | hasil `openssl rand -hex 32` | Minimal 32 karakter, sama persis dengan nilai di Vercel |

- [ ] Semua variabel di atas terisi.
- [ ] Nilai yang berisi spasi atau `<` `>` diberi tanda kutip ganda. `deploy.sh` membaca berkas ini dengan `bash`, jadi `EMAIL_FROM=AksesBogor <masuk@…>` tanpa kutip membuat deploy gagal. Periksa dengan:

  ```bash
  sudo bash -c 'set -a; . /etc/akses-bogor/api.env; set +a; echo "sintaks ok: $EMAIL_FROM"'
  ```

- [ ] Format setiap nilai benar. Saat rilis baru mulai, API memeriksa format semua variabel dan tidak mau jalan bila ada yang salah. Contohnya `sk.` dan `re_` di awal token, `R2_ACCOUNT_ID` heksadesimal, URL lengkap dengan `https://`, dan pasangan yang tidak lengkap. Lognya menyebut nama variabel yang salah tanpa nilainya (langkah 6.3).

Rilis lama mengabaikan variabel baru, jadi berkas ini aman diubah sebelum rilis.

## 4. Vercel

Proyek `akses-bogor-web`, Settings:

| Pengaturan | Nilai |
| --- | --- |
| Environments → Production → Branch | `release` |
| Node.js Version | 24.x |
| Domains | `akses-bogor.vercel.app` sebagai domain produksi |
| Environment Variables, untuk Production dan Preview | `API_URL` = `https://akses-bogor-api.yorhagarden.xyz`, `ENABLE_EXPERIMENTAL_COREPACK` = `1`, `NEXT_PUBLIC_GOOGLE_CLIENT_ID` = client ID dari langkah 2.3, dan `WEB_SERVER_SECRET` = nilai yang sama dengan API, bertipe Sensitive |

- [ ] Semua pengaturan di atas sudah benar.
- [ ] `NEXT_PUBLIC_GOOGLE_CLIENT_ID` dibaca saat build. Bila diisi setelah deploy terakhir, deploy ulang supaya tombol Google muncul.
- [ ] Preview deployment memakai API produksi. Masuk dengan Google tidak jalan di alamat preview karena alamat itu tidak terdaftar sebagai origin Google; masuk dengan kode email tetap jalan dan membuat akun di database produksi.

Bila nanti memakai domain sendiri, misalnya `aksesbogor.id` atau `aksesbogor.site`:

1. Tambahkan domainnya di Vercel → Domains.
2. Tambahkan juga di origin JavaScript klien Google.
3. Tambahkan di `FRONTEND_ORIGINS`, di urutan pertama bila domain itu menjadi alamat utama.
4. Ganti tautan di OAuth consent screen.

## 5. Evergarden: log dan cadangan

- [ ] Log server disimpan 30 hari, sesuai Kebijakan Privasi 2026-11-10. Saat ini journald hanya dibatasi ukuran. Pengaturan ini berlaku untuk semua aplikasi di Evergarden, bukan hanya AksesBogor:

  ```bash
  sudo install -d -m 0755 /etc/systemd/journald.conf.d
  printf '[Journal]\nMaxRetentionSec=30day\n' | sudo tee /etc/systemd/journald.conf.d/30-hari.conf
  sudo systemctl restart systemd-journald
  ```

- [ ] Salinan cadangan ke R2 jalan. Setelah `backup.env` di langkah 2.2 diisi, log harus menulis "disalin ke r2:…":

  ```bash
  sudo systemctl start akses-bogor-backup
  journalctl -u akses-bogor-backup -n 20
  ```

## 6. Rilis

### 6.1 Cadangan tepat sebelum rilis

- [ ] Jalankan `sudo systemctl start akses-bogor-backup` dan catat nama dump terbaru dari `journalctl -u akses-bogor-backup -n 5`. Dump ini dipakai bila rollback butuh database lama (langkah 8).

### 6.2 PR rilis

Ketiga repo memakai PR dari `main` ke `release` dengan judul seperti `chore(release): bring main into release (#<issue>)`. PR ini di-merge dengan **merge commit, bukan squash**, supaya `release` membawa riwayat `main`:

```bash
gh pr create -R al-dioooo/<repo> --base release --head main --title "chore(release): bring main into release (#<issue>)"
gh pr merge <nomor> -R al-dioooo/<repo> --merge
```

Urutannya:

1. [ ] **akses-bogor-docs**, kapan saja.
2. [ ] **akses-bogor-api**. Timer men-deploy paling lama 5 menit kemudian, atau langsung dengan `sudo systemctl start akses-bogor-deploy`. Deploy ini menjalankan migrasi 0002 sampai 0005.
3. [ ] **akses-bogor-web**, segera setelah API sehat (langkah 6.3). Vercel membangun deployment produksi dari `release`.

API rilis lebih dulu karena web lama tidak punya fitur akun, jadi tetap jalan dengan API baru. Sebaliknya, web baru butuh API baru: keduanya meminta Kebijakan Privasi 2026-11-10, dan Lengkapi Akun gagal dengan 409 bila versinya berbeda. Jarak antara keduanya sebaiknya hanya beberapa menit.

### 6.3 Periksa API

```bash
journalctl -u akses-bogor-deploy -n 50     # harus berakhir dengan "rilis aktif: <sha>"
systemctl status akses-bogor-api
journalctl -u akses-bogor-api -n 50        # tidak ada "Konfigurasi environment tidak valid"
curl https://akses-bogor-api.yorhagarden.xyz/healthz
curl https://akses-bogor-api.yorhagarden.xyz/places
```

- [ ] `healthz` menjawab `{"status":"ok"}`, dan `/places` menjawab `"total":7`.
- [ ] Bila API tidak mau jalan karena environment, perbaiki `/etc/akses-bogor/api.env` lalu `sudo systemctl restart akses-bogor-api`. Bila tidak bisa cepat diperbaiki, lakukan rollback (langkah 8).

### 6.4 Periksa web

- [ ] Deployment produksi di Vercel berstatus Ready dan dibangun dari commit `release` terbaru.
- [ ] `https://akses-bogor.vercel.app` menampilkan Beranda, Jelajahi, Peta dengan peta Mapbox, Detail lokasi, Tentang, Kebijakan Privasi versi 10 November 2026, dan Syarat Layanan.

## 7. Setelah rilis

### 7.1 Admin pertama

- [ ] Calon admin masuk di `https://akses-bogor.vercel.app/masuk` dan melengkapi akun.
- [ ] Di Evergarden ([README akses-bogor-api, bagian Peran](https://github.com/al-dioooo/akses-bogor-api#peran)):

  ```bash
  sudo -u aksesbogor -H sh -c 'cd /srv/akses-bogor/current && COREPACK_ENABLE_DOWNLOAD_PROMPT=0 \
    corepack pnpm exec tsx --env-file=/etc/akses-bogor/api.env scripts/peran.ts --email <email> --peran admin'
  ```

### 7.2 Verifikator

- [ ] Setiap calon verifikator masuk dan melengkapi akun lebih dulu.
- [ ] Admin membuka `/verifikasi/tim` dan memberi peran verifikator dengan email yang persis sama. Akun yang belum ada menjawab "akun tidak ditemukan", dan akun yang belum dilengkapi ditolak. Peran admin hanya bisa diberikan lewat skrip di langkah 7.1 ([ADR-0035](../decisions/0035-peran-dan-panel-verifikator-di-web-yang-sama.md)).

### 7.3 Uji alur penuh di produksi

Butuh dua akun: **A** sebagai pelapor dan **B** sebagai verifikator atau admin, karena verifikator tidak bisa memeriksa laporannya sendiri. Pakai tempat nyata yang benar-benar diperiksa penguji, karena laporan yang terbit tidak bisa dihapus dari web.

1. [ ] A masuk dengan kode email. Email kode dari Resend sampai (periksa juga folder spam), dan Lengkapi Akun meminta persetujuan Kebijakan Privasi 10 November 2026.
2. [ ] Masuk dengan Google juga berhasil, dengan akun ketiga atau akun A.
3. [ ] A membuka Laporkan:
   - mencari tempat lewat pencarian (Mapbox Search Box);
   - memilih titik di peta, lalu kecamatan terisi otomatis;
   - mengisi keempat fasilitas;
   - menambah foto;
   - menekan "Kirim laporan". Foto terunggah (bucket privat), dan halaman Laporan terkirim tampil.
4. [ ] B membuka `/verifikasi`. Laporan A ada di antrean. Foto bisa dibuka, area kabur bisa ditandai, dan setiap jawaban bisa diperiksa.
5. [ ] B menerbitkan laporan. A menerima email "Laporan Anda sudah terbit", dan tautan di email itu mengarah ke `https://akses-bogor.vercel.app`.
6. [ ] Tempat itu tampil di Detail dengan foto yang sudah dikaburkan (dari `PHOTO_PUBLIC_URL`), di Jelajahi, dan sebagai marker di Peta.
7. [ ] Kirim laporan kedua yang sengaja tidak lengkap. B meminta klarifikasi, dan A menerima email lalu bisa menjawab dari Laporan saya. Setelah itu B menolaknya, dan A menerima email beserta alasannya. Laporan yang ditolak tidak tampil di publik.
8. [ ] Hapus akun uji yang tidak dipakai lagi dari Akun Saya. Email konfirmasi terakhir harus terkirim.

### 7.4 Pantau

- [ ] Hari pertama: `journalctl -u akses-bogor-api --since today` tanpa galat berulang, log pengiriman di dashboard Resend, dan pemakaian di dashboard Mapbox.
- [ ] Malam pertama: cadangan pukul 02.30 WIB tersalin ke R2.

## 8. Rollback

**Web.** Di Vercel → Deployments, pilih deployment produksi sebelumnya lalu Instant Rollback. Setelah itu revert merge rilis di `release` web dengan tombol Revert di PR rilisnya, supaya push berikutnya tidak men-deploy ulang versi yang bermasalah.

**API.** Hentikan timer supaya tidak men-deploy ulang, lalu kembalikan tautan `current` ke rilis sebelumnya. Rilis yang aktif sekarang adalah `7a4fdb5`, dan tiga rilis terakhir disimpan di `/srv/akses-bogor/releases`:

```bash
sudo systemctl stop akses-bogor-deploy.timer
sudo -u aksesbogor ln -sfn releases/<sha-lama> /srv/akses-bogor/current
sudo systemctl restart akses-bogor-api
```

Revert merge rilis di `release` API dengan tombol Revert di PR rilisnya, lalu nyalakan lagi timer dengan `sudo systemctl start akses-bogor-deploy.timer`.

Migrasi tidak ikut dibatalkan. Kode `7a4fdb5` belum diuji dengan skema 0005, walaupun perubahan skemanya hanya menambah tabel dan kolom yang boleh kosong. Bila API lama tetap gagal, pulihkan dump dari langkah 6.1 dengan perintah di [README akses-bogor-api, bagian Cadangan dan Pemulihan](https://github.com/al-dioooo/akses-bogor-api#cadangan-dan-pemulihan). Akun dan laporan yang dibuat setelah dump itu ikut hilang.

Web dan API di-rollback bersama, karena web baru tidak berfungsi dengan API lama.

## Referensi

- [PRD](../product/prd.md), [Kebijakan Privasi](../product/kebijakan-privasi.md), [Syarat Layanan](../product/syarat-layanan.md)
- README dan `deploy/` di [akses-bogor-api](https://github.com/al-dioooo/akses-bogor-api); README di [akses-bogor-web](https://github.com/al-dioooo/akses-bogor-web)
- [ADR-0016](../decisions/0016-peta-dan-pencarian-mapbox.md), [ADR-0025](../decisions/0025-api-dan-postgresql-di-vps-sendiri.md), [ADR-0026](../decisions/0026-api-dan-postgresql-di-evergarden-oci.md), [ADR-0029](../decisions/0029-akun-better-auth-dan-sesi-lewat-server-web.md) sampai [ADR-0040](../decisions/0040-syarat-layanan-lisensi-laporan-dan-persetujuan.md)
