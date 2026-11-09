# ADR-0041: Halaman Edukasi Aksesibilitas, Disabilitas, dan Tips Tayang sebagai Draf sampai Ditinjau Komunitas

- Status: Proposed
- Tanggal: 2026-11-09
- Pengambil keputusan: Tim AksesBogor
- Issue: #69
- Melengkapi: [ADR-0017](0017-empat-jawaban-ketersediaan-fasilitas.md), [ADR-0018](0018-status-pintu-masuk-dulu-dan-ringkasan-fasilitas.md), [ADR-0022](0022-tersedia-berarti-bisa-dipakai.md)

## Konteks dan Masalah

FR-51 meminta halaman edukasi Accessibility, Difabel, dan Tips. Nav bar, menu Lainnya, dan footer di akses-bogor-web sudah menautkan `/aksesibilitas`, `/difabel`, dan `/tips`, tetapi ketiganya masih menampilkan halaman tidak ditemukan. Belum ada frame Figma untuk halaman ini. Teks seperti ini mudah salah: istilah yang menyinggung, pasal atau angka yang tidak bisa diperiksa, atau saran yang tidak cocok dengan cara AksesBogor mencatat tempat. Apa isi setiap halaman, istilah apa yang dipakai, sumber apa yang boleh dikutip, dan kapan isinya dianggap final?

## Decision Drivers

- Pengguna bisa membaca arti status dan jawaban fasilitas, dan pelapor tahu cara melapor yang bisa diverifikasi.
- Cara pandang model sosial: hambatan ada di lingkungan, jadi yang dinilai adalah tempat, bukan orangnya.
- Tidak ada pasal, angka, atau kutipan yang tidak bisa diperiksa di teks sumbernya.
- Bahasa Indonesia sederhana ([ADR-0020](0020-antarmuka-bahasa-indonesia-siap-i18n.md)) dan WCAG 2.2 AA ([ADR-0021](0021-wcag-22-aa-dan-pengujian-berlapis.md)).
- Tim belum punya mitra organisasi atau komunitas penyandang disabilitas yang meninjau isi.

## Opsi yang Dipertimbangkan

Isi halaman:

1. Tiga halaman pendek yang menjelaskan aturan AksesBogor, hanya mengutip sumber primer, dan tayang sebagai draf sampai ditinjau komunitas
2. Menunda halaman sampai ada mitra komunitas yang ikut menulis
3. Merangkum materi edukasi dari organisasi atau situs lain

Label dan alamat halaman kedua:

1. Label "Disabilitas" di `/disabilitas`
2. Label "Difabel" di `/difabel`, seperti di frame Figma DS v1
3. Label "Penyandang Disabilitas"

## Keputusan

Opsi terpilih: "Tiga halaman pendek, sumber primer saja, tayang sebagai draf", karena isinya sebagian besar menjelaskan aturan yang sudah diputuskan di ADR, dan "Label Disabilitas di `/disabilitas`", karena halaman itu menjelaskan istilah hukum "penyandang disabilitas".

### Halaman dan isinya

| Halaman | Tujuan | Garis besar isi |
| --- | --- | --- |
| Aksesibilitas (`/aksesibilitas`) | Menjelaskan cara AksesBogor menilai sebuah tempat | Arti aksesibilitas dan hak atas fasilitas publik menurut UU No. 8 Tahun 2016; empat fasilitas dan siapa yang membutuhkannya; empat jawaban dan alasan Tidak tersedia ([ADR-0017](0017-empat-jawaban-ketersediaan-fasilitas.md), [ADR-0022](0022-tersedia-berarti-bisa-dipakai.md)); "Tersedia berarti bisa dipakai"; status "pintu masuk dulu" dan ringkasan "X dari Y fasilitas" ([ADR-0018](0018-status-pintu-masuk-dulu-dan-ringkasan-fasilitas.md)); batas penilaian: bukan penilaian resmi dan tanpa ukuran teknis |
| Disabilitas (`/disabilitas`) | Menjelaskan istilah dan cara pandang AksesBogor | Definisi penyandang disabilitas menurut UU No. 8 Tahun 2016; hambatan ada di lingkungan; empat ragam disabilitas dan kebutuhan yang berbeda; batas empat fasilitas; kata yang kami pakai; peran mitra komunitas dalam verifikasi ([ADR-0019](0019-verifikasi-tim-dan-mitra-terlatih.md)) |
| Tips (`/tips`) | Saran praktis sebelum berkunjung dan saat melapor | Sebelum berangkat: membaca status dan alasannya, ringkasan, foto, tanggal verifikasi, dan tanda perlu dicek ulang, lalu menghubungi pengelola bila kunjungan bergantung pada satu fasilitas. Saat melapor: pin di pintu masuk utama, menjawab yang dilihat sendiri, Belum tahu lebih baik daripada menebak, alasan dan catatan tanpa data pribadi. Foto: satu jauh dan satu dekat, yang perlu terlihat untuk setiap fasilitas ([Panduan Verifikasi](../operations/panduan-verifikasi.md) bagian 5.2), tanpa wajah, pelat nomor, atau toilet yang sedang dipakai ([ADR-0023](0023-hingga-sepuluh-foto-bertag-per-laporan.md), [ADR-0037](0037-foto-dikaburkan-di-api-dan-asli-dihapus.md)). Setelah mengirim: klarifikasi dijawab dalam 7 hari, dan "Laporkan kekurangan" untuk tempat yang sudah ada |

