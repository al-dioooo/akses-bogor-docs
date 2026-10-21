# Fondasi Visual

Diekstrak dari [desain Figma](../product/referensi-figma.md) per 2026-10-21. File Figma belum punya variable atau style, jadi dokumen ini menjadi acuan sementara sampai design token diformalkan. Nama token di bawah adalah usulan.

## Tipografi

| Peran | Font | Ukuran | Weight | Contoh |
| --- | --- | --- | --- | --- |
| Display | Plus Jakarta Sans | 60 px, tracking −1.5 px | Bold | "Akses untuk Semua" |
| Heading halaman/section | Plus Jakarta Sans | 36 px | Bold | "Explore Lokasi", "Dampak AksesBogor" |
| Angka statistik | Plus Jakarta Sans | 30 px | Bold | "125+", "80%" |
| Judul kartu | Plus Jakarta Sans | 20 px | SemiBold | "Deskripsi", "Galeri Foto" |
| Subjudul | Plus Jakarta Sans | 18 px | SemiBold | Nama kategori, judul kolom footer |
| Lead | Inter | 20 px | Regular | Subjudul hero |
| Body | Inter | 14–16 px | Regular | Paragraf, deskripsi |
| Label dan tombol | Inter | 14–16 px | Medium, SemiBold | Navigasi, tombol |
| Caption | Inter | 12 px | Regular, Medium | "25 lokasi", "PNG, JPG hingga 5MB" |

## Warna

| Token (usulan) | Nilai | Dipakai untuk |
| --- | --- | --- |
| `color-primary` | `#387AFF` | Tombol utama, link, menu navigasi aktif |
| `color-primary-subtle` | `#387AFF` opasitas 10% | Latar ikon, latar menu aktif |
| `color-text` | `#1E2029` | Teks utama, latar footer |
| `color-text-muted` | `#6A6E81` | Teks sekunder |
| `color-background` | `#F0F3FF` | Latar halaman |
| `color-surface` | `#FFFFFF` | Kartu, header |
| `color-surface-muted` | `#E8EDF7` | Placeholder peta, latar ilustrasi |
| `color-border` | `#DEE3ED` | Border input dan kartu |
| `color-status-accessible` | `#39D597` | Status Accessible, fasilitas tersedia, langkah selesai |
| `color-status-partial` | `#FFCC33` | Status Partial, label skor "Baik" |
| `color-status-partial-marker` | `#F5A623` | Marker Partial di peta |
| `color-status-unavailable` | `#F37777` | Fasilitas tidak tersedia |

## Kontras Warna (WCAG 2.2)

Ambang AA: 4.5:1 untuk teks normal, 3:1 untuk teks besar dan komponen UI.

| Kombinasi | Rasio | Teks normal | Teks besar / UI |
| --- | --- | --- | --- |
| `#1E2029` di putih | 16.23:1 | Lulus | Lulus |
| `#1E2029` di `#F0F3FF` | 14.66:1 | Lulus | Lulus |
| `#6A6E81` di putih | 5.05:1 | Lulus | Lulus |
| `#6A6E81` di `#F0F3FF` | 4.56:1 | Lulus (tipis) | Lulus |
| Putih di tombol `#387AFF` | 3.89:1 | **Gagal** | Lulus |
| Link `#387AFF` di putih | 3.89:1 | **Gagal** | Lulus |
| Link `#387AFF` di `#F0F3FF` | 3.52:1 | **Gagal** | Lulus |
| Hijau `#39D597` di putih | 1.89:1 | **Gagal** | **Gagal** |
| Hijau `#39D597` di latar hijau 10% | 1.76:1 | **Gagal** | **Gagal** |
| Kuning `#FFCC33` di putih | 1.51:1 | **Gagal** | **Gagal** |
| Merah `#F37777` di putih | 2.72:1 | **Gagal** | **Gagal** |
| Merah `#F37777` di latar merah 10% | 2.47:1 | **Gagal** | **Gagal** |

Semua warna status (Accessible, Partial, tidak tersedia) gagal AA sebagai warna teks, dan teks putih 14 px di tombol utama juga gagal. Untuk platform aksesibilitas, palet ini perlu disesuaikan sebelum implementasi. Status juga sebaiknya tidak dibedakan dengan warna saja; desain sudah memakai ikon ✓/✗ di daftar fasilitas, tetapi badge status dan marker peta hanya memakai warna.

## Bentuk dan Layout

| Elemen | Nilai |
| --- | --- |
| Radius kartu | 20 px |
| Radius tombol, input, menu navigasi | 16 px |
| Radius tombol ikon | 12 px |
| Badge dan chip | Pill (9999 px) |
| Bayangan tombol | `0 4px 6px -1px rgb(0 0 0 / 0.1), 0 2px 4px -2px rgb(0 0 0 / 0.1)` |
| Lebar kontainer maksimum | 1280 px |
| Lebar konten sempit | 768 px (hero), 672 px (formulir laporan) |
| Lebar frame desain | 1440 px (desktop saja) |
