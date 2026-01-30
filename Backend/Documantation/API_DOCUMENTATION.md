# API Documentation

## Base URL

All API requests should be made to:

```
http://localhost:3000/api/v1
```

## Authentication

Most endpoints require authentication. Include the JWT token in the request header:

```
Authorization: Bearer <your_jwt_token>
```

## Response Format

### Success Response

```json
{
  "success": true,
  "data": {
    // Response data
  },
  "message": "Success message"
}
```

### Error Response

```json
{
  "success": false,
  "error": {
    "message": "Error message",
    "code": "ERROR_CODE"
  }
}
```

## Status Codes

| Code | Description |
|------|-------------|
| 200 | OK - Request successful |
| 201 | Created - Resource created successfully |
| 400 | Bad Request - Invalid request parameters |
| 401 | Unauthorized - Authentication required |
| 403 | Forbidden - Insufficient permissions |
| 404 | Not Found - Resource not found |
| 500 | Internal Server Error - Server error |

---

## Authentication Endpoints

### Register User

Create a new user account.

**Endpoint:** `POST /auth/register`

**Request Body:**
```json
{
  "username": "john_doe",
  "email": "john@example.com",
  "password": "SecurePassword123",
  "fullName": "John Doe"
}
```

**Response (201):**
```json
{
  "success": true,
  "data": {
    "user": {
      "id": "user_id",
      "username": "john_doe",
      "email": "john@example.com",
      "fullName": "John Doe"
    },
    "token": "jwt_token_here"
  },
  "message": "User registered successfully"
}
```

**Error Responses:**
- `400` - Validation error
- `409` - User already exists

---

### Login

Authenticate user and receive JWT token.

**Endpoint:** `POST /auth/login`

**Request Body:**
```json
{
  "email": "john@example.com",
  "password": "SecurePassword123"
}
```

**Response (200):**
```json
{
  "success": true,
  "data": {
    "user": {
      "id": "user_id",
      "username": "john_doe",
      "email": "john@example.com",
      "fullName": "John Doe"
    },
    "token": "jwt_token_here"
  },
  "message": "Login successful"
}
```

**Error Responses:**
- `400` - Invalid credentials
- `401` - Authentication failed

---

### Get Current User

Get the currently authenticated user's information.

**Endpoint:** `GET /auth/me`

**Headers:**
```
Authorization: Bearer <token>
```

**Response (200):**
```json
{
  "success": true,
  "data": {
    "user": {
      "id": "user_id",
      "username": "john_doe",
      "email": "john@example.com",
      "fullName": "John Doe",
      "createdAt": "2024-01-01T00:00:00.000Z"
    }
  }
}
```

**Error Responses:**
- `401` - Unauthorized

---

### Logout

Invalidate the current user's token.

**Endpoint:** `POST /auth/logout`

**Headers:**
```
Authorization: Bearer <token>
```

**Response (200):**
```json
{
  "success": true,
  "message": "Logged out successfully"
}
```

---

## User Endpoints

### Get All Users

Retrieve a list of all users (admin only).

**Endpoint:** `GET /users`

**Headers:**
```
Authorization: Bearer <token>
```

**Query Parameters:**
- `page` (optional) - Page number (default: 1)
- `limit` (optional) - Items per page (default: 10)
- `search` (optional) - Search term

**Response (200):**
```json
{
  "success": true,
  "data": {
    "users": [
      {
        "id": "user_id",
        "username": "john_doe",
        "email": "john@example.com",
        "fullName": "John Doe"
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 10,
      "total": 100,
      "pages": 10
    }
  }
}
```

---

### Get User by ID

Retrieve a specific user by ID.

**Endpoint:** `GET /users/:id`

**Headers:**
```
Authorization: Bearer <token>
```

**Response (200):**
```json
{
  "success": true,
  "data": {
    "user": {
      "id": "user_id",
      "username": "john_doe",
      "email": "john@example.com",
      "fullName": "John Doe",
      "createdAt": "2024-01-01T00:00:00.000Z"
    }
  }
}
```

**Error Responses:**
- `404` - User not found

---

### Update User

Update user information.

**Endpoint:** `PUT /users/:id`

**Headers:**
```
Authorization: Bearer <token>
```

**Request Body:**
```json
{
  "fullName": "John Updated",
  "email": "newemail@example.com"
}
```

**Response (200):**
```json
{
  "success": true,
  "data": {
    "user": {
      "id": "user_id",
      "username": "john_doe",
      "email": "newemail@example.com",
      "fullName": "John Updated"
    }
  },
  "message": "User updated successfully"
}
```

**Error Responses:**
- `400` - Validation error
- `401` - Unauthorized
- `404` - User not found

---

### Delete User

Delete a user account.

**Endpoint:** `DELETE /users/:id`

**Headers:**
```
Authorization: Bearer <token>
```

**Response (200):**
```json
{
  "success": true,
  "message": "User deleted successfully"
}
```

**Error Responses:**
- `401` - Unauthorized
- `403` - Forbidden
- `404` - User not found

---

## Resource Endpoints

### Get All Resources

Retrieve a list of resources.

**Endpoint:** `GET /resources`

**Headers:**
```
Authorization: Bearer <token>
```

**Query Parameters:**
- `page` (optional) - Page number
- `limit` (optional) - Items per page
- `sort` (optional) - Sort field
- `order` (optional) - Sort order (asc/desc)
- `filter` (optional) - Filter criteria

