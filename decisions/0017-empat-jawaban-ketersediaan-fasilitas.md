# ADR-0017: Empat Jawaban untuk Ketersediaan Fasilitas

- Status: Accepted
- Tanggal: 2026-10-21
- Pengambil keputusan: Tim AksesBogor
- Issue: #22
- Men-supersede: [ADR-0005](0005-empat-fasilitas-aksesibilitas.md)
- Dilengkapi oleh: [ADR-0022](0022-tersedia-berarti-bisa-dipakai.md)

## Konteks dan Masalah

[ADR-0005](0005-empat-fasilitas-aksesibilitas.md) mencatat empat fasilitas sebagai tersedia atau tidak. Konsekuensinya sendiri sudah mencatat masalahnya: checkbox yang tidak dicentang bisa berarti "tidak tersedia" atau "pelapor tidak tahu". Ada juga kasus ketiga yang belum tertangkap: fasilitas yang memang tidak dibutuhkan, misalnya lift di masjid satu lantai atau ramp di pintu masuk yang sudah rata. Tanpa jawaban itu, tempat seperti ini terlihat lebih buruk dari kenyataannya. Jawaban apa yang mungkin untuk setiap fasilitas?

## Decision Drivers

- Data membedakan "tidak ada", "tidak tahu", dan "tidak dibutuhkan".
- Tempat yang sudah aksesibel tanpa fasilitas tertentu tidak dirugikan.
- Formulir tetap cepat diisi warga awam.
- Status lokasi bisa dihitung dari jawaban ([ADR-0018](0018-status-pintu-masuk-dulu-dan-ringkasan-fasilitas.md)).

## Opsi yang Dipertimbangkan

1. Empat jawaban: Tersedia, Tidak tersedia, Tidak perlu, Belum tahu
2. Tiga jawaban: Tersedia, Tidak tersedia, Belum tahu
3. Tetap dua jawaban: tersedia atau tidak (ADR-0005)

## Keputusan

Opsi terpilih: "Empat jawaban". Daftar fasilitas tetap sama dengan ADR-0005: Ramp / Jalur Landai, Toilet Difabel, Guiding Block, dan Lift / Elevator.

| Jawaban | Arti | Contoh |
| --- | --- | --- |
| Tersedia | Fasilitas ada dan bisa dipakai | Ada ramp di pintu utama |
| Tidak tersedia | Fasilitas dibutuhkan tetapi tidak ada | Pintu masuk bertangga tanpa ramp |
| Tidak perlu | Fasilitas tidak dibutuhkan di tempat itu | Pintu masuk sudah rata (ramp); semua layanan di lantai dasar (lift); tidak ada toilet umum sama sekali (toilet difabel) |
| Belum tahu | Pelapor atau verifikator belum bisa memastikan | Pelapor tidak sempat memeriksa lantai dua |

- Di formulir, setiap fasilitas dijawab dengan satu pilihan dari empat, dengan bawaan **Belum tahu**. Tidak ada jawaban yang otomatis terisi "Tersedia".
- Di halaman Detail, "Belum tahu" ditampilkan sebagai **Belum diketahui**.
- "Tidak perlu" harus dibenarkan verifikator seperti jawaban lain ([ADR-0019](0019-verifikasi-tim-dan-mitra-terlatih.md)).

## Konsekuensi

- Positif: data lebih jujur; status bisa dihitung tanpa menebak.
- Positif: masjid satu lantai atau halte tanpa toilet tidak dianggap kurang aksesibel karena hal yang tidak relevan.
- Negatif: setiap fasilitas di langkah 2 butuh pilihan empat opsi, bukan satu checkbox, sehingga sedikit lebih lama diisi.
- Negatif: "Tidak perlu" bisa disalahgunakan untuk menutupi kekurangan; verifikator harus memeriksanya.
- Tindak lanjut desain: langkah 2 Laporkan perlu komponen pilihan empat opsi per fasilitas, dan komponen Facility item perlu varian "Tidak perlu".

## Pro & Kontra tiap Opsi

### Empat jawaban

- Pro: membedakan semua kasus yang penting untuk status.
- Kontra: formulir sedikit lebih panjang; perlu pengawasan "Tidak perlu".

### Tiga jawaban

- Pro: lebih sederhana; sudah memisahkan "tidak tahu".
- Kontra: fasilitas yang tidak dibutuhkan tetap dihitung sebagai kekurangan.

### Dua jawaban

- Pro: paling cepat diisi.
- Kontra: "tidak ada" dan "tidak tahu" tercampur; status tidak bisa dihitung dengan adil.

## Referensi

- [ADR-0005](0005-empat-fasilitas-aksesibilitas.md) (di-supersede)
- [PRD](../product/prd.md), FR-24
