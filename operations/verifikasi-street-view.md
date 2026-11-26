# Verifikasi Street View

| | |
| --- | --- |
| Status | Draft |
| Tanggal | 2026-11-27 |
| Berlaku untuk | Memeriksa data yang belum diperiksa dengan bukti Street View, lalu menerapkan atau membatalkan hasilnya di production ([ADR-0057](../decisions/0057-verifikasi-dengan-bukti-street-view.md)) |
| Issue | #140 |

Ada dua host:

- **Evergarden**, server production. Database dan R2 hanya disentuh di sini, lewat skrip API.
- **Host pekerja**, tempat gambar Street View diambil: host dengan gosom/google-maps-scraper dan Chrome untuk Playwright. Pada 2026-11-27 itu YoRHa Garden. Host ini tidak punya akses ke database production.

Kerjakan dari atas ke bawah. Nilai dalam `<…>` diisi sendiri. Perintah `sudo` di Evergarden dijalankan admin Evergarden. Satu putaran diberi nama `<putaran>`, mis. `2026-12-01`, dan dipakai sebagai nama folder dan isian `round` di manifest.

Di Evergarden, semua perintah skrip memakai bentuk yang sama dengan skrip API lain. Buat dulu fungsi ini di shell admin:

```bash
api() { sudo -u aksesbogor -H sh -c "cd /srv/akses-bogor/current && COREPACK_ENABLE_DOWNLOAD_PROMPT=0 corepack pnpm exec tsx --env-file=/etc/akses-bogor/api.env $*"; }
```

## 1. Sebelum Mulai (Evergarden)

- [ ] PR API ADR-0057 sudah di `release` dan ter-deploy: `ls /srv/akses-bogor/current/scripts/verifikasi/` menampilkan `daftar.ts` dan `street-view.ts`, dan migrasi `0011` sudah jalan:

  ```bash
  sudo -u postgres psql -p 5433 aksesbogor -Atc "SELECT column_name FROM information_schema.columns WHERE table_name = 'places' AND column_name IN ('verification_source', 'street_view_image_date')"
  ```

- [ ] PR web ADR-0057 sudah ter-deploy. Web lama menulis "Diverifikasi" untuk tempat Street View, jadi **jangan menerapkan apa pun sebelum web baru tayang**.
- [ ] Cadangan lewat timer biasa: `sudo systemctl start akses-bogor-backup`, lalu `journalctl -u akses-bogor-backup -n 5`.
- [ ] Cadangan tambahan yang mudah ditemukan, khusus putaran ini:

  ```bash
  sudo install -d -m 0700 -o postgres /var/backups/akses-bogor/street-view
  sudo -u postgres pg_dump -p 5433 -Fc aksesbogor -f /var/backups/akses-bogor/street-view/sebelum-<putaran>.dump
  ```

## 2. Daftar Data yang Belum Diperiksa (Evergarden)

```bash
api scripts/verifikasi/daftar.ts > ~/verifikasi-daftar-<putaran>.csv
```

Skrip berjalan di transaksi read-only, jadi tidak ada yang berubah. CSV masuk ke berkas, dan jumlah per jenis tercetak di terminal. Kolom CSV:

| Kolom | Isi |
| --- | --- |
| `jenis` | `petunjuk_google`, `petunjuk_osm`, `tempat_admin`, `laporan`, atau `kandidat` |
| `id` | Kandidat dan petunjuk: `node/123`, `way/456`. Tempat dan laporan: UUID |
| `status` | Petunjuk: `belum_diperiksa_tim`. Tempat: status aksesibilitas. Laporan: `menunggu` atau `klarifikasi`. Kandidat: `tayang` |
| `nama`, `kategori`, `kecamatan`, `lat`, `lng` | Dari tempat atau kandidat. Kosong untuk laporan |
| `klaim` | Isi petunjuk (mis. opsi aksesibilitas Google atau `wheelchair=yes`), jawaban fasilitas tempat admin, jenis laporan (`baru`, `perubahan`, `kekurangan`), atau tag `wheelchair` kandidat |
| `tayang_di_peta` | Kandidat dan petunjuk: apakah kandidatnya sedang tampil sebagai "Belum ada data" |

Baris laporan hanya berisi id, jenis laporan, dan status: tanpa pelapor, nama tempat, titik, atau isian teks. Laporan tidak pernah diproses alat ini; laporan tetap diperiksa verifikator lewat antrean biasa.

