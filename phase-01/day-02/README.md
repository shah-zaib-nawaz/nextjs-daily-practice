# Day 2: File-Based Routing + Dynamic Routes - Complete Beginner's Guide

Today you'll learn one of the **coolest features** of Next.js: **Dynamic Routes**! This means creating pages that can handle ANY value (like `/recipes/1`, `/recipes/2`, `/recipes/999`).

---

## 🎯 What You'll Learn Today

- How to create **dynamic routes** (pages that work with any ID)
- How to access **URL parameters** (like the `1` in `/recipes/1`)
- How to build a **recipe website** with categories
- How to link between pages properly

---

## 📚 Part 1: Understanding Dynamic Routes (20 minutes)

### What's the Problem?

Imagine you have 100 recipes. Do you create 100 separate files?
- `recipes/1/page.tsx`
- `recipes/2/page.tsx`
- `recipes/3/page.tsx`
- ... (97 more files?) 😰

**That's crazy!**

### The Solution: Dynamic Routes

Instead, create **ONE file** that handles ALL recipe IDs:

```
recipes/[id]/page.tsx  ← The [brackets] make it dynamic!
```

This single file will handle:
- `/recipes/1`
- `/recipes/2`
- `/recipes/999`
- `/recipes/any-value-here`

### How It Works

The **folder name in brackets** `[id]` means:
- "Capture whatever comes here"
- "Store it in a variable called `id`"
- "Let me use it in my code"

**Example:**
- URL: `/recipes/123`
- Next.js captures: `id = "123"`
- You can use this `id` to fetch the right recipe!

---

## 🛠️ Part 2: Setting Up the Recipe Website

### Your Project Structure

We'll build this exact structure:

```
app/
├── page.tsx                      ← Homepage
├── recipes/
│   ├── page.tsx                  ← Recipe list (/recipes)
│   └── [id]/
│       └── page.tsx              ← Single recipe (/recipes/1)
└── categories/
    └── [category]/
        └── page.tsx              ← Category page (/categories/italian)
```

**Note:** Keep your Day 1 files! We're just adding to them.

---

## 💻 Part 3: Building the Homepage

### Step 1: Update Homepage with Links

**File:** `app/page.tsx`

Replace with:

```typescript
export default function HomePage() {
  return (
    <main className="min-h-screen p-8">
      {/* Navigation */}
      <nav className="mb-8 p-4 bg-gray-100 rounded">
        <ul className="flex gap-6">
          <li>
            <a href="/" className="text-blue-600 hover:underline font-semibold">
              Home
            </a>
          </li>
          <li>
            <a href="/recipes" className="text-blue-600 hover:underline">
              Recipes
            </a>
          </li>
        </ul>
      </nav>

      {/* Homepage Content */}
      <div className="max-w-2xl">
        <h1 className="text-5xl font-bold text-blue-600 mb-4">
          🍳 Recipe Collection
        </h1>
        <p className="text-xl text-gray-700 mb-8">
          Welcome to my delicious recipe website! Browse recipes from around the world.
        </p>

        <div className="grid gap-4">
          <a 
            href="/recipes" 
            className="block p-6 bg-blue-50 rounded-lg hover:bg-blue-100 transition"
          >
            <h2 className="text-2xl font-semibold text-blue-600">
              📖 Browse All Recipes
            </h2>
            <p className="text-gray-600 mt-2">
              Explore our complete recipe collection
            </p>
          </a>

          <a 
            href="/categories/italian" 
            className="block p-6 bg-green-50 rounded-lg hover:bg-green-100 transition"
          >
            <h2 className="text-2xl font-semibold text-green-600">
              🇮🇹 Italian Recipes
            </h2>
            <p className="text-gray-600 mt-2">
              Discover authentic Italian dishes
            </p>
          </a>
        </div>
      </div>
    </main>
  );
}
```

**Save and check** `http://localhost:3000/` - you should see a nice homepage!

---

## 📝 Part 4: Creating the Recipe List Page

### Step 2: Create Recipe List

**Create folder and file:** `app/recipes/page.tsx`