**Response (200):**
```json
{
  "success": true,
  "data": {
    "resources": [
      {
        "id": "resource_id",
        "title": "Resource Title",
        "description": "Resource description",
        "createdAt": "2024-01-01T00:00:00.000Z"
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 10,
      "total": 50,
      "pages": 5
    }
  }
}
```

---

### Get Resource by ID

Retrieve a specific resource by ID.

**Endpoint:** `GET /resources/:id`

**Headers:**
```
Authorization: Bearer <token>
```

**Response (200):**
```json
{
  "success": true,
  "data": {
    "resource": {
      "id": "resource_id",
      "title": "Resource Title",
      "description": "Resource description",
      "content": "Full resource content",
      "author": {
        "id": "user_id",
        "username": "john_doe"
      },
      "createdAt": "2024-01-01T00:00:00.000Z",
      "updatedAt": "2024-01-01T00:00:00.000Z"
    }
  }
}
```

**Error Responses:**
- `404` - Resource not found

---

### Create Resource

Create a new resource.

**Endpoint:** `POST /resources`

**Headers:**
```
Authorization: Bearer <token>
```

**Request Body:**
```json
{
  "title": "New Resource",
  "description": "Resource description",
  "content": "Resource content here"
}
```

**Response (201):**
```json
{
  "success": true,
  "data": {
    "resource": {
      "id": "resource_id",
      "title": "New Resource",
      "description": "Resource description",
      "content": "Resource content here",
      "author": "user_id",
      "createdAt": "2024-01-01T00:00:00.000Z"
    }
  },
  "message": "Resource created successfully"
}
```

**Error Responses:**
- `400` - Validation error
- `401` - Unauthorized

---

### Update Resource

Update an existing resource.

**Endpoint:** `PUT /resources/:id`

**Headers:**
```
Authorization: Bearer <token>
```

**Request Body:**
```json
{
  "title": "Updated Resource Title",
  "description": "Updated description",
  "content": "Updated content"
}
```

**Response (200):**
```json
{
  "success": true,
  "data": {
    "resource": {
      "id": "resource_id",
      "title": "Updated Resource Title",
      "description": "Updated description",
      "content": "Updated content",
      "updatedAt": "2024-01-01T00:00:00.000Z"
    }
  },
  "message": "Resource updated successfully"
}
```

**Error Responses:**
- `400` - Validation error
- `401` - Unauthorized
- `403` - Forbidden (not the owner)
- `404` - Resource not found

---

### Delete Resource

Delete a resource.

**Endpoint:** `DELETE /resources/:id`

**Headers:**
```
Authorization: Bearer <token>
```

**Response (200):**
```json
{
  "success": true,
  "message": "Resource deleted successfully"
}
```

**Error Responses:**
- `401` - Unauthorized
- `403` - Forbidden
- `404` - Resource not found

---

## Rate Limiting

API requests are rate-limited to prevent abuse:

- **Authenticated requests**: 100 requests per 15 minutes
- **Unauthenticated requests**: 20 requests per 15 minutes

Rate limit headers are included in responses:

```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 99
X-RateLimit-Reset: 1609459200
```

---

## Pagination

List endpoints support pagination using query parameters:

- `page` - Page number (default: 1)
- `limit` - Items per page (default: 10, max: 100)

Example:
```
GET /api/v1/resources?page=2&limit=20
```

---

## Filtering and Sorting

Many list endpoints support filtering and sorting:

**Sorting:**
```
GET /api/v1/resources?sort=createdAt&order=desc
```

**Filtering:**
```
GET /api/v1/resources?filter[status]=active&filter[category]=tech
```

---

## Webhooks

The API supports webhooks for real-time notifications. Configure webhooks in your account settings.

**Webhook Events:**
- `user.created`
- `user.updated`
- `resource.created`
- `resource.updated`
- `resource.deleted`

---

## SDK and Code Examples

### cURL Examples

**Register User:**
```bash
curl -X POST http://localhost:3000/api/v1/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "username": "john_doe",
    "email": "john@example.com",
    "password": "SecurePassword123",
    "fullName": "John Doe"
  }'
```

**Login:**
```bash
curl -X POST http://localhost:3000/api/v1/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "john@example.com",
    "password": "SecurePassword123"
  }'
```

**Get Resources:**
```bash
curl -X GET http://localhost:3000/api/v1/resources \
  -H "Authorization: Bearer <your_token>"
```

### JavaScript (Fetch) Examples

**Register User:**
```javascript
const response = await fetch('http://localhost:3000/api/v1/auth/register', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    username: 'john_doe',
    email: 'john@example.com',
    password: 'SecurePassword123',
    fullName: 'John Doe'
  })
});

const data = await response.json();
```

**Authenticated Request:**
```javascript
const response = await fetch('http://localhost:3000/api/v1/resources', {
  method: 'GET',
  headers: {
    'Authorization': `Bearer ${token}`,
    'Content-Type': 'application/json'
  }
});

const data = await response.json();
```

---

## Changelog

### Version 1.0.0
- Initial API release
- Authentication endpoints
- User management endpoints
- Resource CRUD operations

---

## Support

For API support, please contact:
- Email: support@example.com
- Documentation: [Full Documentation](./README.md)
- Issues: [GitHub Issues](https://github.com/your-repo/issues)
