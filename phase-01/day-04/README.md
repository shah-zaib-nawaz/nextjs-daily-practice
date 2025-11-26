# Day 4: Layouts - Complete Beginner's Guide

Today you'll learn how to create **reusable layouts** that wrap multiple pages, eliminating code repetition!

---

## 🎯 What You'll Learn Today

- What layouts are and why they're powerful
- How to create a root layout for all pages
- How to create nested layouts for specific sections
- How to build navigation that highlights active links

---

## 📚 Part 1: Understanding Layouts (20 minutes)

### What is a Layout?

A **layout** is a component that wraps around your pages and provides shared UI elements.

**Think of it like a picture frame:**
- The **frame** (layout) stays the same
- The **picture** (page content) changes

### The Problem Without Layouts

Imagine you have 10 pages and want a navigation bar on each:

```typescript
// page1.tsx
<div>
  <nav>Home | About | Contact</nav>  ← Repeated!
  <h1>Page 1</h1>
</div>

// page2.tsx
<div>
  <nav>Home | About | Contact</nav>  ← Repeated!
  <h1>Page 2</h1>
</div>

// page3.tsx
<div>
  <nav>Home | About | Contact</nav>  ← Repeated!
  <h1>Page 3</h1>
</div>
```

**Problems:**
- Code repetition (DRY principle violation)
- Hard to update (change nav = update 10 files!)
- Prone to inconsistencies

### The Solution: Layouts

```typescript
// layout.tsx (ONE file!)
<div>
  <nav>Home | About | Contact</nav>
  {children}  ← Each page goes here
</div>

// page1.tsx
<h1>Page 1</h1>  ← Just the content!

// page2.tsx
<h1>Page 2</h1>  ← Just the content!
```

**Benefits:**
- Write once, use everywhere
- Change once, updates all pages
- Cleaner, more maintainable code

---

## 🏗️ Part 2: Understanding Layout Hierarchy

Next.js supports **nested layouts**:

```
Root Layout (app/layout.tsx)
├── Wraps EVERYTHING
│
├── Home Page (/)
│
├── Shop Layout (app/shop/layout.tsx)
│   ├── Wraps only /shop pages
│   ├── Shop Home (/shop)
│   ├── Products (/shop/products)
│   └── Cart (/shop/cart)
│
└── Dashboard Layout (app/dashboard/layout.tsx)
    ├── Wraps only /dashboard pages
    └── Dashboard Home (/dashboard)
```

**Key Concept:** Layouts stack like Russian dolls! 🪆

For `/shop/products`:
```
Root Layout
  └── Shop Layout
      └── Products Page
```

---

## 💻 Part 3: Creating the Root Layout

### Step 1: Update Root Layout

**File:** `app/layout.tsx`

This file should already exist. Replace it completely:

```typescript
import type { Metadata } from 'next';
import './globals.css';

export const metadata: Metadata = {
  title: 'My Multi-Section Website',
  description: 'Learning Next.js Layouts',
};

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body>
        {/* Header with Navigation - Shows on ALL pages */}
        <header className="bg-blue-600 text-white shadow-lg">
          <div className="max-w-7xl mx-auto px-4 py-4">
            <div className="flex items-center justify-between">
              {/* Logo */}
              <div className="text-2xl font-bold">
                🏪 MyStore
              </div>

              {/* Navigation */}
              <nav>
                <ul className="flex gap-6">
                  <li>
                    <a 
                      href="/" 
                      className="hover:text-blue-200 transition font-semibold"
                    >
                      Home
                    </a>
                  </li>
                  <li>
                    <a 
                      href="/shop" 
                      className="hover:text-blue-200 transition font-semibold"
                    >
                      Shop
                    </a>
                  </li>
                  <li>
                    <a 
                      href="/dashboard" 
                      className="hover:text-blue-200 transition font-semibold"
                    >
                      Dashboard
                    </a>
                  </li>
                </ul>
              </nav>
            </div>
          </div>
        </header>

        {/* Main Content - This is where page content goes */}
        <main className="min-h-screen">
          {children}
        </main>

        {/* Footer - Shows on ALL pages */}
        <footer className="bg-gray-800 text-white">
          <div className="max-w-7xl mx-auto px-4 py-8">
            <div className="text-center">
              <p className="text-lg mb-2">
                © 2024 MyStore. All rights reserved.
              </p>
              <p className="text-gray-400 text-sm">
                Built with Next.js 16 | Learning Layouts
              </p>
            </div>
          </div>
        </footer>
      </body>
    </html>
  );
}
```

