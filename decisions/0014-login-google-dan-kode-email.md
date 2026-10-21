# ADR-0014: Login dengan Google atau Kode Email

- Status: Accepted
- Tanggal: 2026-10-21
- Pengambil keputusan: Tim AksesBogor
- Issue: #18

## Konteks dan Masalah

[ADR-0011](0011-pelapor-wajib-login.md) mewajibkan pelapor login dan menyerahkan metodenya ke keputusan tech stack ([ADR-0013](0013-tech-stack-nextjs-fastify.md)). Pengguna kebanyakan memakai ponsel, sering lewat aplikasi PWA yang sudah dipasang, dan proses login harus memenuhi WCAG 2.2 kriteria 3.3.8 (tanpa tes kognitif). Metode login apa yang dipakai?

## Decision Drivers

- Tanpa kata sandi baru yang harus diingat.
- Tetap berjalan di dalam PWA yang terpasang, tidak berpindah ke browser lain.
- Bisa dipakai orang yang tidak punya atau tidak mau memakai akun Google.
- Aksesibel: boleh tempel, mendukung isi otomatis, tanpa CAPTCHA teka-teki.

## Opsi yang Dipertimbangkan

1. Google atau kode 6 digit lewat email
2. Google atau email dan kata sandi
3. Google atau magic link lewat email
4. Google saja (pilihan lokerin)

## Keputusan

Opsi terpilih: "Google atau kode 6 digit lewat email".

- **Google**: tombol "Masuk dengan Google" memakai Google Identity Services. Fastify memverifikasi ID token terhadap client ID AksesBogor.
- **Kode email**: pengguna memasukkan email, lalu Fastify mengirim kode 6 digit. Nilai awal: kode berlaku 10 menit dan sekali pakai, disimpan dalam bentuk hash, maksimal 5 percobaan, kirim ulang setelah 60 detik, dan pembatasan laju per email dan per IP.
- **Kolom kode**: satu kolom (bukan enam kotak) dengan `autocomplete="one-time-code"` dan `inputmode="numeric"`; tempel selalu diizinkan.
- **Sesi**: token bertanda tangan, dikirim sebagai cookie `HttpOnly`, `Secure`, `SameSite=Lax`, atau header `Authorization: Bearer` dari origin lain, seperti di lokerin. Keluar mencabut sesi.
- **Akun**: dibuat saat login pertama dengan email sebagai identitas. Login Google dan kode email dengan email terverifikasi yang sama masuk ke akun yang sama. Persetujuan Kebijakan Privasi diminta di login pertama (FR-11).

## Konsekuensi

- Positif: tidak ada kata sandi yang bisa bocor atau dilupakan.
- Positif: kode 6 digit diketik atau ditempel di layar yang sama, jadi alur tetap di dalam PWA.
- Positif: pengguna tanpa akun Google tetap bisa melapor.
- Negatif: bergantung pada pengiriman email; kode bisa masuk folder spam. Layar kode perlu menyebutkannya dan menyediakan kirim ulang.
- Negatif: butuh penyedia email transaksional dan logika penggabungan akun.

## Pro & Kontra tiap Opsi

### Google atau kode email

- Pro: tanpa kata sandi; tetap di dalam PWA; inklusif.
- Kontra: bergantung pada email.

### Google atau email dan kata sandi

- Pro: familiar; didukung pengelola kata sandi.
- Kontra: alur reset dan risiko kebocoran kata sandi.

### Google atau magic link

- Pro: satu ketukan dari email.
- Kontra: tautan terbuka di browser bawaan, bukan di PWA terpasang, terutama di iOS.

### Google saja

- Pro: paling sedikit dibangun; sama dengan lokerin.
- Kontra: menutup akses bagi orang tanpa akun Google.

## Referensi

- [ADR-0011](0011-pelapor-wajib-login.md), [ADR-0013](0013-tech-stack-nextjs-fastify.md)
- [WCAG 2.2, kriteria 3.3.8 Accessible Authentication (Minimum)](https://www.w3.org/TR/WCAG22/#accessible-authentication-minimum)
- Repo lokerin: `al-dioooo/lokerin-docs` (ADR 0007, bagian Login)
