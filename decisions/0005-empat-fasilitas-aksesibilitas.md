# ADR-0005: Empat Fasilitas Aksesibilitas Dicatat sebagai Tersedia atau Tidak

- Status: Accepted
- Tanggal: 2026-10-21
- Pengambil keputusan: Tim AksesBogor
- Issue: #5

> Dicatat retroaktif dari desain Figma. Opsi alternatif di bawah adalah analisis penulis, bukan catatan diskusi tim.

## Konteks dan Masalah

Langkah 2 formulir laporan meminta pelapor memilih fasilitas yang tersedia dari empat pilihan:

| Fasilitas | Penjelasan di formulir |
| --- | --- |
| Ramp / Jalur Landai | Jalur miring untuk kursi roda |
| Toilet Difabel | Toilet dengan akses kursi roda |
| Guiding Block | Ubin pengarah untuk tunanetra |
| Lift / Elevator | Lift dengan akses difabel |

Halaman Detail menampilkan keempat fasilitas dengan tanda ✓ (tersedia) atau ✗ (tidak tersedia), dan Explore memakai daftar yang sama sebagai filter. Fasilitas apa yang dicatat, dan dengan tingkat detail seperti apa?

## Decision Drivers

- Mudah diisi warga awam dalam waktu singkat.
- Mudah diverifikasi dari foto atau kunjungan singkat.
- Mencakup kebutuhan utama pengguna kursi roda dan tunanetra.

## Opsi yang Dipertimbangkan

1. Empat fasilitas, masing-masing bernilai tersedia atau tidak
2. Empat fasilitas ditambah atribut kondisi (baik, rusak, terhalang)
3. Daftar fasilitas lebih lengkap (mis. parkir difabel, pintu lebar, signage braille, informasi visual untuk Tuli, kursi roda pinjaman)

## Keputusan

Opsi terpilih: "Empat fasilitas, masing-masing bernilai tersedia atau tidak", sesuai desain. Nama yang dipakai di seluruh produk mengikuti formulir: "Ramp / Jalur Landai", "Toilet Difabel", "Guiding Block", "Lift / Elevator".

## Konsekuensi

- Positif: formulir cepat diisi; filter Explore sederhana.
- Negatif: kebutuhan ragam disabilitas lain, seperti Tuli dan disabilitas intelektual, belum terwakili.
- Negatif: checkbox yang tidak dicentang bisa berarti "tidak tersedia" atau "pelapor tidak tahu". Detail menampilkan keduanya sebagai ✗, padahal maknanya berbeda. Perlu status ketiga "belum diketahui" atau aturan verifikasi yang jelas.
- Negatif: kondisi fasilitas (mis. guiding block rusak) hanya bisa dicatat di deskripsi bebas. Lihat [PT-08](../product/pertanyaan-terbuka.md#pt-08-kondisi-fasilitas).
- Tindak lanjut: Landing menyebut "Lift & eskalator"; teks itu perlu diganti "Lift / Elevator" karena eskalator tidak bisa dipakai pengguna kursi roda.

## Pro & Kontra tiap Opsi

### Tersedia atau tidak

- Pro: paling cepat diisi dan diverifikasi.
- Kontra: tidak menangkap kondisi atau kualitas fasilitas.

### Dengan atribut kondisi

- Pro: informasi lebih berguna bagi pengguna ("ada ramp, tapi terhalang").
- Kontra: formulir lebih panjang; kondisi cepat berubah sehingga data cepat usang.

### Daftar lebih lengkap

- Pro: mewakili lebih banyak ragam disabilitas.
- Kontra: formulir panjang; pelapor awam mungkin tidak bisa menilai semua fasilitas.

## Referensi

- [Laporkan langkah 2 (Figma 13:1002)](https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=13-1002)
- [Detail (Figma 13:1578)](https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor?node-id=13-1578)
- [Pertanyaan Terbuka PT-08](../product/pertanyaan-terbuka.md#pt-08-kondisi-fasilitas)