**Understanding the Code:**

1. **Line 10:** `children` prop contains the page content
2. **Lines 17-51:** Header with navigation (shows on ALL pages)
3. **Line 54:** `{children}` renders the specific page content
4. **Lines 58-69:** Footer (shows on ALL pages)

**The magic:** Every page automatically gets wrapped with header + footer!

---

## 🏠 Part 4: Creating the Homepage

### Step 2: Create/Update Homepage

**File:** `app/page.tsx`

```typescript
export default function HomePage() {
  return (
    <div className="max-w-7xl mx-auto px-4 py-12">
      {/* Hero Section */}
      <div className="text-center mb-16">
        <h1 className="text-6xl font-bold text-gray-800 mb-4">
          Welcome to MyStore
        </h1>
        <p className="text-xl text-gray-600 mb-8">
          Your one-stop shop for everything you need
        </p>
      </div>

      {/* Feature Cards */}
      <div className="grid md:grid-cols-3 gap-8">
        {/* Card 1: Shop */}
        <a 
          href="/shop"
          className="group block p-8 bg-white rounded-xl shadow-lg hover:shadow-2xl transition transform hover:-translate-y-1"
        >
          <div className="text-5xl mb-4">🛍️</div>
          <h2 className="text-2xl font-bold text-gray-800 mb-2 group-hover:text-blue-600 transition">
            Browse Shop
          </h2>
          <p className="text-gray-600">
            Explore our amazing collection of products
          </p>
        </a>

        {/* Card 2: Products */}
        <a 
          href="/shop/products"
          className="group block p-8 bg-white rounded-xl shadow-lg hover:shadow-2xl transition transform hover:-translate-y-1"
        >
          <div className="text-5xl mb-4">📦</div>
          <h2 className="text-2xl font-bold text-gray-800 mb-2 group-hover:text-blue-600 transition">
            View Products
          </h2>
          <p className="text-gray-600">
            Check out our latest product offerings
          </p>
        </a>

        {/* Card 3: Dashboard */}
        <a 
          href="/dashboard"
          className="group block p-8 bg-white rounded-xl shadow-lg hover:shadow-2xl transition transform hover:-translate-y-1"
        >
          <div className="text-5xl mb-4">📊</div>
          <h2 className="text-2xl font-bold text-gray-800 mb-2 group-hover:text-blue-600 transition">
            Dashboard
          </h2>
          <p className="text-gray-600">
            Access your personal dashboard
          </p>
        </a>
      </div>

      {/* Info Section */}
      <div className="mt-16 bg-blue-50 border-l-4 border-blue-600 p-6 rounded">
        <h3 className="text-xl font-bold text-blue-800 mb-2">
          📚 Layout Demo
        </h3>
        <p className="text-blue-700">
          This page is wrapped by the <strong>Root Layout</strong>. 
          Notice the header navigation and footer are automatically included!
          Try navigating to different sections to see nested layouts in action.
        </p>
      </div>
    </div>
  );
}
```

**Test it:** Visit `http://localhost:3000/`

You should see:
- Blue header with navigation at top
- Homepage content in middle
- Gray footer at bottom

---

## 🛍️ Part 5: Creating the Shop Section

### Step 3: Create Shop Layout

**Create folder and file:** `app/shop/layout.tsx`

