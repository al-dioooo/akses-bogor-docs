# ADR-0032: Pencarian Tempat di Formulir Laporan Lewat API dengan Mapbox Search Box

- Status: Accepted
- Tanggal: 2026-11-07
- Pengambil keputusan: Tim AksesBogor
- Issue: #54
- Melengkapi: [ADR-0016](0016-peta-dan-pencarian-mapbox.md) (rincian pencarian)

## Konteks dan Masalah

[ADR-0015](0015-input-lokasi-peta-gps-pencarian.md) mewajibkan pencarian nama tempat atau alamat di pemilih lokasi, sebagai jalur tanpa menyeret peta. [ADR-0016](0016-peta-dan-pencarian-mapbox.md) memilih layanan pencarian Mapbox yang dibatasi ke Kota Bogor, tetapi belum memilih API-nya, apakah browser memanggilnya langsung, dan token apa yang dipakai. Geocoding API v6 Mapbox tidak lagi memuat tempat (POI), padahal pelapor biasanya mencari nama masjid, kampus, halte, atau rumah sakit.

## Decision Drivers

- Hasil memuat nama tempat, bukan hanya jalan.
- Alamat IP dan kata kunci pelapor sesedikit mungkin sampai ke pihak lain.
- Biaya tetap di kuota gratis dan tidak bisa dihabiskan satu klien.
- Tidak ada rahasia baru untuk diatur tim.
- Hasil bisa dipilih dengan keyboard dan pembaca layar ([ADR-0021](0021-wcag-22-aa-dan-pengujian-berlapis.md)).

## Opsi yang Dipertimbangkan

1. Mapbox Search Box API lewat API AksesBogor
2. Mapbox Search Box API dipanggil browser dengan token peta sementara
3. Mapbox Geocoding API v6 (alamat saja)

## Keputusan

Opsi terpilih: "Search Box lewat API".

- Browser mengirim kata kunci ke server Next.js, yang meneruskannya ke `POST /map/search` di API. Kata kunci dikirim di badan permintaan, bukan di URL, supaya tidak tercatat di log server.
- API memanggil endpoint `/search/searchbox/v1/forward` dengan `auto_complete=true`, kotak batas sekitar Kota Bogor, kedekatan ke tengah peta yang sedang dilihat pelapor, `country=id`, `language=id`, dan paling banyak 5 hasil berjenis tempat, alamat, atau jalan.
- Sebelum hasil Mapbox, API menampilkan paling banyak 3 lokasi AksesBogor yang sudah terbit dengan nama mirip, diberi label "Sudah ada di AksesBogor". Ini membantu mencegah laporan ganda (FR-23).
- Memilih hasil hanya menggeser peta. Hasil tidak disimpan, tidak di-cache, dan yang disimpan tetap pin yang dikonfirmasi pelapor ([ADR-0015](0015-input-lokasi-peta-gps-pencarian.md)).
- API memakai `MAPBOX_SECRET_TOKEN` yang sudah ada. Token tidak pernah sampai ke browser, dan tidak ada variabel baru.
- Browser mencari setelah pelapor berhenti mengetik 400 ms dengan minimal 3 huruf, atau saat menekan Enter. Jumlah hasil diumumkan ke pembaca layar, dan daftar hasil memakai pola combobox.

### Batas

- 30 pencarian per menit per klien.
- Paling banyak 1.500 pencarian per hari untuk seluruh pengguna, dihitung ulang setiap tengah malam WIB. Setelah itu API menjawab 503 `pencarian_penuh`, dan formulir mengarahkan ke peta atau "Gunakan lokasi saya".
- Batas per klien baru berlaku per pengunjung setelah server web mengirim IP pengunjung ke API (akses-bogor-api#17, akses-bogor-web#27). Sebelum itu, semua pengunjung lewat Vercel berbagi batas yang sama.

## Konsekuensi

- Positif: pelapor bisa menemukan tempat dengan namanya, dan lokasi yang sudah ada muncul lebih dulu.
- Positif: Mapbox menerima kata kunci dari server, bukan alamat IP pelapor.
- Positif: Search Box `/forward` ditagih per permintaan, gratis 50.000 per bulan, lalu US$1 per 1.000. Batas harian menjaga pemakaian di bawah kuota gratis.
- Negatif: setiap pencarian lewat Vercel dan Evergarden, jadi sedikit lebih lambat daripada memanggil Mapbox langsung.
- Negatif: batas harian bisa tercapai saat ada acara pengumpulan data. Naikkan batasnya sebelum acara bila tim siap membayar kelebihannya.
- Netral: data dari Search Box hanya boleh dipakai sementara menurut ketentuan Mapbox; keputusan ini memang tidak menyimpannya.

## Pro & Kontra tiap Opsi

### Search Box lewat API

- Pro: memuat POI; IP pelapor tidak ke Mapbox; batas biaya di satu tempat; tanpa rahasia baru.
- Kontra: dua lompatan jaringan tambahan.

### Search Box dari browser

- Pro: paling cepat; tidak membebani server.
- Kontra: IP dan kata kunci pelapor ke Mapbox; dokumentasi Mapbox tidak menyebut bahwa token sementara bisa dipakai untuk pencarian; tidak ada batas biaya di server.

### Geocoding API v6

- Pro: kuota gratis lebih besar (100.000 per bulan).
- Kontra: tanpa POI, sehingga mencari "Masjid Raya" hanya menemukan jalan.

## Referensi

- [ADR-0015](0015-input-lokasi-peta-gps-pencarian.md), [ADR-0016](0016-peta-dan-pencarian-mapbox.md), [ADR-0021](0021-wcag-22-aa-dan-pengujian-berlapis.md), [ADR-0029](0029-akun-better-auth-dan-sesi-lewat-server-web.md)
- [PRD](../product/prd.md), FR-21 dan FR-23
- [Mapbox Search Box API](https://docs.mapbox.com/api/search/search-box/), [Harga Mapbox](https://www.mapbox.com/pricing)
