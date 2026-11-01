# Fondasi Visual

Ringkasan design system AksesBogor versi 2: permukaan datar, tipografi besar, palet **Ungu Inklusi**, dan ilustrasi lingkungan 3D, diputuskan di [ADR-0027](../decisions/0027-design-system-datar-dan-ilustrasi-3d.md). Versi 1 (kaca dan aurora, [ADR-0009](../decisions/0009-design-system-kaca-ungu-inklusi.md)) dicatat di [Riwayat](#riwayat). Sumber nilai saat ini adalah `design-tokens/figma-variables.json` di akses-bogor-web; variable di [halaman Design System Figma](https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=1-2) masih versi 1 dan akan diperbarui menyusul.

## Prinsip

- Permukaan opak. Kontras teks tidak boleh bergantung pada gambar atau warna yang bergeser di baliknya.
- Status tidak pernah dibedakan dengan warna saja: selalu warna, ikon, dan label.
- Satu warna aksi (ungu). Warna lain punya tugas: status, atau sorotan di pita gelap.
- Ilustrasi menjelaskan fasilitas, tetapi informasinya selalu tertulis juga di teks.
- Semua gerak bisa dihentikan dan tidak pernah menyembunyikan konten.

## Mode Warna

| Mode | Kapan dipakai |
| --- | --- |
| Terang | Bawaan |
| Gelap | `prefers-color-scheme: dark` |

Pengaturan tampilan di header bisa memaksa salah satu mode lewat `<html data-theme="terang|gelap">`.

## Palet dan Maknanya

### Ungu Inklusi (warna utama)

Ungu dipakai gerakan hak penyandang disabilitas (#PurpleLightUp setiap 3 Desember, Hari Disabilitas Internasional). Maknanya martabat dan inklusi, sekaligus membedakan AksesBogor dari biru layanan pemerintah.

| Tingkat | Nilai | Pemakaian |
| --- | --- | --- |
| 100 | `#EAE4FF` | Tint terpilih, kartu sorotan |
| 300 | `#B8A4FF` | Teks brand dan cincin fokus di mode Gelap |
| 600 | `#6440E8` | Isi tombol utama (teks putih 6.1:1), pin ilustrasi |
| 700 | `#5230C2` | Teks dan link brand, hover tombol |
| 800 | `#40259A` | Tombol ditekan |

### Status aksesibilitas

| Status | Arti | Teks (fg) | Latar (bg) | Solid (marker) | Ikon |
| --- | --- | --- | --- | --- | --- |
| Aksesibel | Bisa dipakai mandiri (OSM `wheelchair=yes`) | `#0E6B41` | `#DFF8EE` | `#13804F` | Lingkaran centang |
| Sebagian | Bisa dipakai dengan hambatan atau bantuan (`wheelchair=limited`) | `#7F4A00` | `#FFF7DE` | `#FFCC33` | Lingkaran setengah |
| Tidak aksesibel | Ada hambatan utama (`wheelchair=no`) | `#A11D24` | `#FDE9E9` | `#C0262D` | Lingkaran silang |
| Belum diketahui | Belum diverifikasi; bukan berarti tidak aksesibel | `#52607A` | `#EEF1F6` | `#52607A` | Lingkaran tanya |

Di mode Gelap, teks status memakai tingkat 300 dan latarnya tint 16%.

### Kertas, tinta, dan aspal (netral)

| Token | Nilai | Pemakaian |
| --- | --- | --- |
| paper-50 | `#F9F9F7` | Latar halaman |
| paper-100 | `#F0F0ED` | Latar cekung: panel filter, hover |
| paper-200 | `#E6E5DF` | Garis pembatas kartu |
| ink-950 | `#12131A` | Teks utama, garis tegas, tombol hitam; latar mode Gelap |
| ink-600 | `#4B5066` | Teks sekunder dan placeholder |
| ink-500 | `#6A6E81` | Border field (non-teks); tidak untuk teks |
| asphalt-700 | `#3C3F52` | Pita jalan di bawah hero, tempat angka |

### Kuning guiding block

`#F2C230`, warna ubin pemandu di Indonesia. Dipakai di ilustrasi dan untuk satu angka sorotan di pita jalan (6.2:1 di atas aspal). Tidak dipakai untuk status, supaya tidak tertukar dengan Sebagian.

## Token

| Koleksi | Mode | Isi |
| --- | --- | --- |
| `Primitives` | Value | Warna mentah: violet, ink, paper, asphalt, kuning, status, dan alpha |
| `Color` | Terang, Gelap | Token semantik: `color/bg/*`, `color/surface/*`, `color/border/*`, `color/text/*`, `color/icon/*`, `color/brand/*`, `color/danger/*`, `color/status/*`, `color/shadow/*`, `color/focus/*`, `color/control/*`, `color/marker/*` |
| `Dimension` | Value | `spacing/2xs`–`4xl` (4–96), `radius/sm`–`3xl` (8–32) dan `full`, `border-width/*`, `size/*` |

Nama CSS diturunkan dari nama variable: `color/text/primary` menjadi `var(--color-text-primary)`.

## Tipografi

| Peran | Font | Ukuran |
| --- | --- | --- |
| Display (judul hero) | Bricolage Grotesque ExtraBold, −3.5% | 45–83 px, tinggi baris 0.95 |
| Judul bagian | Bricolage Grotesque ExtraBold, −3.5% | 37–61 px, tinggi baris 1 |
| Judul kartu | Bricolage Grotesque Bold, −2% | 20–28 px |
| Aksen | Instrument Serif Italic | Satu kata di akhir judul, warna brand |
| Lead | Inter Regular | 19/30 |
| Isi | Inter Regular | 16/26 |
| Kecil | Inter Regular | 14/20 |
| Label | IBM Plex Mono Medium, kapital, +8% | 12/16 |

Teks isi minimal 14 px; 12 px hanya untuk label bernomor dan caption.

## Bentuk dan Efek

| Elemen | Aturan |
| --- | --- |
| Tombol | Pil, tinggi 44 px (kecil), 52 px (utama). Isi ungu, isi hitam, atau garis tinta 2 px |
| Kartu | Putih, garis paper-200 1 px, sudut 20–24 px; panel besar 32 px |
| Bayangan | Hanya untuk elemen yang mengambang di atas gambar (kolom cari, pin): bayangan lembut netral |
| Fokus | Outline 2 px violet-600 dengan jarak 2 px, terlihat di semua tombol |
| Judul bagian | Label bernomor, lalu judul dua ketukan di kiri dan satu kalimat pengantar di kanan |

## Ilustrasi

- **Hero "Halte ke pintu"**: lingkungan 3D jalan di Bogor. Angkot di halte, guiding block menyeberang zebra cross dengan kerb landai, tangga di samping ramp, toilet difabel, lift kaca, pohon Kebun Raya, dan hujan di atas gunung. Sisi kiri gambar sengaja tenang untuk judul.
- **Empat close-up**: guiding block (01), ramp (02), toilet difabel (03), dan lift (04). Nomornya sama dengan pin di hero, dan dipakai lagi di bagian "Rute ke pintu" Beranda dan di kartu fasilitas Detail.
- **Pembuatan**: `gpt-image-2` lewat Codex CLI, dengan hero sebagai acuan gaya untuk close-up. Prompt dicatat bersama gambar di repo web. Gambar ini ilustrasi, bukan foto lokasi.
- **Lapisan 3D**: WebGL tanpa library. Bagian depan gambar bergeser lebih jauh daripada latar belakang saat pointer bergerak, hujan tipis turun di sisi gambar, dan pin bernomor menempel di fasilitas. Berhenti saat gerak dikurangi; gambar statis tampil bila WebGL tidak ada.
- **Aksesibilitas**: gambar dan pin dekoratif (`alt=""`, `aria-hidden`); nama dan arti fasilitas tertulis di teks.

## Komponen

Daftar komponen Figma versi 1. Perilakunya tetap berlaku; tampilannya mengikuti aturan versi 2 di atas.

| Komponen | Varian | Properti utama |
| --- | --- | --- |
| Button | Style (Primary, Ink, Outline, Ghost, Danger) × Size (Medium 52 px, Small 44 px) × State (Default, Hover, Focus, Disabled) | Label, ikon depan/belakang (bisa diganti) |
| Toggle | Checked × State = 6 | Label |
| Checkbox | Checked × State = 6 | Label, deskripsi |
| Input | State (Default, Focus, Filled, Error, Disabled) = 5 | Label, teks, ikon depan, helper, pesan error |
| Status badge | Status (4) × Size (2) = 8 | Label tetap per status |
| Map marker | Status (4) × State (Default, Selected) = 8 | — |
| Card | Raised, Sunken | Judul, lokasi, isi, badge, aksi |
| Nav item | State (Default, Hover, Active, Focus) = 4 | Label |
| Nav bar | 1 | — |

Ditambah 33 ikon outline 24 px. Halaman Preview di Figma masih menampilkan versi 1.

### Komponen pola

Dibuat saat migrasi delapan layar, di bagian Patterns halaman Design System:

| Kelompok | Komponen |
| --- | --- |
| Umum | Logo (Default, Inverse), Tag (Brand), Check item, Footer |
| Beranda | Category tile (Default, Hover, Focus), Stat item |
| Peta | Map base (Kota Bogor skematis), Zoom control, Pan control, Map legend, Picker pin |
| Explore | Chip (terpilih atau tidak), Select, Location card (Default, Hover, Focus) |
| Detail | Facility item (Tersedia, Tidak tersedia, Tidak perlu, Belum diketahui; baris alasan dan catatan opsional), Kartu status (angka besar "X dari Y", empat nada status; menggantikan Score ring) |
| Laporkan | Stepper step, Stepper (langkah 1–3), Radio tile, Facility answer (Answer: Tersedia, Tidak tersedia, Tidak perlu, Belum tahu × Note: Closed, Open), Checkbox card, Textarea, Upload dropzone, Photo item |
| Mobile | App bar (Home, Back), Bottom nav item (5 tab × Default, Active), Bottom nav, Form action bar, Footer mobile, Location row |
| Akun | Google sign-in button, Report row (Menunggu, Klarifikasi, Diterbitkan, Ditolak) |

Komponen yang mengikuti keputusan produk:

- **Facility answer**: satu fasilitas dengan empat pilihan radio, bawaan "Belum tahu" ([ADR-0017](../decisions/0017-empat-jawaban-ketersediaan-fasilitas.md)). Di Detail, jawaban yang sama tampil lewat Facility item, dengan "Belum tahu" ditulis "Belum diketahui". Varian Tidak tersedia menampilkan pilihan alasan (Tidak ada, Rusak, Terhalang, Terbatas, Tidak sesuai), dan Note=Open menampilkan kolom catatan; Note=Closed menampilkan tombol "Tambah catatan" ([ADR-0022](../decisions/0022-tersedia-berarti-bisa-dipakai.md)).
- **Facility item**: properti Show detail menampilkan baris kedua berisi alasan dan catatan, misalnya "Tidak sesuai · Jalur terputus sebelum pintu masuk" ([ADR-0022](../decisions/0022-tersedia-berarti-bisa-dipakai.md)). Karena bisa dua baris, sudutnya memakai `radius/lg`, bukan bentuk pil.
- **Kartu status** (pengganti Score ring): angka X (fasilitas relevan yang Tersedia) dan teks "dari Y fasilitas tersedia" di atas latar warna status, bersama nama status dan kalimat alasannya; bukan skor 0–100 ([ADR-0018](../decisions/0018-status-pintu-masuk-dulu-dan-ringkasan-fasilitas.md)).
- **Picker pin** dan **Pan control**: pin tetap di tengah peta pemilih lokasi, dan tombol arah 48 px untuk pengguna yang tidak bisa menyeret peta ([ADR-0015](../decisions/0015-input-lokasi-peta-gps-pencarian.md)). Ikon Locate dipakai di tombol "Gunakan lokasi saya".
- **Map base**: peta skematis Kota Bogor (batas kota, enam kecamatan, Kebun Raya, Ciliwung, Cisadane, jalan utama, rel) yang digambar dari token warna, dipakai di Map dan peta kecil Detail. Untuk peta kecil, instance diperbesar dan layer label disembunyikan. Komponen ini hanya pengganti visual; di produk peta dirender Mapbox ([ADR-0016](../decisions/0016-peta-dan-pencarian-mapbox.md)).
- **Upload dropzone** dan **Photo item**: dropzone menerima hingga 10 foto JPEG, PNG, atau WebP. Setiap foto yang dipilih tampil sebagai Photo item berisi pratinjau, nama dan ukuran berkas, Select untuk tag, dan tombol hapus 44 px, dalam dua baris sehingga Select selebar kartu di layar sempit ([ADR-0023](../decisions/0023-hingga-sepuluh-foto-bertag-per-laporan.md)).

### Akun

- **Button Style=Danger**: untuk aksi yang menghapus data, mis. "Hapus akun saya". Isi memakai `color/danger/fill` (red-700) dan `color/danger/fill-hover` (red-800) dengan teks putih, tanpa glow ungu; Focus hanya memakai cincin fokus. Labelnya selalu menyebut apa yang dihapus.
- **Google sign-in button**: pengganti visual tombol Google Identity Services ("Lanjutkan dengan Google"). Di produk tombol dirender Google, dan warna logonya mengikuti aturan merek Google, bukan token.
- **Report row**: satu laporan di "Laporan saya". Nama, lalu status berupa ikon dan teks dengan warna status, tanggal, dan alasan atau pertanyaan verifikator bila ada.
- Nav bar desktop kini punya tombol "Masuk" (atau "Akun saya" setelah login) di sebelah "Laporkan tempat". Ikon Clock, Trash, dan LogOut ditambahkan.

### Navigasi mobile

Di lebar 360 px, nav bar desktop (tujuh menu) diganti komponen berikut:

- **App bar**: menempel di atas. Type=Home berisi logo dan tombol Masuk; Type=Back berisi tombol Kembali 44 px dan judul satu baris.
- **Bottom nav**: menempel di bawah layar dengan lima tab, yaitu Beranda, Jelajahi, Peta, Laporkan, dan Lainnya. Menu lain dari nav desktop pindah ke Lainnya. Tab aktif ditandai indikator, warna brand, dan `aria-current="page"`, bukan warna saja. Halaman diberi ruang bawah setinggi nav supaya elemen yang difokus tidak tertutup (WCAG 2.4.11).
- **Form action bar**: menggantikan Bottom nav selama mengisi formulir bertahap. Tombol utama ("Lanjut" atau "Kirim laporan") selebar sisa bar, dan "Kembali" muncul mulai langkah kedua; tombol Kembali di App bar keluar dari formulir.

Komponen bersama menyesuaikan lebar 328 px: garis penghubung Stepper meregang, nilai Input dan Select dipotong satu baris, dan label, teks bantuan, label Checkbox, serta petunjuk dropzone membungkus.

Ikon Home, Map, Menu, dan User ditambahkan untuk pola ini. Footer mobile menumpuk kolom footer desktop dengan tautan setinggi 44 px, dan Location row adalah kartu ringkas satu lokasi untuk Peta.

Footer memakai `color/surface/inverse` (tinta gelap opak), dan angka di bawah hero memakai pita `color/bg/road` (aspal).

## Kontras

Diukur di permukaan opak, jadi tidak ada kasus terburuk yang bergantung pada latar.

| Kombinasi | Rasio | Mode |
| --- | --- | --- |
| Teks utama ink-950 di latar paper-50 | 17.6:1 | Terang |
| Teks sekunder ink-600 di paper-50 / di tint ungu | 7.6:1 / 6.5:1 | Terang |
| Teks brand violet-700 di paper-50 | 7.8:1 | Terang |
| Putih di tombol violet-600 | 6.1:1 | Semua mode |
| Teks status tingkat 800 di latar statusnya | 5.6–6.8:1 | Terang |
| Putih / kuning di pita aspal | 10.4:1 / 6.2:1 | Terang |
| Border field ink-500 (non-teks) di putih | 5.1:1 | Terang |
| Teks utama ink-50 di latar ink-950 | 17.2:1 | Gelap |
| Teks sekunder ink-300 di kartu ink-900 | 9.7:1 | Gelap |
| Teks brand violet-300 di kartu ink-900 | 7.6:1 | Gelap |

## Riwayat

### Versi 1: kaca dan aurora

Design system versi 1 ([ADR-0009](../decisions/0009-design-system-kaca-ungu-inklusi.md)) memakai permukaan kaca putih 72% dengan blur 24 px di atas latar aurora (ungu, biru langit, mint), tombol berkilap dengan glow ungu, serta tiga mode: Glass, Solid, dan Dark. Kontrasnya dihitung untuk kasus terburuk kaca di atas gumpalan latar paling pekat. Versi ini diganti karena semua permukaan menjadi kartu kaca yang sama dan kontras teks bergantung pada latar yang bergeser.

### Palet desain awal

Delapan layar desain awal memakai biru `#387AFF`, teks sekunder `#6A6E81`, dan warna status `#39D597`, `#FFCC33`, `#F37777`. Audit 2026-10-21 menemukan:

- Semua warna status gagal AA sebagai teks (1.5–2.7:1).
- Teks putih di tombol `#387AFF` hanya 3.9:1.
- `#6A6E81` turun ke 3.8:1 di atas kaca.

Palet itu digantikan design system di atas. Layar-layar tersebut belum dimigrasi ke token dan komponen baru.
