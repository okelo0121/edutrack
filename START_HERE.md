# 🎯 Complete Backend Setup - Step by Step

## 📋 Quick Status

| Component | Status | Issue | Action |
|-----------|--------|-------|--------|
| **Dependencies** | ✅ Installed | None | Ready |
| **TypeScript** | ✅ Configured | Fixed | Ready |
| **Build System** | ✅ Configured | Fixed | Ready |
| **Code** | ✅ Compiled | Fixed | Ready |
| **MongoDB** | ⏳ Needed | Not started | **👈 DO THIS FIRST** |

---

## 🔴 REQUIRED: Install & Start MongoDB

### Windows Installation

#### Step 1: Download MongoDB
1. Go to: https://www.mongodb.com/try/download/community
2. Select Windows
3. Download the .msi installer
4. Run the installer
5. Choose default options (accept all)

#### Step 2: Verify Installation
Open PowerShell and run:
```bash
mongosh
```

You should see MongoDB shell connection. Type `exit` to close.

#### Step 3: Confirm MongoDB is Running
MongoDB typically auto-starts after installation. You can verify:
- It should be running as a Windows service
- Or start it manually: Search "Services" → Find "MongoDB" → Start

### Alternative: MongoDB Atlas (Cloud)

If you don't want to install locally:

1. Go to: https://www.mongodb.com/cloud/atlas
2. Create free account
3. Create a cluster
4. Get connection string
5. Update `backend/.env`:
   ```
   MONGODB_URI=mongodb+srv://username:password@cluster.mongodb.net/present-smart
   ```

---

## 🚀 Start Backend Server

### Option A: With Local MongoDB

**Terminal 1: Start MongoDB**
```bash
mongod
```
Keep this running.

**Terminal 2: Start Backend**
```bash
cd backend
npm run dev
```

Expected output:
```
[nodemon] 3.1.10
[nodemon] watching path(s): *.*
[nodemon] starting `ts-node src/server.ts`
✓ Connected to MongoDB
✓ Server running on http://localhost:5000
✓ Frontend URL: http://localhost:5173
```

### Option B: With MongoDB Atlas

Just run:
```bash
cd backend
npm run dev
```

Backend will connect to your cloud MongoDB.

---

## ✅ Verify Backend is Working

Open a **new Terminal** (keep backend running) and test:

```bash
# Health check
curl http://localhost:5000/api/health

# Should return:
# {"status":"OK","timestamp":"2025-11-11T..."}
```

---

## 🎨 Start Frontend

### Step 1: Create Frontend .env

Create file `.env` in project root:
```
VITE_API_URL=http://localhost:5000
```

### Step 2: Start Frontend Server

**Terminal 3**:
```bash
npm run dev
```

Expected output:
```
  VITE v5.4.19  ready in 2384 ms

  ➜  Local:   http://localhost:5173/
  ➜  Network: http://192.168.1.xxx:5173/
```

---

## 🌐 Access the Application

1. Open browser
2. Go to: http://localhost:5173
3. You should see the login page

---

## 🧪 Test Features

### 1. Sign Up as Teacher
- Click "Sign Up"
- Select "Teacher"
- Enter email and password
- Click "Sign Up"

### 2. Create Teacher Profile
- Enter name, department, class
- Submit

### 3. Generate Attendance Code
- Click "Generate Code"
- Displays 6-character code (expires in 2 minutes)

### 4. Sign Up as Student (Invited)
- Sign out or new browser
- Paste the invitation link
- Or manual signup with teacher code

### 5. Submit Attendance
- Enter code as student
- Click "Submit"
- Code is verified and attendance recorded

---

## 📊 Database

### View Data with MongoDB Compass

1. Download: https://www.mongodb.com/products/compass
2. Install it
3. Open Compass
4. Connect to: `mongodb://localhost:27017`
5. Browse collections:
   - **Users** - Login credentials
   - **Teachers** - Teacher profiles
   - **Students** - Student profiles
   - **AttendanceCodes** - Active codes (auto-expire)
   - **AttendanceRecords** - Marked attendance
   - **StudentInvites** - Pending invitations (7-day expiry)

