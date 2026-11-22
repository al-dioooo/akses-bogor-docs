# ADR-0045: Alamat Staging Pindah ke aldio.dev

- Status: Accepted
- Tanggal: 2026-11-22
- Pengambil keputusan: Tim AksesBogor
- Issue: #81
- Men-supersede sebagian: [ADR-0026](0026-api-dan-postgresql-di-evergarden-oci.md) (hostname publik API)
- Di-supersede sebagian oleh: [ADR-0048](0048-web-di-host-sendiri-di-evergarden-oci.md) (web di Vercel dan alias `akses-bogor.vercel.app`)

Dicatat secara retroaktif: perpindahan sudah dijalankan pada tanggal yang sama sebelum ADR ini ditulis.

## Konteks dan Masalah

Web dibuka di `akses-bogor.vercel.app`, API di `akses-bogor-api.yorhagarden.xyz` ([ADR-0026](0026-api-dan-postgresql-di-evergarden-oci.md)), dan foto yang sudah terbit di `akses-bogor-foto.yorhagarden.xyz` ([ADR-0031](0031-foto-laporan-di-bucket-r2-privat.md)). Lingkungan ini dipakai tim untuk mencoba dan menguji, dan sekarang disebut staging, karena domain produksi akhir belum diputuskan. Di mana web, API, dan foto dibuka selama staging, dan apa yang terjadi dengan alamat lama?

## Decision Drivers

- Tim butuh alamat yang bisa dipakai sekarang, tanpa menunggu domain produksi dipilih.
- Web, API, dan foto sebaiknya berada di satu domain yang dikelola di Cloudflare, karena tunnel API dan custom domain R2 sama-sama dipasang di sana.
- Tautan yang sudah dibagikan ke alamat web lama sebaiknya tetap jalan.
- Server, tunnel, database, dan cara deploy di ADR-0026 tidak perlu berubah.

## Opsi yang Dipertimbangkan

1. Subdomain aldio.dev untuk web, API, dan foto, dengan `akses-bogor.vercel.app` tetap sebagai alias
2. Tetap di `akses-bogor.vercel.app` dan yorhagarden.xyz
3. Menunggu domain produksi sebelum memindahkan alamat

## Keputusan

Opsi terpilih: "Subdomain aldio.dev untuk web, API, dan foto", karena ketiganya bisa dipindah sekarang tanpa mengubah server atau cara deploy, dan alamat web lama tetap jalan.

| Bagian | Alamat staging | Alamat sebelumnya |
| --- | --- | --- |
| Web | `https://akses-bogor.aldio.dev`, custom domain di proyek Vercel yang sama | `https://akses-bogor.vercel.app`, tetap jalan sebagai alias |
| API | `https://akses-bogor-api.aldio.dev`, public hostname di Cloudflare Tunnel Evergarden yang sama | `https://akses-bogor-api.yorhagarden.xyz`, sudah dihapus |
| Foto | `https://akses-bogor-foto.aldio.dev`, custom domain R2 di bucket `akses-bogor-foto` | `https://akses-bogor-foto.yorhagarden.xyz`, sudah dihapus |

Konfigurasi yang ikut berubah:

| Tempat | Nilai |
| --- | --- |
| `FRONTEND_ORIGINS` di `/etc/akses-bogor/api.env` | `https://akses-bogor.aldio.dev,https://akses-bogor.vercel.app`. Origin pertama dipakai untuk tautan di email status laporan ([ADR-0039](0039-email-status-laporan.md)) |
| `BETTER_AUTH_URL` di `/etc/akses-bogor/api.env` | `https://akses-bogor-api.aldio.dev` |
| `PHOTO_PUBLIC_URL` di `/etc/akses-bogor/api.env` | `https://akses-bogor-foto.aldio.dev` |
| `API_URL` di Vercel | `https://akses-bogor-api.aldio.dev` |
| Klien OAuth Google | `https://akses-bogor.aldio.dev` ditambahkan sebagai Authorized JavaScript origin |

