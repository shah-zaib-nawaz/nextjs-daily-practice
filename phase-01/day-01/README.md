# **Day 1 — Setup & Basic Routing (Complete Beginner’s Guide)**

Welcome to **Day 1** of your Next.js learning journey!
Today you’ll create your first Next.js project and build four pages using **file-based routing** — the core of how Next.js works.

---

# 🎯 **What You Will Learn**

By the end of this day, you will understand:

* How to create a new Next.js app
* How Next.js automatically creates routes using folders
* How to build simple pages (Home, About, Services, Contact)
* How to run your app locally
* How to add basic navigation

---

# 📚 **Part 1 — Understanding Next.js Setup (15 min)**

### 👉 What is Next.js?

Next.js is a **framework for building fast, modern websites** with:

* Automatic routing
* Server-side rendering
* Built-in optimizations
* Easy folder structure

You don’t configure anything — Next.js generates everything for you.

### 👉 What does `npx create-next-app` do?

It automatically:

* Downloads Next.js
* Creates all folders
* Sets up everything for you
* Installs dependencies

It’s like getting a complete “starter kit” for your website.

---

# 🛠️ **Part 2 — Create Your First Next.js App (20 min)**

### **Step 1 — Open Terminal**

Windows → `Win + R` → type `cmd` → Enter
Mac → Spotlight → search "terminal"

### **Step 2 — Create Project**

```bash
npx create-next-app@latest my-practice-app
```

### **Step 3 — Use These Settings**

```
✔ TypeScript? → Yes
✔ ESLint? → Yes
✔ Tailwind CSS? → Yes
✔ src/ directory? → No
✔ App Router? → Yes
✔ Turbopack? → Yes
✔ Customize import alias? → No
```

### **Step 4 — Enter Your Project**

```bash
cd my-practice-app
```

### **Step 5 — Start the Development Server**

```bash
npm run dev
```

Your website is now running at:

➡️ **[http://localhost:3000](http://localhost:3000)**

---

# 📂 **Part 3 — Understanding the Project Structure (15 min)**

```
my-practice-app/
│
├── app/                 ← All your pages live here
│   ├── page.tsx         ← Home page
│   ├── layout.tsx       ← Layout wrapper
│   └── globals.css      ← Styling
│
├── public/              ← Images and assets
├── package.json         ← Project config
└── next.config.ts       ← Next.js settings
```

### ⭐ Key Concept: File-Based Routing

Next.js turns folders into URLs:

| File/Folder             | Route       |
| ----------------------- | ----------- |
| `app/page.tsx`          | `/`         |
| `app/about/page.tsx`    | `/about`    |
| `app/services/page.tsx` | `/services` |
| `app/contact/page.tsx`  | `/contact`  |

You build websites **by making folders** — no routing code needed.

---

# 💻 **Part 4 — Build Your Website Pages (30 min)**

## **Page 1 — Home Page (`/`)**

File: `app/page.tsx`

```tsx
export default function HomePage() {
  return (
    <main className="min-h-screen p-8">
      <h1 className="text-4xl font-bold text-blue-600">
        Welcome to My Site
      </h1>
      <p className="mt-4 text-lg text-gray-700">
        This is my personal website built with Next.js!
      </p>
    </main>
  );
}
```

---

## **Page 2 — About Page (`/about`)**

Create folder + file:
`app/about/page.tsx`

```tsx
export default function AboutPage() {
  return (
    <main className="min-h-screen p-8">
      <h1 className="text-4xl font-bold text-green-600">
        About Me
      </h1>

      <div className="mt-6 space-y-4 text-gray-700">
        <p>Hi! I'm learning Next.js and building awesome websites.</p>
        <p>I love creating beautiful user experiences.</p>
        <p>Skills: React, Next.js, TypeScript, Tailwind CSS</p>
      </div>
    </main>
  );
}
```

---

## **Page 3 — Services Page (`/services`)**

Create file:
`app/services/page.tsx`

```tsx
export default function ServicesPage() {
  return (
    <main className="min-h-screen p-8">
      <h1 className="text-4xl font-bold text-purple-600 mb-8">
        My Services
      </h1>

      <div className="space-y-6">
        <div className="border-l-4 border-purple-600 pl-4">
          <h2 className="text-2xl font-semibold">Web Development</h2>
          <p className="text-gray-700 mt-2">
            Modern and responsive websites.
          </p>
        </div>

        <div className="border-l-4 border-purple-600 pl-4">
          <h2 className="text-2xl font-semibold">UI/UX Design</h2>
          <p className="text-gray-700 mt-2">
            Clean and beautiful interfaces.
          </p>
        </div>

        <div className="border-l-4 border-purple-600 pl-4">
          <h2 className="text-2xl font-semibold">Consulting</h2>
          <p className="text-gray-700 mt-2">
            Helping businesses choose the right tech stack.
          </p>
        </div>
      </div>
    </main>
  );
}
```

---

## **Page 4 — Contact Page (`/contact`)**

Create file:
`app/contact/page.tsx`

```tsx
export default function ContactPage() {
  return (
    <main className="min-h-screen p-8">
      <h1 className="text-4xl font-bold text-orange-600 mb-6">
        Contact Me
      </h1>

      <div className="space-y-4 text-lg">
        <p className="text-gray-700">I'd love to hear from you!</p>

        <div className="bg-orange-50 p-6 rounded-lg">
          <p className="font-semibold">📧 Email:</p>
          <p className="text-blue-600">yourname@example.com</p>
        </div>

        <div className="bg-orange-50 p-6 rounded-lg">
          <p className="font-semibold">💼 LinkedIn:</p>
          <p className="text-blue-600">linkedin.com/in/yourprofile</p>
        </div>

        <div className="bg-orange-50 p-6 rounded-lg">
          <p className="font-semibold">🐦 Twitter:</p>
          <p className="text-blue-600">@yourhandle</p>
        </div>
      </div>
    </main>
  );
}
```

---

# 🌟 **Bonus — Add Simple Navigation**

Add this inside each page, right after `<main>`:

```tsx
<nav className="mb-8 p-4 bg-gray-100 rounded">
  <ul className="flex gap-6">
    <li><a href="/" className="text-blue-600 hover:underline">Home</a></li>
    <li><a href="/about" className="text-blue-600 hover:underline">About</a></li>
    <li><a href="/services" className="text-blue-600 hover:underline">Services</a></li>
    <li><a href="/contact" className="text-blue-600 hover:underline">Contact</a></li>
  </ul>
</nav>
```

Tomorrow you will learn how to avoid repeating navigation using **layouts**.

---

# ✅ **Success Checklist**

### Make sure:

✔ Home page works
✔ About page works
✔ Services page works
✔ Contact page works
✔ URLs load correctly
✔ Navigation appears on all pages

---

# 🧠 **Common Fixes**

| Issue             | Solution                     |
| ----------------- | ---------------------------- |
| `npx` not working | Install Node.js              |
| Page not updating | Save file + wait 1–2s        |
| Port 3000 busy    | Use `npm run dev -- -p 3001` |

---

# 🎉 **Congratulations — Day 1 Completed!**

Today you learned:

* How to create a Next.js project
* How file-based routing works
* How to build multiple pages
* How to add basic navigation
* How to organize a real project

You're officially ready for **Day 2: File-Based Routing + Dynamic Routes** 🚀
