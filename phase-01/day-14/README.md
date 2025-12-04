# Day 14: Client-Side Fetching with SWR - Complete Beginner's Guide

Today you'll learn how to **fetch data on the client side** using SWR - a powerful React hooks library for data fetching!

---

## 🎯 What You'll Learn Today

- What SWR is and why it's powerful
- Client-side vs server-side fetching
- Auto-revalidation and caching
- Real-time data updates
- Optimistic UI updates
- Debounced search

---

## 📚 Part 1: Understanding SWR (20 minutes)

### What is SWR?

**SWR** stands for **"stale-while-revalidate"** - a caching strategy.

**Think of it like a news app:**
```
1. Show cached news (even if old) → Fast UX
2. Check for new news in background → Stay fresh
3. Update with new news if found → Always current
```

### Why Use SWR?

```
WITHOUT SWR (Manual Fetching):
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
❌ Write useEffect + useState boilerplate
❌ Manual loading states
❌ Manual error handling
❌ No caching
❌ No auto-refresh
❌ Race conditions possible
❌ Repetitive code

WITH SWR:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ One hook: useSWR()
✅ Automatic loading states
✅ Automatic error handling
✅ Built-in caching
✅ Auto-revalidation
✅ Race condition safe
✅ Clean, simple code
```

---

## 🔍 Part 2: When to Use Server vs Client Fetching

### Server Components (Day 13)
```typescript
// ✅ Use for:
- Initial page load data
- SEO-important content
- Secure data (API keys)
- Data that rarely changes

async function Page() {
  const data = await fetch('...');
  return <div>{data}</div>;
}
```

### Client Components + SWR (Today)
```typescript
// ✅ Use for:
- Real-time updates
- User interactions (search, filters)
- Data that changes frequently
- Polling/auto-refresh
- After user actions

'use client';
function Page() {
  const { data } = useSWR('...', fetcher);
  return <div>{data}</div>;
}
```

### Combined Approach (Best!)
```typescript
// Server: Initial data (fast first load)
async function Page() {
  const initialData = await fetch('...');
  return <ClientComponent fallbackData={initialData} />;
}

// Client: Real-time updates
'use client';
function ClientComponent({ fallbackData }) {
  const { data } = useSWR('...', fetcher, { fallbackData });
  return <div>{data}</div>;
}
```

---

## 💻 Part 3: Installing SWR

### Step 1: Install SWR

Open your terminal and run:

```bash
npm install swr
```

**Wait for installation...**

```
added 1 package in 2s
```

---

## 📊 Part 4: Creating a Live Stock Ticker

### Step 2: Create Stock Ticker Component

**Create file:** `components/StockTicker.tsx`

