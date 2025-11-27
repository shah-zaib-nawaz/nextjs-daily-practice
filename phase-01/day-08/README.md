# Day 8: Async Params - Complete Beginner's Guide

Today you'll learn about **Next.js 16's breaking change**: `params` and `searchParams` are now **Promises** that must be awaited!

---

## 🎯 What You'll Learn Today

- Why Next.js 16 changed params to Promises
- How to properly await params and searchParams
- The difference between old and new syntax
- How to handle multiple dynamic segments
- How to work with URL query parameters

---

## 📚 Part 1: Understanding the Breaking Change (20 minutes)

### What Changed in Next.js 16?

**Next.js 15 (OLD WAY):**
```typescript
// ❌ This no longer works in Next.js 16!
export default function ProductPage({ params }: { params: { id: string } }) {
  const { id } = params;  // Direct access
  return <h1>Product {id}</h1>;
}
```

**Next.js 16 (NEW WAY):**
```typescript
// ✅ This is the correct way!
export default async function ProductPage({
  params
}: {
  params: Promise<{ id: string }>  // Promise now!
}) {
  const { id } = await params;  // Must await!
  return <h1>Product {id}</h1>;
}
```

### Why This Change?

**The problem Next.js solved:**
- Better streaming support
- Improved parallel data fetching
- More predictable behavior
- Preparation for React Server Components evolution

**Think of it like ordering food:**
- **Old way:** Food appears instantly (unrealistic)
- **New way:** You wait for food to be prepared (realistic)

The new way is more honest about the async nature of routing!

---

## 🔍 Part 2: Understanding Promises

### What is a Promise?

A **Promise** represents a value that will be available in the future.

**Analogy:**
```
Promise = A receipt for your order

You order coffee ☕
    ↓
Barista gives you a receipt (Promise)
    ↓
You wait...
    ↓
Coffee is ready! (Promise resolves)
    ↓
You get your coffee (await the Promise)
```

### Awaiting Promises

```typescript
// params is a Promise - you can't use it directly
const params = Promise { ... }  // ❌ Can't read id yet

// You must await it
const resolvedParams = await params;  // ✅ Now you can use it
const { id } = resolvedParams;  // ✅ Access id
```

---

## 💻 Part 3: Building the Product Catalog

### Your Project Structure

```
app/
├── products/
│   ├── page.tsx              ← Product list
│   └── [id]/
│       └── page.tsx          ← Product detail (async params!)
└── layout.tsx                (existing)
```

---

## 📦 Part 4: Creating Product Data

### Step 1: Create Product Data File

**Create file:** `lib/product-data.ts`

```typescript
export interface Product {
  id: string;
  name: string;
  price: number;
  description: string;
  category: string;
  colors: string[];
  sizes: string[];
  inStock: boolean;
  image: string;
}

export const products: Product[] = [
  {
    id: '1',
    name: 'Classic T-Shirt',
    price: 29.99,
    description: 'Comfortable cotton t-shirt perfect for everyday wear. Made from 100% organic cotton.',
    category: 'Clothing',
    colors: ['red', 'blue', 'black', 'white'],
    sizes: ['S', 'M', 'L', 'XL'],
    inStock: true,
    image: '👕',
  },
  {
    id: '2',
    name: 'Wireless Headphones',
    price: 199.99,
    description: 'Premium wireless headphones with noise cancellation. 30-hour battery life.',
    category: 'Electronics',
    colors: ['black', 'silver', 'blue'],
    sizes: ['One Size'],
    inStock: true,
    image: '🎧',
  },
  {
    id: '3',
    name: 'Running Shoes',
    price: 89.99,
    description: 'Lightweight running shoes with advanced cushioning technology.',
    category: 'Footwear',
    colors: ['black', 'white', 'red', 'blue'],
    sizes: ['7', '8', '9', '10', '11', '12'],
    inStock: true,
    image: '👟',
  },
  {
    id: '4',
    name: 'Laptop Backpack',
    price: 59.99,
    description: 'Durable backpack with padded laptop compartment. Water-resistant material.',
    category: 'Accessories',
    colors: ['black', 'gray', 'navy'],
    sizes: ['One Size'],
    inStock: true,
    image: '🎒',
  },
  {
    id: '5',
    name: 'Smart Watch',
    price: 299.99,
    description: 'Feature-rich smartwatch with fitness tracking and notifications.',
    category: 'Electronics',
    colors: ['black', 'silver', 'gold'],
    sizes: ['38mm', '42mm', '44mm'],
    inStock: false,
    image: '⌚',
  },
];

export function getProduct(id: string): Product | undefined {
  return products.find((product) => product.id === id);
}

export function getAllProducts(): Product[] {
  return products;
}
```

