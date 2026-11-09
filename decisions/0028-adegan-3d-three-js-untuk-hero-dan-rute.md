# ADR-0028: Adegan 3D three.js untuk Hero dan Rute ke Pintu

- Status: Accepted
- Tanggal: 2026-11-01
- Pengambil keputusan: Tim AksesBogor
- Issue: #48
- Men-supersede sebagian: [ADR-0027](0027-design-system-datar-dan-ilustrasi-3d.md) (ilustrasi dan lapisan 3D)

## Konteks dan Masalah

[ADR-0027](0027-design-system-datar-dan-ilustrasi-3d.md) memilih hero "Halte ke pintu" berupa gambar buatan `gpt-image-2` dengan lapisan WebGL tanpa library: gambar digeser mengikuti pointer, hujan tipis, dan pin bernomor. Setelah dipakai, tim meminta ilustrasinya dibuat sebagai 3D sungguhan: adegan yang dirender di browser, dengan kamera yang bisa berpindah dan benda yang bergerak, bukan gambar datar yang digeser. Empat close-up fasilitas juga harus berasal dari adegan yang sama supaya konsisten. Bagaimana adegan itu dibuat, dengan library apa, dan bagaimana ia tetap aksesibel dan ringan?

## Decision Drivers

- Ilustrasi yang benar-benar 3D: kamera berpindah ke tiap fasilitas, benda bergerak, dan mode Gelap bisa menjadi malam.
- Satu sumber untuk hero, bagian "Rute ke pintu", dan kartu fasilitas Detail.
- Aksesibilitas ([ADR-0021](0021-wcag-22-aa-dan-pengujian-berlapis.md)): gerak bisa dihentikan, dan tanpa WebGL tetap ada gambar.
- Beranda tetap cepat: 3D tidak boleh menunda teks, pencarian, dan kategori.
- Bisa diubah oleh tim lewat review kode, tanpa alat 3D khusus dan tanpa aset biner besar di repo.
- Tidak bergantung pada layanan AI untuk membuat ulang gambar.

## Opsi yang Dipertimbangkan

1. **A · Gambar AI dengan paralaks**: tetap seperti ADR-0027.
2. **B · Adegan dari kode dengan three.js**: bentuk dasar disusun di TypeScript, dimuat malas; poster dan close-up dirender dari adegan yang sama.
3. **C · Model glTF dari Blender atau Spline**: adegan dibuat di alat 3D lalu dimuat dengan three.js.
4. **D · Adegan 3D tanpa library**: material, bayangan, dan kamera ditulis sendiri di WebGL.

## Keputusan

Opsi terpilih: **B · Adegan dari kode dengan three.js**. Tim memilih tata letak lanskap terbuka setelah melihat pratinjau diorama melayang dan lanskap.

- **Adegan**: "Halte ke pintu" dibangun dari kotak, kapsul, ikosahedron, dan ekstrusi di `src/components/beranda/scene/` (repo web), satuan meter, tanpa berkas model dan tanpa tekstur gambar; piktogram akses dan bus digambar ke canvas. Palet mengikuti [Fondasi Visual](../design/fondasi-visual.md). Jalan dan rumput sampai tepi gambar, kabut menyatu dengan latar halaman, dan bukit di kejauhan. Sisi kiri tetap tenang untuk judul.
- **Cerita**: siklus 32 detik. Angkot ngetem di halte, pejalan bertongkat putih turun lalu mengikuti guiding block menyeberang zebra cross sampai pintu, pengguna kursi roda naik ramp, lift naik turun, pohon bergoyang, dan hujan turun di sisi kanan.
- **Library**: three.js (MIT) sebagai dependensi, dimuat lewat `import()` setelah Beranda tampil, hanya kelas yang dipakai. Ini mengganti aturan "WebGL tanpa library" di ADR-0027.
- **Poster**: hero menampilkan poster WebP yang dirender dari adegan yang sama (versi Terang dan Gelap) sampai three.js siap, dan seterusnya bila gerak dikurangi atau WebGL tidak tersedia; pada dua keadaan itu three.js tidak dimuat. Pin 01–04 diproyeksikan dari titik 3D saat adegan hidup, dan dari koordinat di poster saat diam.
- **Rute ke pintu**: di layar lebar satu kanvas lengket, dan kamera berpindah ke empat titik (guiding block, ramp, toilet difabel, lift) mengikuti bagian yang sedang dibaca. Di ponsel, saat gerak dikurangi, dan tanpa WebGL tampil render diam dari titik yang sama. Kartu fasilitas Detail memakai render yang sama.
- **Mode Gelap**: versi malam, dengan lampu jalan, jendela, halte, dan kabin lift yang menyala.
- **Render ulang**: skrip di repo web merender poster dan close-up dengan Chromium headless lewat Playwright. Gambar tidak lagi dibuat dengan AI, dan catatan prompt dihapus.
- **Batas gerak dan kinerja**: sama dengan ADR-0027, gerak berhenti saat gerak dikurangi, hero tidak terlihat, tab tidak aktif, atau tombol hentikan ditekan. Geometri statis digabung per material, peta bayangan 2048 px, dan rasio piksel dibatasi 1,5.

## Konsekuensi

- Positif: ilustrasi benar-benar 3D. Kamera dan gerak menjelaskan rute, mode Gelap punya versi malam, dan hero, rute, serta Detail konsisten dari satu sumber.
- Positif: semua ilustrasi berasal dari kode di repo. Perubahan adegan direview seperti kode lain, dan tidak ada ketergantungan pada layanan AI.
- Positif: unduhan awal Beranda tidak bertambah. Poster seukuran gambar sebelumnya, dan three.js baru dimuat setelah halaman tampil.
- Negatif: three.js dan adegan menambah sekitar 155 KB (gzip) bagi pengunjung dengan gerak aktif dan WebGL. Ukurannya dicatat di PR dan diperiksa lagi tiap kali adegan berubah.
- Negatif: gaya "clay" prosedural. Orang dan kendaraan sederhana, dan detailnya tidak sehalus gambar AI.
- Negatif: GPU ponsel kelas bawah bisa terbebani. Batas rasio piksel dan peta bayangan harus dijaga, dan tombol hentikan tetap ada.
- Netral: ilustrasi dan pin tetap dekoratif; informasinya tertulis di teks halaman.

## Pro & Kontra tiap Opsi

### A · Gambar AI dengan paralaks

- Pro: sudah ada dan ringan.
- Kontra: bukan 3D sungguhan. Close-up tidak konsisten dengan hero, dan gambar sulit diubah tanpa membuat ulang dengan AI.

### B · Adegan dari kode dengan three.js

- Pro: 3D sungguhan dari kode yang bisa direview, konsisten di semua halaman, dan poster dari sumber yang sama.
- Kontra: dependensi three.js, lebih banyak kode, dan gaya yang sederhana.

### C · Model glTF dari Blender atau Spline

- Pro: detail dan material lebih halus.
- Kontra: butuh alat 3D untuk setiap perubahan, aset biner besar di repo, dan unduhan lebih besar.

### D · Adegan 3D tanpa library

- Pro: tanpa dependensi.
- Kontra: material, bayangan, dan kamera harus ditulis sendiri, ribuan baris yang sulit dirawat.

## Referensi

- [ADR-0027](0027-design-system-datar-dan-ilustrasi-3d.md) (di-supersede sebagian), [ADR-0021](0021-wcag-22-aa-dan-pengujian-berlapis.md)
- [Fondasi Visual](../design/fondasi-visual.md)
- Issue #48, akses-bogor-web#37
- [three.js](https://threejs.org)