```typescript
'use client';

import useSWR from 'swr';
import { useState } from 'react';

// Fetcher function - SWR requires this
const fetcher = (url: string) => fetch(url).then((res) => {
  if (!res.ok) throw new Error('Failed to fetch');
  return res.json();
});

export default function StockTicker() {
  const [isPaused, setIsPaused] = useState(false);

  // SWR hook - the magic!
  const { data, error, isLoading, mutate } = useSWR(
    'https://jsonplaceholder.typicode.com/posts/1',
    fetcher,
    {
      refreshInterval: isPaused ? 0 : 5000,  // Refresh every 5 seconds (unless paused)
      revalidateOnFocus: true,                // Refresh when window gets focus
      revalidateOnReconnect: true,            // Refresh when internet reconnects
    }
  );

  // Loading state
  if (isLoading) {
    return (
      <div className="bg-white rounded-lg shadow-lg p-8 border-l-4 border-blue-600">
        <div className="animate-pulse">
          <div className="h-6 bg-gray-200 rounded w-3/4 mb-4"></div>
          <div className="h-4 bg-gray-200 rounded w-full mb-2"></div>
          <div className="h-4 bg-gray-200 rounded w-5/6"></div>
        </div>
        <p className="text-sm text-gray-500 mt-4">Loading data...</p>
      </div>
    );
  }

  // Error state
  if (error) {
    return (
      <div className="bg-white rounded-lg shadow-lg p-8 border-l-4 border-red-600">
        <div className="flex items-start gap-4">
          <span className="text-4xl">❌</span>
          <div className="flex-1">
            <h3 className="text-xl font-bold text-red-600 mb-2">
              Failed to Load Data
            </h3>
            <p className="text-gray-600 mb-4">
              {error.message}
            </p>
            <button
              onClick={() => mutate()}
              className="px-4 py-2 bg-red-600 text-white rounded-lg hover:bg-red-700 transition font-semibold"
            >
              🔄 Try Again
            </button>
          </div>
        </div>
      </div>
    );
  }

  // Success state - data loaded!
  return (
    <div className="bg-white rounded-lg shadow-lg p-8 border-l-4 border-green-600">
      {/* Header */}
      <div className="flex items-start justify-between mb-4">
        <div>
          <h2 className="text-2xl font-bold text-gray-800 mb-1">
            📈 Live Stock Ticker
          </h2>
          <p className="text-sm text-gray-500">
            Updates every 5 seconds
          </p>
        </div>
        <div className="flex gap-2">
          {/* Pause/Resume Button */}
          <button
            onClick={() => setIsPaused(!isPaused)}
            className={`px-4 py-2 rounded-lg font-semibold transition ${
              isPaused
                ? 'bg-green-100 text-green-700 hover:bg-green-200'
                : 'bg-yellow-100 text-yellow-700 hover:bg-yellow-200'
            }`}
          >
            {isPaused ? '▶️ Resume' : '⏸️ Pause'}
          </button>
          {/* Manual Refresh Button */}
          <button
            onClick={() => mutate()}
            className="px-4 py-2 bg-blue-600 text-white rounded-lg hover:bg-blue-700 transition font-semibold"
          >
            🔄 Refresh
          </button>
        </div>
      </div>

      {/* Data Display */}
      <div className="bg-gray-50 rounded-lg p-6 mb-4">
        <h3 className="text-xl font-bold text-gray-800 mb-3 capitalize">
          {data.title}
        </h3>
        <p className="text-gray-700 leading-relaxed">
          {data.body}
        </p>
      </div>

      {/* Metadata */}
      <div className="flex items-center justify-between text-sm text-gray-500">
        <div className="flex items-center gap-4">
          <span>📌 Post ID: {data.id}</span>
          <span>👤 User ID: {data.userId}</span>
        </div>
        <div className="flex items-center gap-2">
          {!isPaused && (
            <>
              <span className="w-2 h-2 bg-green-500 rounded-full animate-pulse"></span>
              <span>Live</span>
            </>
          )}
          {isPaused && (
            <>
              <span className="w-2 h-2 bg-gray-400 rounded-full"></span>
              <span>Paused</span>
            </>
          )}
        </div>
      </div>

      {/* Info */}
      <div className="mt-6 bg-blue-50 border border-blue-200 rounded-lg p-4">
        <h4 className="font-bold text-blue-800 text-sm mb-2">
          ⚡ SWR Features in Action:
        </h4>
        <ul className="text-blue-700 text-sm space-y-1">
          <li>✅ Auto-refreshes every 5 seconds</li>
          <li>✅ Caches data for instant display</li>
          <li>✅ Revalidates on window focus</li>
          <li>✅ Handles errors automatically</li>
          <li>✅ Manual refresh with mutate()</li>
        </ul>
      </div>
    </div>
  );
}
```

**Understanding the Code:**

- **Line 1:** `'use client'` - Must be client component
- **Line 7-10:** Fetcher function (required by SWR)
- **Line 16-23:** `useSWR()` hook with options
- **Line 19:** `refreshInterval` - auto-refresh every 5 seconds
- **Line 113:** `mutate()` - manually refresh data
- **Lines 27-38:** Loading skeleton
- **Lines 41-63:** Error handling with retry

---

### Step 3: Create Demo Page

**Create file:** `app/stock/page.tsx`

