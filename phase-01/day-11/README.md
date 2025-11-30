# Day 11: Font Optimization - Complete Beginner's Guide

Today you'll learn how to **load fonts efficiently** using Next.js's built-in font optimization with `next/font`!

---

## 🎯 What You'll Learn Today

- Why font optimization matters for performance
- How to use Google Fonts without external requests
- How to prevent FOUT (Flash of Unstyled Text)
- How to use multiple fonts in one project
- How to load local custom fonts
- Font display strategies and best practices

---

## 📚 Part 1: Understanding Font Loading Problems (20 minutes)

### The Problem with Traditional Font Loading

**Old way (bad!):**
```html
<!-- ❌ External request to Google -->
<link href="https://fonts.googleapis.com/css2?family=Inter" rel="stylesheet">
```

**Problems:**
- 🌐 **External request** - Slows down page load
- 👁️ **FOUT** - Flash of Unstyled Text (text appears, then changes)
- 🔄 **FOIT** - Flash of Invisible Text (blank space, then text appears)
- 📊 **Layout shift** - Page jumps when font loads
- 🔒 **Privacy** - Google tracks users
- 🚫 **Blocking** - Some countries block Google Fonts

### The Solution: next/font

```typescript
import { Inter } from 'next/font/google';

const inter = Inter({ subsets: ['latin'] });

// ✅ Self-hosted, optimized, zero layout shift!
```

**Benefits:**
- ✅ **Self-hosted** - Fonts served from your domain
- ✅ **Automatic optimization** - Only loads needed characters
- ✅ **Zero layout shift** - Size calculated at build time
- ✅ **No FOUT/FOIT** - CSS font-display: swap strategy
- ✅ **Privacy-friendly** - No external tracking
- ✅ **Better performance** - Cached with your site

---

## 🔍 Part 2: Understanding Font Terminology

### FOUT vs FOIT

```
FOUT (Flash of Unstyled Text):
┌────────────────────────┐
│ System font appears    │ ← Text visible immediately
│ (Arial/Times)          │
└────────────────────────┘
         ↓ (font loads)
┌────────────────────────┐
│ Custom font appears    │ ← Text changes (flash!)
│ (Inter/Playfair)       │
└────────────────────────┘

FOIT (Flash of Invisible Text):
┌────────────────────────┐
│                        │ ← Blank space (invisible)
│ [waiting for font...]  │
└────────────────────────┘
         ↓ (font loads)
┌────────────────────────┐
│ Custom font appears    │ ← Text suddenly appears
│ (Inter/Playfair)       │
└────────────────────────┘
```

**Next.js solution:** Uses `font-display: swap` to show text immediately with fallback, then swap to custom font smoothly.

---

## 📦 Part 3: Font Loading Strategies

### Font Display Options

```typescript
const inter = Inter({
  subsets: ['latin'],
  display: 'swap',  // Font display strategy
});
```

**Options:**
- **`swap`** (default, recommended): Show fallback, swap when ready
- **`optional`**: Use custom font if available quickly, otherwise fallback
- **`block`**: Brief invisible period, then show font
- **`fallback`**: Similar to swap but gives up after ~3 seconds
- **`auto`**: Browser decides

---

## ⚙️ Part 4: Setting Up Font Optimization

### Step 1: Update Root Layout with Fonts

**Update file:** `app/layout.tsx`

