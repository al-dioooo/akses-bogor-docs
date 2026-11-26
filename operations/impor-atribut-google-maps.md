# Impor Atribut Google Maps

| | |
| --- | --- |
| Status | Draft |
| Tanggal | 2026-11-26 |
| Berlaku untuk | Pengambilan dan impor atribut aksesibilitas Google Maps ke kandidat OSM, dan pembaruannya ([ADR-0056](../decisions/0056-impor-atribut-aksesibilitas-google-maps-lewat-apify.md)) |
| Issue | #126 |

Kerjakan dari atas ke bawah di Evergarden. Nilai dalam `<…>` diisi sendiri. Perintah `sudo` dijalankan admin Evergarden. Token Apify hanya ditulis di `/etc/akses-bogor/apify.env`, tidak pernah di chat, issue, PR, atau repo.

Semua perintah skrip memakai bentuk yang sama dengan skrip API lain: user `aksesbogor`, folder `/srv/akses-bogor/current`, dan `api.env`. Supaya baris di bawah pendek, buat dulu fungsi ini di shell admin:

```bash
api() { sudo -u aksesbogor -H sh -c "cd /srv/akses-bogor/current && COREPACK_ENABLE_DOWNLOAD_PROMPT=0 APIFY_ENV_FILE=/etc/akses-bogor/apify.env corepack pnpm exec tsx --env-file=/etc/akses-bogor/api.env $*"; }
```

## 1. Sebelum mulai

- [ ] PR ADR-0056 di akses-bogor-api sudah di `release` dan ter-deploy: `ls /srv/akses-bogor/current/scripts/petunjuk/` menampilkan `google-maps-ambil.ts` dan `google-maps-impor.ts`.
- [ ] Kandidat OSM sudah dimuat. Perintah ini harus menampilkan sekitar 644:

  ```bash
  sudo -u postgres psql -p 5433 aksesbogor -Atc "SELECT count(*) FROM osm_places"
  ```

- [ ] Cadangan dulu: `sudo systemctl start akses-bogor-backup`, lalu `journalctl -u akses-bogor-backup -n 5`.
- [ ] Berkas token. Ambil token di console.apify.com (Settings → API & Integrations), lalu:

  ```bash
  sudo install -m 0640 -o root -g aksesbogor /dev/null /etc/akses-bogor/apify.env
  sudo -e /etc/akses-bogor/apify.env   # satu baris: APIFY_TOKEN=<token>
  ```

- [ ] Folder data. Skrip menyimpan dataset di `$HOME/data/akses-bogor/google-maps` milik user `aksesbogor`:

  ```bash
  sudo -u aksesbogor -H sh -c 'mkdir -p "$HOME/data/akses-bogor/google-maps" && echo "$HOME/data/akses-bogor/google-maps"'
  ```

  Bila gagal karena user itu tidak punya home yang bisa ditulis, buat `/srv/akses-bogor/data/google-maps` milik `aksesbogor`, tambahkan `--keluaran /srv/akses-bogor/data/google-maps` di langkah 3, dan sebutkan path berkasnya di langkah 4 dan 5.

## 2. Rencana dan Biaya

Tanpa memanggil Apify:

```bash
api scripts/petunjuk/google-maps-ambil.ts --rencana
```

Hasil yang diharapkan dengan data OSM 2026-11-22: sekitar 528 pencarian, perkiraan paling banyak $2,11 dengan paket Free. Bila angkanya jauh berbeda, berhenti dan laporkan.

## 3. Ambil, Run Kecil

```bash
api scripts/petunjuk/google-maps-ambil.ts --limit 20
```

Skrip mencetak tautan run di console Apify, lalu `Tersimpan: <berkas>`. Catat path berkas itu. Periksa `.meta.json` di sebelahnya:

```bash
sudo -u aksesbogor -H sh -c 'cat "<berkas tanpa .json>.meta.json"' | head -20
```