Salin CSV ke host pekerja, mis. `scp ~/verifikasi-daftar-<putaran>.csv <host-pekerja>:`. Hapus salinannya di Evergarden setelah tersalin.

## 3. Ambil Gambar dan Nilai (Host Pekerja)

Pilih baris `petunjuk_google`, `petunjuk_osm`, dan `tempat_admin` yang akan diperiksa. Untuk setiap data:

1. Cari tempatnya dan pano Street View-nya dengan gosom/google-maps-scraper.
2. Tentukan titik pintu masuk: node OSM `entrance=*`, lalu `barrier=gate`, lalu pin Google Maps.
3. Cari pano resmi Google (bukan photosphere) di titik jalan dekat pintu masuk: bergambar 2022 atau lebih baru, paling jauh 40 m, terbaru, lalu terdekat.
4. Ambil gambar lebar (fov 80, pitch 0) dan gambar dekat (fov 32, pitch −7), keduanya menghadap pintu masuk, 1280 × 800, tanpa memotong atau menyembunyikan apa pun. Catat id pano, arah, dan bulan gambarnya.
5. Nilai dengan aturan ADR-0057: hanya yang terlihat jelas; toilet dan lift selalu `belum_tahu`; pintu masuk rata berarti ramp `tidak_perlu`; `tidak_tersedia` hanya bila titiknya terlihat jelas; guiding block di trotoar umum tidak dihitung, kecuali di halte.

Skrip pengambilan uji 2026-11-26 ada di `~/data/akses-bogor/street-view/2026-11-26-uji-2/raw/scripts/` di YoRHa Garden. Skrip itu belum ada di repo mana pun.

Simpan hasilnya di satu folder per putaran: `manifest.json` dan folder `screenshots/`. Pemilik produk atau verifikator memeriksa penilaian setiap data sebelum lanjut.

### Bentuk Manifest

Satu berkas JSON. Kunci yang tidak dikenal ditolak, kecuali di dalam `notes`.

```json
{
  "version": 1,
  "round": "2026-12-01",
  "generated_at": "2026-12-01T10:00:00Z",
  "records": [
    {
      "record": "02",
      "target": { "osm": "node/4994301869" },
      "verdict": "settled",
      "reason": "Pintu masuk kaca rata dengan tempat turun kendaraan, tanpa anak tangga.",
      "place": { "name": "Siloam Hospitals Bogor", "category": "rumah_sakit", "entrance": { "lat": -6.5956638, "lng": 106.8046881 } },
      "facilities": {
        "ramp": { "answer": "tidak_perlu" },
        "toilet_difabel": { "answer": "belum_tahu" },
        "guiding_block": { "answer": "belum_tahu" },
        "lift": { "answer": "belum_tahu" }
      },
      "photos": [
        { "file": "screenshots/02-wide.png", "kind": "wide", "pano_id": "UFTHywLCN4qr8GgHaw8N5A", "image_date": "2026-01", "heading": 128 },
        { "file": "screenshots/02-close.png", "kind": "close-up", "pano_id": "UFTHywLCN4qr8GgHaw8N5A", "image_date": "2026-01", "heading": 128 }
      ]
    },
    {
      "record": "05",
      "target": { "osm": "way/782982224" },
      "verdict": "unsettled",
      "reason": "Pintu masuk terhalang pagar, papan nama, dan mobil yang parkir."
    }
  ]
}
```