---

## 🛠️ Troubleshooting

### MongoDB Connection Error

```
Error: connect ECONNREFUSED 127.0.0.1:27017
```

**Solution:**
1. Ensure MongoDB is installed
2. Run `mongod` in a terminal
3. Restart backend: `npm run dev`

---

### Port 5000 Already in Use

**Solution 1:** Kill the process
```bash
netstat -ano | findstr :5000
taskkill /PID <PID> /F
```

**Solution 2:** Use different port
Edit `backend/.env`:
```
PORT=5001
```

---

### Frontend Can't Connect to Backend

```
Error: Failed to connect to api
```

**Solution:**
1. Check `VITE_API_URL=http://localhost:5000` in `.env`
2. Ensure backend is running: `npm run dev` in `backend/` folder
3. Test: `curl http://localhost:5000/api/health`

---

### TypeScript Errors

```
Unable to compile TypeScript
```

**Solution:**
```bash
cd backend
rm -r node_modules
npm install
npm run dev
```

---

## 📁 Current Terminal Setup

```
Terminal 1: MongoDB
$ mongod

Terminal 2: Backend
$ cd backend
$ npm run dev

Terminal 3: Frontend
$ npm run dev
```

All three should be running simultaneously.

---

## 🔑 Environment Variables

### backend/.env
```
# Server
PORT=5000
NODE_ENV=development

# Database
MONGODB_URI=mongodb://localhost:27017/present-smart

# Auth
JWT_SECRET=your-secret-key-change-in-production

# CORS
FRONTEND_URL=http://localhost:5173

# Email (optional in dev)
RESEND_API_KEY=
RESEND_FROM_EMAIL=onboarding@resend.dev
```

### .env (root, for frontend)
```
VITE_API_URL=http://localhost:5000
```

---

## 📝 API Endpoints

Once backend is running, all 13 endpoints available:

### Auth
- `POST /api/auth/signup` - Register
- `POST /api/auth/signin` - Login
- `POST /api/auth/signout` - Logout
- `GET /api/auth/me` - Current user

### Teacher
- `GET /api/users/teacher/profile` - Get profile
- `GET /api/users/teacher/students` - List students
- `POST /api/users/teacher/invite-student` - Invite

### Student
- `GET /api/users/student/profile` - Get profile

### Attendance
- `POST /api/attendance/generate-code` - Create code
- `POST /api/attendance/submit` - Mark present
- `GET /api/attendance/history` - View history
- `GET /api/attendance/stats` - Statistics

See `API_DOCUMENTATION.md` for full details.

---

## 📊 Success Indicators

Once fully running, you should see:

✅ Backend terminal:
```
✓ Connected to MongoDB
✓ Server running on http://localhost:5000
```

✅ Frontend terminal:
```
  ➜  Local:   http://localhost:5173/
```

✅ Browser at localhost:5173:
- Login page loads
- No console errors
- Can sign up
- Can login

---

## 🚀 Next: Deployment

Once working locally, see:
- `MERN_CONVERSION_GUIDE.md` → Deployment section
- Deploy backend to Render/Railway
- Deploy frontend to Vercel
- Update environment variables

---

## 📚 Documentation

| File | Purpose |
|------|---------|
| **BACKEND_FIXES_SUMMARY.md** | Issues fixed and solutions |
| **BACKEND_SETUP.md** | Troubleshooting guide |
| **QUICK_START.md** | 5-minute startup guide |
| **MERN_CONVERSION_GUIDE.md** | Complete reference |
| **API_DOCUMENTATION.md** | Endpoint documentation |

---

## ✨ You're Almost There!

**Remaining Steps:**
1. Install MongoDB (if not done)
2. Ensure `mongod` is running
3. Run `npm run dev` in `/backend`
4. Run `npm run dev` in project root
5. Visit http://localhost:5173

**Status: 🟢 READY TO START MONGODB**

Good luck! 🎉
