# ADR-0055: Rute Pejalan Kaki Aksesibel dengan Valhalla

- Status: Accepted
- Tanggal: 2026-11-26
- Pengambil keputusan: pemilik produk
- Issue: #130
- Melengkapi: [ADR-0042](0042-agen-rute-jalan-kaki-dan-informasi-aksesibilitas.md) (rute jalan kaki Mapbox dan hambatan OSM), [ADR-0050](0050-asisten-rute-publik.md) (rute untuk pengunjung tanpa akun)

## Konteks dan Masalah

QA tim meminta rute lewat trotoar untuk pejalan kaki penyandang disabilitas. Kartu rute sekarang memakai Mapbox Directions profil `walking` ([ADR-0042](0042-agen-rute-jalan-kaki-dan-informasi-aksesibilitas.md)). Profil itu tidak tahu tangga, trotoar, kerb, permukaan, atau kemiringan, jadi kartu hanya bisa berkata "Belum tentu bisa dilewati kursi roda" dan menampilkan hambatan OSM di dekat garis rute. Bagaimana AksesBogor memberi rute yang menghindari tangga, dengan keterangan jujur tentang seberapa lengkap data di sepanjang rute itu?

## Decision Drivers

- Pengguna kursi roda perlu rute yang tidak lewat tangga dan lebih memilih trotoar daripada badan jalan.
- Jangan memberi rasa aman palsu. Data OSM Kota Bogor tipis: pada ekstrak 2026-11-25 hanya 3.304 dari 62.894 way jalan yang punya data trotoar atau berupa jalur pejalan kaki, 71 jalur penyeberangan, dan 4 yang kerb-nya tercatat.
- Posisi pengguna adalah data pribadi (UU No. 27 Tahun 2022). Tidak boleh ada pihak ketiga baru yang menerimanya, dan perlakuannya sama dengan rute Mapbox: dibulatkan, tidak disimpan, tidak dikirim ke model.
- Biaya nol dan server produksi kecil: sekitar 3,8 GB RAM dengan CPU lama, berbagi dengan API, PostgreSQL, dan web.
- Fitur ini harus bisa dimatikan tanpa mengubah rute Mapbox yang sekarang.

## Opsi yang Dipertimbangkan

1. Valhalla di server sendiri dengan costing `pedestrian` (`type: wheelchair` dan `foot`) dan data OSM Kota Bogor
2. Tetap Mapbox `walking` ditambah daftar hambatan OSM (keadaan sekarang)
3. OpenRouteService, profil `wheelchair`, lewat API publik atau di server sendiri
4. GraphHopper atau OSRM di server sendiri dengan profil kursi roda buatan sendiri

## Keputusan

Opsi terpilih: "Valhalla di server sendiri", karena Valhalla sudah punya profil kursi roda yang menolak tangga, berjalan ringan untuk satu kota, dan menjaga posisi pengguna tetap di server kami. Pilihan router dibuat pemilik produk; rincian di bawah adalah cara menjalankannya.

### Router dan profil

- Image resmi `ghcr.io/valhalla/valhalla:3.9.3`, ditambah `osmium-tool` untuk memotong ekstrak. Valhalla hanya mendengar di `127.0.0.1` dan dipanggil API di host yang sama.
- `kursi_roda`: costing `pedestrian` dengan `type: wheelchair`, `sidewalk_factor` dan `walkway_factor` 0,8 (trotoar dan jalur pejalan kaki lebih disukai), `use_tracks: 0`, `step_penalty` tinggi, `max_grade: 8`, dan `use_hills: 0`. Dengan data Bogor, profil ini tidak pernah memakai satu pun dari 152 way `highway=steps`: rute memutar (mis. 50 m lewat tangga menjadi 374 m lewat jalan) atau Valhalla menjawab tidak ada jalur.
- `jalan_kaki`: `type: foot` dengan faktor trotoar 0,9.
- Yang tidak dihindari Valhalla menjadi peringatan, bukan janji: kerb tinggi (Valhalla tidak membaca tag kerb di node), permukaan buruk, dan tanjakan yang dicatat di tag `incline`. Ubin dibangun tanpa data ketinggian, jadi `max_grade` dan `use_hills` belum berpengaruh dan setiap rute kursi roda membawa peringatan bahwa kemiringan belum dihitung.

### Data

