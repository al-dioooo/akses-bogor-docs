# ADR-0039: Email Status Laporan Dikirim setelah Keputusan dan Dicoba Ulang Selama 3 Hari

- Status: Accepted
- Tanggal: 2026-11-08
- Pengambil keputusan: Tim AksesBogor
- Issue: #58
- Melengkapi: [ADR-0030](0030-email-transaksional-resend.md)

## Konteks dan Masalah

FR-40 meminta email saat laporan diterbitkan atau ditolak beserta alasannya, dan FR-41 meminta email saat verifikator meminta klarifikasi. [ADR-0030](0030-email-transaksional-resend.md) sudah memilih Resend dan gaya email (teks biasa dan HTML sederhana, tanpa pelacak), dan [ADR-0033](0033-isi-dulu-masuk-saat-kirim-dan-draf-di-perangkat.md) memutuskan tidak ada email "laporan diterima". Yang belum diputuskan: isi setiap email, tautan di dalamnya, dan apa yang terjadi bila Resend gagal atau kuota hariannya habis.

## Decision Drivers

- Keputusan verifikator tidak boleh gagal karena email gagal.
- Pelapor tetap mendapat kabar walaupun Resend sedang gangguan atau kuota 100 email per hari habis.
- Alamat email tidak disalin ke tempat lain selain tabel akun (UU No. 27 Tahun 2022, [ADR-0024](0024-data-setelah-akun-dihapus.md)).
- Tanpa dependensi, layanan, atau timer baru.

## Opsi yang Dipertimbangkan

1. Dikirim setelah keputusan tersimpan, dengan antrean kecil yang dicoba ulang setiap jam selama 3 hari
2. Dikirim di dalam permintaan keputusan; gagal berarti hilang
3. Dikirim di dalam transaksi keputusan; gagal berarti keputusan dibatalkan

## Keputusan

Opsi terpilih: "Setelah keputusan tersimpan, dengan antrean coba ulang".

### Email yang dikirim

| Kapan | Subjek | Isi |
| --- | --- | --- |
| Diterbitkan | Laporan Anda sudah terbit: *nama tempat* | Terima kasih, tautan ke halaman lokasi |
| Ditolak oleh verifikator (R1–R5) | Laporan Anda belum bisa diterbitkan: *nama tempat* | Nama alasan dan pesan baku dari [Panduan Verifikasi](../operations/panduan-verifikasi.md#63-tolak-dengan-alasan-baku), tautan ke Akun saya |
| Ditolak otomatis (R6) | Sama dengan baris di atas | Pesan baku R6 |
| Klarifikasi diminta | Pertanyaan tentang laporan Anda: *nama tempat* | Pertanyaan verifikator, tenggat jawaban (tanggal dan jam WIB), tautan ke halaman jawab di Laporan saya |

- Email memakai `EMAIL_FROM` yang sama dengan kode masuk. Tautan dibuat dari origin pertama di `FRONTEND_ORIGINS`, jadi tidak ada variabel environment baru.
- Email tidak menyebut nama verifikator, nama tampilan pelapor, atau catatan internal. Pertanyaan klarifikasi ikut di email karena memang ditujukan untuk pelapor.
- Tidak ada email untuk verifikator, mis. saat laporan baru masuk.

### Pengiriman dan coba ulang

- Keputusan menyimpan satu baris antrean berisi id laporan dan jenis email, di transaksi yang sama dengan keputusannya. Alamat email tidak disimpan di antrean.
- Setelah transaksi selesai, API langsung mengirim email itu dan menghapus barisnya bila berhasil.
- Yang gagal dicoba lagi setiap jam bersama pemeriksaan tenggat klarifikasi ([ADR-0036](0036-keputusan-verifikasi-dan-klarifikasi-di-laporan-saya.md)). Setelah 3 hari tanpa berhasil, barisnya dibuang dan dicatat di log server. Pelapor tetap bisa melihat status di Laporan saya.
- Saat dikirim, alamat dan isi dibaca dari data terbaru. Email tidak dikirim bila akun pelapor sudah dihapus atau status laporan sudah berubah, mis. klarifikasi yang sudah dijawab.
- Di development tanpa `RESEND_API_KEY`, email dicetak ke log API seperti kode masuk ([ADR-0030](0030-email-transaksional-resend.md)).

## Konsekuensi

- Positif: keputusan verifikator selalu tersimpan, dan email yang tertunda tetap terkirim setelah gangguan atau keesokan harinya saat kuota pulih.
- Positif: alamat email hanya dibaca saat mengirim, dan antrean tidak menyimpan data pribadi.
- Negatif: email bisa terlambat sampai beberapa jam bila Resend gagal atau kuota habis.
- Negatif: email status ikut menghabiskan kuota 100 email per hari bersama kode masuk. Pantau dasbor Resend selama beta.
- Negatif: setelah 3 hari gagal, pelapor hanya tahu statusnya dari Laporan saya.

## Pro & Kontra tiap Opsi

### Setelah keputusan, dengan antrean

- Pro: keputusan tidak bergantung pada Resend; email tidak hilang karena gangguan singkat.
- Kontra: satu tabel dan satu tugas berkala tambahan.

### Di dalam permintaan, tanpa coba ulang

- Pro: paling sederhana.
- Kontra: email hilang setiap kali Resend gagal atau kuota habis.

### Di dalam transaksi keputusan

- Pro: email dan keputusan selalu sejalan.
- Kontra: verifikator tidak bisa memutuskan saat Resend gangguan.

## Referensi

- [ADR-0024](0024-data-setelah-akun-dihapus.md), [ADR-0030](0030-email-transaksional-resend.md), [ADR-0033](0033-isi-dulu-masuk-saat-kirim-dan-draf-di-perangkat.md), [ADR-0036](0036-keputusan-verifikasi-dan-klarifikasi-di-laporan-saya.md)
- [PRD](../product/prd.md), FR-40 dan FR-41
- [Panduan Verifikasi](../operations/panduan-verifikasi.md), bagian 6