```typescript
// Recipe data - we'll use this on multiple pages
const recipes = [
  { id: '1', name: 'Pasta Carbonara', category: 'italian', time: '30 min' },
  { id: '2', name: 'Chicken Curry', category: 'indian', time: '45 min' },
  { id: '3', name: 'Beef Tacos', category: 'mexican', time: '25 min' },
];

export default function RecipesPage() {
  return (
    <main className="min-h-screen p-8">
      {/* Navigation */}
      <nav className="mb-8 p-4 bg-gray-100 rounded">
        <ul className="flex gap-6">
          <li>
            <a href="/" className="text-blue-600 hover:underline">
              Home
            </a>
          </li>
          <li>
            <a href="/recipes" className="text-blue-600 hover:underline font-semibold">
              Recipes
            </a>
          </li>
        </ul>
      </nav>

      {/* Page Title */}
      <h1 className="text-4xl font-bold text-blue-600 mb-8">
        All Recipes
      </h1>

      {/* Recipe Grid */}
      <div className="grid md:grid-cols-2 lg:grid-cols-3 gap-6">
        {recipes.map((recipe) => (
          <div 
            key={recipe.id}
            className="border border-gray-200 rounded-lg p-6 hover:shadow-lg transition"
          >
            {/* Recipe Name */}
            <h2 className="text-2xl font-semibold text-gray-800 mb-2">
              {recipe.name}
            </h2>

            {/* Recipe Details */}
            <div className="space-y-2 text-gray-600 mb-4">
              <p>⏱️ Time: {recipe.time}</p>
              <p>🏷️ Category: {recipe.category}</p>
            </div>

            {/* Link to Single Recipe */}
            <a 
              href={`/recipes/${recipe.id}`}
              className="inline-block px-4 py-2 bg-blue-600 text-white rounded hover:bg-blue-700 transition"
            >
              View Recipe
            </a>
          </div>
        ))}
      </div>

      {/* Category Links */}
      <div className="mt-12">
        <h2 className="text-2xl font-bold text-gray-800 mb-4">
          Browse by Category
        </h2>
        <div className="flex gap-4">
          <a 
            href="/categories/italian"
            className="px-6 py-3 bg-green-100 text-green-700 rounded-lg hover:bg-green-200 transition"
          >
            🇮🇹 Italian
          </a>
          <a 
            href="/categories/indian"
            className="px-6 py-3 bg-orange-100 text-orange-700 rounded-lg hover:bg-orange-200 transition"
          >
            🇮🇳 Indian
          </a>
          <a 
            href="/categories/mexican"
            className="px-6 py-3 bg-red-100 text-red-700 rounded-lg hover:bg-red-200 transition"
          >
            🇲🇽 Mexican
          </a>
        </div>
      </div>
    </main>
  );
}
```

**Test it:** Visit `http://localhost:3000/recipes` 

You should see all 3 recipes displayed in cards!

---

## 🎯 Part 5: Creating Dynamic Single Recipe Page

This is where the **magic** happens!

### Step 3: Create Dynamic Recipe Route

**Create folder and file:** `app/recipes/[id]/page.tsx`

**⚠️ IMPORTANT:** The folder MUST be named `[id]` with square brackets!

```typescript
// Recipe data (same as before)
const recipes = [
  { id: '1', name: 'Pasta Carbonara', category: 'italian', time: '30 min' },
  { id: '2', name: 'Chicken Curry', category: 'indian', time: '45 min' },
  { id: '3', name: 'Beef Tacos', category: 'mexican', time: '25 min' },
];

// This is the IMPORTANT part - notice the params!
export default async function RecipePage({
  params,
}: {
  params: Promise<{ id: string }>;  // Next.js 16 requires Promise!
}) {
  // MUST await params in Next.js 16
  const { id } = await params;

  // Find the recipe with matching ID
  const recipe = recipes.find((r) => r.id === id);

  // If recipe not found, show error
  if (!recipe) {
    return (
      <main className="min-h-screen p-8">
        <div className="max-w-2xl mx-auto text-center">
          <h1 className="text-4xl font-bold text-red-600 mb-4">
            Recipe Not Found
          </h1>
          <p className="text-gray-600 mb-8">
            Recipe ID: {id} does not exist.
          </p>
          <a 
            href="/recipes"
            className="inline-block px-6 py-3 bg-blue-600 text-white rounded hover:bg-blue-700"
          >
            ← Back to All Recipes
          </a>
        </div>
      </main>
    );
  }

  // If recipe found, display it
  return (
    <main className="min-h-screen p-8">
      {/* Navigation */}
      <nav className="mb-8 p-4 bg-gray-100 rounded">
        <ul className="flex gap-6">
          <li>
            <a href="/" className="text-blue-600 hover:underline">
              Home
            </a>
          </li>
          <li>
            <a href="/recipes" className="text-blue-600 hover:underline">
              Recipes
            </a>
          </li>
        </ul>
      </nav>

      {/* Recipe Content */}
      <div className="max-w-2xl mx-auto">
        {/* Recipe ID Display (for success criteria) */}
        <div className="bg-blue-50 border-l-4 border-blue-600 p-4 mb-6">
          <p className="text-sm text-blue-600 font-semibold">
            Recipe ID: {id}
          </p>
        </div>

        {/* Recipe Details */}
        <h1 className="text-5xl font-bold text-gray-800 mb-4">
          {recipe.name}
        </h1>

        <div className="space-y-4 text-lg">
          <div className="flex items-center gap-3">
            <span className="text-3xl">⏱️</span>
            <div>
              <p className="font-semibold text-gray-700">Cooking Time</p>
              <p className="text-gray-600">{recipe.time}</p>
            </div>
          </div>

          <div className="flex items-center gap-3">
            <span className="text-3xl">🏷️</span>
            <div>
              <p className="font-semibold text-gray-700">Category</p>
              <p className="text-gray-600 capitalize">{recipe.category}</p>
            </div>
          </div>
        </div>

        {/* Placeholder Content */}
        <div className="mt-8 p-6 bg-gray-50 rounded-lg">
          <h2 className="text-2xl font-bold text-gray-800 mb-4">
            Ingredients
          </h2>
          <p className="text-gray-600 italic">
            Ingredient list would go here...
          </p>
        </div>

        <div className="mt-8 p-6 bg-gray-50 rounded-lg">
          <h2 className="text-2xl font-bold text-gray-800 mb-4">
            Instructions
          </h2>
          <p className="text-gray-600 italic">
            Step-by-step instructions would go here...
          </p>
        </div>

        {/* Back Button */}
        <div className="mt-8">
          <a 
            href="/recipes"
            className="inline-block px-6 py-3 bg-blue-600 text-white rounded hover:bg-blue-700 transition"
          >
            ← Back to All Recipes
          </a>
        </div>
      </div>
    </main>
  );
}
```

