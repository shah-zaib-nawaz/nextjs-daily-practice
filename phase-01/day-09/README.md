# Day 9: proxy.ts (Middleware Replacement) - Complete Beginner's Guide

Today you'll learn how to **intercept requests** and **protect routes** using Next.js 16's `proxy.ts` - the new middleware system!

---

## 🎯 What You'll Learn Today

- What middleware/proxy is and why it's powerful
- How to intercept requests before they reach pages
- How to implement authentication with cookies
- How to redirect users based on conditions
- The difference between `middleware.ts` and `proxy.ts`

---

## 📚 Part 1: Understanding Middleware/Proxy (20 minutes)

### What is Middleware?

**Middleware** is code that runs **between** the request and the response.

**Think of it like airport security:**
```
User makes request → Middleware checks → Page loads
     (Passenger)   →   (Security)   →   (Gate)
```

### Visual Flow

```
WITHOUT MIDDLEWARE:
User → /dashboard → Dashboard Page
       (Anyone can access!)

WITH MIDDLEWARE:
User → /dashboard → Middleware checks auth → Dashboard Page
                    ↓
                    Not logged in? → Redirect to /login
```

### What Changed in Next.js?

**Old naming (still works):**
```
middleware.ts  ← Old name
```

**New naming (Next.js 16):**
```
proxy.ts  ← New name (more descriptive!)
```

**Both work!** But `proxy.ts` better describes what it does: it's a **proxy** that sits between requests and responses.

---

## 🔍 Part 2: Understanding Cookies

### What are Cookies?

**Cookies** are small pieces of data stored in the browser.

**Think of them like entry wristbands at an event:**
- You get a wristband when you enter (login)
- Security checks your wristband to let you in (authentication)
- You can remove the wristband to leave (logout)

### Cookie Flow

```
LOGIN:
1. User enters credentials
2. Server validates
3. Server sets cookie: "user-session=true"
4. Browser stores cookie
5. Browser sends cookie with every request

PROTECTED PAGE:
1. User visits /dashboard
2. Browser automatically sends cookie
3. Proxy checks cookie
4. If cookie exists → Allow access
5. If no cookie → Redirect to login
```

---

## 💻 Part 3: Setting Up the Project Structure

### Your Project Structure

```
my-practice-app/
├── proxy.ts                    ← Middleware (NEW!)
├── app/
│   ├── page.tsx                ← Homepage
│   ├── login/
│   │   └── page.tsx            ← Login page
│   └── dashboard/
│       └── page.tsx            ← Protected page
└── ...
```

---

## 🛡️ Part 4: Creating the Proxy (Middleware)

### Step 1: Create proxy.ts

**Create file in ROOT:** `proxy.ts` (same level as `package.json`)

```typescript
import { NextRequest, NextResponse } from 'next/server';

// This function runs BEFORE every request that matches the config
export default function proxy(request: NextRequest) {
  // Log every request (helpful for debugging)
  console.log('🔍 Proxy checking:', request.nextUrl.pathname);

  // Get the auth cookie
  const userSession = request.cookies.get('user-session');
  const isLoggedIn = userSession?.value === 'true';

  // Check if user is trying to access dashboard
  if (request.nextUrl.pathname.startsWith('/dashboard')) {
    console.log('🚪 Dashboard access attempt. Logged in?', isLoggedIn);

    // If not logged in, redirect to login page
    if (!isLoggedIn) {
      console.log('❌ Not logged in. Redirecting to /login');
      const loginUrl = new URL('/login', request.url);
      // Add 'from' parameter to redirect back after login
      loginUrl.searchParams.set('from', request.nextUrl.pathname);
      return NextResponse.redirect(loginUrl);
    }

    console.log('✅ Logged in. Allowing access to dashboard');
  }

  // Check if logged-in user tries to access login page
  if (request.nextUrl.pathname === '/login' && isLoggedIn) {
    console.log('👤 Already logged in. Redirecting to dashboard');
    return NextResponse.redirect(new URL('/dashboard', request.url));
  }

  // Allow request to continue
  return NextResponse.next();
}

// Configure which routes this proxy applies to
export const config = {
  // Match all dashboard routes and login page
  matcher: ['/dashboard/:path*', '/login'],
};
```

**Understanding the Code:**

- **Line 4:** Function runs before matched requests
- **Line 9-10:** Check for authentication cookie
- **Line 13:** Check if accessing dashboard
- **Line 17-22:** Redirect to login if not authenticated
- **Line 27-30:** Redirect to dashboard if already logged in (prevents seeing login page)
- **Line 34:** Allow request to proceed
- **Line 38-41:** Configure which routes to intercept

