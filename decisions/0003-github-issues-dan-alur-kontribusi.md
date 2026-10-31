# ADR-0003: GitHub Issues sebagai Ticket Tracker dan Alur Kontribusi Repo Docs

- Status: Accepted
- Tanggal: 2026-10-21
- Pengambil keputusan: Tim AksesBogor
- Issue: #2

## Konteks dan Masalah

PR guideline mewajibkan ticket ID di judul PR dan issue yang tertaut. Repository guideline mewajibkan trunk-based development (`main` dan `release`), nama branch sesuai jenis perubahan, larangan commit langsung ke `main`, dan CI yang lulus sebelum merge. Repo docs perlu menerapkan aturan itu secara konkret, termasuk memilih ticket tracker.

## Decision Drivers

- Memenuhi kedua guideline tim.
- Tool sesedikit mungkin dan tanpa biaya (proyek kampus).
- Issue, pull request, dan dokumen berada di tempat yang sama agar mudah dilacak.

## Opsi yang Dipertimbangkan

1. GitHub Issues di repo ini
2. Jira atau Linear dengan key proyek (mis. `AKB-123`)
3. Tanpa ticket ID untuk repo docs; hanya repo kode yang memakai ticket

## Keputusan

Opsi terpilih: "GitHub Issues di repo ini", karena gratis, sudah menyatu dengan pull request, dan tidak menambah tool baru.

Aturan turunan:

- Judul PR: `<type>(<scope>): <deskripsi> (#<nomor-issue>)`. Body PR memuat `Closes #<nomor-issue>`.
- Branch: `docs/` untuk isi dokumen, `fix/` untuk koreksi kecil, `chore/` untuk pemeliharaan repo. Rinciannya ada di [CONTRIBUTING.md](../CONTRIBUTING.md).
- `main` hanya diubah lewat PR. Pengecualian: commit bootstrap saat repo masih kosong (dipakai sekali pada 2026-10-21) dan perbaikan darurat.
- `release` diperbarui dari `main` lewat PR pada titik milestone.
- CI markdownlint harus lulus sebelum merge.
- Repo dihosting privat di GitHub: `al-dioooo/akses-bogor-docs`.

## Konsekuensi

- Positif: jejak issue, PR, dan ADR berada di satu tempat dan saling tertaut otomatis.
- Positif: tidak ada biaya atau akun tambahan.
- Negatif: GitHub Issues tidak punya fitur sprint dan estimasi selengkap Jira. Bila dibutuhkan, tambahkan GitHub Projects tanpa mengganti tracker.
- Negatif: nomor issue berlaku per repo. Bila nanti ada repo kode terpisah, referensi lintas repo ditulis `owner/repo#nomor`.

## Pro & Kontra tiap Opsi

### GitHub Issues

- Pro: gratis, menyatu dengan PR (`Closes #n` menutup issue otomatis).
- Kontra: fitur perencanaan terbatas tanpa GitHub Projects.

### Jira atau Linear

- Pro: fitur perencanaan, sprint, dan pelaporan lengkap.
- Kontra: tool dan akun tambahan; tautan ke PR butuh integrasi.

### Tanpa ticket ID untuk repo docs

- Pro: paling ringan untuk perubahan kecil.
- Kontra: melanggar PR guideline bagian 3.3; diskusi sebelum menulis ADR tidak tercatat.

## Referensi

- [Pull Request & Code Review Guideline](../guidelines/pull-request-code-review-guideline.md), bagian 3.3 "Pull Request Structure"
- [Github Repository Guideline](../guidelines/github-repository-guideline.md), bagian 3 "Branching Strategy"
- [CONTRIBUTING.md](../CONTRIBUTING.md)