```typescript
import StockTicker from '@/components/StockTicker';

export default function StockPage() {
  return (
    <div className="min-h-screen bg-gradient-to-br from-green-50 to-blue-50 py-12 px-4">
      <div className="max-w-4xl mx-auto">
        {/* Header */}
        <div className="text-center mb-12">
          <h1 className="text-5xl font-bold text-gray-800 mb-4">
            📊 Live Data Dashboard
          </h1>
          <p className="text-xl text-gray-600">
            Client-side data fetching with SWR
          </p>
        </div>

        {/* Info Box */}
        <div className="bg-blue-50 border-l-4 border-blue-600 p-6 mb-8 rounded">
          <h3 className="text-lg font-bold text-blue-800 mb-2">
            🔄 Real-Time Updates
          </h3>
          <ul className="space-y-2 text-blue-700">
            <li>✅ Data fetches on the client (browser)</li>
            <li>✅ Automatically refreshes every 5 seconds</li>
            <li>✅ Caches data for better performance</li>
            <li>✅ Handles loading and error states</li>
            <li>✅ Manual refresh available</li>
          </ul>
        </div>

        {/* Stock Ticker Component */}
        <StockTicker />

        {/* How It Works */}
        <div className="mt-12 bg-white rounded-lg shadow-lg p-8">
          <h2 className="text-2xl font-bold text-gray-800 mb-6">
            🔧 How SWR Works
          </h2>

          <div className="space-y-6">
            {/* Step 1 */}
            <div className="flex gap-4">
              <div className="flex-shrink-0 w-10 h-10 bg-blue-100 rounded-full flex items-center justify-center text-blue-600 font-bold">
                1
              </div>
              <div>
                <h3 className="font-bold text-gray-800 mb-1">
                  Initial Fetch
                </h3>
                <p className="text-gray-600 text-sm">
                  SWR fetches data when component mounts. Shows loading state during first fetch.
                </p>
              </div>
            </div>

            {/* Step 2 */}
            <div className="flex gap-4">
              <div className="flex-shrink-0 w-10 h-10 bg-purple-100 rounded-full flex items-center justify-center text-purple-600 font-bold">
                2
              </div>
              <div>
                <h3 className="font-bold text-gray-800 mb-1">
                  Cache Data
                </h3>
                <p className="text-gray-600 text-sm">
                  Data is cached in memory. Next time you visit, cached data shows instantly!
                </p>
              </div>
            </div>

            {/* Step 3 */}
            <div className="flex gap-4">
              <div className="flex-shrink-0 w-10 h-10 bg-green-100 rounded-full flex items-center justify-center text-green-600 font-bold">
                3
              </div>
              <div>
                <h3 className="font-bold text-gray-800 mb-1">
                  Auto-Revalidate
                </h3>
                <p className="text-gray-600 text-sm">
                  Every 5 seconds, SWR refetches in background and updates if data changed.
                </p>
              </div>
            </div>

            {/* Step 4 */}
            <div className="flex gap-4">
              <div className="flex-shrink-0 w-10 h-10 bg-yellow-100 rounded-full flex items-center justify-center text-yellow-600 font-bold">
                4
              </div>
              <div>
                <h3 className="font-bold text-gray-800 mb-1">
                  Smart Updates
                </h3>
                <p className="text-gray-600 text-sm">
                  Only updates UI if data actually changed. No unnecessary re-renders!
                </p>
              </div>
            </div>
          </div>
        </div>

        {/* Code Example */}
        <div className="mt-8 bg-gray-800 rounded-lg p-6 text-white font-mono text-sm overflow-x-auto">
          <p className="text-green-400 mb-2">// SWR Usage:</p>
          <pre className="text-gray-300">{`'use client';
import useSWR from 'swr';

const fetcher = (url) => fetch(url).then(r => r.json());

export default function Component() {
  const { data, error, isLoading } = useSWR(
    'https://api.example.com/data',
    fetcher,
    { refreshInterval: 5000 }
  );
  
  if (isLoading) return <div>Loading...</div>;
  if (error) return <div>Error!</div>;
  return <div>{data.title}</div>;
}`}</pre>
        </div>

        {/* Test Instructions */}
        <div className="mt-8 bg-yellow-50 border-l-4 border-yellow-500 p-6 rounded">
          <h3 className="text-lg font-bold text-yellow-800 mb-3">
            🧪 Test SWR Features
          </h3>
          <ol className="list-decimal list-inside space-y-2 text-yellow-700">
            <li>Watch the data auto-refresh every 5 seconds</li>
            <li>Click "Pause" to stop auto-refresh</li>
            <li>Click "Refresh" to manually fetch new data</li>
            <li>Open DevTools → Network tab to see requests</li>
            <li>Switch to another tab, then come back (revalidates on focus)</li>
          </ol>
        </div>
      </div>
    </div>
  );
}
```

