# CLAUDE.md — StayOnBoard Santorini

This file provides guidance for AI assistants working on the StayOnBoard platform — a multi-operator tourism website for Santorini, Greece.

---

## Project Overview

| Property | Value |
|---|---|
| **Brand** | StayOnBoard |
| **Location** | Santorini (Thíra), Cyclades, Greece |
| **Tagline** | *"Your island. Your story."* |
| **Mission** | Connect travellers with curated Santorini experiences by unifying hotels, tour operators, and tourist guides on a single authenticated platform |
| **Audiences** | International tourists (primary), local tourism operators (secondary) |
| **Operator types** | Hotels & Villas · Tour Operators · Tourist Guides |

---

## Repository Status

This is currently a **specification-phase repository**. The files present are:

- `README.md` — Project title
- `SKILL.md` — Complete platform specification (tech stack, design system, auth architecture, component patterns, design rules, tone of voice)
- `CLAUDE.md` — This file

No application source code exists yet. All implementation must be guided by `SKILL.md`.

---

## Tech Stack

### Frontend

| Layer | Technology | Notes |
|---|---|---|
| Framework | **Next.js v14+** | App Router, SSR + SSG hybrid |
| Styling | **Tailwind CSS v3** | JIT mode, custom design tokens |
| Components | **shadcn/ui** | Radix UI primitives, unstyled base |
| Animations | **Framer Motion** | Page transitions, scroll reveals, micro-interactions |
| Icons | **Lucide React** | Consistent icon set |
| Maps | **Mapbox GL JS v3+** | Custom island style, operator pin clusters |
| Forms | **React Hook Form + Zod** | Schema-validated forms |
| State | **Zustand** | Lightweight global state |
| Data fetching | **TanStack Query** | Server state, caching, background refetch |

### Backend

| Layer | Technology | Notes |
|---|---|---|
| API routes | **Next.js API Routes** | `/app/api/`, Edge Runtime where possible |
| Database | **Supabase (PostgreSQL)** | Managed Postgres with Row Level Security (RLS) |
| Auth | **Supabase Auth** | Google OAuth + Apple OAuth + magic link fallback |
| File storage | **Supabase Storage** | Operator profile images, documents |
| Media CDN | **Cloudinary** | Responsive images, video, auto-format delivery |
| CMS | **Sanity.io** | Headless CMS for tours, listings, blog articles |

### Infrastructure

| Layer | Technology | Notes |
|---|---|---|
| Hosting | **Vercel** | Git-based deployments, Edge Network, preview URLs |
| CI/CD | **GitHub Actions** | Lint, test, deploy pipeline |
| Monitoring | **Vercel Analytics** | Core Web Vitals + custom events |
| Error tracking | **Sentry** | Frontend + API route error capture |

---

## Planned Directory Structure

```
stayonboard/
├── app/
│   ├── (marketing)/          # Public pages (SSG)
│   │   ├── page.tsx          # Landing page
│   │   ├── explore/          # Browse operators
│   │   └── [operator]/       # Public operator profile
│   ├── (platform)/           # Authenticated operator dashboard
│   │   ├── dashboard/
│   │   ├── listings/
│   │   └── analytics/
│   ├── auth/
│   │   ├── login/page.tsx
│   │   └── callback/route.ts
│   ├── api/
│   │   ├── operators/
│   │   └── bookings/
│   └── layout.tsx
├── components/
│   ├── ui/                   # shadcn/ui primitives
│   ├── marketing/            # Hero, FeatureCard, Testimonial, etc.
│   ├── map/                  # MapboxMap, OperatorPin, etc.
│   ├── auth/                 # LoginCard, OAuthButton, etc.
│   └── dashboard/            # Sidebar, StatsCard, etc.
├── lib/
│   ├── supabase/             # client.ts, server.ts, middleware.ts
│   ├── cloudinary.ts
│   ├── sanity/
│   └── mapbox.ts
├── hooks/                    # useOperator, useMapbox, useAuth, etc.
├── store/                    # Zustand stores
├── types/                    # TypeScript interfaces
├── public/
│   └── assets/               # Logos, static images
├── sanity/                   # Sanity Studio (embedded)
├── .env.local
├── next.config.ts
├── tailwind.config.ts
└── middleware.ts             # Auth protection for /platform routes
```

---

## Environment Variables

Create `.env.local` with the following. Never commit real values.

```bash
# Supabase
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=
SUPABASE_SERVICE_ROLE_KEY=

# Mapbox
NEXT_PUBLIC_MAPBOX_TOKEN=

# Cloudinary
NEXT_PUBLIC_CLOUDINARY_CLOUD_NAME=
CLOUDINARY_API_KEY=
CLOUDINARY_API_SECRET=

# Sanity
NEXT_PUBLIC_SANITY_PROJECT_ID=
NEXT_PUBLIC_SANITY_DATASET=production
SANITY_API_TOKEN=

# Sentry
SENTRY_DSN=
```

