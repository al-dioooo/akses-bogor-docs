# AksesBogor Docs

Repositori dokumentasi proyek **AksesBogor**, platform informasi aksesibilitas fasilitas publik untuk difabel. Repositori ini adalah sumber kebenaran (*single source of truth*) untuk semua keputusan proyek: produk, desain, teknis, dan proses kerja tim.

## Ringkasan Proyek

AksesBogor membantu difabel menemukan fasilitas publik yang ramah akses, seperti kampus, masjid, halte, dan rumah sakit, melalui peta, pencarian, dan halaman detail lokasi. Data lokasi dikumpulkan dari laporan warga yang diverifikasi tim sebelum ditayangkan.

Referensi desain: [Figma: AksesBogor](https://www.figma.com/design/aTaMQUxVF2P1zQlJY3s4du/AksesBogor)

## Repositori Terkait

| Repo | Isi |
| --- | --- |
| [akses-bogor-web](https://github.com/al-dioooo/akses-bogor-web) | Aplikasi web Next.js di Vercel, mobile-first dan bisa dipasang sebagai PWA |
| [akses-bogor-api](https://github.com/al-dioooo/akses-bogor-api) | API Fastify di Azure App Service, dengan Neon Postgres + PostGIS |

Keduanya mengikuti [guideline tim](guidelines/) dan [ADR-0013](decisions/0013-tech-stack-nextjs-fastify.md).

## Struktur Repositori

| Path | Isi |
| --- | --- |
| `decisions/` | Decision log (ADR) format MADR, beserta template dan index |
| `product/` | PRD MVP, ringkasan produk, referensi desain Figma, dan pertanyaan terbuka |
| `design/` | Fondasi visual (tipografi, warna, layout) dan audit kontras |
| `operations/` | Panduan kerja operasional, mulai dari panduan verifikasi laporan |
| `guidelines/` | Guideline tim: repositori GitHub serta pull request & code review |
| `CONTRIBUTING.md` | Cara berkontribusi di repo ini (alur, branch, commit, PR, review) |
| `.github/` | Template pull request, template issue, dan workflow CI |

## Tech Stack

- Markdown (GitHub Flavored Markdown), dibaca langsung di GitHub
- [markdownlint-cli2](https://github.com/DavidAnson/markdownlint-cli2) untuk linting
- GitHub Actions untuk CI
- GitHub Issues sebagai ticket tracker

## Setup & Run

Prasyarat: Git dan Node.js 20 atau lebih baru (hanya untuk menjalankan lint).

```bash
git clone https://github.com/al-dioooo/akses-bogor-docs.git
cd akses-bogor-docs
npx --yes markdownlint-cli2@0.23.3 "**/*.md"
```

## Environment Variables

Tidak ada. Jangan commit `.env`, API key, atau file log.

## Deployment

Tidak ada proses deployment; dokumen dibaca langsung di GitHub.

- `main`: versi terbaru yang sudah di-review dan di-merge.
- `release`: versi dokumen yang sudah ditetapkan (mis. setelah review milestone).

## Kontribusi

Semua perubahan lewat issue, branch, dan pull request. Lihat [CONTRIBUTING.md](CONTRIBUTING.md).
