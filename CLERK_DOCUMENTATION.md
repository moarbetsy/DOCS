# Clerk - Documentation

This document contains all documentation extracted from Clerk official documentation and API references.

**Last Updated:** November 12, 2025

**Source:** https://clerk.com/docs/

**Documentation:** https://clerk.com/docs/

---

## Table of Contents

1. [Introduction](#introduction)
2. [Getting Started](#getting-started)
3. [UI Components](#ui-components)
4. [Authentication](#authentication)
5. [User Management](#user-management)
6. [Organizations](#organizations)
7. [Billing](#billing)
8. [SDKs and Frameworks](#sdks-and-frameworks)
9. [API Reference](#api-reference)
10. [Best Practices](#best-practices)
11. [Troubleshooting](#troubleshooting)

---

## Introduction

Clerk is a complete user management platform that provides authentication, user management, organization management, and billing features. It offers pixel-perfect UI components that can be embedded in minutes and customized to match your brand.

### Key Features

- **Complete User Management**: Full stack auth and user management
- **Prebuilt UI Components**: Drop-in components for authentication, profile management, and billing
- **Multi-Factor Authentication**: Self-serve MFA settings enforced automatically
- **Social Sign-On**: 20+ social providers and growing
- **Organization Management**: Multi-tenant SaaS features with custom roles and permissions
- **Billing Integration**: Subscription billing without writing payment code
- **Security**: SOC 2 type 2 compliant, CCPA compliant, regular audits
- **Bot Detection**: Machine learning-based fraud prevention
- **Session Management**: Full session lifecycle with device monitoring

### Supported Frameworks

**Frontend:**
- Next.js
- React
- Vue
- Astro
- Nuxt
- Remix
- React Router
- TanStack React Start
- Expo (React Native)
- Chrome Extension
- iOS
- Android

**Backend:**
- JavaScript/TypeScript
- Express
- Fastify
- Go
- Python
- Ruby on Rails
- C#

---

## Getting Started

### Quickstart (Next.js)

1. **Create a new Next.js application:**
   ```bash
   npm create next-app@latest my-clerk-app -- --yes
   ```

2. **Install Clerk:**
   ```bash
   npm install @clerk/nextjs
   ```

3. **Add `clerkMiddleware()` to your app:**

   Create `proxy.ts` (or `middleware.ts` for Next.js ≤15):
   ```typescript
   import { clerkMiddleware } from '@clerk/nextjs/server'

   export default clerkMiddleware()

   export const config = {
     matcher: [
       // Skip Next.js internals and all static files
       '/((?!_next|[^?]*\\.(?:html?|css|js(?!on)|jpe?g|webp|png|gif|svg|ttf|woff2?|ico|csv|docx?|xlsx?|zip|webmanifest)).*)',
       // Always run for API routes
       '/(api|trpc)(.*)',
     ],
   }
   ```

4. **Add `<ClerkProvider>` and components:**

   Update `app/layout.tsx`:
   ```typescript
   import {
     ClerkProvider,
     SignInButton,
     SignUpButton,
     SignedIn,
     SignedOut,
     UserButton,
   } from '@clerk/nextjs'

   export default function RootLayout({
     children,
   }: {
     children: React.ReactNode
   }) {
     return (
       <ClerkProvider>
         <html lang="en">
           <body>
             <header>
               <SignedOut>
                 <SignInButton />
                 <SignUpButton>
                   <button>Sign Up</button>
                 </SignUpButton>
               </SignedOut>
               <SignedIn>
                 <UserButton />
               </SignedIn>
             </header>
             {children}
           </body>
         </html>
       </ClerkProvider>
     )
   }
   ```

5. **Set up environment variables:**

   Create `.env.local`:
   ```env
   NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=pk_test_...
   CLERK_SECRET_KEY=sk_test_...
   ```

6. **Run your app:**
   ```bash
   npm run dev
   ```

7. **Create your first user:**
   - Visit http://localhost:3000
   - Click "Sign up" and authenticate

---

## UI Components

Clerk provides a comprehensive suite of prebuilt UI components that can be customized to match your brand.

### Authentication Components

#### `<SignIn />`
Full-featured sign-in component with support for multiple authentication methods.

**Usage:**
```typescript
import { SignIn } from '@clerk/nextjs'

export default function SignInPage() {
  return <SignIn />
}
```

**Features:**
- Email/password authentication
- Social sign-on (Google, GitHub, etc.)
- Magic links
- SMS one-time passcodes
- Multi-factor authentication

#### `<SignUp />`
Complete sign-up component with user registration flow.

**Usage:**
```typescript
import { SignUp } from '@clerk/nextjs'

export default function SignUpPage() {
  return <SignUp />
}
```

#### `<GoogleOneTap />`
Google One Tap authentication component.

**Usage:**
```typescript
import { GoogleOneTap } from '@clerk/nextjs'

export default function HomePage() {
  return <GoogleOneTap />
}
```

#### `<SignInButton />` / `<SignUpButton />`
Unstyled button components that trigger authentication modals.

**Usage:**
```typescript
import { SignInButton, SignUpButton } from '@clerk/nextjs'

export default function Header() {
  return (
    <>
      <SignInButton mode="modal">
        <button>Sign In</button>
      </SignInButton>
      <SignUpButton mode="modal">
        <button>Sign Up</button>
      </SignUpButton>
    </>
  )
}
```

### User Components

#### `<UserButton />`
User account button with dropdown menu for account management.

**Usage:**
```typescript
import { UserButton } from '@clerk/nextjs'

export default function Header() {
  return <UserButton />
}
```

**Features:**
- Account switcher
- Manage account
- Sign out
- Organization switching

#### `<UserProfile />`
Complete user profile management component.

**Usage:**
```typescript
import { UserProfile } from '@clerk/nextjs'

export default function ProfilePage() {
  return <UserProfile />
}
```

**Features:**
- Profile details management
- Email addresses
- Phone numbers
- Connected accounts
- Security settings
- Billing (if enabled)

### Organization Components

#### `<CreateOrganization />`
Component for creating new organizations.

**Usage:**
```typescript
import { CreateOrganization } from '@clerk/nextjs'

export default function CreateOrgPage() {
  return <CreateOrganization />
}
```

#### `<OrganizationSwitcher />`
Component for switching between organizations.

**Usage:**
```typescript
import { OrganizationSwitcher } from '@clerk/nextjs'

export default function Header() {
  return <OrganizationSwitcher />
}
```

#### `<OrganizationProfile />`
Complete organization management component.

**Usage:**
```typescript
import { OrganizationProfile } from '@clerk/nextjs'

export default function OrgSettingsPage() {
  return <OrganizationProfile />
}
```

**Features:**
- Organization profile management
- Member management
- Roles and permissions
- Verified domains
- Billing (if enabled)

#### `<OrganizationList />`
Component for listing and joining organizations.

**Usage:**
```typescript
import { OrganizationList } from '@clerk/nextjs'

export default function OrganizationsPage() {
  return <OrganizationList />
}
```

### Control Components

Control components manage authentication-related behaviors.

#### `<SignedIn />` / `<SignedOut />`
Conditionally render content based on authentication status.

**Usage:**
```typescript
import { SignedIn, SignedOut } from '@clerk/nextjs'

export default function Page() {
  return (
    <>
      <SignedIn>
        <p>You are signed in!</p>
      </SignedIn>
      <SignedOut>
        <p>Please sign in to continue.</p>
      </SignedOut>
    </>
  )
}
```

#### `<Protect />`
Protect content based on authentication and authorization.

**Usage:**
```typescript
import { Protect } from '@clerk/nextjs'

export default function AdminPage() {
  return (
    <Protect
      condition={(auth) => auth.orgRole === 'admin'}
      fallback={<p>Access denied</p>}
    >
      <AdminContent />
    </Protect>
  )
}
```

#### `<ClerkLoaded />` / `<ClerkLoading />`
Handle loading states during Clerk initialization.

**Usage:**
```typescript
import { ClerkLoaded, ClerkLoading } from '@clerk/nextjs'

export default function App() {
  return (
    <>
      <ClerkLoading>
        <LoadingSpinner />
      </ClerkLoading>
      <ClerkLoaded>
        <AppContent />
      </ClerkLoaded>
    </>
  )
}
```

### Billing Components

#### `<PricingTable />`
Display pricing plans with feature comparisons.

**Usage:**
```typescript
import { PricingTable } from '@clerk/nextjs'

export default function PricingPage() {
  return <PricingTable />
}
```

#### `<CheckoutButton />`
Button to initiate checkout for a subscription plan.

**Usage:**
```typescript
import { CheckoutButton } from '@clerk/nextjs'

export default function PricingCard() {
  return (
    <CheckoutButton planId="plan_123">
      <button>Subscribe</button>
    </CheckoutButton>
  )
}
```

---

## Authentication

### Authentication Strategies

Clerk supports multiple authentication strategies:

- **Email/Password**: Traditional email and password authentication
- **Social Sign-On**: 20+ providers (Google, GitHub, Facebook, etc.)
- **Magic Links**: Passwordless authentication via email
- **SMS One-Time Passcodes**: Phone number authentication
- **Multi-Factor Authentication**: Additional security layer
- **Web3**: Wallet-based authentication (MetaMask, WalletConnect)

### Multi-Factor Authentication

Each user's self-serve MFA settings are enforced automatically during sign-in.

**Supported Methods:**
- TOTP (Time-based One-Time Password)
- SMS codes
- Backup codes

### Fraud and Abuse Prevention

**Features:**
- Block high-risk disposable email domains
- Restrict email subaddresses with "+" separator
- Machine learning-based bot detection
- Continually updated fraud detection

### Session Management

Clerk manages the full session lifecycle:

- **Active Device Monitoring**: Track and manage active sessions
- **Session Revocation**: Revoke sessions remotely
- **Device Management**: View and manage devices
- **Security Alerts**: Notifications for suspicious activity

### Social Sign-On

Add high-conversion social sign-on in minutes.

**Supported Providers:**
- Google
- GitHub
- Facebook
- Twitter/X
- Apple
- Microsoft
- And 15+ more

**Setup:**
1. Configure provider in Clerk Dashboard
2. Add provider credentials
3. Enable in authentication settings
4. Components automatically support the provider

---

## User Management

### User Object

The User object contains all account information:

- Personal information (name, email, phone)
- Profile image
- Authentication methods
- Security settings
- Organization memberships
- Metadata

### User Profile Management

Users can manage their profiles through `<UserProfile />` component:

- Update profile details
- Add/remove email addresses
- Add/remove phone numbers
- Connect/disconnect social accounts
- Manage security settings

### Reading User Data

**Server-side (Next.js):**
```typescript
import { auth, currentUser } from '@clerk/nextjs/server'

export default async function Page() {
  const { userId } = await auth()
  const user = await currentUser()
  
  if (!userId) {
    return <div>Not authenticated</div>
  }
  
  return <div>Hello {user?.firstName}!</div>
}
```

**Client-side:**
```typescript
'use client'

import { useUser } from '@clerk/nextjs'

export default function Component() {
  const { user, isLoaded } = useUser()
  
  if (!isLoaded) {
    return <div>Loading...</div>
  }
  
  return <div>Hello {user?.firstName}!</div>
}
```

---

## Organizations

Organizations allow members to collaborate across shared resources.

### Organization Features

- **Custom Roles and Permissions**: Define custom roles with specific permissions
- **Auto-Join**: Users can discover and join organizations based on email domain
- **Invitations**: Simple team invitation system
- **Member Management**: Add, remove, and manage members
- **Organization Settings**: Configure organization-level settings

### Custom Roles and Permissions

Define custom roles with specific permissions:

```typescript
// In Clerk Dashboard or via API
{
  "role": "editor",
  "permissions": [
    "org:read",
    "org:write",
    "org:members:read"
  ]
}
```

### Auto-Join

Let users discover and join organizations based on their email domain:

1. Verify organization domain in Clerk Dashboard
2. Enable auto-join for the domain
3. Users with matching email domains can automatically join

### Invitations

Fuel application growth with team invitations:

1. Organization admins can invite members
2. Invitations sent via email
3. Members can accept and join organization
4. Track invitation status

---

## Billing

Clerk's billing features allow you to add subscriptions without writing payment code.

### Features

- **Define and Manage Plans**: Create subscription plans in Clerk Dashboard
- **Unify User and Subscription Data**: User and subscription data in one place
- **Gate Access to Content**: Control access based on subscription status
- **Drop-in Components**: React components for pricing and checkout

### Pricing Table Component

Display pricing plans with feature comparisons:

```typescript
import { PricingTable } from '@clerk/nextjs'

export default function PricingPage() {
  return <PricingTable />
}
```

### Checkout Flow

Users can subscribe directly through Clerk components:

1. Display pricing with `<PricingTable />`
2. User clicks subscribe button
3. Checkout modal opens
4. User completes payment
5. Subscription is created automatically

### Subscription Management

Users can manage subscriptions through `<UserProfile />` component:

- View current plan
- Upgrade/downgrade plans
- Update payment methods
- View billing history
- Cancel subscriptions

---

## SDKs and Frameworks

### Next.js

**Installation:**
```bash
npm install @clerk/nextjs
```

**Key Features:**
- Server-side authentication
- Middleware support
- API route protection
- App Router and Pages Router support

### React

**Installation:**
```bash
npm install @clerk/clerk-react
```

**Usage:**
```typescript
import { ClerkProvider } from '@clerk/clerk-react'

function App() {
  return (
    <ClerkProvider publishableKey={process.env.REACT_APP_CLERK_PUBLISHABLE_KEY}>
      <YourApp />
    </ClerkProvider>
  )
}
```

### Express

**Installation:**
```bash
npm install @clerk/express
```

**Usage:**
```typescript
import { requireAuth } from '@clerk/express'

app.get('/protected', requireAuth(), (req, res) => {
  res.json({ userId: req.auth.userId })
})
```

### Other Frameworks

Clerk supports many other frameworks:
- Vue
- Astro
- Nuxt
- Remix
- React Router
- TanStack React Start
- Expo (React Native)
- Go
- Python
- Ruby on Rails
- C#

---

## API Reference

### Backend API

Clerk provides a RESTful API for backend operations.

**Base URL:** `https://api.clerk.com/v1`

**Authentication:** Bearer token (API key)

**Example: Get User**
```python
import requests

headers = {
    "Authorization": f"Bearer {CLERK_SECRET_KEY}",
    "Content-Type": "application/json"
}

response = requests.get(
    f"https://api.clerk.com/v1/users/{user_id}",
    headers=headers
)

user = response.json()
```

**Example: Create User**
```python
data = {
    "email_address": ["user@example.com"],
    "password": "secure_password",
    "first_name": "John",
    "last_name": "Doe"
}

response = requests.post(
    "https://api.clerk.com/v1/users",
    headers=headers,
    json=data
)

user = response.json()
```

**Example: List Organizations**
```python
response = requests.get(
    "https://api.clerk.com/v1/organizations",
    headers=headers
)

organizations = response.json()
```

### Webhooks

Clerk sends webhooks for important events:

- `user.created`
- `user.updated`
- `user.deleted`
- `session.created`
- `session.ended`
- `organization.created`
- `organization.updated`
- `organization.deleted`
- `organizationMembership.created`
- `organizationMembership.deleted`

**Webhook Setup:**
1. Configure webhook endpoint in Clerk Dashboard
2. Add webhook signing secret to environment variables
3. Verify webhook signatures in your endpoint

**Example Webhook Handler:**
```typescript
import { Webhook } from 'svix'
import { headers } from 'next/headers'

export async function POST(req: Request) {
  const headerPayload = await headers()
  const svix_id = headerPayload.get('svix-id')
  const svix_timestamp = headerPayload.get('svix-timestamp')
  const svix_signature = headerPayload.get('svix-signature')

  if (!svix_id || !svix_timestamp || !svix_signature) {
    return new Response('Error', { status: 400 })
  }

  const payload = await req.json()
  const body = JSON.stringify(payload)

  const wh = new Webhook(process.env.CLERK_WEBHOOK_SECRET!)

  let evt: any

  try {
    evt = wh.verify(body, {
      'svix-id': svix_id,
      'svix-timestamp': svix_timestamp,
      'svix-signature': svix_signature,
    })
  } catch (err) {
    return new Response('Error', { status: 400 })
  }

  const eventType = evt.type

  if (eventType === 'user.created') {
    // Handle user creation
    const { id, email_addresses } = evt.data
    // Create user in your database
  }

  return new Response('', { status: 200 })
}
```

---

## Best Practices

### 1. Protect Routes

Use middleware to protect routes:

```typescript
import { clerkMiddleware, createRouteMatcher } from '@clerk/nextjs/server'

const isProtectedRoute = createRouteMatcher([
  '/dashboard(.*)',
  '/admin(.*)',
])

export default clerkMiddleware(async (auth, req) => {
  if (isProtectedRoute(req)) {
    await auth.protect()
  }
})
```

### 2. Use Server Components

Prefer server components for authentication checks:

```typescript
import { auth } from '@clerk/nextjs/server'

export default async function Page() {
  const { userId } = await auth()
  
  if (!userId) {
    redirect('/sign-in')
  }
  
  return <div>Protected content</div>
}
```

### 3. Handle Loading States

Always handle loading states:

```typescript
'use client'

import { useUser } from '@clerk/nextjs'

export default function Component() {
  const { user, isLoaded } = useUser()
  
  if (!isLoaded) {
    return <LoadingSpinner />
  }
  
  if (!user) {
    return <SignInPrompt />
  }
  
  return <UserContent user={user} />
}
```

### 4. Customize Components

Customize component appearance to match your brand:

```typescript
import { SignIn } from '@clerk/nextjs'

export default function SignInPage() {
  return (
    <SignIn
      appearance={{
        elements: {
          rootBox: "mx-auto",
          card: "shadow-lg",
        },
        variables: {
          colorPrimary: "#6c47ff",
        },
      }}
    />
  )
}
```

### 5. Use Environment Variables

Store Clerk keys in environment variables:

```env
# .env.local
NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=pk_test_...
CLERK_SECRET_KEY=sk_test_...
```

### 6. Implement Error Handling

Handle authentication errors gracefully:

```typescript
import { useAuth } from '@clerk/nextjs'

export default function Component() {
  const { isLoaded, userId } = useAuth()
  
  if (!isLoaded) {
    return <Loading />
  }
  
  if (!userId) {
    return <Error message="Please sign in to continue" />
  }
  
  return <Content />
}
```

---

## Troubleshooting

### Common Issues

#### Components Not Rendering

**Solution:**
1. Verify `<ClerkProvider>` wraps your app
2. Check environment variables are set
3. Ensure middleware is configured correctly
4. Check browser console for errors

#### Authentication Not Working

**Solution:**
1. Verify API keys are correct
2. Check middleware configuration
3. Ensure routes are properly protected
4. Check Clerk Dashboard for configuration issues

#### Organization Features Not Available

**Solution:**
1. Verify organization features are enabled in Clerk Dashboard
2. Check user has organization access
3. Verify organization components are properly configured

#### Webhooks Not Receiving Events

**Solution:**
1. Verify webhook endpoint is accessible
2. Check webhook signing secret is correct
3. Verify webhook signature verification
4. Check Clerk Dashboard webhook logs

### Getting Help

- **Discord:** https://clerk.com/discord
- **Support:** https://clerk.com/support
- **Documentation:** https://clerk.com/docs
- **GitHub:** https://github.com/clerk/clerk

---

## Additional Resources

- **Clerk Documentation:** https://clerk.com/docs/
- **Clerk Dashboard:** https://dashboard.clerk.com/
- **Clerk Pricing:** https://clerk.com/pricing
- **Clerk Blog:** https://clerk.com/blog
- **Clerk Discord:** https://clerk.com/discord
- **GitHub Repository:** https://github.com/clerk/clerk

---

*End of Documentation*








