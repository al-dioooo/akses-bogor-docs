# Ringkasan Produk

Disusun dari [desain Figma](referensi-figma.md) per 2026-10-21. Bagian yang merupakan tafsiran penulis, bukan isi desain, ditandai *(asumsi)*.

## Tujuan

Berdasarkan copy di desain, AksesBogor bertujuan:

- Menyediakan informasi aksesibilitas fasilitas publik agar difabel bisa menilai sebuah tempat sebelum berkunjung.
- Mengajak warga melaporkan fasilitas publik di sekitarnya ("Bantu Kami Memetakan Aksesibilitas di Kota Bogor").
- Memberi edukasi dasar tentang aksesibilitas ("Apa itu Aksesibilitas?").

## Pengguna

| Peran | Kebutuhan | Layar terkait |
| --- | --- | --- |
| Difabel dan pendamping | Mencari lokasi yang aksesibel, melihat fasilitas dan skornya | Explore, Map, Detail |
| Kontributor (warga) | Melaporkan tempat baru dan kekurangan di tempat yang sudah ada | Laporkan, tombol "Laporkan Kekurangan" di Detail |
| Tim verifikator | Memverifikasi laporan sebelum ditayangkan | Belum ada layar; disebut di Laporan Berhasil |

## Fitur per Layar

### Beranda (Landing)

- Hero "Akses untuk Semua" dengan tombol "Explore Locations" dan "Pelajari Aksesibilitas".
- Jelajahi berdasarkan kategori: Kampus, Masjid, Halte, Rumah Sakit, masing-masing dengan jumlah lokasi.
- Edukasi "Apa itu Aksesibilitas?" berisi daftar fasilitas dan tombol "Pelajari Lebih Lanjut".
- Statistik "Dampak AksesBogor": tempat dilaporkan, persentase yang memiliki ramp, kontributor aktif.
- Ajakan melapor: "Laporkan Tempat Sekarang".

### Peta (Map)

- Peta penuh dengan marker per lokasi; warna marker mengikuti status (hijau dan oranye).
- Tombol "Filter". Isi panel filter belum didesain.

### Explore

- Filter: Kategori, Kota (dropdown "Semua Kota"), Fasilitas (checkbox Ramp, Toilet Difabel, Guiding Block, Lift).
- Pencarian "Cari lokasi...".
- Kartu lokasi: foto, badge status, badge kategori, nama, kota, dan link "Lihat Detail".

### Detail Lokasi

- Header foto dengan nama, kota, kategori, dan status.
- Deskripsi, daftar fasilitas (tersedia ditandai ✓, tidak tersedia ditandai ✗), dan galeri foto.
- "Accessibility Score" 0–100 dengan label (contoh: 65, "Baik").
- Tombol "Edit Lokasi" dan "Laporkan Kekurangan".
- Kotak "Lokasi di Peta" dengan link "Lihat di Peta".

### Laporkan Tempat

Formulir tiga langkah:

1. **Informasi**: Nama Tempat (wajib), Kategori (wajib, pilih satu).
2. **Fasilitas**: pilih fasilitas yang tersedia (boleh lebih dari satu).
3. **Detail**: Upload Foto (PNG/JPG, maksimal 5 MB), Deskripsi Tambahan, dan Ringkasan Laporan, lalu "Kirim Laporan".

### Laporan Berhasil

- Konfirmasi bahwa laporan terkirim.
- Langkah selanjutnya: tim memverifikasi, lokasi muncul di peta setelah diverifikasi, pelapor mendapat notifikasi saat lokasi dipublikasikan.
- Tombol "Kembali ke Explore" dan "Laporkan Tempat Lain".

## Navigasi

- **Header**: Home, Explore, Map, Accessibility, Difabel, Tips, About, dan tombol "Laporkan Tempat".
- **Footer**: Quick Links (Explore Locations, Map View, Laporkan Tempat, Accessibility 101), Resources (Tipe Difabel, Tips Membangun, About Us), Kontak, Privacy Policy, Terms of Service.

## Model Data Awal *(asumsi)*

Diturunkan dari elemen UI; belum menjadi keputusan.

### Lokasi

| Field | Contoh di desain | Catatan |
| --- | --- | --- |
| Nama | Halte TransJakarta Bundaran HI | |
| Kategori | Halte | Satu dari empat kategori |
| Kota | Jakarta | Dipakai filter Kota di Explore |
| Koordinat | Marker di peta | Belum ada input di formulir laporan |
| Status | Partial | Nilai yang terlihat: Accessible, Partial |
| Skor | 65 / 100, "Baik" | Rumus belum ditentukan |
| Deskripsi | "Halte TransJakarta di Bundaran HI memiliki lift dan ramp, …" | Teks bebas |
| Fasilitas | Ramp ✓, Toilet Difabel ✗, Guiding Block ✓, Lift ✓ | Tersedia / tidak tersedia |
| Foto | Galeri 2 foto | |

### Laporan

| Field | Sumber di formulir |
| --- | --- |
| Nama tempat | Langkah 1 |
| Kategori | Langkah 1 |
| Fasilitas tersedia | Langkah 2 |
| Foto | Langkah 3 |
| Deskripsi tambahan | Langkah 3 |
| Status verifikasi | Tersirat dari layar Laporan Berhasil |

## Belum Ada di Desain

- Login atau akun pengguna (padahal Laporan Berhasil menjanjikan notifikasi).
- Panel admin atau verifikator.
- Tampilan mobile dan tablet.
- Halaman Accessibility, Difabel, Tips, About, Privacy Policy, dan Terms of Service.
- Panel Filter di Map, serta alur "Edit Lokasi" dan "Laporkan Kekurangan".