### 🔑 Understanding the Code

**Line 10-13:** The magic part!
```typescript
export default async function RecipePage({
  params,
}: {
  params: Promise<{ id: string }>;
```

- `params` is an object containing URL parameters
- In Next.js 16, it's a **Promise**, so we must `await` it
- `{ id: string }` means we expect an `id` parameter

**Line 16:**
```typescript
const { id } = await params;
```
- Extracts the `id` from the URL
- If URL is `/recipes/1`, then `id = "1"`

**Test it:**
- Visit `http://localhost:3000/recipes/1` → Shows Pasta Carbonara
- Visit `http://localhost:3000/recipes/2` → Shows Chicken Curry
- Visit `http://localhost:3000/recipes/3` → Shows Beef Tacos
- Visit `http://localhost:3000/recipes/999` → Shows "Not Found"

---

## 🌮 Part 6: Creating Dynamic Category Pages

Now let's create pages for categories!

### Step 4: Create Dynamic Category Route

**Create folder and file:** `app/categories/[category]/page.tsx`

```typescript
// Recipe data
const recipes = [
  { id: '1', name: 'Pasta Carbonara', category: 'italian', time: '30 min' },
  { id: '2', name: 'Chicken Curry', category: 'indian', time: '45 min' },
  { id: '3', name: 'Beef Tacos', category: 'mexican', time: '25 min' },
];

export default async function CategoryPage({
  params,
}: {
  params: Promise<{ category: string }>;
}) {
  // Extract category from URL
  const { category } = await params;

  // Filter recipes by category
  const categoryRecipes = recipes.filter(
    (recipe) => recipe.category === category
  );

  return (
    <main className="min-h-screen p-8">
      {/* Navigation */}
      <nav className="mb-8 p-4 bg-gray-100 rounded">
        <ul className="flex gap-6">
          <li>
            <a href="/" className="text-blue-600 hover:underline">
              Home
            </a>
          </li>
          <li>
            <a href="/recipes" className="text-blue-600 hover:underline">
              All Recipes
            </a>
          </li>
        </ul>
      </nav>

      {/* Category Display (for success criteria) */}
      <div className="bg-green-50 border-l-4 border-green-600 p-4 mb-6">
        <p className="text-sm text-green-600 font-semibold">
          Category: {category}
        </p>
      </div>

      {/* Page Title */}
      <h1 className="text-4xl font-bold text-gray-800 mb-2 capitalize">
        {category} Recipes
      </h1>
      <p className="text-gray-600 mb-8">
        Found {categoryRecipes.length} {category} {categoryRecipes.length === 1 ? 'recipe' : 'recipes'}
      </p>

      {/* Recipe List */}
      {categoryRecipes.length > 0 ? (
        <div className="grid md:grid-cols-2 lg:grid-cols-3 gap-6">
          {categoryRecipes.map((recipe) => (
            <div 
              key={recipe.id}
              className="border border-gray-200 rounded-lg p-6 hover:shadow-lg transition"
            >
              <h2 className="text-2xl font-semibold text-gray-800 mb-2">
                {recipe.name}
              </h2>
              <p className="text-gray-600 mb-4">⏱️ {recipe.time}</p>
              <a 
                href={`/recipes/${recipe.id}`}
                className="inline-block px-4 py-2 bg-green-600 text-white rounded hover:bg-green-700 transition"
              >
                View Recipe
              </a>
            </div>
          ))}
        </div>
      ) : (
        <div className="text-center py-12">
          <p className="text-gray-600 text-xl">
            No recipes found in this category.
          </p>
          <a 
            href="/recipes"
            className="inline-block mt-4 px-6 py-3 bg-blue-600 text-white rounded hover:bg-blue-700"
          >
            View All Recipes
          </a>
        </div>
      )}

      {/* Category Navigation */}
      <div className="mt-12">
        <h2 className="text-2xl font-bold text-gray-800 mb-4">
          Other Categories
        </h2>
        <div className="flex gap-4">
          <a 
            href="/categories/italian"
            className="px-6 py-3 bg-green-100 text-green-700 rounded-lg hover:bg-green-200 transition"
          >
            🇮🇹 Italian
          </a>
          <a 
            href="/categories/indian"
            className="px-6 py-3 bg-orange-100 text-orange-700 rounded-lg hover:bg-orange-200 transition"
          >
            🇮🇳 Indian
          </a>
          <a 
            href="/categories/mexican"
            className="px-6 py-3 bg-red-100 text-red-700 rounded-lg hover:bg-red-200 transition"
          >
            🇲🇽 Mexican
          </a>
        </div>
      </div>
    </main>
  );
}
```

