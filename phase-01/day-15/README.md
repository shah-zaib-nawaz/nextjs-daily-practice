# Day 15: Caching Strategies - Complete Beginner's Guide

Today you'll learn how to **control caching** in Next.js to balance performance and freshness!

---

## 🎯 What You'll Learn Today

- The three main caching strategies
- When to use each caching strategy
- How to implement cache controls
- Time-based revalidation
- On-demand cache invalidation
- Performance optimization techniques

---

## 📚 Part 1: Understanding Caching Strategies (20 minutes)

### What is Caching?

**Caching** means storing data so you don't fetch it again.

**Think of it like a refrigerator:**
- **Static (forever):** Canned goods (never expire) 🥫
- **Revalidate (time-based):** Milk (expires after a week) 🥛
- **Dynamic (never cache):** Fresh coffee (always make new) ☕

---

### The Three Caching Strategies

```
1. STATIC (force-cache) - Default
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
First Visit:  |████████| Fetch → Cache
Second Visit: |█|         Load from cache (instant!)
Third Visit:  |█|         Load from cache (instant!)

✅ Use for: Country lists, product catalogs, static content
⚡ Performance: Fastest (no network request)
🔄 Freshness: Lowest (data never updates)


2. REVALIDATE (time-based)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
0s:   |████████| Fetch → Cache
30s:  |█|         Load from cache
60s:  |████████| Revalidate → Update cache
90s:  |█|         Load from NEW cache

✅ Use for: News, stock prices, weather, user data
⚡ Performance: Good (cached most of the time)
🔄 Freshness: Medium (updates periodically)


3. DYNAMIC (no-store)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
First Visit:  |████████| Fetch (no cache)
Second Visit: |████████| Fetch again (no cache)
Third Visit:  |████████| Fetch again (no cache)

✅ Use for: Live data, personalized content, real-time updates
⚡ Performance: Slowest (always fetches)
🔄 Freshness: Highest (always current)
```

---

## 🔍 Part 2: Cache Control Syntax

### Method 1: Fetch Options
```typescript
// Static (cached forever)
fetch('url')  // Default behavior

// Dynamic (never cached)
fetch('url', { cache: 'no-store' })

// Revalidate (time-based)
fetch('url', { next: { revalidate: 60 } })
```

### Method 2: Route Segment Config
```typescript
// In page.tsx or layout.tsx
export const revalidate = 60;  // Revalidate every 60 seconds
export const dynamic = 'force-dynamic';  // Never cache
export const fetchCache = 'force-cache';  // Cache forever
```

---

## 💻 Part 3: Building the Weather App

### Your Project Structure

```
app/
├── static/
│   └── page.tsx              ← Cached forever
├── dynamic/
│   └── page.tsx              ← Never cached
├── revalidate/
│   └── page.tsx              ← Time-based cache
└── cache-demo/
    └── page.tsx              ← Comparison page
```

---

### Step 1: Create Static Page (Cached Forever)

**Create file:** `app/static/page.tsx`