---

## 🛍️ Part 5: Creating the Product List Page

### Step 2: Create Product List

**Create file:** `app/products/page.tsx`

```typescript
import { getAllProducts } from '@/lib/product-data';

export default function ProductsPage() {
  const products = getAllProducts();

  return (
    <div className="min-h-screen bg-gray-50 py-12 px-4">
      <div className="max-w-6xl mx-auto">
        {/* Header */}
        <div className="text-center mb-12">
          <h1 className="text-5xl font-bold text-gray-800 mb-4">
            🛍️ Product Catalog
          </h1>
          <p className="text-xl text-gray-600">
            Browse our amazing collection of products
          </p>
        </div>

        {/* Info Box - Async Params Explanation */}
        <div className="bg-blue-50 border-l-4 border-blue-600 p-6 mb-8 rounded">
          <h3 className="text-lg font-bold text-blue-800 mb-2">
            🔄 Next.js 16 Feature: Async Params
          </h3>
          <p className="text-blue-700 mb-2">
            In Next.js 16, <code className="bg-blue-100 px-2 py-1 rounded">params</code> and{' '}
            <code className="bg-blue-100 px-2 py-1 rounded">searchParams</code> are now Promises!
          </p>
          <p className="text-blue-700">
            <strong>Try this:</strong> Click any product and notice the URL parameters. 
            Then try adding <code className="bg-blue-100 px-2 py-1 rounded">?color=red&size=large</code> to the URL!
          </p>
        </div>

        {/* Products Grid */}
        <div className="grid md:grid-cols-2 lg:grid-cols-3 gap-6">
          {products.map((product) => (
            
              key={product.id}
              href={`/products/${product.id}`}
              className="block bg-white rounded-lg shadow hover:shadow-xl transition overflow-hidden group"
            >
              {/* Product Image */}
              <div className="bg-gradient-to-br from-blue-400 to-purple-500 h-48 flex items-center justify-center text-8xl">
                {product.image}
              </div>

              {/* Product Info */}
              <div className="p-6">
                {/* Category Badge */}
                <span className="inline-block px-3 py-1 bg-purple-100 text-purple-700 rounded-full text-xs font-semibold mb-3">
                  {product.category}
                </span>

                {/* Product Name */}
                <h2 className="text-xl font-bold text-gray-800 mb-2 group-hover:text-blue-600 transition">
                  {product.name}
                </h2>

                {/* Product Description */}
                <p className="text-gray-600 text-sm mb-4 line-clamp-2">
                  {product.description}
                </p>

                {/* Price */}
                <p className="text-3xl font-bold text-blue-600 mb-4">
                  ${product.price}
                </p>

                {/* Stock Status */}
                <div className="flex items-center justify-between">
                  {product.inStock ? (
                    <span className="text-green-600 text-sm font-semibold flex items-center gap-1">
                      ✓ In Stock
                    </span>
                  ) : (
                    <span className="text-red-600 text-sm font-semibold flex items-center gap-1">
                      ✗ Out of Stock
                    </span>
                  )}
                  <span className="text-blue-600 font-semibold group-hover:gap-2 flex items-center transition-all">
                    View Details →
                  </span>
                </div>
              </div>
            </a>
          ))}
        </div>

        {/* Test Links */}
        <div className="mt-12 bg-yellow-50 border-l-4 border-yellow-500 p-6 rounded">
          <h3 className="text-lg font-bold text-yellow-800 mb-4">
            🧪 Test Async Params & Search Params
          </h3>
          <p className="text-yellow-700 mb-4">
            Try these URLs to see params and searchParams in action:
          </p>
          <div className="space-y-2">
            
              href="/products/1"
              className="block px-4 py-2 bg-white rounded hover:bg-yellow-50 transition text-yellow-800 font-mono text-sm"
            >
              /products/1 → Basic params
            </a>
            
              href="/products/2?color=blue&size=M"
              className="block px-4 py-2 bg-white rounded hover:bg-yellow-50 transition text-yellow-800 font-mono text-sm"
            >
              /products/2?color=blue&size=M → With search params
            </a>
            
              href="/products/3?color=red&size=large&quantity=2"
              className="block px-4 py-2 bg-white rounded hover:bg-yellow-50 transition text-yellow-800 font-mono text-sm"
            >
              /products/3?color=red&size=large&quantity=2 → Multiple search params
            </a>
          </div>
        </div>
      </div>
    </div>
  );
}
```

