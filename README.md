# Ueetup Certificate Generation

This repository will generate the certificate for the speakers, volunteers, media team and participants etc.

> 1. Icon made by Pixel perfect from `www.flaticon.com`.

## UI

> 1. Image will come here

## Project Setup

```powershell
python --version
pip --version

pip install virtualenv
python -m venv .venv
.venv/Scripts/activate
python -m pip install --upgrade pip

pip install flask openai python-dotenv flask_sqlalchemy
py -m pip install Django

pip freeze > requirements.txt
pip install -r .\requirements.txt
```

## Tailwind

```powershell
py -m pip install django-tailwind

py -m pip list | findstr django-tailwind

py manage.py tailwind init
py manage.py tailwind install
py manage.py tailwind build
```
