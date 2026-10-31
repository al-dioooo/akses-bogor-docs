# ADR-0027: Design System Datar dengan Ilustrasi Lingkungan 3D

- Status: Accepted
- Tanggal: 2026-10-31
- Pengambil keputusan: Tim AksesBogor
- Issue: #46
- Men-supersede: [ADR-0009](0009-design-system-kaca-ungu-inklusi.md)

## Konteks dan Masalah

[ADR-0009](0009-design-system-kaca-ungu-inklusi.md) memakai kaca buram di atas latar aurora. Setelah Beranda, Jelajahi, dan Detail dibangun, hampir semua permukaan menjadi kartu kaca yang sama. Hierarki hilang, kontras teks bergantung pada warna latar yang bergeser di baliknya, dan halaman terasa seperti templat: label pil, judul di tengah, lalu deretan kartu yang setara. Tim meminta tampilan yang terasa seperti [deliverologi.id](https://deliverologi.id) dan [kamadigital.com](https://kamadigital.com), dengan ilustrasi lingkungan 3D seperti [aksesin](https://aksesin.onefatir.dev). Gaya visual apa yang menggantikan kaca, dan bagaimana ilustrasi 3D tetap aksesibel?

## Decision Drivers

- Kontras WCAG 2.2 AA ([ADR-0021](0021-wcag-22-aa-dan-pengujian-berlapis.md)) yang tidak bergantung pada latar di balik teks.
- Hierarki yang jelas: judul besar, bagian selebar layar, satu warna utama.
- Visual yang menjelaskan cara kerja produk, bukan hiasan.
- Satu keluarga dengan aksesin, termasuk pilihan untuk menghentikan gerak.
- Makna Ungu Inklusi dan aturan status dari ADR-0009 tetap berlaku.
- Tanpa dependensi besar untuk 3D, dan tetap ringan di ponsel kelas bawah.

## Opsi yang Dipertimbangkan

1. **A · Kaca dirapikan**: pertahankan kaca dan aurora, perbaiki tata letak saja.
2. **B · Datar dengan ilustrasi lingkungan 3D**: permukaan opak, tipografi besar, pita penuh, dan hero 3D "Halte ke pintu".
3. **C · Datar tanpa ilustrasi**: seperti B, tetapi hanya tipografi dan data.

## Keputusan

Opsi terpilih: **B · Datar dengan ilustrasi lingkungan 3D**. Ilustrasinya menunjukkan rute kunjungan, dan empat fasilitas menjadi empat titik di rute itu. Pola ini dipakai lagi di Beranda dan Detail.

- **Material**: tanpa kaca, blur, aurora, dan tombol berkilap. Latar `#F9F9F7`, permukaan putih dengan garis tipis, pita gelap untuk angka, dan ungu sebagai warna aksi.
- **Mode warna**: Terang (bawaan) dan Gelap. Mode Kaca dan Solid dihapus, sehingga `prefers-reduced-transparency` tidak perlu ditangani lagi.
- **Warna**: Ungu Inklusi tetap: violet-600 untuk tombol, violet-700 untuk teks brand. Warna status, ikon, dan label tidak berubah. Kuning guiding block (`#F2C230`) hanya dipakai di ilustrasi dan di angka sorotan pada pita gelap.
- **Tipografi**: Bricolage Grotesque untuk judul, Inter untuk isi, dan IBM Plex Mono untuk label bernomor. Instrument Serif miring dipakai untuk satu kata aksen di judul. Plus Jakarta Sans tidak dipakai lagi.
- **Bentuk**: tombol pil dengan tinggi minimal 44 px, kartu bersudut 20–32 px, dan judul bagian dua kolom (judul di kiri, pengantar di kanan).
- **Ilustrasi**: satu gambar lingkungan 3D dan empat close-up fasilitas, dibuat dengan `gpt-image-2` lewat Codex CLI. Prompt setiap gambar dicatat di repo web. Gambar disimpan sebagai WebP.
- **Lapisan 3D**: WebGL tanpa library. Gambar sedikit bergeser mengikuti pointer, dan bagian depan bergeser lebih jauh daripada latar belakang. Hujan tipis turun di sisi gambar, dan pin bernomor menempel di keempat fasilitas. Semuanya berhenti bila perangkat meminta gerak dikurangi atau pengguna memilih "Kurangi gerakan". Bila WebGL tidak tersedia, gambar statis yang tampil. Ilustrasi dan pin bersifat dekoratif; informasinya tertulis di teks halaman.
- **Sumber token**: snapshot `design-tokens/figma-variables.json` di akses-bogor-web diubah langsung di repo. Variable di Figma diperbarui menyusul, dan selama itu snapshot di repo yang dipakai.

Rincian nilai ada di [Fondasi Visual](../design/fondasi-visual.md).

## Konsekuensi

- Positif: kontras teks tidak lagi bergantung pada latar. Semua pasangan teks lulus AA; yang terendah adalah teks sekunder di tint ungu (6.5:1).
- Positif: tanpa `backdrop-filter`, sehingga lebih ringan di ponsel.
- Positif: ilustrasi menjelaskan keempat fasilitas sebelum orang membaca daftar.
- Negatif: Figma tertinggal dari kode sampai variable dan frame DS v1 diperbarui.
- Negatif: gambar buatan AI bisa berbeda detail dari kenyataan. Gambar ini ilustrasi, bukan foto lokasi, dan tidak boleh dipakai sebagai bukti kondisi tempat mana pun.
- Negatif: hero menambah unduhan sekitar 300 KB di Beranda. Close-up dimuat malas (lazy) di bawah layar pertama.
- Netral: empat keluarga font dimuat lewat `next/font`, sehingga di-host sendiri dan hanya subset latin.

## Pro & Kontra tiap Opsi

### A · Kaca dirapikan

- Pro: perubahan paling kecil; token dan Figma tetap.
- Kontra: masalah kontras dan permukaan yang seragam tetap ada; tidak menjawab permintaan tim.

### B · Datar dengan ilustrasi lingkungan 3D

- Pro: kontras stabil, hierarki jelas, dan identitasnya khas Bogor (angkot, halte, Kebun Raya, hujan).
- Kontra: perlu aset gambar dan kode WebGL; Figma harus menyusul.

### C · Datar tanpa ilustrasi

- Pro: paling ringan dan paling cepat dibangun.
- Kontra: tidak ada yang menunjukkan fasilitas secara visual; terasa kering untuk halaman depan.

## Referensi

- [ADR-0009](0009-design-system-kaca-ungu-inklusi.md) (di-supersede)
- [ADR-0020](0020-antarmuka-bahasa-indonesia-siap-i18n.md), [ADR-0021](0021-wcag-22-aa-dan-pengujian-berlapis.md)
- [Fondasi Visual](../design/fondasi-visual.md)
- Issue #46
