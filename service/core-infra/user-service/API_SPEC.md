# API Specification: User Service
**Base URL:** `/api/users`

## 1. User Management

### 1.1 Get All Users
List all users with pagination and filtering.

*   **URL:** `GET /`
*   **Query Params:** `page`, `size`, `role` (optional)
*   **Response (200 OK):**
    ```json
    {
      "content": [
        {
          "id": "550e8400-...",
          "fullName": "John Doe",
          "email": "john@bank-xyz.com",
          "department": "IT",
          "roles": ["OPERATOR"]
        }
      ],
      "totalPages": 5,
      "totalElements": 48
    }
    ```

### 1.2 Get User Detail
*   **URL:** `GET /{id}`
*   **Response (200 OK):**
    ```json
    {
      "id": "550e8400-...",
      "fullName": "John Doe",
      "email": "john@bank-xyz.com",
      "department": "IT",
      "costCenter": "IT-001",
      "jobTitle": "DevOps Engineer",
      "roles": ["OPERATOR"],
      "createdAt": "2026-01-01T10:00:00Z"
    }
    ```

### 1.3 Create User
Creates a user profile. May trigger async event to Auth Service create credential.

*   **URL:** `POST /`
*   **Body:**
    ```json
    {
      "email": "jane@bank-xyz.com",
      "fullName": "Jane Smith",
      "department": "Finance",
      "roles": ["FINANCE"]
    }
    ```
*   **Response (201 Created):**
    ```json
    {
      "id": "generated-uuid",
      "message": "User created successfully"
    }
    ```

### 1.4 Update User
*   **URL:** `PUT /{id}`
*   **Body:**
    ```json
    {
      "department": "Treasury",
      "jobTitle": "Head of Treasury"
    }
    ```

## 2. Role Management

### 2.1 Assign Role
*   **URL:** `POST /{id}/roles`
*   **Body:**
    ```json
    {
      "role": "SUPERVISOR"
    }
    ```
