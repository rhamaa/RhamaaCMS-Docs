# Running the Server

Guide to running the development server and accessing your RhamaaCMS application.

## Starting the Development Server

### Using Make Command

RhamaaCMS provides a Makefile for easier development:

```bash
make start
```

### Using Django Command

Alternatively, you can run the server directly:

```bash
python manage.py runserver
```

### Running on Custom Port

```bash
python manage.py runserver 8080
```

or

```bash
python manage.py runserver 0.0.0.0:8000
```

!!! tip "Network Access"
    Use `0.0.0.0:8000` to access the server from other devices on the same network.

## Accessing the Application

After the server is running, you can access:

### Frontend Website
- **URL:** [http://localhost:8000](http://localhost:8000)
- **Description:** Main website that visitors can see

### Admin Panel
- **URL:** [http://localhost:8000/admin](http://localhost:8000/admin)
- **Default Credentials:**
  - Username: `rhamaa`
  - Password: `admin`

!!! warning "Security"
    Change the default password immediately after first login!

### Django Admin (Optional)
- **URL:** [http://localhost:8000/django-admin](http://localhost:8000/django-admin)
- **Description:** Django admin interface for low-level management

## Development Server Features

### Auto-reload

The development server automatically restarts when you change Python files:

```bash
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).
January 26, 2025 - 10:30:00
Django version 4.2.x, using settings 'myproject.settings.dev'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

### Static Files

Static files (CSS, JS, images) are served automatically in development mode.

### Debug Mode

In development, debug mode is active so you'll see:
- Detailed error pages
- Django Debug Toolbar (if installed)
- SQL query information

## Other Make Commands

RhamaaCMS provides several useful make commands:

### Load Data
```bash
make load-data
```
Load dummy data for development.

### Dump Data
```bash
make dump-data
```
Save current data to fixtures.

### Run Tests
```bash
make test
```
Run the test suite.

### Collect Static Files
```bash
make collectstatic
```
Collect static files for production.

### Database Migration
```bash
make migrate
```
Run database migrations.

### Create Migration
```bash
make makemigrations
```
Create new migration files.

## Troubleshooting

### Port Already in Use

**Error:**
```
Error: That port is already in use.
```

**Solution:**
```bash
# Use different port
python manage.py runserver 8080

# Or kill process using port 8000
sudo lsof -t -i tcp:8000 | xargs kill -9
```

### Permission Denied

**Error:**
```
PermissionError: [Errno 13] Permission denied
```

**Solution:**
- Ensure virtual environment is active
- Check project folder permissions
- Run with appropriate user

### Module Not Found

**Error:**
```
ModuleNotFoundError: No module named 'wagtail'
```

**Solution:**
```bash
# Ensure virtual environment is active
source env/bin/activate

# Reinstall dependencies
pip install -r requirements.txt
```

### Database Error

**Error:**
```
django.db.utils.OperationalError: no such table
```

**Solution:**
```bash
# Run migrations
python manage.py migrate

# Or use make command
make migrate
```

## Development Tips

### 1. Use Debug Toolbar

Install Django Debug Toolbar for better debugging:

```bash
pip install django-debug-toolbar
```

### 2. Monitor Log Files

Monitor log files for debugging:

```bash
tail -f logs/django.log
```

### 3. Use Shell

Access Django shell for testing:

```bash
python manage.py shell
```

### 4. Browser Auto-refresh

Use tools like LiveReload for auto-refresh when files change.

## Next Steps

After the server is running properly, you can start learning about the [project structure](../guide/project-structure.md) and begin developing content.