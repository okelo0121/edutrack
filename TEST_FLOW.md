# Quick Test Flow for Generate Code Issue

## Servers Status
- ✅ Backend: http://localhost:3000 (running)
- ✅ Frontend: http://localhost:8080 (running)

## Test Steps

### 1. Teacher Signs Up / Signs In
1. Open http://localhost:8080 in browser
2. Click "Get Started" or go to /auth
3. Select "Teacher" user type
4. Fill in:
   - Name: Test Teacher
   - Email: teacher@test.com
   - Password: TestPass123!
   - Department: Computer Science
5. Click Sign Up
6. Verify sign-in is successful (should see dashboard)

### 2. Navigate to Generate Code
1. In Teacher Dashboard, click "Generate Code" button (left sidebar or main view)
2. Should see "Generate Attendance Code" page
3. Click "Generate New Code" button

### 3. Monitor Logs
- **Browser Console** (Ctrl+Shift+I): Look for logs starting with `[generateCode]`
- **Backend Terminal**: Look for logs starting with `[generateCode]`

## Expected Behavior
- When clicking "Generate New Code":
  - Frontend should log: `[generateCode] Calling endpoint: http://localhost:3000/api/attendance/generate-code`
  - Backend should log: `[generateCode] Request from user: <userId>`
  - Backend should log: `[generateCode] Teacher lookup result: Found: <teacherId>` or `Not found`
  - If found: `[generateCode] Code created successfully: XXXXXX`
  - A code should appear on screen with countdown timer

## Debugging If It Fails
1. Check browser Network tab → filter for "generate-code" → look at response
2. Check backend logs for error details
3. In MongoDB, check:
   - `users` collection for teacher user record
   - `teachers` collection for teacher profile with matching userId

## Files Modified for Logging
- `src/components/TeacherDashboard.tsx` - Added detailed console logs
- `backend/src/controllers/attendanceController.ts` - Added detailed console logs
- `backend/src/server.ts` - Fixed PORT type (was string, now number)

