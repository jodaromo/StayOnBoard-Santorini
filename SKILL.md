# StayOnBoard — Santorini Tourism Platform Skill

---
name: stayonboard-santorini
description: >
  Use this skill whenever building any component, page, feature, or document for the
  StayOnBoard platform — a multi-operator tourism website for Santorini, Greece.
  Covers frontend (Next.js, Tailwind CSS, shadcn/ui), backend (Supabase, Next.js API
  routes), authentication (Supabase Auth with Google & Apple OAuth), maps (Mapbox GL JS),
  media (Cloudinary), content (Sanity.io), and deployment (Vercel). Also governs brand
  identity, color palette, typography, and tone of voice for all artifacts.
---

---

## 1. Project Identity

| Property        | Value                                                   |
|-----------------|---------------------------------------------------------|
| **Brand name**  | StayOnBoard                                             |
| **Location**    | Santorini (Thíra), Cyclades, Greece                     |
| **Tagline**     | *"Your island. Your story."*                            |
| **Mission**     | Connect travellers with curated Santorini experiences by unifying hotels, tour operators, and tourist guides on a single authenticated platform. |
| **Audience**    | International tourists (primary) + local tourism operators (secondary) |
| **Operator types** | Hotels & Villas · Tour Operators · Tourist Guides    |

---

## 2. Tech Stack

### 2.1 Frontend
| Layer          | Technology            | Version / Notes                                  |
|----------------|-----------------------|--------------------------------------------------|
| Framework      | **Next.js**           | v14+ App Router, SSR + SSG hybrid                |
| Styling        | **Tailwind CSS**      | v3, JIT mode, custom design tokens               |
| Components     | **shadcn/ui**         | Radix UI primitives, fully unstyled base          |
| Animations     | **Framer Motion**     | Page transitions, scroll reveals, micro-interactions |
| Icons          | **Lucide React**      | Consistent icon set                              |
| Maps           | **Mapbox GL JS**      | v3+, custom island style, operator pin clusters  |
| Forms          | **React Hook Form**   | + Zod for schema validation                      |
| State          | **Zustand**           | Lightweight global state                         |
| Data fetching  | **TanStack Query**    | Server state, caching, background refetch        |

### 2.2 Backend
| Layer          | Technology            | Notes                                            |
|----------------|-----------------------|--------------------------------------------------|
| API routes     | **Next.js API Routes**| /app/api/ directory, Edge Runtime where possible  |
| Database       | **Supabase (PostgreSQL)** | Managed Postgres, Row Level Security (RLS)   |
| Auth           | **Supabase Auth**     | Google OAuth + Apple OAuth + magic link fallback |
| File storage   | **Supabase Storage**  | Operator profile images, documents              |
| Media CDN      | **Cloudinary**        | Responsive images, video, auto-format delivery   |
| CMS            | **Sanity.io**         | Headless CMS for tours, listings, blog articles  |

### 2.3 Infrastructure & DevOps
| Layer          | Technology            | Notes                                            |
|----------------|-----------------------|--------------------------------------------------|
| Hosting        | **Vercel**            | Git-based deployments, Edge Network, preview URLs |
| CI/CD          | **GitHub Actions**    | Lint, test, deploy pipeline                      |
| Environment    | `.env.local`          | Secrets via Vercel Environment Variables          |
| Monitoring     | **Vercel Analytics**  | Core Web Vitals + custom events                  |
| Error tracking | **Sentry**            | Frontend + API route error capture               |

---

## 3. Brand & Design System

### 3.1 Color Palette

```css
:root {
  /* Primary — Aegean Sea */
  --color-aegean:        #1A5F8A;   /* deep Aegean blue  */
  --color-aegean-light:  #4A9CC4;   /* midday sea        */
  --color-aegean-pale:   #C8E6F5;   /* foam & horizon    */

  /* Accent — Santorini White & Sunset */
  --color-white-cave:    #F7F4EF;   /* whitewashed walls */
  --color-sunset-gold:   #E8A045;   /* caldera sunset    */
  --color-bougainvillea: #C0365A;   /* fuchsia flowers   */

  /* Neutrals */
  --color-volcanic:      #2B2B2B;   /* dark volcanic rock */
  --color-pumice:        #8A8A8A;   /* pumice stone      */
  --color-linen:         #F2EDE6;   /* linen & terracotta */

  /* Semantic */
  --color-success:       #3A9E6F;
  --color-warning:       --color-sunset-gold;
  --color-error:         #D94F4F;
}
```

