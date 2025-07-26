# Kontribusi

Terima kasih atas minat Anda untuk berkontribusi pada RhamaaCMS! Panduan ini akan membantu Anda memulai kontribusi dengan cara yang efektif.

## Cara Berkontribusi

Ada beberapa cara untuk berkontribusi pada RhamaaCMS:

- 🐛 **Report bugs** - Laporkan bug yang Anda temukan
- 💡 **Suggest features** - Usulkan fitur baru
- 📝 **Improve documentation** - Perbaiki atau tambah dokumentasi
- 💻 **Submit code** - Kontribusi kode untuk bug fixes atau features
- 🧪 **Write tests** - Tambah atau perbaiki test coverage
- 🎨 **Design improvements** - Perbaiki UI/UX

## Getting Started

### 1. Fork Repository

1. Kunjungi [RhamaaCMS repository](https://github.com/rhamaa/RhamaaCMS)
2. Klik tombol "Fork" di kanan atas
3. Clone fork Anda ke local machine:

```bash
git clone https://github.com/YOUR_USERNAME/RhamaaCMS.git
cd RhamaaCMS
```

### 2. Setup Development Environment

```bash
# Buat virtual environment
python -m venv env
source env/bin/activate  # Linux/macOS
# atau
env\Scripts\activate     # Windows

# Install dependencies
pip install -r requirements.txt
pip install -r requirements-dev.txt

# Setup database
python manage.py migrate
python manage.py load_initial_data
```

### 3. Create Branch

Buat branch baru untuk setiap kontribusi:

```bash
# Untuk bug fix
git checkout -b fix/issue-description

# Untuk feature baru
git checkout -b feature/feature-name

# Untuk dokumentasi
git checkout -b docs/documentation-improvement
```

## Development Workflow

### 1. Membuat Perubahan

#### Code Style

Ikuti coding standards yang sudah ditetapkan:

- **Python**: Follow PEP 8
- **HTML**: Proper indentation dan semantic markup
- **CSS**: BEM methodology untuk naming
- **JavaScript**: ES6+ standards

#### Pre-commit Hooks

Install pre-commit hooks untuk quality assurance:

```bash
pip install pre-commit
pre-commit install
```

Hooks yang aktif:
- **Black** - Code formatting
- **Flake8** - Linting
- **isort** - Import sorting
- **djhtml** - HTML template formatting

### 2. Testing

Jalankan tests sebelum submit:

```bash
# Run all tests
python manage.py test

# Run specific app tests
python manage.py test home

# Run with coverage
coverage run --source='.' manage.py test
coverage report
```

#### Writing Tests

Tambahkan tests untuk setiap perubahan:

```python
# tests/test_models.py
from django.test import TestCase
from home.models import HomePage

class HomePageTestCase(TestCase):
    def setUp(self):
        self.home_page = HomePage.objects.get(slug='home')
    
    def test_home_page_title(self):
        self.assertEqual(self.home_page.title, 'Home')
    
    def test_home_page_url(self):
        response = self.client.get('/')
        self.assertEqual(response.status_code, 200)
```

### 3. Documentation

Update dokumentasi jika diperlukan:

```bash
# Install MkDocs
pip install mkdocs mkdocs-material

# Serve documentation locally
mkdocs serve

# Build documentation
mkdocs build
```

## Submission Guidelines

### 1. Commit Messages

Gunakan format commit message yang konsisten:

```
type(scope): description

[optional body]

[optional footer]
```

**Types:**
- `feat`: Feature baru
- `fix`: Bug fix
- `docs`: Dokumentasi
- `style`: Formatting, missing semicolons, etc
- `refactor`: Code refactoring
- `test`: Adding tests
- `chore`: Maintenance tasks

**Examples:**
```bash
feat(home): add hero section to homepage
fix(admin): resolve image upload issue
docs(readme): update installation instructions
```

### 2. Pull Request

#### Before Submitting

Checklist sebelum submit PR:

- [ ] Code follows style guidelines
- [ ] Tests pass locally
- [ ] Documentation updated
- [ ] Commit messages are clear
- [ ] Branch is up to date with main

#### PR Template

Gunakan template berikut untuk PR description:

```markdown
## Description
Brief description of changes

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Documentation update
- [ ] Refactoring

## Testing
- [ ] Tests pass
- [ ] New tests added
- [ ] Manual testing completed

## Screenshots (if applicable)
Add screenshots for UI changes

## Checklist
- [ ] Code follows style guidelines
- [ ] Self-review completed
- [ ] Documentation updated
```

#### Review Process

1. **Automated checks** akan berjalan otomatis
2. **Maintainer review** - Code review oleh maintainer
3. **Feedback** - Perbaiki jika ada feedback
4. **Merge** - PR akan di-merge setelah approved

## Issue Guidelines

### Reporting Bugs

Gunakan template bug report:

```markdown
**Bug Description**
Clear description of the bug

**Steps to Reproduce**
1. Go to '...'
2. Click on '....'
3. Scroll down to '....'
4. See error

**Expected Behavior**
What you expected to happen

**Screenshots**
Add screenshots if applicable

**Environment:**
- OS: [e.g. Ubuntu 20.04]
- Python version: [e.g. 3.9]
- Django version: [e.g. 4.2]
- Wagtail version: [e.g. 5.0]
```

### Feature Requests

Gunakan template feature request:

```markdown
**Feature Description**
Clear description of the feature

**Problem Statement**
What problem does this solve?

**Proposed Solution**
How should this be implemented?

**Alternatives Considered**
Other solutions you've considered

**Additional Context**
Any other context or screenshots
```

## Code Review Guidelines

### For Contributors

Saat menerima review:

- **Be responsive** - Respond to feedback promptly
- **Be open** - Accept constructive criticism
- **Ask questions** - If feedback unclear, ask for clarification
- **Make changes** - Address all feedback before re-requesting review

### For Reviewers

Saat melakukan review:

- **Be constructive** - Provide helpful feedback
- **Be specific** - Point to exact lines and suggest improvements
- **Be respectful** - Maintain professional tone
- **Test changes** - Pull and test the changes locally

## Community Guidelines

### Code of Conduct

Kami berkomitmen untuk menyediakan lingkungan yang:

- **Welcoming** - Terbuka untuk semua kontributor
- **Respectful** - Menghormati perbedaan pendapat
- **Collaborative** - Bekerja sama untuk tujuan bersama
- **Professional** - Maintain professional standards

### Communication

- **GitHub Issues** - Untuk bug reports dan feature requests
- **Pull Requests** - Untuk code contributions
- **Discussions** - Untuk general questions dan ideas

## Development Setup Advanced

### Database Options

#### PostgreSQL (Recommended for development)

```bash
# Install PostgreSQL
sudo apt-get install postgresql postgresql-contrib

# Create database
sudo -u postgres createdb rhamaacms_dev

# Update settings
# settings/dev.py
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'rhamaacms_dev',
        'USER': 'postgres',
        'PASSWORD': 'password',
        'HOST': 'localhost',
        'PORT': '5432',
    }
}
```

### Frontend Development

#### CSS/SCSS

```bash
# Install Node.js dependencies
npm install

# Watch for changes
npm run watch

# Build for production
npm run build
```

#### JavaScript

```bash
# Lint JavaScript
npm run lint:js

# Format code
npm run format:js
```

## Release Process

### Versioning

RhamaaCMS menggunakan [Semantic Versioning](https://semver.org/):

- **MAJOR** - Breaking changes
- **MINOR** - New features (backward compatible)
- **PATCH** - Bug fixes

### Release Checklist

Untuk maintainers:

1. Update version numbers
2. Update CHANGELOG.md
3. Run full test suite
4. Create release branch
5. Tag release
6. Update documentation
7. Announce release

## Recognition

Kontributor akan diakui dalam:

- **CONTRIBUTORS.md** file
- **Release notes**
- **Documentation credits**
- **Social media mentions**

## Getting Help

Jika Anda membutuhkan bantuan:

1. **Check documentation** - Baca dokumentasi terlebih dahulu
2. **Search issues** - Cari di existing issues
3. **Ask questions** - Buat issue dengan label "question"
4. **Join discussions** - Participate in GitHub Discussions

## Resources

### Useful Links

- [Wagtail Documentation](https://docs.wagtail.org/)
- [Django Documentation](https://docs.djangoproject.com/)
- [Python Style Guide](https://pep8.org/)
- [Git Best Practices](https://git-scm.com/book)

### Tools

- **IDE**: VS Code, PyCharm, atau editor favorit Anda
- **Database**: PostgreSQL untuk development
- **Testing**: pytest, coverage
- **Linting**: flake8, black, isort

---

Terima kasih telah berkontribusi pada RhamaaCMS! Setiap kontribusi, sekecil apapun, sangat berarti untuk kemajuan project ini. 🚀