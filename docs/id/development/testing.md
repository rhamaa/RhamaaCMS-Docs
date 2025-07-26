# Testing

Panduan lengkap untuk testing RhamaaCMS, termasuk unit tests, integration tests, dan best practices.

## Testing Framework

RhamaaCMS menggunakan Django testing framework yang dibangun di atas Python's unittest:

- **Django TestCase** - Untuk database-backed tests
- **Wagtail Test Utils** - Utilities khusus untuk Wagtail
- **Factory Boy** - Untuk membuat test data
- **Coverage.py** - Untuk test coverage analysis

## Setup Testing Environment

### Install Testing Dependencies

```bash
pip install -r requirements-dev.txt
```

### Test Settings

Buat file `settings/test.py`:

```python
from .base import *

# Use in-memory SQLite for faster tests
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': ':memory:',
    }
}

# Disable migrations for faster tests
class DisableMigrations:
    def __contains__(self, item):
        return True
    
    def __getitem__(self, item):
        return None

MIGRATION_MODULES = DisableMigrations()

# Disable logging during tests
LOGGING_CONFIG = None

# Use dummy cache
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.dummy.DummyCache',
    }
}

# Disable password validation
AUTH_PASSWORD_VALIDATORS = []

# Use console email backend
EMAIL_BACKEND = 'django.core.mail.backends.locmem.EmailBackend'
```

## Model Testing

### Page Model Tests

```python
# tests/test_models.py
from django.test import TestCase
from wagtail.test.utils import WagtailPageTests
from wagtail.models import Page, Site
from home.models import HomePage, ContentPage

class HomePageTests(TestCase, WagtailPageTests):
    def setUp(self):
        self.root_page = Page.objects.get(id=2)
        self.home_page = HomePage(
            title="Test Home",
            slug="test-home",
            hero_title="Welcome to Test Site",
            hero_subtitle="This is a test subtitle"
        )
        self.root_page.add_child(instance=self.home_page)
    
    def test_can_create_homepage(self):
        """Test that HomePage can be created"""
        self.assertCanCreateAt(Page, HomePage)
    
    def test_homepage_fields(self):
        """Test HomePage fields"""
        self.assertEqual(self.home_page.hero_title, "Welcome to Test Site")
        self.assertEqual(self.home_page.hero_subtitle, "This is a test subtitle")
    
    def test_homepage_url(self):
        """Test HomePage URL generation"""
        self.assertEqual(self.home_page.url, '/test-home/')
    
    def test_homepage_template(self):
        """Test HomePage uses correct template"""
        response = self.client.get(self.home_page.url)
        self.assertTemplateUsed(response, 'home/home_page.html')

class ContentPageTests(TestCase, WagtailPageTests):
    def setUp(self):
        self.home_page = HomePage.objects.get(slug='home')
        self.content_page = ContentPage(
            title="Test Content",
            slug="test-content",
            intro="Test introduction",
            body="<p>Test body content</p>"
        )
        self.home_page.add_child(instance=self.content_page)
    
    def test_content_page_creation(self):
        """Test ContentPage creation"""
        self.assertCanCreateAt(HomePage, ContentPage)
    
    def test_content_page_fields(self):
        """Test ContentPage fields"""
        self.assertEqual(self.content_page.intro, "Test introduction")
        self.assertIn("Test body content", self.content_page.body)
    
    def test_content_page_parent_types(self):
        """Test ContentPage can only be created under certain parents"""
        self.assertCanCreateAt(HomePage, ContentPage)
        self.assertCanNotCreateAt(ContentPage, ContentPage)
```

### Block Testing

```python
# tests/test_blocks.py
from django.test import TestCase
from wagtail.blocks import StreamValue
from home.blocks import HeroBlock, CardBlock

class HeroBlockTests(TestCase):
    def setUp(self):
        self.hero_block = HeroBlock()
    
    def test_hero_block_render(self):
        """Test HeroBlock rendering"""
        value = {
            'title': 'Test Hero Title',
            'subtitle': 'Test Hero Subtitle',
            'cta_text': 'Click Me',
            'cta_url': 'https://example.com'
        }
        
        html = self.hero_block.render(value)
        self.assertIn('Test Hero Title', html)
        self.assertIn('Test Hero Subtitle', html)
        self.assertIn('Click Me', html)
        self.assertIn('https://example.com', html)
    
    def test_hero_block_validation(self):
        """Test HeroBlock validation"""
        # Valid data
        valid_data = {
            'title': 'Valid Title',
            'subtitle': 'Valid Subtitle'
        }
        self.assertTrue(self.hero_block.clean(valid_data))
        
        # Invalid data (missing required title)
        invalid_data = {
            'subtitle': 'Valid Subtitle'
        }
        with self.assertRaises(ValidationError):
            self.hero_block.clean(invalid_data)

class CardBlockTests(TestCase):
    def test_card_block_render(self):
        """Test CardBlock rendering"""
        card_block = CardBlock()
        value = {
            'title': 'Test Card',
            'description': 'Test Description',
            'link': 'https://example.com'
        }
        
        html = card_block.render(value)
        self.assertIn('Test Card', html)
        self.assertIn('Test Description', html)
        self.assertIn('https://example.com', html)
```

