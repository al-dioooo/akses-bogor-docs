# AksesBogor Docs

Repositori dokumentasi proyek **AksesBogor**, platform informasi aksesibilitas fasilitas publik untuk difabel. Repositori ini adalah sumber kebenaran (*single source of truth*) untuk semua keputusan proyek: produk, desain, teknis, dan proses kerja tim.

## Ringkasan Proyek

AksesBogor membantu difabel menemukan fasilitas publik yang ramah akses, seperti kampus, masjid, halte, dan rumah sakit, melalui peta, pencarian, dan halaman detail lokasi. Data lokasi dikumpulkan dari laporan warga yang diverifikasi tim sebelum ditayangkan.

Referensi desain: [Figma: AksesBogor](https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor)

## Struktur Repositori

| Path | Isi |
| --- | --- |
| `.github/` | Template pull request, template issue, dan workflow CI |

## Tech Stack

- Markdown (GitHub Flavored Markdown), dibaca langsung di GitHub
- [markdownlint-cli2](https://github.com/DavidAnson/markdownlint-cli2) untuk linting
- GitHub Actions untuk CI
- GitHub Issues sebagai ticket tracker

## Setup & Run

Prasyarat: Git dan Node.js 20 atau lebih baru (hanya untuk menjalankan lint).

```bash
git clone git@github.com:al-dioooo/akses-bogor-docs.git
cd akses-bogor-docs
npx --yes markdownlint-cli2@0.23.3 "**/*.md"
```

## Environment Variables

Tidak ada. Jangan commit `.env`, API key, atau file log.

## Deployment

Tidak ada proses deployment; dokumen dibaca langsung di GitHub.

- `main`: versi terbaru yang sudah di-review dan di-merge.
- `release`: versi dokumen yang sudah ditetapkan (mis. setelah review milestone).
