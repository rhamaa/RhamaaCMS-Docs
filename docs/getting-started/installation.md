# Instalasi RhamaaCMS

Panduan lengkap untuk menginstall dan menyiapkan RhamaaCMS di sistem Anda.

## Persyaratan Sistem

Sebelum memulai, pastikan sistem Anda memenuhi persyaratan berikut:

### Python Version

Anda memerlukan [versi Python yang kompatibel](https://docs.wagtail.org/en/stable/releases/upgrading.html#compatible-django-python-versions) dengan Wagtail:

=== "Linux/macOS"
    ```bash
    python --version
    # atau
    python3 --version
    ```

=== "Windows"
    ```cmd
    py --version
    ```

!!! tip "Versi yang Direkomendasikan"
    Gunakan Python 3.8 atau lebih baru untuk kompatibilitas terbaik.

## Langkah Instalasi

### 1. Membuat Virtual Environment

Virtual environment membantu mengisolasi dependencies proyek Anda:

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

!!! warning "Penting"
    Pastikan virtual environment aktif sebelum melanjutkan. Anda akan melihat `(env)` di awal prompt terminal.

### 2. Navigasi ke Direktori Proyek

```bash
cd myproject
```

### 3. Install Wagtail

```bash
pip install wagtail
```

### 4. Inisialisasi Proyek dengan Template RhamaaCMS

```bash
wagtail start --template=https://github.com/rhamaa/RhamaaCMS/archive/refs/heads/main.zip myproject .
```

!!! info "Template URL"
    Template diunduh langsung dari GitHub repository RhamaaCMS. Pastikan koneksi internet stabil.

### 5. Install Dependencies Proyek

```bash
pip install -r requirements.txt
```

## Verifikasi Instalasi

Untuk memastikan instalasi berhasil, jalankan perintah berikut:

```bash
python manage.py check
```

Jika tidak ada error, instalasi Anda berhasil!

## Troubleshooting

### Error: Python tidak ditemukan

**Solusi:**
- Pastikan Python terinstall dengan benar
- Tambahkan Python ke PATH sistem
- Gunakan `python3` instead of `python` di Linux/macOS

### Error: pip tidak ditemukan

**Solusi:**
```bash
python -m ensurepip --upgrade
```

### Error: Permission denied

**Solusi:**
- Gunakan virtual environment (direkomendasikan)
- Atau gunakan `--user` flag: `pip install --user wagtail`

## Langkah Selanjutnya

Setelah instalasi selesai, lanjutkan ke [Konfigurasi Awal](initial-setup.md) untuk menyiapkan proyek Anda.