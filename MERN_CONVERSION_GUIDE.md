# Present Smart - MERN Stack Conversion Guide

## Overview

This project has been successfully converted from a **Supabase-based stack** to a **full MERN stack** (MongoDB, Express, React, Node.js) while maintaining all frontend functionality and adding a robust backend.

### Key Changes
- ✅ Removed all Supabase dependencies
- ✅ Added Express.js backend with TypeScript
- ✅ MongoDB with Mongoose ORM
- ✅ JWT-based authentication
- ✅ Resend email integration maintained
- ✅ Frontend components updated to use REST API

---

## Project Structure

```
present-smart-code/
├── backend/                          # Express.js backend
│   ├── src/
│   │   ├── server.ts               # Main Express app
│   │   ├── models/                 # Mongoose schemas
│   │   │   ├── User.ts
│   │   │   ├── Teacher.ts
│   │   │   ├── Student.ts
│   │   │   ├── StudentInvite.ts
│   │   │   ├── AttendanceCode.ts
│   │   │   └── AttendanceRecord.ts
│   │   ├── controllers/            # Business logic
│   │   │   ├── authController.ts
│   │   │   ├── userController.ts
│   │   │   └── attendanceController.ts
│   │   ├── routes/                 # API routes
│   │   │   ├── authRoutes.ts
│   │   │   ├── userRoutes.ts
│   │   │   └── attendanceRoutes.ts
│   │   ├── middleware/
│   │   │   └── auth.ts
│   │   ├── utils/
│   │   │   ├── jwt.ts
│   │   │   ├── password.ts
│   │   │   └── email.ts
│   │   └── config/
│   ├── package.json
│   ├── tsconfig.json
│   ├── .env.example
│   └── .gitignore
├── src/                              # React frontend
│   ├── components/
│   │   ├── TeacherDashboard.tsx    # Updated with API calls
│   │   ├── StudentInterface.tsx    # Updated with API calls
│   │   └── InviteStudentForm.tsx   # Updated with API calls
│   ├── hooks/
│   │   └── useAuth.tsx             # Replaced Supabase with REST API
│   └── ...
├── .env.example
└── README.md
```

---

## Quick Start

### Prerequisites
- Node.js 16+ and npm/yarn
- MongoDB (local or cloud - MongoDB Atlas)
- Resend account with API key

### Step 1: Clone & Setup Environment

```bash
# Navigate to project root
cd present-smart-code

# Setup backend environment
cp backend/.env.example backend/.env

# Setup frontend environment
cp .env.example .env
```

### Step 2: Configure Environment Variables

#### Backend (.env)
```env
PORT=5000
NODE_ENV=development
MONGODB_URI=mongodb://localhost:27017/present-smart
# For MongoDB Atlas: MONGODB_URI=mongodb+srv://username:password@cluster.mongodb.net/present-smart
JWT_SECRET=your-super-secret-jwt-key-change-this-in-production-min-32-chars
FRONTEND_URL=http://localhost:5173
RESEND_API_KEY=re_your_resend_api_key_here
RESEND_FROM_EMAIL=noreply@yourdomain.com
```

#### Frontend (.env)
```env
VITE_API_URL=http://localhost:5000/api
```

### Step 3: Install Dependencies

```bash
# Backend
cd backend
npm install
cd ..

# Frontend
npm install
```

### Step 4: Start MongoDB

```bash
# Using MongoDB locally
mongod

# Or use MongoDB Atlas (configure MONGODB_URI in .env)
```

### Step 5: Run Development Servers

**Terminal 1 - Backend:**
```bash
cd backend
npm run dev
# Server runs on http://localhost:5000
```

**Terminal 2 - Frontend:**
```bash
npm run dev
# Frontend runs on http://localhost:5173
```

---

## API Endpoints

