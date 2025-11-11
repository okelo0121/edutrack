# API Documentation

## Base URL
```
Development: http://localhost:5000/api
Production: https://your-backend-domain.com/api
```

## Authentication

All protected endpoints require the `Authorization` header:

```
Authorization: Bearer <jwt_token>
```

### Token Structure
```json
{
  "userId": "64f3a5b9c2d1e4f5g6h7i8j9",
  "email": "user@example.com",
  "userType": "teacher",
  "iat": 1700000000,
  "exp": 1700604800
}
```

---

## Auth Endpoints

### 1. Sign Up
**POST** `/api/auth/signup`

Register a new user (teacher or student).

**Request Body:**
```json
{
  "email": "user@example.com",
  "password": "SecurePass123",
  "name": "John Doe",
  "userType": "teacher",
  "department": "Computer Science",
  "inviteToken": "optional-for-students"
}
```

**Parameters:**
- `email` (string, required): Valid email address
- `password` (string, required): Min 8 chars, uppercase, lowercase, number
- `name` (string, required): Full name
- `userType` (string, required): "teacher" or "student"
- `department` (string, optional): Department name
- `inviteToken` (string, optional): For invited students

**Response (201):**
```json
{
  "message": "User created successfully",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "id": "64f3a5b9c2d1e4f5g6h7i8j9",
    "email": "user@example.com",
    "name": "John Doe",
    "userType": "teacher"
  }
}
```

**Errors:**
- `400`: Missing required fields
- `400`: User already exists

---

### 2. Sign In
**POST** `/api/auth/signin`

Authenticate an existing user.

**Request Body:**
```json
{
  "email": "user@example.com",
  "password": "SecurePass123"
}
```

**Parameters:**
- `email` (string, required): Registered email
- `password` (string, required): Account password

**Response (200):**
```json
{
  "message": "Signed in successfully",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "id": "64f3a5b9c2d1e4f5g6h7i8j9",
    "email": "user@example.com",
    "name": "John Doe",
    "userType": "teacher"
  }
}
```

**Errors:**
- `400`: Missing email or password
- `401`: Invalid credentials

---

### 3. Sign Out
**POST** `/api/auth/signout`

Sign out current user. Frontend removes token from localStorage.

**Headers:**
```
Authorization: Bearer <jwt_token>
```

**Response (200):**
```json
{
  "message": "Signed out successfully"
}
```

---

### 4. Get Current User
**GET** `/api/auth/me`

Get authenticated user's profile.

**Headers:**
```
Authorization: Bearer <jwt_token>
```

**Response (200):**
```json
{
  "user": {
    "id": "64f3a5b9c2d1e4f5g6h7i8j9",
    "email": "user@example.com",
    "name": "John Doe",
    "userType": "teacher",
    "emailVerified": true
  }
}
```

**Errors:**
- `401`: Invalid or missing token
- `404`: User not found

---

## User Endpoints

### 5. Get Teacher Profile
**GET** `/api/users/teacher/profile`

Get authenticated teacher's profile.

**Headers:**
```
Authorization: Bearer <jwt_token>
```

**Response (200):**
```json
{
  "_id": "64f3a5b9c2d1e4f5g6h7i8j9",
  "userId": "64f3a5b9c2d1e4f5g6h7i8ja",
  "email": "teacher@example.com",
  "name": "John Doe",
  "department": "Computer Science",
  "createdAt": "2025-11-11T10:00:00Z",
  "updatedAt": "2025-11-11T10:00:00Z"
}
```

**Errors:**
- `401`: Unauthorized
- `403`: User is not a teacher
- `404`: Teacher profile not found

---

### 6. Get Teacher's Students
**GET** `/api/users/teacher/students`

Get all students taught by authenticated teacher.

**Headers:**
```
Authorization: Bearer <jwt_token>
```

**Response (200):**
```json
[
  {
    "_id": "64f3a5b9c2d1e4f5g6h7i8k9",
    "userId": "64f3a5b9c2d1e4f5g6h7i8kb",
    "teacherId": "64f3a5b9c2d1e4f5g6h7i8ja",
    "name": "Jane Doe",
    "email": "student@example.com",
    "department": "Computer Science",
    "class": "CS101",
    "createdAt": "2025-11-11T10:00:00Z"
  }
]
```

---

### 7. Invite Student
**POST** `/api/users/teacher/invite-student`

Send invitation email to a student.

**Headers:**
```
Authorization: Bearer <jwt_token>
Content-Type: application/json
```