```typescript
// This page's data is cached forever (at build time)
async function getCountries() {
  console.log('🌍 Fetching countries... (should only see this once)');
  
  const response = await fetch('https://restcountries.com/v3.1/all');
  
  if (!response.ok) {
    throw new Error('Failed to fetch countries');
  }
  
  return response.json();
}

export default async function StaticPage() {
  const countries = await getCountries();
  
  // Get a random sample of countries to display
  const displayCountries = countries
    .sort(() => Math.random() - 0.5)
    .slice(0, 12);

  return (
    <div className="min-h-screen bg-gray-50 py-12 px-4">
      <div className="max-w-6xl mx-auto">
        {/* Header */}
        <div className="mb-8">
          
            href="/"
            className="inline-flex items-center gap-2 px-4 py-2 bg-white rounded-lg shadow hover:shadow-md transition mb-6 font-semibold text-gray-700"
          >
            ← Back to Home
          </a>
          <h1 className="text-5xl font-bold text-gray-800 mb-4">
            🌍 Static Page
          </h1>
          <p className="text-xl text-gray-600">
            Data cached forever (force-cache strategy)
          </p>
        </div>

        {/* Cache Info */}
        <div className="bg-blue-50 border-l-4 border-blue-600 p-6 mb-8 rounded">
          <h3 className="text-lg font-bold text-blue-800 mb-2">
            ♾️ Cached Forever
          </h3>
          <ul className="space-y-2 text-blue-700">
            <li>✅ Data fetched once at build time</li>
            <li>✅ Subsequent visits use cached data</li>
            <li>✅ Zero network requests after first load</li>
            <li>✅ Fastest possible performance</li>
            <li>⚠️ Data never updates (until rebuild)</li>
          </ul>
        </div>

        {/* Test Instructions */}
        <div className="bg-yellow-50 border-l-4 border-yellow-500 p-6 mb-8 rounded">
          <h3 className="text-lg font-bold text-yellow-800 mb-2">
            🧪 Test This:
          </h3>
          <ol className="list-decimal list-inside space-y-2 text-yellow-700">
            <li>Note the current time: {new Date().toLocaleTimeString()}</li>
            <li>Refresh the page multiple times</li>
            <li>Check your browser console - "Fetching countries" only appears once</li>
            <li>The page loads instantly after first visit</li>
            <li>The list order stays the same (cached)</li>
          </ol>
        </div>

        {/* Stats */}
        <div className="grid md:grid-cols-3 gap-6 mb-8">
          <div className="bg-white rounded-lg shadow-lg p-6 text-center">
            <div className="text-4xl mb-2">🌐</div>
            <p className="text-3xl font-bold text-blue-600">{countries.length}</p>
            <p className="text-gray-600 text-sm">Total Countries</p>
          </div>
          <div className="bg-white rounded-lg shadow-lg p-6 text-center">
            <div className="text-4xl mb-2">⚡</div>
            <p className="text-3xl font-bold text-green-600">~10ms</p>
            <p className="text-gray-600 text-sm">Load Time (cached)</p>
          </div>
          <div className="bg-white rounded-lg shadow-lg p-6 text-center">
            <div className="text-4xl mb-2">🔄</div>
            <p className="text-3xl font-bold text-purple-600">Never</p>
            <p className="text-gray-600 text-sm">Revalidation</p>
          </div>
        </div>

        {/* Countries Grid */}
        <div>
          <h2 className="text-2xl font-bold text-gray-800 mb-4">
            Sample Countries
          </h2>
          <div className="grid md:grid-cols-3 lg:grid-cols-4 gap-4">
            {displayCountries.map((country: any) => (
              <div
                key={country.cca3}
                className="bg-white rounded-lg shadow hover:shadow-lg transition p-4"
              >
                {/* Flag */}
                <div className="text-6xl text-center mb-3">
                  {country.flag}
                </div>
                
                {/* Country Info */}
                <h3 className="font-bold text-gray-800 text-center mb-1">
                  {country.name.common}
                </h3>
                <p className="text-sm text-gray-600 text-center">
                  {country.region}
                </p>
                
                {/* Population */}
                <div className="mt-3 pt-3 border-t border-gray-200 text-center">
                  <p className="text-xs text-gray-500">Population</p>
                  <p className="font-semibold text-gray-700">
                    {country.population.toLocaleString()}
                  </p>
                </div>
              </div>
            ))}
          </div>
        </div>

        {/* Cache Explanation */}
        <div className="mt-12 bg-white rounded-lg shadow-lg p-8">
          <h2 className="text-2xl font-bold text-gray-800 mb-4">
            💡 How Static Caching Works
          </h2>
          <div className="space-y-4 text-gray-700">
            <p>
              This page uses the <strong>default caching behavior</strong> in Next.js.
              The fetch request has no cache options, so it uses <code className="bg-gray-100 px-2 py-1 rounded">force-cache</code> by default.
            </p>
            <div className="bg-gray-900 rounded-lg p-4 font-mono text-sm text-green-400">
              <code>{`async function getCountries() {
  // No cache options = force-cache (default)
  const response = await fetch('https://restcountries.com/v3.1/all');
  return response.json();
}`}</code>
            </div>
            <p>
              <strong>Best for:</strong> Data that rarely or never changes, like:
            </p>
            <ul className="list-disc list-inside space-y-1 ml-4">
              <li>Country lists</li>
              <li>Product catalogs</li>
              <li>Documentation</li>
              <li>Blog posts</li>
            </ul>
          </div>
        </div>
      </div>
    </div>
  );
}
```

---

### Step 2: Create Dynamic Page (Never Cached)

**Create file:** `app/dynamic/page.tsx`