---

## 🔍 Part 5: Advanced SWR Features

### Step 4: Create Multi-Ticker Dashboard

**Create file:** `components/MultiTicker.tsx`

```typescript
'use client';

import useSWR from 'swr';

const fetcher = (url: string) => fetch(url).then((res) => res.json());

export default function MultiTicker() {
  // Fetch multiple data sources simultaneously
  const { data: post1 } = useSWR('https://jsonplaceholder.typicode.com/posts/1', fetcher, {
    refreshInterval: 3000,
  });

  const { data: post2 } = useSWR('https://jsonplaceholder.typicode.com/posts/2', fetcher, {
    refreshInterval: 3000,
  });

  const { data: post3 } = useSWR('https://jsonplaceholder.typicode.com/posts/3', fetcher, {
    refreshInterval: 3000,
  });

  return (
    <div className="grid md:grid-cols-3 gap-6">
      {/* Ticker 1 */}
      <div className="bg-white rounded-lg shadow-lg p-6 border-t-4 border-blue-500">
        <div className="flex items-center justify-between mb-3">
          <h3 className="font-bold text-gray-800">Stock A</h3>
          <span className="w-2 h-2 bg-green-500 rounded-full animate-pulse"></span>
        </div>
        {post1 ? (
          <>
            <p className="text-3xl font-bold text-blue-600 mb-2">
              ${(Math.random() * 100 + 100).toFixed(2)}
            </p>
            <p className="text-sm text-gray-600 line-clamp-2">
              {post1.title}
            </p>
          </>
        ) : (
          <div className="animate-pulse">
            <div className="h-8 bg-gray-200 rounded mb-2"></div>
            <div className="h-4 bg-gray-200 rounded"></div>
          </div>
        )}
      </div>

      {/* Ticker 2 */}
      <div className="bg-white rounded-lg shadow-lg p-6 border-t-4 border-green-500">
        <div className="flex items-center justify-between mb-3">
          <h3 className="font-bold text-gray-800">Stock B</h3>
          <span className="w-2 h-2 bg-green-500 rounded-full animate-pulse"></span>
        </div>
        {post2 ? (
          <>
            <p className="text-3xl font-bold text-green-600 mb-2">
              ${(Math.random() * 100 + 200).toFixed(2)}
            </p>
            <p className="text-sm text-gray-600 line-clamp-2">
              {post2.title}
            </p>
          </>
        ) : (
          <div className="animate-pulse">
            <div className="h-8 bg-gray-200 rounded mb-2"></div>
            <div className="h-4 bg-gray-200 rounded"></div>
          </div>
        )}
      </div>

      {/* Ticker 3 */}
      <div className="bg-white rounded-lg shadow-lg p-6 border-t-4 border-purple-500">
        <div className="flex items-center justify-between mb-3">
          <h3 className="font-bold text-gray-800">Stock C</h3>
          <span className="w-2 h-2 bg-green-500 rounded-full animate-pulse"></span>
        </div>
        {post3 ? (
          <>
            <p className="text-3xl font-bold text-purple-600 mb-2">
              ${(Math.random() * 100 + 150).toFixed(2)}
            </p>
            <p className="text-sm text-gray-600 line-clamp-2">
              {post3.title}
            </p>
          </>
        ) : (
          <div className="animate-pulse">
            <div className="h-8 bg-gray-200 rounded mb-2"></div>
            <div className="h-4 bg-gray-200 rounded"></div>
          </div>
        )}
      </div>
    </div>
  );
}
```

**Add to stock page:**

```typescript
import MultiTicker from '@/components/MultiTicker';

// Add this section before the "How It Works" section
<div className="mt-8">
  <h2 className="text-2xl font-bold text-gray-800 mb-4">
    Multiple Live Tickers
  </h2>
  <MultiTicker />
</div>
```

---

## 🌟 Bonus Challenge: Debounced Search

### Step 5: Create Search Component

**Create file:** `components/SearchPosts.tsx`