---

## 🎯 Part 6: Creating Product Detail with Async Params

### Step 3: Create Product Detail Page

**Create file:** `app/products/[id]/page.tsx`

```typescript
import { getProduct, getAllProducts } from '@/lib/product-data';
import { notFound } from 'next/navigation';

// 🔥 THIS IS THE IMPORTANT PART - Next.js 16 Async Params!
export default async function ProductPage({
  params,
  searchParams,
}: {
  params: Promise<{ id: string }>;           // ← Promise!
  searchParams: Promise<{                     // ← Promise!
    color?: string;
    size?: string;
    quantity?: string;
  }>;
}) {
  // 🔥 MUST AWAIT params in Next.js 16!
  const { id } = await params;
  
  // 🔥 MUST AWAIT searchParams in Next.js 16!
  const { color, size, quantity } = await searchParams;

  // Get product data
  const product = getProduct(id);

  // If product doesn't exist, show 404
  if (!product) {
    notFound();
  }

  // Parse quantity
  const quantityNum = quantity ? parseInt(quantity, 10) : 1;
  const totalPrice = (product.price * quantityNum).toFixed(2);

  return (
    <div className="min-h-screen bg-gray-50 py-12 px-4">
      <div className="max-w-4xl mx-auto">
        {/* Back Button */}
        
          href="/products"
          className="inline-flex items-center gap-2 px-4 py-2 bg-white rounded-lg shadow hover:shadow-md transition mb-8 font-semibold text-gray-700"
        >
          ← Back to Products
        </a>

        {/* Params Display (Success Criteria!) */}
        <div className="bg-green-50 border-l-4 border-green-600 p-6 mb-8 rounded">
          <h3 className="text-lg font-bold text-green-800 mb-3">
            ✅ Async Params in Action
          </h3>
          <div className="space-y-2 font-mono text-sm">
            <p className="text-green-700">
              <strong>params.id:</strong> <code className="bg-green-100 px-2 py-1 rounded">{id}</code>
            </p>
            {color && (
              <p className="text-green-700">
                <strong>searchParams.color:</strong>{' '}
                <code className="bg-green-100 px-2 py-1 rounded">{color}</code>
              </p>
            )}
            {size && (
              <p className="text-green-700">
                <strong>searchParams.size:</strong>{' '}
                <code className="bg-green-100 px-2 py-1 rounded">{size}</code>
              </p>
            )}
            {quantity && (
              <p className="text-green-700">
                <strong>searchParams.quantity:</strong>{' '}
                <code className="bg-green-100 px-2 py-1 rounded">{quantity}</code>
              </p>
            )}
          </div>
        </div>

        {/* Product Card */}
        <div className="bg-white rounded-lg shadow-xl overflow-hidden">
          {/* Product Image */}
          <div className="bg-gradient-to-br from-blue-400 to-purple-500 h-96 flex items-center justify-center">
            <span className="text-9xl">{product.image}</span>
          </div>

          {/* Product Details */}
          <div className="p-8">
            {/* Category */}
            <span className="inline-block px-4 py-2 bg-purple-100 text-purple-700 rounded-full text-sm font-semibold mb-4">
              {product.category}
            </span>

            {/* Product Name */}
            <h1 className="text-4xl font-bold text-gray-800 mb-4">
              {product.name}
            </h1>

            {/* Price */}
            <p className="text-5xl font-bold text-blue-600 mb-6">
              ${product.price}
            </p>

            {/* Description */}
            <p className="text-gray-700 text-lg mb-8 leading-relaxed">
              {product.description}
            </p>

            {/* Stock Status */}
            <div className="mb-8">
              {product.inStock ? (
                <span className="inline-flex items-center gap-2 px-4 py-2 bg-green-100 text-green-700 rounded-lg font-semibold">
                  ✓ In Stock
                </span>
              ) : (
                <span className="inline-flex items-center gap-2 px-4 py-2 bg-red-100 text-red-700 rounded-lg font-semibold">
                  ✗ Out of Stock
                </span>
              )}
            </div>

            {/* Selected Options (from searchParams) */}
            {(color || size || quantity) && (
              <div className="bg-blue-50 border border-blue-200 rounded-lg p-6 mb-8">
                <h3 className="text-xl font-bold text-blue-800 mb-4">
                  🎨 Selected Options
                </h3>
                <div className="space-y-3">
                  {color && (
                    <div>
                      <span className="text-blue-700 font-semibold">Color:</span>
                      <span className="ml-3 px-4 py-2 bg-white border-2 border-blue-300 rounded-lg capitalize">
                        {color}
                      </span>
                    </div>
                  )}
                  {size && (
                    <div>
                      <span className="text-blue-700 font-semibold">Size:</span>
                      <span className="ml-3 px-4 py-2 bg-white border-2 border-blue-300 rounded-lg uppercase">
                        {size}
                      </span>
                    </div>
                  )}
                  {quantity && (
                    <div>
                      <span className="text-blue-700 font-semibold">Quantity:</span>
                      <span className="ml-3 px-4 py-2 bg-white border-2 border-blue-300 rounded-lg">
                        {quantity}
                      </span>
                    </div>
                  )}
                  {quantity && (
                    <div className="pt-3 border-t border-blue-200">
                      <span className="text-blue-700 font-semibold">Total:</span>
                      <span className="ml-3 text-2xl font-bold text-blue-600">
                        ${totalPrice}
                      </span>
                    </div>
                  )}
                </div>
              </div>
            )}

            {/* Available Colors */}
            <div className="mb-6">
              <h3 className="text-lg font-semibold text-gray-800 mb-3">
                Available Colors:
              </h3>
              <div className="flex flex-wrap gap-2">
                {product.colors.map((c) => (
                  
                    key={c}
                    href={`/products/${id}?color=${c}${size ? `&size=${size}` : ''}${quantity ? `&quantity=${quantity}` : ''}`}
                    className={`px-4 py-2 rounded-lg capitalize font-medium transition ${
                      color === c
                        ? 'bg-blue-600 text-white'
                        : 'bg-gray-100 text-gray-700 hover:bg-gray-200'
                    }`}
                  >
                    {c}
                  </a>
                ))}
              </div>
            </div>

            {/* Available Sizes */}
            <div className="mb-8">
              <h3 className="text-lg font-semibold text-gray-800 mb-3">
                Available Sizes:
              </h3>
              <div className="flex flex-wrap gap-2">
                {product.sizes.map((s) => (
                  
                    key={s}
                    href={`/products/${id}?${color ? `color=${color}&` : ''}size=${s}${quantity ? `&quantity=${quantity}` : ''}`}
                    className={`px-4 py-2 rounded-lg uppercase font-medium transition ${
                      size === s
                        ? 'bg-blue-600 text-white'
                        : 'bg-gray-100 text-gray-700 hover:bg-gray-200'
                    }`}
                  >
                    {s}
                  </a>
                ))}
              </div>
            </div>

            {/* Action Buttons */}
            <div className="flex gap-4">
              <button className="flex-1 px-8 py-4 bg-blue-600 text-white rounded-lg hover:bg-blue-700 transition font-bold text-lg">
                Add to Cart
              </button>
              <button className="px-8 py-4 bg-gray-200 text-gray-700 rounded-lg hover:bg-gray-300 transition font-bold">
                ♥
              </button>
            </div>
          </div>
        </div>

        {/* Code Example */}
        <div className="mt-12 bg-gray-800 rounded-lg p-6 text-white font-mono text-sm overflow-x-auto">
          <p className="text-green-400 mb-2">// Next.js 16 Async Params Example:</p>
          <pre className="text-gray-300">{`export default async function ProductPage({
  params,
  searchParams
}: {
  params: Promise<{ id: string }>;
  searchParams: Promise<{ color?: string; size?: string }>;
}) {
  const { id } = await params;        // ← Must await!
  const { color, size } = await searchParams;  // ← Must await!
  
  return <div>Product {id}</div>;
}`}</pre>
        </div>

        {/* Other Products */}
        <div className="mt-12">
          <h2 className="text-2xl font-bold text-gray-800 mb-6">
            You May Also Like
          </h2>
          <div className="grid md:grid-cols-3 gap-6">
            {getAllProducts()
              .filter((p) => p.id !== id)
              .slice(0, 3)
              .map((otherProduct) => (
                
                  key={otherProduct.id}
                  href={`/products/${otherProduct.id}`}
                  className="block bg-white rounded-lg shadow hover:shadow-lg transition p-4"
                >
                  <div className="text-6xl text-center mb-3">
                    {otherProduct.image}
                  </div>
                  <h3 className="font-bold text-gray-800 mb-1">
                    {otherProduct.name}
                  </h3>
                  <p className="text-blue-600 font-bold">${otherProduct.price}</p>
                </a>
              ))}
          </div>
        </div>
      </div>
    </div>
  );
}
```