```typescript
// Mark route as dynamic (never cached)
export const dynamic = 'force-dynamic';

async function getCurrentTime() {
  console.log('🕐 Fetching current time... (should see this every refresh)');
  
  const response = await fetch(
    'https://worldtimeapi.org/api/timezone/America/New_York',
    { cache: 'no-store' }  // Never cache this request
  );
  
  if (!response.ok) {
    throw new Error('Failed to fetch time');
  }
  
  return response.json();
}

export default async function DynamicPage() {
  const timeData = await getCurrentTime();
  const currentTime = new Date(timeData.datetime);

  return (
    <div className="min-h-screen bg-gray-50 py-12 px-4">
      <div className="max-w-4xl mx-auto">
        {/* Header */}
        <div className="mb-8">
          
            href="/"
            className="inline-flex items-center gap-2 px-4 py-2 bg-white rounded-lg shadow hover:shadow-md transition mb-6 font-semibold text-gray-700"
          >
            ← Back to Home
          </a>
          <h1 className="text-5xl font-bold text-gray-800 mb-4">
            🕐 Dynamic Page
          </h1>
          <p className="text-xl text-gray-600">
            Data never cached (no-store strategy)
          </p>
        </div>

        {/* Cache Info */}
        <div className="bg-red-50 border-l-4 border-red-600 p-6 mb-8 rounded">
          <h3 className="text-lg font-bold text-red-800 mb-2">
            🔴 Never Cached
          </h3>
          <ul className="space-y-2 text-red-700">
            <li>✅ Data fetched fresh on every request</li>
            <li>✅ Always shows current data</li>
            <li>✅ No stale data possible</li>
            <li>⚠️ Slower load times (network request each time)</li>
            <li>⚠️ Higher server load</li>
          </ul>
        </div>

        {/* Test Instructions */}
        <div className="bg-yellow-50 border-l-4 border-yellow-500 p-6 mb-8 rounded">
          <h3 className="text-lg font-bold text-yellow-800 mb-2">
            🧪 Test This:
          </h3>
          <ol className="list-decimal list-inside space-y-2 text-yellow-700">
            <li>Note the exact time shown below</li>
            <li>Refresh the page</li>
            <li>Check your browser console - "Fetching current time" appears every time</li>
            <li>The time updates with each refresh</li>
            <li>The page takes a moment to load each time (no caching)</li>
          </ol>
        </div>

        {/* Current Time Display */}
        <div className="bg-gradient-to-br from-red-500 to-pink-500 rounded-lg shadow-2xl p-12 text-white text-center mb-8">
          <h2 className="text-2xl font-semibold mb-6 opacity-90">
            Current Time in New York
          </h2>
          <div className="text-7xl font-bold mb-6 font-mono">
            {currentTime.toLocaleTimeString('en-US', {
              hour: '2-digit',
              minute: '2-digit',
              second: '2-digit',
              hour12: true,
            })}
          </div>
          <p className="text-xl opacity-90">
            {currentTime.toLocaleDateString('en-US', {
              weekday: 'long',
              year: 'numeric',
              month: 'long',
              day: 'numeric',
            })}
          </p>
        </div>

        {/* Time Details */}
        <div className="grid md:grid-cols-2 gap-6 mb-8">
          <div className="bg-white rounded-lg shadow-lg p-6">
            <h3 className="font-bold text-gray-800 mb-4 text-lg">
              ⏰ Time Details
            </h3>
            <div className="space-y-3 text-gray-700">
              <div className="flex justify-between">
                <span>Timezone:</span>
                <span className="font-semibold">{timeData.timezone}</span>
              </div>
              <div className="flex justify-between">
                <span>UTC Offset:</span>
                <span className="font-semibold">{timeData.utc_offset}</span>
              </div>
              <div className="flex justify-between">
                <span>Day of Week:</span>
                <span className="font-semibold">{timeData.day_of_week}</span>
              </div>
              <div className="flex justify-between">
                <span>Day of Year:</span>
                <span className="font-semibold">{timeData.day_of_year}</span>
              </div>
              <div className="flex justify-between">
                <span>Week Number:</span>
                <span className="font-semibold">{timeData.week_number}</span>
              </div>
            </div>
          </div>

          <div className="bg-white rounded-lg shadow-lg p-6">
            <h3 className="font-bold text-gray-800 mb-4 text-lg">
              📊 Request Info
            </h3>
            <div className="space-y-3 text-gray-700">
              <div className="flex justify-between">
                <span>Cache Strategy:</span>
                <span className="font-semibold text-red-600">no-store</span>
              </div>
              <div className="flex justify-between">
                <span>Cached:</span>
                <span className="font-semibold text-red-600">No</span>
              </div>
              <div className="flex justify-between">
                <span>Revalidation:</span>
                <span className="font-semibold">Every Request</span>
              </div>
              <div className="flex justify-between">
                <span>Freshness:</span>
                <span className="font-semibold text-green-600">100%</span>
              </div>
            </div>
          </div>
        </div>

        {/* Cache Explanation */}
        <div className="bg-white rounded-lg shadow-lg p-8">
          <h2 className="text-2xl font-bold text-gray-800 mb-4">
            💡 How Dynamic Caching Works
          </h2>
          <div className="space-y-4 text-gray-700">
            <p>
              This page uses <code className="bg-gray-100 px-2 py-1 rounded">cache: 'no-store'</code> 
              to prevent caching. Every request fetches fresh data.
            </p>
            <div className="bg-gray-900 rounded-lg p-4 font-mono text-sm text-green-400">
              <code>{`async function getCurrentTime() {
  const response = await fetch('url', {
    cache: 'no-store'  // Never cache
  });
  return response.json();
}`}</code>
            </div>
            <p>
              <strong>Best for:</strong> Data that must always be current:
            </p>
            <ul className="list-disc list-inside space-y-1 ml-4">
              <li>Current time/date</li>
              <li>Live stock prices</li>
              <li>Real-time sports scores</li>
              <li>User-specific data</li>
              <li>Shopping cart contents</li>
            </ul>
          </div>
        </div>

        {/* Refresh Button */}
        <div className="mt-8 text-center">
          <button
            onClick={() => window.location.reload()}
            className="px-8 py-4 bg-red-600 text-white rounded-lg hover:bg-red-700 transition font-bold text-lg shadow-lg"
          >
            🔄 Refresh to Get Latest Time
          </button>
        </div>
      </div>
    </div>
  );
}
```