### Authentication Routes

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/auth/signup` | Register new user |
| POST | `/api/auth/signin` | Login user |
| POST | `/api/auth/signout` | Logout user |
| GET | `/api/auth/me` | Get current user |

**Signup Request:**
```json
{
  "email": "user@example.com",
  "password": "SecurePass123",
  "name": "John Doe",
  "userType": "teacher|student",
  "department": "Computer Science",
  "inviteToken": "optional-for-students"
}
```

### User Routes

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/users/teacher/profile` | Get teacher profile |
| GET | `/api/users/teacher/students` | Get teacher's students |
| POST | `/api/users/teacher/invite-student` | Invite student |
| GET | `/api/users/student/profile` | Get student profile |

**Invite Student Request:**
```json
{
  "email": "student@example.com",
  "name": "Jane Doe",
  "department": "Computer Science",
  "class": "CS101"
}
```

### Attendance Routes

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/attendance/generate-code` | Generate attendance code (teacher) |
| POST | `/api/attendance/submit` | Submit attendance (student) |
| GET | `/api/attendance/history` | Get attendance history (student) |
| GET | `/api/attendance/stats` | Get attendance stats (teacher) |

**Submit Attendance Request:**
```json
{
  "code": "ABC123"
}
```

---

## Authentication

All protected routes require the `Authorization` header:

```
Authorization: Bearer <jwt_token>
```

### JWT Token Structure
```json
{
  "userId": "user_id",
  "email": "user@example.com",
  "userType": "teacher|student",
  "iat": 1234567890,
  "exp": 1234654290
}
```

---

## Database Schema

### User Collection
```typescript
{
  _id: ObjectId
  email: string (unique)
  password: string (hashed)
  name: string
  userType: 'teacher' | 'student'
  emailVerified: boolean
  createdAt: Date
  updatedAt: Date
}
```

### Teacher Collection
```typescript
{
  _id: ObjectId
  userId: ObjectId (ref: User)
  email: string (unique)
  name: string
  department: string
  createdAt: Date
  updatedAt: Date
}
```

### Student Collection
```typescript
{
  _id: ObjectId
  userId: ObjectId (ref: User, nullable)
  teacherId: ObjectId (ref: Teacher, nullable)
  name: string
  email: string
  department: string
  class: string
  createdAt: Date
  updatedAt: Date
}
```

### AttendanceCode Collection
```typescript
{
  _id: ObjectId
  code: string (unique, uppercase)
  teacherId: ObjectId (ref: Teacher)
  class: string
  expiresAt: Date
  createdAt: Date (TTL index: expires after 1 hour)
}
```

### AttendanceRecord Collection
```typescript
{
  _id: ObjectId
  studentId: ObjectId (ref: Student)
  codeId: ObjectId (ref: AttendanceCode)
  submittedAt: Date
}
```

### StudentInvite Collection
```typescript
{
  _id: ObjectId
  email: string
  token: string (unique)
  expiresAt: Date (7 days)
  used: boolean
  createdAt: Date
  createdBy: ObjectId (ref: Teacher)
}
```

---

## Deployment

### Backend - Deploy to Render/Railway

#### Render.com
1. Push code to GitHub
2. Create new Web Service on Render
3. Connect GitHub repository
4. Set environment variables:
   - `MONGODB_URI`: MongoDB Atlas connection string
   - `JWT_SECRET`: Strong random secret
   - `FRONTEND_URL`: Your Vercel frontend URL
   - `RESEND_API_KEY`: Resend API key
   - `RESEND_FROM_EMAIL`: Email sender
5. Deploy

#### Railway.app
1. Push code to GitHub
2. Create new project
3. Add MongoDB plugin (or use MongoDB Atlas)
4. Set environment variables
5. Deploy

### Frontend - Deploy to Vercel

```bash
npm run build
# Push to GitHub

