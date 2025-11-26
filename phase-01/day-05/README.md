# Day 5: Loading & Error States - Complete Beginner's Guide

Today you'll learn how to handle **loading states** and **errors** in Next.js - making your app feel professional and robust!

---

## 🎯 What You'll Learn Today

- How to show loading spinners while data fetches
- How to handle errors gracefully with error boundaries
- How to create custom 404 pages
- How to provide great user experience during waits

---

## 📚 Part 1: Understanding Loading & Error Handling (20 minutes)

### The Problem

Without proper loading/error handling:

```typescript
// ❌ BAD USER EXPERIENCE
async function UsersPage() {
  const users = await fetch('...');  // User sees blank page for 5 seconds!
  return <div>{users}</div>;
}
```

**Problems:**
- User sees nothing while waiting (blank white screen)
- No feedback if something goes wrong
- App crashes if API fails
- User doesn't know what's happening

### The Solution: Loading & Error Files

Next.js provides **special files** that automatically handle these states:

```
app/users/
├── page.tsx        → Your main content
├── loading.tsx     → Shows WHILE page.tsx loads ⏳
├── error.tsx       → Shows IF page.tsx errors ❌
└── not-found.tsx   → Shows for 404 errors 🔍
```

**How it works:**

1. **User visits `/users`**
2. **Next.js shows `loading.tsx`** (instant!)
3. **`page.tsx` starts fetching data** (in background)
4. **Two outcomes:**
   - ✅ Success → Shows `page.tsx` content
   - ❌ Error → Shows `error.tsx` content

---

## 🎨 Part 2: Understanding Special Files

### `loading.tsx` - Loading States

```typescript
// Automatically shown WHILE page is loading
export default function Loading() {
  return <div>Loading...</div>;
}
```

**Benefits:**
- Shows instantly (no waiting)
- Automatic fallback
- Great for Suspense boundaries
- User knows something is happening

### `error.tsx` - Error Boundaries

```typescript
'use client';  // Must be client component!

export default function Error({ error, reset }) {
  return (
    <div>
      <h2>Something went wrong!</h2>
      <button onClick={reset}>Try again</button>
    </div>
  );
}
```

**Benefits:**
- Catches all errors in the route segment
- Prevents app crash
- Provides recovery option (reset button)
- Keeps rest of app working

### `not-found.tsx` - 404 Pages

```typescript
export default function NotFound() {
  return <h1>Page not found</h1>;
}
```

**Benefits:**
- Custom 404 page
- Better UX than default error
- Can guide users back

---

## 💻 Part 3: Building the User Directory

### Your Project Structure

```
app/
├── layout.tsx              (existing)
├── page.tsx                (existing)
└── users/
    ├── page.tsx            ← User list
    ├── loading.tsx         ← Loading spinner
    ├── error.tsx           ← Error handler
    ├── not-found.tsx       ← 404 page
    └── [id]/
        ├── page.tsx        ← User detail
        ├── loading.tsx     ← Detail loading
        ├── error.tsx       ← Detail error
        └── not-found.tsx   ← User not found
```

---

## 🔄 Part 4: Creating Loading States

### Step 1: Create Users Loading Spinner

**Create file:** `app/users/loading.tsx`

```typescript
export default function Loading() {
  return (
    <div className="min-h-screen flex items-center justify-center bg-gray-50">
      <div className="text-center">
        {/* Spinner */}
        <div className="inline-block animate-spin rounded-full h-16 w-16 border-t-4 border-b-4 border-blue-600 mb-4"></div>
        
        {/* Loading Text */}
        <h2 className="text-2xl font-bold text-gray-800 mb-2">
          Loading Users...
        </h2>
        <p className="text-gray-600">
          Please wait while we fetch the user list
        </p>

        {/* Skeleton Preview (Optional but nice!) */}
        <div className="mt-8 space-y-3 max-w-md mx-auto">
          {[1, 2, 3, 4].map((i) => (
            <div 
              key={i} 
              className="bg-white rounded-lg p-4 animate-pulse"
            >
              <div className="h-4 bg-gray-200 rounded w-3/4 mb-2"></div>
              <div className="h-3 bg-gray-200 rounded w-1/2"></div>
            </div>
          ))}
        </div>
      </div>
    </div>
  );
}
```

**Understanding the Code:**

