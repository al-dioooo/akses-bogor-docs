# ADR-0009: Design System Kaca dengan Palet Ungu Inklusi

- Status: Accepted
- Tanggal: 2026-10-21
- Pengambil keputusan: Tim AksesBogor
- Issue: #11
- Men-supersede: [ADR-0007](0007-fondasi-visual-dari-desain-figma.md)

## Konteks dan Masalah

Halaman Design System di Figma masih kosong, dan [audit kontras](../design/fondasi-visual.md#riwayat-palet-desain-awal) menunjukkan palet desain awal gagal WCAG AA untuk warna status dan tombol utama. Tim ingin design system bergaya glassmorphism (permukaan kaca buram, tombol bercahaya, toggle lembut). Gaya kaca rawan kontras rendah karena latar di balik teks berubah-ubah, padahal AksesBogor adalah produk aksesibilitas. Palet apa yang dipakai, apa maknanya, dan bagaimana gaya kaca tetap aksesibel?

## Decision Drivers

- Semua teks lulus WCAG AA (4.5:1) di kasus terburuk: kaca 72% di atas gumpalan latar paling pekat.
- Ada pilihan tanpa transparansi untuk pengguna yang mengaktifkan `prefers-reduced-transparency` atau butuh kontras tinggi.
- Desain dan kode setara: efek harus bisa dibuat dengan CSS (`backdrop-filter`, `box-shadow`).
- Warna punya makna yang bisa dijelaskan ke warga dan mitra.

## Opsi yang Dipertimbangkan

1. **A · Biru Hujan**: biru (`#387AFF` sebagai glow). Makna: Bogor Kota Hujan, warna simbol akses kursi roda, rasa percaya.
2. **B · Ungu Inklusi**: ungu (`#6440E8` untuk tombol). Makna: warna gerakan hak penyandang disabilitas (#PurpleLightUp setiap 3 Desember), martabat, beda dari biru layanan pemerintah.
3. **C · Hijau Kebun Raya**: teal (`#0B7166`). Makna: Kebun Raya Bogor; bentrok dengan hijau = Aksesibel.

## Keputusan

Opsi terpilih: **B · Ungu Inklusi**, dengan tiga mode warna dan komponen inti. Design system dibangun di halaman Design System Figma pada 2026-10-21.

- **Mode**: satu koleksi warna semantik dengan mode Glass (default), Solid (opak), dan Dark (kaca gelap).
- **Efek kaca**: background blur + bayangan dan highlight, bukan efek "Glass" bawaan Figma, karena efek bawaan tidak bisa diikat ke variable dan tidak punya padanan CSS.
- **Status**: selalu warna + ikon + label. Teks status memakai tingkat 800; warna terang hanya untuk marker dan glow.
- **Teks sekunder**: ink-600 (`#4B5066`) menggantikan `#6A6E81`, yang hanya 3.8:1 di kaca.
- **Fokus**: cincin 2 px celah + 2 px ungu, terlihat di semua gaya tombol.
- **Latar aurora** membawa makna Bogor: ungu (inklusi), biru langit (Kota Hujan), mint (Kebun Raya).
- **Cakupan v1**: 141 variable, 12 text style, 9 effect style, 14 ikon, dan komponen Button, Toggle, Checkbox, Input, Status badge, Map marker, Glass card, Nav item, dan Nav bar. Saat migrasi layar (hari yang sama) bertambah 2 token permukaan inverse, 11 ikon, dan 19 komponen pola.

Rincian nilai ada di [Fondasi Visual](../design/fondasi-visual.md).

## Konsekuensi

- Positif: identitas visual punya makna yang jelas dan selaras dengan isu disabilitas.
- Positif: kontras AA terverifikasi untuk semua token teks di ketiga mode.
- Positif: token dan komponen siap dipakai untuk mendesain ulang layar dan untuk kode (setiap variable punya nama CSS).
- Positif: delapan layar sudah dibangun ulang sebagai frame "DS v1" dari komponen dan token ini (lihat [Referensi Figma](../product/referensi-figma.md#frame-ds-v1)).
- Negatif: frame asli dengan palet biru masih ada di halaman Main sebagai referensi dan perlu diarsipkan setelah frame DS v1 disetujui.
- Negatif: belum ada mode "Dark Solid". Pengguna mode gelap yang butuh tanpa transparansi jatuh ke mode Solid terang.
- Negatif: `backdrop-filter` berat di ponsel kelas bawah; mode Solid juga perlu dipakai sebagai fallback performa.
- Netral: ungu kurang terasa "resmi" dibanding biru; dikompensasi dengan copy yang jelas dan data yang terverifikasi.

## Pro & Kontra tiap Opsi

### A · Biru Hujan

- Pro: paling dekat dengan desain awal; makna lokal (Kota Hujan) dan simbol akses kursi roda.
- Kontra: mirip banyak layanan pemerintah, identitas kurang khas.

### B · Ungu Inklusi

- Pro: makna paling kuat untuk produk disabilitas; identitas khas; kontras tombol paling tinggi (6.1:1).
- Kontra: semua layar perlu diwarnai ulang.

### C · Hijau Kebun Raya

- Pro: makna lokal paling kuat.
- Kontra: bentrok dengan status Aksesibel, sehingga warna status harus pindah dari konvensi yang sudah dipahami.

## Referensi

- [Figma: halaman Design System](https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=1-2)
- [Fondasi Visual](../design/fondasi-visual.md)
- [ADR-0007](0007-fondasi-visual-dari-desain-figma.md) (di-supersede)
- [Pertanyaan Terbuka PT-05](../product/pertanyaan-terbuka.md#pt-05-target-aksesibilitas-antarmuka)
