# API Specification: Auth Service
**Base URL:** `/api/auth`

## 1. Authentication

### 1.1 Login
Authenticate user credentials and return access tokens.

*   **URL:** `POST /login`
*   **Access:** Public
*   **Body:**
    ```json
    {
      "email": "alexander@bank-xyz.com",
      "password": "password123"
    }
    ```
*   **Response (200 OK):**
    ```json
    {
      "accessToken": "eyJh...",
      "refreshToken": "udHE...",
      "tokenType": "Bearer",
      "expiresIn": 900,
      "user": {
        "id": "550e8400-e29b...",
        "email": "alexander@bank-xyz.com",
        "name": "Alexander Pierce",
        "role": "OPERATOR"
      }
    }
    ```
*   **Errors:**
    *   `401 Unauthorized`: Invalid credentials.
    *   `403 Forbidden`: Account inactive/locked.

### 1.2 Refresh Token
Get a new Access Token using a valid Refresh Token.

*   **URL:** `POST /refresh`
*   **Access:** Public
*   **Body:**
    ```json
    {
      "refreshToken": "udHE..."
    }
    ```
*   **Response (200 OK):**
    ```json
    {
      "accessToken": "eyJh...",
      "refreshToken": "newRefresh..." // Optional: Rotate refresh token
    }
    ```

### 1.3 Logout
Invalidate the current session (remove refresh token).

*   **URL:** `POST /logout`
*   **Access:** Authenticated
*   **Response (200 OK):**
    ```json
    {
      "message": "Logged out successfully"
    }
    ```

## 2. Validation & Introspection

### 2.1 Validate Token (Internal)
Used by API Gateway to check token validity and get user context.

*   **URL:** `GET /validate`
*   **Headers:** `Authorization: Bearer <token>`
*   **Access:** Internal Services / Gateway
*   **Response (200 OK):**
    ```json
    {
      "valid": true,
      "sub": "alexander@bank-xyz.com",
      "roles": ["OPERATOR"],
      "userId": "550e8400-e29b..."
    }
    ```

## 3. Account Management

### 3.1 Current User Info
Get profile of currently logged-in user.

*   **URL:** `GET /me`
*   **Access:** Authenticated
*   **Response (200 OK):**
    ```json
    {
      "id": "...",
      "email": "...",
      "roles": ["..."],
      "permissions": ["PR_CREATE", "PO_READ"]
    }
    ```

### 3.2 Change Password
*   **URL:** `POST /change-password`
*   **Access:** Authenticated
*   **Body:**
    ```json
    {
      "oldPassword": "...",
      "newPassword": "..."
    }
    ```
