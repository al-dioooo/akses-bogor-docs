# Fondasi Visual

Ringkasan design system AksesBogor versi 1: gaya kaca (glassmorphism) dengan palet **Ungu Inklusi**, diputuskan di [ADR-0009](../decisions/0009-design-system-kaca-ungu-inklusi.md). Sumber kebenarannya adalah variable dan style di [halaman Design System Figma](https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=1-2); dokumen ini merangkumnya untuk pembaca yang tidak membuka Figma.

## Prinsip

- Permukaan kaca buram di atas latar aurora, dan semua teks tetap lulus WCAG AA di kasus terburuk.
- Status tidak pernah dibedakan dengan warna saja: selalu warna, ikon, dan label.
- Setiap permukaan kaca punya kembaran opak (mode Solid).
- Efek harus bisa dibuat di CSS: `backdrop-filter: blur()` dan `box-shadow`.

## Mode Warna

| Mode | Kapan dipakai | Permukaan kaca |
| --- | --- | --- |
| Glass | Default | Putih 72% + blur 24 px |
| Solid | `prefers-reduced-transparency`, kontras tinggi, atau ponsel yang berat menjalankan blur | Putih opak |
| Dark | `prefers-color-scheme: dark` | Tinta gelap 56% + blur, di latar malam ungu |

## Palet dan Maknanya

### Ungu Inklusi (warna utama)