**Request Body:**
```json
{
  "email": "student@example.com",
  "name": "Jane Doe",
  "department": "Computer Science",
  "class": "CS101"
}
```

**Parameters:**
- `email` (string, required): Student's email
- `name` (string, required): Student's full name
- `department` (string, optional): Department name
- `class` (string, optional): Class code

**Response (201):**
```json
{
  "message": "Invitation sent successfully",
  "inviteToken": "abc123def456ghi789jkl"
}
```

**Errors:**
- `400`: Missing required fields
- `401`: Unauthorized
- `403`: User is not a teacher
- `404`: Teacher profile not found

---

### 8. Get Student Profile
**GET** `/api/users/student/profile`

Get authenticated student's profile with teacher info.

**Headers:**
```
Authorization: Bearer <jwt_token>
```

**Response (200):**
```json
{
  "_id": "64f3a5b9c2d1e4f5g6h7i8k9",
  "userId": "64f3a5b9c2d1e4f5g6h7i8kb",
  "teacherId": {
    "_id": "64f3a5b9c2d1e4f5g6h7i8ja",
    "name": "John Doe",
    "email": "teacher@example.com",
    "department": "Computer Science"
  },
  "name": "Jane Doe",
  "email": "student@example.com",
  "department": "Computer Science",
  "class": "CS101",
  "createdAt": "2025-11-11T10:00:00Z"
}
```

**Errors:**
- `401`: Unauthorized
- `403`: User is not a student
- `404`: Student profile not found

---

## Attendance Endpoints

### 9. Generate Attendance Code
**POST** `/api/attendance/generate-code`

Generate a 2-minute expiring attendance code.

**Headers:**
```
Authorization: Bearer <jwt_token>
```

**Response (201):**
```json
{
  "code": "ABC123",
  "expiresAt": "2025-11-11T10:02:00Z",
  "expiresIn": 120
}
```

**Errors:**
- `401`: Unauthorized
- `403`: User is not a teacher
- `404`: Teacher profile not found

---

### 10. Submit Attendance
**POST** `/api/attendance/submit`

Student submits attendance code to mark present.

**Headers:**
```
Authorization: Bearer <jwt_token>
Content-Type: application/json
```

**Request Body:**
```json
{
  "code": "ABC123"
}
```

**Parameters:**
- `code` (string, required): 6-character attendance code

**Response (201):**
```json
{
  "success": true,
  "message": "Attendance marked successfully",
  "record": {
    "_id": "64f3a5b9c2d1e4f5g6h7i8m9",
    "studentId": "64f3a5b9c2d1e4f5g6h7i8k9",
    "codeId": "64f3a5b9c2d1e4f5g6h7i8n9",
    "submittedAt": "2025-11-11T10:01:00Z"
  }
}
```

**Errors:**
- `400`: Code is required
- `400`: Code has expired
- `400`: Already marked attendance today
- `401`: Unauthorized
- `403`: User is not a student
- `404`: Invalid attendance code
- `404`: Student profile not found

---

### 11. Get Attendance History
**GET** `/api/attendance/history`

Get student's attendance records (last 50).

**Headers:**
```
Authorization: Bearer <jwt_token>
```

**Query Parameters:**
- `limit` (number, optional): Default 50

**Response (200):**
```json
[
  {
    "_id": "64f3a5b9c2d1e4f5g6h7i8m9",
    "studentId": "64f3a5b9c2d1e4f5g6h7i8k9",
    "codeId": {
      "_id": "64f3a5b9c2d1e4f5g6h7i8n9",
      "code": "ABC123",
      "class": "CS101",
      "expiresAt": "2025-11-11T10:02:00Z"
    },
    "submittedAt": "2025-11-11T10:01:00Z"
  }
]
```

**Errors:**
- `401`: Unauthorized
- `403`: User is not a student
- `404`: Student profile not found

---

### 12. Get Attendance Statistics
**GET** `/api/attendance/stats`

Get teacher's class attendance statistics (last 7 days).

**Headers:**
```
Authorization: Bearer <jwt_token>
```

**Response (200):**
```json
{
  "totalStudents": 25,
  "stats": [
    {
      "date": "11/11/2025",
      "present": 22,
      "absent": 3,
      "rate": 88
    },
    {
      "date": "11/10/2025",
      "present": 24,
      "absent": 1,
      "rate": 96
    }
  ]
}
```

**Errors:**
- `401`: Unauthorized
- `403`: User is not a teacher
- `404`: Teacher profile not found