| Kunci | Isi |
| --- | --- |
| `version` | Selalu `1` |
| `round` | Nama putaran: huruf, angka, `.`, `_`, atau `-`, paling banyak 60 karakter. Dicatat di log audit dan dipakai `--batalkan` |
| `records[].record` | Nomor data, unik di manifest |
| `records[].target` | `{ "osm": "node/123" }` untuk kandidat OSM beserta salinan petunjuknya, atau `{ "place_id": "<uuid>" }` untuk tempat admin. Satu sasaran hanya boleh muncul sekali |
| `records[].verdict` | `settled` (terjawab) atau `unsettled` (tidak terjawab) |
| `records[].reason` | Kalimat bahasa Indonesia, 5–300 karakter. Untuk `unsettled`, alasan menyembunyikan. Tampil di log audit dan di Kelola tempat |
| `records[].place` | Hanya `settled`: `name`, `category` (`kampus`, `masjid`, `halte`, `rumah_sakit`), `entrance` (titik pintu masuk yang dinilai, menjadi pin tempat), dan `landmark` opsional |
| `records[].facilities` | Hanya `settled`: keempat fasilitas, masing-masing `answer` (`tersedia`, `tidak_tersedia`, `tidak_perlu`, `belum_tahu`), `reason` wajib untuk `tidak_tersedia` (`tidak_ada`, `rusak`, `terhalang`, `terbatas`, `tidak_sesuai`), dan `note` opsional (paling banyak 200 karakter) |
| `records[].photos` | Hanya `settled`: 1–4 gambar yang dipasang sebagai foto. `file` relatif terhadap folder manifest (PNG, JPEG, atau WebP, paling besar 3 MB), `kind` (`wide`, `close-up`, atau `other`), `image_date` (`YYYY-MM`), `pano_id`, `heading`, `tag` opsional (`pintu_masuk` bawaan, atau `guiding_block`), dan `alt` opsional untuk mengganti teks alternatif bawaan |
| `records[].notes` | Opsional, bebas, tidak dibaca alat. Mis. URL pano, jarak, catatan penilai |

Alat menolak manifest yang melanggar aturan ADR-0057, sebelum membuka transaksi:

- `settled` tanpa jawaban ramp (ramp `belum_tahu`), atau dengan toilet difabel atau lift selain `belum_tahu`;
- gambar bertanggal sebelum `2022-01`;
- `tidak_tersedia` tanpa alasan;
- guiding block `tersedia` atau `tidak_perlu` tanpa foto bertag `guiding_block`.

Bulan gambar tempat diambil dari gambar terlama yang dipasang.

## 4. Pindahkan ke Evergarden

```bash
rsync -a <folder-putaran>/ <evergarden>:street-view-<putaran>/
```

Lalu di Evergarden:

```bash
sudo install -d -m 0750 -o aksesbogor -g aksesbogor /srv/akses-bogor/data/street-view
sudo mv ~/street-view-<putaran> /srv/akses-bogor/data/street-view/<putaran>
sudo chown -R aksesbogor:aksesbogor /srv/akses-bogor/data/street-view/<putaran>
```

## 5. Dry Run (Evergarden)

```bash
api scripts/verifikasi/street-view.ts /srv/akses-bogor/data/street-view/<putaran>/manifest.json --dry-run 2>&1 | tee ~/street-view-dry-run-<putaran>.txt
```

Dry run membaca dan memproses semua gambar, lalu mencetak setiap perubahan tanpa menulis apa pun ke database atau R2. Periksa:

- [ ] Skrip tidak berhenti dengan `Manifest tidak valid:`. Bila berhenti, daftar di bawahnya menyebut masalah setiap data (mis. kandidat yang tidak ada, titik di luar Kota Bogor, atau aturan penilaian yang dilanggar), dan tidak ada yang ditulis. Perbaiki manifest dan ulangi.
- [ ] Setiap `buat tempat` dan `ubah tempat` menyebut nama, kategori, kecamatan, status, jawaban fasilitas, dan bulan gambar yang sesuai penilaian, diikuti `akan unggah foto` untuk setiap gambar.
- [ ] `lewati` hanya untuk tempat yang sudah diverifikasi lewat laporan, dan untuk yang sudah diterapkan sebelumnya tanpa perubahan.
- [ ] `sembunyikan salinan` dan `turunkan tempat` sesuai daftar data yang tidak terjawab.
- [ ] Ringkasan di akhir: jumlah tempat dibuat, diubah, diturunkan, salinan ditandai, dan salinan disembunyikan. Baris `laporan: tidak disentuh` selalu ada.

Kandidat OSM hanya disembunyikan bila pemilik produk sudah memutuskannya: tambahkan `--sembunyikan-kandidat` di langkah ini dan langkah 6. Tanpa opsi itu, kandidat tetap tampil di Peta sebagai "Belum ada data", tetapi tanpa petunjuk.

## 6. Terapkan (Evergarden)

Perintah yang sama tanpa `--dry-run`:

```bash
api scripts/verifikasi/street-view.ts /srv/akses-bogor/data/street-view/<putaran>/manifest.json 2>&1 | tee ~/street-view-terapkan-<putaran>.txt
```

