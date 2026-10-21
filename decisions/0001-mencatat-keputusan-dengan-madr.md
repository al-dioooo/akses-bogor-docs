# ADR-0001: Mencatat Keputusan Proyek dengan ADR Format MADR

- Status: Accepted
- Tanggal: 2026-10-21
- Pengambil keputusan: Tim AksesBogor
- Issue: #2

## Konteks dan Masalah

Keputusan proyek AksesBogor (cakupan produk, desain, pilihan teknis, proses kerja) saat ini tersebar di Figma, percakapan, dan dokumen terpisah. Alasan di balik keputusan mudah hilang, dan anggota tim baru tidak punya satu tempat untuk memahami mengapa sesuatu dibuat seperti sekarang. Guideline repositori tim (bagian 4) juga mewajibkan *Decision log (ADR)* sebagai dokumentasi tambahan.

## Decision Drivers

- Diwajibkan guideline repositori tim.
- Opsi yang ditolak beserta alasannya ikut tercatat.
- Mudah ditulis dan di-review lewat pull request.
- Bisa dibaca langsung di GitHub tanpa tool tambahan.

## Opsi yang Dipertimbangkan

1. MADR (Markdown Any Decision Records)
2. Format Nygard (Title, Status, Context, Decision, Consequences)
3. Y-statement dalam satu tabel log
4. Tanpa format khusus (catatan bebas di wiki atau dokumen lain)

## Keputusan

Opsi terpilih: "MADR", karena format ini mencatat opsi yang dipertimbangkan beserta pro dan kontranya, sehingga diskusi tidak perlu diulang ketika keputusan dipertanyakan kembali.

Aturan penerapan:

- Satu keputusan per file di `decisions/`, bernama `NNNN-judul-singkat.md`.
- Heading mengikuti [template](template.md) dalam bahasa Indonesia; "Decision Drivers" tetap dalam bahasa Inggris sebagai istilah teknis.
- ADR mencakup semua jenis keputusan (produk, desain, teknis, proses), bukan hanya arsitektur.
- Keputusan yang sudah tercermin di desain tetapi belum tercatat ditulis secara retroaktif dan ditandai demikian.

## Konsekuensi

- Positif: alasan dan alternatif setiap keputusan bisa dilacak; review keputusan terjadi di PR seperti review kode.
- Negatif: menulis ADR format MADR lebih lama dibanding format Nygard; index di [decisions/README.md](README.md) harus dijaga secara manual.

## Pro & Kontra tiap Opsi

### MADR

- Pro: mencatat opsi yang ditolak beserta alasannya; template terstruktur dan banyak dipakai.
- Kontra: dokumen lebih panjang.

### Format Nygard

- Pro: singkat dan cepat ditulis.
- Kontra: tidak ada tempat khusus untuk opsi alternatif, sehingga mudah terlewat.

### Y-statement

- Pro: sangat ringkas; satu tabel mudah dipindai.
- Kontra: konteks dan konsekuensi terlalu padat untuk keputusan produk yang kompleks.

### Tanpa format khusus

- Pro: tanpa overhead.
- Kontra: tidak memenuhi guideline; isi tidak konsisten dan sulit dicari.

## Referensi

- [Github Repository Guideline](../guidelines/github-repository-guideline.md), bagian 4 "Documentation Standard"
- <https://adr.github.io/madr/>
