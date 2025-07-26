# FAQ (Frequently Asked Questions)

Kumpulan pertanyaan yang sering diajukan tentang RhamaaCMS beserta jawabannya.

## Instalasi dan Setup

### Q: Versi Python apa yang didukung RhamaaCMS?

**A:** RhamaaCMS mendukung Python 3.8 ke atas. Kami merekomendasikan menggunakan Python 3.11 untuk performa terbaik. Pastikan versi Python Anda kompatibel dengan [versi Wagtail yang digunakan](https://docs.wagtail.org/en/stable/releases/upgrading.html#compatible-django-python-versions).

### Q: Apakah saya harus menggunakan virtual environment?

**A:** Ya, sangat disarankan menggunakan virtual environment untuk mengisolasi dependencies proyek. Ini mencegah konflik dengan package Python lain di sistem Anda.

### Q: Bagaimana cara mengatasi error "wagtail command not found"?

**A:** Pastikan:
1. Virtual environment sudah aktif
2. Wagtail sudah terinstall: `pip install wagtail`
3. PATH environment variable sudah benar

### Q: Apakah bisa menggunakan database selain SQLite?

**A:** Ya, RhamaaCMS mendukung PostgreSQL, MySQL, dan database lain yang didukung Django. Update konfigurasi di `settings/base.py` sesuai database pilihan Anda.

## Penggunaan dan Development

### Q: Bagaimana cara menambah halaman baru?

**A:** 
1. Buat model page baru di `models.py`
2. Buat template di folder `templates/`
3. Jalankan `makemigrations` dan `migrate`
4. Tambahkan halaman melalui admin panel

### Q: Bagaimana cara membuat custom block?

**A:** Buat class yang inherit dari `blocks.StructBlock`:

```python
class CustomBlock(blocks.StructBlock):
    title = blocks.CharBlock()
    content = blocks.RichTextBlock()
    
    class Meta:
        template = 'blocks/custom_block.html'
```

### Q: Apakah bisa mengubah tema admin Wagtail?

**A:** Ya, Anda bisa mengkustomisasi admin interface dengan:
1. Override template admin
2. Menambah custom CSS/JS
3. Menggunakan third-party packages seperti `wagtail-admin-theme`

### Q: Bagaimana cara backup data?

**A:** Gunakan Django management commands:
```bash
# Backup ke JSON
python manage.py dumpdata > backup.json

# Backup database (PostgreSQL)
pg_dump database_name > backup.sql
```

## Deployment

### Q: Platform deployment mana yang direkomendasikan?

**A:** RhamaaCMS dapat di-deploy ke berbagai platform:
- **Fly.io** - Mudah dan cepat untuk small-medium apps
- **Divio Cloud** - Optimized untuk Django apps
- **Heroku** - Popular dan user-friendly
- **DigitalOcean** - Flexible dan cost-effective
- **AWS/GCP** - Untuk enterprise applications

### Q: Apakah perlu menggunakan CDN untuk static files?

**A:** Untuk production, sangat disarankan menggunakan CDN untuk:
- Mempercepat loading static files
- Mengurangi load server
- Meningkatkan user experience

### Q: Bagaimana cara setup HTTPS?

**A:** Tergantung platform deployment:
- **Fly.io/Divio**: Otomatis tersedia
- **Custom server**: Gunakan Let's Encrypt atau SSL certificate
- Update `SECURE_SSL_REDIRECT = True` di settings

### Q: Bagaimana cara mengatasi error 500 di production?

**A:** 
1. Check logs aplikasi
2. Pastikan `DEBUG = False`
3. Verifikasi `ALLOWED_HOSTS` sudah benar
4. Check database connection
5. Pastikan static files sudah di-collect

## Performance dan Optimization

### Q: Bagaimana cara mempercepat loading website?

**A:** Beberapa tips optimization:
1. Enable caching (Redis/Memcached)
2. Optimize database queries
3. Compress dan minify static files
4. Gunakan CDN
5. Optimize images
6. Enable gzip compression

### Q: Apakah perlu menggunakan caching?

**A:** Ya, untuk production disarankan menggunakan:
- **Page caching** untuk halaman yang jarang berubah
- **Database caching** untuk query yang expensive
- **Template fragment caching** untuk komponen yang berat

### Q: Bagaimana cara monitoring performance?

**A:** Gunakan tools seperti:
- Django Debug Toolbar (development)
- New Relic atau Datadog (production)
- Google PageSpeed Insights
- GTmetrix untuk web performance

## Keamanan

### Q: Bagaimana cara mengamankan admin panel?

**A:** 
1. Gunakan password yang kuat
2. Enable two-factor authentication
3. Limit akses berdasarkan IP
4. Regular update dependencies
5. Monitor login attempts

### Q: Apakah perlu SSL certificate?

**A:** Ya, SSL certificate wajib untuk:
- Keamanan data
- SEO ranking
- User trust
- Compliance requirements

### Q: Bagaimana cara backup yang aman?

**A:** 
1. Encrypt backup files
2. Store di multiple locations
3. Regular backup schedule
4. Test restore process
5. Limit access ke backup files

## Troubleshooting

### Q: Website loading sangat lambat, apa yang harus dilakukan?

**A:** 
1. Check database queries dengan Django Debug Toolbar
2. Enable caching
3. Optimize images dan static files
4. Check server resources
5. Review third-party integrations

### Q: Error "Template not found", bagaimana mengatasinya?

**A:** 
1. Check path template di `TEMPLATES` setting
2. Pastikan nama file template benar
3. Verifikasi struktur folder templates
4. Check case sensitivity (Linux/macOS)

### Q: Bagaimana cara debug error di production?

**A:** 
1. Check application logs
2. Enable logging untuk specific modules
3. Use error tracking tools (Sentry)
4. Reproduce error di staging environment

### Q: Media files tidak muncul di production?

**A:** 
1. Check `MEDIA_URL` dan `MEDIA_ROOT` settings
2. Pastikan web server serve media files
3. Untuk cloud storage, check credentials
4. Verify file permissions

## Customization

### Q: Bagaimana cara mengubah tampilan homepage?

**A:** 
1. Edit model `HomePage` di `home/models.py`
2. Update template `home/home_page.html`
3. Modify CSS di static files
4. Jalankan migrations jika ada perubahan model

### Q: Apakah bisa menambah field custom ke page?

**A:** Ya, tambahkan field di model page:

```python
class HomePage(Page):
    custom_field = models.CharField(max_length=255)
    
    content_panels = Page.content_panels + [
        FieldPanel('custom_field'),
    ]
```

### Q: Bagaimana cara membuat menu navigation custom?

**A:** 
1. Buat model untuk menu items
2. Register di admin
3. Create template tag untuk render menu
4. Include di base template

## Integrasi

### Q: Apakah bisa integrasi dengan API external?

**A:** Ya, Anda bisa:
1. Buat service classes untuk API calls
2. Use Django REST framework untuk API endpoints
3. Implement caching untuk API responses
4. Handle error dan timeout dengan baik

### Q: Bagaimana cara integrasi dengan payment gateway?

**A:** 
1. Install package yang sesuai (stripe, paypal, dll)
2. Buat models untuk orders/transactions
3. Implement webhook handlers
4. Add security measures

### Q: Apakah mendukung multi-language?

**A:** Ya, gunakan:
1. Django internationalization (i18n)
2. Wagtail localization
3. `wagtail-localize` package untuk translation management

## Support

### Q: Dimana bisa mendapat bantuan jika ada masalah?

**A:** 
1. **Dokumentasi**: Baca dokumentasi lengkap
2. **GitHub Issues**: Report bugs atau request features
3. **Wagtail Community**: Slack channel dan forum
4. **Stack Overflow**: Tag dengan `wagtail` dan `django`

### Q: Bagaimana cara berkontribusi ke RhamaaCMS?

**A:** 
1. Fork repository di GitHub
2. Buat branch untuk feature/bugfix
3. Follow coding standards
4. Submit pull request dengan deskripsi yang jelas
5. Baca [panduan kontribusi](development/contributing.md)

### Q: Apakah ada roadmap development?

**A:** Ya, check GitHub repository untuk:
- Planned features
- Current milestones
- Issue labels untuk priority

---

**Tidak menemukan jawaban yang Anda cari?** 

Silakan buka issue di [GitHub repository](https://github.com/rhamaa/RhamaaCMS) atau hubungi komunitas untuk bantuan lebih lanjut.