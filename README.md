# RhamaaCMS Documentation

Complete documentation for RhamaaCMS - Wagtail template for rapid development.

Available in multiple languages:
- 🇺🇸 **English** (Default)
- 🇮🇩 **Bahasa Indonesia**

## About RhamaaCMS

RhamaaCMS is a project template designed to create [Wagtail](https://wagtail.org) builds quickly. The template comes with pre-defined pages, content blocks, complete functionality, and data fixtures to accelerate the development process.

## Documentation Features

- ✨ **Installation Guide** - Step-by-step RhamaaCMS setup
- 🚀 **Quick Start** - Get started in minutes
- 📖 **Complete Guide** - In-depth documentation for all features
- 🎨 **Customization** - How to customize templates and appearance
- 🚢 **Deployment** - Deploy to various platforms (Fly.io, Divio, etc.)
- 🧪 **Testing** - Testing and quality assurance guide
- 🤝 **Contributing** - How to contribute to the project
- 🌍 **Multi-language** - Available in English and Indonesian

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

## Documentation Structure

```
docs/
├── index.md                    # Main page (English)
├── id/                        # Indonesian documentation
│   ├── index.md              # Main page (Indonesian)
│   ├── getting-started/      # Getting started guide
│   ├── guide/                # User guide
│   ├── deployment/           # Deployment guide
│   ├── development/          # Development guide
│   └── faq.md               # FAQ
├── getting-started/           # Getting started guide (English)
│   ├── installation.md       # Installation
│   ├── initial-setup.md      # Initial setup
│   └── running-server.md     # Running server
├── guide/                    # User guide (English)
│   ├── project-structure.md  # Project structure
│   ├── pages-blocks.md       # Pages and blocks
│   ├── content-management.md # Content management
│   └── template-customization.md # Template customization
├── deployment/               # Deployment guide (English)
│   ├── flyio.md             # Deploy to Fly.io
│   ├── divio.md             # Deploy to Divio Cloud
│   └── other-servers.md     # Other servers
├── development/              # Development guide (English)
│   ├── contributing.md      # Contributing
│   ├── template-structure.md # Template structure
│   └── testing.md           # Testing
└── faq.md                   # FAQ (English)
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

### Contribution Guidelines

- Use clear and easy-to-understand language
- Include runnable code examples
- Test all instructions before submitting
- Follow existing documentation structure
- Use screenshots if they help understanding
- For Indonesian content, place files in `docs/id/` folder
- For English content, place files in `docs/` folder

## Technologies Used

- **[MkDocs](https://www.mkdocs.org/)** - Static site generator
- **[Material for MkDocs](https://squidfunk.github.io/mkdocs-material/)** - Modern and responsive theme
- **[Python Markdown Extensions](https://python-markdown.github.io/extensions/)** - Extended markdown features
- **[MkDocstrings](https://mkdocstrings.github.io/)** - API documentation
- **[mkdocs-static-i18n](https://github.com/ultrabug/mkdocs-static-i18n)** - Multi-language support

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