# ADR-0054: Agen AI Satu Pintu untuk Semua Pengunjung, dengan Alat Kontributor bagi Akun yang Sudah Masuk

- Status: Accepted
- Tanggal: 2026-11-26
- Pengambil keputusan: pemilik produk
- Issue: #123
- Men-supersede sebagian: [ADR-0043](0043-agen-untuk-kontributor-dengan-model-gratis-openrouter.md) (halaman agen `/laporkan/bantu-memetakan` dan namanya) dan [ADR-0050](0050-asisten-rute-publik.md) (halaman `/tanya-rute`, panel titik awal, dan namanya). Aturan model, alat, kejujuran, batas, dan privasi di kedua ADR itu tetap berlaku kecuali disebut di bawah.

## Konteks dan Masalah

Situs punya dua fitur AI dengan nama dan halaman sendiri: "Bantu memetakan" untuk akun yang sudah masuk ([ADR-0043](0043-agen-untuk-kontributor-dengan-model-gratis-openrouter.md)) dan "Tanya rute" untuk semua pengunjung ([ADR-0050](0050-asisten-rute-publik.md)). Keduanya memakai formulir tanya-jawab di dalam panel, bukan obrolan. QA tim menilai fitur AI seharusnya terasa seperti agen obrolan yang sudah dikenal orang, dan pemilik produk ingin menyebutnya "Agen AI" saja, karena semua orang sudah tahu AI. Bagaimana menggabungkan keduanya menjadi satu tempat tanpa mengubah aturan privasi dan tanpa merusak web lama selama rilis?

## Decision Drivers

- Satu nama dan satu halaman lebih mudah ditemukan daripada dua fitur dengan nama yang harus dijelaskan.
- Orang sudah tahu cara memakai agen obrolan: pesan di kanan dan kiri, kotak tulis di bawah, saran pertanyaan, tombol berhenti.
- Model tetap tidak pernah menerima koordinat, garis rute, atau hasil Mapbox ([ADR-0042](0042-agen-rute-jalan-kaki-dan-informasi-aksesibilitas.md)), dan server tidak menyimpan percakapan.
- API harus tetap melayani web lama sampai web baru dirilis.
- Jatah dan biaya tidak bertambah.
- WCAG 2.2 AA ([ADR-0021](0021-wcag-22-aa-dan-pengujian-berlapis.md)) dan gaya situs yang sama ([ADR-0027](0027-design-system-datar-dan-ilustrasi-3d.md)).

## Opsi yang Dipertimbangkan

1. Satu halaman `/agen` dan satu endpoint asisten, dengan alat kontributor yang diminta web untuk akun yang sudah lengkap
2. Satu halaman yang memilih `POST /agent/ask` atau `POST /route-assistant/ask` menurut status masuk
3. Tetap dua halaman, hanya nama dan tampilannya yang diubah

## Keputusan

Opsi terpilih: "Satu halaman `/agen` dan satu endpoint asisten", karena akun yang sudah masuk mendapat kemampuan Tanya rute dan Bantu memetakan dalam satu percakapan, dan perubahan API-nya kecil serta tetap cocok dengan web lama.

### Nama dan pintu masuk

- Nama fitur di semua teks: "Agen AI". Halamannya `/agen`, terbuka untuk semua pengunjung.
- `/tanya-rute` dan `/laporkan/bantu-memetakan` dialihkan permanen (308) ke `/agen`, dengan query tetap, jadi `/tanya-rute?tempat=<id>` membuka Agen AI dengan tempat itu terpilih.
- Agen AI ada di menu utama desktop, menu Lainnya di ponsel, footer, dan sitemap. Beranda, Laporkan, Detail lokasi ("Tanya rute ke sini"), Ringkasan akun, dan menu Kontribusi di dasbor (selama agen kontributor dibuka) menautkan ke `/agen`.

### Siapa dan apa

