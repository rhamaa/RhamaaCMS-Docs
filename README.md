# RhamaaCMS Documentation

Dokumentasi lengkap untuk RhamaaCMS - Template Wagtail untuk pengembangan cepat.

## Tentang RhamaaCMS

RhamaaCMS adalah template proyek yang dirancang untuk membuat build [Wagtail](https://wagtail.org) dengan cepat. Template ini dilengkapi dengan halaman pre-defined, blok konten, fungsionalitas lengkap, dan data fixtures untuk mempercepat proses pengembangan.

## Fitur Dokumentasi

- ✨ **Panduan Instalasi** - Langkah demi langkah setup RhamaaCMS
- 🚀 **Quick Start** - Mulai dengan cepat dalam hitungan menit
- 📖 **Panduan Lengkap** - Dokumentasi mendalam untuk semua fitur
- 🎨 **Kustomisasi** - Cara mengkustomisasi template dan tampilan
- 🚢 **Deployment** - Deploy ke berbagai platform (Fly.io, Divio, dll)
- 🧪 **Testing** - Panduan testing dan quality assurance
- 🤝 **Kontribusi** - Cara berkontribusi pada proyek

## Menjalankan Dokumentasi Lokal

### Persyaratan

- Python 3.8+
- pip

### Instalasi

```bash
# Clone repository
git clone https://github.com/rhamaa/RhamaaCMS-Docs.git
cd RhamaaCMS-Docs

# Install dependencies
pip install -r requirements.txt
```

### Menjalankan Server

```bash
# Jalankan development server
mkdocs serve

# Akses di browser
# http://localhost:8000
```

### Build Dokumentasi

```bash
# Build static files
mkdocs build

# Output akan tersimpan di folder 'site/'
```

## Struktur Dokumentasi

```
docs/
├── index.md                    # Halaman utama
├── getting-started/           # Panduan memulai
│   ├── installation.md       # Instalasi
│   ├── initial-setup.md      # Setup awal
│   └── running-server.md     # Menjalankan server
├── guide/                    # Panduan penggunaan
│   ├── project-structure.md  # Struktur proyek
│   ├── pages-blocks.md       # Halaman dan blok
│   ├── content-management.md # Mengelola konten
│   └── template-customization.md # Kustomisasi template
├── deployment/               # Panduan deployment
│   ├── flyio.md             # Deploy ke Fly.io
│   ├── divio.md             # Deploy ke Divio Cloud
│   └── other-servers.md     # Server lainnya
├── development/              # Panduan development
│   ├── contributing.md      # Kontribusi
│   ├── template-structure.md # Struktur template
│   └── testing.md           # Testing
└── faq.md                   # FAQ
```

## Kontribusi

Kami menyambut kontribusi untuk meningkatkan dokumentasi ini! Berikut cara berkontribusi:

### Melaporkan Issues

Jika Anda menemukan kesalahan atau informasi yang kurang jelas:

1. Buka [GitHub Issues](https://github.com/rhamaa/RhamaaCMS-Docs/issues)
2. Pilih template yang sesuai
3. Berikan deskripsi yang jelas

### Mengirim Pull Request

1. Fork repository ini
2. Buat branch baru: `git checkout -b improve-documentation`
3. Lakukan perubahan
4. Test perubahan: `mkdocs serve`
5. Commit perubahan: `git commit -m "Improve installation guide"`
6. Push ke branch: `git push origin improve-documentation`
7. Buat Pull Request

### Guidelines Kontribusi

- Gunakan bahasa Indonesia yang jelas dan mudah dipahami
- Sertakan contoh kode yang dapat dijalankan
- Test semua instruksi sebelum submit
- Ikuti struktur dokumentasi yang ada
- Gunakan screenshot jika membantu pemahaman

## Teknologi yang Digunakan

- **[MkDocs](https://www.mkdocs.org/)** - Static site generator
- **[Material for MkDocs](https://squidfunk.github.io/mkdocs-material/)** - Theme yang modern dan responsive
- **[Python Markdown Extensions](https://python-markdown.github.io/extensions/)** - Extended markdown features
- **[MkDocstrings](https://mkdocstrings.github.io/)** - API documentation

## Konfigurasi

Dokumentasi dikonfigurasi melalui file `mkdocs.yml`:

```yaml
site_name: RhamaaCMS Documentation
theme:
  name: material
  palette:
    - scheme: default
      primary: teal
      accent: teal
nav:
  - Beranda: index.md
  - Memulai: getting-started/
  - Panduan: guide/
  - Deployment: deployment/
  - Development: development/
  - FAQ: faq.md
```

## Deployment

Dokumentasi ini dapat di-deploy ke berbagai platform:

### GitHub Pages

```bash
# Deploy ke GitHub Pages
mkdocs gh-deploy
```

### Netlify

1. Connect repository ke Netlify
2. Set build command: `mkdocs build`
3. Set publish directory: `site`

### Vercel

1. Import project ke Vercel
2. Set build command: `mkdocs build`
3. Set output directory: `site`

## Lisensi

Dokumentasi ini dilisensikan di bawah [MIT License](LICENSE).

## Links

- **RhamaaCMS Repository**: [https://github.com/rhamaa/RhamaaCMS](https://github.com/rhamaa/RhamaaCMS)
- **Wagtail Documentation**: [https://docs.wagtail.org/](https://docs.wagtail.org/)
- **Django Documentation**: [https://docs.djangoproject.com/](https://docs.djangoproject.com/)

## Support

Jika Anda membutuhkan bantuan:

1. **Baca dokumentasi** - Cari di dokumentasi terlebih dahulu
2. **Check FAQ** - Lihat pertanyaan yang sering diajukan
3. **GitHub Issues** - Buat issue untuk bug atau pertanyaan
4. **Discussions** - Gunakan GitHub Discussions untuk diskusi umum

---

**Happy coding dengan RhamaaCMS!** 🚀

Dibuat dengan ❤️ oleh [Rhamaa](https://github.com/rhamaa)