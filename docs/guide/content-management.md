# Mengelola Konten

Panduan lengkap untuk mengelola konten di RhamaaCMS menggunakan admin interface Wagtail.

## Akses Admin Panel

### Login ke Admin

1. Buka browser dan akses `http://localhost:8000/admin`
2. Masukkan kredensial:
   - **Username:** `rhamaa`
   - **Password:** `admin`

!!! warning "Keamanan"
    Segera ganti password default setelah login pertama kali!

### Dashboard Admin

Setelah login, Anda akan melihat dashboard dengan menu utama:
- **Pages** - Mengelola halaman website
- **Images** - Mengelola gambar
- **Documents** - Mengelola dokumen
- **Snippets** - Mengelola snippet konten
- **Users** - Mengelola user
- **Settings** - Pengaturan situs

## Mengelola Halaman

### Struktur Halaman

Wagtail menggunakan struktur tree untuk halaman:
```
Root
└── Home (Homepage)
    ├── About
    ├── Services
    │   ├── Web Development
    │   └── Mobile Apps
    └── Contact
```

### Membuat Halaman Baru

1. **Navigasi ke Pages**
   - Klik "Pages" di menu utama
   - Pilih parent page (biasanya Home)

2. **Pilih Tipe Halaman**
   - Klik "Add child page"
   - Pilih tipe halaman (Content Page, Landing Page, dll)

3. **Isi Konten**
   - **Title**: Judul halaman
   - **Slug**: URL slug (otomatis generate dari title)
   - **Content**: Isi konten menggunakan StreamField

4. **Pengaturan SEO**
   - **Search description**: Meta description untuk SEO
   - **Tags**: Tag untuk kategorisasi

5. **Publikasi**
   - **Save draft**: Simpan sebagai draft
   - **Publish**: Publikasikan langsung
   - **Schedule**: Jadwalkan publikasi

### Edit Halaman Existing

1. Navigasi ke halaman yang ingin diedit
2. Klik "Edit" di toolbar admin
3. Lakukan perubahan
4. Pilih action:
   - **Save draft**: Simpan perubahan sebagai draft
   - **Publish**: Publikasikan perubahan
   - **Replace current draft**: Ganti draft yang ada

### Mengelola Status Halaman

#### Draft vs Published

- **Draft**: Halaman belum dipublikasi, hanya bisa dilihat di admin
- **Published**: Halaman sudah live dan bisa diakses publik
- **Scheduled**: Halaman dijadwalkan untuk publikasi otomatis

#### Workflow Approval

Untuk tim yang lebih besar, aktifkan workflow approval:

1. **Settings** → **Workflow**
2. Buat workflow baru
3. Assign ke halaman atau section tertentu
4. Set approver dan reviewer

## Menggunakan StreamField

### Konsep StreamField

StreamField memungkinkan editor membuat konten yang fleksibel dengan berbagai tipe blok.

### Menambah Blok Konten

1. **Klik tombol "+"** di StreamField
2. **Pilih tipe blok**:
   - **Heading**: Judul section
   - **Paragraph**: Teks paragraf
   - **Image**: Gambar dengan caption
   - **Quote**: Kutipan
   - **Hero**: Section hero dengan CTA

3. **Isi konten blok**
4. **Atur urutan** dengan drag & drop
5. **Duplikasi blok** dengan tombol copy
6. **Hapus blok** dengan tombol delete

### Rich Text Editor

Untuk blok paragraph dan rich text:

#### Formatting Options
- **Bold** dan *Italic*
- Heading levels (H2, H3, H4)
- Lists (bullet dan numbered)
- Links (internal dan external)
- Embed media

#### Keyboard Shortcuts
- `Ctrl+B`: Bold
- `Ctrl+I`: Italic
- `Ctrl+K`: Insert link
- `Ctrl+Z`: Undo
- `Ctrl+Y`: Redo

### Custom Blocks

RhamaaCMS menyediakan custom blocks:

#### Hero Block
- **Title**: Judul utama
- **Subtitle**: Subjudul
- **Background Image**: Gambar latar
- **CTA Button**: Tombol call-to-action

#### Card Block
- **Title**: Judul kartu
- **Description**: Deskripsi
- **Image**: Gambar kartu
- **Link**: URL tujuan

#### Gallery Block
- **Title**: Judul galeri
- **Images**: Multiple gambar
- **Layout**: Grid atau carousel

## Mengelola Media