Semua perubahan database ada di satu transaksi. Foto diunggah ke bucket publik R2 di dalam transaksi itu; bila transaksi gagal, foto yang sudah terunggah diantrekan untuk dihapus. Menjalankan ulang manifest yang sama tidak mengubah apa pun.

## 7. Periksa

- [ ] Jumlah di database:

  ```bash
  sudo -u postgres psql -p 5433 aksesbogor -c "SELECT verification_source, street_view_image_date, count(*) FROM places WHERE unpublished_at IS NULL GROUP BY 1, 2"
  sudo -u postgres psql -p 5433 aksesbogor -c "SELECT count(*) FILTER (WHERE tags ? 'aksesbogor:google_maps') AS google, count(*) FILTER (WHERE tags ? 'aksesbogor:osm_aksesibilitas') AS osm, count(*) FILTER (WHERE tags ? 'aksesbogor:disembunyikan') AS disembunyikan FROM osm_places"
  ```

- [ ] Buka Detail satu tempat yang terjawab: tertulis "Diverifikasi dari Street View · gambar <bulan tahun>", dan foto pintu masuk berketerangan "Sumber: Google Street View, gambar …".
- [ ] Buka kartu kandidat yang salinannya disembunyikan di Peta: bagian "Petunjuk dari sumber lain" tidak lagi menampilkan salinan Google Maps atau OSM.
- [ ] Jalankan langkah 2 lagi. Jumlah `petunjuk_google`, `petunjuk_osm`, dan `tempat_admin` turun sebanyak yang diterapkan. Jumlah `laporan` tidak berubah.

## 8. Setelah Memuat Ulang OSM atau Impor Google Maps

`pnpm pengetahuan:fasilitas` mengganti semua baris `osm_places`, jadi semua tanda dan salinan yang disembunyikan hilang. `pnpm petunjuk:google-impor` bisa menulis salinan baru di kandidat yang salinannya sudah disembunyikan. Setelah salah satunya, terapkan lagi semua manifest, dari putaran terlama:

```bash
for m in /srv/akses-bogor/data/street-view/*/manifest.json; do api scripts/verifikasi/street-view.ts "$m" --dry-run; done
```

Periksa dry run-nya seperti langkah 5, lalu jalankan tanpa `--dry-run`, dengan `--sembunyikan-kandidat` bila putaran itu memakainya. Setelah itu jalankan langkah 2: salinan baru dari impor muncul lagi sebagai `petunjuk_google` dan perlu putaran Street View sendiri.

## 9. Membatalkan

- Membatalkan satu manifest, dengan dry run dulu:

  ```bash
  api scripts/verifikasi/street-view.ts /srv/akses-bogor/data/street-view/<putaran>/manifest.json --batalkan --dry-run
  api scripts/verifikasi/street-view.ts /srv/akses-bogor/data/street-view/<putaran>/manifest.json --batalkan
  ```

  Yang dikembalikan: salinan petunjuk ke kunci asalnya, tanda sudah diperiksa dihapus, tempat admin yang diturunkan ditayangkan lagi, kandidat yang disembunyikan tampil lagi, tempat yang diubah kembali ke data sebelumnya beserta labelnya, dan foto Street View tempat itu dihapus. Tempat yang dibuat putaran itu diturunkan dengan alasan "dibatalkan" (tempat tidak pernah dihapus permanen, ADR-0051). Tempat yang sudah diubah orang lain setelah diterapkan tidak disentuh, dan dilaporkan.

  Batalkan beberapa manifest dari putaran terbaru ke terlama.

- Menghapus semua foto Street View (mis. bila Google memintanya). Batalkan dulu semua manifest, lalu:

  ```bash
  sudo -u postgres psql -p 5433 aksesbogor -c "BEGIN; INSERT INTO photo_deletions (storage_key) SELECT storage_key FROM photos WHERE source = 'street_view' ON CONFLICT DO NOTHING; DELETE FROM photos WHERE source = 'street_view'; COMMIT;"
  ```

  Pembersihan foto berkala di API menghapus berkasnya dari R2 dalam satu jam.

- Bila semuanya gagal, pulihkan cadangan langkah 1 (`pg_restore`) dengan prosedur pemulihan biasa. Foto yang sudah diunggah ke R2 sejak cadangan itu tetap ada di bucket.
