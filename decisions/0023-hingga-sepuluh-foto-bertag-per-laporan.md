# ADR-0023: Hingga Sepuluh Foto Bertag per Laporan

- Status: Accepted
- Tanggal: 2026-10-21
- Pengambil keputusan: Tim AksesBogor
- Issue: #28
- Melengkapi: [ADR-0006](0006-laporan-warga-diverifikasi-sebelum-tayang.md) (rincian foto)

## Konteks dan Masalah

Desain dan [ADR-0006](0006-laporan-warga-diverifikasi-sebelum-tayang.md) menerima foto PNG atau JPG maksimal 5 MB, dan draf FR-25 membatasinya hingga 3 foto. Padahal foto minimum di [Panduan Verifikasi](../operations/panduan-verifikasi.md#51-foto-minimum) bisa mencapai 5 foto: pintu masuk, ditambah satu untuk setiap fasilitas yang dijawab Tersedia atau Tidak perlu. Foto mentah dari ponsel juga sering lebih dari 5 MB. Berapa foto yang diterima, dan bagaimana foto ditangani?

## Decision Drivers

- Foto adalah bukti utama verifikasi ([ADR-0019](0019-verifikasi-tim-dan-mitra-terlatih.md)).
- Pelapor sering memakai data seluler.
- Verifikator harus bisa meninjau foto dengan cepat.
- Biaya penyimpanan di Cloudflare R2 tetap kecil ([ADR-0013](0013-tech-stack-nextjs-fastify.md)).

## Opsi yang Dipertimbangkan

1. Hingga 10 foto, masing-masing diberi tag objek
2. Hingga 5 foto
3. Hingga 3 foto, maksimal 5 MB per foto (draf FR-25)

## Keputusan

Opsi terpilih: "Hingga 10 foto bertag". Sepuluh foto cukup untuk satu foto jauh dan satu foto dekat bagi setiap objek, sesuai tips di Panduan Verifikasi.

### Jumlah dan tag

- Laporan baru, "Edit lokasi", dan "Laporkan kekurangan" menerima 0 sampai 10 foto. Foto tetap opsional.
- Setiap foto diberi satu tag: Pintu masuk dan ramp, Toilet difabel, Guiding block, Lift, atau Lainnya. Verifikator bisa memperbaiki tag yang salah.
- Langkah 3 formulir menunjukkan foto yang membantu verifikasi, berdasarkan jawaban di langkah 2.

### Berkas

- Format yang diterima: JPEG, PNG, dan WebP, hingga 20 MB per berkas mentah. Dukungan HEIC dari iPhone diperiksa saat implementasi.
- Browser memperkecil foto sebelum diunggah: sisi terpanjang maksimal 2048 px, kualitas sekitar 80%. Hasilnya biasanya 300–600 KB per foto.
- Fastify memeriksa jenis dan ukuran berkas (maksimal 3 MB setelah diperkecil), menyandikan ulang, dan menghapus metadata EXIF sebelum menyimpan ke R2 ([ADR-0013](0013-tech-stack-nextjs-fastify.md)).
- Setiap foto diunggah terpisah dengan indikator kemajuan. Foto yang gagal bisa dicoba ulang tanpa kehilangan isian formulir.

### Tampilan

- Galeri di halaman Detail dikelompokkan menurut tag.
- Teks alternatif dibuat dari tag dan nama lokasi, misalnya "Foto pintu masuk dan ramp, Halte contoh". Verifikator bisa menambahkan keterangan singkat saat menerbitkan.

## Konsekuensi

- Positif: bukti cukup untuk setiap fasilitas, sehingga klarifikasi lebih jarang.
- Positif: foto ponsel apa pun diterima, dan data seluler yang terpakai kecil.
- Positif: perkiraan penyimpanan kecil, sekitar 4 MB untuk laporan dengan 10 foto, atau sekitar 4 GB untuk 1.000 laporan penuh.
- Negatif: verifikator meninjau lebih banyak foto per laporan.
- Negatif: pemberian tag menambah satu langkah kecil bagi pelapor.
- Tindak lanjut: Upload dropzone di Figma diganti teksnya ("Hingga 10 foto") dan ditambah pemilih tag.

## Pro & Kontra tiap Opsi

### Hingga 10 foto bertag

- Pro: mencakup foto minimum dengan ruang untuk foto dekat; tag mempercepat verifikasi.
- Kontra: lebih banyak foto untuk ditinjau.

### Hingga 5 foto

- Pro: lebih sedikit untuk ditinjau.
- Kontra: pas-pasan untuk foto minimum; tidak ada ruang untuk foto dekat.

### Hingga 3 foto, 5 MB

- Pro: paling sederhana.
- Kontra: kurang dari foto minimum; menolak banyak foto mentah dari ponsel.

## Referensi

- [PRD](../product/prd.md), FR-25
- [Panduan Verifikasi](../operations/panduan-verifikasi.md), bagian 5
- [ADR-0013](0013-tech-stack-nextjs-fastify.md), [ADR-0019](0019-verifikasi-tim-dan-mitra-terlatih.md), [ADR-0021](0021-wcag-22-aa-dan-pengujian-berlapis.md), [ADR-0022](0022-tersedia-berarti-bisa-dipakai.md)