---

### Step 3: Create Revalidate Page (Time-Based Cache)

**Create file:** `app/revalidate/page.tsx`

```typescript
// Page-level revalidation
export const revalidate = 60; // Revalidate every 60 seconds

async function getQuote() {
  console.log('💬 Fetching quote... (should see this every 60 seconds)');
  
  const response = await fetch('https://api.quotable.io/random', {
    next: { revalidate: 60 }  // Revalidate after 60 seconds
  });
  
  if (!response.ok) {
    throw new Error('Failed to fetch quote');
  }
  
  return response.json();
}

async function getJoke() {
  console.log('😄 Fetching joke...');
  
  const response = await fetch('https://official-joke-api.appspot.com/random_joke', {
    next: { revalidate: 60 }
  });
  
  if (!response.ok) {
    throw new Error('Failed to fetch joke');
  }
  
  return response.json();
}

export default async function RevalidatePage() {
  // Fetch both in parallel
  const [quote, joke] = await Promise.all([
    getQuote(),
    getJoke(),
  ]);

  const now = new Date();
  const cacheExpiresAt = new Date(now.getTime() + 60000); // +60 seconds

  return (
    <div className="min-h-screen bg-gray-50 py-12 px-4">
      <div className="max-w-4xl mx-auto">
        {/* Header */}
        <div className="mb-8">
          
            href="/"
            className="inline-flex items-center gap-2 px-4 py-2 bg-white rounded-lg shadow hover:shadow-md transition mb-6 font-semibold text-gray-700"
          >
            ← Back to Home
          </a>
          <h1 className="text-5xl font-bold text-gray-800 mb-4">
            ⏱️ Revalidate Page
          </h1>
          <p className="text-xl text-gray-600">
            Time-based cache (revalidates every 60 seconds)
          </p>
        </div>

        {/* Cache Info */}
        <div className="bg-green-50 border-l-4 border-green-600 p-6 mb-8 rounded">
          <h3 className="text-lg font-bold text-green-800 mb-2">
            🔄 Time-Based Revalidation
          </h3>
          <ul className="space-y-2 text-green-700">
            <li>✅ Data cached for 60 seconds</li>
            <li>✅ Serves cached data instantly (within 60s)</li>
            <li>✅ Automatically refetches after 60 seconds</li>
            <li>✅ Balance between speed and freshness</li>
            <li>⚡ Best of both worlds!</li>
          </ul>
        </div>

        {/* Cache Timer */}
        <div className="bg-purple-50 border-l-4 border-purple-600 p-6 mb-8 rounded">
          <h3 className="text-lg font-bold text-purple-800 mb-3">
            ⏰ Cache Status
          </h3>
          <div className="space-y-2 text-purple-700">
            <p>
              <strong>Page generated at:</strong> {now.toLocaleTimeString()}
            </p>
            <p>
              <strong>Cache expires at:</strong> {cacheExpiresAt.toLocaleTimeString()}
            </p>
            <p className="text-sm mt-3">
              💡 Refresh within 60 seconds → instant load (cached)
              <br />
              💡 Refresh after 60 seconds → revalidates (fetches new data)
            </p>
          </div>
        </div>

        {/* Test Instructions */}
        <div className="bg-yellow-50 border-l-4 border-yellow-500 p-6 mb-8 rounded">
          <h3 className="text-lg font-bold text-yellow-800 mb-2">
            🧪 Test This:
          </h3>
          <ol className="list-decimal list-inside space-y-2 text-yellow-700">
            <li>Note the quote and joke below</li>
            <li>Refresh immediately → Same content (cached)</li>
            <li>Wait 60 seconds</li>
            <li>Refresh again → New quote and joke!</li>
            <li>Check console → Only logs after 60 seconds</li>
          </ol>
        </div>

        {/* Quote Display */}
        <div className="bg-gradient-to-br from-blue-500 to-purple-500 rounded-lg shadow-2xl p-8 text-white mb-8">
          <div className="text-6xl mb-4 text-center">💭</div>
          <blockquote className="text-2xl font-serif italic text-center mb-6">
            "{quote.content}"
          </blockquote>
          <p className="text-right text-lg opacity-90">
            — {quote.author}
          </p>
        </div>

        {/* Joke Display */}
        <div className="bg-white rounded-lg shadow-lg p-8 mb-8">
          <div className="text-6xl mb-4 text-center">😄</div>
          <h3 className="text-2xl font-bold text-gray-800 text-center mb-4">
            {joke.setup}
          </h3>
          <p className="text-xl text-gray-600 text-center">
            {joke.punchline}
          </p>
        </div>

        {/* Stats */}
        <div className="grid md:grid-cols-3 gap-6 mb-8">
          <div className="bg-white rounded-lg shadow-lg p-6 text-center">
            <div className="text-4xl mb-2">⚡</div>
            <p className="text-3xl font-bold text-green-600">~10ms</p>
            <p className="text-gray-600 text-sm">Load Time (cached)</p>
          </div>
          <div className="bg-white rounded-lg shadow-lg p-6 text-center">
            <div className="text-4xl mb-2">🔄</div>
            <p className="text-3xl font-bold text-blue-600">60s</p>
            <p className="text-gray-600 text-sm">Revalidation Period</p>
          </div>
          <div className="bg-white rounded-lg shadow-lg p-6 text-center">
            <div className="text-4xl mb-2">📊</div>
            <p className="text-3xl font-bold text-purple-600">Balanced</p>
            <p className="text-gray-600 text-sm">Speed vs Freshness</p>
          </div>
        </div>

        {/* Cache Explanation */}
        <div className="bg-white rounded-lg shadow-lg p-8">
          <h2 className="text-2xl font-bold text-gray-800 mb-4">
            💡 How Time-Based Revalidation Works
          </h2>
          <div className="space-y-4 text-gray-700">
            <p>
              This page uses <code className="bg-gray-100 px-2 py-1 rounded">next: {'{ revalidate: 60 }'}</code> 
              to cache data for 60 seconds, then automatically refresh it.
            </p>
            <div className="bg-gray-900 rounded-lg p-4 font-mono text-sm text-green-400 overflow-x-auto">
              <code>{`// Page-level revalidation