```typescript
import type { Metadata } from 'next';
import './globals.css';
import { Inter, Playfair_Display, Roboto_Mono } from 'next/font/google';

// Configure Inter (body text)
const inter = Inter({
  subsets: ['latin'],
  display: 'swap',
  variable: '--font-inter',
});

// Configure Playfair Display (headings)
const playfair = Playfair_Display({
  subsets: ['latin'],
  display: 'swap',
  variable: '--font-playfair',
});

// Configure Roboto Mono (code)
const robotoMono = Roboto_Mono({
  subsets: ['latin'],
  display: 'swap',
  variable: '--font-roboto-mono',
});

export const metadata: Metadata = {
  title: 'Font Optimization Demo',
  description: 'Learning Next.js Font Optimization',
};

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body className={`${inter.className} ${inter.variable} ${playfair.variable} ${robotoMono.variable}`}>
        {/* Header with Monospace Font */}
        <header className="bg-gray-800 text-white shadow-lg">
          <div className="max-w-7xl mx-auto px-4 py-4">
            <nav className={robotoMono.className}>
              <ul className="flex gap-6 text-sm">
                <li>
                  <a href="/" className="hover:text-blue-300 transition">
                    HOME
                  </a>
                </li>
                <li>
                  <a href="/typography" className="hover:text-blue-300 transition">
                    TYPOGRAPHY
                  </a>
                </li>
                <li>
                  <a href="/gallery" className="hover:text-blue-300 transition">
                    GALLERY
                  </a>
                </li>
              </ul>
            </nav>
          </div>
        </header>

        {/* Main Content */}
        <main>{children}</main>

        {/* Footer */}
        <footer className="bg-gray-800 text-white mt-12">
          <div className="max-w-7xl mx-auto px-4 py-8 text-center">
            <p className="text-sm">
              © 2024 Font Demo. Body text uses Inter. Code uses Roboto Mono.
            </p>
          </div>
        </footer>
      </body>
    </html>
  );
}
```

**Understanding the Code:**

- **Lines 6-10:** Configure Inter for body text
- **Lines 13-17:** Configure Playfair Display for headings
- **Lines 20-24:** Configure Roboto Mono for code/monospace
- **Line 37:** Apply Inter as default body font
- **Line 43:** Apply Roboto Mono to navigation
- **`variable`**: Creates CSS variables for use in Tailwind

---

### Step 2: Update Tailwind Config (Optional but Recommended)

**Update file:** `tailwind.config.ts`

```typescript
import type { Config } from 'tailwindcss';

const config: Config = {
  content: [
    './pages/**/*.{js,ts,jsx,tsx,mdx}',
    './components/**/*.{js,ts,jsx,tsx,mdx}',
    './app/**/*.{js,ts,jsx,tsx,mdx}',
  ],
  theme: {
    extend: {
      fontFamily: {
        sans: ['var(--font-inter)'],
        serif: ['var(--font-playfair)'],
        mono: ['var(--font-roboto-mono)'],
      },
    },
  },
  plugins: [],
};

export default config;
```

**Now you can use fonts in Tailwind:**
```html
<h1 className="font-serif">Heading in Playfair</h1>
<p className="font-sans">Body in Inter</p>
<code className="font-mono">Code in Roboto Mono</code>
```

---

## 📝 Part 5: Creating the Typography Showcase

### Step 3: Create Typography Page

**Create file:** `app/typography/page.tsx`

