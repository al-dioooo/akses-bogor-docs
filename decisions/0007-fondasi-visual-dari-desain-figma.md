# ADR-0007: Fondasi Visual Mengikuti Desain Figma, Kecuali Warna Status

- Status: Accepted
- Tanggal: 2026-10-21
- Pengambil keputusan: Tim AksesBogor
- Issue: #5

> Dicatat retroaktif dari desain Figma. Opsi alternatif di bawah adalah analisis penulis, bukan catatan diskusi tim.

## Konteks dan Masalah

Desain Figma memakai tipografi, warna, radius, dan layout yang konsisten di delapan layar, tetapi file Figma belum punya variable, style, atau komponen, dan halaman Design System kosong. Nilai-nilainya sudah diekstrak ke [Fondasi Visual](../design/fondasi-visual.md). Audit kontras di dokumen itu menunjukkan semua warna status (Accessible, Partial, tidak tersedia) dan teks putih di tombol utama gagal WCAG AA. Bagian mana dari fondasi visual ini yang dijadikan acuan?

## Decision Drivers

- Implementasi butuh acuan visual yang tetap.
- AksesBogor adalah produk aksesibilitas; antarmukanya harus bisa dipakai pengguna low vision dan buta warna.
- Hindari mendesain ulang bagian yang tidak bermasalah.

## Opsi yang Dipertimbangkan

1. Pakai fondasi visual dari desain apa adanya
2. Pakai tipografi, bentuk, layout, dan warna dasar dari desain; warna status dan kontras tombol utama ditentukan terpisah
3. Ganti dengan design system siap pakai (mis. Material Design atau tema bawaan library UI)

## Keputusan

Opsi terpilih: opsi 2. Yang menjadi acuan:

- **Tipografi**: Plus Jakarta Sans untuk heading, Inter untuk teks dan UI, dengan skala sesuai [Fondasi Visual](../design/fondasi-visual.md#tipografi).
- **Bentuk dan layout**: radius kartu 20 px, tombol dan input 16 px, badge pill, kontainer maksimum 1280 px.
- **Warna dasar**: hue primer biru (`#387AFF`), teks `#1E2029` dan `#6A6E81`, latar `#F0F3FF`, border `#DEE3ED`.

Yang **tidak** termasuk keputusan ini: nilai warna status dan kombinasi teks di atas warna primer. Keduanya menunggu target aksesibilitas di [PT-05](../product/pertanyaan-terbuka.md#pt-05-target-aksesibilitas-antarmuka).

## Konsekuensi

- Positif: implementasi bisa dimulai dengan acuan tipografi dan layout yang jelas.
- Positif: bagian yang gagal kontras tidak terkunci sebagai keputusan.
- Negatif: palet status harus didesain ulang. Status juga perlu penanda selain warna (ikon atau teks) pada badge dan marker peta.
- Tindak lanjut: formalkan nilai di atas sebagai variable Figma dan design token di kode, agar desain dan kode memakai sumber yang sama.

## Pro & Kontra tiap Opsi

### Apa adanya

- Pro: tidak ada pekerjaan desain tambahan.
- Kontra: produk aksesibilitas meluncur dengan kontras yang gagal WCAG AA.

### Sebagian, warna status ditentukan terpisah

- Pro: mempertahankan identitas visual sambil memperbaiki bagian yang bermasalah.
- Kontra: sebagian fondasi masih menunggu keputusan lain.

### Design system siap pakai

- Pro: komponen dan aksesibilitas sudah teruji.
- Kontra: membuang identitas visual yang sudah ada; tetap perlu kustomisasi.

## Referensi

- [Fondasi Visual](../design/fondasi-visual.md)
- [Referensi Figma](../product/referensi-figma.md)
- [Pertanyaan Terbuka PT-05](../product/pertanyaan-terbuka.md#pt-05-target-aksesibilitas-antarmuka)