- Ekstrak Geofabrik `asia/indonesia/java-latest.osm.pbf` (ODbL), dipotong ke kotak Kota Bogor ditambah sekitar 3 km di setiap sisi (`106.69,-6.72,106.89,-6.47`) dengan strategi `simple` karena memorinya lebih kecil (1,9 GB, bukan 3,3 GB untuk `complete_ways`). Way di tepi kotak terpotong; tepi 3 km cukup untuk rute di dalam kota.
- Dibangun ulang setiap Minggu pukul 03.30 WIB oleh timer systemd. Ekstrak hanya diunduh bila lebih baru dan dicek MD5-nya. Versi baru baru dipakai setelah dua rute contoh berhasil, tautan `aktif` dipindah secara atomik, dan dua versi terakhir disimpan untuk kembali bila perlu. Seminggu sekali cukup untuk data trotoar yang jarang berubah; sebulan sekali membuat perbaikan data dari kontributor OSM terlalu lama muncul.
- Valhalla tidak membedakan tag yang kosong dari tag bernilai "tidak", dan jenis permukaannya selalu terisi dari tebakan. Karena itu pembangunan ubin juga membuat `jalur.json`: ringkasan tag mentah per way dari ekstrak yang sama (trotoar tercatat, permukaan tercatat, penyeberangan, kerb tercatat, tanpa trotoar, permukaan buruk, curam lebih dari 8%, kerb tinggi, tangga).

### API

- Route baru `POST /route/accessible` dengan `profil` = `kursi_roda` | `jalan_kaki`, untuk pemanggil yang sama dengan `POST /route/walking` ([ADR-0042](0042-agen-rute-jalan-kaki-dan-informasi-aksesibilitas.md), [ADR-0050](0050-asisten-rute-publik.md)) dan batas 30 rute per akun atau per IP per jam.
- Jawaban: tujuan, profil, jarak, waktu, garis rute, langkah dalam bahasa Indonesia (Valhalla 3.9.3 tidak punya narasi bahasa Indonesia, jadi kalimatnya dibuat API dari jenis manuver), cakupan data, peringatan, label tetap "Dihitung dari data OpenStreetMap; kondisi lapangan bisa berbeda.", dan atribusi OSM.
- Cakupan data: persen panjang rute di jalur pejalan kaki atau jalan yang trotoarnya tercatat, persen yang jenis permukaannya tercatat, dan berapa penyeberangan yang kerb-nya tercatat. Id way didapat dari `trace_attributes` Valhalla pada garis rute yang sama. Bila berkas atau pencocokan tidak ada, cakupan dilaporkan tidak diketahui.
- `ACCESSIBLE_ROUTER_URL` kosong berarti fitur mati: route ini menjawab 503 `rute_aksesibel_belum_aktif` dan `POST /route/walking` tetap seperti sekarang. API tidak pernah mengganti rute OSM yang gagal dengan rute Mapbox. Bila profil kursi roda tidak menemukan jalur, API menjawab 422 `tanpa_rute_kursi_roda`.
- Privasi sama dengan rute Mapbox: posisi dibulatkan ke 4 desimal (sekitar 11 m), hanya dikirim ke Valhalla di `127.0.0.1`, tidak disimpan, dan tidak dikirim ke model. Log Valhalla hanya berisi nomor permintaan dan kode status, tanpa koordinat.

### Web

- Kartu rute meminta rute OSM dulu dengan profil Kursi roda. Pengguna bisa beralih ke Jalan kaki; rute dihitung ulang dari titik awal yang sama.
- Kartu menampilkan jarak, waktu, garis rute di peta kartu, label tetap, kelengkapan data, peringatan dari API, langkah, hambatan OSM di dekat rute, dan atribusi OSM. Kartu tidak pernah menyebut rute dijamin bisa dilewati.
- Bila API menjawab fitur ini belum aktif, kartu memakai rute Mapbox seperti sekarang, dengan peringatannya dan tanpa pilihan profil.
- Bila tidak ada rute kursi roda, kartu menampilkan pesan API dan tombol untuk melihat rute jalan kaki.
- Halaman Peta belum menampilkan rute ini. Bila nanti diinginkan, perubahannya ada di berkas Peta dan diurutkan setelah pekerjaan peta tematik.

### Kebijakan Privasi