```typescript
import { Playfair_Display } from 'next/font/google';

const playfair = Playfair_Display({ subsets: ['latin'] });

export default function TypographyPage() {
  return (
    <div className="min-h-screen bg-gradient-to-br from-blue-50 to-purple-50 py-12 px-4">
      <div className="max-w-4xl mx-auto">
        {/* Header */}
        <div className="text-center mb-12">
          <h1 className={`${playfair.className} text-6xl font-bold text-gray-800 mb-4`}>
            Typography Showcase
          </h1>
          <p className="text-xl text-gray-600">
            Optimized fonts with next/font
          </p>
        </div>

        {/* Info Box */}
        <div className="bg-blue-50 border-l-4 border-blue-600 p-6 mb-8 rounded">
          <h3 className="text-lg font-bold text-blue-800 mb-2">
            ⚡ Font Optimization in Action
          </h3>
          <ul className="space-y-2 text-blue-700">
            <li>✅ Three fonts loaded efficiently</li>
            <li>✅ Self-hosted (no external requests)</li>
            <li>✅ Zero layout shift</li>
            <li>✅ No FOUT (Flash of Unstyled Text)</li>
            <li>✅ Automatic font subsetting</li>
          </ul>
        </div>

        {/* Font Showcase */}
        <div className="space-y-8">
          {/* Inter (Body Font) */}
          <div className="bg-white rounded-lg shadow-lg p-8">
            <div className="flex items-center justify-between mb-4">
              <h2 className="text-2xl font-bold text-gray-800">Inter</h2>
              <span className="px-3 py-1 bg-blue-100 text-blue-700 rounded-full text-sm font-semibold">
                Body Text
              </span>
            </div>
            <p className="text-gray-600 mb-4">
              <strong>Used for:</strong> Body text, paragraphs, general content
            </p>
            <div className="border-t pt-4">
              <p className="text-4xl font-light mb-2">The quick brown fox</p>
              <p className="text-2xl font-normal mb-2">The quick brown fox</p>
              <p className="text-xl font-medium mb-2">The quick brown fox</p>
              <p className="text-lg font-semibold mb-2">The quick brown fox</p>
              <p className="text-base font-bold">The quick brown fox</p>
            </div>
            <div className="mt-4 text-sm text-gray-500 font-mono bg-gray-50 p-3 rounded">
              Font: Inter | Loaded from: Self-hosted | Display: swap
            </div>
          </div>

          {/* Playfair Display (Heading Font) */}
          <div className="bg-white rounded-lg shadow-lg p-8">
            <div className="flex items-center justify-between mb-4">
              <h2 className={`${playfair.className} text-2xl font-bold text-gray-800`}>
                Playfair Display
              </h2>
              <span className="px-3 py-1 bg-purple-100 text-purple-700 rounded-full text-sm font-semibold">
                Headings
              </span>
            </div>
            <p className="text-gray-600 mb-4">
              <strong>Used for:</strong> Headings, titles, decorative text
            </p>
            <div className="border-t pt-4">
              <h1 className={`${playfair.className} text-5xl font-bold mb-4 text-gray-800`}>
                Elegant Heading
              </h1>
              <h2 className={`${playfair.className} text-4xl font-bold mb-4 text-gray-700`}>
                Beautiful Typography
              </h2>
              <h3 className={`${playfair.className} text-3xl font-bold mb-4 text-gray-600`}>
                Classic Serif Style
              </h3>
              <p className={`${playfair.className} text-xl text-gray-600 italic`}>
                "Typography is the craft of endowing human language with a durable visual form."
              </p>
            </div>
            <div className="mt-4 text-sm text-gray-500 font-mono bg-gray-50 p-3 rounded">
              Font: Playfair Display | Loaded from: Self-hosted | Display: swap
            </div>
          </div>

          {/* Roboto Mono (Code Font) */}
          <div className="bg-white rounded-lg shadow-lg p-8">
            <div className="flex items-center justify-between mb-4">
              <h2 className="text-2xl font-bold text-gray-800 font-mono">
                Roboto Mono
              </h2>
              <span className="px-3 py-1 bg-green-100 text-green-700 rounded-full text-sm font-semibold">
                Code
              </span>
            </div>
            <p className="text-gray-600 mb-4">
              <strong>Used for:</strong> Code blocks, monospace text, technical content
            </p>
            <div className="border-t pt-4">
              <div className="bg-gray-900 rounded-lg p-6 text-green-400 font-mono">
                <code className="block text-sm">
                  <span className="text-purple-400">import</span>{' '}
                  <span className="text-yellow-300">{'{ Inter }'}</span>{' '}
                  <span className="text-purple-400">from</span>{' '}
                  <span className="text-green-300">'next/font/google'</span>;
                  <br />
                  <br />
                  <span className="text-purple-400">const</span> inter ={' '}
                  <span className="text-blue-300">Inter</span>
                  {'({ '}
                  <span className="text-yellow-300">subsets</span>
                  {': ['}
                  <span className="text-green-300">'latin'</span>
                  {'] });'}
                </code>
              </div>
              <div className="mt-4 font-mono text-sm space-y-1">
                <p>0123456789</p>
                <p>ABCDEFGHIJKLMNOPQRSTUVWXYZ</p>
                <p>abcdefghijklmnopqrstuvwxyz</p>
                <p>{'!@#$%^&*()_+-=[]{}|;:,.<>?'}</p>
              </div>
            </div>
            <div className="mt-4 text-sm text-gray-500 font-mono bg-gray-50 p-3 rounded">
              Font: Roboto Mono | Loaded from: Self-hosted | Display: swap
            </div>
          </div>
        </div>

        {/* Font Pairing Example */}
        <div className="mt-12 bg-white rounded-lg shadow-lg p-8">
          <h2 className={`${playfair.className} text-4xl font-bold text-gray-800 mb-6`}>
            Font Pairing in Action
          </h2>
          <p className="text-lg text-gray-700 leading-relaxed mb-4">
            This is a paragraph using <strong>Inter</strong> for body text. It's highly legible 
            and works well for large amounts of content. The geometric shapes and even spacing 
            make it perfect for modern web design.
          </p>
          <p className="text-lg text-gray-700 leading-relaxed mb-4">
            Notice how the heading above uses <strong>Playfair Display</strong>, creating 
            a beautiful contrast between the elegant serif heading and the clean sans-serif body text.
          </p>
          <div className="bg-gray-900 rounded p-4 font-mono text-green-400 text-sm">
            // And here's some code using Roboto Mono
            <br />
            const message = "Perfect for technical content!";
          </div>
        </div>

        {/* Performance Stats */}
        <div className="mt-12 bg-green-50 border border-green-200 rounded-lg p-6">
          <h3 className="text-xl font-bold text-green-800 mb-4">
            📊 Performance Metrics
          </h3>
          <div className="grid md:grid-cols-3 gap-6">
            <div className="text-center">
              <p className="text-4xl font-bold text-green-600 mb-2">0ms</p>
              <p className="text-green-700 text-sm">FOUT/FOIT</p>
            </div>
            <div className="text-center">
              <p className="text-4xl font-bold text-green-600 mb-2">0</p>
              <p className="text-green-700 text-sm">Layout Shift</p>
            </div>
            <div className="text-center">
              <p className="text-4xl font-bold text-green-600 mb-2">100%</p>
              <p className="text-green-700 text-sm">Self-Hosted</p>
            </div>
          </div>
        </div>

        {/* How It Works */}
        <div className="mt-12 bg-white rounded-lg shadow-lg p-8">
          <h2 className="text-2xl font-bold text-gray-800 mb-6">
            🔧 How next/font Works
          </h2>
          <div className="space-y-4">
            <div className="flex gap-4">
              <div className="flex-shrink-0 w-10 h-10 bg-blue-100 rounded-full flex items-center justify-center text-blue-600 font-bold">
                1
              </div>
              <div>
                <h3 className="font-bold text-gray-800">Build Time</h3>
                <p className="text-gray-600 text-sm">
                  Next.js downloads fonts from Google Fonts and self-hosts them
                </p>
              </div>
            </div>
            <div className="flex gap-4">
              <div className="flex-shrink-0 w-10 h-10 bg-purple-100 rounded-full flex items-center justify-center text-purple-600 font-bold">
                2
              </div>
              <div>
                <h3 className="font-bold text-gray-800">Optimization</h3>
                <p className="text-gray-600 text-sm">
                  Fonts are subset to include only characters you need
                </p>
              </div>
            </div>
            <div className="flex gap-4">
              <div className="flex-shrink-0 w-10 h-10 bg-green-100 rounded-full flex items-center justify-center text-green-600 font-bold">
                3
              </div>
              <div>
                <h3 className="font-bold text-gray-800">Runtime</h3>
                <p className="text-gray-600 text-sm">
                  Fonts load from your domain with perfect CSS font-display strategy
                </p>
              </div>
            </div>
          </div>
        </div>

        {/* Code Example */}
        <div className="mt-8 bg-gray-800 rounded-lg p-6 text-white font-mono text-sm overflow-x-auto">
          <p className="text-green-400 mb-2">// app/layout.tsx</p>
          <pre className="text-gray-300">{`import { Inter, Playfair_Display } from 'next/font/google';

