# Runbook Valhalla untuk Rute Pejalan Kaki Aksesibel

| | |
| --- | --- |
| Status | Draft, belum dipasang di server mana pun |
| Tanggal | 2026-11-26 |
| Berlaku untuk | Server yang menjalankan akses-bogor-api, dengan Valhalla di host yang sama ([ADR-0055](../decisions/0055-rute-pejalan-kaki-aksesibel-dengan-valhalla.md)) |
| Issue | #130 |

Valhalla menghitung rute pejalan kaki dari data OpenStreetMap Kota Bogor untuk `POST /route/accessible`. Berkasnya ada di `deploy/valhalla/` di akses-bogor-api. Kerjakan dari atas ke bawah. Perintah `sudo` dijalankan admin server. Nilai dalam `<…>` diisi sendiri.

## 1. Angka yang Diukur

Diukur 2026-11-26 di host dev (AMD EPYC 9J14, 16 vCPU, 16 GB RAM) dengan ekstrak Geofabrik Jawa tanggal 2026-11-25. Server produksi lebih lambat; anggap waktunya beberapa kali lipat, memorinya sama.

| Langkah | Waktu | Memori puncak |
| --- | --- | --- |
| Unduh `java-latest.osm.pbf` (900 MB) | 2,5 menit | kecil |
| Potong ke Kota Bogor, `osmium extract --strategy simple`, dibatasi 2 CPU | 18 detik | 1,9 GB |
| Potong dengan `--strategy complete_ways` (tidak dipakai) | 49 sampai 55 detik | 3,2 sampai 3,4 GB; berhenti karena kehabisan memori dengan batas 1,2 GB |
| `valhalla_build_tiles`, 2 thread | 6 detik | 175 MB |
| Seluruh `ubin.sh --lokal` dengan 16 thread | 16 detik | 1,88 GB (kontainer, batas 2,4 GB) |
| Valhalla melayani, setelah 200 rute acak di Kota Bogor | median 7 ms, p95 13 ms per rute plus `trace_attributes` | 145 MB (95 MB saat baru jalan) |

| Berkas | Ukuran |
| --- | --- |
| Image `akses-bogor-valhalla:3.9.3` | 967 MB |
| Ekstrak Jawa (disimpan untuk unduhan berikutnya) | 900 MB |
| `ubin.tar` per versi | 20 MB |
| `jalur.json` per versi | 88 KB (62.894 way jalan, 6.099 dengan tag aksesibilitas) |

Memori bebas sekitar 2 GB hanya dibutuhkan saat memotong ekstrak, kurang dari satu menit setiap Minggu dini hari. Kontainer pembangun dibatasi 2,4 GB; bila batas itu terlampaui, hanya pembangunan yang berhenti dan versi aktif tetap dipakai.

## 2. Sebelum Mulai

