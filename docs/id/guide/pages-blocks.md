# Halaman dan Blok

RhamaaCMS menyediakan sistem halaman dan blok yang fleksibel untuk membangun konten yang dinamis dan mudah dikelola.

## Konsep Dasar

### Pages (Halaman)

Pages adalah unit konten utama di Wagtail. Setiap page memiliki:
- **URL** - Alamat web halaman
- **Template** - File HTML untuk rendering
- **Fields** - Data yang dapat diedit di admin
- **Hierarchy** - Struktur parent-child

### Blocks (Blok)

Blocks adalah komponen konten yang dapat digunakan kembali:
- **StreamField** - Field yang berisi multiple blocks
- **StructBlock** - Block dengan multiple fields
- **ListBlock** - Block yang berisi list items
- **RichTextBlock** - Block untuk rich text

## Page Models

### HomePage Model

Model untuk halaman utama website:

```python
# home/models.py
from wagtail.models import Page
from wagtail.fields import RichTextField, StreamField
from wagtail.admin.panels import FieldPanel
from wagtail import blocks

class HomePage(Page):
    # Hero section
    hero_title = models.CharField(max_length=255, blank=True)
    hero_subtitle = models.TextField(blank=True)
    hero_image = models.ForeignKey(
        'wagtailimages.Image',
        null=True,
        blank=True,
        on_delete=models.SET_NULL,
        related_name='+'
    )
    
    # Main content
    body = StreamField([
        ('heading', blocks.CharBlock(form_classname="title")),
        ('paragraph', blocks.RichTextBlock()),
        ('image', ImageChooserBlock()),
        ('quote', blocks.BlockQuoteBlock()),
    ], blank=True, use_json_field=True)
    
    content_panels = Page.content_panels + [
        FieldPanel('hero_title'),
        FieldPanel('hero_subtitle'),
        FieldPanel('hero_image'),
        FieldPanel('body'),
    ]
```

### ContentPage Model

Model untuk halaman konten umum:

```python
class ContentPage(Page):
    date = models.DateField("Post date")
    intro = models.CharField(max_length=250)
    body = RichTextField(blank=True)
    
    # Featured image
    featured_image = models.ForeignKey(
        'wagtailimages.Image',
        null=True,
        blank=True,
        on_delete=models.SET_NULL,
        related_name='+'
    )
    
    content_panels = Page.content_panels + [
        FieldPanel('date'),
        FieldPanel('intro'),
        FieldPanel('featured_image'),
        FieldPanel('body'),
    ]
    
    # SEO settings
    promote_panels = Page.promote_panels + [
        FieldPanel('featured_image'),
    ]
```

## Custom Blocks

### Hero Block

Block untuk section hero:

```python
# blocks.py
from wagtail import blocks
from wagtail.images.blocks import ImageChooserBlock

class HeroBlock(blocks.StructBlock):
    title = blocks.CharBlock(max_length=255, help_text="Hero title")
    subtitle = blocks.TextBlock(help_text="Hero subtitle")
    image = ImageChooserBlock(required=False)
    cta_text = blocks.CharBlock(max_length=50, required=False)
    cta_url = blocks.URLBlock(required=False)
    
    class Meta:
        template = 'blocks/hero_block.html'
        icon = 'image'
        label = 'Hero Section'
```

### Card Block

Block untuk menampilkan kartu konten:

```python
class CardBlock(blocks.StructBlock):
    title = blocks.CharBlock(max_length=100)
    description = blocks.TextBlock()
    image = ImageChooserBlock(required=False)
    link = blocks.URLBlock(required=False)
    
    class Meta:
        template = 'blocks/card_block.html'
        icon = 'doc-full'
        label = 'Card'
```

### Gallery Block

Block untuk galeri gambar:

```python
class GalleryBlock(blocks.StructBlock):
    title = blocks.CharBlock(max_length=100, required=False)
    images = blocks.ListBlock(ImageChooserBlock())
    
    class Meta:
        template = 'blocks/gallery_block.html'
        icon = 'image'
        label = 'Gallery'
```

## StreamField Configuration

### Basic StreamField

```python
from wagtail.fields import StreamField
from wagtail import blocks

body = StreamField([
    ('heading', blocks.CharBlock(form_classname="title")),
    ('paragraph', blocks.RichTextBlock()),
    ('image', ImageChooserBlock()),
    ('hero', HeroBlock()),
    ('cards', blocks.ListBlock(CardBlock())),
    ('gallery', GalleryBlock()),
], blank=True, use_json_field=True)
```

### Advanced StreamField

```python
body = StreamField([
    ('text', blocks.StructBlock([
        ('heading', blocks.CharBlock()),
        ('content', blocks.RichTextBlock()),
        ('alignment', blocks.ChoiceBlock(choices=[
            ('left', 'Left'),
            ('center', 'Center'),
            ('right', 'Right'),
        ])),
    ])),
    ('media', blocks.StructBlock([
        ('type', blocks.ChoiceBlock(choices=[
            ('image', 'Image'),
            ('video', 'Video'),
            ('embed', 'Embed'),
        ])),
        ('image', ImageChooserBlock(required=False)),
        ('video', blocks.URLBlock(required=False)),
        ('embed', blocks.RawHTMLBlock(required=False)),
    ])),
], blank=True, use_json_field=True)
```