---

## 🏠 Part 5: Creating the Homepage

### Step 2: Update Homepage

**Update file:** `app/page.tsx`

```typescript
export default function HomePage() {
  return (
    <div className="min-h-screen bg-gradient-to-br from-blue-500 to-purple-600 flex items-center justify-center p-8">
      <div className="max-w-4xl w-full">
        {/* Main Card */}
        <div className="bg-white rounded-2xl shadow-2xl p-12 text-center">
          {/* Icon */}
          <div className="text-8xl mb-6">🔐</div>

          {/* Title */}
          <h1 className="text-5xl font-bold text-gray-800 mb-4">
            Protected Dashboard Demo
          </h1>

          {/* Description */}
          <p className="text-xl text-gray-600 mb-8">
            Learn how proxy.ts (middleware) protects routes and handles authentication
          </p>

          {/* Info Box */}
          <div className="bg-blue-50 border-l-4 border-blue-600 p-6 mb-8 text-left rounded">
            <h3 className="text-lg font-bold text-blue-800 mb-3">
              🎯 What You'll Learn:
            </h3>
            <ul className="space-y-2 text-blue-700">
              <li>✅ How proxy.ts intercepts requests</li>
              <li>✅ How to protect routes with authentication</li>
              <li>✅ How to use cookies for session management</li>
              <li>✅ How to redirect based on auth state</li>
            </ul>
          </div>

          {/* Action Buttons */}
          <div className="grid md:grid-cols-2 gap-4">
            {/* Try Dashboard (will redirect to login) */}
            
              href="/dashboard"
              className="block px-8 py-4 bg-red-500 text-white rounded-lg hover:bg-red-600 transition font-bold text-lg"
            >
              🚫 Try Dashboard (Protected)
            </a>

            {/* Go to Login */}
            
              href="/login"
              className="block px-8 py-4 bg-green-500 text-white rounded-lg hover:bg-green-600 transition font-bold text-lg"
            >
              🔓 Go to Login
            </a>
          </div>

          {/* Instructions */}
          <div className="mt-8 text-left">
            <h3 className="text-xl font-bold text-gray-800 mb-3">
              📋 Try This:
            </h3>
            <ol className="list-decimal list-inside space-y-2 text-gray-700">
              <li>Click "Try Dashboard" - you'll be redirected to login (not logged in)</li>
              <li>Click "Login" button on the login page</li>
              <li>Click "Try Dashboard" again - now you can access it!</li>
              <li>Click "Logout" to clear your session</li>
            </ol>
          </div>
        </div>

        {/* How It Works */}
        <div className="mt-8 bg-white rounded-2xl shadow-2xl p-8">
          <h2 className="text-3xl font-bold text-gray-800 mb-6 text-center">
            🔄 How proxy.ts Works
          </h2>

          <div className="grid md:grid-cols-3 gap-6">
            {/* Step 1 */}
            <div className="text-center">
              <div className="text-5xl mb-3">1️⃣</div>
              <h3 className="font-bold text-gray-800 mb-2">Request Arrives</h3>
              <p className="text-gray-600 text-sm">
                User tries to visit /dashboard
              </p>
            </div>

            {/* Step 2 */}
            <div className="text-center">
              <div className="text-5xl mb-3">2️⃣</div>
              <h3 className="font-bold text-gray-800 mb-2">Proxy Checks</h3>
              <p className="text-gray-600 text-sm">
                proxy.ts checks for auth cookie
              </p>
            </div>

            {/* Step 3 */}
            <div className="text-center">
              <div className="text-5xl mb-3">3️⃣</div>
              <h3 className="font-bold text-gray-800 mb-2">Decision</h3>
              <p className="text-gray-600 text-sm">
                Allow access or redirect to login
              </p>
            </div>
          </div>
        </div>
      </div>
    </div>
  );
}
```

---

## 🔓 Part 6: Creating the Login Page

### Step 3: Create Login Page

**Create file:** `app/login/page.tsx`

