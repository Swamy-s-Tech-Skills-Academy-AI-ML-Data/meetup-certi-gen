# Step by Step

## Step 1: Initialize Django Environment in `src/` on Windows 11 using PowerShell

### 1. Open PowerShell and Navigate to Your Project Root Directory

```powershell
cd D:\STSAALMLDT\meetup-certi-gen
```

### 2. Install Django (if not already installed)

```powershell
py -m pip install Django
```

Verify the installation with:

```powershell
py -m django --version
```

### 3. Create a New `src` Folder (if it doesn't already exist)

```powershell
mkdir src
```

### 4. Navigate into the `src` Folder

```powershell
cd src
```

### 5. Initialize a New Django Project Inside `src`

```powershell
django-admin startproject meetup_certi_gen .
```

This command creates the following folder structure inside `src`:

```text
src\
├── meetup_certi_gen\
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   ├── asgi.py
│   ├── wsgi.py
│   └── manage.py
```

### 6. Verify the Folder Structure

Run:

```powershell
Get-ChildItem -Recurse
```

Confirm that the output displays:

- A `manage.py` file in the `src/` directory.
- A `meetup_certi_gen/` folder containing `__init__.py`, `settings.py`, `urls.py`, `asgi.py`, and `wsgi.py`.

## Step 2: Create Django Apps

### 2.1 Create the Certificates App

1. Open PowerShell and make sure you are inside the `src` folder:

   ```powershell
   cd D:\STSAALMLDT\meetup-certi-gen\src
   ```

2. Create the `certificates` app:

   ```powershell
   python manage.py startapp certificates
   ```

   This will generate a new folder `certificates/` with the following structure:

   ```text
   certificates/
   ├── __init__.py
   ├── admin.py
   ├── apps.py
   ├── migrations/
   │   └── __init__.py
   ├── models.py
   ├── tests.py
   ├── views.py
   └── urls.py  (you might need to create this file)
   ```

### 2.2 Create the Users App (Optional, for Authentication & Admin Dashboard)

If you plan to implement user authentication or an admin dashboard, create a `users` app:

1. Create the `users` app:

   ```powershell
   python manage.py startapp users
   ```

   This will create a folder named `users/` similar to the `certificates/` app.

### 2.3 Create the Integrations App (Optional, for External Integrations)

If you plan to integrate with external services like Google Drive or email services, create an `integrations` app:

1. Create the `integrations` app:

   ```powershell
   python manage.py startapp integrations
   ```

### 2.4 Update `INSTALLED_APPS` in Settings

Now, open `src/meetup_certi_gen/settings.py` and add your new apps to the `INSTALLED_APPS` list. For example:

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'certificates',    # Certificate generation app
    'users',           # (Optional) User management app
    'integrations',    # (Optional) External integrations app
]
```

### **2.5 Verify the Changes**

1. **Run Migrations:**  
   This step ensures that Django recognizes your new apps and creates the necessary database tables.

   ```powershell
   python manage.py makemigrations
   python manage.py migrate
   ```

2. **Create a Superuser (if not already created):**

   ```powershell
   python manage.py createsuperuser
   ```

3. **Run the Development Server:**

   ```powershell
   python manage.py runserver
   ```

   Open your browser at `http://127.0.0.1:8000/admin/` to verify that the Django admin panel is working and that your new apps are registered (they may appear empty until you add models).