```typescript
export default function ShopLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <div className="flex min-h-screen bg-gray-50">
      {/* Shop Sidebar - Shows ONLY on shop pages */}
      <aside className="w-64 bg-white shadow-lg p-6">
        <h2 className="text-2xl font-bold text-gray-800 mb-6">
          Shop Categories
        </h2>

        {/* Category List */}
        <nav className="space-y-2">
          <a 
            href="/shop"
            className="block px-4 py-3 rounded-lg hover:bg-blue-50 hover:text-blue-600 transition font-medium text-gray-700"
          >
            🏠 Shop Home
          </a>
          <a 
            href="/shop/products"
            className="block px-4 py-3 rounded-lg hover:bg-blue-50 hover:text-blue-600 transition font-medium text-gray-700"
          >
            📦 All Products
          </a>
          <a 
            href="/shop/cart"
            className="block px-4 py-3 rounded-lg hover:bg-blue-50 hover:text-blue-600 transition font-medium text-gray-700"
          >
            🛒 Shopping Cart
          </a>
        </nav>

        {/* Category Divider */}
        <hr className="my-6 border-gray-200" />

        {/* Product Categories */}
        <div>
          <h3 className="text-sm font-semibold text-gray-500 uppercase mb-3">
            Product Categories
          </h3>
          <nav className="space-y-2">
            <a 
              href="/shop/products?category=electronics"
              className="block px-4 py-2 rounded hover:bg-gray-100 transition text-gray-600 text-sm"
            >
              💻 Electronics
            </a>
            <a 
              href="/shop/products?category=clothing"
              className="block px-4 py-2 rounded hover:bg-gray-100 transition text-gray-600 text-sm"
            >
              👕 Clothing
            </a>
            <a 
              href="/shop/products?category=books"
              className="block px-4 py-2 rounded hover:bg-gray-100 transition text-gray-600 text-sm"
            >
              📚 Books
            </a>
            <a 
              href="/shop/products?category=toys"
              className="block px-4 py-2 rounded hover:bg-gray-100 transition text-gray-600 text-sm"
            >
              🎮 Toys
            </a>
          </nav>
        </div>

        {/* Info Box */}
        <div className="mt-8 bg-green-50 border border-green-200 rounded-lg p-4">
          <p className="text-sm text-green-800">
            <strong>✅ Shop Layout</strong>
            <br />
            This sidebar appears on all shop pages!
          </p>
        </div>
      </aside>

      {/* Main Content Area */}
      <div className="flex-1 p-8">
        {children}
      </div>
    </div>
  );
}
```

**Understanding the Code:**

- **Lines 8-79:** Sidebar with shop navigation (ONLY shows on `/shop/*` pages)
- **Line 83:** `{children}` renders the specific shop page content
- **Layout structure:** Sidebar on left, page content on right

---

### Step 4: Create Shop Home Page

**Create file:** `app/shop/page.tsx`

```typescript
export default function ShopPage() {
  return (
    <div>
      {/* Page Header */}
      <div className="mb-8">
        <h1 className="text-4xl font-bold text-gray-800 mb-2">
          Welcome to Our Shop
        </h1>
        <p className="text-gray-600 text-lg">
          Browse through our amazing collection of products
        </p>
      </div>

      {/* Featured Categories */}
      <div className="grid md:grid-cols-2 gap-6 mb-8">
        <div className="bg-white rounded-lg shadow p-6 hover:shadow-lg transition">
          <div className="text-4xl mb-3">🎉</div>
          <h2 className="text-2xl font-bold text-gray-800 mb-2">
            Featured Products
          </h2>
          <p className="text-gray-600 mb-4">
            Check out our handpicked selection of amazing items
          </p>
          <a 
            href="/shop/products"
            className="inline-block px-4 py-2 bg-blue-600 text-white rounded hover:bg-blue-700 transition"
          >
            Browse Now
          </a>
        </div>

        <div className="bg-white rounded-lg shadow p-6 hover:shadow-lg transition">
          <div className="text-4xl mb-3">🔥</div>
          <h2 className="text-2xl font-bold text-gray-800 mb-2">
            Hot Deals
          </h2>
          <p className="text-gray-600 mb-4">
            Don't miss out on our limited-time offers
          </p>
          <a 
            href="/shop/products"
            className="inline-block px-4 py-2 bg-red-600 text-white rounded hover:bg-red-700 transition"
          >
            View Deals
          </a>
        </div>
      </div>

      {/* Info Box */}
      <div className="bg-blue-50 border-l-4 border-blue-600 p-6 rounded">
        <h3 className="text-lg font-bold text-blue-800 mb-2">
          📦 Layout Structure
        </h3>
        <p className="text-blue-700">
          This page has <strong>three layers</strong>:
        </p>
        <ol className="list-decimal list-inside mt-2 text-blue-700 space-y-1">
          <li>Root Layout (header + footer)</li>
          <li>Shop Layout (sidebar - see left side!)</li>
          <li>This page content</li>
        </ol>
      </div>
    </div>
  );
}
```