export const revalidate = 60;

// Or fetch-level
async function getData() {
  const response = await fetch('url', {
    next: { revalidate: 60 }
  });
  return response.json();
}`}</code>
            </div>
            <p>
              <strong>Best for:</strong> Data that changes periodically:
            </p>
            <ul className="list-disc list-inside space-y-1 ml-4">
              <li>News articles (revalidate: 300 = 5 minutes)</li>
              <li>Weather data (revalidate: 600 = 10 minutes)</li>
              <li>Stock prices (revalidate: 60 = 1 minute)</li>
              <li>User profiles (revalidate: 3600 = 1 hour)</li>
              <li>Blog posts (revalidate: 86400 = 1 day)</li>
            </ul>
          </div>
        </div>
      </div>
    </div>
  );
}
```

---

### Step 4: Create Comparison Homepage

**Create/Update file:** `app/page.tsx`

```typescript
export default function HomePage() {
  return (
    <div className="min-h-screen bg-gradient-to-br from-blue-500 to-purple-600 py-12 px-4">
      <div className="max-w-6xl mx-auto">
        {/* Header */}
        <div className="text-center text-white mb-12">
          <h1 className="text-6xl font-bold mb-4">
            💾 Caching Strategies
          </h1>
          <p className="text-2xl opacity-90">
            Learn how to control data caching in Next.js
          </p>
        </div>

        {/* Strategy Cards */}
        <div className="grid md:grid-cols-3 gap-6 mb-12">
          {/* Static */}
          
            href="/static"
            className="block bg-white rounded-lg shadow-2xl p-8 hover:scale-105 transition-transform"
          >
            <div className="text-6xl mb-4 text-center">♾️</div>
            <h2 className="text-2xl font-bold text-gray-800 mb-3 text-center">
              Static
            </h2>
            <p className="text-gray-600 mb-4 text-center">
              Cached Forever
            </p>
            <div className="space-y-2 text-sm text-gray-700">
              <p>✅ Fastest performance</p>
              <p>✅ Zero network requests</p>
              <p>⚠️ Data never updates</p>
            </div>
            <div className="mt-6 pt-6 border-t border-gray-200">
              <p className="text-xs font-mono text-gray-500">
                force-cache (default)
              </p>
            </div>
          </a>

          {/* Revalidate */}
          
            href="/revalidate"
            className="block bg-white rounded-lg shadow-2xl p-8 hover:scale-105 transition-transform"
          >
            <div className="text-6xl mb-4 text-center">⏱️</div>
            <h2 className="text-2xl font-bold text-gray-800 mb-3 text-center">
              Revalidate
            </h2>
            <p className="text-gray-600 mb-4 text-center">
              Time-Based Cache
            </p>
            <div className="space-y-2 text-sm text-gray-700">
              <p>✅ Good performance</p>
              <p>✅ Periodic updates</p>
              <p>✅ Best balance</p>
            </div>
            <div className="mt-6 pt-6 border-t border-gray-200">
              <p className="text-xs font-mono text-gray-500">
                revalidate: 60
              </p>
            </div>
          </a>

          {/* Dynamic */}
          
            href="/dynamic"
            className="block bg-white rounded-lg shadow-2xl p-8 hover:scale-105 transition-transform"
          >
            <div className="text-6xl mb-4 text-center">🔴</div><h2 className="text-2xl font-bold text-gray-800 mb-3 text-center">
              Dynamic
            </h2>
            <p className="text-gray-600 mb-4 text-center">
              Never Cached
            </p>
            <div className="space-y-2 text-sm text-gray-700">
              <p>✅ Always fresh data</p>
              <p>⚠️ Slower performance</p>
              <p>⚠️ More server load</p>
            </div>
            <div className="mt-6 pt-6 border-t border-gray-200">
              <p className="text-xs font-mono text-gray-500">
                cache: 'no-store'
              </p>
            </div>
          </a>
        </div>

        {/* Comparison Table */}
        <div className="bg-white rounded-lg shadow-2xl p-8 mb-12">
          <h2 className="text-3xl font-bold text-gray-800 mb-6">
            📊 Strategy Comparison
          </h2>
          <div className="overflow-x-auto">
            <table className="w-full text-left">
              <thead>
                <tr className="border-b-2 border-gray-300">
                  <th className="pb-3 pr-4">Feature</th>
                  <th className="pb-3 px-4 text-center">Static</th>
                  <th className="pb-3 px-4 text-center">Revalidate</th>
                  <th className="pb-3 px-4 text-center">Dynamic</th>
                </tr>
              </thead>
              <tbody className="text-gray-700">
                <tr className="border-b border-gray-200">
                  <td className="py-3 pr-4 font-semibold">Speed</td>
                  <td className="py-3 px-4 text-center">🟢 Fastest</td>
                  <td className="py-3 px-4 text-center">🟡 Fast</td>
                  <td className="py-3 px-4 text-center">🔴 Slow</td>
                </tr>
                <tr className="border-b border-gray-200">
                  <td className="py-3 pr-4 font-semibold">Freshness</td>
                  <td className="py-3 px-4 text-center">🔴 Never</td>
                  <td className="py-3 px-4 text-center">🟡 Periodic</td>
                  <td className="py-3 px-4 text-center">🟢 Always</td>
                </tr>
                <tr className="border-b border-gray-200">
                  <td className="py-3 pr-4 font-semibold">Server Load</td>
                  <td className="py-3 px-4 text-center">🟢 Minimal</td>
                  <td className="py-3 px-4 text-center">🟡 Low</td>
                  <td className="py-3 px-4 text-center">🔴 High</td>
                </tr>
                <tr className="border-b border-gray-200">
                  <td className="py-3 pr-4 font-semibold">Use Case</td>
                  <td className="py-3 px-4 text-center text-sm">Rarely changes</td>
                  <td className="py-3 px-4 text-center text-sm">Changes often</td>
                  <td className="py-3 px-4 text-center text-sm">Real-time</td>
                </tr>
                <tr>
                  <td className="py-3 pr-4 font-semibold">Example</td>
                  <td className="py-3 px-4 text-center text-sm">Countries</td>
                  <td className="py-3 px-4 text-center text-sm">News/Weather</td>
                  <td className="py-3 px-4 text-center text-sm">Live clock</td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>

        {/* Decision Tree */}
        <div className="bg-white rounded-lg shadow-2xl p-8">
          <h2 className="text-3xl font-bold text-gray-800 mb-6">
            🤔 Which Strategy Should I Use?
          </h2>
          <div className="space-y-4">
            <div className="flex items-start gap-4">
              <div className="flex-shrink-0 w-8 h-8 bg-blue-100 rounded-full flex items-center justify-center text-blue-600 font-bold">
                ?
              </div>
              <div>
                <p className="font-bold text-gray-800 mb-1">
                  Does your data change frequently?
                </p>
                <p className="text-gray-600 text-sm">
                  <strong>No</strong> → Use <span className="text-blue-600 font-mono">Static</span>
                  <br />
                  <strong>Yes</strong> → Continue...
                </p>
              </div>
            </div>

            <div className="flex items-start gap-4">
              <div className="flex-shrink-0 w-8 h-8 bg-green-100 rounded-full flex items-center justify-center text-green-600 font-bold">
                ?
              </div>
              <div>
                <p className="font-bold text-gray-800 mb-1">
                  Must data be 100% real-time?
                </p>
                <p className="text-gray-600 text-sm">
                  <strong>Yes</strong> → Use <span className="text-red-600 font-mono">Dynamic</span>
                  <br />
                  <strong>No</strong> → Continue...
                </p>
              </div>
            </div>

            <div className="flex items-start gap-4">
              <div className="flex-shrink-0 w-8 h-8 bg-purple-100 rounded-full flex items-center justify-center text-purple-600 font-bold">
                ✓
              </div>
              <div>
                <p className="font-bold text-gray-800 mb-1">
                  Use Revalidate!
                </p>
                <p className="text-gray-600 text-sm">
                  Set an appropriate revalidation time:
                  <br />
                  • 60s for frequently changing data
                  <br />
                  • 300s (5min) for moderate updates
                  <br />
                  • 3600s (1hr) for slow changes
                </p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  );
}
```