Yang tidak berubah: server Evergarden, tunnel, database, deploy dari branch `release`, dan web di Vercel (ADR-0026, [ADR-0013](0013-tech-stack-nextjs-fastify.md)). Domain pengirim email ([ADR-0030](0030-email-transaksional-resend.md)) tidak dibahas di ADR ini.

Domain produksi akhir belum diputuskan, dan tidak akan berupa alamat `vercel.app`. Domain itu dipilih di ADR tersendiri. Saat itu, langkah "domain sendiri" di bagian 4 [Daftar Periksa Rilis Pertama](../operations/rilis-pertama.md) dipakai lagi.

## Konsekuensi

- Positif: web, API, dan foto berada di satu domain, dan alamatnya tidak lagi tersebar di dua domain dan `vercel.app`.
- Positif: tautan lama ke `akses-bogor.vercel.app`, termasuk tautan di email status laporan yang sudah terkirim, tetap jalan.
- Positif: alamat foto di respons API dibentuk dari `PHOTO_PUBLIC_URL` saat diminta, jadi data di database tidak perlu diubah.
- Negatif: alamat API dan foto di yorhagarden.xyz tidak bisa dibuka lagi. Siapa pun yang memanggil alamat API lama secara langsung harus pindah ke alamat baru.
- Negatif: sesi masuk disimpan di cookie `__Host-` milik server web ([ADR-0029](0029-akun-better-auth-dan-sesi-lewat-server-web.md)), yang terikat ke satu domain. Pengguna yang masuk di `akses-bogor.vercel.app` perlu masuk lagi di `akses-bogor.aldio.dev`.
- Negatif: tautan sumber halaman edukasi di basis pengetahuan agen ([ADR-0042](0042-agen-rute-jalan-kaki-dan-informasi-aksesibilitas.md)) baru memakai alamat baru setelah dokumen dimuat ulang dengan `pnpm pengetahuan:dokumen` di server. Sampai saat itu tautannya tetap jalan lewat alias.
- Negatif: saat domain produksi dipilih, alamat dipindah sekali lagi: origin Google, `FRONTEND_ORIGINS`, `BETTER_AUTH_URL`, `PHOTO_PUBLIC_URL`, `API_URL`, tautan di OAuth consent screen, basis pengetahuan, dan dokumentasi.

## Pro & Kontra tiap Opsi

### Subdomain aldio.dev untuk web, API, dan foto

- Pro: bisa dijalankan sekarang dengan tunnel, bucket, dan proyek Vercel yang sudah ada.
- Pro: satu domain untuk ketiga bagian, dan alias `vercel.app` menjaga tautan lama.
- Kontra: alamat ini sementara, jadi akan ada perpindahan lagi saat domain produksi dipilih.
- Kontra: pengguna yang sudah masuk perlu masuk lagi di alamat baru.

### Tetap di `akses-bogor.vercel.app` dan yorhagarden.xyz

- Pro: tidak ada yang perlu diubah.
- Kontra: alamat tersebar di dua domain dan `vercel.app`, padahal domain produksi tidak akan berupa alamat `vercel.app`.

### Menunggu domain produksi

- Pro: alamat hanya dipindah sekali.
- Kontra: domain produksi belum diputuskan, jadi tim tetap memakai alamat lama untuk waktu yang belum jelas.

## Referensi

- Issue #81
- [ADR-0026](0026-api-dan-postgresql-di-evergarden-oci.md): API dan PostgreSQL di Evergarden, hostname API sebelumnya
- [ADR-0031](0031-foto-laporan-di-bucket-r2-privat.md): bucket publik di balik `PHOTO_PUBLIC_URL`
- [Daftar Periksa Rilis Pertama](../operations/rilis-pertama.md)
- akses-bogor-api#77 dan akses-bogor-web#108: alamat di contoh konfigurasi, README, dan tes