## View Testing

### Page View Tests

```python
# tests/test_views.py
from django.test import TestCase, Client
from django.contrib.auth.models import User
from wagtail.models import Page
from home.models import HomePage

class PageViewTests(TestCase):
    def setUp(self):
        self.client = Client()
        self.home_page = HomePage.objects.get(slug='home')
    
    def test_homepage_get(self):
        """Test homepage GET request"""
        response = self.client.get('/')
        self.assertEqual(response.status_code, 200)
        self.assertContains(response, self.home_page.title)
    
    def test_homepage_context(self):
        """Test homepage context variables"""
        response = self.client.get('/')
        self.assertIn('page', response.context)
        self.assertEqual(response.context['page'], self.home_page)
    
    def test_404_page(self):
        """Test 404 error page"""
        response = self.client.get('/non-existent-page/')
        self.assertEqual(response.status_code, 404)

class SearchViewTests(TestCase):
    def setUp(self):
        self.client = Client()
    
    def test_search_get(self):
        """Test search page GET request"""
        response = self.client.get('/search/')
        self.assertEqual(response.status_code, 200)
        self.assertTemplateUsed(response, 'search/search.html')
    
    def test_search_with_query(self):
        """Test search with query parameter"""
        response = self.client.get('/search/?query=test')
        self.assertEqual(response.status_code, 200)
        self.assertIn('query', response.context)
        self.assertEqual(response.context['query'], 'test')
```

## Admin Testing

### Admin Interface Tests

```python
# tests/test_admin.py
from django.test import TestCase
from django.contrib.auth.models import User
from wagtail.test.utils import WagtailTestUtils
from home.models import HomePage

class AdminTests(TestCase, WagtailTestUtils):
    def setUp(self):
        self.user = self.create_superuser(
            username='admin',
            email='admin@example.com',
            password='password'
        )
        self.home_page = HomePage.objects.get(slug='home')
    
    def test_admin_login(self):
        """Test admin login"""
        self.client.login(username='admin', password='password')
        response = self.client.get('/admin/')
        self.assertEqual(response.status_code, 200)
    
    def test_page_edit_access(self):
        """Test page edit access"""
        self.client.login(username='admin', password='password')
        response = self.client.get(f'/admin/pages/{self.home_page.id}/edit/')
        self.assertEqual(response.status_code, 200)
    
    def test_page_creation(self):
        """Test page creation through admin"""
        self.client.login(username='admin', password='password')
        
        # Get add page form
        response = self.client.get(f'/admin/pages/add/home/contentpage/{self.home_page.id}/')
        self.assertEqual(response.status_code, 200)
        
        # Submit new page
        response = self.client.post(f'/admin/pages/add/home/contentpage/{self.home_page.id}/', {
            'title': 'New Test Page',
            'slug': 'new-test-page',
            'intro': 'Test introduction',
            'body': '<p>Test content</p>',
            'action-publish': 'Publish'
        })
        
        # Should redirect after successful creation
        self.assertEqual(response.status_code, 302)
```

## Form Testing

### Contact Form Tests

```python
# tests/test_forms.py
from django.test import TestCase
from home.forms import ContactForm

class ContactFormTests(TestCase):
    def test_valid_form(self):
        """Test valid contact form"""
        form_data = {
            'name': 'John Doe',
            'email': 'john@example.com',
            'subject': 'Test Subject',
            'message': 'Test message content'
        }
        form = ContactForm(data=form_data)
        self.assertTrue(form.is_valid())
    
    def test_invalid_email(self):
        """Test form with invalid email"""
        form_data = {
            'name': 'John Doe',
            'email': 'invalid-email',
            'subject': 'Test Subject',
            'message': 'Test message content'
        }
        form = ContactForm(data=form_data)
        self.assertFalse(form.is_valid())
        self.assertIn('email', form.errors)
    
    def test_required_fields(self):
        """Test form with missing required fields"""
        form = ContactForm(data={})
        self.assertFalse(form.is_valid())
        self.assertIn('name', form.errors)
        self.assertIn('email', form.errors)
        self.assertIn('message', form.errors)
```