```typescript
'use client';

import useSWR from 'swr';
import { useState, useEffect } from 'react';

const fetcher = (url: string) => fetch(url).then((res) => res.json());

export default function SearchPosts() {
  const [searchTerm, setSearchTerm] = useState('');
  const [debouncedTerm, setDebouncedTerm] = useState('');

  // Debounce: Wait 500ms after user stops typing
  useEffect(() => {
    const timer = setTimeout(() => {
      setDebouncedTerm(searchTerm);
    }, 500);

    return () => clearTimeout(timer);
  }, [searchTerm]);

  // Only fetch if there's a search term
  const { data, error, isLoading } = useSWR(
    debouncedTerm
      ? `https://jsonplaceholder.typicode.com/posts?userId=${debouncedTerm}`
      : null,
    fetcher
  );

  return (
    <div className="bg-white rounded-lg shadow-lg p-8">
      <h2 className="text-2xl font-bold text-gray-800 mb-4">
        🔍 Search Posts
      </h2>

      {/* Search Input */}
      <div className="mb-6">
        <input
          type="text"
          value={searchTerm}
          onChange={(e) => setSearchTerm(e.target.value)}
          placeholder="Search by User ID (1-10)..."
          className="w-full px-4 py-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
        />
        <p className="text-sm text-gray-500 mt-2">
          {debouncedTerm
            ? `Searching for User ID: ${debouncedTerm}`
            : 'Type a number between 1-10 to search'}
        </p>
      </div>

      {/* Loading State */}
      {isLoading && (
        <div className="space-y-3">
          {[1, 2, 3].map((i) => (
            <div key={i} className="bg-gray-50 rounded-lg p-4 animate-pulse">
              <div className="h-4 bg-gray-200 rounded w-3/4 mb-2"></div>
              <div className="h-3 bg-gray-200 rounded w-full"></div>
            </div>
          ))}
        </div>
      )}

      {/* Error State */}
      {error && (
        <div className="bg-red-50 border-l-4 border-red-500 p-4 rounded">
          <p className="text-red-700">Failed to load results</p>
        </div>
      )}

      {/* Results */}
      {data && data.length > 0 && (
        <div className="space-y-3">
          <p className="text-sm font-semibold text-gray-600 mb-3">
            Found {data.length} posts
          </p>
          {data.slice(0, 5).map((post: any) => (
            <div key={post.id} className="bg-gray-50 rounded-lg p-4 hover:bg-gray-100 transition">
              <h3 className="font-bold text-gray-800 mb-1 capitalize">
                {post.title}
              </h3>
              <p className="text-sm text-gray-600">
                {post.body}
              </p>
            </div>
          ))}
        </div>
      )}

      {/* No Results */}
      {data && data.length === 0 && (
        <div className="text-center py-8">
          <p className="text-gray-500">No results found</p>
        </div>
      )}

      {/* Info */}
      <div className="mt-6 bg-purple-50 border border-purple-200 rounded-lg p-4">
        <h4 className="font-bold text-purple-800 text-sm mb-2">
          🎯 Debounced Search:
        </h4>
        <ul className="text-purple-700 text-sm space-y-1">
          <li>✅ Waits 500ms after you stop typing</li>
          <li>✅ Prevents excessive API calls</li>
          <li>✅ Better performance</li>
          <li>✅ Better user experience</li>
        </ul>
      </div>
    </div>
  );
}
```

**Add to stock page:**

```typescript
import SearchPosts from '@/components/SearchPosts';

// Add after MultiTicker
<div className="mt-8">
  <SearchPosts />
</div>
```

---

## 🧪 Part 6: Testing SWR Features

### Test 1: Auto-Refresh

1. Visit `http://localhost:3000/stock`
2. **Watch the ticker** for 15 seconds
3. Open **DevTools** → **Network** tab
4. **Observe:** New fetch request every 5 seconds

**Expected:** ✅ Auto-refreshes every 5 seconds

---

### Test 2: Manual Refresh

1. Click the **"🔄 Refresh"** button
2. **Watch Network tab**
3. **Observe:** Immediate fetch request

**Expected:** ✅ Manual refresh works instantly

---

### Test 3: Pause/Resume

1. Click **"⏸️ Pause"** button
2. **Wait 10 seconds**
3. **Check Network tab** - no new requests
4. Click **"▶️ Resume"**
5. **Observe:** Requests start again