```typescript
'use client';

import { useRouter, useSearchParams } from 'next/navigation';
import { useState } from 'react';

export default function LoginPage() {
  const router = useRouter();
  const searchParams = useSearchParams();
  const [isLoading, setIsLoading] = useState(false);

  // Get the page user was trying to access (from redirect)
  const from = searchParams.get('from') || '/dashboard';

  const handleLogin = () => {
    setIsLoading(true);

    // Simulate login delay
    setTimeout(() => {
      // Set authentication cookie
      document.cookie = 'user-session=true; path=/; max-age=86400'; // 24 hours
      
      console.log('✅ Cookie set! Redirecting to:', from);
      
      // Redirect to the page they were trying to access
      router.push(from);
      router.refresh(); // Force refresh to update middleware
    }, 1000);
  };

  return (
    <div className="min-h-screen bg-gradient-to-br from-green-400 to-blue-500 flex items-center justify-center p-8">
      <div className="max-w-md w-full">
        {/* Login Card */}
        <div className="bg-white rounded-2xl shadow-2xl p-8">
          {/* Icon */}
          <div className="text-center text-7xl mb-6">🔐</div>

          {/* Title */}
          <h1 className="text-4xl font-bold text-gray-800 mb-2 text-center">
            Welcome Back!
          </h1>
          <p className="text-gray-600 text-center mb-8">
            Log in to access your dashboard
          </p>

          {/* Info about redirection */}
          {from !== '/dashboard' && (
            <div className="bg-yellow-50 border-l-4 border-yellow-500 p-4 mb-6 rounded">
              <p className="text-yellow-800 text-sm">
                <strong>Note:</strong> You tried to access <code className="bg-yellow-100 px-2 py-1 rounded">{from}</code>
                <br />
                You'll be redirected there after login.
              </p>
            </div>
          )}

          {/* Explanation */}
          <div className="bg-blue-50 border border-blue-200 rounded-lg p-4 mb-6">
            <h3 className="font-bold text-blue-800 mb-2 text-sm">
              🔍 What Happens When You Login:
            </h3>
            <ol className="list-decimal list-inside text-blue-700 text-sm space-y-1">
              <li>Cookie is set: <code className="bg-blue-100 px-1 rounded text-xs">user-session=true</code></li>
              <li>Browser stores this cookie</li>
              <li>proxy.ts reads cookie on next request</li>
              <li>You're allowed to access protected pages</li>
            </ol>
          </div>

          {/* Login Form */}
          <div className="space-y-4 mb-6">
            {/* Fake Username Input */}
            <div>
              <label className="block text-sm font-semibold text-gray-700 mb-2">
                Username (demo - any value works)
              </label>
              <input
                type="text"
                placeholder="Enter any username"
                className="w-full px-4 py-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                defaultValue="demo-user"
              />
            </div>

            {/* Fake Password Input */}
            <div>
              <label className="block text-sm font-semibold text-gray-700 mb-2">
                Password (demo - any value works)
              </label>
              <input
                type="password"
                placeholder="Enter any password"
                className="w-full px-4 py-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                defaultValue="password"
              />
            </div>
          </div>

          {/* Login Button */}
          <button
            onClick={handleLogin}
            disabled={isLoading}
            className="w-full px-8 py-4 bg-blue-600 text-white rounded-lg hover:bg-blue-700 transition font-bold text-lg disabled:bg-gray-400 disabled:cursor-not-allowed"
          >
            {isLoading ? '🔄 Logging in...' : '🚀 Login'}
          </button>

          {/* Note */}
          <p className="text-center text-gray-500 text-sm mt-4">
            This is a demo. No real authentication is performed.
          </p>

          {/* Back to Home */}
          <div className="mt-6 text-center">
            
              href="/"
              className="text-blue-600 hover:underline font-semibold"
            >
              ← Back to Home
            </a>
          </div>
        </div>

        {/* Technical Details */}
        <div className="mt-6 bg-white rounded-lg p-6">
          <h3 className="font-bold text-gray-800 mb-3">
            🔧 Technical Details:
          </h3>
          <div className="space-y-2 text-sm text-gray-700">
            <p>
              <strong>Cookie name:</strong> <code className="bg-gray-100 px-2 py-1 rounded">user-session</code>
            </p>
            <p>
              <strong>Cookie value:</strong> <code className="bg-gray-100 px-2 py-1 rounded">true</code>
            </p>
            <p>
              <strong>Path:</strong> <code className="bg-gray-100 px-2 py-1 rounded">/</code> (all routes)
            </p>
            <p>
              <strong>Max age:</strong> 86400 seconds (24 hours)
            </p>
          </div>
        </div>
      </div>
    </div>
  );
}
```

