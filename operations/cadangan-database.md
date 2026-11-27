# Runbook Cadangan Database

| | |
| --- | --- |
| Status | Berlaku |
| Tanggal | 2026-11-27 |
| Berlaku untuk | Server produksi akses-bogor-api (fatir-mitya) |
| Issue | #148, #154 |

Setiap malam database AksesBogor di-dump dan dua salinannya disimpan: tujuh dump terakhir di server, dan satu salinan di bucket R2 privat selama 30 hari ([ADR-0025](../decisions/0025-api-dan-postgresql-di-vps-sendiri.md), [ADR-0026](../decisions/0026-api-dan-postgresql-di-evergarden-oci.md)). Salinan R2 dienkripsi dengan `age` sebelum meninggalkan server. Dokumen ini mencatat keadaan salinan R2, cara enkripsinya, cara melihatnya, dan cara menguji bahwa salinan itu bisa dipulihkan. Perintah `sudo` dijalankan admin server. Nilai dalam `<…>` diisi sendiri.

## 1. Keadaan Sekarang

Salinan R2 aktif di server produksi sejak 2026-11-27. Sebelumnya log `akses-bogor-backup` setiap malam menulis "BACKUP_R2_REMOTE kosong; salinan R2 dilewati".

Enkripsi salinan R2 berlaku setelah langkah [3.3](#33-memasang-di-server) dijalankan di server produksi. Selama `BACKUP_AGE_RECIPIENT` kosong, dump masih disalin tanpa enkripsi dan log menulis "PERINGATAN: BACKUP_AGE_RECIPIENT kosong; dump disalin ke R2 tanpa enkripsi".

| Hal | Nilai |
| --- | --- |
| Jadwal | `akses-bogor-backup.timer`, setiap malam pukul 02.30 WIB |
| Skrip | `/usr/local/lib/akses-bogor/backup.sh` (`deploy/backup.sh` di akses-bogor-api), dijalankan service sebagai user `postgres` |
| Salinan di server | Tujuh dump terakhir di `/var/backups/akses-bogor/aksesbogor-<waktu UTC>.dump` |
| Bucket R2 | `akses-bogor-backup`, privat, terpisah dari bucket foto |
| Remote rclone | `r2:akses-bogor-backup/postgres` |
| Masa simpan di R2 | Lifecycle rule menghapus objek setelah 30 hari, ditambah aturan bawaan R2 yang membatalkan unggahan multipart yang tidak selesai |
| Token R2 | Object Read & Write untuk bucket `akses-bogor-backup` saja |
| Enkripsi salinan R2 | `age` ke kunci publik di `BACKUP_AGE_RECIPIENT`. Objeknya bernama `aksesbogor-<waktu UTC>.dump.age` |
| Kunci privat | `~/credentials/akses-bogor/age-cadangan.key` di mesin tim. Tidak di `backup.env` dan tidak di Cloudflare |
| Konfigurasi | `/etc/akses-bogor/backup.env`, pemilik `root:root`, mode `0600`. Contohnya `deploy/backup.env.example` di akses-bogor-api |
| rclone | 1.60.1 dari paket Debian, dikonfigurasi lewat variabel `RCLONE_CONFIG_R2_*`, tanpa `rclone.conf` |

Cakupan token dijelaskan di komentar `deploy/backup.env.example` dan di [langkah 2.2 daftar periksa rilis pertama](rilis-pertama.md). Nilai token dan Access Key hanya ada di `/etc/akses-bogor/backup.env`, tidak di repo, issue, PR, atau chat. Data akun yang dihapus masih ada di salinan ini sampai 30 hari ([ADR-0024](../decisions/0024-data-setelah-akun-dihapus.md)).

## 2. rclone 1.60 dan R2

rclone 1.60 memanggil `HEAD …?versionId=` setelah setiap unggahan, dan R2 menjawab 501 NotImplemented. Akibatnya percobaan pertama gagal dan hanya percobaan ulang yang lolos. `RCLONE_CONFIG_R2_NO_HEAD=true` di `backup.env` menghilangkan panggilan itu. Keutuhan unggahan tetap diperiksa lewat Content-MD5. Baris ini sudah ada di `deploy/backup.env.example` (akses-bogor-api#181). Server yang `backup.env`-nya dibuat sebelum itu perlu menambahkannya sendiri.

Skrip `backup.sh` yang sudah memuat perbaikan #181 menulis nama objek di log: `disalin ke r2:akses-bogor-backup/postgres/aksesbogor-<waktu UTC>.dump`. Versi sebelumnya hanya menulis nama remote. Dengan enkripsi, nama objeknya berakhiran `.dump.age`.

## 3. Enkripsi Salinan R2

Pemeriksaan UU PDP (Pasal 35 dan 39) menemukan bahwa salinan R2 berisi seluruh data pribadi tanpa enkripsi. Token R2 yang hanya berlaku untuk satu bucket melindungi dari token yang bocor, tetapi tidak dari akun Cloudflare yang diambil alih. Karena itu dump dienkripsi dengan [age](https://age-encryption.org) sebelum diunggah, dan kunci untuk membukanya disimpan di luar Cloudflare.

### 3.1 Cara Kerja

1. `pg_dump` menulis dump ke `/var/backups/akses-bogor` seperti biasa. Tujuh dump di server tetap tidak dienkripsi, karena server itu sendiri sudah memegang databasenya.
2. Bila `BACKUP_R2_REMOTE` dan `BACKUP_AGE_RECIPIENT` diisi, `backup.sh` menjalankan `age -r "$BACKUP_AGE_RECIPIENT"` dan menulis hasilnya ke berkas sementara di folder yang sama.
3. Bila `age` berhasil, berkas itu diunggah sebagai `<dump>.age`. Dump polos tidak diunggah. Berkas sementara dihapus, baik skrip berhasil maupun gagal.
4. Bila `age` gagal atau belum terpasang, skrip berhenti dengan kode bukan nol sebelum `rclone` dipanggil. Tidak ada yang diunggah, dan unit `akses-bogor-backup` tercatat gagal di `journalctl`.
5. Bila `BACKUP_AGE_RECIPIENT` kosong, dump disalin tanpa enkripsi seperti sebelumnya, dan log menulis peringatan.

Server hanya memegang kunci publik (diawali `age1`). Kunci publik hanya bisa mengenkripsi, jadi `backup.env` yang bocor tidak membuka isi salinan R2, dan Cloudflare hanya menyimpan berkas yang tidak bisa dibacanya.

### 3.2 Kunci

| Berkas | Isi |
| --- | --- |
| `~/credentials/akses-bogor/age-cadangan.pub` | Kunci publik, sama dengan nilai `BACKUP_AGE_RECIPIENT` |
| `~/credentials/akses-bogor/age-cadangan.key` | Kunci privat dari `age-keygen`, mode `0600` |

- Kunci privat hanya ada di `~/credentials/akses-bogor/` di mesin tim, dan admin yang menyalinnya antarmesin. Kunci ini tidak pernah diunggah ke Cloudflare, tidak ditaruh di `backup.env`, dan tidak masuk repo, issue, PR, atau chat.
- Tanpa kunci privat, salinan R2 tidak bisa dipulihkan. Simpan di lebih dari satu mesin.
- Mengganti kunci: buat pasangan baru dengan `age-keygen -o <berkas>`, ganti `BACKUP_AGE_RECIPIENT`, dan simpan kunci privat lama 30 hari lagi, sampai salinan terakhir yang dienkripsi dengannya dihapus lifecycle. Bila kunci lama bocor, hapus juga salinan R2 yang dienkripsi dengannya.

### 3.3 Memasang di Server

- [ ] Pasang `age` dari paket Debian:

  ```bash
  sudo apt install -y age
  age --version
  ```

- [ ] Pasang `backup.sh` yang sudah memuat enkripsi (akses-bogor-api#187). Skrip ini tidak ikut diperbarui oleh deploy otomatis, jadi salin dari rilis aktif setelah perubahannya masuk ke `release`:

  ```bash
  sudo install -m 0755 /srv/akses-bogor/current/deploy/backup.sh /usr/local/lib/akses-bogor/backup.sh
  ```

- [ ] Tambahkan kunci publik ke `backup.env`. Nilainya isi `~/credentials/akses-bogor/age-cadangan.pub`:

  ```bash
  sudo -e /etc/akses-bogor/backup.env   # BACKUP_AGE_RECIPIENT=age1…
  ```

- [ ] Jalankan cadangan sekarang. Log harus menulis `disalin ke r2:akses-bogor-backup/postgres/aksesbogor-<waktu UTC>.dump.age` tanpa peringatan:

  ```bash
  sudo systemctl start akses-bogor-backup
  journalctl -u akses-bogor-backup -n 5
  ```

- [ ] Jalankan [uji pemulihan](#5-uji-pemulihan-dari-r2) dengan salinan `.age` itu.

Dump `.dump` lama yang belum dienkripsi tetap di R2 sampai lifecycle menghapusnya setelah 30 hari. Setelah uji pemulihan salinan `.age` lulus, dump lama itu boleh dihapus lebih awal. Lihat dulu daftarnya dengan `--dry-run`:

```bash
sudo bash -c 'set -a; . /etc/akses-bogor/backup.env; set +a; rclone delete --dry-run --include "*.dump" "$BACKUP_R2_REMOTE"'
```

Bila daftarnya hanya berisi `.dump` lama, jalankan lagi tanpa `--dry-run`.

## 4. Melihat Salinan di R2

`backup.env` hanya bisa dibaca root, jadi baca lewat shell root dan ekspor isinya ke rclone:

```bash
sudo bash -c 'set -a; . /etc/akses-bogor/backup.env; set +a; rclone lsl "$BACKUP_R2_REMOTE"'
```

Keluarannya satu baris per dump: ukuran, waktu, dan nama berkas. Salinan terenkripsi berakhiran `.dump.age`. Karena dump dibuat tiap malam dan lifecycle menghapus yang lebih dari 30 hari, jumlahnya naik sampai sekitar 30. Dump yang hilang di tengah deretan berarti cadangan malam itu gagal. Cek `journalctl -u akses-bogor-backup`.

## 5. Uji Pemulihan dari R2

Salinan baru berguna bila pemulihannya pernah dicoba. Salinan `.age` hanya bisa dibuka dengan kunci privat, jadi uji ini dijalankan admin yang menyimpan `~/credentials/akses-bogor/age-cadangan.key` di server itu. Hasil dekripsi langsung diteruskan ke `pg_restore` lewat pipa, jadi dump polos tidak pernah ditulis ke disk. Database ujinya tetap berisi data pribadi (akun dan laporan), jadi hapus di akhir.

- [ ] Buat dump baru supaya hitungan bisa dibandingkan dengan database hidup, lalu pastikan log menulis `disalin ke r2:…/aksesbogor-<waktu UTC>.dump.age`:

  ```bash
  sudo systemctl start akses-bogor-backup
  journalctl -u akses-bogor-backup -n 5
  ```

- [ ] Cari port PostgreSQL yang dipakai service (`PGPORT`). Ganti `<port>` di langkah berikutnya dengan nilainya:

  ```bash
  systemctl show -p Environment akses-bogor-backup
  ```

- [ ] Unduh salinan `.age` terbaru dari R2 ke folder milik admin. Berkas ini terenkripsi, jadi boleh disimpan di luar folder `postgres`. `--include` memastikan yang diambil salinan terenkripsi, bukan dump `.dump` lama:

  ```bash
  mkdir -m 0700 ~/uji-pulih
  sudo bash -c 'set -a; . /etc/akses-bogor/backup.env; set +a
    terbaru=$(rclone lsf --include "*.dump.age" "$BACKUP_R2_REMOTE" | sort | tail -n 1)
    echo "dump terbaru: $terbaru"
    rclone copyto "$BACKUP_R2_REMOTE/$terbaru" "$1/$terbaru"
    chown "$2": "$1/$terbaru"' _ ~/uji-pulih "$USER"
  ```

- [ ] Buat database uji sebagai `postgres`, lalu dekripsi salinan itu dan teruskan ke `pg_restore`. `age` membaca kunci privat sebagai admin, dan `pg_restore` berjalan sebagai `postgres`. `--no-owner --no-privileges` dipakai karena role aplikasi tidak perlu dibuat ulang untuk uji ini:

  ```bash
  sudo -u postgres createdb -p <port> aksesbogor_uji_pulih
  age -d -i ~/credentials/akses-bogor/age-cadangan.key ~/uji-pulih/<nama dump terbaru> \
    | sudo -u postgres pg_restore -p <port> --no-owner --no-privileges -d aksesbogor_uji_pulih
  ```

  `pg_restore` bisa membaca dump format custom dari pipa untuk pemulihan penuh seperti ini. Untuk pemulihan paralel (`pg_restore -j`), dekripsi dulu ke berkas di folder yang hanya bisa dibaca `postgres`.

- [ ] Bandingkan jumlah baris tabel `places`, `reports`, `users`, `osm_places`, dan `knowledge_passages` di database hidup dan database uji:

  ```bash
  for db in aksesbogor aksesbogor_uji_pulih; do
    echo "== $db"
    sudo -u postgres psql -p <port> -d "$db" -Atc "
      select 'places', count(*) from places union all
      select 'reports', count(*) from reports union all
      select 'users', count(*) from users union all
      select 'osm_places', count(*) from osm_places union all
      select 'knowledge_passages', count(*) from knowledge_passages"
  done
  ```

  Kedua daftar harus sama. Selisih kecil wajar hanya bila ada laporan atau akun masuk di antara dump dan hitungan.

- [ ] Hapus database uji dan berkas unduhan:

  ```bash
  sudo -u postgres dropdb -p <port> aksesbogor_uji_pulih
  rm -rf ~/uji-pulih
  ```

Ulangi uji ini setelah token R2 diganti, rclone diperbarui, server dipindah, atau kunci age diganti.

### Hasil 2026-11-27

Uji pertama dijalankan di server produksi dengan dump terbaru dari R2, sebelum enkripsi age. Hitungan baris sama persis dengan database hidup, dan database uji sudah dihapus setelahnya.

| Tabel | Database hidup | Hasil pemulihan |
| --- | --- | --- |
| `places` | 13 | 13 |
| `reports` | 1 | 1 |
| `users` | 6 | 6 |
| `osm_places` | 644 | 644 |
| `knowledge_passages` | 421 | 421 |

Uji ini memenuhi syarat uji pemulihan dari dump R2 di [ADR-0025](../decisions/0025-api-dan-postgresql-di-vps-sendiri.md). Uji ini hanya membuktikan dump bisa dibaca dan isinya lengkap. Memulihkan produksi sungguhan memakai langkah di [README akses-bogor-api, bagian Cadangan dan Pemulihan](https://github.com/al-dioooo/akses-bogor-api#cadangan-dan-pemulihan).