Ungu dipakai gerakan hak penyandang disabilitas (#PurpleLightUp setiap 3 Desember, Hari Disabilitas Internasional). Maknanya martabat dan inklusi, sekaligus membedakan AksesBogor dari biru layanan pemerintah.

| Tingkat | Nilai | Pemakaian |
| --- | --- | --- |
| 50 | `#F4F1FF` | Latar halaman (mode Glass) |
| 100 | `#EAE4FF` | Tint terpilih (mode Solid) |
| 200 | `#D5C9FF` | Gumpalan aurora "Inklusi" |
| 300 | `#B8A4FF` | Teks brand dan cincin fokus di mode Dark |
| 500 | `#7B5CFF` | Glow dan ilustrasi saja |
| 600 | `#6440E8` | Isi tombol Primary (teks putih 6.1:1) |
| 700 | `#5230C2` | Teks dan link brand (6.2:1 di kaca), hover tombol |
| 800 | `#40259A` | Tombol ditekan |

Tingkat 400, 900, dan 950 tersedia sebagai primitive untuk kebutuhan ilustrasi.

### Status aksesibilitas

| Status | Arti | Teks (fg) | Latar (bg) | Solid (marker) | Ikon |
| --- | --- | --- | --- | --- | --- |
| Aksesibel | Bisa dipakai mandiri (OSM `wheelchair=yes`) | `#0E6B41` | `#DFF8EE` | `#13804F` | Lingkaran centang |
| Sebagian | Bisa dipakai dengan hambatan atau bantuan (`wheelchair=limited`) | `#7F4A00` | `#FFF7DE` | `#FFCC33` | Lingkaran setengah |
| Tidak aksesibel | Ada hambatan utama (`wheelchair=no`) | `#A11D24` | `#FDE9E9` | `#C0262D` | Lingkaran silang |
| Belum diketahui | Belum diverifikasi; bukan berarti tidak aksesibel | `#52607A` | `#EEF1F6` | `#52607A` | Lingkaran tanya |

Di mode Dark, teks status memakai tingkat 300 dan latarnya tint 16%.

### Tinta (netral)

| Token | Nilai | Pemakaian |
| --- | --- | --- |
| ink-900 | `#1E2029` | Teks utama |
| ink-600 | `#4B5066` | Teks sekunder dan placeholder |
| ink-500 | `#6A6E81` | Border field (non-teks, 3.8:1); tidak untuk teks |
| ink-200 | `#DEE1EC` | Pembatas |
| ink-50 | `#F5F6FB` | Teks utama di mode Dark, latar mode Solid |

### Latar aurora

| Gumpalan | Nilai | Makna |
| --- | --- | --- |
| Inklusi | `#D5C9FF` | Nilai inti produk |
| Hujan | `#C9E2FF` | Bogor, Kota Hujan |
| Kebun Raya | `#BDF3DC` | Hijau Kebun Raya Bogor |

Di mode Solid latar menjadi polos.

## Token

Tiga koleksi variable:

| Koleksi | Mode | Isi |
| --- | --- | --- |
| `Primitives` | Value | 66 warna mentah (disembunyikan dari picker) |
| `Color` | Glass, Solid, Dark | 55 token semantik: `color/bg/*`, `color/surface/*`, `color/border/*`, `color/text/*`, `color/icon/*`, `color/brand/*`, `color/status/*`, `color/shadow/*`, `color/focus/*`, `color/control/*`, `color/marker/*` |
| `Dimension` | Value | 22 token: `spacing/2xs`–`4xl` (4–96), `radius/sm`–`full` (8–999), `blur/glass` (24) dan `blur/glass-strong` (32), `border-width/*`, `size/*` |

Setiap variable punya scope dan nama CSS. Nama CSS diturunkan dari nama variable: `color/text/primary` menjadi `var(--color-text-primary)`.

## Tipografi

| Style | Font | Ukuran / tinggi baris |
| --- | --- | --- |
| Display/XL | Plus Jakarta Sans Bold, −2.5% | 60/66 |
| Heading/H1 | Plus Jakarta Sans Bold, −2.5% | 36/44 |
| Heading/H2 | Plus Jakarta Sans Bold, −2% | 30/36 |
| Heading/H3 | Plus Jakarta Sans SemiBold, −2.5% | 20/28 |
| Heading/H4 | Plus Jakarta Sans SemiBold, −2.5% | 18/28 |
| Body/Lead | Inter Regular | 20/30 |
| Body/Base | Inter Regular | 16/24 |
| Body/Small | Inter Regular | 14/20 |
| Label/Base | Inter Semi Bold | 16/24 |
| Label/Small | Inter Semi Bold | 14/20 |
| Label/Nav | Inter Medium | 14/20 |
| Caption | Inter Medium | 12/16 |

Teks isi minimal 14 px; 12 px hanya untuk caption.

## Efek

| Style | Isi | Padanan CSS |
| --- | --- | --- |
| Glass/Regular | Blur latar 24 px, bayangan 0 8 32 ungu 12%, highlight atas 1 px | `backdrop-filter: blur(24px)` |
| Glass/Strong | Blur latar 32 px, bayangan 0 12 40 | `backdrop-filter: blur(32px)` |
| Glow/Brand | Bayangan ungu 48% di bawah tombol Primary | `box-shadow: 0 8px 24px -4px` |
| Focus/Ring | Celah 2 px + cincin 2 px ungu | `box-shadow: 0 0 0 2px <gap>, 0 0 0 4px <ring>` |
| Elevation/Sm, Md, Lg | Bayangan netral | `box-shadow` |
| Marker/Drop | Bayangan pin peta | `filter: drop-shadow()` |

Warna bayangan dan radius blur terikat ke variable, sehingga ikut berubah per mode.

## Komponen v1

| Komponen | Varian | Properti utama |
| --- | --- | --- |
| Button | Style (Primary, Glass, Ghost) × Size (Medium 48 px, Small 36 px) × State (Default, Hover, Focus, Disabled) = 24 | Label, ikon depan/belakang (bisa diganti) |
| Toggle | Checked × State = 6 | Label |
| Checkbox | Checked × State = 6 | Label, deskripsi |
| Input | State (Default, Focus, Filled, Error, Disabled) = 5 | Label, teks, ikon depan, helper, pesan error |
| Status badge | Status (4) × Size (2) = 8 | Label tetap per status |
| Map marker | Status (4) × State (Default, Selected) = 8 | — |
| Glass card | Surface (Regular, Strong) = 2 | Judul, lokasi, isi, badge, aksi |
| Nav item | State (Default, Hover, Active, Focus) = 4 | Label |
| Nav bar | 1 | — |

Ditambah 30 ikon outline 24 px. Halaman Preview di Figma menyusun komponen ini menjadi satu layar contoh di ketiga mode.

### Komponen pola

Dibuat saat migrasi delapan layar, di bagian Patterns halaman Design System:

| Kelompok | Komponen |
| --- | --- |
| Umum | Logo (Default, Inverse), Tag (Brand, Glass), Check item, Footer |
| Beranda | Category tile (Default, Hover, Focus), Stat item |
| Peta | Map base (Kota Bogor skematis), Zoom control, Pan control, Map legend, Picker pin |
| Explore | Chip (terpilih atau tidak), Select, Location card (Default, Hover, Focus) |
| Detail | Facility item (Tersedia, Tidak tersedia, Tidak perlu, Belum diketahui; baris alasan dan catatan opsional), Score ring (empat nada status, isi "X dari Y") |
| Laporkan | Stepper step, Stepper (langkah 1–3), Radio tile, Facility answer (Answer: Tersedia, Tidak tersedia, Tidak perlu, Belum tahu × Note: Closed, Open), Checkbox card, Textarea, Upload dropzone, Photo item |
| Mobile | App bar (Home, Back), Bottom nav item (5 tab × Default, Active), Bottom nav, Form action bar, Footer mobile, Location row |

Komponen yang mengikuti keputusan produk:

- **Facility answer**: satu fasilitas dengan empat pilihan radio, bawaan "Belum tahu" ([ADR-0017](../decisions/0017-empat-jawaban-ketersediaan-fasilitas.md)). Di Detail, jawaban yang sama tampil lewat Facility item, dengan "Belum tahu" ditulis "Belum diketahui". Varian Tidak tersedia menampilkan pilihan alasan (Tidak ada, Rusak, Terhalang, Terbatas, Tidak sesuai), dan Note=Open menampilkan kolom catatan; Note=Closed menampilkan tombol "Tambah catatan" ([ADR-0022](../decisions/0022-tersedia-berarti-bisa-dipakai.md)).
- **Facility item**: properti Show detail menampilkan baris kedua berisi alasan dan catatan, misalnya "Tidak sesuai · Jalur terputus sebelum pintu masuk" ([ADR-0022](../decisions/0022-tersedia-berarti-bisa-dipakai.md)). Karena bisa dua baris, sudutnya memakai `radius/lg`, bukan bentuk pil.
- **Score ring**: angka X (fasilitas relevan yang Tersedia), teks "dari Y", dan nama status; bukan skor 0–100 ([ADR-0018](../decisions/0018-status-pintu-masuk-dulu-dan-ringkasan-fasilitas.md)). Busur hanya penguat visual dan diatur per instance.
- **Picker pin** dan **Pan control**: pin tetap di tengah peta pemilih lokasi, dan tombol arah 48 px untuk pengguna yang tidak bisa menyeret peta ([ADR-0015](../decisions/0015-input-lokasi-peta-gps-pencarian.md)). Ikon Locate dipakai di tombol "Gunakan lokasi saya".
- **Map base**: peta skematis Kota Bogor (batas kota, enam kecamatan, Kebun Raya, Ciliwung, Cisadane, jalan utama, rel) yang digambar dari token warna, dipakai di Map dan peta kecil Detail. Untuk peta kecil, instance diperbesar dan layer label disembunyikan. Komponen ini hanya pengganti visual; di produk peta dirender Mapbox ([ADR-0016](../decisions/0016-peta-dan-pencarian-mapbox.md)).
- **Upload dropzone** dan **Photo item**: dropzone menerima hingga 10 foto JPEG, PNG, atau WebP. Setiap foto yang dipilih tampil sebagai Photo item berisi pratinjau, nama dan ukuran berkas, Select untuk tag, dan tombol hapus 44 px, dalam dua baris sehingga Select selebar kartu di layar sempit ([ADR-0023](../decisions/0023-hingga-sepuluh-foto-bertag-per-laporan.md)).

### Navigasi mobile

Di lebar 360 px, nav bar desktop (tujuh menu) diganti komponen berikut:

- **App bar**: menempel di atas. Type=Home berisi logo dan tombol Masuk; Type=Back berisi tombol Kembali 44 px dan judul satu baris.
- **Bottom nav**: menempel di bawah layar dengan lima tab, yaitu Beranda, Jelajahi, Peta, Laporkan, dan Lainnya. Menu lain dari nav desktop pindah ke Lainnya. Tab aktif ditandai indikator, warna brand, dan `aria-current="page"`, bukan warna saja. Halaman diberi ruang bawah setinggi nav supaya elemen yang difokus tidak tertutup (WCAG 2.4.11).
- **Form action bar**: menggantikan Bottom nav selama mengisi formulir bertahap. Tombol utama ("Lanjut" atau "Kirim laporan") selebar sisa bar, dan "Kembali" muncul mulai langkah kedua; tombol Kembali di App bar keluar dari formulir.

Komponen bersama menyesuaikan lebar 328 px: garis penghubung Stepper meregang, nilai Input dan Select dipotong satu baris, dan label, teks bantuan, serta petunjuk dropzone membungkus.

Ikon Home, Map, Menu, dan User ditambahkan untuk pola ini. Footer mobile menumpuk kolom footer desktop dengan tautan setinggi 44 px, dan Location row adalah kartu ringkas satu lokasi untuk Peta.

Footer dan panel ajakan memakai `color/surface/inverse` (tinta gelap opak), bukan kaca gelap, karena kaca 72% di atas latar terang hanya memberi kontras sekitar 4.4:1 untuk teks sekunder.

## Kontras

Kasus terburuk: kaca 72% di atas gumpalan latar paling pekat.

| Kombinasi | Rasio | Mode |
| --- | --- | --- |
| Teks utama ink-900 | 12.2:1 | Glass |
| Teks sekunder ink-600 | 6.0:1 | Glass |
| Teks brand violet-700 | 6.2:1 | Glass |
| Putih di tombol violet-600 | 6.1:1 | Semua mode |
| Teks status tingkat 800 | 4.9–5.8:1 | Glass |
| Border field ink-500 (non-teks) | 3.8:1 | Glass |
| Teks utama ink-50 | 11.4:1 | Dark |
| Teks sekunder ink-300 | 7.4:1 | Dark |
| Teks brand violet-300 | 5.8:1 | Dark |

## Riwayat: Palet Desain Awal

Delapan layar desain awal memakai biru `#387AFF`, teks sekunder `#6A6E81`, dan warna status `#39D597`, `#FFCC33`, `#F37777`. Audit 2026-10-21 menemukan:

- Semua warna status gagal AA sebagai teks (1.5–2.7:1).
- Teks putih di tombol `#387AFF` hanya 3.9:1.
- `#6A6E81` turun ke 3.8:1 di atas kaca.

Palet itu digantikan design system di atas. Layar-layar tersebut belum dimigrasi ke token dan komponen baru.