- [ ] API sudah memuat `POST /route/accessible` (PR akses-bogor-api untuk issue #151 sudah di `release`).
- [ ] Ruang disk bebas minimal 3 GB: `df -h /srv /var/lib/docker`.
- [ ] Port 8002 di `127.0.0.1` belum dipakai: `ss -tlnp | grep ':8002 '`. Bila terpakai, pilih port lain dan pakai di langkah 4 dan 6.
- [ ] Docker Engine terpasang: `docker version`. Bila belum, admin memasangnya dari repo Ubuntu:

```bash
sudo apt-get update && sudo apt-get install -y docker.io
sudo systemctl enable --now docker
```

## 3. User, Folder, dan Image

```bash
sudo useradd --system --home-dir /srv/valhalla --shell /usr/sbin/nologin --groups docker valhalla
sudo install -d -o valhalla -g valhalla -m 0755 /srv/valhalla
# Dari checkout akses-bogor-api yang berisi deploy/valhalla, mis. rilis aktif.
cd /srv/akses-bogor/current
sudo docker build -t akses-bogor-valhalla:3.9.3 deploy/valhalla
sudo install -d /usr/local/lib/akses-bogor/valhalla
sudo install -m 0755 deploy/valhalla/ubin.sh /usr/local/lib/akses-bogor/valhalla/
sudo install -m 0644 deploy/valhalla/akses-bogor-valhalla.service deploy/valhalla/akses-bogor-valhalla-ubin.service deploy/valhalla/akses-bogor-valhalla-ubin.timer /etc/systemd/system/
sudo systemctl daemon-reload
```

Anggota grup `docker` setara root. User `valhalla` tidak punya shell dan hanya dipakai unit pembangun.

## 4. Port Lain (bila perlu)

Bila port 8002 terpakai:

```bash
sudo systemctl edit akses-bogor-valhalla
# Isi:
# [Service]
# Environment=VALHALLA_PORT=<port>
```

## 5. Pembangunan Pertama

```bash
sudo systemctl start akses-bogor-valhalla-ubin
journalctl -u akses-bogor-valhalla-ubin -n 30 --no-pager
```

Yang diharapkan di log: `rute contoh foot 1.46 km`, `rute contoh wheelchair 1.46 km`, dan `versi <waktu> selesai dalam … detik, ubin 20M`. Unit ini juga menyalakan `akses-bogor-valhalla.service` sesudahnya.

Bila gagal karena memori (`exit 137` atau `Killed`), versi aktif tidak berubah. Pilihannya: jalankan lagi saat server lebih sepi, atau bangun di mesin lain dengan Docker (lihat bagian 9).

## 6. Nyalakan Layanan dan Timer

```bash
sudo systemctl enable --now akses-bogor-valhalla akses-bogor-valhalla-ubin.timer
systemctl list-timers akses-bogor-valhalla-ubin.timer
curl -s http://127.0.0.1:8002/status
curl -s http://127.0.0.1:8002/route -H 'content-type: application/json' \
  -d '{"locations":[{"lat":-6.5954,"lon":106.7906},{"lat":-6.5946,"lon":106.7963}],"costing":"pedestrian","costing_options":{"pedestrian":{"type":"wheelchair"}},"directions_type":"none"}'
```

`/status` menjawab `"version":"3.9.3"`, dan rute contoh menjawab `trip` dengan panjang sekitar 1,46 km.

## 7. Sambungkan API

Tambahkan ke `/etc/akses-bogor/api.env`:

```bash
ACCESSIBLE_ROUTER_URL=http://127.0.0.1:8002
ACCESSIBLE_ROUTER_WAYS_FILE=/srv/valhalla/aktif/jalur.json
```

Lalu `sudo systemctl restart akses-bogor-api` dan coba kartu rute di web: pilihan profil Kursi roda dan Jalan kaki muncul, dengan label "Dihitung dari data OpenStreetMap; kondisi lapangan bisa berbeda". `ACCESSIBLE_ROUTER_WAYS_FILE` harus bisa dibaca user `aksesbogor`; berkasnya dibuat dengan mode 0644.

Urutan rilis bersama Kebijakan Privasi: web dengan teks kebijakan baru dulu, lalu `PRIVACY_POLICY_VERSION` di API beberapa menit kemudian, lalu kedua env di atas. Web tanpa rute OSM tetap memakai rute Mapbox, jadi web boleh dirilis lebih dulu.

## 8. Pemantauan dan Pemulihan

| Keadaan | Tindakan |
| --- | --- |
| Melihat versi aktif | `readlink /srv/valhalla/aktif` dan `ls /srv/valhalla/versi` |
| Kembali ke versi sebelumnya | `sudo -u valhalla ln -sfn versi/<versi lama> /srv/valhalla/aktif.baru && sudo -u valhalla mv -T /srv/valhalla/aktif.baru /srv/valhalla/aktif && sudo systemctl restart akses-bogor-valhalla` |
| Mematikan fitur tanpa menyentuh Valhalla | Kosongkan `ACCESSIBLE_ROUTER_URL` di `/etc/akses-bogor/api.env`, restart API. Web kembali ke rute Mapbox. |
| Valhalla mati | API menjawab 502 `rute_gagal` dan kartu menampilkan pesan coba lagi; tidak ada cadangan ke Mapbox. `systemctl status akses-bogor-valhalla`, `docker logs akses-bogor-valhalla` |
| Geofabrik tidak bisa dihubungi | Pembangunan gagal di langkah unduh; versi aktif tetap. Timer mencoba lagi minggu depan, atau jalankan unit dengan tangan. |
| Memperbarui Valhalla | Ganti tag di `Dockerfile` dan `VALHALLA_IMAGE` di kedua unit, build image, jalankan unit pembangun, lalu periksa rute contoh. |

Log Valhalla hanya berisi nomor permintaan dan kode status, tanpa koordinat, dan dibatasi 2 × 5 MB oleh Docker. Log pembangun ada di journald dan mengikuti masa simpan log server (30 hari).

## 9. Membangun di Mesin Lain

Bila server tidak punya memori bebas yang cukup, ubin bisa dibangun di mesin mana pun dengan Docker, lalu disalin:

```bash
docker build -t akses-bogor-valhalla:3.9.3 deploy/valhalla
VALHALLA_DATA_DIR=$HOME/valhalla deploy/valhalla/ubin.sh
rsync -a $HOME/valhalla/versi/<versi>/ <server>:/tmp/<versi>/
# Di server:
sudo install -d -o valhalla -g valhalla /srv/valhalla/versi/<versi>
sudo rsync -a --chown=valhalla:valhalla /tmp/<versi>/ /srv/valhalla/versi/<versi>/
```

Lalu pindahkan tautan `aktif` seperti di bagian 8. `valhalla.json` di setiap versi sudah menunjuk `/data/aktif/ubin.tar`, jadi tidak perlu diubah. Bila cara ini dipakai, matikan timer di server: `sudo systemctl disable --now akses-bogor-valhalla-ubin.timer`.
