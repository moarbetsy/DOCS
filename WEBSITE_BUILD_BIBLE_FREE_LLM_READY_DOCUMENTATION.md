# Website Build Bible - FREE Version (LLM Ready) - Documentation

This document contains documentation for the Website Build Bible FREE Version guide, which provides a complete blueprint for building world-class websites using 100% free services.

**Last Updated:** November 12, 2025

**Source:** https://github.com/dricusbowser-alt/website-guide/blob/main/WEBSITE_BUILD_BIBLE_FREE_LLM_READY.md

**Documentation:** https://github.com/dricusbowser-alt/website-guide

---

## Table of Contents

1. [Introduction](#introduction)
2. [Quick Reference](#quick-reference)
3. [Technology Stack](#technology-stack)
4. [Required API Keys & Services](#required-api-keys--services)
5. [Step-by-Step Build Guide](#step-by-step-build-guide)
6. [Performance Optimization](#performance-optimization)
7. [Security Implementation](#security-implementation)
8. [Cost Optimization](#cost-optimization)
9. [Best Practices](#best-practices)
10. [Troubleshooting](#troubleshooting)

---

## Introduction

The Website Build Bible FREE Version is a comprehensive guide for building world-class websites using **100% free services**. This guide consolidates best practices from analyzing industry-leading websites (Linear.app, Stripe.com, Vercel.com, Clerk.com) and provides a complete blueprint that costs $0/month.

### Key Differences from Paid Version

- **Authentication:** NextAuth.js (free) instead of Clerk
- **Error Monitoring:** Sentry Free Tier (5k errors/month) or removed
- **Analytics:** Vercel Analytics + Speed Insights (free on Vercel)
- **Rate Limiting:** In-memory or removed (no Upstash Redis)
- **Hosting:** Vercel Free Tier (unlimited personal projects)

### Target Metrics

| Metric Category | Target | Measurement Tool |
|----------------|--------|------------------|
| LCP | < 2.5s | Lighthouse, WebPageTest |
| INP | < 200ms | Lighthouse, WebPageTest |
| CLS | < 0.1 | Lighthouse, WebPageTest |
| Network Requests | < 150 | Chrome DevTools |
| JS Bundle (gz) | < 170 kB | Bundle Analyzer |
| Security Score | A+ | SecurityHeaders.com |
| Monthly Cost | $0 | All free tier services |

---

## Quick Reference

### Performance Targets

- **LCP:** < 2.5s
- **INP:** < 200ms
- **CLS:** < 0.1
- **Network Requests:** < 150
- **JS Bundle:** < 170 kB gz

### Security Targets

- **Security Score:** A+
- **CSP:** Nonce-based or SHA-256
- **Security Headers:** 10+

### Cost

- **Monthly Cost:** $0 (100% free tier services)

---

## Technology Stack

### Core Framework

```bash
- Next.js 14+ (App Router) - Free
- React 18+ - Free
- TypeScript (strict mode) - Free
- Tailwind CSS - Free
```

### Authentication

```bash
- NextAuth.js v5 (Auth.js) - Free, open-source
- OAuth providers (GitHub, Google) - Free
- Database adapter for sessions - Free
```

### Hosting & CDN

```bash
- Vercel (free tier) - Unlimited personal projects
- Alternative: Netlify (free tier), Cloudflare Pages (free)
```

### Monitoring & Analytics

```bash
# Free Stack
- Vercel Analytics (free on Vercel)
- Vercel Speed Insights (free on Vercel)
- Sentry Free Tier (5k errors/month, optional)
```

### Database

```bash
# Free Options
- Supabase PostgreSQL (500MB free)
- Neon PostgreSQL (0.5GB free)
- SQLite (local, unlimited)
```

### Image Optimization

```bash
# Free Options
- Next.js Image Optimization (free on Vercel)
- Cloudflare Images (free tier: 100k images/month)
```

---

## Required API Keys & Services

### Authentication Service (NextAuth.js)

**Service:** NextAuth.js with OAuth Providers  
**Cost:** Free

#### Option A: GitHub OAuth (Recommended)

1. **Register at:** https://github.com/settings/developers
2. **Steps:**
   - Click "New OAuth App"
   - Application name: `Your App Name`
   - Homepage URL: `http://localhost:3000` (for dev)
   - Authorization callback URL: `http://localhost:3000/api/auth/callback/github`
   - Click "Register application"
3. **Required Keys:**
   - `GITHUB_CLIENT_ID` - Found in OAuth App settings
   - `GITHUB_CLIENT_SECRET` - Click "Generate a new client secret"

#### Option B: Google OAuth

1. **Register at:** https://console.cloud.google.com/apis/credentials
2. **Steps:**
   - Create new project (or select existing)
   - Go to "Credentials" → "Create Credentials" → "OAuth client ID"
   - Application type: "Web application"
   - Authorized redirect URIs: `http://localhost:3000/api/auth/callback/google`
3. **Required Keys:**
   - `GOOGLE_CLIENT_ID` - Found in credentials
   - `GOOGLE_CLIENT_SECRET` - Found in credentials

**NextAuth Secret (Required for all options):**
- Generate: `openssl rand -base64 32`
- Or use: https://generate-secret.vercel.app/32
- **Key:** `NEXTAUTH_SECRET`

**NextAuth URL:**
- Development: `NEXTAUTH_URL=http://localhost:3000`
- Production: `NEXTAUTH_URL=https://yourdomain.com`

### Database (Choose One)

#### Option A: PostgreSQL with Supabase (Recommended)

**Service:** Supabase  
**Cost:** Free (500MB database, 2GB bandwidth/month)  
**Register at:** https://supabase.com

**Required Keys:**
- `DATABASE_URL` - Connection string
- `NEXT_PUBLIC_SUPABASE_URL` - Project URL
- `NEXT_PUBLIC_SUPABASE_ANON_KEY` - Anon/public key
- `SUPABASE_SERVICE_ROLE_KEY` - Service role key

#### Option B: PostgreSQL with Neon

**Service:** Neon  
**Cost:** Free (0.5GB storage, unlimited projects)  
**Register at:** https://neon.tech

**Required Keys:**
- `DATABASE_URL` - Connection string from dashboard

#### Option C: SQLite (No Registration)

**Service:** SQLite (via better-sqlite3)  
**Cost:** Free (local file-based)  
**No registration needed**

### Error Monitoring (Optional)

**Service:** Sentry  
**Cost:** Free (5,000 errors/month)  
**Register at:** https://sentry.io/signup/

**Required Keys:**
- `NEXT_PUBLIC_SENTRY_DSN` - DSN from project settings
- `SENTRY_ORG` - Organization slug
- `SENTRY_PROJECT` - Project slug
- `SENTRY_AUTH_TOKEN` - Auth token

**Note:** Can be skipped if you don't need error monitoring

### Hosting & Analytics (Vercel)

**Service:** Vercel  
**Cost:** Free (unlimited personal projects)  
**Register at:** https://vercel.com/signup

**Required Keys:**
- None (automatically configured when deployed)
- Analytics work out of the box on Vercel

---

## Step-by-Step Build Guide

### Phase 1: Project Setup

#### Step 1.1: Initialize Next.js Project

```bash
npx create-next-app@latest my-perfect-website \
  --typescript \
  --tailwind \
  --app \
  --src-dir \
  --import-alias "@/*"

cd my-perfect-website
```

#### Step 1.2: Install Core Dependencies

```bash
# Authentication (NextAuth.js - Free)
npm install next-auth@beta
npm install @auth/prisma-adapter  # If using Prisma
# OR
npm install @auth/pg-adapter      # If using PostgreSQL directly

# Database (Choose one)
npm install @prisma/client prisma  # If using Prisma
npm install pg                      # If using PostgreSQL directly
npm install better-sqlite3         # If using SQLite

# Analytics (Free on Vercel)
npm install @vercel/speed-insights
npm install @vercel/analytics

# Error Monitoring (Optional - Free Tier)
npm install @sentry/nextjs

# UI & Styling
npm install tailwindcss-animate
npm install clsx tailwind-merge
npm install lucide-react

# Development dependencies
npm install -D @types/node
npm install -D @next/bundle-analyzer
```

#### Step 1.3: Project Structure

```
my-perfect-website/
├── src/
│   ├── app/
│   │   ├── (marketing)/          # Public marketing pages
│   │   │   ├── layout.tsx
│   │   │   ├── page.tsx          # Homepage
│   │   │   ├── pricing/
│   │   │   └── blog/
│   │   ├── (dashboard)/          # Protected dashboard
│   │   │   ├── layout.tsx
│   │   │   └── dashboard/
│   │   ├── api/
│   │   │   └── auth/
│   │   │       └── [...nextauth]/
│   │   │           └── route.ts  # NextAuth route handler
│   │   ├── layout.tsx            # Root layout
│   │   ├── sitemap.ts            # Dynamic sitemap
│   │   ├── robots.ts             # Robots.txt
│   │   └── globals.css
│   ├── components/
│   │   ├── ui/                   # Reusable UI components
│   │   ├── marketing/            # Marketing-specific components
│   │   └── dashboard/            # Dashboard components
│   ├── lib/
│   │   ├── utils.ts
│   │   ├── auth.ts               # NextAuth configuration
│   │   ├── db.ts                 # Database connection
│   │   └── sentry.ts             # Sentry config (optional)
│   └── middleware.ts            # NextAuth middleware + security headers
├── prisma/
│   └── schema.prisma            # Prisma schema (if using Prisma)
├── public/
│   ├── manifest.webmanifest     # PWA manifest
│   └── icons/                   # PWA icons
├── .env.local                   # Environment variables
├── next.config.js
├── tailwind.config.ts
└── package.json
```

### Phase 2: Authentication Setup (NextAuth.js)

#### Step 2.1: Configure Environment Variables

**File:** `.env.local`
```env
# NextAuth.js Configuration
NEXTAUTH_SECRET=your_generated_secret_here
NEXTAUTH_URL=http://localhost:3000

# OAuth Provider (GitHub - Recommended)
GITHUB_CLIENT_ID=your_github_client_id
GITHUB_CLIENT_SECRET=your_github_client_secret

# Database
DATABASE_URL=your_database_connection_string

# Optional: Sentry
NEXT_PUBLIC_SENTRY_DSN=your_sentry_dsn
```

#### Step 2.2: Configure NextAuth.js

**File:** `src/lib/auth.ts`
```typescript
import NextAuth from "next-auth"
import GitHub from "next-auth/providers/github"
import { PrismaAdapter } from "@auth/prisma-adapter"
import { prisma } from "@/lib/db"

export const { handlers, auth, signIn, signOut } = NextAuth({
  adapter: PrismaAdapter(prisma),
  providers: [
    GitHub({
      clientId: process.env.GITHUB_CLIENT_ID,
      clientSecret: process.env.GITHUB_CLIENT_SECRET,
    }),
  ],
  callbacks: {
    async session({ session, user }) {
      if (session.user) {
        session.user.id = user.id
      }
      return session
    },
  },
})
```

#### Step 2.3: Create NextAuth Route Handler

**File:** `src/app/api/auth/[...nextauth]/route.ts`
```typescript
import { handlers } from "@/lib/auth"

export const { GET, POST } = handlers
```

#### Step 2.4: Middleware for Route Protection & Security

**File:** `src/middleware.ts`
```typescript
import { auth } from "@/lib/auth"
import { NextResponse } from 'next/server'
import type { NextRequest } from 'next/server'
import { randomBytes } from 'crypto'

export default auth((req) => {
  const response = NextResponse.next()

  // Security headers
  const nonce = randomBytes(16).toString('base64')

  response.headers.set('Strict-Transport-Security', 'max-age=63072000; includeSubDomains; preload')
  response.headers.set('X-Content-Type-Options', 'nosniff')
  response.headers.set('Referrer-Policy', 'strict-origin-when-cross-origin')
  response.headers.set('X-DNS-Prefetch-Control', 'on')
  response.headers.set('Permissions-Policy', 'camera=(), microphone=(), geolocation=(), payment=(), web-share=(), storage-access=(), browsing-topics=()')
  response.headers.set('Cross-Origin-Opener-Policy', 'same-origin-allow-popups')
  response.headers.set('Cross-Origin-Resource-Policy', 'same-site')

  // Enhanced CSP with nonces
  const cspHeader = [
    "default-src 'self'",
    "base-uri 'self'",
    "form-action 'self'",
    "object-src 'none'",
    "frame-ancestors 'self'",
    `script-src 'self' 'nonce-${nonce}' https://*.vercel-insights.com`,
    "style-src 'self' 'unsafe-inline'",
    "img-src 'self' data: blob: https:",
    "font-src 'self' data: https://fonts.gstatic.com",
    "connect-src 'self' https://*.vercel-insights.com",
    'upgrade-insecure-requests',
  ].join('; ')

  response.headers.set('Content-Security-Policy', cspHeader)
  response.headers.set('x-nonce', nonce)

  // Protect dashboard routes
  if (req.nextUrl.pathname.startsWith('/dashboard') && !req.auth) {
    return NextResponse.redirect(new URL('/sign-in', req.url))
  }

  return response
})

export const config = {
  matcher: ['/((?!.+\\.[\\w]+$|_next).*)', '/', '/(api|trpc)(.*)'],
}
```

---

## Performance Optimization

### Minimize Network Requests

**Target:** <150 total requests

**Strategies:**
- Consolidate third-party scripts
- Use dynamic imports for heavy libraries
- Optimize bundle sizes
- Implement smart code splitting

### Advanced Image Optimization

**Next.js Image Optimization (Free on Vercel):**
```typescript
<Image
  src="/hero-image.webp"
  alt="Hero"
  width={1200}
  height={600}
  priority
  fetchPriority="high"
  placeholder="blur"
  quality={85}
/>
```

### Smart Caching Strategy

**Stale-While-Revalidate Pattern:**
```typescript
// In API routes or data fetching
export const revalidate = 1800 // 30 minutes

// Or with fetch
const data = await fetch(url, {
  next: {
    revalidate: 1800,
    tags: ['data']
  }
})
```

### Bundle Optimization

**Dynamic Imports:**
```typescript
import dynamic from 'next/dynamic'

const HeavyComponent = dynamic(() => import('./HeavyComponent'), {
  loading: () => <Skeleton />,
  ssr: false,
})
```

---

## Security Implementation

### CSP Implementation

**Option A: Nonces (Flexible, per-request)**
- Generated per-request in middleware
- Passed via headers
- More flexible for modern frameworks

**Option B: SHA-256 Hashes (Static)**
```typescript
const script = `console.log('hello')`
const hash = crypto.createHash('sha256').update(script).digest('base64')

// Add to CSP
script-src 'self' 'sha256-${hash}' 'sha256-another-hash' 'report-sample'
```

### Cross-Origin Policies

```typescript
// In middleware
response.headers.set(
  'Cross-Origin-Opener-Policy',
  'same-origin-allow-popups'
)
```

### Rate Limiting (In-Memory - Free)

**In-Memory Rate Limiting:**
```typescript
// Simple in-memory rate limiter
const rateLimitMap = new Map<string, { count: number; resetTime: number }>()

export function rateLimit(ip: string, limit: number = 10, window: number = 60000): boolean {
  const now = Date.now()
  const record = rateLimitMap.get(ip)

  if (!record || now > record.resetTime) {
    rateLimitMap.set(ip, { count: 1, resetTime: now + window })
    return true
  }

  if (record.count >= limit) {
    return false
  }

  record.count++
  return true
}

// In API route
export async function POST(request: Request) {
  const ip = request.headers.get('x-forwarded-for') || 'unknown'

  if (!rateLimit(ip, 10, 60000)) {
    return new Response('Rate limit exceeded', { status: 429 })
  }

  // Your logic
}
```

---

## Cost Optimization

### Free Tier Services Summary

**All Services Used:**
- ✅ NextAuth.js: Free (open-source)
- ✅ Vercel Hosting: Free (unlimited personal projects)
- ✅ Vercel Analytics: Free (on Vercel)
- ✅ Vercel Speed Insights: Free (on Vercel)
- ✅ Supabase/Neon: Free (500MB-0.5GB database)
- ✅ Sentry: Free (5k errors/month, optional)
- ✅ GitHub OAuth: Free
- ✅ Google OAuth: Free

**Total Monthly Cost: $0**

### Monitor Free Tier Limits

**Vercel Free Tier:**
- 100GB bandwidth/month
- Unlimited requests
- Unlimited personal projects

**Supabase Free Tier:**
- 500MB database
- 2GB bandwidth/month
- 50k monthly active users

**Sentry Free Tier:**
- 5,000 errors/month
- 10k performance units/month

---

## Best Practices

### 1. Use Caching

Add `maxAge` parameter for faster repeated requests:
```typescript
result = mcp_firecrawl_scrape(
    url="https://example.com",
    maxAge=172800000  # 2 days
)
```

### 2. Limit Crawl Depth

Set appropriate `maxDiscoveryDepth` and `limit`:
```typescript
job_id = mcp_firecrawl_crawl(
    url="https://example.com",
    maxDiscoveryDepth=2,
    limit=50
)
```

### 3. Use Main Content Only

Enable `onlyMainContent` for cleaner results:
```typescript
result = mcp_firecrawl_scrape(
    url="https://example.com",
    onlyMainContent=True
)
```

### 4. Handle Rate Limits

Implement retry logic with exponential backoff:
```typescript
async function fetchWithRetry(url: string, maxRetries = 3) {
  for (let i = 0; i < maxRetries; i++) {
    try {
      const response = await fetch(url)
      if (response.ok) return response
    } catch (error) {
      if (i === maxRetries - 1) throw error
      await new Promise(resolve => setTimeout(resolve, 2 ** i * 1000))
    }
  }
}
```

---

## Troubleshooting

### Common Issues

#### Scrape Returns Empty Content

1. Check if the page requires JavaScript rendering
2. Try adding `waitFor` parameter
3. Use `actions` to interact with the page
4. Check if the page blocks scrapers

#### Authentication Not Working

1. Verify API keys are correct
2. Check NextAuth configuration
3. Ensure database connection is working
4. Check middleware configuration

#### Performance Issues

1. Check bundle sizes with analyzer
2. Optimize images
3. Implement caching
4. Reduce third-party scripts

---

## Additional Resources

- **Full Guide:** https://github.com/dricusbowser-alt/website-guide/blob/main/WEBSITE_BUILD_BIBLE_FREE_LLM_READY.md
- **Next.js Documentation:** https://nextjs.org/docs
- **NextAuth.js Documentation:** https://next-auth.js.org
- **Vercel Documentation:** https://vercel.com/docs
- **Supabase Documentation:** https://supabase.com/docs

---

*End of Documentation*








