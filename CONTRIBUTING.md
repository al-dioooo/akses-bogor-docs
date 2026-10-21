# Panduan Kontribusi

Repositori ini mengikuti dua guideline tim:

- [Github Repository Guideline](guidelines/github-repository-guideline.md)
- [Pull Request & Code Review Guideline](guidelines/pull-request-code-review-guideline.md)

Dokumen ini menjelaskan cara menerapkan kedua guideline tersebut pada repo dokumentasi. Alasan di balik aturan ini dicatat di [ADR-0002](decisions/0002-bahasa-indonesia-untuk-dokumentasi.md) (bahasa) dan [ADR-0003](decisions/0003-github-issues-dan-alur-kontribusi.md) (ticket tracker dan alur kontribusi). Cara menulis ADR ada di [decisions/README.md](decisions/README.md).

## Alur Kerja

1. **Buat atau temukan issue** di GitHub Issues. Pakai template "Usulan keputusan (ADR)" untuk keputusan dan "Perubahan dokumen" untuk perubahan lain.
2. **Buat branch** dari `main` (atau dari branch PR sebelumnya bila stacked).
3. **Tulis perubahan** dalam commit kecil, satu perubahan logis per commit.
4. **Jalankan lint** sebelum push.
5. **Buka pull request** dengan nomor issue di judul.
6. **Review**: reviewer memberi masukan, penulis merevisi, ulangi sampai disetujui.
7. **Merge** setelah CI lulus, semua diskusi selesai, dan reviewer yang diminta menyetujui.

## Penamaan Branch

| Prefix | Dipakai untuk | Contoh |
| --- | --- | --- |
| `docs/` | Menambah atau mengubah isi dokumen (ADR, referensi produk, guideline) | `docs/adr-map-provider` |
| `fix/` | Koreksi kecil: typo, link rusak, data yang salah | `fix/broken-figma-links` |
| `chore/` | Pemeliharaan repo: CI, template, konfigurasi | `chore/update-markdownlint` |

Guideline meminta nama branch "sesuai perubahan yang dilakukan". Di repo docs, perubahan isi memakai `docs/` agar sejalan dengan tipe commit `docs`.

Dilarang commit langsung ke `main`. Pengecualian hanya untuk commit bootstrap saat repo masih kosong dan perbaikan darurat.

## Commit

Pakai [Conventional Commits](https://www.conventionalcommits.org/) dalam bahasa Inggris, mengikuti contoh di guideline: `type(scope): description`.

| Tipe | Kapan |
| --- | --- |
| `docs` | Menambah atau mengubah isi dokumen |
| `fix` | Mengoreksi isi yang salah (typo, link, data) |
| `style` | Merapikan format tanpa mengubah isi |
| `chore` | Pemeliharaan repo |
| `ci` | Perubahan workflow CI |

Scope yang dipakai: `guidelines`, `adr`, `product`, `design`, `repo`.

```text
docs(adr): add map provider decision
fix(product): correct IPB location in sample data
style(guidelines): fix markdown lint issues
```

Untuk ADR: satu ADR per commit.

## Pull Request

- **Judul**: `<type>(<scope>): <deskripsi> (#<nomor-issue>)`, mis. `docs(adr): add map provider decision (#12)`.
- **Body**: ikuti [template PR](.github/pull_request_template.md) dan tautkan issue dengan `Closes #<nomor>`.
- **Ukuran**: satu topik per PR, usahakan di bawah 500 baris.
- **Stacked PR**: bila perubahan saling bergantung, base PR berikutnya adalah branch PR sebelumnya. Merge dari PR paling bawah.
- **Test Evidence** untuk docs: output lint, ditambah screenshot render GitHub bila ada tabel atau diagram yang rumit.
- **Breaking changes** untuk docs: ADR yang di-supersede, file yang dipindah atau di-rename, link dan anchor yang berubah.

## Review

Fokus reviewer di repo docs (adaptasi bagian 5 PR guideline):

- **Akurasi**: isi sesuai sumber (Figma node, issue, diskusi). Asumsi ditandai sebagai asumsi.
- **Kelengkapan ADR**: konteks, opsi, keputusan, dan konsekuensi terisi; status benar.
- **Konsistensi**: istilah, bahasa, dan link antar dokumen.
- **Keterlacakan**: ada referensi ke issue, PR, atau node Figma.

Bedakan *required change* dan *nit* (saran opsional).

## Branch `release`

`release` menandai versi dokumen yang sudah ditetapkan. Branch ini diperbarui dari `main` lewat PR `main → release` pada titik milestone (mis. setelah review sprint).

## Keamanan

Jangan commit `.env`, API key, token, file log, atau data pribadi pelapor (nama, kontak, foto yang memperlihatkan wajah).

## Menjalankan Lint

```bash
npx --yes markdownlint-cli2@0.23.3 "**/*.md"
```
