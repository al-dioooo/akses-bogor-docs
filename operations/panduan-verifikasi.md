# Panduan Verifikasi

| | |
| --- | --- |
| Status | Draft |
| Versi | 0.1 |
| Tanggal | 2026-10-21 |
| Berlaku untuk | Verifikator tim inti dan mitra terlatih ([ADR-0019](../decisions/0019-verifikasi-tim-dan-mitra-terlatih.md)) |
| Issue | #24 |

Panduan ini menjelaskan cara memeriksa laporan warga sebelum tayang di AksesBogor. Aturan status ada di [ADR-0018](../decisions/0018-status-pintu-masuk-dulu-dan-ringkasan-fasilitas.md) dan arti setiap jawaban fasilitas di [ADR-0017](../decisions/0017-empat-jawaban-ketersediaan-fasilitas.md). Bila panduan ini bertentangan dengan ADR, ADR yang berlaku.

## 1. Prinsip

1. **Lindungi pengguna dulu.** Lebih baik menulis "Belum tahu" daripada menandai "Tersedia" tanpa bukti. Pengguna kursi roda yang datang karena data kita tidak boleh terjebak di depan tangga.
2. **Periksa bukti, bukan niat.** Setiap jawaban "Tersedia" atau "Tidak perlu" harus terlihat di foto, terbukti lewat kunjungan lapangan, atau dikonfirmasi pengelola tempat.
3. **Konsisten.** Pakai daftar periksa ini dan alasan baku, bukan selera pribadi.
4. **Hormati privasi.** Pelapor, pengunjung, dan staf tempat tidak boleh dikenali dari data atau foto yang tayang.
5. **Ramah.** Pesan ke pelapor ditulis sopan dan menjelaskan apa yang dibutuhkan.

## 2. Peran dan Etika

- Peran verifikator diberikan oleh admin setelah pelatihan (FR-36).
- Jangan memverifikasi laporan sendiri (FR-37). Antrean tidak menampilkannya kepadamu.
- Lewati laporan yang punya konflik kepentingan, misalnya tempat milik keluarga atau tempat kerjamu. Kembalikan ke antrean dengan catatan "konflik kepentingan".
- Kamu hanya melihat nama tampilan pelapor. Jangan mencari atau menghubungi pelapor di luar AksesBogor; klarifikasi selalu lewat tombol "Minta klarifikasi".
- Data laporan bersifat rahasia sampai tayang. Jangan menyalin, memotret, atau membagikan isi antrean.

## 3. Alur Kerja per Laporan

1. Buka antrean dan ambil laporan **terlama** lebih dulu.
2. Periksa **informasi dasar** (bagian 4.1).
3. Periksa **setiap fasilitas** dengan kriteria di bagian 4.2.
4. Periksa **foto**: kecukupan bukti (bagian 5) dan privasi (bagian 8).
5. Untuk perubahan pada lokasi yang sudah tayang, bandingkan dengan data saat ini (bagian 7).
6. Putuskan: **Terbitkan**, **Minta klarifikasi**, atau **Tolak** (bagian 6).
7. Tulis catatan singkat untuk verifikator lain, misalnya "Ramp terlihat di foto 2; lift dikonfirmasi pengelola lewat telepon".

Target median keputusan adalah 3 hari kerja sejak laporan dikirim. Bila kamu ragu lebih dari 15 menit pada satu laporan, eskalasikan (bagian 9).

## 4. Daftar Periksa

### 4.1 Informasi Dasar

| Periksa | Bila tidak sesuai |
| --- | --- |
| Nama tempat sesuai nama yang dikenal warga atau tertulis di papan nama | Perbaiki ejaan; minta klarifikasi bila tempatnya tidak bisa dipastikan |
| Kategori benar: Kampus, Masjid, Halte, atau Rumah Sakit ([ADR-0004](../decisions/0004-empat-kategori-lokasi-untuk-mvp.md)) | Perbaiki kategori; tolak dengan R4 bila bukan fasilitas publik dalam kategori MVP |
| Pin berada di pintu masuk utama yang paling aksesibel, bukan di tengah gedung atau di jalan ([ADR-0015](../decisions/0015-input-lokasi-peta-gps-pencarian.md)) | Geser pin dan catat alasannya |
| Kecamatan terisi otomatis dan berada di Kota Bogor ([ADR-0010](../decisions/0010-cakupan-wilayah-kota-bogor.md)) | Tolak dengan R1 |
| Tidak ada lokasi lain dalam radius sekitar 50 m dengan nama mirip | Tolak dengan R2 dan sarankan "Laporkan kekurangan" pada lokasi yang sudah ada |
| Patokan (bila diisi) masuk akal dan tidak memuat data pribadi | Hapus bagian yang memuat data pribadi |

