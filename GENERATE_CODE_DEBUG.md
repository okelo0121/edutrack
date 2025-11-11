# Generate Code Debug Guide

## Latest Changes Made
1. Added detailed logging to `TeacherDashboard.tsx` component
2. Added logging to button click handler
3. All changes auto-reloaded via Vite HMR

## Test Procedure

### Step 1: Open Browser & Developer Console
1. Go to http://localhost:8080
2. **Press F12** to open Developer Tools
3. Click **Console** tab
4. Look for any errors or logs already there

### Step 2: Navigate to Generate Code Page
1. If logged in as teacher, click **"Generate Code"** in the left sidebar
2. The page should change and show the "Current Session" card
3. **In console, you should see**:
   ```
   [TeacherDashboard] Rendered with activeView: generate-code
   [TeacherDashboard] State - currentCode: null teacherData: true/false
   ```

### Step 3: Click "Generate New Code" Button
1. Look at the button - does it say "Generate New Code" or "Session Active"?
   - If it says "Session Active", the button is disabled (currentCode is set)
   - If it says "Generate New Code", the button should be clickable
2. Click the button
3. **In console, you should immediately see**:
   ```
   [Button] Clicked! Current disabled state: false currentCode value: null
   [generateCode] Button clicked!
   [generateCode] Teacher data: { name: '...', email: '...', ... }
   [generateCode] currentCode state: null
   [generateCode] Calling endpoint: http://localhost:3000/api/attendance/generate-code
   [generateCode] Response status: 201
   [generateCode] Success: { code: 'XXXXXX', expiresAt: '...', expiresIn: 120 }
   ```

### Step 4: What to Report If It Fails

**If you see NO console logs after clicking:**
- The button click isn't firing
- Check if button is disabled (grayed out)
- Try reloading the page with Ctrl+R or Ctrl+Shift+R
- Check if "Generate Code" view is actually active (sidebar highlight)

**If you see `[Button] Clicked!` but nothing after:**
- `generateCode` function isn't being called
- Check if teacherData is null/undefined in the log
- That means teacher profile fetch failed at startup

**If you see request error (Response status not 201):**
- Backend is rejecting the request
- Check backend terminal logs for `[generateCode]` messages
- Check Network tab to see full response body

## Quick Checklist
- [ ] Both servers running (backend on 3000, frontend on 8080)
- [ ] Logged in as a teacher user
- [ ] On the "Generate Code" page
- [ ] Browser console open and ready
- [ ] Button is clickable (not disabled/grayed)