const inter = Inter({ subsets: ['latin'] });
const playfair = Playfair_Display({ subsets: ['latin'] });

export default function RootLayout({ children }) {
  return (
    <html>
      <body className={inter.className}>
        <h1 className={playfair.className}>Heading</h1>
        {children}
      </body>
    </html>
  );
}`}</pre>
        </div>
      </div>
    </div>
  );
}
```

---

## 🧪 Part 6: Testing Font Optimization

### Test 1: Check Self-Hosted Fonts

1. Open DevTools (F12)
2. Go to **Network** tab
3. Filter by **Font**
4. Visit `http://localhost:3000/typography`
5. Look at font requests

**Expected:**
- Fonts load from your domain (localhost)
- NOT from `fonts.googleapis.com` or `fonts.gstatic.com`
- Format: woff2 (most efficient)

---

### Test 2: Test for FOUT/FOIT

1. Open typography page
2. **Throttle network** (DevTools → Network → Slow 3G)
3. **Hard refresh** (Cmd+Shift+R or Ctrl+Shift+R)
4. Watch page load carefully

**Expected:**
- Text appears immediately (no blank space)
- No visible "flash" when fonts swap
- Smooth transition from fallback to custom font

---

### Test 3: Check Layout Shift

1. Open **Lighthouse** in DevTools
2. Run performance audit
3. Check **Cumulative Layout Shift (CLS)** score

