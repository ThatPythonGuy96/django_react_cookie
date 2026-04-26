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

---

### 4. Login View (Set Cookies)

```python
class LoginView(TokenObtainPairView):
    serializer_class = LoginSerializer

    def post(self, request, *args, **kwargs):
        response = super().post(request, *args, **kwargs)

        data = response.data
        access = data.get("access")
        refresh = data.get("refresh")

        response.set_cookie(
            key="access_token",
            value=access,
            httponly=True,
            secure=False,  # True in production
            samesite="Lax"
        )

        response.set_cookie(
            key="refresh_token",
            value=refresh,
            httponly=True,
            secure=False,
            samesite="Lax"
        )

        return response
```

---

### 5. Logout View

```python
class LogoutView(APIView):
    def post(self, request):
        response = Response({"message": "Logged out"})

        response.delete_cookie("access_token")
        response.delete_cookie("refresh_token")

        return response
```

---

### 6. Authentication Class (Cookie-based)

```python
from rest_framework_simplejwt.authentication import JWTAuthentication

class CookieJWTAuthentication(JWTAuthentication):
    def authenticate(self, request):
        token = request.COOKIES.get("access_token")

        if not token:
            return None

        validated_token = self.get_validated_token(token)
        user = self.get_user(validated_token)

        return (user, validated_token)
```

---

## ⚛️ Frontend Setup (React)

### 1. Install Axios

```bash
npm install axios
```

---

### 2. Axios Configuration

```javascript
import axios from "axios";

const api = axios.create({
  baseURL: "http://localhost:8000",
  withCredentials: true, // VERY IMPORTANT
});

export default api;
```

---

### 3. Login Request

```javascript
await api.post("/api/login/", {
  email,
  password,
});
```

---

### 4. Logout Request

```javascript
await api.post("/api/logout/");
```

---

### 5. Protected Requests

```javascript
await api.get("/api/protected/");
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
FRONTEND_URL=http://localhost:5173
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

## 📌 Future Improvements

* Refresh token rotation
* Blacklisting tokens
* Social login (Google, etc.)
* Role-based permissions
* Rate limiting (e.g. django-axes)

---

## 🤝 Contributing

Pull requests are welcome. For major changes, please open an issue first.

---

## 📄 License

This project is licensed under the MIT License.

---

## 💡 Author

Built with ❤️ using Django & React