Setiap halaman:

- menampilkan tanda draf yang terlihat, dengan komponen yang sama dengan tanda draf di Kebijakan Privasi dan Syarat Layanan;
- menutup dengan daftar sumber, lalu tautan ke Jelajahi dan Laporkan;
- adalah halaman statis tanpa data dari API, dengan semua teks di `messages/id.json`;
- punya satu h1, heading yang berurutan, dan teks tautan yang jelas tanpa konteks.

### Istilah

- Label nav dan footer menjadi "Disabilitas", dengan alamat `/disabilitas`. `/difabel` tidak dialihkan karena halamannya belum pernah ada.
- Untuk orang, halaman memakai "penyandang disabilitas", atau menyebut kebutuhannya, mis. "pengguna kursi roda" dan "tunanetra dan low vision", seperti di PRD.
- Kata "difabel" tetap ada pada nama fasilitas "Toilet Difabel" ([ADR-0017](0017-empat-jawaban-ketersediaan-fasilitas.md)). ADR ini tidak mengubah nama fasilitas.
- Halaman tidak memakai "cacat", "penderita", atau "normal" sebagai lawan disabilitas, dan tidak memakai kalimat yang mengasihani atau memuji berlebihan. UU No. 8 Tahun 2016 sendiri mengganti UU No. 4 Tahun 1997 tentang Penyandang Cacat karena tidak sesuai lagi dengan "paradigma kebutuhan penyandang disabilitas" (bagian Menimbang huruf d).
- Kalimatnya menyebut hambatan pada tempat, mis. "pintu masuk bertangga", bukan keterbatasan pada orang.

### Sumber

Halaman hanya mengutip sumber yang teksnya sudah diperiksa di sumber primer:

| Sumber | Yang dipakai |
| --- | --- |
| UU No. 8 Tahun 2016 tentang Penyandang Disabilitas (Lembaran Negara 2016 Nomor 69), teks di peraturan.go.id | Pasal 1 angka 1 (definisi penyandang disabilitas) dan angka 8 (aksesibilitas), Pasal 4 ayat (1) dan (2) (ragam), Pasal 18 (aksesibilitas untuk fasilitas publik), Pasal 97 (infrastruktur, termasuk bangunan gedung), dan Menimbang huruf d |
| Peraturan Menteri PUPR No. 14/PRT/M/2017 tentang Persyaratan Kemudahan Bangunan Gedung | Hanya disebut sebagai tempat ketentuan teknis resmi, seperti di Panduan Verifikasi. Halaman tidak mengutip pasal atau ukurannya. |
| ADR AksesBogor dan [Panduan Verifikasi](../operations/panduan-verifikasi.md) | Aturan produk: fasilitas, jawaban, status, foto, dan verifikasi |

- Halaman tidak memuat statistik jumlah penyandang disabilitas, kutipan tokoh, Penjelasan undang-undang, putusan pengadilan, atau peraturan daerah.
- Pasal 98 dan 99 (sanksi dan izin bangunan gedung) tidak dikutip. Halaman ini bukan nasihat hukum.
- Sumber baru ditambahkan lewat PR setelah teksnya diperiksa di sumber primer.

### Status draf

- Ketiga halaman tayang dengan tanda draf: isinya menunggu tinjauan organisasi atau komunitas penyandang disabilitas di Bogor dan bisa berubah.
- Tanda draf dilepas lewat PR setelah setidaknya satu organisasi atau komunitas meninjau ketiga halaman. PR itu mencatat siapa yang meninjau dan kapan, bila mereka setuju disebut.
- Masukan tinjauan diterapkan lewat PR biasa. Mengubah istilah, label, atau tujuan halaman butuh ADR baru.

