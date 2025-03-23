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
