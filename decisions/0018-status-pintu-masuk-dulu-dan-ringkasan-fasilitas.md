# ADR-0018: Status "Pintu Masuk Dulu" dan Ringkasan "X dari Y Fasilitas"

- Status: Accepted
- Tanggal: 2026-10-21
- Pengambil keputusan: Tim AksesBogor
- Issue: #22

## Konteks dan Masalah

Desain menampilkan status (Accessible, Partial) dan Accessibility Score 65/100 berlabel "Baik", tanpa rumus yang jelas: 3 dari 4 fasilitas seharusnya 75 bila bobotnya sama ([PT-02](../product/pertanyaan-terbuka.md#pt-02-status-dan-accessibility-score)). Pengguna kursi roda dan pengguna tunanetra membutuhkan hal yang berbeda, sehingga satu angka mudah menyesatkan. Bagaimana status ditentukan, dan angka apa yang ditampilkan?

## Decision Drivers

- Status bisa dijelaskan ke pengguna dalam satu kalimat.
- Hasilnya konsisten, tidak bergantung pada verifikator yang memeriksa.
- Tidak ada kesan presisi palsu.
- Selaras dengan skema `wheelchair=*` di OpenStreetMap untuk impor dan ekspor data.

## Opsi yang Dipertimbangkan

1. Aturan "pintu masuk dulu" yang dihitung dari jawaban fasilitas, dengan ringkasan "X dari Y fasilitas"
2. Verifikator memilih status berdasarkan panduan tertulis, dengan skor 0–100
3. Skor berbobot 0–100, lalu batas nilai menentukan status

## Keputusan

Opsi terpilih: "Aturan pintu masuk dulu, dengan ringkasan X dari Y fasilitas". Hanya jawaban yang sudah diverifikasi yang dihitung ([ADR-0017](0017-empat-jawaban-ketersediaan-fasilitas.md)).

### Istilah

- **Fasilitas relevan**: fasilitas dengan jawaban selain "Tidak perlu".
- **Pintu masuk**: jawaban untuk Ramp / Jalur Landai. "Tidak perlu" berarti pintu masuk sudah rata.

### Aturan status, diperiksa berurutan

1. Ramp "Tidak tersedia" → **Tidak aksesibel**. Bila tidak bisa masuk, fasilitas lain tidak membantu.
2. Ramp "Belum tahu", atau setidaknya separuh fasilitas relevan "Belum tahu" → **Belum diketahui**.
3. Semua fasilitas relevan "Tersedia" → **Aksesibel**.
4. Selain itu → **Sebagian**.

### Ringkasan di halaman Detail

- Angka yang ditampilkan adalah "X dari Y fasilitas tersedia": X = fasilitas relevan yang Tersedia, Y = jumlah fasilitas relevan. Bila ada yang Belum diketahui, jumlahnya disebutkan terpisah.
- Komponen Score ring menampilkan X/Y dan nama status, bukan skor 0–100.
- Satu kalimat alasan menyertai status, misalnya "Sebagian: Toilet Difabel tidak tersedia".
- Tidak ada lagi Accessibility Score 0–100.

### Lain-lain

- Status dihitung ulang otomatis setiap kali jawaban terverifikasi berubah. Verifikator tidak menimpa status secara manual.
- Pemetaan kasar ke OpenStreetMap: Aksesibel → `wheelchair=yes`, Sebagian → `limited`, Tidak aksesibel → `no`, Belum diketahui → tanpa tag. Status AksesBogor juga mempertimbangkan guiding block, jadi pemetaan ini hanya perkiraan.

## Konsekuensi

- Positif: status konsisten dan bisa dijelaskan; pengguna melihat alasannya.
- Positif: ringkasan X dari Y jujur tentang apa yang sudah diketahui.
- Negatif: aturan yang sama untuk semua kategori bisa terasa kaku untuk kasus khusus; perubahan aturan butuh ADR baru.
- Negatif: Accessibility Score di desain dan PRD diganti, sehingga copy dan komponen Score ring perlu diperbarui.

## Pro & Kontra tiap Opsi

### Aturan pintu masuk dulu + X dari Y

- Pro: konsisten, bisa dijelaskan, tanpa presisi palsu.
- Kontra: kurang fleksibel untuk kasus khusus.

### Penilaian verifikator + skor 0–100

- Pro: menangkap nuansa yang tidak tercakup aturan.
- Kontra: berbeda antar verifikator; angka sulit dijelaskan.

### Skor berbobot → batas nilai

- Pro: satu angka yang mudah dibandingkan.
- Kontra: bobot sewenang-wenang; menyembunyikan kebutuhan yang berbeda per jenis disabilitas.

## Referensi

- [Pertanyaan Terbuka PT-02](../product/pertanyaan-terbuka.md#pt-02-status-dan-accessibility-score)
- [ADR-0017](0017-empat-jawaban-ketersediaan-fasilitas.md), [ADR-0019](0019-verifikasi-tim-dan-mitra-terlatih.md)
- [OpenStreetMap: Key:wheelchair](https://wiki.openstreetmap.org/wiki/Key:wheelchair)