### 4.2 Fasilitas

Ukuran di bawah adalah perkiraan lapangan untuk menilai dari foto atau kunjungan singkat, bukan ukuran resmi. Untuk ketentuan teknis resmi, lihat Peraturan Menteri PUPR No. 14/PRT/M/2017 tentang Persyaratan Kemudahan Bangunan Gedung.

"Tersedia" berarti fasilitas bisa dipakai saat diverifikasi. Setiap jawaban Tidak tersedia diberi satu alasan sebelum diterbitkan ([ADR-0022](../decisions/0022-tersedia-berarti-bisa-dipakai.md)):

- **Tidak ada**: belum pernah dibangun.
- **Rusak**: ada tetapi rusak. Tulis sejak kapan di catatan bila diketahui.
- **Terhalang**: ada tetapi tertutup benda, misalnya parkir motor atau lapak.
- **Terbatas**: ada tetapi tidak untuk umum, misalnya lift khusus staf.
- **Tidak sesuai**: ada tetapi tidak memenuhi kriteria Tersedia di bawah, misalnya terlalu curam atau terlalu sempit.

Pelapor mungkin sudah memilih alasan; periksa dan perbaiki bila tidak cocok dengan bukti. Catatan per fasilitas (maksimal 200 karakter) tampil di halaman Detail, jadi tulis untuk pengunjung dan tanpa data pribadi.

#### Ramp / Jalur Landai (penentu pintu masuk)

| Jawaban | Gunakan bila |
| --- | --- |
| Tersedia | Ada jalur landai tanpa anak tangga dari jalan atau parkir sampai pintu masuk utama; cukup lebar untuk kursi roda (kira-kira 90 cm atau lebih); tidak curam sampai pengguna kursi roda harus didorong kuat; permukaan tidak licin; tidak terhalang secara permanen. |
| Tidak tersedia | Pintu masuk hanya bisa dicapai lewat tangga atau undakan; atau ramp ada tetapi terlalu curam, terlalu sempit, atau terhalang permanen (pot, tiang, parkir motor tetap) sehingga tidak bisa dipakai. |
| Tidak perlu | Jalan dari jalan atau parkir ke pintu masuk utama sudah rata, tanpa undakan (ambang pintu kira-kira tidak lebih dari 2 cm). |
| Belum tahu | Foto tidak memperlihatkan seluruh jalan menuju pintu masuk. |

Ingat: Ramp "Tidak tersedia" membuat status lokasi **Tidak aksesibel**, apa pun fasilitas lainnya ([ADR-0018](../decisions/0018-status-pintu-masuk-dulu-dan-ringkasan-fasilitas.md)).

#### Toilet Difabel

| Jawaban | Gunakan bila |
| --- | --- |
| Tersedia | Minimal satu toilet untuk umum yang muat kursi roda dan ada ruang untuk berputar di dalamnya, dengan pintu lebar, kloset duduk, pegangan di samping kloset, dan tanpa undakan di depannya. Toilet yang dikunci tetapi bisa dibuka atas permintaan tetap dihitung Tersedia, dengan catatan. |
| Tidak tersedia | Ada toilet umum, tetapi tidak satu pun bisa dipakai pengguna kursi roda (hanya kloset jongkok, pintu sempit, ada undakan, atau dipakai sebagai gudang). |
| Tidak perlu | Tempat tidak menyediakan toilet umum sama sekali, misalnya halte. |
| Belum tahu | Hanya ada foto papan "Toilet Difabel" tanpa foto bagian dalam. |

#### Guiding Block