**Test it:**
- Visit `http://localhost:3000/categories/italian` → Shows Italian recipes
- Visit `http://localhost:3000/categories/indian` → Shows Indian recipes
- Visit `http://localhost:3000/categories/mexican` → Shows Mexican recipes

---

## ✅ Part 7: Verify Success Criteria

Let's check everything works!

### ✅ Test 1: Recipe ID Display
Visit `http://localhost:3000/recipes/1`

Should see: **"Recipe ID: 1"** in a blue box at the top

### ✅ Test 2: Category Display
Visit `http://localhost:3000/categories/italian`

Should see: **"Category: italian"** in a green box at the top

### ✅ Test 3: All Routes Work
Try these URLs - none should error:
- `/` → Homepage ✅
- `/recipes` → Recipe list ✅
- `/recipes/1` → Pasta Carbonara ✅
- `/recipes/2` → Chicken Curry ✅
- `/recipes/3` → Beef Tacos ✅
- `/categories/italian` → Italian recipes ✅
- `/categories/indian` → Indian recipes ✅
- `/categories/mexican` → Mexican recipes ✅

---

## 🌟 Bonus Challenge: Complete!

The links are already added! Notice:

**On `/recipes` page:**
```typescript
<a href={`/recipes/${recipe.id}`}>
  View Recipe
</a>
```

This creates dynamic links to each recipe!

---

## 📊 Your Final Structure

```
app/
├── page.tsx                     ✅ Homepage
├── recipes/
│   ├── page.tsx                 ✅ Recipe list
│   └── [id]/
│       └── page.tsx             ✅ Single recipe (dynamic)
└── categories/
    └── [category]/
        └── page.tsx             ✅ Category page (dynamic)
```

---

## 🎓 Key Concepts Recap

### 1. **Square Brackets = Dynamic**
```
[id]      → Captures any value as "id"
[category] → Captures any value as "category"
```

### 2. **Accessing Parameters (Next.js 16)**
```typescript
// Must be async and await params!
export default async function Page({
  params
}: {
  params: Promise<{ id: string }>
}) {
  const { id } = await params;  // ← Must await!
}
```

### 3. **Dynamic Links**
```typescript
<a href={`/recipes/${recipe.id}`}>
  Link to recipe
</a>
```

---

## 🐛 Common Issues & Solutions

**Problem:** "params is not iterable" or TypeScript errors  
**Solution:** Make sure params is typed as `Promise<{ id: string }>` and you `await` it

**Problem:** Page shows blank  
**Solution:** Check the folder is named `[id]` with brackets, not `{id}` or just `id`

**Problem:** Recipe not found  
**Solution:** Make sure your recipe IDs match the URLs (both are strings: `'1'` not `1`)

---

## 🎉 Congratulations!

You've mastered **Dynamic Routes**! You now know:

✅ How to create dynamic route segments with `[param]`  
✅ How to access URL parameters in Next.js 16  
✅ How to filter and display data based on routes  
✅ How to create dynamic links between pages  

You're officially ready for Day 3: Server vs Client Components 🚀

**Tomorrow:** You'll learn the difference between **Server Components** and **Client Components** - a game changer! 🚀