Teks tentang titik awal kartu rute berubah: titik awal dipakai server kami untuk menghitung rute, dengan data OpenStreetMap di server sendiri atau, bila rute itu belum tersedia, lewat Mapbox. Mapbox tidak menerima titik awal untuk rute OSM. Perubahan ini masuk ke versi 2026-11-26 yang belum dirilis (Agen AI, [ADR-0054](0054-agen-ai-satu-pintu.md)), supaya pengguna hanya diminta menyetujui ulang sekali. Bila versi 2026-11-26 sudah dirilis lebih dulu, perubahan ini menjadi versi 2026-11-27. Seperti biasa, web dirilis dulu, lalu `PRIVACY_POLICY_VERSION` di API.

## Konsekuensi

- Positif: pengguna kursi roda mendapat rute yang menghindari tangga yang tercatat, dengan keterangan jujur tentang data yang belum ada. Tidak ada pihak ketiga baru yang menerima posisi, dan rute kursi roda tidak lagi memakai kuota Mapbox.
- Positif: ringan saat melayani. Diukur 2026-11-26 di host dev: Valhalla memakai 145 MB setelah 200 rute, median 7 ms dan p95 13 ms per rute termasuk `trace_attributes`. Ubin 20 MB.
- Negatif: rute hanya sebaik data OSM. Sebagian besar rute di Bogor akan menampilkan data trotoar dan kerb yang belum lengkap, kerb tinggi tidak dihindari, dan kemiringan belum dihitung.
- Negatif: server produksi butuh Docker, 2 GB disk (image dan ekstrak), dan sekitar 1,9 GB RAM bebas selama kurang dari satu menit setiap minggu saat memotong ekstrak. Bila memori tidak cukup, kontainer pembangun dihentikan dan versi aktif tetap dipakai.
- Negatif: ada router kedua yang perlu dirawat (versi Valhalla, kalimat langkah buatan sendiri, timer).

## Pro & Kontra tiap Opsi

### Valhalla di server sendiri

- Pro: profil kursi roda bawaan yang menolak tangga dan jalur bertag `wheelchair=no`, faktor untuk trotoar dan jalur pejalan kaki, dan `trace_attributes` untuk id way.
- Pro: posisi tetap di server kami; tidak ada kuota atau biaya per rute.
- Kontra: tidak ada narasi bahasa Indonesia, tidak membaca kerb di node, dan kemiringan butuh data ketinggian tambahan.

### Tetap Mapbox walking ditambah hambatan OSM

- Pro: tidak ada pekerjaan dan server tambahan.
- Kontra: rute bisa lewat tangga; peringatan dan daftar hambatan tidak mengubah rutenya.

### OpenRouteService

- Pro: profil `wheelchair` dengan batas kemiringan, permukaan, dan tinggi kerb.
- Kontra: API publik berarti posisi pengguna dikirim ke pihak ketiga dengan kuota harian. Di server sendiri, ORS berbasis Java dan butuh memori jauh lebih besar dari yang tersedia.

### GraphHopper atau OSRM dengan profil sendiri

- Pro: cepat, dan profil bisa diatur penuh.
- Kontra: profil kursi roda harus ditulis dan diuji sendiri. GraphHopper berbasis Java dan lebih berat; OSRM perlu dibangun ulang untuk setiap perubahan profil.

## Pertanyaan Terbuka

- Data ketinggian (mis. SRTM 30 m untuk satu ubin 1° di sekitar Bogor) supaya `max_grade` dan `use_hills` berlaku. Butuh unduhan data tambahan dan atribusinya, dan resolusi 30 m kasar untuk trotoar.
- Apakah rute OSM juga ditampilkan di halaman Peta.
- Apakah server produksi punya memori bebas yang cukup setiap Minggu dini hari, atau ubin dibangun di mesin lain lalu disalin.

## Referensi

- Runbook pemasangan dan angka pengukuran: [operations/valhalla-rute-aksesibel.md](../operations/valhalla-rute-aksesibel.md)
- Dokumentasi costing Valhalla: <https://valhalla.github.io/valhalla/api/turn-by-turn/api-reference/#pedestrian-costing-options>
- Ekstrak Geofabrik Jawa: <https://download.geofabrik.de/asia/indonesia/java.html>
- Hak cipta dan lisensi OpenStreetMap: <https://www.openstreetmap.org/copyright>
- akses-bogor-api issue #151, akses-bogor-web issue #224