### 3.2 Typography

```css
/* Headings — Cormorant Garamond (elegant, Mediterranean editorial feel) */
@import url('https://fonts.googleapis.com/css2?family=Cormorant+Garamond:ital,wght@0,300;0,400;0,600;1,300;1,400&display=swap');

/* Body — Plus Jakarta Sans (modern, readable, friendly) */
@import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;500;600&display=swap');

/* Accent / labels — Montserrat (clean, tourism-industry standard) */
@import url('https://fonts.googleapis.com/css2?family=Montserrat:wght@400;500;600&display=swap');

:root {
  --font-heading: 'Cormorant Garamond', Georgia, serif;
  --font-body:    'Plus Jakarta Sans', sans-serif;
  --font-label:   'Montserrat', sans-serif;
}
```

### 3.3 Aesthetic Direction

StayOnBoard blends **refined Mediterranean luxury** with **approachable warmth**.

- **Whitespace is generous** — like the open Santorini sky
- **Photography is hero** — full-bleed caldera views, golden-hour shots, close-ups of local cuisine
- **Glassmorphism accents** — frosted-glass cards that echo the island's translucent light
- **Subtle grain texture** on hero sections to evoke linen and aged plaster
- **Animations are unhurried** — slow parallax, gentle fade-ins, easing curves that feel warm, not techy

---

## 4. Authentication Architecture

### 4.1 Providers

| Provider   | Method                     | Operator type          |
|------------|----------------------------|------------------------|
| Google     | OAuth 2.0 via Supabase Auth | All operator types     |
| Apple      | Sign in with Apple via Supabase Auth | All operator types |
| Magic Link | Email OTP fallback         | Edge case / recovery   |

### 4.2 Supabase Auth Setup

```typescript
// lib/supabase/client.ts
import { createBrowserClient } from '@supabase/ssr'

export const supabase = createBrowserClient(
  process.env.NEXT_PUBLIC_SUPABASE_URL!,
  process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!
)

// Sign in with Google
export const signInWithGoogle = () =>
  supabase.auth.signInWithOAuth({
    provider: 'google',
    options: { redirectTo: `${location.origin}/auth/callback` }
  })

// Sign in with Apple
export const signInWithApple = () =>
  supabase.auth.signInWithOAuth({
    provider: 'apple',
    options: { redirectTo: `${location.origin}/auth/callback` }
  })
```

### 4.3 Operator Roles & Row Level Security

After first login, operators complete an onboarding form. Their role is stored in a `profiles` table.

```sql
-- Supabase: profiles table
CREATE TABLE profiles (
  id          UUID PRIMARY KEY REFERENCES auth.users(id) ON DELETE CASCADE,
  full_name   TEXT,
  business_name TEXT,
  role        TEXT CHECK (role IN ('hotel', 'tour_operator', 'guide')),
  verified    BOOLEAN DEFAULT FALSE,
  created_at  TIMESTAMPTZ DEFAULT NOW()
);

-- RLS: operators can only read/write their own profile
ALTER TABLE profiles ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Own profile only" ON profiles
  USING (auth.uid() = id)
  WITH CHECK (auth.uid() = id);
```

### 4.4 Auth Callback Route

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

## 5. Project Structure

```
stayonboard/
├── app/
│   ├── (marketing)/          # Public pages (SSG)
│   │   ├── page.tsx           # Landing page
│   │   ├── explore/           # Browse operators
│   │   └── [operator]/        # Public operator profile
│   ├── (platform)/            # Authenticated operator dashboard
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
│   ├── ui/                    # shadcn/ui primitives
│   ├── marketing/             # Hero, FeatureCard, Testimonial, etc.
│   ├── map/                   # MapboxMap, OperatorPin, etc.
│   ├── auth/                  # LoginCard, OAuthButton, etc.
│   └── dashboard/             # Sidebar, StatsCard, etc.
├── lib/
│   ├── supabase/              # client.ts, server.ts, middleware.ts
│   ├── cloudinary.ts
│   ├── sanity/
│   └── mapbox.ts
├── hooks/                     # useOperator, useMapbox, useAuth, etc.
├── store/                     # Zustand stores
├── types/                     # TypeScript interfaces
├── public/
│   └── assets/                # Logos, static images
├── sanity/                    # Sanity Studio (embedded)
├── .env.local
├── next.config.ts
├── tailwind.config.ts
└── middleware.ts              # Auth protection for /platform routes
```