**Key Features:**
- **Lines 8-13:** Type definitions with `Promise<>` wrappers
- **Line 16:** `await params` - REQUIRED in Next.js 16
- **Line 19:** `await searchParams` - REQUIRED in Next.js 16
- **Lines 48-75:** Display of params and searchParams (success criteria!)
- **Lines 167-182:** Interactive color selector that updates URL
- **Lines 185-200:** Interactive size selector that updates URL

---

## 🧪 Part 7: Testing Async Params

### Test 1: Basic Params

1. Visit `http://localhost:3000/products`
2. Click "Classic T-Shirt" (Product ID: 1)
3. **Check the green box at top** - should show:
   ```
   params.id: 1
   ```

**Expected:** ✅ Product ID displays correctly

---

### Test 2: Search Params

1. Visit `http://localhost:3000/products/2?color=blue&size=M`
2. **Check the green box** - should show:
   ```
   params.id: 2
   searchParams.color: blue
   searchParams.size: M
   ```
3. **Check "Selected Options" section** - should show color and size

**Expected:** ✅ Search params display when present

---

### Test 3: Multiple Search Params

1. Visit `http://localhost:3000/products/3?color=red&size=large&quantity=2`
2. **Check the green box** - should show all three params
3. **Check total price** - should calculate correctly (89.99 × 2 = $179.98)