---

## 🧪 Part 4: Testing Caching Strategies

### Test 1: Static Page

1. Visit `http://localhost:3000/static`
2. Note the countries displayed
3. **Refresh 5 times**
4. **Check console** - "Fetching countries" appears only once
5. **Check Network tab** - No new requests after first

**Expected:** ✅ Instant loads after first visit

---

### Test 2: Dynamic Page

1. Visit `http://localhost:3000/dynamic`
2. Note the exact time
3. **Refresh immediately**
4. **Check console** - "Fetching current time" appears every time
5. Time updates with each refresh

**Expected:** ✅ Fresh data every request

---

### Test 3: Revalidate Page

1. Visit `http://localhost:3000/revalidate`
2. Note the quote and joke
3. **Refresh within 60 seconds** → Same content (cached)
4. **Wait 61 seconds**
5. **Refresh** → New quote and joke!

**Expected:** ✅ Updates after revalidation period

---

## ✅ Part 5: Success Criteria Verification

### ✅ Static Page Loads Instantly on Second Visit
- First visit: Fetches data
- Second visit: < 10ms (cached)
- **Verified:** ✅

### ✅ Dynamic Page Fetches Fresh Data Every Time
- Every refresh: New fetch request
- Time always current
- **Verified:** ✅

### ✅ Revalidate Page Updates After Set Time
- Within 60s: Uses cache
- After 60s: Refetches
- **Verified:** ✅