---

## Design System

### Color Palette

Always use these tokens — never hardcode hex values outside of `tailwind.config.ts` or CSS custom properties.

```css
:root {
  /* Primary — Aegean Sea */
  --color-aegean:        #1A5F8A;   /* deep Aegean blue */
  --color-aegean-light:  #4A9CC4;   /* midday sea */
  --color-aegean-pale:   #C8E6F5;   /* foam & horizon */

  /* Accent */
  --color-white-cave:    #F7F4EF;   /* whitewashed walls */
  --color-sunset-gold:   #E8A045;   /* caldera sunset */
  --color-bougainvillea: #C0365A;   /* fuchsia flowers */

  /* Neutrals */
  --color-volcanic:      #2B2B2B;   /* dark volcanic rock */
  --color-pumice:        #8A8A8A;   /* pumice stone */
  --color-linen:         #F2EDE6;   /* linen & terracotta */

  /* Semantic */
  --color-success:       #3A9E6F;
  --color-warning:       #E8A045;
  --color-error:         #D94F4F;
}
```

### Typography

| Role | Font | Weights |
|---|---|---|
| Headings (H1/H2) | **Cormorant Garamond** | 300, 400, 600 (italic variants too) |
| Body text | **Plus Jakarta Sans** | 300, 400, 500, 600 |
| Buttons, labels, badges | **Montserrat** | 400, 500, 600 |

```css
:root {
  --font-heading: 'Cormorant Garamond', Georgia, serif;
  --font-body:    'Plus Jakarta Sans', sans-serif;
  --font-label:   'Montserrat', sans-serif;
}
```

### Aesthetic Direction

- **Whitespace is generous** — like the open Santorini sky
- **Photography is hero** — full-bleed caldera views, golden-hour shots
- **Glassmorphism accents** — frosted-glass cards with `backdrop-blur`
- **Subtle grain texture** on hero sections (linen/aged plaster feel)
- **Unhurried animations** — slow parallax, gentle fade-ins

---

## Authentication

### Providers

| Provider | Method |
|---|---|
| Google | OAuth 2.0 via Supabase Auth |
| Apple | Sign In with Apple via Supabase Auth |
| Magic Link | Email OTP fallback |

Google and Apple sign-in buttons **must always appear together**, stacked vertically, with Apple below Google.

### Database Schema

```sql
CREATE TABLE profiles (
  id            UUID PRIMARY KEY REFERENCES auth.users(id) ON DELETE CASCADE,
  full_name     TEXT,
  business_name TEXT,
  role          TEXT CHECK (role IN ('hotel', 'tour_operator', 'guide')),
  verified      BOOLEAN DEFAULT FALSE,
  created_at    TIMESTAMPTZ DEFAULT NOW()
);

ALTER TABLE profiles ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Own profile only" ON profiles
  USING (auth.uid() = id)
  WITH CHECK (auth.uid() = id);
```

### Supabase Client

```typescript
// lib/supabase/client.ts
import { createBrowserClient } from '@supabase/ssr'

export const supabase = createBrowserClient(
  process.env.NEXT_PUBLIC_SUPABASE_URL!,
  process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!
)

export const signInWithGoogle = () =>
  supabase.auth.signInWithOAuth({
    provider: 'google',
    options: { redirectTo: `${location.origin}/auth/callback` }
  })

export const signInWithApple = () =>
  supabase.auth.signInWithOAuth({
    provider: 'apple',
    options: { redirectTo: `${location.origin}/auth/callback` }
  })
```

### Auth Callback Route

```typescript
// app/auth/callback/route.ts
import { createServerClient } from '@supabase/ssr'
import { cookies } from 'next/headers'
import { NextResponse } from 'next/server'

export async function GET(request: Request) {
  const { searchParams } = new URL(request.url)
  const code = searchParams.get('code')
  const next = searchParams.get('next') ?? '/dashboard'

  if (code) {
    const supabase = createServerClient(
      process.env.NEXT_PUBLIC_SUPABASE_URL!,
      process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!,
      { cookies: { get: (n) => cookies().get(n)?.value } }
    )
    await supabase.auth.exchangeCodeForSession(code)
  }
  return NextResponse.redirect(new URL(next, request.url))
}
```

---

## Mapbox Integration

```typescript
// lib/mapbox.ts
export const MAPBOX_STYLE = 'mapbox://styles/stayonboard/custom-santorini'
export const SANTORINI_CENTER: [number, number] = [25.4319, 36.3932]
export const SANTORINI_BOUNDS: [[number, number], [number, number]] = [
  [25.32, 36.33],  // SW
  [25.52, 36.47]   // NE
]

export const PIN_COLORS = {
  hotel:         '#1A5F8A',   // Aegean
  tour_operator: '#E8A045',   // Sunset gold
  guide:         '#C0365A'    // Bougainvillea
}
```