# In Vercel:
# 1. Import project from GitHub
# 2. Set environment variables:
#    - VITE_API_URL: Your backend API URL
# 3. Deploy
```

### Environment Variables for Production

**Backend:**
```env
PORT=3000
NODE_ENV=production
MONGODB_URI=mongodb+srv://user:pass@cluster.mongodb.net/present-smart
JWT_SECRET=your-production-secret-key-min-32-chars
FRONTEND_URL=https://your-frontend.vercel.app
RESEND_API_KEY=re_production_key
RESEND_FROM_EMAIL=noreply@yourdomain.com
```

**Frontend:**
```env
VITE_API_URL=https://your-backend.render.com/api
```

---

## Migration from Supabase

### What Changed

| Aspect | Before | After |
|--------|--------|-------|
| Auth | Supabase Auth | JWT (backend) |
| Database | Supabase PostgreSQL | MongoDB |
| Real-time | Supabase Realtime | Polling (2s interval) |
| Email | Supabase Triggers + Resend | Resend (backend) |
| API Calls | `supabase.from().select()` | `fetch()` with JWT |

### Frontend Updates

All Supabase calls have been replaced with REST API calls. Example:

**Before (Supabase):**
```typescript
const { data, error } = await supabase
  .from('teachers')
  .select('*')
  .eq('user_id', user.id)
  .single();
```

**After (REST API):**
```typescript
const response = await fetch(`${API_URL}/users/teacher/profile`, {
  headers: { 'Authorization': `Bearer ${token}` }
});
const data = await response.json();
```

---

## Production Checklist

- [ ] Update `JWT_SECRET` to a strong, random value
- [ ] Configure MongoDB Atlas with production database
- [ ] Set up Resend email domain verification
- [ ] Update `FRONTEND_URL` and `RESEND_FROM_EMAIL`
- [ ] Enable HTTPS on backend
- [ ] Set up database backups
- [ ] Configure CORS for production domain
- [ ] Set `NODE_ENV=production`
- [ ] Use environment-specific `.env` files
- [ ] Test all features in production
- [ ] Set up monitoring/logging (e.g., Sentry)
- [ ] Document API endpoints
- [ ] Create database indexes for performance

---

## Troubleshooting

### MongoDB Connection Error
```
Error: connect ECONNREFUSED 127.0.0.1:27017
```
**Solution:** Make sure MongoDB is running or update `MONGODB_URI` in `.env`

### JWT Token Expired
**Solution:** Token expires in 7 days. Frontend will require re-login.

### CORS Error
**Solution:** Check `FRONTEND_URL` in backend `.env` matches your frontend URL

### Email Not Sending
**Solution:** Verify `RESEND_API_KEY` and `RESEND_FROM_EMAIL` in `.env`

### Attendance Code Not Expiring
**Solution:** Codes expire after 2 minutes. Generate a new code if needed.

---

## Scripts

### Backend

```bash
# Development
npm run dev

# Build TypeScript
npm run build

# Production
npm start

# Linting
npm run lint
```

### Frontend

```bash
# Development
npm run dev

# Build
npm run build

# Preview build
npm run preview

# Linting
npm run lint
```

---

## Support & Documentation

For more information:
- Backend API: `http://localhost:5000/api/health`
- Frontend: `http://localhost:5173`
- MongoDB Docs: https://docs.mongodb.com
- Express Docs: https://expressjs.com
- React Docs: https://react.dev
- Resend Docs: https://resend.com/docs

---

## License

MIT

---

## Summary

This MERN stack conversion provides:

✅ **Scalable Backend** - Express.js with TypeScript
✅ **Flexible Database** - MongoDB with Mongoose
✅ **Secure Auth** - JWT tokens with bcrypt hashing
✅ **Email Integration** - Resend for invitations
✅ **Type Safety** - Full TypeScript support
✅ **REST API** - Clean, documented endpoints
✅ **Deployment Ready** - Works on Render, Railway, Vercel
✅ **PLP Submission Ready** - Professional structure and documentation

All functionality from the Supabase version is maintained, with improved performance and scalability!
