# API Specification

## 1. General Configuration
- **Base URL:** `https://api.example.com/v1`
- **Auth Method:** Bearer Token (JWT)

## 2. Endpoints

### Authentication
#### `POST /auth/login`
- **Request Body:**
  ```json
  {
    "email": "user@example.com",
    "password": "securepassword"
  }
  ```
- **Response (200 OK):**
  ```json
  {
    "token": "jwt-token-string"
  }
  ```
