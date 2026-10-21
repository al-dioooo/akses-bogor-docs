# Referensi Figma

- File: [AksesBogor](https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor) (file key `aTaMQUxVF2P1zQlJY3s4du`)
- Diperiksa: 2026-10-21
- Halaman:
  - `Main`: 8 frame DS v1 (desktop, lebar 1440 px), lihat [Frame DS v1](#frame-ds-v1)
  - `Design System`: design system v1, lihat [Fondasi Visual](../design/fondasi-visual.md)
  - `Archive · Lovable v0`: 8 frame asli hasil impor Lovable, disimpan tanpa diubah

## Frame Asli (Archive · Lovable v0)

| Layar | Node | Tinggi | Isi singkat |
| --- | --- | --- | --- |
| Landing | [13:2](https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=13-2) | 3141 px | Hero, kategori, edukasi, statistik dampak, ajakan melapor |
| Map | [13:351](https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=13-351) | 1329 px | Peta penuh dengan marker per status dan tombol Filter |
| Explore | [13:528](https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=13-528) | 1855 px | Panel filter, pencarian, grid kartu lokasi |
| Detail | [13:1578](https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=13-1578) | 1795 px | Detail lokasi: deskripsi, fasilitas, galeri, skor, peta |
| Laporkan | [13:812](https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=13-812) | 1429 px | Formulir langkah 1: Informasi |
| Laporkan (2) | [13:1002](https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=13-1002) | 1500 px | Formulir langkah 2: Fasilitas |
| Laporkan (3) | [13:1207](https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=13-1207) | 1586 px | Formulir langkah 3: Detail dan ringkasan |
| Laporan Berhasil | [13:1412](https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=13-1412) | 1268 px | Konfirmasi laporan terkirim dan langkah selanjutnya |

## Kondisi File

- **Hasil impor prototipe Lovable.** Struktur layer berupa frame "Container" berlapis dan teks bernama "Text". Lima frame masih memuat elemen "Complementary - Edit with Lovable" (node 13:989, 13:1194, 13:1399, 13:1565, 13:1795). Frame DS v1 tidak memuatnya.
- **Design system sudah dibangun (2026-10-21).** Halaman `Design System` berisi 143 variable, 12 text style, 9 effect style, 30 ikon, 9 komponen inti, 30 komponen pola, serta bagian Foundations dan Preview ([ADR-0009](../decisions/0009-design-system-kaca-ungu-inklusi.md)).
- **Layar sudah dimigrasi (2026-10-21).** Kedelapan layar dibangun ulang sebagai frame "DS v1" di halaman `Main` (lihat [Frame DS v1](#frame-ds-v1)). Frame asli dipindah tanpa diubah ke halaman `Archive · Lovable v0`.
- **Mobile (2026-10-21).** Kedelapan layar punya versi 360 px (lihat [Frame Mobile](#frame-mobile)); layar akun dan panel verifikator belum didesain sama sekali. Belum ada frame tablet.
- **Peta di frame asli berupa tangkapan tile OpenStreetMap berpusat di Jakarta**, dengan atribusi Leaflet. Frame DS v1 memakai komponen Map base, peta skematis Kota Bogor, dengan atribusi Mapbox ([ADR-0016](../decisions/0016-peta-dan-pencarian-mapbox.md)).

## Frame DS v1

Layar hasil migrasi ke design system kaca, di bagian atas halaman `Main`, berurutan sesuai alur pengguna. Isinya hanya instance komponen, token warna, dan text style; foto memakai ulang gambar dari frame asli.

| Frame | Node |
| --- | --- |
| Landing · DS v1 | [56:2](https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=56-2) |
| Map · DS v1 | [59:182](https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=59-182) |
| Explore · DS v1 | [61:1203](https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=61-1203) |
| Detail · DS v1 | [63:541](https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=63-541) |
| Laporkan · Langkah 1 · DS v1 | [66:670](https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=66-670) |
| Laporkan · Langkah 2 · DS v1 | [66:839](https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=66-839) |
| Laporkan · Langkah 3 · DS v1 | [66:1004](https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=66-1004) |
| Laporan Berhasil · DS v1 | [68:1060](https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=68-1060) |

Perubahan dibanding frame asli:

- Label status memakai istilah Status badge (Aksesibel, Sebagian) dan label tombol memakai sentence case.
- Map: ditambah legenda status dan tombol "Lihat sebagai daftar" sebagai alternatif peta. Atribusi menjadi "© Mapbox © OpenStreetMap" dengan tempat logo Mapbox di kiri bawah ([ADR-0016](../decisions/0016-peta-dan-pencarian-mapbox.md)). Tangkapan peta Jakarta diganti Map base, dan marker dipindah ke kecamatan lokasi contoh dengan status yang sama seperti kartu Explore.
- Kategori di langkah 1 Laporkan memakai Radio tile dengan indikator radio agar jelas hanya satu pilihan.
- Tombol mengambang "Laporkan Tempat" di Landing dan Explore dihapus karena menutupi footer dan sudah ada di nav bar.
- Teks kartu "Apa selanjutnya?" di Laporan Berhasil tidak lagi meluber keluar kartu.
- Landing memakai "Lift / elevator", bukan "Lift & eskalator" ([ADR-0005](../decisions/0005-empat-fasilitas-aksesibilitas.md)).
- Kota Bogor ([ADR-0010](../decisions/0010-cakupan-wilayah-kota-bogor.md)): copy Landing dan Explore menyebut Kota Bogor, filter di Explore memakai kecamatan, dan kontak footer memakai kota Bogor.
- Explore menulis "Menampilkan 4 lokasi", sesuai jumlah kartu.
- Detail: daftar fasilitas memakai empat jawaban ([ADR-0017](../decisions/0017-empat-jawaban-ketersediaan-fasilitas.md)). Kartu "Accessibility Score" menjadi "Ringkasan fasilitas" berisi "2 dari 3", status Sebagian, dan kalimat alasannya ([ADR-0018](../decisions/0018-status-pintu-masuk-dulu-dan-ringkasan-fasilitas.md)).
- Laporkan langkah 1: ditambah pemilih lokasi ([ADR-0015](../decisions/0015-input-lokasi-peta-gps-pencarian.md)) berisi pencarian, tombol "Gunakan lokasi saya", peta dengan pin tetap di tengah, tombol arah dan zoom, kecamatan hasil hitung, tombol "Pakai lokasi ini", dan patokan opsional.
- Laporkan langkah 2: setiap fasilitas dijawab dengan Facility answer (empat pilihan), bukan checkbox. Toilet Difabel menunjukkan alasan yang dipilih, dan Lift menunjukkan kolom catatan yang terbuka ([ADR-0022](../decisions/0022-tersedia-berarti-bisa-dipakai.md)). Ringkasan di langkah 3 menampilkan jawaban dan alasannya.
- Laporkan langkah 3: bagian foto berisi hitungan "3 dari 10 foto", daftar foto yang membantu verifikasi berdasarkan jawaban langkah 2, tiga Photo item bertag, dan dropzone ([ADR-0023](../decisions/0023-hingga-sepuluh-foto-bertag-per-laporan.md)).
- Detail: fasilitas ditampilkan dalam satu kolom dengan baris alasan atau catatan, dan kalimat status menyebut alasannya: "Sebagian: Guiding Block tidak tersedia (tidak sesuai)" ([ADR-0022](../decisions/0022-tersedia-berarti-bisa-dipakai.md)).
- Bahasa ([ADR-0020](../decisions/0020-antarmuka-bahasa-indonesia-siap-i18n.md)): nav bar menjadi Beranda, Jelajahi, Peta, Aksesibilitas, Difabel, Tips, Tentang; footer, judul Explore ("Jelajahi Lokasi"), dan tombol "Jelajahi lokasi" berbahasa Indonesia. Teks Laporan Berhasil tidak lagi menyebut "database".

Belum digambar: keadaan lain pemilih lokasi (daftar hasil pencarian, akurasi GPS, titik di luar Kota Bogor, peringatan laporan ganda), keadaan unggah foto (sedang diunggah, gagal), dan galeri Detail yang dikelompokkan per tag. Frame mobile yang belum ada dicatat di [Frame Mobile](#frame-mobile).

## Frame Mobile

Versi 360 px ([ADR-0012](../decisions/0012-web-mobile-first-pwa.md)), di halaman `Main` tepat di bawah frame desktop pasangannya (y = 3400). Isinya instance komponen, token, dan text style yang sama dengan frame DS v1.

| Frame | Node | Tinggi |
| --- | --- | --- |
| Landing · Mobile | [99:1734](https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=99-1734) | 3477 px |
| Jelajahi · Mobile | [100:1734](https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=100-1734) | 2533 px |
| Peta · Mobile | [101:1933](https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=101-1933) | 800 px, satu layar |
| Detail · Mobile | [102:2085](https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=102-2085) | 2861 px |
| Laporkan · Langkah 1 · Mobile | [105:2277](https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=105-2277) | 1334 px |
| Laporkan · Langkah 2 · Mobile | [105:2946](https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=105-2946) | 1660 px |
| Laporkan · Langkah 3 · Mobile | [105:3134](https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=105-3134) | 1592 px |
| Laporan Berhasil · Mobile | [105:3261](https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=105-3261) | 1777 px |

Pola yang dipakai:

- App bar di atas (logo dan "Masuk", atau tombol Kembali) dan Bottom nav di bawah: Beranda, Jelajahi, Peta, Laporkan, Lainnya. Menu desktop lain (Aksesibilitas, Difabel, Tips, Tentang) pindah ke "Lainnya". Detail tidak memakai App bar; tombol Kembali mengambang di atas foto.
- Tombol utama selebar layar dengan tinggi 48 px; target sentuh lain minimal 44 px ([ADR-0021](../decisions/0021-wcag-22-aa-dan-pengujian-berlapis.md)). Frame panjang diberi ruang bawah setinggi Bottom nav supaya konten dan fokus tidak tertutup.
- Jelajahi: tombol Filter (kecamatan dan fasilitas) dan chip kategori yang bisa digulir ke samping; hasil berupa Location card selebar layar.
- Peta: peta penuh dengan pencarian mengambang, tombol Filter dan Daftar, kolom kontrol (legenda, zoom, lokasi saya), dan Location row untuk lokasi yang dipilih.
- Detail: urutan nama dan status, ringkasan fasilitas, daftar fasilitas, tombol Edit dan Laporkan kekurangan, lalu deskripsi, galeri yang digulir ke samping, dan peta kecil.
- Laporkan: App bar dengan judul "Laporkan tempat" dan tombol Kembali untuk keluar, stepper, dan isian selebar layar. Selama mengisi, Bottom nav diganti Form action bar yang menempel di bawah: "Lanjut" atau "Kirim laporan", dengan "Kembali" mulai langkah 2. Tombol "Gunakan lokasi saya" dan "Pakai lokasi ini" memakai ukuran 48 px.
- Laporan Berhasil: Bottom nav kembali dengan tab Laporkan aktif, dan kedua tombol aksi ditumpuk.

Belum digambar untuk mobile: lembar Filter, legenda yang terbuka, menu Lainnya, keadaan pencarian, keadaan lain pemilih lokasi dan unggah foto, serta layar akun dan panel verifikator.

## Data Placeholder

Data berikut di frame DS v1 adalah isian contoh dan tidak boleh dianggap sebagai keputusan:

- Kontak footer: `info@aksesbogor.id`, `+62 251 000 0000`, "Kota Bogor, Jawa Barat". Nomor telepon fiktif.
- Statistik dampak: 125+ tempat dilaporkan, 80% memiliki ramp, 50+ kontributor aktif.
- Jumlah lokasi per kategori: Kampus 25, Masjid 42, Halte 38, Rumah Sakit 20.
- Lokasi contoh: Kampus contoh A (Tanah Sareal), Halte contoh (Bogor Tengah), Rumah sakit contoh (Bogor Barat), Kampus contoh B (Bogor Utara), dan Masjid contoh (Bogor Tengah) di ringkasan laporan. Nama sengaja fiktif agar tidak ada status aksesibilitas yang melekat pada tempat nyata; kecamatannya nyata.
- Foto lokasi dan foto di langkah 3 memakai ulang foto frame asli; nama berkas foto di langkah 3 fiktif. Foto header dan galeri halte berupa bus antarkota, bukan halte.
- Peta di Map, Detail, dan pemilih lokasi berupa gambar skematis. Letak jalan, sungai, dan kecamatan hanya perkiraan. Marker kelima di Bogor Timur tidak punya kartu di Explore.

Frame arsip masih memuat data asli prototipe: Universitas Indonesia (Depok), Halte TransJakarta Bundaran HI, "RSUP Dr. Cipto…", Institut Pertanian Bogor (tertulis "Bandung"), "Menampilkan 8 lokasi", dan kontak Jakarta.

## Masalah Visual yang Ditemukan

- Laporan Berhasil: teks di kartu "Apa selanjutnya?" melebihi batas kartu.
- Landing dan Explore: tombol mengambang "Laporkan Tempat" menutupi link "Terms of Service" di footer.
- Explore: label status "Partial" berwarna kuning di atas foto hampir tidak terbaca.

## Cara Merujuk

Di ADR dan dokumen lain, rujuk frame dengan nama dan node, mis. "Detail (Figma 13:1578)", dan tautkan ke `?node-id=13-1578`.
