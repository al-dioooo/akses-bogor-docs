# Runbook Cadangan Database

| | |
| --- | --- |
| Status | Berlaku |
| Tanggal | 2026-11-27 |
| Berlaku untuk | Server produksi akses-bogor-api (fatir-mitya) |
| Issue | #148 |

Setiap malam database AksesBogor di-dump dan dua salinannya disimpan: tujuh dump terakhir di server, dan satu salinan di bucket R2 privat selama 30 hari ([ADR-0025](../decisions/0025-api-dan-postgresql-di-vps-sendiri.md), [ADR-0026](../decisions/0026-api-dan-postgresql-di-evergarden-oci.md)). Dokumen ini mencatat keadaan salinan R2, cara melihatnya, dan cara menguji bahwa salinan itu bisa dipulihkan. Perintah `sudo` dijalankan admin server. Nilai dalam `<…>` diisi sendiri.

## 1. Keadaan Sekarang

Salinan R2 aktif di server produksi sejak 2026-11-27. Sebelumnya log `akses-bogor-backup` setiap malam menulis "BACKUP_R2_REMOTE kosong; salinan R2 dilewati".

| Hal | Nilai |
| --- | --- |
| Jadwal | `akses-bogor-backup.timer`, setiap malam pukul 02.30 WIB |
| Skrip | `/usr/local/lib/akses-bogor/backup.sh` (`deploy/backup.sh` di akses-bogor-api), dijalankan service sebagai user `postgres` |
| Salinan di server | Tujuh dump terakhir di `/var/backups/akses-bogor/aksesbogor-<waktu UTC>.dump` |
| Bucket R2 | `akses-bogor-backup`, privat, terpisah dari bucket foto |
| Remote rclone | `r2:akses-bogor-backup/postgres` |
| Masa simpan di R2 | Lifecycle rule menghapus objek setelah 30 hari, ditambah aturan bawaan R2 yang membatalkan unggahan multipart yang tidak selesai |
| Token R2 | Object Read & Write untuk bucket `akses-bogor-backup` saja |
| Konfigurasi | `/etc/akses-bogor/backup.env`, pemilik `root:root`, mode `0600`. Contohnya `deploy/backup.env.example` di akses-bogor-api |
| rclone | 1.60.1 dari paket Debian, dikonfigurasi lewat variabel `RCLONE_CONFIG_R2_*`, tanpa `rclone.conf` |

Cakupan token dijelaskan di komentar `deploy/backup.env.example` dan di [langkah 2.2 daftar periksa rilis pertama](rilis-pertama.md). Nilai token dan Access Key hanya ada di `/etc/akses-bogor/backup.env`, tidak di repo, issue, PR, atau chat. Data akun yang dihapus masih ada di salinan ini sampai 30 hari ([ADR-0024](../decisions/0024-data-setelah-akun-dihapus.md)).

## 2. rclone 1.60 dan R2

rclone 1.60 memanggil `HEAD …?versionId=` setelah setiap unggahan, dan R2 menjawab 501 NotImplemented. Akibatnya percobaan pertama gagal dan hanya percobaan ulang yang lolos. `RCLONE_CONFIG_R2_NO_HEAD=true` di `backup.env` menghilangkan panggilan itu. Keutuhan unggahan tetap diperiksa lewat Content-MD5. Baris ini sudah ada di `deploy/backup.env.example` (akses-bogor-api#181). Server yang `backup.env`-nya dibuat sebelum itu perlu menambahkannya sendiri.

Skrip `backup.sh` yang sudah memuat perbaikan #181 menulis nama objek di log: `disalin ke r2:akses-bogor-backup/postgres/aksesbogor-<waktu UTC>.dump`. Versi sebelumnya hanya menulis nama remote.

## 3. Melihat Salinan di R2

`backup.env` hanya bisa dibaca root, jadi baca lewat shell root dan ekspor isinya ke rclone:

```bash
sudo bash -c 'set -a; . /etc/akses-bogor/backup.env; set +a; rclone lsl "$BACKUP_R2_REMOTE"'
```

Keluarannya satu baris per dump: ukuran, waktu, dan nama berkas. Karena dump dibuat tiap malam dan lifecycle menghapus yang lebih dari 30 hari, jumlahnya naik sampai sekitar 30. Dump yang hilang di tengah deretan berarti cadangan malam itu gagal. Cek `journalctl -u akses-bogor-backup`.

## 4. Uji Pemulihan dari R2

Salinan baru berguna bila pemulihannya pernah dicoba. Dump berisi data pribadi (akun dan laporan), jadi simpan berkas unduhannya di folder yang hanya bisa dibaca `postgres` dan hapus bersama database ujinya di akhir.

- [ ] Buat dump baru supaya hitungan bisa dibandingkan dengan database hidup, lalu pastikan log menulis "disalin ke r2:…":

  ```bash
  sudo systemctl start akses-bogor-backup
  journalctl -u akses-bogor-backup -n 5
  ```

- [ ] Cari port PostgreSQL yang dipakai service (`PGPORT`). Ganti `<port>` di langkah berikutnya dengan nilainya:

  ```bash
  systemctl show -p Environment akses-bogor-backup
  ```

- [ ] Unduh dump terbaru dari R2 ke folder yang hanya bisa dibaca `postgres`:

  ```bash
  sudo install -d -m 0700 -o postgres -g postgres /var/tmp/uji-pulih
  sudo bash -c 'set -a; . /etc/akses-bogor/backup.env; set +a
    terbaru=$(rclone lsf "$BACKUP_R2_REMOTE" | sort | tail -n 1)
    echo "dump terbaru: $terbaru"
    rclone copyto "$BACKUP_R2_REMOTE/$terbaru" "/var/tmp/uji-pulih/$terbaru"
    chown postgres:postgres "/var/tmp/uji-pulih/$terbaru"
    chmod 0600 "/var/tmp/uji-pulih/$terbaru"'
  ```

- [ ] Buat database uji sebagai `postgres` dan pulihkan dump ke dalamnya. `--no-owner --no-privileges` dipakai karena role aplikasi tidak perlu dibuat ulang untuk uji ini:

  ```bash
  sudo -u postgres createdb -p <port> aksesbogor_uji_pulih
  sudo -u postgres pg_restore -p <port> --no-owner --no-privileges -d aksesbogor_uji_pulih /var/tmp/uji-pulih/<nama dump terbaru>
  ```

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
  sudo rm -rf /var/tmp/uji-pulih
  ```

Ulangi uji ini setelah token R2 diganti, rclone diperbarui, atau server dipindah.

### Hasil 2026-11-27

Uji pertama dijalankan di server produksi dengan dump terbaru dari R2. Hitungan baris sama persis dengan database hidup, dan database uji sudah dihapus setelahnya.

| Tabel | Database hidup | Hasil pemulihan |
| --- | --- | --- |
| `places` | 13 | 13 |
| `reports` | 1 | 1 |
| `users` | 6 | 6 |
| `osm_places` | 644 | 644 |
| `knowledge_passages` | 421 | 421 |

Uji ini memenuhi syarat uji pemulihan dari dump R2 di [ADR-0025](../decisions/0025-api-dan-postgresql-di-vps-sendiri.md). Uji ini hanya membuktikan dump bisa dibaca dan isinya lengkap. Memulihkan produksi sungguhan memakai langkah di [README akses-bogor-api, bagian Cadangan dan Pemulihan](https://github.com/al-dioooo/akses-bogor-api#cadangan-dan-pemulihan).