---

### Step 5: Create Products Page

**Create folder and file:** `app/shop/products/page.tsx`

```typescript
const products = [
  { id: 1, name: 'Laptop', price: '$999', category: 'Electronics', emoji: '💻' },
  { id: 2, name: 'Headphones', price: '$199', category: 'Electronics', emoji: '🎧' },
  { id: 3, name: 'T-Shirt', price: '$29', category: 'Clothing', emoji: '👕' },
  { id: 4, name: 'Jeans', price: '$59', category: 'Clothing', emoji: '👖' },
  { id: 5, name: 'Book', price: '$15', category: 'Books', emoji: '📚' },
  { id: 6, name: 'Toy Car', price: '$25', category: 'Toys', emoji: '🚗' },
];

export default function ProductsPage() {
  return (
    <div>
      {/* Page Header */}
      <div className="mb-8">
        <h1 className="text-4xl font-bold text-gray-800 mb-2">
          All Products
        </h1>
        <p className="text-gray-600">
          Showing {products.length} products
        </p>
      </div>

      {/* Product Grid */}
      <div className="grid md:grid-cols-2 lg:grid-cols-3 gap-6">
        {products.map((product) => (
          <div 
            key={product.id}
            className="bg-white rounded-lg shadow-md hover:shadow-xl transition p-6"
          >
            {/* Product Emoji */}
            <div className="text-6xl mb-4 text-center">
              {product.emoji}
            </div>

            {/* Product Info */}
            <h3 className="text-xl font-bold text-gray-800 mb-2">
              {product.name}
            </h3>
            <p className="text-gray-600 mb-1">
              Category: {product.category}
            </p>
            <p className="text-2xl font-bold text-blue-600 mb-4">
              {product.price}
            </p>

            {/* Action Buttons */}
            <div className="space-y-2">
              <button className="w-full px-4 py-2 bg-blue-600 text-white rounded hover:bg-blue-700 transition">
                Add to Cart
              </button>
              <button className="w-full px-4 py-2 bg-gray-200 text-gray-700 rounded hover:bg-gray-300 transition">
                View Details
              </button>
            </div>
          </div>
        ))}
      </div>

      {/* Notice Box */}
      <div className="mt-8 bg-green-50 border-l-4 border-green-600 p-6 rounded">
        <p className="text-green-800">
          <strong>✅ Notice:</strong> The shop sidebar on the left is provided 
          by the Shop Layout. It appears on this page and all other shop pages!
        </p>
      </div>
    </div>
  );
}
```

---

### Step 6: Create Cart Page

**Create folder and file:** `app/shop/cart/page.tsx`

```typescript
export default function CartPage() {
  return (
    <div>
      {/* Page Header */}
      <div className="mb-8">
        <h1 className="text-4xl font-bold text-gray-800 mb-2">
          🛒 Shopping Cart
        </h1>
        <p className="text-gray-600">
          Review your items before checkout
        </p>
      </div>

      {/* Empty Cart Message */}
      <div className="bg-white rounded-lg shadow p-12 text-center">
        <div className="text-8xl mb-6">🛒</div>
        <h2 className="text-3xl font-bold text-gray-800 mb-4">
          Your cart is empty
        </h2>
        <p className="text-gray-600 mb-8 text-lg">
          Add some products to get started!
        </p>
        <a 
          href="/shop/products"
          className="inline-block px-8 py-3 bg-blue-600 text-white rounded-lg hover:bg-blue-700 transition text-lg font-semibold"
        >
          Browse Products
        </a>
      </div>

      {/* Info Box */}
      <div className="mt-8 bg-purple-50 border-l-4 border-purple-600 p-6 rounded">
        <h3 className="text-lg font-bold text-purple-800 mb-2">
          🎯 Layout Demonstration
        </h3>
        <p className="text-purple-700">
          This cart page also uses the Shop Layout. All three shop pages 
          (/shop, /shop/products, /shop/cart) share the same sidebar!
        </p>
      </div>
    </div>
  );
}
```

**Test Shop Section:**
- Visit `http://localhost:3000/shop` → See shop home with sidebar
- Visit `http://localhost:3000/shop/products` → See products with sidebar
- Visit `http://localhost:3000/shop/cart` → See cart with sidebar
- Visit `http://localhost:3000/` → Homepage has NO sidebar