### ✅ Each Strategy Works as Expected
- Static: Fastest, never updates
- Dynamic: Slowest, always fresh
- Revalidate: Balanced approach
- **Verified:** ✅

---

## 🌟 Bonus Challenge: On-Demand Revalidation

### Step 5: Add Cache Tags

**Create file:** `app/api/revalidate/route.ts`

```typescript
import { revalidateTag, revalidatePath } from 'next/cache';
import { NextRequest, NextResponse } from 'next/server';

export async function POST(request: NextRequest) {
  const { tag, path } = await request.json();

  try {
    if (tag) {
      // Revalidate by cache tag
      revalidateTag(tag);
      return NextResponse.json({ 
        revalidated: true, 
        tag,
        now: Date.now() 
      });
    }

    if (path) {
      // Revalidate by path
      revalidatePath(path);
      return NextResponse.json({ 
        revalidated: true, 
        path,
        now: Date.now() 
      });
    }

    return NextResponse.json({ 
      revalidated: false, 
      error: 'Missing tag or path' 
    }, { status: 400 });
    
  } catch (error) {
    return NextResponse.json({ 
      revalidated: false, 
      error: 'Error revalidating' 
    }, { status: 500 });
  }
}
```

**Usage:**

```bash
# Revalidate by path
curl -X POST http://localhost:3000/api/revalidate \
  -H "Content-Type: application/json" \
  -d '{"path": "/revalidate"}'

# Revalidate by tag
curl -X POST http://localhost:3000/api/revalidate \
  -H "Content-Type: application/json" \
  -d '{"tag": "quotes"}'
```