**Expected:** ✅ Pause stops auto-refresh

---

### Test 4: Revalidate on Focus

1. Visit stock page
2. **Switch to another tab** (or app)
3. **Wait 5 seconds**
4. **Switch back** to stock page
5. **Check Network tab**

**Expected:** ✅ Revalidates when tab gets focus

---

### Test 5: Debounced Search

1. Type **"1"** in search box
2. **Immediately type** "12"
3. **Watch Network tab**

**Expected:** ✅ Only one request after 500ms delay

---

## ✅ Part 7: Success Criteria Verification

### ✅ Component Fetches Data on Client
- Data loads in browser (not server)
- Check Network tab shows fetch
- **Verified:** ✅

### ✅ Loading State Shows While Fetching
- Skeleton shows on first load
- Smooth transition to data
- **Verified:** ✅

### ✅ Auto-Refreshes Every 5 Seconds
- Watch for 15 seconds
- See 3 refresh requests
- **Verified:** ✅

### ✅ Error State Displays on Failure
- Simulate error (change URL)
- Error message shows
- Retry button works
- **Verified:** ✅

---

## 📊 SWR vs useEffect Comparison

### Traditional useEffect (Verbose)

```typescript
function Component() {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    let cancelled = false;
    
    setLoading(true);
    fetch('url')
      .then(r => r.json())
      .then(data => {
        if (!cancelled) {
          setData(data);
          setLoading(false);
        }
      })
      .catch(err => {
        if (!cancelled) {
          setError(err);
          setLoading(false);
        }
      });

    return () => { cancelled = true; };
  }, []);

  // ... render logic
}
```

### SWR (Simple)

```typescript
function Component() {
  const { data, error, isLoading } = useSWR('url', fetcher);
  
  // That's it!
}
```

**Lines of code:**
- useEffect: ~25 lines
- SWR: ~1 line
- **95% less code!**

---

## 🎓 Key Concepts Recap

### 1. **Basic SWR Usage**
```typescript
'use client';
import useSWR from 'swr';

const fetcher = (url) => fetch(url).then(r => r.json());

function Component() {
  const { data, error, isLoading } = useSWR('url', fetcher);
}
```

### 2. **Auto-Revalidation**
```typescript
useSWR('url', fetcher, {
  refreshInterval: 5000,      // Refresh every 5s
  revalidateOnFocus: true,    // Refresh on tab focus
  revalidateOnReconnect: true // Refresh on reconnect
})
```

### 3. **Manual Refresh**
```typescript
const { mutate } = useSWR('url', fetcher);

<button onClick={() => mutate()}>
  Refresh
</button>
```

### 4. **Conditional Fetching**
```typescript
// Only fetch if userId exists
useSWR(userId ? `/api/user/${userId}` : null, fetcher)
```

### 5. **Debouncing**
```typescript
const [term, setTerm] = useState('');
const [debounced, setDebounced] = useState('');

useEffect(() => {
  const timer = setTimeout(() => setDebounced(term), 500);
  return () => clearTimeout(timer);
}, [term]);

useSWR(debounced ? `/search?q=${debounced}` : null, fetcher);
```

---

## 🐛 Common Issues & Solutions

**Problem:** Data not fetching  
**Solution:** Make sure component has `'use client'` at top

**Problem:** Too many requests  
**Solution:** Increase `refreshInterval` or use debouncing

**Problem:** Stale data showing  
**Solution:** Call `mutate()` to force refresh

**Problem:** TypeScript errors  
**Solution:** Type your data: `useSWR<DataType>('url', fetcher)`

---

## 🎉 Congratulations!

You've mastered **Client-Side Fetching with SWR**! You now know:

✅ How to use SWR for client-side data fetching  
✅ Auto-revalidation and caching strategies  
✅ Loading and error states  
✅ Manual refresh with mutate()
✅ Bonus: Debounced search implementation!  

**Your dashboard now has:**
- ⚡ Real-time auto-updating data
- 💾 Smart caching for performance
- 🔄 Manual and automatic refresh
- 🔍 Debounced search (bonus!)
- 📊 Multiple live tickers

  You're officially ready for Day 15: Caching Strategies 🚀
