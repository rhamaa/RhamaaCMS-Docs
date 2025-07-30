# RhamaaCMS Documentation

Welcome to the RhamaaCMS documentation! This project template is designed for creating [Wagtail](https://wagtail.org) builds quickly, intended for developers to bootstrap their Wagtail site development using `wagtail start --template=`.

## What is RhamaaCMS?

RhamaaCMS is a modern Wagtail template that comes with:

- ✨ **Pre-defined pages** - Ready-to-use page structures
- 🧱 **Content blocks** - Reusable components
- ⚡ **Complete functionality** - Essential features already integrated
- 📦 **Data fixtures** - Sample data to get started quickly
- 🎨 **RHAMAA Global Design System** - Modern styling with Tailwind CSS and Preline UI
- 🌙 **Theme Support** - Light/dark mode with custom theme capabilities
- 📱 **Responsive Design** - Mobile-first approach with modern build tools

## Quick Start

Want to get started right away? Follow these steps:

1. **Ensure Python 3 is installed**
   ```bash
   python --version
   ```

2. **Create virtual environment**
   ```bash
   python -m venv myproject/env
   source myproject/env/bin/activate
   ```

3. **Install Wagtail**
   ```bash
   pip install wagtail
   ```

4. **Create project with RhamaaCMS template**
   ```bash
   wagtail start --template=https://github.com/rhamaa/RhamaaCMS/archive/refs/heads/main.zip myproject .
   ```

5. **Install dependencies and run**
   ```bash
   pip install -r requirements.txt
   make load-data
   make start
   ```

After the server is running, open `localhost:8000` to view the site and `localhost:8000/admin` for the admin panel.

!!! info "Default Credentials"
    - Username: `rhamaa`
    - Password: `admin`

## Documentation Navigation

- **[Getting Started](getting-started/installation.md)** - Complete installation guide
- **[User Guide](guide/project-structure.md)** - How to use RhamaaCMS
- **[Design System](guide/design-system.md)** - RHAMAA Global Design System overview
- **[Theme Customization](guide/theme-customization.md)** - Create and customize themes
- **[Component Development](guide/component-development.md)** - Build reusable UI components
- **[JavaScript Development](guide/javascript-development.md)** - Modern JavaScript with Preline UI
- **[Deployment](deployment/flyio.md)** - Deploy to various platforms
- **[Development](development/contributing.md)** - Contributing and development

## Support

If you encounter any issues or have suggestions for improvement, feel free to contribute or open an issue on the [GitHub repository](https://github.com/rhamaa/RhamaaCMS).

Happy coding with Wagtail! 🚀