| Jawaban | Gunakan bila |
| --- | --- |
| Tersedia | Ubin pemandu (bergaris untuk arah, berbintik untuk peringatan) tersambung dari trotoar atau gerbang sampai pintu masuk atau layanan utama, tidak terputus, dan tidak mengarah ke bahaya. |
| Tidak tersedia | Tidak ada ubin pemandu; atau ada tetapi terputus, tertutup, atau mengarah ke tiang, pohon, selokan, atau tangga tanpa peringatan sehingga menyesatkan. |
| Tidak perlu | Hampir tidak pernah dipakai. Eskalasikan ke koordinator sebelum menerima jawaban ini. |
| Belum tahu | Foto tidak memperlihatkan jalur dari trotoar atau gerbang. |

Bila guiding block mengarah ke bahaya, tandai Tidak tersedia dan beri tahu koordinator agar pengelola tempat bisa dihubungi.

#### Lift / Elevator

| Jawaban | Gunakan bila |
| --- | --- |
| Tersedia | Lift yang boleh dipakai pengunjung menjangkau semua lantai yang punya layanan publik, tombolnya terjangkau dari kursi roda, dan berfungsi saat diperiksa. |
| Tidak tersedia | Ada layanan publik di lantai atas tanpa lift atau ramp; lift hanya untuk staf; atau lift sedang rusak (tulis sejak kapan di catatan). |
| Tidak perlu | Semua layanan publik berada di lantai dasar, atau lantai atas terhubung dengan ramp. |
| Belum tahu | Tidak jelas apakah ada layanan publik di lantai atas. |

## 5. Bukti Foto

### 5.1 Foto Minimum

- Satu foto **pintu masuk utama** yang memperlihatkan jalan dari jalan atau parkir.
- Satu foto untuk **setiap** fasilitas yang dijawab Tersedia atau Tidak perlu.
- Bila foto tidak ada atau tidak cukup, minta klarifikasi atau pakai kunjungan lapangan atau konfirmasi pengelola. Catat sumber buktinya.

Setiap laporan memuat 0 sampai 10 foto, masing-masing bertag: Pintu masuk dan ramp, Toilet difabel, Guiding block, Lift, atau Lainnya ([ADR-0023](../decisions/0023-hingga-sepuluh-foto-bertag-per-laporan.md)). Perbaiki tag yang salah, dan tambahkan keterangan singkat bila teks alternatif bawaan belum cukup menjelaskan isi foto.

### 5.2 Foto yang Diterima dan yang Kurang

| Objek | Foto yang diterima | Foto yang kurang |
| --- | --- | --- |
| Pintu masuk dan ramp | Diambil dari ujung bawah ramp atau dari tepi jalan, memperlihatkan seluruh jalur sampai pintu; ada pembanding ukuran (pintu atau orang dari belakang) | Hanya potongan lantai ramp; diambil dari dalam gedung; gelap atau buram |
| Pintu masuk rata (Tidak perlu) | Diambil rendah dari luar, memperlihatkan ambang pintu dan lantai di kedua sisi | Diambil dari jauh sehingga undakan kecil tidak terlihat |
| Toilet difabel | Bagian dalam dari ambang pintu: kloset, pegangan, dan ruang gerak terlihat dalam satu foto | Hanya papan nama di pintu; foto saat toilet sedang dipakai (dilarang) |
| Guiding block | Diambil sambil berdiri di ujung awal, memperlihatkan arah jalur sampai pintu atau sampai terputus | Close-up satu ubin saja |
| Lift | Pintu lift beserta panel tombol, dan papan petunjuk lantai | Hanya papan "Lift" tanpa liftnya |

Tips untuk pelapor dan verifikator di lapangan: ambil satu foto jauh untuk konteks dan satu foto dekat untuk detail, pakai cahaya siang, posisi landscape, dan hindari wajah orang.

### 5.3 Contoh Foto Nyata

Repo ini belum berisi contoh foto. Contoh foto nyata akan ditambahkan setelah survei lapangan, dengan aturan:

- Disimpan di `operations/contoh-foto/` dengan nama `<fasilitas>-<diterima|kurang>-<nomor>.jpg`, misalnya `ramp-diterima-01.jpg`.
- Diambil sendiri oleh tim atau dengan izin pemilik foto, dengan wajah dan pelat nomor sudah dikaburkan dan metadata EXIF sudah dihapus.
- Setiap foto diberi keterangan satu kalimat tentang mengapa diterima atau kurang.

## 6. Keputusan

### 6.1 Terbitkan

Pilih **Terbitkan** bila informasi dasar benar dan setiap jawaban fasilitas punya bukti. Jawaban yang tidak bisa dibuktikan diubah menjadi **Belum tahu** sebelum diterbitkan, jangan ditolak seluruhnya ([ADR-0019](../decisions/0019-verifikasi-tim-dan-mitra-terlatih.md)). Status dan ringkasan "X dari Y fasilitas" dihitung otomatis setelah diterbitkan; kamu tidak memilih status secara manual.

### 6.2 Minta Klarifikasi

Pilih **Minta klarifikasi** bila satu informasi penting kurang dan pelapor kemungkinan bisa melengkapinya. Pelapor punya 7 hari untuk menjawab; setelah itu laporan otomatis ditolak dengan R6.

Contoh pesan:

- "Terima kasih atas laporannya. Bisakah Anda menambahkan foto pintu masuk utama yang memperlihatkan jalan dari area parkir? Kami perlu memastikan ramp-nya bisa dilalui kursi roda."
- "Terima kasih. Apakah toilet difabel di lantai 1 bisa dipakai pengunjung umum, atau hanya untuk staf?"
- "Pin lokasi berada di tengah jalan. Bisakah Anda memastikan pintu masuk mana yang paling mudah diakses?"

### 6.3 Tolak dengan Alasan Baku

| Kode | Alasan | Kapan dipakai | Pesan ke pelapor |
| --- | --- | --- | --- |
| R1 | Di luar Kota Bogor | Titik berada di luar enam kecamatan Kota Bogor ([ADR-0010](../decisions/0010-cakupan-wilayah-kota-bogor.md)) | "Saat ini AksesBogor hanya mencakup Kota Bogor, jadi laporan ini belum bisa kami terbitkan. Terima kasih sudah berkontribusi." |
| R2 | Duplikat | Tempat yang sama sudah ada | "Tempat ini sudah ada di AksesBogor. Bila ada informasi baru, gunakan 'Laporkan kekurangan' di halaman tempat tersebut." |
| R3 | Bukti tidak cukup | Tidak ada jawaban fasilitas yang bisa dibuktikan, bahkan setelah klarifikasi atau pengecekan lain | "Kami belum bisa memastikan kondisi tempat ini dari foto yang ada. Anda bisa mengirim ulang dengan foto pintu masuk dan fasilitasnya." |
| R4 | Bukan fasilitas publik | Bukan fasilitas publik, atau di luar kategori Kampus, Masjid, Halte, dan Rumah Sakit ([ADR-0004](../decisions/0004-empat-kategori-lokasi-untuk-mvp.md)) | "Untuk saat ini AksesBogor hanya mencatat kampus, masjid, halte, dan rumah sakit." |
| R5 | Konten tidak pantas | Spam, ujaran kebencian, iklan, atau data pribadi orang lain | "Laporan ini tidak sesuai dengan ketentuan AksesBogor." |
| R6 | Tidak ada klarifikasi | Pelapor tidak menjawab dalam 7 hari (otomatis) | "Kami belum menerima jawaban atas pertanyaan kami, jadi laporan ini ditutup. Anda bisa mengirim laporan baru kapan saja." |

## 7. Perubahan pada Lokasi yang Sudah Tayang

Laporan "Edit lokasi" dan "Laporkan kekurangan" masuk antrean yang sama. Data lama tetap tayang sampai perubahan diterbitkan.

- Bandingkan setiap jawaban baru dengan data saat ini dan tanggal verifikasi terakhir.
- Perubahan yang **memperburuk** status (misalnya lift rusak, ramp dibongkar) tetap butuh bukti, tetapi prioritaskan karena berdampak langsung pada keselamatan pengguna.
- Fasilitas yang rusak sementara ditandai **Tidak tersedia** dengan alasan Rusak dan catatan sejak kapan. Setelah diperbaiki, laporan baru mengembalikannya ke Tersedia.
- Lokasi yang terakhir diverifikasi lebih dari 12 bulan lalu ditandai perlu dicek ulang (FR-38).