**Expected:** ✅ All params work together

---

### Test 4: Interactive Selection

1. Visit any product page
2. **Click different colors** - URL updates, green box updates
3. **Click different sizes** - URL updates, green box updates
4. **Notice:** Selected options highlight in blue

**Expected:** ✅ Interactive URL updates work

---

## ✅ Part 8: Success Criteria Verification

### ✅ Product ID Displays Correctly
- Visit `/products/1`
- Check green box shows `params.id: 1`
- **Verified:** ✅

### ✅ Search Params Display When Present
- Visit `/products/2?color=blue`
- Check green box shows `searchParams.color: blue`
- **Verified:** ✅

### ✅ TypeScript Shows No Errors
- Open `app/products/[id]/page.tsx` in VS Code
- Check for red squiggly lines
- **Expected:** No TypeScript errors
- **Verified:** ✅

### ✅ Works with Next.js 16
- Run `npm run dev`
- Navigate to products
- Everything works without errors
- **Verified:** ✅

---

## 🌟 Bonus Challenge: Multiple Dynamic Segments

Let's add category-based routing!

### Step 4: Create Nested Dynamic Route

**Create file:** `app/products/[category]/[id]/page.tsx`

```typescript
import { getProduct } from '@/lib/product-data';
import { notFound } from 'next/navigation';

// 🔥 Multiple dynamic segments!
export default async function CategoryProductPage({
  params,
  searchParams,
}: {
  params: Promise<{ category: string; id: string }>;  // ← Two segments!
  searchParams: Promise<{ color?: string; size?: string }>;
}) {
  // Await params (Next.js 16 requirement)
  const { category, id } = await params;
  const { color, size } = await searchParams;

  // Get product
  const product = getProduct(id);

  // Check if product exists and matches category
  if (!product || product.category.toLowerCase() !== category.toLowerCase()) {
    notFound();
  }

  return (
    <div className="min-h-screen bg-gray-50 py-12 px-4">
      <div className="max-w-4xl mx-auto">
        {/* Back Button */}
        
          href="/products"
          className="inline-flex items-center gap-2 px-4 py-2 bg-white rounded-lg shadow hover:shadow-md transition mb-8 font-semibold text-gray-700"
        >
          ← Back to Products
        </a>

        {/* Multiple Segments Display */}
        <div className="bg-purple-50 border-l-4 border-purple-600 p-6 mb-8 rounded">
          <h3 className="text-lg font-bold text-purple-800 mb-3">
            🎯 Multiple Dynamic Segments
          </h3>
          <div className="space-y-2 font-mono text-sm">
            <p className="text-purple-700">
              <strong>params.category:</strong>{' '}
              <code className="bg-purple-100 px-2 py-1 rounded">{category}</code>
            </p>
            <p className="text-purple-700">
              <strong>params.id:</strong>{' '}
              <code className="bg-purple-100 px-2 py-1 rounded">{id}</code>
            </p>
            {color && (
              <p className="text-purple-700">
                <strong>searchParams.color:</strong>{' '}
                <code className="bg-purple-100 px-2 py-1 rounded">{color}</code>
              </p>
            )}
            {size && (
              <p className="text-purple-700">
                <strong>searchParams.size:</strong>{' '}
                <code className="bg-purple-100 px-2 py-1 rounded">{size}</code>
              </p>
            )}
          </div>
          <p className="text-purple-600 text-sm mt-3">
            URL pattern: <code className="bg-purple-100 px-2 py-1 rounded">/products/[category]/[id]</code>
          </p>
        </div>

        {/* Product Display */}
        <div className="bg-white rounded-lg shadow-xl p-8">
          <span className="inline-block px-4 py-2 bg-purple-100 text-purple-700 rounded-full text-sm font-semibold mb-4">
            {product.category}
          </span>

          <div className="text-8xl text-center my-8">{product.image}</div>

          <h1 className="text-4xl font-bold text-gray-800 mb-4">
            {product.name}
          </h1>

          <p className="text-5xl font-bold text-blue-600 mb-6">
            ${product.price}
          </p>

          <p className="text-gray-700 text-lg mb-8">
            {product.description}
          </p>

          {/* Selected Options */}
          {(color || size) && (
            <div className="bg-blue-50 border border-blue-200 rounded-lg p-6">
              <h3 className="font-bold text-blue-800 mb-3">Selected Options:</h3>
              {color && <p className="text-blue-700">Color: <span className="capitalize font-semibold">{color}</span></p>}
              {size && <p className="text-blue-700">Size: <span className="uppercase font-semibold">{size}</span></p>}
            </div>
          )}
        </div>

        {/* Test Links */}
        <div className="mt-8 bg-yellow-50 border-l-4 border-yellow-500 p-6 rounded">
          <h3 className="font-bold text-yellow-800 mb-3">🧪 Try These URLs:</h3>
          <div className="space-y-2 font-mono text-sm">
            <a href="/products/clothing/1" className="block text-yellow-700 hover:underline">
              /products/clothing/1
            </a>
            <a href="/products/electronics/2?color=black" className="block text-yellow-700 hover:underline">
              /products/electronics/2?color=black
            </a>
            <a href="/products/footwear/3?size=10&color=red" className="block text-yellow-700 hover:underline">
              /products/footwear/3?size=10&color=red
            </a>
          </div>
        </div>
      </div>
    </div>
  );
}
```