---

## 📊 Part 6: Creating the Dashboard Section

### Step 7: Create Dashboard Layout

**Create folder and file:** `app/dashboard/layout.tsx`

```typescript
export default function DashboardLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <div className="min-h-screen bg-gradient-to-br from-purple-50 to-pink-50">
      <div className="max-w-7xl mx-auto px-4 py-8">
        {/* Dashboard Header */}
        <div className="bg-white rounded-lg shadow-lg p-6 mb-8">
          <div className="flex items-center justify-between">
            <div>
              <h1 className="text-3xl font-bold text-gray-800">
                📊 Dashboard
              </h1>
              <p className="text-gray-600 mt-1">
                Welcome back! Here's your overview
              </p>
            </div>
            <div className="flex gap-3">
              <button className="px-4 py-2 bg-purple-600 text-white rounded-lg hover:bg-purple-700 transition">
                Settings
              </button>
              <button className="px-4 py-2 bg-gray-200 text-gray-700 rounded-lg hover:bg-gray-300 transition">
                Logout
              </button>
            </div>
          </div>
        </div>

        {/* Dashboard Navigation Tabs */}
        <div className="bg-white rounded-lg shadow-lg p-2 mb-8">
          <nav className="flex gap-2">
            <a 
              href="/dashboard"
              className="flex-1 px-6 py-3 text-center rounded-lg bg-purple-100 text-purple-700 font-semibold hover:bg-purple-200 transition"
            >
              Overview
            </a>
            <a 
              href="/dashboard/analytics"
              className="flex-1 px-6 py-3 text-center rounded-lg text-gray-600 hover:bg-gray-100 transition"
            >
              Analytics
            </a>
            <a 
              href="/dashboard/reports"
              className="flex-1 px-6 py-3 text-center rounded-lg text-gray-600 hover:bg-gray-100 transition"
            >
              Reports
            </a>
            <a 
              href="/dashboard/settings"
              className="flex-1 px-6 py-3 text-center rounded-lg text-gray-600 hover:bg-gray-100 transition"
            >
              Settings
            </a>
          </nav>
        </div>

        {/* Info Box */}
        <div className="bg-orange-50 border-l-4 border-orange-600 p-4 mb-8 rounded">
          <p className="text-sm text-orange-800">
            <strong>✅ Dashboard Layout</strong> - This section has its own layout 
            with a special header and navigation tabs!
          </p>
        </div>

        {/* Main Content */}
        <div>
          {children}
        </div>
      </div>
    </div>
  );
}
```

---

### Step 8: Create Dashboard Home Page

**Create file:** `app/dashboard/page.tsx`