---

## 6. Key Environment Variables

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

## 7. Mapbox Integration

```typescript
// lib/mapbox.ts
export const MAPBOX_STYLE = 'mapbox://styles/stayonboard/custom-santorini'
export const SANTORINI_CENTER: [number, number] = [25.4319, 36.3932]
export const SANTORINI_BOUNDS: [[number, number], [number, number]] = [
  [25.32, 36.33],  // SW
  [25.52, 36.47]   // NE
]

// Operator category colors
export const PIN_COLORS = {
  hotel:         '#1A5F8A',   // Aegean
  tour_operator: '#E8A045',   // Sunset gold
  guide:         '#C0365A'    // Bougainvillea
}
```

---

## 8. Cloudinary Image Helper

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

---

## 9. Component Patterns

### OAuth Login Button
```tsx
// components/auth/OAuthButton.tsx
'use client'
import { signInWithGoogle, signInWithApple } from '@/lib/supabase/client'

export function GoogleSignIn() {
  return (
    <button
      onClick={signInWithGoogle}
      className="flex items-center gap-3 w-full px-5 py-3 rounded-xl border border-pumice bg-white-cave hover:bg-aegean-pale transition-colors font-label text-sm font-medium text-volcanic"
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
    >
      <AppleIcon />
      Continue with Apple
    </button>
  )
}
```

### Operator Card
```tsx
// components/marketing/OperatorCard.tsx
interface OperatorCardProps {
  name: string
  category: 'hotel' | 'tour_operator' | 'guide'
  image: string   // Cloudinary public ID
  rating: number
  location: string
}
```

---

## 10. Design Rules (Enforced for Every Artifact)

1. **Never** use generic placeholder colors — always use the brand palette tokens.
2. **Always** use `font-heading` (Cormorant Garamond) for H1/H2, `font-body` for paragraphs, `font-label` for buttons and badges.
3. **Images** must go through Cloudinary with `format=webp` and `quality=auto`.
4. **All authenticated routes** under `/platform/**` must be protected by the Next.js `middleware.ts` Supabase session check.
5. **Google and Apple sign-in buttons** must always appear together on the same login surface, stacked vertically, Apple below Google.
6. **Maps** must always be bounded to Santorini (`SANTORINI_BOUNDS`) and never show the full globe.
7. **Operator pins** must be color-coded by type using `PIN_COLORS`.
8. **Mobile-first** — all layouts start at 375px and scale up.
9. **Accessibility** — all interactive elements need `aria-label`, focusable with keyboard, WCAG AA contrast minimum.
10. **Animations** — use Framer Motion `variants` with `staggerChildren` for list reveals; keep duration ≤ 0.6s and easing `easeOut`.

---

## 11. Tone of Voice

| Context         | Tone                                                   |
|-----------------|--------------------------------------------------------|
| Marketing copy  | Evocative, poetic, sensory — *"Where the caldera meets the sky"* |
| Operator dashboard | Clear, efficient, professional                    |
| Error messages  | Friendly, never technical jargon                      |
| Onboarding      | Warm, encouraging, step-by-step                       |
| Emails          | Conversational, Mediterranean warmth                  |

---

## 12. Checklist Before Delivering Any Artifact

- [ ] Brand colors used from palette (no hardcoded hex outside design tokens)
- [ ] Correct fonts loaded (Cormorant Garamond + Plus Jakarta Sans + Montserrat)
- [ ] Auth flow includes both Google **and** Apple buttons
- [ ] Supabase RLS policies applied to any new table
- [ ] Mapbox map bounded to Santorini
- [ ] All images served via Cloudinary helper
- [ ] Mobile-first responsive layout verified
- [ ] TypeScript types defined for all props and API responses
- [ ] Environment variables documented in `.env.local` example
- [ ] Accessibility attributes present on interactive elements
