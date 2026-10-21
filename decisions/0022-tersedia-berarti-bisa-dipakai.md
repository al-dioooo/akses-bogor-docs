# ADR-0022: "Tersedia" Berarti Bisa Dipakai, dengan Alasan untuk "Tidak Tersedia"

- Status: Accepted
- Tanggal: 2026-10-21
- Pengambil keputusan: Tim AksesBogor
- Issue: #28
- Melengkapi: [ADR-0017](0017-empat-jawaban-ketersediaan-fasilitas.md)

## Konteks dan Masalah

[ADR-0017](0017-empat-jawaban-ketersediaan-fasilitas.md) mengartikan "Tersedia" sebagai ada dan bisa dipakai, dan "Tidak tersedia" sebagai dibutuhkan tetapi tidak ada. Fasilitas yang ada tetapi tidak bisa dipakai, seperti lift rusak atau ramp tertutup parkir motor, belum punya tempat. Panduan Verifikasi sudah memperlakukannya sebagai Tidak tersedia dengan catatan, tetapi PRD belum ([PT-08](../product/pertanyaan-terbuka.md#pt-08-kondisi-fasilitas)). Bagaimana kondisi fasilitas dicatat?

## Decision Drivers

- Bagi pengguna kursi roda, lift rusak sama dengan tidak ada lift hari itu.
- Pengelola dan advokat perlu tahu bedanya fasilitas yang harus diperbaiki dan yang harus dibangun.
- Formulir tetap singkat; pelapor tidak bisa mengukur.
- Aturan status di [ADR-0018](0018-status-pintu-masuk-dulu-dan-ringkasan-fasilitas.md) tidak berubah.

## Opsi yang Dipertimbangkan

1. "Tersedia" berarti bisa dipakai; "Tidak tersedia" diberi alasan; catatan opsional
2. Skala kondisi terpisah (Baik, Perlu perbaikan, Rusak) untuk setiap fasilitas
3. Ukuran teknis (kemiringan ramp, lebar pintu) yang dicatat verifikator
4. Kondisi hanya di deskripsi bebas

## Keputusan

Opsi terpilih: "Tersedia berarti bisa dipakai, dengan alasan untuk Tidak tersedia".

### Arti jawaban

- **Tersedia**: fasilitas ada dan bisa dipakai oleh orang yang membutuhkannya saat diverifikasi.
- **Tidak tersedia**: fasilitas dibutuhkan tetapi tidak ada atau tidak bisa dipakai. "Tidak perlu" dan "Belum tahu" tidak berubah.

### Alasan untuk "Tidak tersedia"

| Alasan | Arti | Contoh |
| --- | --- | --- |
| Tidak ada | Belum pernah dibangun | Pintu masuk bertangga tanpa ramp |
| Rusak | Ada tetapi rusak | Lift mati sejak awal Oktober |
| Terhalang | Ada tetapi tertutup benda | Ramp tertutup parkir motor; guiding block tertutup lapak |
| Terbatas | Ada tetapi tidak untuk umum | Lift khusus staf; toilet terkunci tanpa petugas yang bisa membuka |
| Tidak sesuai | Ada tetapi tidak memenuhi syarat dasar di [Panduan Verifikasi](../operations/panduan-verifikasi.md#42-fasilitas) | Ramp terlalu curam atau sempit; toilet tidak muat kursi roda |

- Pelapor boleh memilih alasan saat menjawab Tidak tersedia, atau melewatinya.
- Verifikator wajib memastikan alasannya sebelum laporan diterbitkan.

### Catatan

- Setiap jawaban boleh diberi catatan singkat, maksimal 200 karakter. Contoh: "Kunci toilet di pos satpam", "Rusak sejak awal Oktober 2026".
- Catatan tampil di halaman Detail dan tidak boleh memuat data pribadi.

### Tampilan dan status

- Detail menampilkan jawaban, alasan, dan catatan, misalnya "Lift · Tidak tersedia · Rusak".
- Kalimat alasan status memakai alasannya, misalnya "Sebagian: Lift tidak tersedia (rusak)".
- Aturan status di ADR-0018 tidak berubah. Alasan tidak mengubah status.
- Ukuran teknis tidak dicatat di MVP. Foto per fasilitas diatur lewat tag foto di [ADR-0023](0023-hingga-sepuluh-foto-bertag-per-laporan.md).

## Konsekuensi

- Positif: data membedakan "tidak ada" dan "ada tetapi tidak bisa dipakai" tanpa menambah pertanyaan di formulir.
- Positif: menyamakan PRD dengan praktik di Panduan Verifikasi.
- Negatif: verifikator punya satu keputusan tambahan untuk setiap Tidak tersedia.
- Negatif: fasilitas yang rusak sementara perlu laporan baru setelah diperbaiki.
- Tindak lanjut: komponen Facility answer di Figma menampilkan pilihan alasan saat Tidak tersedia dipilih dan kolom catatan; Facility item menampilkan alasan.

## Pro & Kontra tiap Opsi

### Tersedia berarti bisa dipakai, dengan alasan

- Pro: formulir tetap singkat, status tetap sederhana, informasi kondisi tetap ada.
- Kontra: kondisi sedang (bisa dipakai tetapi kurang baik) hanya muncul di catatan.

### Skala kondisi terpisah

- Pro: lebih rinci.
- Kontra: empat pertanyaan tambahan; tidak jelas bagaimana "Perlu perbaikan" memengaruhi status.

### Ukuran teknis

- Pro: paling presisi dan bisa dibandingkan dengan Permen PUPR No. 14/PRT/M/2017.
- Kontra: pelapor tidak bisa mengukur; butuh alat dan pelatihan. Lebih cocok setelah MVP.

### Deskripsi bebas

- Pro: tanpa perubahan.
- Kontra: tidak bisa ditampilkan secara konsisten atau dipakai sebagai filter.

## Referensi

- [Pertanyaan Terbuka PT-08](../product/pertanyaan-terbuka.md#pt-08-kondisi-fasilitas)
- [ADR-0017](0017-empat-jawaban-ketersediaan-fasilitas.md), [ADR-0018](0018-status-pintu-masuk-dulu-dan-ringkasan-fasilitas.md), [ADR-0019](0019-verifikasi-tim-dan-mitra-terlatih.md)
- [Panduan Verifikasi](../operations/panduan-verifikasi.md), bagian 4.2 dan 7
