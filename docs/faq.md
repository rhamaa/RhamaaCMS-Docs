# FAQ (Frequently Asked Questions)

Common questions about RhamaaCMS and their answers.

## Installation and Setup

### Q: What Python versions does RhamaaCMS support?

**A:** RhamaaCMS supports Python 3.8 and above. We recommend using Python 3.11 for the best performance. Make sure your Python version is compatible with the [Wagtail version being used](https://docs.wagtail.org/en/stable/releases/upgrading.html#compatible-django-python-versions).

### Q: Do I need to use a virtual environment?

**A:** Yes, it's highly recommended to use a virtual environment to isolate project dependencies. This prevents conflicts with other Python packages on your system.

### Q: How do I fix "wagtail command not found" error?

**A:** Ensure:
1. Virtual environment is active
2. Wagtail is installed: `pip install wagtail`
3. PATH environment variable is correct

### Q: Can I use databases other than SQLite?

**A:** Yes, RhamaaCMS supports PostgreSQL, MySQL, and other Django-supported databases. Update the configuration in `settings/base.py` according to your chosen database.

## Usage and Development

### Q: How do I add a new page?

**A:** 
1. Create a new page model in `models.py`
2. Create a template in the `templates/` folder
3. Run `makemigrations` and `migrate`
4. Add the page through the admin panel

### Q: How do I create custom blocks?

**A:** Create a class that inherits from `blocks.StructBlock`:

```python
class CustomBlock(blocks.StructBlock):
    title = blocks.CharBlock()
    content = blocks.RichTextBlock()
    
    class Meta:
        template = 'blocks/custom_block.html'
```

### Q: Can I customize the Wagtail admin theme?

**A:** Yes, you can customize the admin interface by:
1. Overriding admin templates
2. Adding custom CSS/JS
3. Using third-party packages like `wagtail-admin-theme`

### Q: How do I backup data?

**A:** Use Django management commands:
```bash
# Backup to JSON
python manage.py dumpdata > backup.json

# Backup database (PostgreSQL)
pg_dump database_name > backup.sql
```

## Deployment

### Q: Which deployment platform is recommended?

**A:** RhamaaCMS can be deployed to various platforms:
- **Fly.io** - Easy and fast for small-medium apps
- **Divio Cloud** - Optimized for Django apps
- **Heroku** - Popular and user-friendly
- **DigitalOcean** - Flexible and cost-effective
- **AWS/GCP** - For enterprise applications

### Q: Do I need a CDN for static files?

**A:** For production, using a CDN is highly recommended to:
- Speed up static file loading
- Reduce server load
- Improve user experience

### Q: How do I set up HTTPS?

**A:** Depends on your deployment platform:
- **Fly.io/Divio**: Automatically available
- **Custom server**: Use Let's Encrypt or SSL certificate
- Update `SECURE_SSL_REDIRECT = True` in settings

### Q: How do I fix 500 errors in production?

**A:** 
1. Check application logs
2. Ensure `DEBUG = False`
3. Verify `ALLOWED_HOSTS` is correct
4. Check database connection
5. Ensure static files are collected

## Performance and Optimization

### Q: How can I speed up website loading?

**A:** Several optimization tips:
1. Enable caching (Redis/Memcached)
2. Optimize database queries
3. Compress and minify static files
4. Use CDN
5. Optimize images
6. Enable gzip compression

### Q: Do I need caching?

**A:** Yes, for production it's recommended to use:
- **Page caching** for rarely changing pages
- **Database caching** for expensive queries
- **Template fragment caching** for heavy components

### Q: How do I monitor performance?

**A:** Use tools like:
- Django Debug Toolbar (development)
- New Relic or Datadog (production)
- Google PageSpeed Insights
- GTmetrix for web performance

## Security

### Q: How do I secure the admin panel?

**A:** 
1. Use strong passwords
2. Enable two-factor authentication
3. Limit access by IP
4. Regular dependency updates
5. Monitor login attempts

### Q: Do I need an SSL certificate?

**A:** Yes, SSL certificate is required for:
- Data security
- SEO ranking
- User trust
- Compliance requirements

### Q: How do I backup securely?

**A:** 
1. Encrypt backup files
2. Store in multiple locations
3. Regular backup schedule
4. Test restore process
5. Limit access to backup files

## Troubleshooting

### Q: Website is loading very slowly, what should I do?

**A:** 
1. Check database queries with Django Debug Toolbar
2. Enable caching
3. Optimize images and static files
4. Check server resources
5. Review third-party integrations

### Q: "Template not found" error, how to fix it?

**A:** 
1. Check template path in `TEMPLATES` setting
2. Ensure template file name is correct
3. Verify template folder structure
4. Check case sensitivity (Linux/macOS)

### Q: How do I debug errors in production?

**A:** 
1. Check application logs
2. Enable logging for specific modules
3. Use error tracking tools (Sentry)
4. Reproduce error in staging environment

### Q: Media files not showing in production?

**A:** 
1. Check `MEDIA_URL` and `MEDIA_ROOT` settings
2. Ensure web server serves media files
3. For cloud storage, check credentials
4. Verify file permissions

## Customization

### Q: How do I change the homepage appearance?

**A:** 
1. Edit `HomePage` model in `home/models.py`
2. Update template `home/home_page.html`
3. Modify CSS in static files
4. Run migrations if model changes

### Q: Can I add custom fields to pages?

**A:** Yes, add fields to the page model:

```python
class HomePage(Page):
    custom_field = models.CharField(max_length=255)
    
    content_panels = Page.content_panels + [
        FieldPanel('custom_field'),
    ]
```

### Q: How do I create custom navigation menu?

**A:** 
1. Create model for menu items
2. Register in admin
3. Create template tag to render menu
4. Include in base template

## Integration

### Q: Can I integrate with external APIs?

**A:** Yes, you can:
1. Create service classes for API calls
2. Use Django REST framework for API endpoints
3. Implement caching for API responses
4. Handle errors and timeouts properly

### Q: How do I integrate payment gateways?

**A:** 
1. Install appropriate package (stripe, paypal, etc.)
2. Create models for orders/transactions
3. Implement webhook handlers
4. Add security measures

### Q: Does it support multi-language?

**A:** Yes, use:
1. Django internationalization (i18n)
2. Wagtail localization
3. `wagtail-localize` package for translation management

## Support

### Q: Where can I get help if I have problems?

**A:** 
1. **Documentation**: Read the complete documentation
2. **GitHub Issues**: Report bugs or request features
3. **Wagtail Community**: Slack channel and forum
4. **Stack Overflow**: Tag with `wagtail` and `django`

### Q: How can I contribute to RhamaaCMS?

**A:** 
1. Fork the repository on GitHub
2. Create a branch for feature/bugfix
3. Follow coding standards
4. Submit pull request with clear description
5. Read the [contributing guide](development/contributing.md)

### Q: Is there a development roadmap?

**A:** Yes, check the GitHub repository for:
- Planned features
- Current milestones
- Issue labels for priority

---

**Can't find the answer you're looking for?** 

Please open an issue on the [GitHub repository](https://github.com/rhamaa/RhamaaCMS) or contact the community for further assistance.