# Website Build Bible - LLM Ready Documentation

This document contains documentation for the Website Build Bible guide, which provides a complete blueprint for building world-class websites using best practices from industry leaders.

**Last Updated:** November 12, 2025

**Source:** https://github.com/dricusbowser-alt/website-guide/blob/main/WEBSITE_BUILD_BIBLE_LLM_READY.md

**Documentation:** https://github.com/dricusbowser-alt/website-guide

---

## Table of Contents

1. [Introduction](#introduction)
2. [Quick Reference](#quick-reference)
3. [Technology Stack](#technology-stack)
4. [Step-by-Step Build Guide](#step-by-step-build-guide)
5. [Performance Optimization](#performance-optimization)
6. [Security Implementation](#security-implementation)
7. [Best Practices from Industry Leaders](#best-practices-from-industry-leaders)
8. [Cost Optimization](#cost-optimization)
9. [Troubleshooting](#troubleshooting)

---

## Introduction

The Website Build Bible consolidates best practices from analyzing industry-leading websites (Linear.app, Stripe.com, Vercel.com, Clerk.com) to provide a complete blueprint for building world-class websites.

### Key Findings from Analysis

**Linear.app (Performance Leader):**
- Network Requests: ~104 requests (verified Nov 2025)
- Image Delivery: Cloudflare Image Delivery
- Caching: Smart caching with stale-while-revalidate
- CSP: Uses `'unsafe-inline'` (practical compromise)

**Stripe.com (Security Leader):**
- CSP Implementation: SHA-256 hashes (actual implementation)
- COOP: `same-origin-allow-popups` with reporting
- Security Approach: Deny-by-default
- No `'unsafe-inline'` or `'unsafe-eval'` in script-src

**Vercel.com (Optimization Reference):**
- CSP: Permissive for dev flexibility
- Uses: `'unsafe-eval'` and `'unsafe-inline'` (practical for Next.js)
- Analytics: Consolidated stack (Vercel Analytics + Speed Insights + Sentry)

**Clerk.com (Modern SaaS Architecture):**
- Security: Minimal security headers
- CSP: Not implemented
- Uses: X-XSS-Protection (legacy approach)

### Target Metrics

| Metric Category | Target | Measurement Tool |
|----------------|--------|------------------|
| LCP | < 2.5s | Lighthouse, WebPageTest |
| INP | < 200ms | Lighthouse, WebPageTest |
| CLS | < 0.1 | Lighthouse, WebPageTest |
| Network Requests | < 150 | Chrome DevTools |
| JS Bundle (gz) | < 170 kB | Bundle Analyzer |
| Security Score | A+ | SecurityHeaders.com |
| Cost Reduction | 30-50% | Service dashboards |

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

### Cost Savings

- **Target:** 30-50% reduction through optimization

---

## Technology Stack

### Core Framework

```bash
- Next.js 14+ (App Router)
- React 18+
- TypeScript (strict mode)
- Tailwind CSS
```

### Authentication

```bash
- Clerk (or similar: Auth0, Supabase Auth)
- Middleware for route protection
```

### Hosting & CDN

```bash
- Vercel (optimal for Next.js)
- Alternative: Netlify, Cloudflare Pages
```

### Monitoring & Analytics

```bash
# Consolidated Stack
- Vercel Analytics + Speed Insights
- Sentry (error monitoring)
- Optional: PostHog (product analytics)
```

### Image Optimization

```bash
# Learn from Linear.app
- Cloudflare Image Delivery (or)
- Next.js Image Optimization
- Vercel Blob Storage
```

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
# Core dependencies
npm install @clerk/nextjs
npm install @sentry/nextjs
npm install @vercel/speed-insights
npm install @vercel/analytics

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
│   │   ├── api/                  # API routes
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
│   │   ├── analytics.ts          # Analytics setup
│   │   └── sentry.ts             # Sentry config
│   └── middleware.ts            # Clerk middleware + security headers
├── public/
│   ├── manifest.webmanifest     # PWA manifest
│   └── icons/                   # PWA icons
├── next.config.js
├── tailwind.config.ts
└── package.json
```

### Phase 2: Authentication Setup

#### Step 2.1: Configure Clerk

**File:** `.env.local`
```env
NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=pk_test_...
CLERK_SECRET_KEY=sk_test_...
NEXT_PUBLIC_CLERK_SIGN_IN_URL=/sign-in
NEXT_PUBLIC_CLERK_SIGN_UP_URL=/sign-up
NEXT_PUBLIC_CLERK_AFTER_SIGN_IN_URL=/dashboard
NEXT_PUBLIC_CLERK_AFTER_SIGN_UP_URL=/dashboard
```

#### Step 2.2: Root Layout with Clerk Provider

**File:** `src/app/layout.tsx`
```typescript
import { ClerkProvider } from '@clerk/nextjs'
import { Inter } from 'next/font/google'
import './globals.css'
import { Analytics } from '@vercel/analytics/next'
import { SpeedInsights } from '@vercel/speed-insights/next'

const inter = Inter({
  subsets: ['latin'],
  display: 'swap',
  preload: true,
  variable: '--font-inter',
  fallback: ['system-ui', 'arial'],
})

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <ClerkProvider>
      <html lang="en">
        <head>
          <link rel="preconnect" href="https://clerk.clerk.com" crossOrigin="anonymous" />
        </head>
        <body className={inter.className}>
          {children}
          <Analytics />
          <SpeedInsights />
        </body>
      </html>
    </ClerkProvider>
  )
}
```

#### Step 2.3: Middleware for Route Protection & Security

**File:** `src/middleware.ts`
```typescript
import { authMiddleware } from '@clerk/nextjs'
import { NextResponse } from 'next/server'
import type { NextRequest } from 'next/server'
import { randomBytes } from 'crypto'

export default authMiddleware({
  publicRoutes: [
    '/',
    '/pricing',
    '/blog(.*)',
  ],
  ignoredRoutes: ['/api/webhooks(.*)'],
  async afterAuth(auth, req) {
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
      `script-src 'self' 'nonce-${nonce}' https://*.clerk.com https://*.vercel-insights.com`,
      "style-src 'self' 'unsafe-inline'",
      "img-src 'self' data: blob: https:",
      "font-src 'self' data:",
      "connect-src 'self' https://*.clerk.com https://*.ingest.sentry.io",
      "frame-src https://*.clerk.com",
      'upgrade-insecure-requests',
    ].join('; ')

    response.headers.set('Content-Security-Policy', cspHeader)
    response.headers.set('x-nonce', nonce)

    return response
  },
})

export const config = {
  matcher: ['/((?!.+\\.[\\w]+$|_next).*)', '/', '/(api|trpc)(.*)'],
}
```

---

## Performance Optimization

### Minimize Network Requests

**Target:** <150 total requests  
**Source:** Linear.app pattern

**Strategies:**
- Consolidate third-party scripts
- Use dynamic imports for heavy libraries
- Optimize bundle sizes
- Implement smart code splitting

### Advanced Image Optimization

**Source:** Linear.app pattern

**Cloudflare Image Delivery Pattern:**
```typescript
const imageUrl = `https://your-domain.com/cdn-cgi/imagedelivery/${accountHash}/${imageId}/f=auto,dpr=2,q=95,fit=scale-down,metadata=none`

// Or use Next.js Image with optimization
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

**Source:** Linear.app pattern

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

**Cache Headers:**
```typescript
response.headers.set(
  'Cache-Control',
  's-maxage=1800, stale-while-revalidate=31534200'
)
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

**Tree Shaking:**
```typescript
// Use named imports
import { specificFunction } from 'large-library'
// Instead of: import * from 'large-library'
```

---

## Security Implementation

### CSP Implementation: Nonces vs SHA-256

**Source:** Stripe.com uses SHA-256 hashes (verified Nov 2025)

**Option A: Nonces (Flexible, per-request)**
- Generated per-request in middleware
- Passed via headers
- More flexible for modern frameworks

**Option B: SHA-256 Hashes (Static, Stripe's approach)**
```typescript
const script = `console.log('hello')`
const hash = crypto.createHash('sha256').update(script).digest('base64')

// Add to CSP
script-src 'self' 'sha256-${hash}' 'sha256-another-hash' 'report-sample'
```

**Recommendation:**
- Use **nonces** if you have dynamic inline scripts or need flexibility
- Use **SHA-256 hashes** if you have static inline scripts and want to match Stripe's approach
- Both are secure; nonces are more flexible for modern frameworks

### Cross-Origin Policies

**Source:** Stripe.com pattern

**Important:** COEP (`require-corp`) breaks any third-party that doesn't serve CORP/CORS headers. Only enable after auditing all third-party resources.

```typescript
// In middleware
response.headers.set(
  'Cross-Origin-Opener-Policy',
  'same-origin-allow-popups' // Safe to enable if auth uses popups
)

// Only enable COEP after audit
// response.headers.set(
//   'Cross-Origin-Embedder-Policy',
//   'require-corp'
// )
```

### Rate Limiting

**Package:** `@upstash/ratelimit @upstash/redis`

**Implementation:**
```typescript
import { Ratelimit } from '@upstash/ratelimit'
import { Redis } from '@upstash/redis'

const ratelimit = new Ratelimit({
  redis: Redis.fromEnv(),
  limiter: Ratelimit.slidingWindow(10, '10 s'),
})

// In API route
export async function POST(request: Request) {
  const ip = request.headers.get('x-forwarded-for') || 'unknown'
  const { success } = await ratelimit.limit(ip)

  if (!success) {
    return new Response('Rate limit exceeded', { status: 429 })
  }

  // Your logic
}
```

---

## Best Practices from Industry Leaders

### From Linear.app (Performance Leader)

**Verified:** November 2025

1. **Minimize Network Requests**
   - Actual: ~104 requests on homepage
   - Consolidate third-party services
   - Optimize bundle sizes

2. **Advanced Image Optimization**
   - Uses Cloudflare Image Delivery (`cdn-cgi/imagedelivery/`)
   - Automatic format selection (AVIF, WebP)
   - DPR-aware serving with query params

3. **Smart Caching**
   - `s-maxage=1800, stale-while-revalidate=31534200`
   - Better cache hit rates
   - Instant responses

4. **CSP Implementation (Reality Check)**
   - Still uses `'unsafe-inline'` in script-src (practical compromise)
   - Uses `frame-ancestors 'self' https://cms.linear.app`
   - Comprehensive `connect-src` allowlist
   - Uses `report-uri` for CSP violation tracking

### From Stripe.com (Security Leader)

**Verified:** November 2025

1. **SHA-256 CSP Hashes (Actual Implementation)**
   - Uses SHA-256 hashes for inline scripts (not nonces)
   - No `'unsafe-inline'` or `'unsafe-eval'` in script-src
   - Multiple SHA-256 hashes for different inline scripts
   - Uses `report-sample` directive

2. **Cross-Origin Policies**
   - COOP: `same-origin-allow-popups; report-to="wsp_coop"`
   - No COEP (not enabled)
   - Uses both `frame-ancestors` in CSP AND `X-Frame-Options: SAMEORIGIN`

3. **CSP Configuration**
   - `base-uri 'none'` (very restrictive)
   - `default-src 'none'` (deny-by-default)
   - Explicit allowlists for each directive
   - `report-uri` for violation tracking

### From Vercel.com (Optimization Reference)

**Verified:** November 2025

1. **Consolidated Analytics Stack**
   - Uses Vercel Analytics + Speed Insights
   - Uses Sentry for error monitoring
   - Uses Google Analytics and LinkedIn ads tracking

2. **Security Posture (Reality Check)**
   - Uses `'unsafe-eval'` and `'unsafe-inline'` in script-src (practical for Next.js)
   - Uses `frame-ancestors` with multiple allowed origins
   - No HSTS header (likely handled at CDN level)
   - Uses `X-Frame-Options: DENY` alongside `frame-ancestors`

3. **CSP Configuration**
   - Very permissive CSP for development flexibility
   - Allows `chrome-extension://*` and `localhost:*`
   - Comprehensive allowlists for all directives

---

## Cost Optimization

### Consolidate Analytics (30-50% savings)

**Before:**
- Segment ($120/month)
- GA4 (free)
- PostHog ($450/month)
- HubSpot ($20/month)
- **Total: ~$590/month**

**After:**
- Vercel Analytics (included)
- Sentry ($26/month)
- PostHog (optional, $0-450/month)
- **Total: ~$26-476/month**

### Implement Caching Strategy

```typescript
// React Query with proper cache times
const { data } = useQuery({
  queryKey: ['users'],
  queryFn: fetchUsers,
  staleTime: 5 * 60 * 1000, // 5 minutes
  gcTime: 10 * 60 * 1000, // 10 minutes
})
```

### Monitor Costs

Set up alerts in:
- Vercel dashboard
- Third-party service dashboards
- CloudWatch / monitoring tools

---

## Troubleshooting

### Common Issues

#### Performance Issues

1. Check bundle sizes with analyzer
2. Optimize images
3. Implement caching
4. Reduce third-party scripts

#### Security Issues

1. Verify CSP configuration
2. Check security headers
3. Test with SecurityHeaders.com
4. Review CSP violation reports

#### Authentication Issues

1. Verify Clerk configuration
2. Check middleware setup
3. Ensure environment variables are set
4. Review Clerk Dashboard logs

---

## Additional Resources

- **Full Guide:** https://github.com/dricusbowser-alt/website-guide/blob/main/WEBSITE_BUILD_BIBLE_LLM_READY.md
- **Next.js Documentation:** https://nextjs.org/docs
- **Clerk Documentation:** https://clerk.com/docs
- **Vercel Documentation:** https://vercel.com/docs
- **Sentry Next.js Guide:** https://docs.sentry.io/platforms/javascript/guides/nextjs/

---

*End of Documentation*