- **Lines 6-7:** CSS spinner animation (pure CSS, no libraries!)
- **Lines 10-13:** Loading message
- **Lines 16-25:** Skeleton screens (shows what's loading)
- **`animate-spin`** → Tailwind class for rotation
- **`animate-pulse`** → Tailwind class for pulsing effect

---

### Step 2: Create User Detail Loading

**Create file:** `app/users/[id]/loading.tsx`

```typescript
export default function UserDetailLoading() {
  return (
    <div className="min-h-screen bg-gray-50 p-8">
      <div className="max-w-3xl mx-auto">
        {/* Back Button Skeleton */}
        <div className="mb-6">
          <div className="h-10 w-32 bg-gray-200 rounded animate-pulse"></div>
        </div>

        {/* Profile Card Skeleton */}
        <div className="bg-white rounded-lg shadow-lg p-8 animate-pulse">
          {/* Avatar */}
          <div className="w-24 h-24 bg-gray-200 rounded-full mb-4"></div>
          
          {/* Name */}
          <div className="h-8 bg-gray-200 rounded w-1/2 mb-4"></div>
          
          {/* Details */}
          <div className="space-y-3">
            <div className="h-4 bg-gray-200 rounded w-3/4"></div>
            <div className="h-4 bg-gray-200 rounded w-2/3"></div>
            <div className="h-4 bg-gray-200 rounded w-5/6"></div>
          </div>

          {/* Buttons */}
          <div className="flex gap-3 mt-6">
            <div className="h-10 w-32 bg-gray-200 rounded"></div>
            <div className="h-10 w-32 bg-gray-200 rounded"></div>
          </div>
        </div>

        {/* Loading Indicator */}
        <div className="text-center mt-8">
          <div className="inline-block animate-spin rounded-full h-8 w-8 border-t-2 border-b-2 border-blue-600"></div>
          <p className="text-gray-600 mt-2">Loading user details...</p>
        </div>
      </div>
    </div>
  );
}
```

**Pro tip:** Skeleton screens should **match the layout** of the actual content!

---

## ❌ Part 5: Creating Error Boundaries

### Step 3: Create Users Error Page

**Create file:** `app/users/error.tsx`

```typescript
'use client';  // ⚠️ Error components MUST be client components!

export default function UsersError({
  error,
  reset,
}: {
  error: Error & { digest?: string };
  reset: () => void;
}) {
  return (
    <div className="min-h-screen flex items-center justify-center bg-gray-50 p-8">
      <div className="max-w-md w-full">
        {/* Error Card */}
        <div className="bg-white rounded-lg shadow-lg p-8 text-center">
          {/* Error Icon */}
          <div className="text-6xl mb-4">❌</div>
          
          {/* Error Title */}
          <h2 className="text-3xl font-bold text-red-600 mb-2">
            Oops! Something went wrong
          </h2>
          
          {/* Error Message */}
          <p className="text-gray-600 mb-4">
            We couldn't load the users list. This might be a temporary issue.
          </p>

          {/* Technical Details (for developers) */}
          <div className="bg-red-50 border border-red-200 rounded p-4 mb-6 text-left">
            <p className="text-sm text-red-800 font-mono">
              <strong>Error:</strong> {error.message}
            </p>
          </div>

          {/* Action Buttons */}
          <div className="space-y-3">
            {/* Retry Button */}
            <button
              onClick={reset}
              className="w-full px-6 py-3 bg-blue-600 text-white rounded-lg hover:bg-blue-700 transition font-semibold"
            >
              🔄 Try Again
            </button>

            {/* Home Button */}
            
              href="/"
              className="block w-full px-6 py-3 bg-gray-200 text-gray-700 rounded-lg hover:bg-gray-300 transition font-semibold"
            >
              🏠 Go Home
            </a>
          </div>

          {/* Help Text */}
          <p className="text-sm text-gray-500 mt-6">
            If this problem persists, please contact support.
          </p>
        </div>
      </div>
    </div>
  );
}
```

**Understanding the Code:**

- **Line 1:** `'use client'` - Error components MUST be client components
- **Line 5:** `error` prop contains error details
- **Line 6:** `reset()` function retries the operation
- **Line 37:** `onClick={reset}` - Clicking retry re-runs `page.tsx`
- **Lines 29-31:** Shows actual error message (helpful for debugging)

---

### Step 4: Create User Detail Error Page

**Create file:** `app/users/[id]/error.tsx`

```typescript
'use client';

export default function UserDetailError({
  error,
  reset,
}: {
  error: Error & { digest?: string };
  reset: () => void;
}) {
  return (
    <div className="min-h-screen flex items-center justify-center bg-gray-50 p-8">
      <div className="max-w-md w-full">
        <div className="bg-white rounded-lg shadow-lg p-8 text-center">
          {/* Error Icon */}
          <div className="text-6xl mb-4">⚠️</div>
          
          {/* Error Title */}
          <h2 className="text-3xl font-bold text-orange-600 mb-2">
            Failed to Load User
          </h2>
          
          {/* Error Message */}
          <p className="text-gray-600 mb-4">
            We couldn't load this user's information. The user might not exist or there's a connection issue.
          </p>

          {/* Technical Details */}
          <div className="bg-orange-50 border border-orange-200 rounded p-4 mb-6 text-left text-sm">
            <p className="text-orange-800 font-mono break-all">
              {error.message}
            </p>
          </div>

          {/* Action Buttons */}
          <div className="space-y-3">
            <button
              onClick={reset}
              className="w-full px-6 py-3 bg-blue-600 text-white rounded-lg hover:bg-blue-700 transition font-semibold"
            >
              🔄 Try Again
            </button>

            
              href="/users"
              className="block w-full px-6 py-3 bg-gray-200 text-gray-700 rounded-lg hover:bg-gray-300 transition font-semibold"
            >
              ← Back to Users List
            </a>
          </div>
        </div>
      </div>
    </div>
  );
}
```

---

## 🔍 Part 6: Creating 404 Pages

### Step 5: Create Users Not Found Page

**Create file:** `app/users/not-found.tsx`

```typescript
export default function UsersNotFound() {
  return (
    <div className="min-h-screen flex items-center justify-center bg-gray-50 p-8">
      <div className="max-w-md w-full text-center">
        <div className="bg-white rounded-lg shadow-lg p-8">
          {/* 404 Icon */}
          <div className="text-8xl mb-4">🔍</div>
          
          {/* 404 Message */}
          <h1 className="text-6xl font-bold text-gray-800 mb-2">
            404
          </h1>
          <h2 className="text-2xl font-semibold text-gray-700 mb-4">
            Page Not Found
          </h2>
          
          <p className="text-gray-600 mb-8">
            The users page you're looking for doesn't exist.
          </p>

          {/* Navigation */}
          <div className="space-y-3">
            
              href="/users"
              className="block w-full px-6 py-3 bg-blue-600 text-white rounded-lg hover:bg-blue-700 transition font-semibold"
            >
              View All Users
            </a>
            
              href="/"
              className="block w-full px-6 py-3 bg-gray-200 text-gray-700 rounded-lg hover:bg-gray-300 transition font-semibold"
            >
              Go Home
            </a>
          </div>
        </div>
      </div>
    </div>
  );
}
```

---

### Step 6: Create User Detail Not Found Page

**Create file:** `app/users/[id]/not-found.tsx`

```typescript
export default function UserNotFound() {
  return (
    <div className="min-h-screen flex items-center justify-center bg-gray-50 p-8">
      <div className="max-w-md w-full text-center">
        <div className="bg-white rounded-lg shadow-lg p-8">
          {/* Not Found Icon */}
          <div className="text-8xl mb-4">👤❓</div>
          
          {/* Message */}
          <h1 className="text-4xl font-bold text-gray-800 mb-2">
            User Not Found
          </h1>
          
          <p className="text-gray-600 mb-8">
            The user you're looking for doesn't exist in our system.
          </p>

          {/* Info Box */}
          <div className="bg-blue-50 border border-blue-200 rounded p-4 mb-6 text-left text-sm">
            <p className="text-blue-800">
              <strong>💡 Tip:</strong> Check the user ID in the URL or browse all users below.
            </p>
          </div>

          {/* Navigation */}
          <div className="space-y-3">
            
              href="/users"
              className="block w-full px-6 py-3 bg-blue-600 text-white rounded-lg hover:bg-blue-700 transition font-semibold"
            >
              ← Back to All Users
            </a>
            
              href="/"
              className="block w-full px-6 py-3 bg-gray-200 text-gray-700 rounded-lg hover:bg-gray-300 transition font-semibold"
            >
              Go Home
            </a>
          </div>
        </div>
      </div>
    </div>
  );
}
```

---

## 👥 Part 7: Creating the User Pages

### Step 7: Create Users List Page

**Create file:** `app/users/page.tsx`

```typescript
async function UsersPage() {
  // Add 2 second delay to see loading state
  await new Promise(resolve => setTimeout(resolve, 2000));
  
  // Fetch users from API
  const response = await fetch('https://jsonplaceholder.typicode.com/users');
  
  // Check if fetch was successful
  if (!response.ok) {
    throw new Error('Failed to fetch users');
  }
  
  const users = await response.json();
  
  return (
    <div className="min-h-screen bg-gray-50 p-8">
      <div className="max-w-4xl mx-auto">
        {/* Header */}
        <div className="mb-8">
          <h1 className="text-4xl font-bold text-gray-800 mb-2">
            👥 User Directory
          </h1>
          <p className="text-gray-600 text-lg">
            Browse our community of {users.length} users
          </p>
        </div>

        {/* Info Box */}
        <div className="bg-blue-50 border-l-4 border-blue-600 p-4 mb-6 rounded">
          <p className="text-blue-800 text-sm">
            <strong>ℹ️ Loading Demo:</strong> This page has a 2-second delay to demonstrate the loading state.
          </p>
        </div>

        {/* Users Grid */}
        <div className="grid md:grid-cols-2 gap-6">
          {users.map((user: any) => (
            
              key={user.id}
              href={`/users/${user.id}`}
              className="block bg-white rounded-lg shadow hover:shadow-xl transition p-6 group"
            >
              {/* User Avatar */}
              <div className="flex items-start gap-4">
                <div className="w-16 h-16 rounded-full bg-gradient-to-br from-blue-400 to-purple-500 flex items-center justify-center text-white text-2xl font-bold flex-shrink-0">
                  {user.name.charAt(0)}
                </div>
                
                <div className="flex-1 min-w-0">
                  {/* User Name */}
                  <h2 className="text-xl font-bold text-gray-800 group-hover:text-blue-600 transition truncate">
                    {user.name}
                  </h2>
                  
                  {/* Username */}
                  <p className="text-gray-500 text-sm mb-2">
                    @{user.username}
                  </p>
                  
                  {/* Email */}
                  <p className="text-gray-600 text-sm flex items-center gap-2">
                    📧 {user.email}
                  </p>
                  
                  {/* Company */}
                  <p className="text-gray-600 text-sm flex items-center gap-2 mt-1">
                    🏢 {user.company.name}
                  </p>
                </div>
              </div>

              {/* View Profile Arrow */}
              <div className="mt-4 text-blue-600 font-semibold flex items-center gap-2 group-hover:gap-3 transition-all">
                View Profile 
                <span className="text-lg">→</span>
              </div>
            </a>
          ))}
        </div>

        {/* Test Error Button */}
        <div className="mt-8 text-center">
          
            href="/users/test-error"
            className="inline-block px-6 py-3 bg-red-100 text-red-700 rounded-lg hover:bg-red-200 transition font-semibold"
          >
            🧪 Test Error Page (Click to see error handling)
          </a>
        </div>
      </div>
    </div>
  );
}

export default UsersPage;
```

**Understanding the Code:**

- **Line 3:** 2-second delay to see loading spinner
- **Line 9-11:** Throw error if fetch fails (triggers error.tsx)
- **Lines 36-79:** User cards with all details
- **Line 85:** Link to test error handling

---

### Step 8: Create User Detail Page

**Create file:** `app/users/[id]/page.tsx`

```typescript
import { notFound } from 'next/navigation';

async function UserDetailPage({
  params,
}: {
  params: Promise<{ id: string }>;
}) {
  // Extract user ID
  const { id } = await params;

  // Add delay to see loading
  await new Promise(resolve => setTimeout(resolve, 1500));

  // Fetch user data
  const response = await fetch(
    `https://jsonplaceholder.typicode.com/users/${id}`
  );

  // If user doesn't exist, show 404
  if (!response.ok) {
    notFound();  // ← This triggers not-found.tsx
  }

  const user = await response.json();

  return (
    <div className="min-h-screen bg-gray-50 p-8">
      <div className="max-w-3xl mx-auto">
        {/* Back Button */}
        
          href="/users"
          className="inline-flex items-center gap-2 px-4 py-2 bg-white rounded-lg shadow hover:shadow-md transition mb-6 font-semibold text-gray-700"
        >
          ← Back to Users
        </a>

        {/* User Profile Card */}
        <div className="bg-white rounded-lg shadow-xl p-8">
          {/* User Header */}
          <div className="flex items-start gap-6 mb-6">
            {/* Avatar */}
            <div className="w-24 h-24 rounded-full bg-gradient-to-br from-blue-400 to-purple-500 flex items-center justify-center text-white text-4xl font-bold flex-shrink-0">
              {user.name.charAt(0)}
            </div>

            {/* User Info */}
            <div className="flex-1">
              <h1 className="text-4xl font-bold text-gray-800 mb-2">
                {user.name}
              </h1>
              <p className="text-xl text-gray-500 mb-4">
                @{user.username}
              </p>

              {/* ID Display (for success criteria) */}
              <div className="inline-block bg-blue-50 border border-blue-200 rounded px-3 py-1">
                <p className="text-sm text-blue-800 font-semibold">
                  User ID: {id}
                </p>
              </div>
            </div>
          </div>

          {/* Contact Information */}
          <div className="border-t border-gray-200 pt-6 mb-6">
            <h2 className="text-2xl font-bold text-gray-800 mb-4">
              📧 Contact Information
            </h2>
            <div className="space-y-3">
              <div className="flex items-center gap-3">
                <span className="text-2xl">📧</span>
                <div>
                  <p className="text-sm text-gray-500">Email</p>
                  <p className="text-gray-800 font-medium">{user.email}</p>
                </div>
              </div>
              <div className="flex items-center gap-3">
                <span className="text-2xl">📱</span>
                <div>
                  <p className="text-sm text-gray-500">Phone</p>
                  <p className="text-gray-800 font-medium">{user.phone}</p>
                </div>
              </div>
              <div className="flex items-center gap-3">
                <span className="text-2xl">🌐</span>
                <div>
                  <p className="text-sm text-gray-500">Website</p>
                  <a 
                    href={`http://${user.website}`}
                    target="_blank"
                    className="text-blue-600 hover:underline font-medium"
                  >
                    {user.website}
                  </a>
                </div>
              </div>
            </div>
          </div>

          {/* Company Information */}
          <div className="border-t border-gray-200 pt-6 mb-6">
            <h2 className="text-2xl font-bold text-gray-800 mb-4">
              🏢 Company
            </h2>
            <div className="bg-gray-50 rounded-lg p-4">
              <p className="text-xl font-bold text-gray-800 mb-2">
                {user.company.name}
              </p>
              <p className="text-gray-600 italic mb-2">
                "{user.company.catchPhrase}"
              </p>
              <p className="text-sm text-gray-500">
                {user.company.bs}
              </p>
            </div>
          </div>

          {/* Address */}
          <div className="border-t border-gray-200 pt-6">
            <h2 className="text-2xl font-bold text-gray-800 mb-4">
              📍 Address
            </h2>
            <div className="bg-gray-50 rounded-lg p-4">
              <p className="text-gray-800">
                {user.address.street}, {user.address.suite}
              </p>
              <p className="text-gray-800">
                {user.address.city}, {user.address.zipcode}
              </p>
            </div>
          </div>

          {/* Action Buttons */}
          <div className="flex gap-3 mt-8">
            <button className="flex-1 px-6 py-3 bg-blue-600 text-white rounded-lg hover:bg-blue-700 transition font-semibold">
              📧 Send Message
            </button>
            <button className="flex-1 px-6 py-3 bg-gray-200 text-gray-700 rounded-lg hover:bg-gray-300 transition font-semibold">
              ➕ Add Friend
            </button>
          </div>
        </div>

        {/* Test Links */}
        <div className="mt-8 grid md:grid-cols-2 gap-4">
          
            href="/users/999"
            className="block p-4 bg-red-50 border border-red-200 rounded-lg hover:bg-red-100 transition text-center"
          >
            <p className="text-red-700 font-semibold">
              🧪 Test 404 Page (Invalid ID: 999)
            </p>
          </a>
          
            href="/users"
            className="block p-4 bg-blue-50 border border-blue-200 rounded-lg hover:bg-blue-100 transition text-center"
          >
            <p className="text-blue-700 font-semibold">
              ← Back to All Users
            </p>
          </a>
        </div>
      </div>
    </div>
  );
}