| Penanya | Profil | Alat |
| --- | --- | --- |
| Pengunjung tanpa akun, atau akun yang belum dilengkapi | Asisten rute publik ([ADR-0050](0050-asisten-rute-publik.md)) | `find_place`, `place_accessibility`, `search_knowledge`, `nearby_places`, `show_route` |
| Akun yang sudah lengkap, selama `ROUTE_AGENT_ENABLED` menyala | Asisten rute publik ditambah alat kontributor | Alat di atas, ditambah `find_places_to_contribute`, `kecamatan_priority` (selama `DISABILITY_DATA_ENABLED`), dan `show_place_card` |

- Profil gabungan memakai instruksi asisten rute publik, ditambah aturan kontributor dari [ADR-0043](0043-agen-untuk-kontributor-dengan-model-gratis-openrouter.md): mencari tempat yang perlu dipetakan, keterangan bahwa angka disabilitas adalah jumlah terdaftar di Dinas Sosial, tanpa kata "cacat", dan ajakan Laporkan.
- Semua alat di profil gabungan memakai id tempat yang sama: id lokasi AksesBogor, atau `osm:<tipe>/<id>` untuk tempat OpenStreetMap. Tempat dari `find_places_to_contribute` bisa langsung menjadi tujuan `show_route` dan `show_place_card`.
- Untuk akun yang sudah masuk, pertanyaan rute sekarang mengikuti aturan kejujuran [ADR-0050](0050-asisten-rute-publik.md) (kartu rute dengan peringatan, tanpa klaim rute aksesibel), bukan jawaban tetap "Maaf, data itu belum tersedia di AksesBogor." dari [ADR-0043](0043-agen-untuk-kontributor-dengan-model-gratis-openrouter.md).

### API

- `POST /route-assistant/ask` menerima `contributorTools` (boolean, opsional). Web mengirimnya selama ada sesi. API memakai profil gabungan hanya bila nilainya benar, penanya akun yang sudah lengkap, dan `ROUTE_AGENT_ENABLED` menyala; selain itu profil publik.
- Tanpa `contributorTools`, jawaban tetap hanya memuat kartu rute, jadi halaman Tanya rute di web lama tidak menerima kartu yang tidak dikenalnya.
- Jatah tidak berubah: 5 pertanyaan per IP atau per akun per hari WIB (akun satu hitungan dengan `POST /agent/ask`), dan `ROUTE_ASSISTANT_QUESTIONS_PER_DAY` untuk semua penanya. Pertanyaan kontributor kini memakai model dan batas harian asisten publik, bukan `AGENT_REQUESTS_PER_DAY`.
- `POST /agent/ask` dan route handler web lama tetap ada sampai web baru dirilis. Menghapusnya pekerjaan terpisah setelah rilis.
- Nama path `/route-assistant/*` tidak diganti: mengganti path memutus web lama, dan nama path tidak terlihat pengunjung.

### Obrolan di web

- Tinggi penuh di bawah header, tanpa footer. Pesan pengguna di kanan, jawaban agen di kiri dengan sumber dan kartu di bawahnya. Kotak tulis menempel di bawah: beberapa baris, Enter mengirim, Shift+Enter membuat baris baru, dan sisa karakter tampil saat mendekati 500.
- Keadaan kosong: pengantar singkat, pemberitahuan privasi dengan tautan Kebijakan Privasi, dan 3–4 saran pertanyaan yang langsung terkirim saat dipilih. Akun dengan alat kontributor mendapat saran kontributor.
- Saat menunggu tampil indikator mengetik. API belum mengalirkan jawaban (streaming), jadi jawaban tampil utuh.
- "Berhenti" membatalkan permintaan web. API bisa tetap menyelesaikan pertanyaan itu, jadi jatahnya tetap terpakai.
- Galat tampil di percakapan dengan "Coba lagi", yang mengirim ulang pertanyaan yang sama. Jawaban bisa disalin. "Percakapan baru" mengosongkan percakapan. Tombol "Ke pesan terbaru" muncul saat pengguna menggulir ke atas.
- Titik awal dan tujuan tampil sebagai chip di atas kotak tulis. Titik awal dipilih lewat dialog yang memakai komponen titik awal kartu rute: penjelasan dulu, lalu lokasi perangkat atau titik di peta.
- Percakapan disimpan di `sessionStorage` tab itu saja, supaya tidak hilang saat halaman dimuat ulang atau pengguna membuka tautan kartu lalu kembali. Percakapan hilang saat tab ditutup atau "Percakapan baru" dipilih. Titik awal tidak pernah disimpan: kartu rute yang dipulihkan meminta titik awal lagi.
- Aksesibilitas: percakapan berupa daftar berlabel; live region terpisah hanya membacakan jawaban terbaru dan status menunggu, tanpa membacakan ulang seluruh percakapan; fokus tetap di kotak tulis setelah mengirim; semua tombol bisa dipakai dengan keyboard; di ponsel kotak tulis tetap di atas keyboard dan safe area, dan halaman utuh di lebar 320 px.