## Template Testing

### Template Tag Tests

```python
# tests/test_templatetags.py
from django.test import TestCase
from django.template import Context, Template
from home.models import HomePage

class TemplateTagTests(TestCase):
    def setUp(self):
        self.home_page = HomePage.objects.get(slug='home')
    
    def test_navigation_tag(self):
        """Test navigation template tag"""
        template = Template('{% load home_tags %}{% navigation %}')
        rendered = template.render(Context())
        self.assertIn('nav', rendered)
    
    def test_breadcrumbs_tag(self):
        """Test breadcrumbs template tag"""
        template = Template('{% load home_tags %}{% breadcrumbs page %}')
        context = Context({'page': self.home_page})
        rendered = template.render(context)
        self.assertIn('breadcrumb', rendered)
```

## Factory Classes

### Model Factories

```python
# tests/factories.py
import factory
from django.contrib.auth.models import User
from wagtail.models import Page
from home.models import HomePage, ContentPage

class UserFactory(factory.django.DjangoModelFactory):
    class Meta:
        model = User
    
    username = factory.Sequence(lambda n: f'user{n}')
    email = factory.LazyAttribute(lambda obj: f'{obj.username}@example.com')
    first_name = factory.Faker('first_name')
    last_name = factory.Faker('last_name')

class HomePageFactory(factory.django.DjangoModelFactory):
    class Meta:
        model = HomePage
    
    title = 'Home'
    slug = 'home'
    hero_title = factory.Faker('sentence', nb_words=4)
    hero_subtitle = factory.Faker('text', max_nb_chars=200)

class ContentPageFactory(factory.django.DjangoModelFactory):
    class Meta:
        model = ContentPage
    
    title = factory.Faker('sentence', nb_words=3)
    slug = factory.LazyAttribute(lambda obj: obj.title.lower().replace(' ', '-'))
    intro = factory.Faker('text', max_nb_chars=250)
    body = factory.Faker('text', max_nb_chars=1000)
```

### Using Factories in Tests

```python
# tests/test_with_factories.py
from django.test import TestCase
from .factories import HomePageFactory, ContentPageFactory, UserFactory

class FactoryTests(TestCase):
    def test_homepage_factory(self):
        """Test HomePage factory"""
        home_page = HomePageFactory()
        self.assertIsInstance(home_page, HomePage)
        self.assertTrue(home_page.hero_title)
    
    def test_content_page_factory(self):
        """Test ContentPage factory"""
        home_page = HomePageFactory()
        content_page = ContentPageFactory(parent=home_page)
        self.assertIsInstance(content_page, ContentPage)
        self.assertEqual(content_page.get_parent(), home_page)
    
    def test_user_factory(self):
        """Test User factory"""
        user = UserFactory()
        self.assertTrue(user.username)
        self.assertTrue(user.email)
```

## Integration Testing

### Full Page Tests

```python
# tests/test_integration.py
from django.test import TestCase, TransactionTestCase
from django.contrib.auth.models import User
from wagtail.models import Site
from home.models import HomePage
from .factories import HomePageFactory, UserFactory

class IntegrationTests(TransactionTestCase):
    def setUp(self):
        # Create site and homepage
        self.site = Site.objects.get(is_default_site=True)
        self.home_page = HomePageFactory()
        self.site.root_page = self.home_page
        self.site.save()
    
    def test_full_page_workflow(self):
        """Test complete page creation and publishing workflow"""
        # Create admin user
        admin = UserFactory(is_superuser=True, is_staff=True)
        self.client.force_login(admin)
        
        # Create new page
        response = self.client.post(f'/admin/pages/add/home/contentpage/{self.home_page.id}/', {
            'title': 'Integration Test Page',
            'slug': 'integration-test',
            'intro': 'Integration test introduction',
            'body': '<p>Integration test content</p>',
            'action-publish': 'Publish'
        })
        
        # Verify redirect
        self.assertEqual(response.status_code, 302)
        
        # Verify page exists and is accessible
        response = self.client.get('/integration-test/')
        self.assertEqual(response.status_code, 200)
        self.assertContains(response, 'Integration Test Page')
```