## Konsekuensi

- Positif: tautan Aksesibilitas, Disabilitas, dan Tips di nav dan footer tidak lagi berakhir di halaman tidak ditemukan.
- Positif: pelapor membaca panduan foto yang sama dengan verifikator, sehingga laporan lebih mudah dibuktikan.
- Positif: setiap kutipan hukum bisa diperiksa ulang ke teks undang-undang.
- Negatif: sampai tinjauan selesai, isi bisa memuat istilah atau saran yang kurang tepat menurut penyandang disabilitas sendiri.
- Negatif: aturan produk tertulis di dua tempat. Bila [ADR-0017](0017-empat-jawaban-ketersediaan-fasilitas.md), [ADR-0018](0018-status-pintu-masuk-dulu-dan-ringkasan-fasilitas.md), [ADR-0022](0022-tersedia-berarti-bisa-dipakai.md), [ADR-0023](0023-hingga-sepuluh-foto-bertag-per-laporan.md), atau [ADR-0037](0037-foto-dikaburkan-di-api-dan-asli-dihapus.md) berubah, halaman ini ikut diperbarui.
- Negatif: label "Disabilitas" berbeda dari frame Figma DS v1.
- Tindak lanjut: cari organisasi atau komunitas penyandang disabilitas di Bogor yang mau meninjau ketiga halaman, sebaiknya bersama rekrutmen verifikator mitra ([ADR-0019](0019-verifikasi-tim-dan-mitra-terlatih.md)).
- Tindak lanjut: status berlaku Permen PUPR No. 14/PRT/M/2017 belum diperiksa di JDIH Kementerian PUPR.

## Pro & Kontra tiap Opsi

### Tiga halaman pendek, sumber primer saja, sebagai draf

- Pro: tautan mati hilang sekarang, dan isinya bisa diperiksa ke ADR dan teks undang-undang.
- Kontra: ditulis tim tanpa penyandang disabilitas sebagai penulis.

### Menunda sampai ada mitra komunitas

- Pro: isi ditulis bersama orang yang paling paham.
- Kontra: tautan nav tetap mati tanpa batas waktu, dan pelapor tetap tanpa panduan foto.

### Merangkum materi pihak lain

- Pro: memakai materi yang mungkin sudah ditinjau komunitas.
- Kontra: perlu izin dan atribusi, sulit dicek kesesuaiannya dengan aturan AksesBogor, dan tidak menjelaskan status atau jawaban fasilitas.

### Label "Disabilitas"

- Pro: sama dengan istilah di undang-undang dan dengan isi halaman; satu kata, sehingga muat di nav.
- Kontra: berbeda dari frame Figma.

### Label "Difabel"

- Pro: sesuai Figma, dan kata ini juga dipakai di nama fasilitas Toilet Difabel.
- Kontra: halaman yang menjelaskan "penyandang disabilitas" diberi label dengan kata lain, dan labelnya terbaca sebagai nama kelompok orang, bukan topik.

### Label "Penyandang Disabilitas"

- Pro: istilah hukum lengkap.
- Kontra: terlalu panjang untuk nav bar dan menu Lainnya.

## Referensi

- [PRD](../product/prd.md), FR-51
- [Panduan Verifikasi](../operations/panduan-verifikasi.md), bagian 4.2, 5, dan 8
- [ADR-0017](0017-empat-jawaban-ketersediaan-fasilitas.md), [ADR-0018](0018-status-pintu-masuk-dulu-dan-ringkasan-fasilitas.md), [ADR-0019](0019-verifikasi-tim-dan-mitra-terlatih.md), [ADR-0020](0020-antarmuka-bahasa-indonesia-siap-i18n.md), [ADR-0021](0021-wcag-22-aa-dan-pengujian-berlapis.md), [ADR-0022](0022-tersedia-berarti-bisa-dipakai.md), [ADR-0023](0023-hingga-sepuluh-foto-bertag-per-laporan.md), [ADR-0027](0027-design-system-datar-dan-ilustrasi-3d.md), [ADR-0037](0037-foto-dikaburkan-di-api-dan-asli-dihapus.md)
- [UU No. 8 Tahun 2016 tentang Penyandang Disabilitas](https://peraturan.go.id/files/uu8-2016bt.pdf) (PDF, peraturan.go.id)
- Peraturan Menteri PUPR No. 14/PRT/M/2017 tentang Persyaratan Kemudahan Bangunan Gedung