```typescript
export default function DashboardPage() {
  return (
    <div className="space-y-6">
      {/* Stats Grid */}
      <div className="grid md:grid-cols-4 gap-6">
        {/* Stat Card 1 */}
        <div className="bg-white rounded-lg shadow p-6">
          <div className="text-3xl mb-2">👥</div>
          <p className="text-gray-600 text-sm">Total Users</p>
          <p className="text-3xl font-bold text-gray-800">1,234</p>
          <p className="text-green-600 text-sm mt-1">↑ 12% from last month</p>
        </div>

        {/* Stat Card 2 */}
        <div className="bg-white rounded-lg shadow p-6">
          <div className="text-3xl mb-2">💰</div>
          <p className="text-gray-600 text-sm">Revenue</p>
          <p className="text-3xl font-bold text-gray-800">$45,678</p>
          <p className="text-green-600 text-sm mt-1">↑ 8% from last month</p>
        </div>

        {/* Stat Card 3 */}
        <div className="bg-white rounded-lg shadow p-6">
          <div className="text-3xl mb-2">📦</div>
          <p className="text-gray-600 text-sm">Orders</p>
          <p className="text-3xl font-bold text-gray-800">567</p>
          <p className="text-red-600 text-sm mt-1">↓ 3% from last month</p>
        </div>

        {/* Stat Card 4 */}
        <div className="bg-white rounded-lg shadow p-6">
          <div className="text-3xl mb-2">⭐</div>
          <p className="text-gray-600 text-sm">Satisfaction</p>
          <p className="text-3xl font-bold text-gray-800">98%</p>
          <p className="text-green-600 text-sm mt-1">↑ 2% from last month</p>
        </div>
      </div>

      {/* Recent Activity */}
      <div className="bg-white rounded-lg shadow p-6">
        <h2 className="text-2xl font-bold text-gray-800 mb-4">
          Recent Activity
        </h2>
        <div className="space-y-3">
          {[1, 2, 3, 4, 5].map((i) => (
            <div key={i} className="flex items-center gap-4 p-3 hover:bg-gray-50 rounded transition">
              <div className="w-10 h-10 rounded-full bg-purple-100 flex items-center justify-center">
                👤
              </div>
              <div className="flex-1">
                <p className="font-semibold text-gray-800">User {i} made a purchase</p>
                <p className="text-sm text-gray-500">{i} hours ago</p>
              </div>
              <button className="px-4 py-2 bg-gray-100 text-gray-700 rounded hover:bg-gray-200 transition text-sm">
                View
              </button>
            </div>
          ))}
        </div>
      </div>

      {/* Layout Info */}
      <div className="bg-purple-50 border-l-4 border-purple-600 p-6 rounded">
        <h3 className="text-lg font-bold text-purple-800 mb-2">
          🎨 Triple-Layered Layout
        </h3>
        <p className="text-purple-700 mb-2">
          This dashboard page has <strong>three layout layers</strong>:
        </p>
        <ol className="list-decimal list-inside text-purple-700 space-y-1">
          <li>Root Layout (blue header + footer)</li>
          <li>Dashboard Layout (purple header + tabs)</li>
          <li>This page content</li>
        </ol>
      </div>
    </div>
  );
}
```

**Test Dashboard:**
- Visit `http://localhost:3000/dashboard`
- Should see: Root header + Dashboard header + Dashboard tabs + Page content

---

## ✅ Part 7: Verify Success Criteria

### ✅ Test 1: Navigation Bar on ALL Pages

Visit these URLs and verify blue header appears:
- `http://localhost:3000/` ✅
- `http://localhost:3000/shop` ✅
- `http://localhost:3000/shop/products` ✅
- `http://localhost:3000/dashboard` ✅

### ✅ Test 2: Shop Sidebar ONLY on Shop Pages

- `http://localhost:3000/shop` → Has sidebar ✅
- `http://localhost:3000/shop/products` → Has sidebar ✅
- `http://localhost:3000/shop/cart` → Has sidebar ✅
- `http://localhost:3000/` → NO sidebar ✅
- `http://localhost:3000/dashboard` → NO sidebar ✅

### ✅ Test 3: Footer on ALL Pages

Scroll to bottom on each page:
- `http://localhost:3000/` → Has footer ✅
- `http://localhost:3000/shop` → Has footer ✅
- `http://localhost:3000/dashboard` → Has footer ✅

### ✅ Test 4: Navigation Works

Click through all navigation links - no errors!

---

## 🌟 Bonus Challenge: Active Link Highlighting

Let's make the active navigation link stand out!

### Step 9: Create Active Link Component

**Create file:** `components/NavLink.tsx`

```typescript
'use client';

import { usePathname } from 'next/navigation';

export default function NavLink({ 
  href, 
  children 
}: { 
  href: string; 
  children: React.ReactNode;
}) {
  const pathname = usePathname();
  
  // Check if this link is active
  const isActive = pathname === href || pathname.startsWith(href + '/');
  
  return (
    <a 
      href={href}
      className={`
        hover:text-blue-200 transition font-semibold
        ${isActive ? 'text-yellow-300 border-b-2 border-yellow-300' : ''}
      `}
    >
      {children}
    </a>
  );
}
```

---

### Step 10: Update Root Layout with Active Links

**Update:** `app/layout.tsx`

Add import at top:
```typescript
import NavLink from '@/components/NavLink';
```

Replace the navigation section:

```typescript
{/* Navigation with Active Links */}
<nav>
  <ul className="flex gap-6">
    <li>
      <NavLink href="/">Home</NavLink>
    </li>
    <li>
      <NavLink href="/shop">Shop</NavLink>
    </li>
    <li>
      <NavLink href="/dashboard">Dashboard</NavLink>
    </li>
  </ul>
</nav>
```