**Key Features:**
- **Line 3:** Import `useSearchParams` to get redirect URL
- **Line 12:** Get the page user was trying to access
- **Line 19:** Set cookie with 24-hour expiration
- **Line 24:** Redirect to original destination
- **Lines 47-56:** Show which page user will be redirected to

---

## 🏢 Part 7: Creating the Protected Dashboard

### Step 4: Create Dashboard Page

**Create file:** `app/dashboard/page.tsx`

```typescript
'use client';

import { useRouter } from 'next/navigation';

export default function DashboardPage() {
  const router = useRouter();

  const handleLogout = () => {
    // Clear the auth cookie
    document.cookie = 'user-session=; path=/; max-age=0';
    
    console.log('🚪 Logged out! Cookie cleared.');
    
    // Redirect to home
    router.push('/');
    router.refresh(); // Force refresh to update middleware
  };

  return (
    <div className="min-h-screen bg-gradient-to-br from-purple-500 to-pink-500 p-8">
      <div className="max-w-5xl mx-auto">
        {/* Dashboard Header */}
        <div className="bg-white rounded-2xl shadow-2xl p-8 mb-8">
          <div className="flex items-center justify-between mb-6">
            <div>
              <h1 className="text-4xl font-bold text-gray-800 mb-2">
                🎉 Dashboard
              </h1>
              <p className="text-gray-600">
                You successfully accessed a protected page!
              </p>
            </div>
            <button
              onClick={handleLogout}
              className="px-6 py-3 bg-red-500 text-white rounded-lg hover:bg-red-600 transition font-bold"
            >
              🚪 Logout
            </button>
          </div>

          {/* Success Message */}
          <div className="bg-green-50 border-l-4 border-green-600 p-6 rounded">
            <h3 className="text-lg font-bold text-green-800 mb-2">
              ✅ Authentication Successful!
            </h3>
            <p className="text-green-700">
              You're seeing this page because proxy.ts verified your authentication cookie.
            </p>
          </div>
        </div>

        {/* How You Got Here */}
        <div className="bg-white rounded-2xl shadow-2xl p-8 mb-8">
          <h2 className="text-3xl font-bold text-gray-800 mb-6">
            🔐 How You Got Here
          </h2>

          <div className="space-y-4">
            {/* Step 1 */}
            <div className="flex gap-4">
              <div className="flex-shrink-0 w-12 h-12 bg-blue-100 rounded-full flex items-center justify-center text-blue-600 font-bold text-xl">
                1
              </div>
              <div>
                <h3 className="font-bold text-gray-800 mb-1">You Logged In</h3>
                <p className="text-gray-600 text-sm">
                  Clicked "Login" button → Set cookie: <code className="bg-gray-100 px-2 py-1 rounded">user-session=true</code>
                </p>
              </div>
            </div>

            {/* Step 2 */}
            <div className="flex gap-4">
              <div className="flex-shrink-0 w-12 h-12 bg-purple-100 rounded-full flex items-center justify-center text-purple-600 font-bold text-xl">
                2
              </div>
              <div>
                <h3 className="font-bold text-gray-800 mb-1">You Visited /dashboard</h3>
                <p className="text-gray-600 text-sm">
                  Browser automatically sent your cookie with the request
                </p>
              </div>
            </div>

            {/* Step 3 */}
            <div className="flex gap-4">
              <div className="flex-shrink-0 w-12 h-12 bg-green-100 rounded-full flex items-center justify-center text-green-600 font-bold text-xl">
                3
              </div>
              <div>
                <h3 className="font-bold text-gray-800 mb-1">proxy.ts Checked Cookie</h3>
                <p className="text-gray-600 text-sm">
                  Found valid cookie → Allowed access to this page
                </p>
              </div>
            </div>

            {/* Step 4 */}
            <div className="flex gap-4">
              <div className="flex-shrink-0 w-12 h-12 bg-yellow-100 rounded-full flex items-center justify-center text-yellow-600 font-bold text-xl">
                4
              </div>
              <div>
                <h3 className="font-bold text-gray-800 mb-1">Dashboard Loaded</h3>
                <p className="text-gray-600 text-sm">
                  You're now viewing this protected content! 🎉
                </p>
              </div>
            </div>
          </div>
        </div>

        {/* Dashboard Stats */}
        <div className="grid md:grid-cols-3 gap-6 mb-8">
          {/* Stat 1 */}
          <div className="bg-white rounded-lg shadow-xl p-6 text-center">
            <div className="text-4xl mb-2">📊</div>
            <p className="text-3xl font-bold text-gray-800">42</p>
            <p className="text-gray-600">Total Projects</p>
          </div>

          {/* Stat 2 */}
          <div className="bg-white rounded-lg shadow-xl p-6 text-center">
            <div className="text-4xl mb-2">✅</div>
            <p className="text-3xl font-bold text-gray-800">128</p>
            <p className="text-gray-600">Tasks Completed</p>
          </div>

          {/* Stat 3 */}
          <div className="bg-white rounded-lg shadow-xl p-6 text-center">
            <div className="text-4xl mb-2">👥</div>
            <p className="text-3xl font-bold text-gray-800">15</p>
            <p className="text-gray-600">Team Members</p>
          </div>
        </div>

        {/* Test Logout */}
        <div className="bg-white rounded-2xl shadow-2xl p-8">
          <h2 className="text-2xl font-bold text-gray-800 mb-4">
            🧪 Test Logout
          </h2>
          <p className="text-gray-600 mb-4">
            Click the "Logout" button above to clear your session cookie. 
            Then try accessing this page again - you'll be redirected to login!
          </p>

          <div className="bg-red-50 border-l-4 border-red-500 p-4 rounded">
            <p className="text-red-700 text-sm">
              <strong>What happens on logout:</strong>
            </p>
            <ol className="list-decimal list-inside text-red-600 text-sm mt-2 space-y-1">
              <li>Cookie is cleared (max-age=0)</li>
              <li>You're redirected to homepage</li>
              <li>Trying to access /dashboard redirects to /login</li>
            </ol>
          </div>
        </div>

        {/* Code Example */}
        <div className="mt-8 bg-gray-800 rounded-lg p-6 text-white font-mono text-sm overflow-x-auto">
          <p className="text-green-400 mb-2">// proxy.ts code:</p>
          <pre className="text-gray-300">{`export default function proxy(request: NextRequest) {
  const isLoggedIn = request.cookies.get('user-session');
  
  if (request.nextUrl.pathname.startsWith('/dashboard')) {
    if (!isLoggedIn) {
      return NextResponse.redirect(new URL('/login', request.url));
    }
  }
  
  return NextResponse.next();
}`}</pre>
        </div>
      </div>
    </div>
  );
}
```