## 8. Privasi dan Moderasi Foto

- Kaburkan wajah orang, pelat nomor, dan layar atau dokumen yang terbaca. Bila tidak bisa dikaburkan dengan baik, sembunyikan fotonya (FR-34).
- Sembunyikan foto yang memperlihatkan orang sebagai objek utama, anak-anak, atau bagian dalam toilet yang sedang dipakai.
- Metadata EXIF sudah dihapus otomatis saat unggah ([ADR-0013](../decisions/0013-tech-stack-nextjs-fastify.md)); jangan mengunggah ulang foto asli dari perangkatmu.
- Data pribadi yang tertulis di deskripsi atau patokan (nomor telepon, nama orang) dihapus sebelum diterbitkan.

## 9. Eskalasi ke Koordinator

Eskalasikan, alih-alih memutuskan sendiri, bila:

- bukti dari foto, pelapor, dan pengelola saling bertentangan;
- jawaban "Tidak perlu" untuk Guiding Block;
- pengelola tempat keberatan dengan data yang tayang;
- kamu menemukan bahaya, misalnya guiding block yang mengarah ke selokan terbuka;
- laporan tidak cocok dengan kasus mana pun di panduan ini.

Koordinator mencatat keputusan pada kasus baru, dan kasus yang berulang ditambahkan ke panduan ini lewat PR.

## 10. Lembar Periksa Singkat

Untuk dibawa saat kunjungan lapangan atau dipakai berdampingan dengan panel verifikator:

- [ ] Nama, kategori, dan pin di pintu masuk utama sudah benar
- [ ] Di dalam Kota Bogor dan bukan duplikat
- [ ] Ramp / Jalur Landai: jawaban sesuai bukti
- [ ] Toilet Difabel: jawaban sesuai bukti
- [ ] Guiding Block: jawaban sesuai bukti
- [ ] Lift / Elevator: jawaban sesuai bukti
- [ ] Jawaban tanpa bukti diubah menjadi Belum tahu
- [ ] Setiap Tidak tersedia punya alasan; catatan aman untuk ditampilkan
- [ ] Tag foto sesuai isinya
- [ ] Foto aman: tanpa wajah, pelat nomor, atau data pribadi
- [ ] Keputusan dipilih, dengan alasan baku bila menolak
- [ ] Catatan untuk verifikator lain ditulis

## 11. Hal yang Belum Diatur

- Tempat yang sudah tutup permanen belum punya alasan baku. Sementara itu, eskalasikan ke koordinator; bila perlu, ADR-0019 diperbarui.
- Ukuran teknis fasilitas (kemiringan ramp, lebar pintu) belum dicatat di MVP ([ADR-0022](../decisions/0022-tersedia-berarti-bisa-dipakai.md)).

## 12. Referensi

- [ADR-0004](../decisions/0004-empat-kategori-lokasi-untuk-mvp.md), [ADR-0010](../decisions/0010-cakupan-wilayah-kota-bogor.md), [ADR-0013](../decisions/0013-tech-stack-nextjs-fastify.md), [ADR-0015](../decisions/0015-input-lokasi-peta-gps-pencarian.md), [ADR-0017](../decisions/0017-empat-jawaban-ketersediaan-fasilitas.md), [ADR-0018](../decisions/0018-status-pintu-masuk-dulu-dan-ringkasan-fasilitas.md), [ADR-0019](../decisions/0019-verifikasi-tim-dan-mitra-terlatih.md), [ADR-0022](../decisions/0022-tersedia-berarti-bisa-dipakai.md), [ADR-0023](../decisions/0023-hingga-sepuluh-foto-bertag-per-laporan.md)
- [PRD](../product/prd.md), FR-30 sampai FR-38
- Peraturan Menteri PUPR No. 14/PRT/M/2017 tentang Persyaratan Kemudahan Bangunan Gedung