Maps must always be bounded to `SANTORINI_BOUNDS`. Never show the full globe.

---

## Cloudinary Image Helper

```typescript
// lib/cloudinary.ts
import { v2 as cloudinary } from 'cloudinary'

cloudinary.config({
  cloud_name: process.env.NEXT_PUBLIC_CLOUDINARY_CLOUD_NAME,
  api_key:    process.env.CLOUDINARY_API_KEY,
  api_secret: process.env.CLOUDINARY_API_SECRET,
})

export const buildImageUrl = (publicId: string, width = 800) =>
  cloudinary.url(publicId, {
    width,
    crop: 'fill',
    gravity: 'auto',
    format: 'webp',
    quality: 'auto'
  })
```

All images must go through this helper. Never use raw `<img>` tags with external URLs.

---

## Component Patterns

### OAuth Buttons

```tsx
// components/auth/OAuthButton.tsx
'use client'
import { signInWithGoogle, signInWithApple } from '@/lib/supabase/client'

export function GoogleSignIn() {
  return (
    <button
      onClick={signInWithGoogle}
      className="flex items-center gap-3 w-full px-5 py-3 rounded-xl border border-pumice bg-white-cave hover:bg-aegean-pale transition-colors font-label text-sm font-medium text-volcanic"
      aria-label="Continue with Google"
    >
      <GoogleIcon />
      Continue with Google
    </button>
  )
}

export function AppleSignIn() {
  return (
    <button
      onClick={signInWithApple}
      className="flex items-center gap-3 w-full px-5 py-3 rounded-xl bg-volcanic hover:bg-volcanic/90 transition-colors font-label text-sm font-medium text-white-cave"
      aria-label="Continue with Apple"
    >
      <AppleIcon />
      Continue with Apple
    </button>
  )
}
```

### Operator Card Props

```tsx
interface OperatorCardProps {
  name: string
  category: 'hotel' | 'tour_operator' | 'guide'
  image: string   // Cloudinary public ID
  rating: number
  location: string
}
```

---

## Key Conventions

### TypeScript

- Define TypeScript interfaces for all props and API responses in `types/`
- Use strict mode
- Prefer `type` for object shapes, `interface` for extendable contracts

### Routing

- `(marketing)` route group: public pages, rendered with SSG
- `(platform)` route group: authenticated operator dashboard, protected by `middleware.ts`
- Auth routes: `/auth/login`, `/auth/callback`

### Middleware (Auth Protection)

All routes under `(platform)/` must be protected via `middleware.ts` using Supabase session checks. Unauthenticated users redirect to `/auth/login`.

### Animations

- Use Framer Motion `variants` with `staggerChildren` for list reveals
- Duration ≤ 0.6s
- Easing: `easeOut`

### Accessibility

- All interactive elements need `aria-label`
- Keyboard navigable
- WCAG AA contrast minimum

### Mobile-First

- All layouts start at 375px and scale up
- Use Tailwind responsive prefixes: `sm:`, `md:`, `lg:`, `xl:`

---

## Tone of Voice

| Context | Tone |
|---|---|
| Marketing copy | Evocative, poetic, sensory — *"Where the caldera meets the sky"* |
| Operator dashboard | Clear, efficient, professional |
| Error messages | Friendly, never technical jargon |
| Onboarding | Warm, encouraging, step-by-step |
| Emails | Conversational, Mediterranean warmth |

---

## Pre-Delivery Checklist

Before delivering any artifact, verify:

- [ ] Brand palette tokens used (no hardcoded hex outside design token files)
- [ ] Correct fonts: Cormorant Garamond (H1/H2), Plus Jakarta Sans (body), Montserrat (buttons/badges)
- [ ] Auth surfaces include both Google **and** Apple buttons (Apple below Google)
- [ ] Supabase RLS policies applied to any new table
- [ ] Mapbox map bounded to `SANTORINI_BOUNDS`
- [ ] All images served via `buildImageUrl()` Cloudinary helper
- [ ] Mobile-first responsive layout (starts at 375px)
- [ ] TypeScript types defined for all props and API responses
- [ ] Environment variables documented in `.env.local` example
- [ ] `aria-label` and keyboard focus on all interactive elements
- [ ] Framer Motion animations: duration ≤ 0.6s, easing `easeOut`

---

## Git Workflow

- Development branch: `claude/claude-md-mmb4hr653wzw6mxk-1D7I7`
- Push with: `git push -u origin <branch-name>`
- Write descriptive commit messages focused on the "why"
- Do not push directly to `master`