---

## Health Check

### 13. API Health
**GET** `/api/health`

Check if API is running.

**Response (200):**
```json
{
  "status": "OK",
  "timestamp": "2025-11-11T10:00:00.000Z"
}
```

---

## Error Responses

All error responses follow this format:

```json
{
  "error": "Error message describing what went wrong"
}
```

### Common HTTP Status Codes

| Code | Meaning | Example |
|------|---------|---------|
| 200 | OK | Successful GET, POST |
| 201 | Created | New resource created |
| 400 | Bad Request | Missing/invalid parameters |
| 401 | Unauthorized | Missing/invalid token |
| 403 | Forbidden | Insufficient permissions |
| 404 | Not Found | Resource doesn't exist |
| 500 | Server Error | Unexpected backend error |

---

## Rate Limiting

Currently no rate limiting implemented. Add for production:

```typescript
const rateLimit = require("express-rate-limit");

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100 // limit each IP to 100 requests per windowMs
});

app.use("/api/", limiter);
```

---

## Pagination (Future)

For large datasets, implement pagination:

```json
GET /api/attendance/history?page=1&limit=20

{
  "data": [...],
  "pagination": {
    "current_page": 1,
    "total_pages": 5,
    "total_records": 100
  }
}
```

---

## Request/Response Examples

### Example 1: Complete Sign Up Flow

**Request:**
```bash
curl -X POST http://localhost:5000/api/auth/signup \
  -H "Content-Type: application/json" \
  -d '{
    "email": "teacher@example.com",
    "password": "MyPassword123",
    "name": "John Doe",
    "userType": "teacher",
    "department": "Computer Science"
  }'
```

**Response:**
```json
{
  "message": "User created successfully",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "id": "64f3a5b9c2d1e4f5g6h7i8j9",
    "email": "teacher@example.com",
    "name": "John Doe",
    "userType": "teacher"
  }
}
```

---

### Example 2: Generate Attendance Code

**Request:**
```bash
curl -X POST http://localhost:5000/api/attendance/generate-code \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." \
  -H "Content-Type: application/json"
```

**Response:**
```json
{
  "code": "XYZ789",
  "expiresAt": "2025-11-11T10:02:30Z",
  "expiresIn": 120
}
```

---

### Example 3: Submit Attendance

**Request:**
```bash
curl -X POST http://localhost:5000/api/attendance/submit \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." \
  -H "Content-Type: application/json" \
  -d '{"code": "XYZ789"}'
```

**Response:**
```json
{
  "success": true,
  "message": "Attendance marked successfully",
  "record": {
    "_id": "64f3a5b9c2d1e4f5g6h7i8m9",
    "studentId": "64f3a5b9c2d1e4f5g6h7i8k9",
    "codeId": "64f3a5b9c2d1e4f5g6h7i8n9",
    "submittedAt": "2025-11-11T10:01:30Z"
  }
}
```

---

## Tips for API Integration

### Frontend Example (TypeScript)

```typescript
const API_URL = 'http://localhost:5000/api';

async function signIn(email: string, password: string) {
  const response = await fetch(`${API_URL}/auth/signin`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ email, password })
  });
  
  const data = await response.json();
  
  if (!response.ok) throw new Error(data.error);
  
  localStorage.setItem('authToken', data.token);
  return data.user;
}

async function fetchAPI(endpoint: string, options = {}) {
  const token = localStorage.getItem('authToken');
  const response = await fetch(`${API_URL}${endpoint}`, {
    ...options,
    headers: {
      'Content-Type': 'application/json',
      'Authorization': `Bearer ${token}`,
      ...options.headers
    }
  });
  
  const data = await response.json();
  if (!response.ok) throw new Error(data.error);
  return data;
}
```

---

## Testing with cURL

```bash
# Get health
curl http://localhost:5000/api/health

# Sign up
curl -X POST http://localhost:5000/api/auth/signup \
  -H "Content-Type: application/json" \
  -d '{"email":"test@test.com","password":"Pass123","name":"Test","userType":"student"}'

# Get current user
curl http://localhost:5000/api/auth/me \
  -H "Authorization: Bearer YOUR_TOKEN"
```

---

## API Versioning

For future versions, add to endpoints:
```
/api/v2/auth/signin
/api/v2/users/teacher/profile
```

Keep v1 endpoints active for backward compatibility.

---

**Last Updated:** November 11, 2025
**API Version:** 1.0
**Status:** Production Ready