### Kebijakan Privasi

Teks kebijakan mengganti "Bantu memetakan" dan "Tanya rute" dengan "Agen AI" dan menyebut penyimpanan percakapan di tab browser. Versinya naik ke 2026-11-26. Urutan rilis sama dengan versi sebelumnya: web dulu, lalu `PRIVACY_POLICY_VERSION` di API beberapa menit kemudian.

## Konsekuensi

- Positif: satu nama dan satu halaman; akun yang sudah masuk bisa bertanya rute dan mencari tempat yang perlu dipetakan dalam percakapan yang sama.
- Positif: perubahan API kecil dan cocok dengan web lama; urutan rilis API fitur dan web bebas.
- Positif: percakapan tidak hilang saat pengguna membuka Detail lokasi atau Laporkan dari kartu lalu kembali.
- Negatif: siapa pun yang memakai tab yang sama bisa membaca percakapan sampai tab ditutup. Kebijakan Privasi dan tombol "Percakapan baru" menjelaskannya.
- Negatif: pertanyaan kontributor kini ikut batas harian asisten publik, jadi asisten yang ramai bisa membuat agen kontributor sibuk juga.
- Negatif: "Berhenti" tidak mengembalikan jatah.
- Negatif: dua endpoint pertanyaan ada bersamaan sampai `POST /agent/ask` dihapus.

## Pro & Kontra tiap Opsi

### Satu halaman `/agen` dan satu endpoint asisten

- Pro: satu percakapan memakai semua alat yang boleh dipakai penanyanya.
- Pro: alat, putaran alat, pemeriksaan jawaban, dan kartu yang sudah teruji dipakai ulang.
- Kontra: satu instruksi sistem tambahan yang harus dirawat.

### Satu halaman yang memilih endpoint menurut status masuk

- Pro: tanpa perubahan API.
- Kontra: akun yang sudah masuk kehilangan `nearby_places` dan kartu rute ke tempat terdekat, dan agen kontributor menolak pertanyaan rute aksesibel. Akun justru mendapat agen yang lebih sempit daripada pengunjung.

### Tetap dua halaman

- Pro: paling sedikit pekerjaan.
- Kontra: dua nama, dua halaman, dan dua cara bertanya tetap membingungkan, dan masukan QA tidak terjawab.

## Referensi

- Issue #123, akses-bogor-api#143, akses-bogor-web#219
- [ADR-0021](0021-wcag-22-aa-dan-pengujian-berlapis.md), [ADR-0027](0027-design-system-datar-dan-ilustrasi-3d.md), [ADR-0042](0042-agen-rute-jalan-kaki-dan-informasi-aksesibilitas.md), [ADR-0043](0043-agen-untuk-kontributor-dengan-model-gratis-openrouter.md), [ADR-0047](0047-model-agen-kontributor-claude-haiku-lewat-claude-api.md), [ADR-0050](0050-asisten-rute-publik.md)