**Complete Updated Root Layout:**

```typescript
import type { Metadata } from 'next';
import './globals.css';
import NavLink from '@/components/NavLink';

export const metadata: Metadata = {
  title: 'My Multi-Section Website',
  description: 'Learning Next.js Layouts',
};

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body>
        {/* Header with Navigation */}
        <header className="bg-blue-600 text-white shadow-lg">
          <div className="max-w-7xl mx-auto px-4 py-4">
            <div className="flex items-center justify-between">
              {/* Logo */}
              <div className="text-2xl font-bold">
                🏪 MyStore
              </div>

              {/* Navigation with Active Links */}
              <nav>
                <ul className="flex gap-6">
                  <li>
                    <NavLink href="/">Home</NavLink>
                  </li>
                  <li>
                    <NavLink href="/shop">Shop</NavLink>
                  </li>
                  <li>
                    <NavLink href="/dashboard">Dashboard</NavLink>
                  </li>
                </ul>
              </nav>
            </div>
          </div>
        </header>

        {/* Main Content */}
        <main className="min-h-screen">
          {children}
        </main>

        {/* Footer */}
        <footer className="bg-gray-800 text-white">
          <div className="max-w-7xl mx-auto px-4 py-8">
            <div className="text-center">
              <p className="text-lg mb-2">
                © 2024 MyStore. All rights reserved.
              </p>
              <p className="text-gray-400 text-sm">
                Built with Next.js 16 | Learning Layouts
              </p>
            </div>
          </div>
        </footer>
      </body>
    </html>
  );
}
```

**Test it:**
- Click "Home" → "Home" link is highlighted in yellow
- Click "Shop" → "Shop" link is highlighted in yellow
- Click "Dashboard" → "Dashboard" link is highlighted in yellow

---

## 📊 Final Project Structure

```
app/
├── layout.tsx              ✅ Root Layout (all pages)
├── page.tsx                ✅ Homepage
├── shop/
│   ├── layout.tsx          ✅ Shop Layout (sidebar)
│   ├── page.tsx            ✅ Shop home
│   ├── products/
│   │   └── page.tsx        ✅ Products
│   └── cart/
│       └── page.tsx        ✅ Cart
└── dashboard/
    ├── layout.tsx          ✅ Dashboard Layout
    └── page.tsx            ✅ Dashboard home

components/
└── NavLink.tsx             ✅ Active link component
```

---

## 🎓 Key Concepts Recap

### 1. **Root Layout (app/layout.tsx)**
- Wraps ALL pages
- Required file (must exist!)
- Contains `<html>` and `<body>` tags
- Common elements: header, footer, global styles

### 2. **Nested Layouts**
```typescript
// app/shop/layout.tsx
export default function ShopLayout({ children }) {
  return (
    <div>
      <aside>Sidebar</aside>
      <div>{children}</div>
    </div>
  );
}
```
- Only wraps pages in that folder
- Can nest infinitely deep
- Inherit parent layouts

### 3. **The `children` Prop**
```typescript
{children}  ← Page content goes here
```
- Represents the page being rendered
- Required in every layout
- Automatically passed by Next.js

### 4. **Active Links (Client Component)**
```typescript
'use client';
import { usePathname } from 'next/navigation';

const pathname = usePathname();  // "/shop/products"
const isActive = pathname === href;
```

---

## 🐛 Common Issues & Solutions

**Problem:** Layout not showing  
**Solution:** Make sure `{children}` is in your layout

**Problem:** Sidebar shows on all pages  
**Solution:** Check it's in `app/shop/layout.tsx`, not `app/layout.tsx`

**Problem:** Active link doesn't work  
**Solution:** Add `'use client'` to NavLink component

**Problem:** Styles conflict between layouts  
**Solution:** Use specific class names or CSS modules

---

## 🎉 Congratulations!

You've mastered **Layouts**! You now know:

✅ What layouts are and why they're useful  
✅ How to create a root layout for all pages  
✅ How to create nested layouts for sections  
✅ How to highlight active navigation links  
✅ How layouts compose and stack  

**Layout Benefits:**
- 🔄 Reusable components
- 🎯 Single source of truth
- 🚀 Easy maintenance
- 🎨 Consistent design

  You're officially ready for Day 5: Loading & Error States 🚀