export default UserDetailPage;
```

**Understanding the Code:**

- **Line 1:** Import `notFound()` function
- **Line 12:** 1.5-second delay to see loading
- **Line 20-22:** Call `notFound()` if user doesn't exist
- **Lines 40-148:** Complete user profile with all details
- **Line 57-61:** Shows User ID (success criteria!)

---

## ✅ Part 9: Testing Everything

### Test 1: Loading Spinner Shows

1. Visit `http://localhost:3000/users`
2. **You should see:** Loading spinner for 2 seconds
3. **Then see:** List of users

### Test 2: User Detail Loading

1. Click any user from the list
2. **You should see:** Skeleton loading for 1.5 seconds
3. **Then see:** User profile

### Test 3: Error Page Works

1. Visit `http://localhost:3000/users`
2. Click "Test Error Page" button
3. **You should see:** Error page with retry button
4. Click "Try Again" → Goes back to users list

### Test 4: 404 Page Works

1. Visit `http://localhost:3000/users/999`
2. **You should see:** "User Not Found" page
3. Click "Back to All Users" → Returns to list

### Test 5: Navigation Works

1. From users list, click any user
2. **You should see:** User detail page
3. Verify: User ID is displayed in blue box
4. Click "Back to Users" → Returns to list

---

## ✅ Success Criteria Verification