## Block Templates

### Hero Block Template

```html
<!-- templates/blocks/hero_block.html -->
<section class="hero">
    {% if value.image %}
        <div class="hero-image">
            {% image value.image fill-1200x600 %}
        </div>
    {% endif %}
    
    <div class="hero-content">
        <h1 class="hero-title">{{ value.title }}</h1>
        {% if value.subtitle %}
            <p class="hero-subtitle">{{ value.subtitle }}</p>
        {% endif %}
        
        {% if value.cta_text and value.cta_url %}
            <a href="{{ value.cta_url }}" class="btn btn-primary">
                {{ value.cta_text }}
            </a>
        {% endif %}
    </div>
</section>
```

### Card Block Template

```html
<!-- templates/blocks/card_block.html -->
<div class="card">
    {% if value.image %}
        <div class="card-image">
            {% image value.image fill-300x200 %}
        </div>
    {% endif %}
    
    <div class="card-content">
        <h3 class="card-title">{{ value.title }}</h3>
        <p class="card-description">{{ value.description }}</p>
        
        {% if value.link %}
            <a href="{{ value.link }}" class="card-link">
                Read More
            </a>
        {% endif %}
    </div>
</div>
```

## Page Templates

### HomePage Template

```html
<!-- templates/home/home_page.html -->
{% extends "base.html" %}
{% load wagtailcore_tags wagtailimages_tags %}

{% block content %}
    <!-- Hero Section -->
    {% if page.hero_title %}
        <section class="hero">
            {% if page.hero_image %}
                {% image page.hero_image fill-1200x600 as hero_img %}
                <div class="hero-image">
                    <img src="{{ hero_img.url }}" alt="{{ page.hero_title }}">
                </div>
            {% endif %}
            
            <div class="hero-content">
                <h1>{{ page.hero_title }}</h1>
                {% if page.hero_subtitle %}
                    <p>{{ page.hero_subtitle }}</p>
                {% endif %}
            </div>
        </section>
    {% endif %}
    
    <!-- Main Content -->
    <main class="main-content">
        {% for block in page.body %}
            {% include_block block %}
        {% endfor %}
    </main>
{% endblock %}
```

### ContentPage Template

```html
<!-- templates/home/content_page.html -->
{% extends "base.html" %}
{% load wagtailcore_tags wagtailimages_tags %}

{% block content %}
    <article class="content-page">
        <header class="page-header">
            <h1>{{ page.title }}</h1>
            <p class="page-date">{{ page.date }}</p>
            
            {% if page.featured_image %}
                <div class="featured-image">
                    {% image page.featured_image fill-800x400 %}
                </div>
            {% endif %}
            
            {% if page.intro %}
                <p class="page-intro">{{ page.intro }}</p>
            {% endif %}
        </header>
        
        <div class="page-content">
            {{ page.body|richtext }}
        </div>
    </article>
{% endblock %}
```

## Admin Interface Customization

### Panel Configuration

```python
class HomePage(Page):
    # ... fields ...
    
    content_panels = Page.content_panels + [
        MultiFieldPanel([
            FieldPanel('hero_title'),
            FieldPanel('hero_subtitle'),
            FieldPanel('hero_image'),
        ], heading="Hero Section"),
        FieldPanel('body'),
    ]
    
    promote_panels = Page.promote_panels + [
        FieldPanel('hero_image'),
    ]
    
    settings_panels = Page.settings_panels + [
        FieldPanel('show_in_menus'),
    ]
```

### Custom Edit Handlers

```python
from wagtail.admin.panels import TabbedInterface, ObjectList

class HomePage(Page):
    # ... fields ...
    
    edit_handler = TabbedInterface([
        ObjectList(content_panels, heading='Content'),
        ObjectList(promote_panels, heading='Promote'),
        ObjectList(settings_panels, heading='Settings'),
    ])
```

## Best Practices

### 1. Block Design

- Buat blocks yang reusable dan modular
- Gunakan nama yang deskriptif
- Sediakan help text untuk editor
- Implementasi validation yang tepat

### 2. Template Organization

- Pisahkan block templates dari page templates
- Gunakan consistent naming convention
- Implementasi responsive design
- Optimasi untuk performance

### 3. Content Structure

- Rencanakan hierarchy halaman dengan baik
- Gunakan StreamField untuk konten yang fleksibel
- Implementasi SEO best practices
- Sediakan preview functionality

### 4. Admin Experience

- Organisir fields dalam panels yang logis
- Gunakan help text dan labels yang jelas
- Implementasi custom widgets jika diperlukan
- Test admin interface secara regular

## Langkah Selanjutnya

Setelah memahami halaman dan blok, lanjutkan ke [Mengelola Konten](content-management.md) untuk mempelajari cara menggunakan admin interface.