**Test the bonus:**
- Visit `/products/clothing/1`
- Visit `/products/electronics/2?color=black`
- Visit `/products/footwear/3?size=10`

---

## 📊 Old vs New Comparison

### Visual Comparison

```typescript
// ❌ NEXT.JS 15 (OLD - DOESN'T WORK IN 16)
export default function Page({ params }: { params: { id: string } }) {
  const { id } = params;  // Direct access
}

// ✅ NEXT.JS 16 (NEW - REQUIRED)
export default async function Page({
  params
}: {
  params: Promise<{ id: string }>  // Promise type
}) {
  const { id } = await params;  // Must await
}
```

### Migration Checklist

If upgrading from Next.js 15 to 16:

- [ ] Add `async` to component function
- [ ] Change params type to `Promise<{ ... }>`
- [ ] Add `await` before destructuring params
- [ ] Do the same for searchParams
- [ ] Test all dynamic routes
- [ ] Check TypeScript has no errors

---

## 🎓 Key Concepts Recap

### 1. **Params are Promises**
```typescript
params: Promise<{ id: string }>  // ← Promise type
const { id } = await params;     // ← Must await
```

### 2. **SearchParams are Promises**
```typescript
searchParams: Promise<{ color?: string }>
const { color } = await searchParams;
```

