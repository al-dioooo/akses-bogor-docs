# Decision Log (ADR)

Folder ini berisi catatan keputusan proyek AksesBogor dalam format [MADR](https://adr.github.io/madr/). Satu file berisi satu keputusan. Cakupannya semua jenis keputusan: produk, desain, teknis, dan proses.

## Kapan Menulis ADR

- Keputusan sulit dibalik atau berdampak ke lebih dari satu orang.
- Ada beberapa opsi yang masuk akal dan alasan memilih perlu diingat.
- Keputusan sudah tercermin di desain atau kode tetapi belum tercatat. Tulis secara retroaktif dan tandai demikian.

## Cara Menulis

1. Buat issue dengan template "Usulan keputusan (ADR)".
2. Salin [template.md](template.md) menjadi `NNNN-judul-singkat.md`, dengan `NNNN` nomor urut berikutnya (4 digit) dan judul dalam kebab-case.
3. Isi semua bagian. Status awal `Proposed`.
4. Buka pull request. Diskusi keputusan terjadi di PR.
5. Sebelum merge, ubah status menjadi `Accepted` atau `Rejected` sesuai hasil review.
6. Tambahkan baris di [index](#index).

## Status

| Status | Arti |
| --- | --- |
| `Proposed` | Diusulkan, belum diputuskan |
| `Accepted` | Disetujui dan berlaku |
| `Rejected` | Ditolak; tetap disimpan sebagai catatan |
| `Deprecated` | Tidak berlaku lagi, tanpa pengganti |
| `Superseded` | Digantikan ADR lain (tautkan penggantinya) |

ADR yang sudah `Accepted` tidak diubah isinya. Mengubah keputusan berarti menulis ADR baru yang men-supersede ADR lama, lalu memperbarui status ADR lama. Perbaikan typo dan link tetap boleh.

## Index

| No | Judul | Status | Tanggal |
| --- | --- | --- | --- |
| [0001](0001-mencatat-keputusan-dengan-madr.md) | Mencatat keputusan proyek dengan ADR format MADR | Accepted | 2026-10-21 |
