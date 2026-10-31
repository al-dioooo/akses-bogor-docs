# ADR-0004: Empat Kategori Lokasi untuk MVP

- Status: Accepted
- Tanggal: 2026-10-21
- Pengambil keputusan: Tim AksesBogor
- Issue: #5

> Dicatat retroaktif dari desain Figma. Opsi alternatif di bawah adalah analisis penulis, bukan catatan diskusi tim.

## Konteks dan Masalah

Desain memakai empat kategori lokasi di tiga tempat: kartu "Jelajahi Berdasarkan Kategori" di Landing, filter Kategori di Explore, dan pilihan Kategori (wajib) di langkah 1 formulir laporan. Kartu Explore dan header Detail menampilkan satu badge kategori, dan formulir hanya mengizinkan satu pilihan, sehingga setiap lokasi memiliki tepat satu kategori. Kategori apa saja yang masuk MVP?

## Decision Drivers

- Cakupan MVP kecil agar pengumpulan dan verifikasi data realistis.
- Konsisten di semua layar (Landing, Explore, formulir).
- Mudah dipahami pelapor awam.

## Opsi yang Dipertimbangkan

1. Empat kategori tetap: Kampus, Masjid, Halte, Rumah Sakit
2. Daftar kategori lebih luas (mis. kantor pelayanan publik, pusat perbelanjaan, taman, tempat ibadah lain)
3. Kategori bebas diisi pelapor

## Keputusan

Opsi terpilih: "Empat kategori tetap: Kampus, Masjid, Halte, Rumah Sakit", sesuai desain. Setiap lokasi memiliki tepat satu kategori.

## Konsekuensi

- Positif: filter dan formulir sederhana; data mudah dibandingkan antar lokasi.
- Negatif: tempat ibadah selain masjid (gereja, pura, vihara, klenteng) dan fasilitas umum lain belum tercakup. Ini perlu dibahas sebelum peluncuran publik.
- Negatif: tempat dengan beberapa fungsi (mis. masjid di dalam kampus) harus memilih satu kategori atau dicatat sebagai dua lokasi.
- Negatif: menambah kategori butuh ADR baru yang men-supersede ADR ini. Usulan implementasi: simpan daftar kategori sebagai data, bukan nilai tetap di kode, agar penambahan tidak memerlukan perubahan skema.

## Pro & Kontra tiap Opsi

### Empat kategori tetap

- Pro: fokus; sesuai desain yang sudah ada.
- Kontra: cakupan terbatas dan belum mewakili semua tempat ibadah.

### Daftar kategori lebih luas

- Pro: lebih banyak kebutuhan pengguna terjawab.
- Kontra: data per kategori lebih tipis di awal; desain Landing dan formulir perlu diubah.

### Kategori bebas

- Pro: fleksibel untuk pelapor.
- Kontra: data tidak konsisten (ejaan dan sinonim berbeda) dan filter tidak bisa diandalkan.

## Referensi

- [Landing (Figma 13:2)](https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=13-2)
- [Explore (Figma 13:528)](https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=13-528)
- [Laporkan langkah 1 (Figma 13:812)](https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=13-812)
- [Ringkasan Produk](../product/ringkasan-produk.md)