### 3. **Component Must Be Async**
```typescript
export default async function Page() {  // ← async keyword
  const { id } = await params;
}
```

### 4. **Multiple Dynamic Segments**
```typescript
params: Promise<{ category: string; id: string }>
const { category, id } = await params;
```

---

## 🐛 Common Issues & Solutions

**Problem:** TypeScript error "Property 'id' does not exist"  
**Solution:** Make sure params is typed as `Promise<{ id: string }>`

**Problem:** "params is not iterable"  
**Solution:** Add `await` before destructuring: `await params`

**Problem:** Component not async  
**Solution:** Add `async` keyword: `export default async function`

**Problem:** searchParams undefined  
**Solution:** Type it as optional: `searchParams: Promise<{ color?: string }>`

---

## 🎉 Congratulations!

You've mastered **Async Params** in Next.js 16! You now know:

✅ Why params and searchParams are Promises  
✅ How to properly await them  
✅ How to handle multiple dynamic segments  
✅ How to work with URL query parameters  
✅ How to migrate from Next.js 15 to 16  

**Your product catalog now has:**
- 🎯 Proper async params handling
- 🔍 URL query parameter support
- 🎨 Interactive option selection
- 📊 Multiple dynamic segments (bonus!)
- ✅ Full Next.js 16 compatibility

You're officially ready for Day 9: proxy.ts 🚀