**Key Features:**
- **Line 8-17:** Logout function that clears cookie
- **Lines 42-48:** Success message explaining authentication
- **Lines 52-116:** Visual explanation of auth flow
- **Lines 120-142:** Dashboard stats (demo data)
- **Lines 145-162:** Logout test instructions

---

## 🧪 Part 8: Testing the Authentication Flow

### Test 1: Access Without Login

1. **Open browser** (use incognito/private mode for clean test)
2. **Visit** `http://localhost:3000/dashboard` directly
3. **Observe:**
   - You're automatically redirected to `/login`
   - URL shows: `/login?from=/dashboard`
   - Console shows: "❌ Not logged in. Redirecting to /login"

**Expected:** ✅ Cannot access dashboard without login

---

### Test 2: Login Process

1. **On login page**, click "🚀 Login" button
2. **Wait 1 second** (simulated delay)
3. **Observe:**
   - Cookie is set (check DevTools → Application → Cookies)
   - Redirected to `/dashboard`
   - Console shows: "✅ Cookie set! Redirecting to: /dashboard"

**Expected:** ✅ Login button sets cookie and redirects

---

### Test 3: Access After Login

1. **You're now on dashboard page**
2. **Open new tab**, visit `http://localhost:3000/dashboard`
3. **Observe:**
   - You can access dashboard directly
   - No redirect to login
   - Console shows: "✅ Logged in. Allowing access to dashboard"

**Expected:** ✅ Can access dashboard after login

---

### Test 4: Logout

1. **On dashboard**, click "🚪 Logout" button
2. **Observe:**
   - Redirected to homepage
   - Cookie cleared (check DevTools)
   - Console shows: "🚪 Logged out! Cookie cleared."
3. **Try accessing** `/dashboard` again
4. **Observe:** Redirected back to login!

**Expected:** ✅ Logout clears session

---

## ✅ Part 9: Success Criteria Verification

### ✅ Cannot Access Dashboard Without Login
1. Clear cookies (Cmd+Shift+Delete or Ctrl+Shift+Delete)
2. Visit `/dashboard`
3. **Expected:** Redirected to `/login?from=/dashboard`
4. **Verified:** ✅