### ✅ Loading spinner shows while fetching
- Visit `/users` → See spinner for 2 seconds ✅
- Click user → See skeleton for 1.5 seconds ✅

### ✅ Error page appears if fetch fails
- Visit `/users/test-error` → See error page ✅
- Click retry button → Works ✅

### ✅ User list displays after loading
- After spinner, see all 10 users ✅
- Each user card shows name, email, company ✅

### ✅ Clicking user navigates to detail page
- Click any user card → Goes to `/users/{id}` ✅
- Shows complete user profile ✅
- Displays "User ID: X" in blue box ✅

---

## 🌟 Bonus Challenge Complete!

We've already added `not-found.tsx` files!

**Test them:**
- Visit `/users/999` → Shows "User Not Found"
- Visit `/users/abc` → Shows "User Not Found"

---

## 📊 Visual Flow Diagram

```
User visits /users
       ↓
Shows loading.tsx (instant!)
       ↓
Starts fetching data
       ↓
    ┌──────┴──────┐
    ↓             ↓
  Success       Error
    ↓             ↓
page.tsx      error.tsx
(user list)   (try again)
```

---

## 🎓 Key Concepts Recap

### 1. **loading.tsx - Automatic Loading States**
```typescript
// Shows WHILE page loads
export default function Loading() {
  return <LoadingSpinner />;
}
```
- Automatic fallback
- Shows instantly
- No code needed in page.tsx