---

## 📊 Performance Impact

### Load Time Comparison

```
STATIC (force-cache):
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
First Visit:   |████████████| 1200ms
Second Visit:  |█|             10ms   (120x faster!)
Third Visit:   |█|             10ms


REVALIDATE (time-based):
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
First Visit:   |████████████| 1200ms
Within 60s:    |█|             10ms
After 60s:     |████████████| 1200ms (refetch)
Next 60s:      |█|             10ms


DYNAMIC (no-store):
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Every Visit:   |████████████| 1200ms (always slow)
```

---

## 🎓 Key Concepts Recap

### 1. **Static (Default)**
```typescript
// No cache options = cached forever
fetch('url')
```

### 2. **Dynamic (Never Cache)**
```typescript
// Fetch-level
fetch('url', { cache: 'no-store' })

// Page-level
export const dynamic = 'force-dynamic';
```

### 3. **Revalidate (Time-Based)**
```typescript
// Fetch-level
fetch('url', { next: { revalidate: 60 } })

// Page-level
export const revalidate = 60;
```

### 4. **On-Demand Revalidation**
```typescript
import { revalidatePath, revalidateTag } from 'next/cache';

// Revalidate specific path
revalidatePath('/blog');

// Revalidate by tag
revalidateTag('posts');
```

---

## 🐛 Common Issues & Solutions

**Problem:** Page not caching  
**Solution:** Check you're not using `cache: 'no-store'` accidentally

**Problem:** Cache not updating  
**Solution:** Wait for revalidation period or use on-demand revalidation

**Problem:** Too frequent refetching  
**Solution:** Increase revalidate time (60 → 300)

**Problem:** Stale data showing  
**Solution:** Use lower revalidate time or dynamic caching

---

## 🎉 Congratulations!

You've mastered **Caching Strategies**! You now know:

✅ The three main caching strategies  
✅ When to use each strategy  
✅ How to implement cache controls  
✅ Time-based revalidation  
✅ Bonus: On-demand cache invalidation!  

**Your app now has:**
- ♾️ Static pages (fastest possible)
- ⏱️ Revalidated pages (balanced)
- 🔴 Dynamic pages (always fresh)
- 🎯 Perfect caching for each use case

  You're officially ready for Day 16: Suspense & Streaming 🚀
