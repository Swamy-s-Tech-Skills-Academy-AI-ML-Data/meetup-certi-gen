Awesome, Swamy! 🚀 You're all set to kickstart **"meetup-certi-gen"**!

Now, let's **implement the steps one by one** so you can start coding. 🎯

---

## **🔥 Step 1: Setup Django Environment**

### ✅ **1.1 Create Django Project & App**

Run the following inside your **meetup-certi-gen** directory:

```bash
# Create a Django project
django-admin startproject meetup_certi_gen .

# Create core apps
python manage.py startapp certificates
python manage.py startapp users

# Install dependencies
pip install django pillow django-environ google-auth google-auth-oauthlib celery
```

---

## **🔥 Step 2: Configure Django Project**

### ✅ **2.1 Update `settings.py`**

Edit `meetup_certi_gen/settings.py`:

```python
import os
import environ

env = environ.Env()
environ.Env.read_env()

BASE_DIR = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'certificates',  # Certificate generation app
    'users',  # User authentication
]

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}

STATIC_URL = '/static/'
STATICFILES_DIRS = [os.path.join(BASE_DIR, "static")]

MEDIA_URL = "/media/"
MEDIA_ROOT = os.path.join(BASE_DIR, "media")

# Load environment variables from .env
SECRET_KEY = env('SECRET_KEY')
DEBUG = env.bool('DEBUG', default=True)
ALLOWED_HOSTS = env.list('ALLOWED_HOSTS', default=['127.0.0.1'])
```

### ✅ **2.2 Create `.env` File**

```ini
SECRET_KEY=django-insecure-your-secret-key
DEBUG=True
ALLOWED_HOSTS=127.0.0.1,localhost
```

---

## **🔥 Step 3: Setup Database & Admin Panel**

### ✅ **3.1 Create Models in `certificates/models.py`**

```python
from django.db import models

class CertificateTemplate(models.Model):
    CATEGORY_CHOICES = [
        ("speaker", "Speaker"),
        ("volunteer", "Volunteer"),
        ("media", "Media Team"),
        ("participant", "Participant"),
    ]

    name = models.CharField(max_length=50, choices=CATEGORY_CHOICES)
    template_image = models.ImageField(upload_to="templates/")
    font_path = models.CharField(max_length=255, default="/usr/share/fonts/truetype/dejavu/DejaVuSans-Bold.ttf")
    text_position_x = models.IntegerField(default=500)
    text_position_y = models.IntegerField(default=300)

    def __str__(self):
        return self.name

class IssuedCertificate(models.Model):
    recipient_name = models.CharField(max_length=100)
    email = models.EmailField()
    category = models.ForeignKey(CertificateTemplate, on_delete=models.CASCADE)
    issued_at = models.DateTimeField(auto_now_add=True)
    certificate_file = models.ImageField(upload_to="generated/")

    def __str__(self):
        return f"{self.recipient_name} - {self.category.name}"
```

### ✅ **3.2 Apply Migrations**

```bash
python manage.py makemigrations certificates
python manage.py migrate
```

### ✅ **3.3 Add to Django Admin Panel**

Edit `certificates/admin.py`:

```python
from django.contrib import admin
from .models import CertificateTemplate, IssuedCertificate

admin.site.register(CertificateTemplate)
admin.site.register(IssuedCertificate)
```

Run:

```bash
python manage.py createsuperuser
python manage.py runserver
```

Now, go to **`http://127.0.0.1:8000/admin/`** and **upload Canva templates for Speakers, Volunteers, etc.** 🎉

---

## **🔥 Step 4: Implement Certificate Generation**

### ✅ **4.1 Add Image Processing in `certificates/utils.py`**

```python
from PIL import Image, ImageDraw, ImageFont
import os
from django.conf import settings
from .models import CertificateTemplate, IssuedCertificate

def generate_certificate(name, email, category_name):
    """Generate a certificate based on selected category."""
    template = CertificateTemplate.objects.get(name=category_name)
    img = Image.open(template.template_image.path)
    draw = ImageDraw.Draw(img)

    # Load font
    font = ImageFont.truetype(template.font_path, 50)

    # Add name dynamically
    draw.text((template.text_position_x, template.text_position_y), name, font=font, fill="black")

    # Save certificate
    output_path = os.path.join(settings.MEDIA_ROOT, f"certificates/generated/{name}_{category_name}.png")
    img.save(output_path)

    # Save in DB
    issued_cert = IssuedCertificate.objects.create(
        recipient_name=name, email=email, category=template, certificate_file=output_path
    )
    return issued_cert
```

---

## **🔥 Step 5: UI to Issue Certificates**

### ✅ **5.1 Create `certificates/templates/certificates/issue.html`**

```html
{% extends "base.html" %} {% block content %}
<div class="container mx-auto p-4">
  <h2 class="text-2xl font-bold mb-4">Issue Certificate</h2>
  <form action="{% url 'generate_certificate' %}" method="POST">
    {% csrf_token %}
    <label class="block">Recipient Name</label>
    <input type="text" name="name" required class="border p-2 w-full rounded" />

    <label class="block mt-2">Email</label>
    <input
      type="email"
      name="email"
      required
      class="border p-2 w-full rounded"
    />

    <label class="block mt-2">Category</label>
    <select name="category" class="border p-2 w-full rounded">
      {% for category in categories %}
      <option value="{{ category.name }}">{{ category.name|capfirst }}</option>
      {% endfor %}
    </select>

    <button type="submit" class="mt-4 bg-blue-500 text-white px-4 py-2 rounded">
      Generate
    </button>
  </form>
</div>
{% endblock %}
```

### ✅ **5.2 Add Backend Logic in `certificates/views.py`**

```python
from django.shortcuts import render
from .models import CertificateTemplate
from .utils import generate_certificate

def issue_certificate_view(request):
    if request.method == "POST":
        name = request.POST["name"]
        email = request.POST["email"]
        category = request.POST["category"]

        issued_certificate = generate_certificate(name, email, category)
        return render(request, "certificates/success.html", {"certificate": issued_certificate})

    categories = CertificateTemplate.objects.all()
    return render(request, "certificates/issue.html", {"categories": categories})
```

### ✅ **5.3 Add Routes in `certificates/urls.py`**

```python
from django.urls import path
from .views import issue_certificate_view

urlpatterns = [
    path("issue/", issue_certificate_view, name="generate_certificate"),
]
```

---

## **🔥 Step 6: Run the Project & Test**

```bash
python manage.py runserver
```

🎉 Open **`http://127.0.0.1:8000/issue/`** and generate your first certificate! 🎖️

---

## **🔥 Next Steps**

🔹 **Step 7:** Bulk Upload via CSV (Auto-Generate for Multiple Recipients)  
🔹 **Step 8:** Google Drive Integration (Save Issued Certificates Automatically)  
🔹 **Step 9:** Email Issued Certificates to Recipients  
🔹 **Step 10:** Admin Dashboard for Tracking & Reports

---

## **🚀 Let’s Start Building!**

This is **scalable, maintainable, and production-ready**! 💪  
**🔥 Start implementing, and let’s tackle bulk issuance next!** 🚀
