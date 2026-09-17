# NUFMS — Newgate University File Management System

A custom-built Django application for digitally managing, organizing, uploading, searching, verifying, and tracking university student files and documents. Built for Newgate University with a fully custom administration dashboard — **no Django default admin site is used**.

## Overview

NUFMS replaces physical student-file management with a centralized, role-based digital system covering student records, document uploads, verification workflows, academic structure (faculties/departments/programmes), user management, reporting, and a complete audit trail.

## Features

- Custom-branded authentication (login, password reset/change, profile) — no default Django auth templates
- Fully custom admin dashboard (dashboard stats, students, documents, users, faculties, departments, programmes, reports, activity logs, settings) — Django's `/admin/` is never installed or exposed
- Role-based access control: **Super Administrator**, **Records Officer**, **Department Staff**, **Student**
- Object-level authorization (department staff only see their department; students only see their own records)
- Student registration & profile management with tabs (Overview, Academic Info, Documents, Activity)
- Document upload with file type/size validation (PDF, DOC, DOCX, JPG, JPEG, PNG)
- Document verification workflow: Pending → Verified / Rejected (with required rejection reason)
- Document viewer (embedded PDF preview, image preview, download)
- Document categories management
- Global search (students, documents, matric numbers, categories)
- Reports with filters (faculty, department, category, date) + CSV export + print-friendly view
- Full activity/audit log (login, uploads, verification, downloads, etc.) with IP tracking
- Responsive Bootstrap 5 UI with offcanvas sidebar on mobile, empty states, toasts, custom 400/403/404/500 pages
- Realistic seed data via `python manage.py seed_data`

## Technology Stack

- **Backend:** Python, Django, Django ORM, SQLite
- **Frontend:** HTML5, CSS3, Bootstrap 5, Vanilla JavaScript, Bootstrap Icons

## Installation

### 1. Create and activate a virtual environment

```bash
python3 -m venv venv
source venv/bin/activate      # Windows: venv\Scripts\activate
```

### 2. Install dependencies

```bash
pip install -r requirements.txt
```

### 3. Apply migrations

```bash
python manage.py makemigrations
python manage.py migrate
```

### 4. Seed demo data

```bash
python manage.py seed_data
```

This creates faculties, departments, programmes, document categories, staff accounts, ~60 demo students, and demo documents.

### 5. Run the development server

```bash
python manage.py runserver
```

Visit **http://127.0.0.1:8000/**. If port 8000 is busy: `python manage.py runserver 8001`.

### 6. Run tests

```bash
python manage.py test
```

## Default Demo Accounts

All created by `seed_data`:

| Role | Username | Password |
|---|---|---|
| Super Administrator | `admin` | `NUFMS@Admin2026` |
| Records Officer | `records1` | `NUFMS@Records2026` |
| Department Staff | `deptstaff1` | `NUFMS@Dept2026` |
| Student | `student1` | `NUFMS@Student2026` |

## Project Structure

```
nufms/
  manage.py
  requirements.txt
  nufms/               # settings, urls, wsgi, asgi
  accounts/            # custom User model, auth, RBAC, user management
  academics/           # Faculty, Department, Programme
  students/            # Student model & views
  documents/            # Document, DocumentCategory, verification workflow
  dashboard/            # dashboard, global search, settings, error handlers
  reports/              # reports + CSV export
  activity_logs/        # audit trail
  templates/            # all Django templates (see below)
  static/                # css/, js/, images/
  media/                  # uploaded documents & profile photos (created at runtime)
```

Templates are organized by feature: `authentication/`, `dashboard/`, `students/`, `documents/`, `users/`, `faculties/`, `departments/`, `programmes/`, `reports/`, `logs/`, `settings/`, `errors/`, `search/`, `partials/`.

## File Upload Configuration

Uploaded files are stored under `MEDIA_ROOT` (`media/`) and served via `MEDIA_URL` in development. Allowed extensions: `.pdf .doc .docx .jpg .jpeg .png`. Max size is configurable via `NUFMS_SETTINGS["MAX_UPLOAD_SIZE_MB"]` in `settings.py` (default 15 MB).

## Security Notes

- `SECRET_KEY` should be set via the `NUFMS_SECRET_KEY` environment variable in production.
- Set `NUFMS_DEBUG=False` and configure `ALLOWED_HOSTS` before deploying.
- All student/document detail and download views enforce object-level checks so a user cannot access another student's files by changing an ID in the URL.

## Known Limitations / Next Steps

- This codebase was authored in a sandboxed environment without internet access, so migrations, `seed_data`, and the dev server have **not** been executed here — run the steps above locally to verify. The code has been checked for Python syntax correctness (`py_compile`) but not runtime-tested against a live Django install.
- Password reset emails print to the console by default (add an `EMAIL_BACKEND` for real delivery).
- Charts on the dashboard use simple progress bars rather than a JS charting library; swap in Chart.js easily if desired.
