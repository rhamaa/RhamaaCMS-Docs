# Installing RhamaaCMS

Complete guide to install and set up RhamaaCMS on your system.

## System Requirements

Before starting, ensure your system meets the following requirements:

### Python Version

You need a [compatible Python version](https://docs.wagtail.org/en/stable/releases/upgrading.html#compatible-django-python-versions) with Wagtail:

=== "Linux/macOS"
    ```bash
    python --version
    # or
    python3 --version
    ```

=== "Windows"
    ```cmd
    py --version
    ```

!!! tip "Recommended Version"
    Use Python 3.8 or newer for best compatibility.

## Installation Steps

### 1. Create Virtual Environment

Virtual environment helps isolate your project dependencies:

=== "Linux/macOS"
    ```bash
    python -m venv myproject/env
    source myproject/env/bin/activate
    ```

=== "Windows"
    ```cmd
    python -m venv myproject\env
    myproject\env\Scripts\activate
    ```

!!! warning "Important"
    Make sure the virtual environment is active before proceeding. You should see `(env)` at the beginning of your terminal prompt.

### 2. Navigate to Project Directory

```bash
cd myproject
```

### 3. Install Wagtail

```bash
pip install wagtail
```

### 4. Initialize Project with RhamaaCMS Template

```bash
wagtail start --template=https://github.com/rhamaa/RhamaaCMS/archive/refs/heads/main.zip myproject .
```

!!! info "Template URL"
    The template is downloaded directly from the RhamaaCMS GitHub repository. Ensure you have a stable internet connection.

### 5. Install Project Dependencies

```bash
pip install -r requirements.txt
```

## Installation Verification

To ensure the installation was successful, run the following command:

```bash
python manage.py check
```

If there are no errors, your installation is successful!

## Troubleshooting

### Error: Python not found

**Solution:**
- Ensure Python is installed correctly
- Add Python to system PATH
- Use `python3` instead of `python` on Linux/macOS

### Error: pip not found

**Solution:**
```bash
python -m ensurepip --upgrade
```

### Error: Permission denied

**Solution:**
- Use virtual environment (recommended)
- Or use `--user` flag: `pip install --user wagtail`

## Next Steps

After installation is complete, continue to [Initial Setup](initial-setup.md) to configure your project.