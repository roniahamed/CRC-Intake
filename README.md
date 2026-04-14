# CheckIn — Smart Patient Intake & Queue Management System

CheckIn is a modern patient flow management system designed to make clinic and hospital intake processes faster, smoother, and more organized.

It simplifies the entire patient journey — from initial registration and form filling to real-time queue management and progress tracking. Doctors and staff can instantly see who is waiting, who is next, and how long patients have been waiting, helping reduce chaos and long wait times.

Patients benefit from a quicker, more transparent check-in experience, while staff receive timely notifications and updates so nothing falls through the cracks. With role-based access, the system ensures the right people have the right tools — reception handles intake, doctors manage their flow, and administrators oversee everything.
Key benefits include:

- Faster and more accurate patient intake with built-in validation
- Real-time queue visibility and live updates
- Automatic notifications to keep everyone informed
- Clear tracking of patient progress from arrival to consultation
- Secure and controlled access based on user roles

Whether you're running a busy clinic, diagnostic center, or hospital department, CheckIn helps reduce waiting times, improve staff coordination, and deliver a better experience for both patients and healthcare teams.



## Features
- JWT-based authentication using DRF Simple JWT with role claims embedded in tokens.
- Custom DRF authentication backend for token-based API access.
- Role-based permissions for Form, Doctor, and Queue workflows.
- Patient intake endpoint with strong serializer-level validation rules.
- Queue lifecycle management with transactional updates and wait-time tracking.
- Real-time queue event broadcasting over WebSocket with Django Channels.
- Asynchronous email notifications for new check-ins using Celery and Redis.
- Admin-managed access tokens and recipient configuration for operational control.
- Image upload support for patient profiles with file type and size validation.