### ✅ Login Button Sets Cookie
1. Click "Login" on login page
2. Open DevTools → Application → Cookies
3. **Expected:** See `user-session=true`
4. **Verified:** ✅

### ✅ Can Access Dashboard After Login
1. After login, visit `/dashboard`
2. **Expected:** Dashboard loads successfully
3. **Verified:** ✅

### ✅ Redirect Works Correctly
1. Try `/dashboard` → Redirects to `/login?from=/dashboard`
2. Login → Redirects back to `/dashboard`
3. **Expected:** Smooth redirect flow
4. **Verified:** ✅

---

## 🌟 Bonus Challenge: Enhanced Logout

We already implemented logout! But let's add a confirmation dialog:

### Step 5: Add Logout Confirmation

**Update:** `app/dashboard/page.tsx`

Replace the `handleLogout` function:

```typescript
const handleLogout = () => {
  // Confirmation dialog
  const confirmed = confirm('Are you sure you want to logout?');
  
  if (!confirmed) {
    return; // User cancelled
  }

  // Clear the auth cookie
  document.cookie = 'user-session=; path=/; max-age=0';
  
  console.log('🚪 Logged out! Cookie cleared.');
  
  // Show success message
  alert('You have been logged out successfully!');
  
  // Redirect to home
  router.push('/');
  router.refresh();
};
```

**Test it:**
- Click "Logout" → See confirmation
- Click "Cancel" → Stays on dashboard
- Click "OK" → Logs out

---

## 📊 Authentication Flow Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                    AUTHENTICATION FLOW                       │
└─────────────────────────────────────────────────────────────┘

1. USER VISITS /dashboard (No Cookie)
   │
   ├─> proxy.ts intercepts request
   │
   ├─> Checks for 'user-session' cookie
   │
   ├─> Cookie not found!
   │
   └─> Redirect to /login?from=/dashboard

2. USER LOGS IN
   │
   ├─> User clicks "Login" button
   │
   ├─> document.cookie = 'user-session=true'
   │
   └─> Redirect to /dashboard

3. USER VISITS /dashboard (With Cookie)
   │
   ├─> proxy.ts intercepts request
   │
   ├─> Checks for 'user-session' cookie
   │
   ├─> Cookie found! ✅
   │
   └─> Allow access to dashboard

4. USER LOGS OUT
   │
   ├─> User clicks "Logout" button
   │
   ├─> document.cookie = 'user-session=; max-age=0'
   │
   └─> Redirect to homepage
```

---

## 🎓 Key Concepts Recap

### 1. **proxy.ts Location**
```
my-practice-app/
├── proxy.ts        ← Root level (same as package.json)
├── app/
└── ...
```

### 2. **Proxy Function Structure**
```typescript
export default function proxy(request: NextRequest) {
  // Check conditions
  if (condition) {
    return NextResponse.redirect(...);  // Redirect
  }
  
  return NextResponse.next();  // Allow request
}
```

### 3. **Cookie Operations**
```typescript
// SET cookie
document.cookie = 'name=value; path=/; max-age=86400';

// GET cookie (in proxy)
request.cookies.get('name');

// DELETE cookie
document.cookie = 'name=; path=/; max-age=0';
```

### 4. **Matcher Configuration**
```typescript
export const config = {
  matcher: [
    '/dashboard/:path*',  // Match all dashboard routes
    '/admin/:path*',      // Match all admin routes
    '/login',             // Match exact route
  ],
};
```

---

## 🐛 Common Issues & Solutions

**Problem:** Redirect loop (keeps redirecting)  
**Solution:** Check that login page isn't in the matcher, or add exception for it

**Problem:** Cookie not set  
**Solution:** Make sure `path=/` is included in cookie string

**Problem:** proxy.ts not running  
**Solution:** Restart dev server (`npm run dev`)

**Problem:** Still can access dashboard  
**Solution:** Clear all cookies and try again (hard refresh)

---

## 🎉 Congratulations!

You've mastered **proxy.ts** (middleware)! You now know:

✅ How to intercept requests with proxy.ts  
✅ How to implement authentication with cookies  
✅ How to protect routes from unauthorized access  
✅ How to redirect based on authentication state  
✅ How to handle login/logout flows  

**Your app now has:**
- 🔐 Protected dashboard routes
- 🍪 Cookie-based authentication
- 🔄 Smart redirects (remembers where user was going)
- 🚪 Full login/logout functionality
- ✅ Production-ready auth pattern

  You're officially ready for Day 10: Image Optimization 🚀
