# Referensi Figma

- File: [AksesBogor](https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor) (file key `aTaMQUxVF2P1zQlJY3s4du`)
- Diperiksa: 2026-10-21
- Halaman: `Main` (8 frame desktop, lebar 1440 px) dan `Design System` (kosong)

## Frame di Halaman Main

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

- **Hasil impor prototipe Lovable.** Struktur layer berupa frame "Container" berlapis dan teks bernama "Text". Lima frame masih memuat elemen "Complementary - Edit with Lovable" (node 13:989, 13:1194, 13:1399, 13:1565, 13:1795) yang harus dihapus sebelum desain dipakai sebagai acuan final.
- **Belum ada komponen, style, atau variable.** Halaman `Design System` kosong. Acuan sementara dicatat di [Fondasi Visual](../design/fondasi-visual.md).
- **Hanya desktop.** Belum ada frame mobile atau tablet.
- **Peta berupa tangkapan Leaflet + OpenStreetMap** (atribusi "Leaflet | © OpenStreetMap contributors"), berpusat di Jakarta.

## Data Placeholder

Data berikut adalah isian contoh dan tidak boleh dianggap sebagai keputusan:

- Kontak footer: `info@aksesbogor.id`, `+62 21 1234 5678`, "Jakarta, Indonesia".
- Statistik dampak: 125+ tempat dilaporkan, 80% memiliki ramp, 50+ kontributor aktif.
- Jumlah lokasi per kategori: Kampus 25, Masjid 42, Halte 38, Rumah Sakit 20.
- Lokasi contoh: Universitas Indonesia (Depok), Halte TransJakarta Bundaran HI (Jakarta), "RSUP Dr. Cipto…" (nama terpotong di desain, Jakarta), Institut Pertanian Bogor (tertulis "Bandung", seharusnya Bogor).
- "Menampilkan 8 lokasi" di Explore, padahal hanya 4 kartu yang tampil.
- Foto galeri halte berupa foto bus antarkota, bukan foto halte.

## Masalah Visual yang Ditemukan

- Laporan Berhasil: teks di kartu "Apa selanjutnya?" melebihi batas kartu.
- Landing dan Explore: tombol mengambang "Laporkan Tempat" menutupi link "Terms of Service" di footer.
- Explore: label status "Partial" berwarna kuning di atas foto hampir tidak terbaca.

## Cara Merujuk

Di ADR dan dokumen lain, rujuk frame dengan nama dan node, mis. "Detail (Figma 13:1578)", dan tautkan ke `?node-id=13-1578`.