**Expected:** CLS = 0 (zero layout shift!)

---

### Test 4: Verify Font Files

1. In Network tab, click on a font file
2. Check the **Headers** tab
3. Look at **Content-Type**

**Expected:** `font/woff2` (most efficient format)

---

## ✅ Part 7: Success Criteria Verification

### ✅ Three Different Fonts Display Correctly
- Inter for body text
- Playfair Display for headings
- Roboto Mono for code
- **Verified:** ✅

### ✅ No FOUT (Flash of Unstyled Text)
- Text appears immediately with fallback
- Smooth transition to custom font
- No visible flash or jump
- **Verified:** ✅

### ✅ Fonts Load from Self-Hosted Files
- Check Network tab
- Fonts from localhost, not Google
- Format: woff2
- **Verified:** ✅

### ✅ Zero Layout Shift
- Run Lighthouse audit
- CLS score = 0
- Page stable during font load
- **Verified:** ✅

---

## 🌟 Bonus Challenge: Add Local Custom Font

Let's add a custom font file!

### Step 4: Add Local Font File

**1. Create fonts folder:**
```
public/
└── fonts/
    └── CustomFont.woff2  ← Place your font file here
```

**2. Create font configuration file:**

**Create file:** `lib/fonts.ts`

```typescript
import { Inter, Playfair_Display, Roboto_Mono } from 'next/font/google';
import localFont from 'next/font/local';

// Google Fonts
export const inter = Inter({
  subsets: ['latin'],
  display: 'swap',
  variable: '--font-inter',
});

export const playfair = Playfair_Display({
  subsets: ['latin'],
  display: 'swap',
  variable: '--font-playfair',
});

export const robotoMono = Roboto_Mono({
  subsets: ['latin'],
  display: 'swap',
  variable: '--font-roboto-mono',
});

// Local Custom Font
export const customFont = localFont({
  src: [
    {
      path: '../public/fonts/CustomFont.woff2',
      weight: '400',
      style: 'normal',
    },
    // Add more weights/styles if you have them
    // {
    //   path: '../public/fonts/CustomFont-Bold.woff2',
    //   weight: '700',
    //   style: 'normal',
    // },
  ],
  variable: '--font-custom',
  display: 'swap',
});
```

