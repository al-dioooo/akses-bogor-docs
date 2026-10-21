# ADR-0019: Verifikasi oleh Tim dan Mitra Terlatih

- Status: Accepted
- Tanggal: 2026-10-21
- Pengambil keputusan: Tim AksesBogor
- Issue: #22

## Konteks dan Masalah

[ADR-0006](0006-laporan-warga-diverifikasi-sebelum-tayang.md) menetapkan bahwa laporan diverifikasi sebelum tayang, tetapi belum menjawab siapa verifikatornya, bukti apa yang diterima, berapa lama prosesnya, dan bagaimana perubahan serta foto ditangani ([PT-09](../product/pertanyaan-terbuka.md#pt-09-proses-verifikasi-dan-moderasi)).

## Decision Drivers

- Data yang tayang harus tepercaya.
- Kapasitas cukup agar laporan tidak menumpuk.
- Pengalaman langsung penyandang disabilitas meningkatkan ketepatan penilaian.
- Data pribadi pelapor terlindungi (UU No. 27 Tahun 2022).

## Opsi yang Dipertimbangkan

1. Tim inti dan mitra komunitas difabel yang dilatih
2. Tim inti saja
3. Voting komunitas oleh pengguna lain

## Keputusan

Opsi terpilih: "Tim inti dan mitra komunitas difabel yang dilatih".

### Verifikator

- Anggota tim AksesBogor dan relawan dari komunitas difabel di Bogor yang sudah mengikuti pelatihan memakai daftar periksa dan contoh foto.
- Peran verifikator hanya diberikan oleh admin (FR-36).
- Verifikator tidak boleh memverifikasi laporannya sendiri.
- Verifikator hanya melihat nama tampilan pelapor. Klarifikasi dikirim lewat sistem, bukan lewat kontak pribadi.

### Bukti

- Foto pintu masuk dan foto fasilitas adalah bukti utama.
- Bila foto tidak cukup, verifikator memakai kunjungan lapangan atau konfirmasi pengelola tempat. Laporan tanpa foto tetap diterima, tetapi biasanya lebih lama diputuskan.
- Jawaban "Tersedia" atau "Tidak perlu" yang tidak bisa dibuktikan diubah verifikator menjadi "Belum tahu", alih-alih menolak seluruh laporan ([ADR-0017](0017-empat-jawaban-ketersediaan-fasilitas.md)).

### Hasil

- **Terbitkan**: jawaban terverifikasi dipakai, dan status dihitung ulang ([ADR-0018](0018-status-pintu-masuk-dulu-dan-ringkasan-fasilitas.md)).
- **Minta klarifikasi**: pelapor mendapat email dan punya 7 hari untuk menjawab. Setelah itu laporan ditolak dengan alasan "tidak ada klarifikasi".
- **Tolak** dengan alasan baku: di luar Kota Bogor, duplikat, bukti tidak cukup, bukan fasilitas publik, atau konten tidak pantas.

### Waktu dan perubahan

- Target median: keputusan dalam 3 hari kerja sejak laporan dikirim.
- Laporan baru, "Edit lokasi", dan "Laporkan kekurangan" masuk antrean yang sama, diurutkan dari yang terlama. Data lama tetap tayang sampai perubahan diterbitkan.
- *(nilai awal)* Lokasi yang terakhir diverifikasi lebih dari 12 bulan lalu ditandai perlu dicek ulang.

### Foto

- Wajah orang dan pelat nomor dikaburkan, atau fotonya disembunyikan (FR-34). Metadata EXIF sudah dihapus saat unggah ([ADR-0013](0013-tech-stack-nextjs-fastify.md)).

## Konsekuensi

- Positif: kapasitas verifikasi lebih besar dan penilaian lebih tepat berkat pengalaman langsung.
- Positif: aturan bukti yang jelas membuat keputusan antar verifikator konsisten.
- Negatif: perlu merekrut dan melatih mitra, serta menyiapkan panduan verifikasi tertulis.
- Negatif: relawan mengakses data laporan, jadi perlu perjanjian kerahasiaan dan akses sesuai peran.
- Tindak lanjut: tulis panduan verifikasi (daftar periksa, contoh foto, daftar alasan baku) di repo ini.

## Pro & Kontra tiap Opsi

### Tim dan mitra terlatih

- Pro: kapasitas dan pengalaman langsung.
- Kontra: rekrutmen, pelatihan, dan pengawasan kualitas.

### Tim inti saja

- Pro: paling mudah dikendalikan.
- Kontra: kapasitas kecil; laporan bisa menumpuk.

### Voting komunitas

- Pro: bisa diskalakan nanti.
- Kontra: lambat mencapai cukup suara saat awal; mudah dimanipulasi.

## Referensi

- [Pertanyaan Terbuka PT-09](../product/pertanyaan-terbuka.md#pt-09-proses-verifikasi-dan-moderasi)
- [ADR-0006](0006-laporan-warga-diverifikasi-sebelum-tayang.md), [ADR-0017](0017-empat-jawaban-ketersediaan-fasilitas.md), [ADR-0018](0018-status-pintu-masuk-dulu-dan-ringkasan-fasilitas.md)
- [PRD](../product/prd.md), FR-30 sampai FR-36
