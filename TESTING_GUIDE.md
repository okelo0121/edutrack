# 🚀 TESTING GUIDE - Present Smart System

## ✅ System Status

### Servers Running
- ✅ **Backend API**: http://localhost:3000
  - MongoDB Connected
  - Resend Email Ready
  - JWT Authentication Active
  
- ✅ **Frontend App**: http://localhost:8080
  - React app loaded
  - Vite dev server ready

## 🧪 How to Test

### Option 1: Use Regular Browser (RECOMMENDED)
```
This is the best option for full functionality
1. Open http://localhost:8080 in Chrome, Firefox, or Edge
2. You should see the Present Smart landing page
3. Click "Get Started" or navigate to /auth
4. Select "Sign Up as Teacher" or "Sign Up as Student"
```

### Option 2: Use VS Code Simple Browser
```
Already opened in the editor
- May have limitations with some browser features
- Try hard refresh if page doesn't load properly (Ctrl+Shift+R)
```

## 📝 Test Scenarios

### Scenario 1: Teacher Signup
```
1. Go to http://localhost:8080/auth
2. Select "Sign Up as Teacher"
3. Fill in:
   - Email: teacher@example.com
   - Password: Teacher123!
   - Name: John Teacher
   - Department: Computer Science (optional)
4. Click "Sign Up"
5. Expected: Redirect to dashboard, see "✓ Signed up successfully"
```

### Scenario 2: Student Signup
```
1. Go to http://localhost:8080/auth
2. Select "Sign Up as Student"
3. Fill in:
   - Email: student@example.com
   - Password: Student123!
   - Name: Jane Student
4. Click "Sign Up"
5. Expected: Redirect to student interface, see attendance code entry
```

### Scenario 3: Teacher Invite Student
```
Prerequisites: Logged in as teacher
1. You should see a student list in the dashboard
2. Find "Invite Student" section
3. Enter student email: newstudent@example.com
4. Click "Send Invite"
5. Expected: Success message, email queued (check backend logs)
6. Check backend terminal for email logs
```

### Scenario 4: Generate & Submit Attendance
```
Prerequisites: Teacher and Student accounts created
Teacher:
1. Click "Generate Code" button
2. Copy the 6-digit code that appears
3. Share with students

Student:
1. Paste the code in "Mark Attendance"
2. Click "Submit"
3. Expected: Success message, code added to your history
```

## 🔍 Monitoring

### Backend Logs
Watch the backend terminal for:
```
POST /auth/signup           <- User creating account
POST /auth/signin           <- User logging in
GET /users/teacher/students <- Teacher getting students list
POST /users/teacher/invite-student <- Sending invite
POST /attendance/generate   <- Generating code
POST /attendance/submit     <- Submitting attendance
[ERROR]                     <- Any errors will show here
```

### Browser Console
Open DevTools (F12) and check:
```
Network tab:
- All API calls to http://localhost:3000/api/*
- Status codes (200, 201 for success; 4xx, 5xx for errors)

Console tab:
- Any JavaScript errors
- Auth token confirmation (localStorage)

Application tab:
- localStorage → authToken (should appear after signup)
- localStorage → user (JSON object with user info)
```

## 🐛 Troubleshooting

### "Route not found" Error
```
Possible causes:
1. Backend API path incorrect
   Solution: Check API_URL in useAuth.tsx
   Should be: http://localhost:3000/api

2. Backend didn't respond
   Solution: Check if backend is running
   Terminal should show: ✓ Server running on http://localhost:3000

3. Request path misspelled
   Solution: Check browser console Network tab for actual request URL
```

### CORS Error
```
Error: "Access to XMLHttpRequest from origin blocked by CORS"
Solution: Backend/.env has FRONTEND_URL=http://localhost:8080
Check:
1. Frontend port matches
2. Backend CORS config includes correct origin
3. Clear browser cache (Ctrl+Shift+Delete)
```

### Token Not Saved
```
Error: Login works but page reloads and logs you out
Solution:
1. Check localStorage in DevTools (F12 > Application > localStorage)
2. Should see: authToken and user
3. Check browser allows localStorage (not in private browsing)
```

### Backend Not Accepting Requests
```
Error: GET/POST to /api/auth/signup returns 404
Solution:
1. Verify backend terminal shows: ✓ Server running on http://localhost:3000
2. Try direct test: Invoke-WebRequest -Uri http://localhost:3000/api/health
3. Should return: {"status":"OK","timestamp":"..."}
4. If fails, restart backend: Stop all node processes and npm run dev again
```

## ✅ Success Indicators

After signup, you should see:
```
✅ Response status 201 (Created) in Network tab
✅ authToken in localStorage
✅ user object in localStorage
✅ Redirect to appropriate dashboard (teacher or student)
✅ No errors in browser console
✅ Backend logs show successful database save
```

## 📊 API Response Examples

### Successful Signup (201 Created)
```json
{
  "message": "User created successfully",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "id": "507f1f77bcf86cd799439011",
    "email": "teacher@example.com",
    "name": "John Teacher",
    "userType": "teacher"
  }
}
```

### Failed Signup (400 Bad Request)
```json
{
  "error": "User already exists with this email"
}
```

### Failed Signup (500 Server Error)
```json
{
  "error": "Failed to create user account"
}
```

## 🛠 Quick Commands

### Check Backend Status
```powershell
Invoke-WebRequest -Uri http://localhost:3000/api/health -UseBasicParsing
```

### Check Frontend Status
```powershell
Invoke-WebRequest -Uri http://localhost:8080 -UseBasicParsing
```

### View Backend Logs in Real-time
```
Already running in terminal (should see POST requests as you test)
```

### Restart Everything
```powershell
# Kill all node processes
Stop-Process -Name node -Force -ErrorAction SilentlyContinue

# Start backend
cd backend; npm run dev

# In another terminal, start frontend
cd .; npm run dev
```

## 📱 Testing on Phone/Network

To access from another computer on your network:
```
Replace localhost with your machine IP:
http://192.168.1.140:8080  (from other computers)

But backend needs to be accessible too!
Update backend/.env:
FRONTEND_URL=http://192.168.1.140:8080
```

## 🎯 Next Steps

1. ✅ Both servers running
2. 👉 **Try signing up** with the test form
3. 👉 **Check DevTools** to see API responses
4. 👉 **Check backend logs** to see requests
5. 👉 **Test inviting** a student (if teacher)
6. 👉 **Test attendance** code workflow
7. 📢 **Report any errors** with exact error message

---

**System Ready**: ✅ All servers operational
**Last Updated**: November 11, 2025
**Test Status**: Ready for QA
