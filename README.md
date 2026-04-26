# 🍪 Django + React Cookie Authentication Project

A full-stack authentication system using **Django (DRF)** and **React**, with secure cookie-based authentication (HTTP-only cookies, refresh tokens, and CSRF protection).

---

## 🚀 Features

* 🔐 JWT Authentication with cookies (access + refresh tokens)
* 🍪 HTTP-only cookies for improved security
* 🔁 Automatic token refresh flow
* 🛡️ CSRF protection enabled
* 👤 User authentication (login, logout, register)
* ⚛️ React frontend with protected routes
* 🌍 Cross-origin setup (CORS + credentials)

---

## 🧱 Tech Stack

### Backend

* Django
* Django REST Framework
* SimpleJWT
* django-cors-headers

### Frontend

* React (Vite or CRA)
* Axios

---

## 📁 Project Structure

```
project-root/
│
├── backend/
│   ├── api/
│   ├── user/
│   └── manage.py
│
├── frontend/
│   ├── src/
│   └── package.json
│
└── README.md
```

---

## ⚙️ Backend Setup (Django)

### 1. Install dependencies

```bash
pip install django djangorestframework djangorestframework-simplejwt django-cors-headers
```
or

```bash
pip install -U -r requirements.txt
```
---

### 2. Settings Configuration

#### CORS & Cookies

```python
CORS_ALLOW_CREDENTIALS = True

CORS_ALLOWED_ORIGINS = [
    "http://localhost:5173",  # React dev server
]

CSRF_TRUSTED_ORIGINS = [
    "http://localhost:5173",
]

SESSION_COOKIE_SECURE = False  # True in production
CSRF_COOKIE_SECURE = False     # True in production
```

---

### 3. SimpleJWT Configuration

```python
from datetime import timedelta

SIMPLE_JWT = {
    "ACCESS_TOKEN_LIFETIME": timedelta(minutes=15),
    "REFRESH_TOKEN_LIFETIME": timedelta(days=1),
}
```

## ⚛️ Frontend Setup (React)

### 1. Install Packages

```bash
npm install
```

Cookies are automatically sent because of `withCredentials: true`.

---

## 🔄 Token Refresh Flow

* Access token expires quickly
* Refresh token stored in cookie
* On 401 error → call refresh endpoint
* Backend issues new access token

---

## 🔐 Security Notes

* Always use:

  * `httponly=True`
  * `secure=True` (in production)
* Use `SameSite=None` if frontend & backend are on different domains
* Enable HTTPS in production
* Never store tokens in localStorage (avoid XSS risks)

---

## 🌍 Environment Variables

### Backend

```
DEBUG=True
FRONTEND_URL=http://localhost:3000
```

### Frontend

```
VITE_API_URL=http://localhost:8000
```

---

## 🧪 Running the Project

### Backend

```bash
python manage.py runserver
```

### Frontend

```bash
npm run dev
```

---

## 🐛 Common Issues

### Cookies not being set

* Check:

  * `withCredentials: true`
  * CORS_ALLOW_CREDENTIALS = True
  * Correct domain & port

---

### 401 Unauthorized

* Token expired → implement refresh logic
* Cookie not sent → check browser settings

---

### CSRF Errors

* Ensure `CSRF_TRUSTED_ORIGINS` is set
* Send CSRF token if needed
---

## 📄 License

This project is licensed under the MIT License.

---

## 💡 Author

Built with ❤️ using Django & React
