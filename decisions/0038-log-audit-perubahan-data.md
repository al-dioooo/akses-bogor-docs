# ADR-0038: Log Audit Mencatat Perubahan Data tanpa Data Pribadi

- Status: Accepted
- Tanggal: 2026-11-08
- Pengambil keputusan: Tim AksesBogor
- Issue: #58

## Konteks dan Masalah

FR-35 meminta setiap perubahan data lokasi tercatat di log audit: siapa, kapan, dan apa. Tabel `audit_log` sudah ada sejak skema awal, dengan pengubah yang dikosongkan saat akunnya dihapus ([ADR-0024](0024-data-setelah-akun-dihapus.md)), tetapi isinya belum ditentukan. Apa yang dicatat, apa yang tidak, dan berapa lama?

## Decision Drivers

- Tim bisa menelusuri siapa mengubah data lokasi dan mengapa, mis. saat pengelola tempat keberatan ([Panduan Verifikasi](../operations/panduan-verifikasi.md), bagian 9).
- Log tidak menjadi salinan kedua data pribadi yang lolos dari penghapusan akun.
- Tidak ada layanan atau penyimpanan baru.

## Opsi yang Dipertimbangkan

1. Satu baris per tindakan, dengan perubahan sebelum dan sesudah untuk data lokasi, tanpa data pribadi
2. Salinan penuh laporan dan lokasi di setiap tindakan
3. Hanya log server (journald), tanpa tabel

## Keputusan

Opsi terpilih: "Satu baris per tindakan, tanpa data pribadi".

### Yang dicatat

| Entitas | Tindakan | Isi `changes` |
| --- | --- | --- |
| `lokasi` | `dibuat`, `diubah` | Kolom yang berubah, nilai sebelum dan sesudah (nama, kategori, kecamatan, titik, patokan, deskripsi, status, jawaban fasilitas), dan id laporan asal |
| `laporan` | `diterbitkan`, `klarifikasi_diminta`, `klarifikasi_dijawab`, `ditolak` | Status lama dan baru, kode alasan, id lokasi |
| `foto` | `dimoderasi`, `diterbitkan`, `dihapus` | Tag, disembunyikan atau tidak, jumlah area kabur, id laporan |
| `pengguna` | `peran_diubah` | Peran lama dan baru |

- Setiap baris berisi entitas, id, pengubah (id akun), waktu, tindakan, dan `changes`.
- Tindakan otomatis, seperti penolakan R6, memakai label pengubah `sistem`. Perubahan lewat skrip `pnpm peran` memakai label `skrip`.

### Yang tidak dicatat

- Email, nama tampilan pelapor, teks pertanyaan dan jawaban klarifikasi, catatan internal verifikator, dan isi foto.
- Data lokasi yang dicatat adalah data yang memang tayang untuk publik.

### Akses dan masa simpan

- Log disimpan tanpa batas waktu. Bila akun pengubah dihapus, pengubah menjadi kosong dan ditampilkan "akun dihapus" atau "verifikator dihapus" ([ADR-0024](0024-data-setelah-akun-dihapus.md)).
- Belum ada halaman untuk membaca log. Tim membacanya lewat database di server sampai halaman riwayat dibutuhkan.
- Baris log ditulis di transaksi yang sama dengan perubahannya, jadi perubahan tanpa log tidak pernah tersimpan.

## Konsekuensi

- Positif: setiap perubahan data lokasi bisa ditelusuri ke keputusan verifikator dan laporannya.
- Positif: menghapus akun tidak meninggalkan data pribadi di log.
- Negatif: alasan di balik keputusan hanya ada di catatan internal laporan, bukan di log.
- Negatif: tanpa halaman riwayat, hanya orang dengan akses database yang bisa membaca log.
- Negatif: log tumbuh terus. Dengan perkiraan ribuan laporan per tahun, ukurannya tetap kecil (beberapa MB).

## Pro & Kontra tiap Opsi

### Satu baris per tindakan

- Pro: cukup untuk menelusuri, tanpa data pribadi.
- Kontra: perlu memilih kolom yang dicatat untuk setiap tindakan.

### Salinan penuh

- Pro: paling lengkap.
- Kontra: menyalin isian dan jawaban pelapor yang seharusnya hilang saat akun dihapus.

### Hanya log server

- Pro: tanpa kode tambahan.
- Kontra: log server tidak terstruktur, berisi alamat IP, dan masa simpannya belum ditetapkan.

## Referensi

- [ADR-0024](0024-data-setelah-akun-dihapus.md), [ADR-0035](0035-peran-dan-panel-verifikator-di-web-yang-sama.md), [ADR-0036](0036-keputusan-verifikasi-dan-klarifikasi-di-laporan-saya.md), [ADR-0037](0037-foto-dikaburkan-di-api-dan-asli-dihapus.md)
- [PRD](../product/prd.md), FR-35 dan bagian 9 (Log audit)
