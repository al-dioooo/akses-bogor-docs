# ADR-0030: Email Transaksional lewat Resend

- Status: Accepted
- Tanggal: 2026-11-07
- Pengambil keputusan: Tim AksesBogor
- Issue: #50
- Dilengkapi oleh: [ADR-0039](0039-email-status-laporan.md) (email status laporan)

## Konteks dan Masalah

[ADR-0013](0013-tech-stack-nextjs-fastify.md) menunda pilihan penyedia email transaksional sampai implementasi. Sekarang API perlu mengirim kode masuk 6 digit ([ADR-0014](0014-login-google-dan-kode-email.md)) dan email konfirmasi akun dihapus ([ADR-0024](0024-data-setelah-akun-dihapus.md)). Setelah itu menyusul email status laporan dan klarifikasi (FR-40, FR-41). Penyedia mana yang dipakai?

## Decision Drivers

- Gratis atau murah untuk proyek kampus, dengan kuota yang cukup untuk alpha dan beta.
- Email kode sampai di kotak masuk, bukan folder spam.
- Bisa dipanggil dari Fastify tanpa SDK besar.
- Data penerima diproses sesedikit mungkin dan bisa dijelaskan di Kebijakan Privasi (UU No. 27 Tahun 2022).

## Opsi yang Dipertimbangkan

1. Resend
2. Brevo
3. Amazon SES region Jakarta

## Keputusan

Opsi terpilih: "Resend", karena paket gratisnya cukup untuk alpha, API HTTP-nya bisa dipanggil dengan `fetch` biasa, dan pelacakan buka dan klik tidak aktif secara bawaan.

- API memanggil `POST https://api.resend.com/emails` dengan `fetch`. Tidak ada SDK yang ditambahkan.
- Pengirim memakai subdomain yang dikelola tim (mis. `AksesBogor <masuk@kirim.yorhagarden.xyz>`), diisi di `EMAIL_FROM`. Subdomain itu diverifikasi di Resend lewat record SPF dan DKIM, ditambah record DMARC.
- Email berisi teks biasa dan HTML sederhana dalam bahasa Indonesia, tanpa gambar, tautan pelacak, atau piksel pembukaan.
- Subjek email kode memuat kodenya, mis. "Kode masuk AksesBogor: 482915", supaya kode terbaca dari notifikasi tanpa membuka email. Isi email menyebut masa berlaku 10 menit dan bahwa email ini bisa diabaikan bila pengguna tidak meminta kode.
- Email yang gagal dikirim membuat API menjawab 502 `email_gagal_dikirim`. Pengguna bisa mencoba lagi; kode yang gagal terkirim tidak dihitung sebagai percobaan salah.
- Di luar production, bila `RESEND_API_KEY` kosong, API tidak mengirim email dan mencetak kode ke log. Di production tanpa `RESEND_API_KEY`, API tetap berjalan, tetapi permintaan kode menjawab 503 `email_belum_diatur`; masuk dengan Google tetap bisa.

### Konfigurasi

| Variabel | Rahasia | Isi |
| --- | --- | --- |
| `RESEND_API_KEY` | Ya | API key Resend dengan izin "Sending access" untuk domain pengirim saja |
| `EMAIL_FROM` | Tidak | Nama dan alamat pengirim pada domain yang sudah diverifikasi |

## Konsekuensi

- Positif: gratis sampai 3.000 email per bulan dan 100 per hari. Bila beta melewati itu, paket Pro sekitar US$20 per bulan untuk 50.000 email.
- Positif: tidak ada dependensi baru di API.
- Negatif: alamat email dan isi email diproses Resend di Amerika Serikat. Ini transfer data pribadi ke luar negeri dan wajib disebut di Kebijakan Privasi.
- Negatif: batas 100 email per hari bisa tercapai saat ada acara pengumpulan data. Pantau dasbor Resend, dan naikkan paket sebelum acara besar.
- Negatif: butuh akses DNS untuk domain pengirim. Bila tim pindah ke domain sendiri, domain baru perlu diverifikasi ulang.

## Pro & Kontra tiap Opsi

### Resend

- Pro: API sederhana; paket gratis permanen; pelacakan mati secara bawaan.
- Kontra: data diproses di AS; batas harian 100 di paket gratis.

### Brevo

- Pro: 300 email per hari di paket gratis; data diproses di Uni Eropa.
- Kontra: logo Brevo di email paket gratis; dasbornya berfokus pada pemasaran, dan pelacakan perlu dimatikan sendiri.

### Amazon SES region Jakarta

- Pro: data tetap di Indonesia; paling murah per email.
- Kontra: butuh akun AWS dengan kartu, permintaan keluar dari sandbox, dan tanda tangan permintaan AWS yang lebih rumit.

## Referensi

- [ADR-0013](0013-tech-stack-nextjs-fastify.md), [ADR-0014](0014-login-google-dan-kode-email.md), [ADR-0024](0024-data-setelah-akun-dihapus.md), [ADR-0029](0029-akun-better-auth-dan-sesi-lewat-server-web.md)
- [PRD](../product/prd.md), FR-40 dan FR-41
- [Harga Resend](https://resend.com/pricing)
