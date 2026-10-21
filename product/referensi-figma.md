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
- **Design system sudah dibangun (2026-10-21).** Halaman `Design System` berisi 143 variable, 12 text style, 9 effect style, 26 ikon, 9 komponen inti, 22 komponen pola, serta bagian Foundations dan Preview ([ADR-0009](../decisions/0009-design-system-kaca-ungu-inklusi.md)).
- **Layar sudah dimigrasi (2026-10-21).** Kedelapan layar dibangun ulang sebagai frame "DS v1" di halaman `Main` (lihat [Frame DS v1](#frame-ds-v1)). Frame asli dipindah tanpa diubah ke halaman `Archive · Lovable v0`.
- **Hanya desktop.** Belum ada frame mobile atau tablet.
- **Peta berupa tangkapan tile OpenStreetMap berpusat di Jakarta.** Frame asli memakai atribusi Leaflet. Map · DS v1 dan peta kecil di Detail · DS v1 masih memakai tangkapan yang sama, walau atribusinya sudah diganti ke Mapbox ([ADR-0016](../decisions/0016-peta-dan-pencarian-mapbox.md)).

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
- Map: ditambah legenda status dan tombol "Lihat sebagai daftar" sebagai alternatif peta. Atribusi menjadi "© Mapbox © OpenStreetMap" dengan tempat logo Mapbox di kiri bawah ([ADR-0016](../decisions/0016-peta-dan-pencarian-mapbox.md)).
- Kategori di langkah 1 Laporkan memakai Radio tile dengan indikator radio agar jelas hanya satu pilihan.
- Tombol mengambang "Laporkan Tempat" di Landing dan Explore dihapus karena menutupi footer dan sudah ada di nav bar.
- Teks kartu "Apa selanjutnya?" di Laporan Berhasil tidak lagi meluber keluar kartu.
- Landing memakai "Lift / elevator", bukan "Lift & eskalator" ([ADR-0005](../decisions/0005-empat-fasilitas-aksesibilitas.md)).
- Kota Bogor ([ADR-0010](../decisions/0010-cakupan-wilayah-kota-bogor.md)): copy Landing dan Explore menyebut Kota Bogor, filter di Explore memakai kecamatan, dan kontak footer memakai kota Bogor.
- Explore menulis "Menampilkan 4 lokasi", sesuai jumlah kartu.
- Detail: daftar fasilitas memakai empat jawaban ([ADR-0017](../decisions/0017-empat-jawaban-ketersediaan-fasilitas.md)). Kartu "Accessibility Score" menjadi "Ringkasan fasilitas" berisi "2 dari 3", status Sebagian, dan kalimat alasannya ([ADR-0018](../decisions/0018-status-pintu-masuk-dulu-dan-ringkasan-fasilitas.md)).
- Laporkan langkah 1: ditambah pemilih lokasi ([ADR-0015](../decisions/0015-input-lokasi-peta-gps-pencarian.md)) berisi pencarian, tombol "Gunakan lokasi saya", peta dengan pin tetap di tengah, tombol arah dan zoom, kecamatan hasil hitung, tombol "Pakai lokasi ini", dan patokan opsional.
- Laporkan langkah 2: setiap fasilitas dijawab dengan Facility answer (empat pilihan), bukan checkbox. Ringkasan di langkah 3 menampilkan jawaban tersebut.

Belum digambar: keadaan lain pemilih lokasi (daftar hasil pencarian, akurasi GPS, titik di luar Kota Bogor, peringatan laporan ganda) dan frame mobile.

## Data Placeholder

Data berikut di frame DS v1 adalah isian contoh dan tidak boleh dianggap sebagai keputusan:

- Kontak footer: `info@aksesbogor.id`, `+62 251 000 0000`, "Kota Bogor, Jawa Barat". Nomor telepon fiktif.
- Statistik dampak: 125+ tempat dilaporkan, 80% memiliki ramp, 50+ kontributor aktif.
- Jumlah lokasi per kategori: Kampus 25, Masjid 42, Halte 38, Rumah Sakit 20.
- Lokasi contoh: Kampus contoh A (Tanah Sareal), Halte contoh (Bogor Tengah), Rumah sakit contoh (Bogor Barat), Kampus contoh B (Bogor Utara), dan Masjid contoh (Bogor Tengah) di ringkasan laporan. Nama sengaja fiktif agar tidak ada status aksesibilitas yang melekat pada tempat nyata; kecamatannya nyata.
- Foto lokasi memakai ulang foto frame asli. Foto header dan galeri halte berupa bus antarkota, bukan halte.
- Peta di Map dan Detail berupa tangkapan Jakarta (lihat [Kondisi File](#kondisi-file)). Peta di pemilih lokasi berupa gambar skematis.

Frame arsip masih memuat data asli prototipe: Universitas Indonesia (Depok), Halte TransJakarta Bundaran HI, "RSUP Dr. Cipto…", Institut Pertanian Bogor (tertulis "Bandung"), "Menampilkan 8 lokasi", dan kontak Jakarta.

## Masalah Visual yang Ditemukan

- Laporan Berhasil: teks di kartu "Apa selanjutnya?" melebihi batas kartu.
- Landing dan Explore: tombol mengambang "Laporkan Tempat" menutupi link "Terms of Service" di footer.
- Explore: label status "Partial" berwarna kuning di atas foto hampir tidak terbaca.

## Cara Merujuk

Di ADR dan dokumen lain, rujuk frame dengan nama dan node, mis. "Detail (Figma 13:1578)", dan tautkan ke `?node-id=13-1578`.