- [ ] `status` bernilai `SUCCEEDED`.
- [ ] `biayaUsd` sekitar $0,08 atau kurang, dan `event` berisi `place-scraped` paling banyak 20. Bila ada `place-details-scraped`, biaya run penuh naik sekitar $1,06 (ADR-0056, Biaya).

## 4. Dry Run

```bash
api scripts/petunjuk/google-maps-impor.ts "<berkas>" --dry-run 2>&1 | tee ~/impor-google-dry-run-$(date +%F).txt
```

Setiap baris `cocok` menyebut kandidat OSM, nama di Google, jarak, dan keputusannya. Periksa:

- [ ] Nama OSM dan nama Google menunjuk tempat yang sama, terutama pasangan di atas 150 m dan semua kampus dan rumah sakit. Bila ragu, buka tautan OSM `https://www.openstreetmap.org/<osm>` dan cari nama Google-nya di Google Maps.
- [ ] Ringkasan per kategori: berapa tempat yang cocok punya atribut. Bila hampir semua "tanpa atribut", berhenti dan laporkan; Google mungkin tidak menampilkan grup Aksesibilitas ke aktor (ADR-0056, Konsekuensi).
- [ ] "diperiksa tim atau bentuk tak dikenal" dan "cara lain" sesuai yang diketahui. "Cara lain" adalah salinan 2026-11-23, yang dibiarkan kecuali diputuskan lain.

Pasangan yang salah dikecualikan dengan `--kecuali <osm>,<osm>` (mis. `--kecuali node/123,way/456`). Jalankan dry run lagi dengan opsi itu sampai semua pasangan benar, dan pakai opsi yang sama di langkah 5.

## 5. Impor

Perintah yang sama tanpa `--dry-run`:

```bash
api scripts/petunjuk/google-maps-impor.ts "<berkas>" 2>&1 | tail -8
```

Baris terakhir menyebut jumlah salinan yang tersimpan. Periksa di database:

```bash
sudo -u postgres psql -p 5433 aksesbogor -c "SELECT tags->'aksesbogor:google_maps'->>'metode' AS metode, count(*) FROM osm_places WHERE tags ? 'aksesbogor:google_maps' GROUP BY 1"
```

Selama `PROVIDER_HINTS_ENABLED` menyala, kartu kandidat yang cocok di Peta menampilkan "Menurut Google Maps (diambil …)" di bagian "Petunjuk dari sumber lain (belum diperiksa tim)".

## 6. Run Penuh

Ulangi langkah 3 sampai 5 tanpa `--limit`. Run penuh juga mengambil 20 tempat dari run kecil; biayanya kecil, dan impornya menimpa salinan dari berkas lama dengan yang baru.

## 7. Pembaruan

- Setiap tiga bulan: langkah 3 (tanpa `--limit`), 4, dan 5.
- Setelah `pnpm pengetahuan:fasilitas` memuat ulang OSM, semua salinan hilang. Impor lagi berkas terakhir (langkah 4 dan 5, dengan `--kecuali` yang sama). Bila OSM baru diunduh, bukan dimuat dari `--salinan`, ambil ulang juga (langkah 3), karena kandidat baru bisa muncul.

## 8. Membatalkan

- Menyembunyikan semua petunjuk, langsung maupun tersimpan: `PROVIDER_HINTS_ENABLED=false` di `/etc/akses-bogor/api.env`, lalu `sudo systemctl restart akses-bogor-api`.
- Menghapus semua salinan dari impor ini, tanpa menyentuh salinan cara lain:

  ```bash
  sudo -u postgres psql -p 5433 aksesbogor -c "UPDATE osm_places SET tags = tags - 'aksesbogor:google_maps' WHERE tags->'aksesbogor:google_maps'->>'metode' = 'apify:compass/crawler-google-places'"
  ```

- Menghapus dataset mentah: `sudo -u aksesbogor -H sh -c 'rm -r "$HOME/data/akses-bogor/google-maps"'`.
- Mencabut token di console Apify, lalu `sudo rm /etc/akses-bogor/apify.env`.
