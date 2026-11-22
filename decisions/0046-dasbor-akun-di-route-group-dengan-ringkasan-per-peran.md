# ADR-0046: Dasbor Akun di Route Group yang Sama, dengan Ringkasan per Peran dan URL Lama Tetap

- Status: Proposed
- Tanggal: 2026-11-22
- Pengambil keputusan: Tim AksesBogor
- Issue: #87
- Melengkapi: [ADR-0035](0035-peran-dan-panel-verifikator-di-web-yang-sama.md), [ADR-0043](0043-agen-untuk-kontributor-dengan-model-gratis-openrouter.md)

## Konteks dan Masalah

Halaman yang butuh akun di akses-bogor-web dibangun bertahap sebagai halaman terpisah di layout publik: Akun saya (`/akun`), jawab klarifikasi (`/akun/laporan/[id]`), Hapus akun (`/akun/hapus`), meja verifikasi dan Tim verifikator (`/verifikasi/*`, [ADR-0035](0035-peran-dan-panel-verifikator-di-web-yang-sama.md)), dan Bantu memetakan (`/laporkan/bantu-memetakan`, [ADR-0043](0043-agen-untuk-kontributor-dengan-model-gratis-openrouter.md)). Tidak ada satu tempat yang menunjukkan apa yang perlu dikerjakan pemilik akun, dan verifikator berpindah lewat tautan di Akun saya. Pengguna meminta satu tata letak dasbor untuk semua fitur yang butuh masuk (akses-bogor-web#113). Di mana dasbor berada, apa isi halaman pertamanya untuk setiap peran, dan dari data apa?

## Decision Drivers

- URL lama tetap berlaku: email klarifikasi menautkan `/akun/laporan/{id}`, dan tim sudah memakai `/verifikasi`.
- Peran tetap diperiksa di API ([ADR-0035](0035-peran-dan-panel-verifikator-di-web-yang-sama.md)); menu hanya memilih apa yang ditampilkan.
- Header, footer, dan Bottom nav publik tidak diubah: Peta layar penuh sedang mengubahnya (akses-bogor-web#110), dan pengaturan tampilan (mode gelap, kurangi gerakan) ada di header.
- WCAG 2.2 AA ([ADR-0021](0021-wcag-22-aa-dan-pengujian-berlapis.md)) di lebar 320 px sampai layar lebar, tanpa gulir horizontal, termasuk 1024–1279 px.
- Web tidak bergantung pada versi API baru bila data yang ada sudah cukup, dan semua data tetap lewat server web ([ADR-0029](0029-akun-better-auth-dan-sesi-lewat-server-web.md)).

## Opsi yang Dipertimbangkan

1. Route group `(dasbor)` di aplikasi yang sama, URL lama tetap, `/akun` menjadi Ringkasan
2. Semua halaman pindah ke awalan baru `/dasbor/*` dengan redirect dari URL lama
3. Root layout kedua untuk dasbor tanpa header dan footer publik

## Keputusan

Opsi terpilih: "Route group `(dasbor)`", karena tidak ada URL yang berubah, tautan "Akun saya" di header sudah menuju `/akun`, dan root layout yang sedang diubah untuk Peta tidak tersentuh.

### Susunan halaman

| URL | Menu | Untuk |
| --- | --- | --- |
| `/akun` | Ringkasan | Semua akun yang sudah lengkap |
| `/akun/laporan` (baru), `/akun/laporan/[id]` | Laporan saya | Semua akun |
| `/laporkan/bantu-memetakan` | Bantu memetakan | Semua akun, selama API membuka agen |
| `/verifikasi`, `/verifikasi/jawaban`, `/verifikasi/cek-ulang`, `/verifikasi/laporan/[id]` | Antrean, Menunggu jawaban, Perlu dicek ulang | Verifikator dan admin |
| `/verifikasi/tim` | Tim verifikator | Admin |
| `/akun/pengaturan` (baru), `/akun/hapus` | Pengaturan akun | Semua akun |

- Masuk, Kode Masuk, Lengkapi Akun (`/masuk/*`) dan Akun dihapus (`/akun/dihapus`) tetap di luar dasbor, karena dibuka tanpa akun yang lengkap. Laporkan (`/laporkan`) juga tetap di luar, karena bisa diisi sebelum masuk ([ADR-0033](0033-isi-dulu-masuk-saat-kirim-dan-draf-di-perangkat.md)).
- Semua halaman dasbor diberi `noindex`, seperti panel verifikator sebelumnya.

### Kerangka

- Root layout tetap memberi header, footer, dan Bottom nav. Layout `(dasbor)` menambah menu dan `main` di bawah header, sehingga skip link melompati header dan menu sekaligus.
- Mulai 1024 px menu berupa sidebar yang menempel di kiri: inisial, nama tampilan, dan peran pemilik akun; bagian menu bernomor (Kontribusi, Verifikasi, Admin, Akun); Keluar; dan tombol Laporkan tempat. Di bawah 1024 px, tombol Menu di atas isi halaman membuka elemen yang sama sebagai popover. Escape menutupnya dan fokus kembali ke tombol.
- Bagian Verifikasi hanya disusun untuk verifikator dan admin, bagian Admin hanya untuk admin. Halaman tetap memeriksa peran di server dan API tetap menjawab 403, jadi akun yang perannya dicabut tidak mendapat data walaupun menunya masih terbuka di tab lama.
- Menu yang dibuka ditandai `aria-current="page"` dan pil tinta. Bagian menu tidak memakai elemen judul, supaya judul halaman tetap h1 pertama.
- Tab meja verifikasi di dalam halaman hanya tampil di bawah 1024 px, karena di layar lebar sidebar sudah memuatnya.

### Ringkasan

Isi `/akun` mengikuti peran. Setiap bagian dimuat sendiri, punya keadaan memuat, dan punya pesan galat dengan tombol coba lagi, jadi satu panggilan yang gagal tidak mengosongkan halaman.

| Bagian | Untuk | Data | Bila kosong |
| --- | --- | --- | --- |
| Yang menunggu Anda: pertanyaan klarifikasi dengan tenggat, laporan antrean yang lewat target 3 hari kerja, dan laporan yang sudah dijawab pelapor | Semua; bagian antrean untuk verifikator dan admin | `GET /me/reports`, `GET /verifier/reports` | "Tidak ada yang menunggu Anda" |
| Meja verifikasi: jumlah antrean dan umur laporan terlama, menunggu jawaban dan tenggat terdekat, perlu dicek ulang | Verifikator dan admin | `GET /verifier/reports?status=menunggu`, `?status=klarifikasi`, `GET /verifier/places/recheck` | Angka 0 dengan kalimat yang menjelaskannya |
| Tim verifikator: jumlah verifikator dan admin | Admin | `GET /admin/team` | Ajakan menambah verifikator |
| Laporan saya: jumlah per status dan tiga laporan terbaru | Semua | `GET /me/reports` | Rute pertama: empat langkah dari akun baru sampai tempat tayang |
| Petakan berikutnya: tiga kecamatan dengan prioritas tertinggi | Semua | `GET /districts/priority`, `GET /agent/tools` tanpa token | Bila prioritas 503 atau agen mati, hanya ajakan Laporkan tempat |

- Prioritas kecamatan selalu tampil dengan kalimat batas data dan sumbernya ([ADR-0043](0043-agen-untuk-kontributor-dengan-model-gratis-openrouter.md)). Selama `DISABILITY_DATA_ENABLED` mati di production, bagian ini hanya mengajak melapor.
- Tidak ada route API baru. `GET /me/summary` sempat dipertimbangkan, tetapi semua angka sudah tersedia dari route yang ada, setiap route sudah memeriksa peran, dan web tidak perlu menunggu rilis API. Batasnya: `GET /me/reports` memuat 100 laporan terbaru, jadi jumlah per status dihitung dari 100 laporan itu; jumlah antrean dan cek ulang memakai `total` dari API sehingga tetap tepat. Bila ada kontributor yang mendekati 100 laporan, route ringkasan dipertimbangkan lagi.
- Kerangka dan halaman berbagi satu `GET /me` per request, dan setiap daftar dimuat sekali per request walaupun dipakai lebih dari satu bagian.

## Konsekuensi

- Positif: pemilik akun langsung melihat apa yang perlu dikerjakan, termasuk tenggat klarifikasi yang sebelumnya hanya ada di email dan di daftar laporan.
- Positif: verifikator dan admin berpindah antarbagian meja dan laporannya sendiri tanpa kembali ke Akun saya.
- Positif: tidak ada redirect, URL baru hanya `/akun/laporan` dan `/akun/pengaturan`, dan tautan di email tetap sama.
- Negatif: footer publik tetap tampil di bawah dasbor, dan header publik tetap di atasnya. Bila Peta layar penuh menambah cara menyembunyikan footer, dasbor bisa memakainya.
- Negatif: halaman Ringkasan verifikator memanggil sampai enam route API per kunjungan (me, laporan saya, dua antrean, cek ulang, prioritas), ditambah tim untuk admin. Dengan tim kecil dan daftar paling banyak 100 baris, bebannya kecil.
- Negatif: menu di sidebar dibaca saat halaman pertama dibuka; peran yang berubah baru terlihat di menu setelah halaman dimuat ulang. Datanya tetap dijaga API.

## Pro & Kontra tiap Opsi

### Route group `(dasbor)`

- Pro: URL dan tautan email tetap; perubahan root layout tidak perlu.
- Kontra: segmen `akun` dan `laporkan` terbagi di dua folder (di dalam dan di luar route group).

### Awalan `/dasbor/*`

- Pro: semua halaman akun di satu awalan URL.
- Kontra: setiap URL lama butuh redirect, tautan di email lama tetap memakai alamat lama, dan semua tes serta tautan berubah.

### Root layout kedua

- Pro: dasbor bisa tampil tanpa header dan footer publik.
- Kontra: semua halaman publik harus pindah ke route group lain, berbenturan dengan pekerjaan Peta; pindah antara dua root layout memuat ulang seluruh halaman; pengaturan tampilan harus dibuat ulang di dasbor.

## Referensi

- [ADR-0021](0021-wcag-22-aa-dan-pengujian-berlapis.md), [ADR-0027](0027-design-system-datar-dan-ilustrasi-3d.md), [ADR-0029](0029-akun-better-auth-dan-sesi-lewat-server-web.md), [ADR-0033](0033-isi-dulu-masuk-saat-kirim-dan-draf-di-perangkat.md), [ADR-0035](0035-peran-dan-panel-verifikator-di-web-yang-sama.md), [ADR-0036](0036-keputusan-verifikasi-dan-klarifikasi-di-laporan-saya.md), [ADR-0043](0043-agen-untuk-kontributor-dengan-model-gratis-openrouter.md)
- akses-bogor-web#110 (Peta layar penuh), akses-bogor-web#113 (dasbor)
- [Next.js Route Groups](https://nextjs.org/docs/app/api-reference/file-conventions/route-groups)