## Performance Testing

### Database Query Tests

```python
# tests/test_performance.py
from django.test import TestCase
from django.test.utils import override_settings
from django.db import connection
from home.models import HomePage

class PerformanceTests(TestCase):
    def test_homepage_query_count(self):
        """Test homepage doesn't make too many database queries"""
        home_page = HomePage.objects.get(slug='home')
        
        with self.assertNumQueries(5):  # Adjust based on expected queries
            response = self.client.get('/')
            self.assertEqual(response.status_code, 200)
    
    def test_search_performance(self):
        """Test search page performance"""
        with self.assertNumQueries(10):  # Adjust based on expected queries
            response = self.client.get('/search/?query=test')
            self.assertEqual(response.status_code, 200)
```

## Running Tests

### Basic Test Commands

```bash
# Run all tests
python manage.py test

# Run specific app tests
python manage.py test home

# Run specific test class
python manage.py test home.tests.test_models.HomePageTests

# Run specific test method
python manage.py test home.tests.test_models.HomePageTests.test_can_create_homepage

# Run with verbosity
python manage.py test --verbosity=2

# Run with keepdb (faster for repeated runs)
python manage.py test --keepdb

# Run parallel tests
python manage.py test --parallel
```

### Coverage Analysis

```bash
# Install coverage
pip install coverage

# Run tests with coverage
coverage run --source='.' manage.py test

# Generate coverage report
coverage report

# Generate HTML coverage report
coverage html

# View coverage in browser
open htmlcov/index.html
```

### Test Configuration

```python
# pytest.ini or setup.cfg
[tool:pytest]
DJANGO_SETTINGS_MODULE = myproject.settings.test
python_files = tests.py test_*.py *_tests.py
addopts = --tb=short --strict-markers
markers =
    slow: marks tests as slow
    integration: marks tests as integration tests
```

## Continuous Integration

### GitHub Actions

```yaml
# .github/workflows/test.yml
name: Tests

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    
    strategy:
      matrix:
        python-version: [3.8, 3.9, '3.10', 3.11]
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Set up Python ${{ matrix.python-version }}
      uses: actions/setup-python@v4
      with:
        python-version: ${{ matrix.python-version }}
    
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
        pip install -r requirements-dev.txt
    
    - name: Run tests
      run: |
        python manage.py test --settings=myproject.settings.test
    
    - name: Run coverage
      run: |
        coverage run --source='.' manage.py test --settings=myproject.settings.test
        coverage xml
    
    - name: Upload coverage to Codecov
      uses: codecov/codecov-action@v3
      with:
        file: ./coverage.xml
```

## Best Practices

### 1. Test Organization

- Organize tests by functionality
- Use descriptive test names
- Group related tests in classes
- Keep tests independent

### 2. Test Data

- Use factories for test data creation
- Clean up test data after each test
- Use realistic but minimal test data
- Avoid hardcoded values

### 3. Assertions

- Use specific assertions
- Test both positive and negative cases
- Verify error conditions
- Check edge cases

### 4. Performance

- Use `TestCase` for database tests
- Use `SimpleTestCase` for non-database tests
- Mock external dependencies
- Use `setUpClass` for expensive setup

### 5. Maintenance

- Keep tests up to date with code changes
- Remove obsolete tests
- Refactor duplicate test code
- Document complex test scenarios

## Debugging Tests

### Common Issues

1. **Test database not created**
   ```bash
   python manage.py test --debug-mode
   ```

2. **Migrations not applied**
   ```bash
   python manage.py test --keepdb --debug-mode
   ```

3. **Static files not found**
   ```python
   # In test settings
   STATICFILES_STORAGE = 'django.contrib.staticfiles.storage.StaticFilesStorage'
   ```

### Debug Tools

```python
# Add to test for debugging
import pdb; pdb.set_trace()

# Or use Django's debug
from django.test.utils import setup_test_environment
setup_test_environment(debug=True)
```

## Langkah Selanjutnya

Setelah menguasai testing:

1. Implementasi automated testing dalam CI/CD
2. Setup monitoring dan alerting
3. Performance testing dan optimization
4. Security testing

Untuk informasi lebih lanjut, lihat [Contributing](contributing.md) dan [FAQ](../faq.md).