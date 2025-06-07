# Django CRUD Sederhana

Dokumentasi ini menjelaskan langkah-langkah membuat aplikasi CRUD sederhana menggunakan Django dan SQLite.

---


## 🗂️ Tentang SQLite dan File `db.sqlite3`

Django secara default menggunakan **SQLite** sebagai sistem database bawaan. SQLite adalah database ringan berbasis file, artinya semua data disimpan dalam satu file saja (`db.sqlite3`). File ini otomatis dibuat saat perintah `python manage.py migrate` dijalankan pertama kali. File ini menyimpan seluruh struktur database dan data proyek Anda, seperti tabel pengguna, session, dan model yang Anda buat.

Anda tidak perlu menginstal software tambahan untuk menggunakan SQLite, karena sudah terintegrasi dalam Python dan Django. File `db.sqlite3` bisa digunakan untuk pengembangan dan testing, tetapi untuk aplikasi produksi disarankan menggunakan PostgreSQL atau MySQL.

---


## 📦 Persiapan Awal

### 1. Buat Proyek Django

```bash
django-admin startproject djangoproject
cd djangoproject
```

### 2. Jalankan Server (Cek Awal)

```bash
python manage.py runserver 8080
```

Jika berhasil, buka browser ke `http://localhost:8080/`

### 3. Buat Aplikasi

```bash
python manage.py startapp djangoweb
```

Tambahkan `'djangoweb',` ke `INSTALLED_APPS` di `settings.py`.

---

## 🧩 Buat Model `Buku`

Di `djangoweb/models.py`:

```python
from django.db import models

class Buku(models.Model):
    judul = models.CharField(max_length=100)
    penulis = models.CharField(max_length=100)
    tahun_terbit = models.IntegerField()

    def __str__(self):
        return self.judul
```

### Migrasi Model ke Database

```bash
python manage.py makemigrations
python manage.py migrate
```

---

## 🔐 Admin Panel (Opsional)

Di `djangoweb/admin.py`:

```python
from django.contrib import admin
from .models import Buku

admin.site.register(Buku)
```

Buat superuser:

```bash
python manage.py createsuperuser
```

Akses: `http://localhost:8080/admin/`

---

## ✍️ Views dan URL

### `views.py` untuk CRUD dasar

```python
from django.shortcuts import render, redirect, get_object_or_404
from .models import Buku

def daftar_buku(request):
    buku = Buku.objects.all()
    return render(request, 'djangoweb/daftar_buku.html', {'buku': buku})

def tambah_buku(request):
    if request.method == 'POST':
        Buku.objects.create(
            judul=request.POST['judul'],
            penulis=request.POST['penulis'],
            tahun_terbit=request.POST['tahun_terbit']
        )
        return redirect('daftar_buku')
    return render(request, 'djangoweb/tambah_buku.html')
```

### Buat File `urls.py` di folder `djangoweb/`

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.daftar_buku, name='daftar_buku'),
    path('tambah/', views.tambah_buku, name='tambah_buku'),
]
```

### Tambahkan routing `urls.py` di `djangoproject/urls.py`

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('djangoweb.urls')),
]
```

---

## 🖼️ Template HTML

Buat folder `templates/djangoweb/` di dalam folder app `djangoweb`, lalu:

### `daftar_buku.html`

```html
<h1>Daftar Buku</h1>
<ul>
  {% for b in buku %}
    <li>{{ b.judul }} oleh {{ b.penulis }} ({{ b.tahun_terbit }})</li>
  {% endfor %}
</ul>
<a href="{% url 'tambah_buku' %}">Tambah Buku</a>
```

### `tambah_buku.html`

```html
<h1>Tambah Buku</h1>
<form method="post">
  {% csrf_token %}
  Judul: <input type="text" name="judul"><br>
  Penulis: <input type="text" name="penulis"><br>
  Tahun Terbit: <input type="number" name="tahun_terbit"><br>
  <button type="submit">Simpan</button>
</form>
```

---

## ▶️ Jalankan Server

```bash
python manage.py runserver
```

* Daftar Buku: `http://localhost:8080/`
* Tambah Buku: `http://localhost:8080/tambah/`

---

## 🧪 Testing CRUD

* Tambah beberapa data buku melalui form.
* Periksa apakah tampil di daftar.
* (Bonus) Tambahkan fitur edit dan delete.

---

## ✅ Penutup

Ini adalah contoh CRUD sederhana dengan Django. Kamu bisa mengembangkannya dengan forms, class-based views, validasi, dan database lain seperti PostgreSQL atau MySQL.

---