## Tech Stack
[![Django](https://img.shields.io/badge/Django-5.2.6-092E20?style=flat&logo=django&logoColor=white)](https://www.djangoproject.com/)
[![Django REST Framework](https://img.shields.io/badge/Django%20REST%20Framework-3.16.1-000000?style=flat&logo=django&logoColor=white)](https://www.django-rest-framework.org/)
[![Simple JWT](https://img.shields.io/badge/SimpleJWT-5.5.1-4B8BBE?style=flat)](https://django-rest-framework-simplejwt.readthedocs.io/)
[![Django Channels](https://img.shields.io/badge/Channels-4.3.1-0C4B33?style=flat)](https://channels.readthedocs.io/)
[![Celery](https://img.shields.io/badge/Celery-5.5.3-37814A?style=flat&logo=celery&logoColor=white)](https://docs.celeryq.dev/)
[![Redis](https://img.shields.io/badge/Redis-6.x-DC382D?style=flat&logo=redis&logoColor=white)](https://redis.io/)
[![SQLite](https://img.shields.io/badge/SQLite-Default-003B57?style=flat&logo=sqlite&logoColor=white)](https://www.sqlite.org/)
[![Django Unfold](https://img.shields.io/badge/Django%20Unfold-0.67.0-1F2937?style=flat)](https://github.com/unfoldadmin/django-unfold)

- Django 5.2.6
- Django REST Framework 3.16.1
- Database: Postgresql, configurable via environment variables
- Authentication: Custom JWT authentication class with role-aware access rules
- Real-time communication: Django Channels with Redis channel layer
- Async jobs: Celery worker and optional Celery Beat

## Prerequisites
- Python 3.12 or higher
- pip
- Virtual environment tool (venv)
- Redis server (required for Celery and Channels)
- SQLite for local development (or PostgreSQL/MySQL in production)

## Installation and Setup
```bash
# 1) Clone the repository
git clone git@github.com:roniahamed/CRC-Intake.git
cd CRC-Intake

# 2) Create and activate virtual environment
python -m venv env
source env/bin/activate
# On Windows use: env\Scripts\activate

# 3) Install dependencies
pip install -r requirements.txt

# 4) Create environment file
cp .env.example .env

# 5) Apply migrations
python manage.py migrate

# 6) Create superuser
python manage.py createsuperuser
```

## Environment Variables
| Variable | Description |
|---|---|
| DEBUG | Enable or disable Django debug mode. |
| SECRET_KEY | Django cryptographic secret key. |
| ALLOWED_HOSTS | Comma-separated list of allowed domains/hosts. |
| SITE_URL | Site URL used in environment-level settings. |
| SITE_DOMAIN | Base domain used for absolute links (for example media URLs in emails). |
| DB_ENGINE | Django database backend engine (default: sqlite3 backend). |
| DB_NAME | Database name or sqlite file path. |
| DB_USER | Database username (non-sqlite databases). |
| DB_PASSWORD | Database password (non-sqlite databases). |
| DB_HOST | Database host (non-sqlite databases). |
| DB_PORT | Database port (non-sqlite databases). |
| EMAIL_BACKEND | Django email backend class path. |
| EMAIL_HOST | SMTP host for outgoing mail. |
| EMAIL_PORT | SMTP port. |
| EMAIL_USE_TLS | Enable TLS for email transport. |
| EMAIL_HOST_USER | SMTP account username/email. |
| EMAIL_HOST_PASSWORD | SMTP account password or app password. |
| DEFAULT_FROM_EMAIL | Sender email used by the application. |
| CELERY_BROKER_URL | Celery broker URL (Redis). |
| CELERY_RESULT_BACKEND | Celery result backend URL. |
| CELERY_TIMEZONE | Timezone for Celery processes. |
| MEDIA_URL | URL prefix for media files. |
| MEDIA_ROOT | Filesystem path for media storage. |
| CSRF_TRUSTED_ORIGINS | Comma-separated trusted origins for CSRF. |
| SECURE_SSL_REDIRECT | Force HTTPS redirects in production. |
| SESSION_COOKIE_SECURE | Send session cookies over HTTPS only. |
| CSRF_COOKIE_SECURE | Send CSRF cookie over HTTPS only. |

## Running the Project
```bash
# Local development
python manage.py runserver
```

```bash
# ASGI server (recommended when testing WebSockets)
daphne -b 0.0.0.0 -p 8000 CheckIn.asgi:application
```

```bash
# Celery worker
celery -A CheckIn worker -l info
```

```bash
# Celery beat (optional)
celery -A CheckIn beat -l info
```

```bash
# Docker (if you add Docker support files)
docker compose up --build -d
```

```bash
# Production example
gunicorn CheckIn.asgi:application \
  --worker-class uvicorn.workers.UvicornWorker \
  --bind 0.0.0.0:8000 \
  --workers 4
```

## Project Structure
```text
CRC-Intake/
├── CheckIn/
│   ├── settings.py
│   ├── urls.py
│   ├── asgi.py
│   ├── celery.py
│   └── wsgi.py
├── management/
│   ├── authentication.py
│   ├── permissions.py
│   ├── models.py
│   ├── serializers.py
│   ├── views.py
│   ├── urls.py
│   ├── tasks.py
│   ├── consumers.py
│   ├── routing.py
│   └── management/commands/cleanup_sitesettings.py
├── templates/
│   └── emails/patient_details.html
├── docs/
├── postman collection/
├── manage.py
├── requirements.txt
└── .env.example
```

## Running Migrations
```bash


# Create migrations
python manage.py makemigrations

# Apply migrations
python manage.py migrate
```

## Deployment
- Render or Railway: deploy the Django service, attach Redis, and configure all environment variables.
- AWS: run with Gunicorn/Uvicorn behind Nginx, use managed Redis (ElastiCache), and configure secure secrets.
- Docker: create Dockerfile and docker-compose for web, redis, celery worker, and celery beat services.
- VPS: use systemd units for Gunicorn, Celery, and Celery Beat with reverse proxy and TLS.


## Custom License
This project is licensed under a custom license.

- Copyright (c) 2026 Roni Ahamed.
- Permission is granted for view-only and evaluation purposes.
- Commercial use, resale, and redistribution are prohibited without prior written permission.
- See the LICENSE file in the repository root for the full license text.


## Contact
- **GitHub**: [@roniahamed](https://github.com/roniahamed)
- **Portfolio**: [roniahamed.com](https://www.roniahamed.com)
- **LinkedIn**: [Roni Ahamed](https://www.linkedin.com/in/roniahamed/)
- **Email**: [mdroniahamed56@gmail.com](mailto:mdroniahamed56@gmail.com)

If you find this project useful, please consider starring the repository on GitHub.