### Upload Gambar

1. **Navigasi ke Images**
2. **Klik "Add an image"**
3. **Upload file** (drag & drop atau browse)
4. **Isi metadata**:
   - **Title**: Nama gambar
   - **Alt text**: Untuk accessibility
   - **Tags**: Untuk kategorisasi

### Optimasi Gambar

#### Format yang Didukung
- **JPEG**: Untuk foto
- **PNG**: Untuk gambar dengan transparency
- **WebP**: Format modern untuk web
- **SVG**: Untuk icon dan logo

#### Best Practices
- Gunakan alt text yang deskriptif
- Compress gambar sebelum upload
- Gunakan format yang tepat
- Pertimbangkan responsive images

### Upload Dokumen

1. **Navigasi ke Documents**
2. **Klik "Add a document"**
3. **Upload file**
4. **Isi informasi**:
   - **Title**: Nama dokumen
   - **Tags**: Kategorisasi

#### Format Dokumen
- PDF, DOC, DOCX
- XLS, XLSX
- PPT, PPTX
- TXT, CSV

## Mengelola Menu Navigation

### Main Navigation

Edit navigation menu di:
1. **Settings** → **Navigation**
2. **Add menu item**:
   - **Link to page**: Link ke halaman internal
   - **Custom URL**: Link eksternal
   - **Label**: Teks yang ditampilkan

### Footer Links

Kelola footer links di:
1. **Snippets** → **Footer Links**
2. **Add footer link**
3. **Atur urutan** dengan drag & drop

## SEO dan Metadata

### Page-level SEO

Untuk setiap halaman:
1. **Promote tab**:
   - **Search description**: Meta description
   - **Tags**: Content tags
   - **Featured image**: Social media image

2. **Settings tab**:
   - **Show in menus**: Tampilkan di navigation
   - **Privacy**: Public atau private

### Site-wide SEO

1. **Settings** → **Sites**
2. **Edit site**:
   - **Site name**: Nama situs
   - **Hostname**: Domain utama

### Social Media

Konfigurasi social media metadata:
- Open Graph tags
- Twitter Card tags
- Structured data (JSON-LD)

## Workflow dan Collaboration

### User Roles

RhamaaCMS mendukung berbagai user roles:

#### Editor
- Buat dan edit konten
- Tidak bisa publish tanpa approval

#### Moderator
- Review dan approve konten
- Manage user permissions

#### Administrator
- Full access ke semua fitur
- Manage site settings

### Comments dan Review

1. **Enable moderation** di page settings
2. **Add comments** untuk feedback
3. **Track changes** dengan revision history

### Revision History

Setiap halaman menyimpan history:
1. **Klik "History"** di page editor
2. **Compare versions** untuk melihat perubahan
3. **Revert** ke versi sebelumnya jika diperlukan

## Tips dan Best Practices

### Content Strategy

1. **Rencanakan struktur** sebelum membuat konten
2. **Gunakan consistent naming** untuk halaman
3. **Optimize untuk SEO** dengan keyword research
4. **Regular content audit** untuk update konten lama

### Performance

1. **Optimize images** sebelum upload
2. **Gunakan caching** untuk konten yang jarang berubah
3. **Minimize plugins** yang tidak perlu
4. **Regular database cleanup**

### Accessibility

1. **Gunakan alt text** untuk semua gambar
2. **Proper heading hierarchy** (H1, H2, H3)
3. **Descriptive link text**
4. **Color contrast** yang cukup

### Security

1. **Regular password updates**
2. **Limit user permissions** sesuai kebutuhan
3. **Monitor login attempts**
4. **Keep system updated**

## Troubleshooting

### Konten Tidak Muncul

**Kemungkinan penyebab:**
- Halaman masih draft
- Cache belum clear
- Template error

**Solusi:**
1. Check status publikasi
2. Clear cache browser
3. Check error logs

### Gambar Tidak Loading

**Kemungkinan penyebab:**
- File terlalu besar
- Format tidak didukung
- Permission error

**Solusi:**
1. Compress gambar
2. Convert ke format yang didukung
3. Check file permissions

### Editor Tidak Responsive

**Solusi:**
1. Clear browser cache
2. Disable browser extensions
3. Try different browser
4. Check JavaScript errors

## Langkah Selanjutnya

Setelah menguasai content management, lanjutkan ke [Kustomisasi Template](template-customization.md) untuk mempelajari cara mengkustomisasi tampilan website.