**3. Update layout to use font configuration:**

**Update:** `app/layout.tsx`

```typescript
import type { Metadata } from 'next';
import './globals.css';
import { inter, playfair, robotoMono, customFont } from '@/lib/fonts';

export const metadata: Metadata = {
  title: 'Font Optimization Demo',
  description: 'Learning Next.js Font Optimization',
};

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body 
        className={`${inter.className} ${inter.variable} ${playfair.variable} ${robotoMono.variable} ${customFont.variable}`}
      >
        {/* ... rest of layout ... */}
      </body>
    </html>
  );
}
```

**4. Use custom font:**

```typescript
import { customFont } from '@/lib/fonts';

<h1 className={customFont.className}>
  Text in Custom Font
</h1>
```

---

## 📊 Font Loading Performance

### Traditional vs next/font

```
TRADITIONAL FONT LOADING:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
1. Browser requests HTML
2. HTML references fonts.googleapis.com
3. DNS lookup for googleapis
4. Download CSS from Google
5. CSS references fonts.gstatic.com
6. DNS lookup for gstatic
7. Download font files
8. Apply fonts

Total: 3-5 seconds, 2 DNS lookups, FOUT/FOIT

NEXT/FONT:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
1. Browser requests HTML
2. Fonts bundled with page
3. Apply fonts immediately

Total: < 1 second, 0 external requests, no FOUT/FOIT
```

---

## 🎓 Key Concepts Recap

### 1. **Import and Configure Google Fonts**
```typescript
import { Inter } from 'next/font/google';

const inter = Inter({
  subsets: ['latin'],        // Character set
  display: 'swap',           // Font display strategy
  variable: '--font-inter',  // CSS variable
});
```

### 2. **Apply Fonts**
```typescript
// As className
<body className={inter.className}>

// As CSS variable
<body className={`${inter.variable}`}>
<p className="font-[family-name:var(--font-inter)]">
```

### 3. **Load Local Fonts**
```typescript
import localFont from 'next/font/local';

const custom = localFont({
  src: './fonts/MyFont.woff2',
  display: 'swap',
});
```

### 4. **Multiple Fonts**
```typescript
// Import multiple
import { Inter, Roboto } from 'next/font/google';

// Configure each
const inter = Inter({ subsets: ['latin'] });
const roboto = Roboto({ weight: '400', subsets: ['latin'] });

// Apply separately
<body className={inter.className}>
  <h1 className={roboto.className}>Heading</h1>
</body>
```

---

## 🐛 Common Issues & Solutions

**Problem:** Fonts not loading  
**Solution:** Restart dev server after font changes

**Problem:** Wrong font weight  
**Solution:** Specify weight: `Roboto({ weight: ['400', '700'] })`

**Problem:** Custom font not found  
**Solution:** Check path is relative to the file importing it

**Problem:** Fonts look different than expected  
**Solution:** Check `subsets` - may need multiple subsets for special characters

---

## 🎉 Congratulations!

You've mastered **Font Optimization**! You now know:

✅ How to load Google Fonts efficiently with next/font  
✅ How to prevent FOUT and FOIT  
✅ How to use multiple fonts in one project  
✅ How to achieve zero layout shift  
✅ Bonus: How to load local custom fonts!  

**Your typography page now has:**
- ⚡ Self-hosted fonts (no external requests)
- 🎨 Three optimized fonts (Inter, Playfair, Roboto Mono)
- 📊 Zero layout shift
- 🚀 Perfect font display strategy
- 🔒 Privacy-friendly (no Google tracking)

  You're officially ready for Day 12: Metadata & SEO 🚀