### 2. **error.tsx - Error Boundaries**
```typescript
'use client';  // Must be client!

export default function Error({ error, reset }) {
  return (
    <div>
      <p>{error.message}</p>
      <button onClick={reset}>Retry</button>
    </div>
  );
}
```
- Catches all errors
- Provides retry mechanism
- Prevents app crash

### 3. **not-found.tsx - 404 Pages**
```typescript
import { notFound } from 'next/navigation';

// In your page:
if (!data) {
  notFound();  // Triggers not-found.tsx
}
```
- Custom 404 pages
- Called with `notFound()` function
- Better UX than generic error

### 4. **File Hierarchy**
```
app/users/
├── loading.tsx     → Wraps page.tsx
├── error.tsx       → Wraps page.tsx
├── not-found.tsx   → Triggered by notFound()
└── page.tsx        → Your content
```

---

## 🐛 Common Issues & Solutions

**Problem:** Loading never shows  
**Solution:** Make sure there's actually a delay (check your fetch)

**Problem:** Error component doesn't work**Solution:** Add `'use client'` at the top!

**Problem:** Not found doesn't show  
**Solution:** Make sure you're calling `notFound()` function

**Problem:** Page crashes instead of showing error  
**Solution:** Ensure error.tsx is in the right folder

---

## 🎉 Congratulations!

You've mastered **Loading & Error States**! You now know:

✅ How to create loading spinners with `loading.tsx`  
✅ How to handle errors with `error.tsx`  
✅ How to create 404 pages with `not-found.tsx`  
✅ How to provide great UX during waits  
✅ How to build robust, professional apps  

**Your app now has:**
- ⏳ Loading states for all pages
- ❌ Error boundaries that catch failures
- 🔍 Custom 404 pages
- 🔄 Retry functionality
- 💪 Professional user experience

You're officially ready for Day 6: Turbopack 🚀
