# CODEBASE_MAP.md

<!--
last_indexed: 2026-06-04
repo: prawnsolo/Smart-Service-Ticket-
branch: main
refresh_reminder: Re-run codegraph index and update this file by 2026-06-11
codegraph_nodes: 574
codegraph_edges: 1048
codegraph_files: 77
-->

## Project Purpose

**Smart-Service-Ticket** (package name: `apple-pass`) is a production-ready Next.js 15 SaaS starter kit. It provides a complete foundation for building subscription-based software products with authentication, payments, AI chat, file uploads, analytics, and a full dashboard UI. Described as "The Ultimate Nextjs Starter Kit — Build your next SAAS product of your dreams. Batteries included."

## Stack

| Layer | Technology |
|---|---|
| Framework | Next.js 15.3.1 (App Router, Turbopack) |
| Language | TypeScript 5 (strict mode, build errors ignored) |
| Styling | Tailwind CSS v4 + shadcn/ui + Radix UI primitives |
| Database | Neon PostgreSQL (serverless) via `@neondatabase/serverless` |
| ORM | Drizzle ORM 0.43 + Drizzle Kit 0.31 |
| Auth | Better Auth v1.2.8 (server + client) |
| Payments | Polar.sh (`@polar-sh/better-auth`, `@polar-sh/sdk`) |
| AI | Vercel AI SDK (`ai`, `@ai-sdk/openai`) with GPT-4o |
| File Storage | Cloudflare R2 via `@aws-sdk/client-s3` (S3-compatible) |
| Analytics | PostHog (`posthog-js`, `posthog-node`) + Vercel Analytics |
| State/Forms | TanStack React Query v5 + React Hook Form + Zod |
| Animations | Framer Motion + Motion |
| Charts | Recharts |
| Toast | Sonner |
| Deployment | Vercel (recommended) |

## Directory Structure

```
Smart-Service-Ticket/
├── app/                          # Next.js App Router root
│   ├── layout.tsx                # Root layout: ThemeProvider, Toaster, Vercel Analytics
│   ├── page.tsx                  # Landing page: Hero, Integrations, PricingTable, Footer
│   ├── globals.css               # Global styles
│   ├── favicon.ico
│   ├── (auth)/                   # Auth route group (implied by sign-in/sign-up at root)
│   │   ├── sign-in/page.tsx      # Sign-in page
│   │   └── sign-up/page.tsx      # Sign-up page
│   ├── pricing/
│   │   ├── page.tsx              # Public pricing page (SSR subscription check)
│   │   └── _component/
│   │       └── pricing-table.tsx # Polar checkout UI (single Starter tier)
│   ├── pricing/                  # Also inlined on home page
│   ├── success/page.tsx          # Post-checkout success page
│   ├── privacy-policy/page.tsx   # Legal page
│   ├── terms-of-service/page.tsx # Legal page
│   ├── dashboard/                # Protected area (middleware guards /dashboard/**)
│   │   ├── layout.tsx            # Dashboard shell: Sidebar + TopNav + Chatbot widget
│   │   ├── page.tsx              # Dashboard home: SectionCards + ChartAreaInteractive
│   │   ├── chat/page.tsx         # Full-page AI chat interface
│   │   ├── upload/page.tsx       # File upload to R2 with drag-and-drop gallery
│   │   ├── payment/page.tsx      # Subscription details + payment gating overlay
│   │   ├── settings/page.tsx     # Profile management + billing history tabs
│   │   └── _components/
│   │       ├── sidebar.tsx       # Navigation sidebar
│   │       ├── navbar.tsx        # Top navigation bar
│   │       ├── chatbot.tsx       # Floating AI chat widget
│   │       ├── chart-interactive.tsx  # Recharts interactive area chart
│   │       ├── section-cards.tsx # Dashboard stat cards
│   │       └── mode-toggle.tsx   # Dark/light theme toggle
│   └── api/
│       ├── auth/[...all]/route.ts   # Better Auth catch-all handler
│       ├── chat/route.ts            # OpenAI streaming chat endpoint
│       ├── subscription/route.ts   # GET subscription status for current user
│       └── upload-image/route.ts   # POST image upload to Cloudflare R2
├── components/
│   ├── ui/                       # 20+ shadcn/ui components (avatar, badge, button, card, chart, dialog, form, input, progress, select, skeleton, sonner, tabs, etc.)
│   ├── homepage/
│   │   ├── hero-section.tsx      # Landing page hero
│   │   ├── integrations.tsx      # Tech stack logos section
│   │   └── footer.tsx            # Site footer
│   ├── logos/                    # SVG logos: BetterAuth, NeonPostgres, Nextjs, Polar, TailwindCSS, shadcnui
│   ├── provider.tsx              # ThemeProvider (next-themes)
│   └── user-profile.tsx          # User profile display component
├── lib/
│   ├── auth.ts                   # Better Auth server config (Polar plugin, Google OAuth, Drizzle adapter, webhooks)
│   ├── auth-client.ts            # Better Auth browser client (organizationClient + polarClient plugins)
│   ├── subscription.ts           # Server-side subscription helpers (getSubscriptionDetails, isUserSubscribed, hasAccessToProduct)
│   ├── upload-image.ts           # R2 upload utility (S3Client PutObjectCommand)
│   └── utils.ts                  # cn() tailwind utility
├── db/
│   ├── drizzle.ts                # DB connection: drizzle(neon-http, DATABASE_URL)
│   └── schema.ts                 # Drizzle schema (see DB Schema section)
├── auth-schema.ts                # Alternate/generated Better Auth schema (snake_case variant)
├── drizzle.config.ts             # Drizzle Kit config: schema=db/schema.ts, out=db/migrations, dialect=postgresql
├── middleware.ts                 # Route protection (session cookie check)
├── next.config.ts                # Next.js config (image remotePatterns for R2 + Vercel Blob)
├── tailwind.config.ts            # Tailwind configuration
├── tsconfig.json                 # TypeScript config
├── components.json               # shadcn/ui registry config
└── package.json
```

## Key Routes

| Route | Type | Description |
|---|---|---|
| `/` | Public (SSR) | Landing page with hero, integrations showcase, pricing, footer |
| `/sign-in` | Public | Better Auth sign-in form; redirects to `/dashboard` if already logged in |
| `/sign-up` | Public | Better Auth sign-up form; redirects to `/dashboard` if already logged in |
| `/pricing` | Public (SSR) | Standalone pricing page with Polar checkout |
| `/success` | Public | Post-checkout success landing |
| `/privacy-policy` | Public | Privacy policy |
| `/terms-of-service` | Public | Terms of service |
| `/dashboard` | Protected | Main dashboard: stat cards + interactive area chart |
| `/dashboard/chat` | Protected | Full-page AI chat with GPT-4o + web search |
| `/dashboard/upload` | Protected | Drag-and-drop image uploads to R2 with gallery |
| `/dashboard/payment` | Protected | Subscription info (gated — requires active subscription) |
| `/dashboard/settings` | Protected | Profile update (name, avatar) + billing history tabs |

## API Endpoints

| Endpoint | Method | Auth | Description |
|---|---|---|---|
| `/api/auth/[...all]` | ALL | — | Better Auth catch-all (sign-in, sign-up, session, OAuth callbacks, Polar webhooks, checkout, portal) |
| `/api/chat` | POST | — | Vercel AI SDK streaming chat. Body: `{ messages }`. Uses GPT-4o with `web_search_preview` tool. Returns data stream. |
| `/api/subscription` | GET | Session required | Returns `SubscriptionDetailsResult` for the authenticated user. 401 if unauthenticated. |
| `/api/upload-image` | POST | — | Accepts `multipart/form-data` with `file` field. Validates MIME type (images only) and 10MB max. Uploads to R2 and returns `{ url }`. |
| `/api/payments/webhooks` | POST | Webhook secret | Polar.sh subscription webhook (bypasses middleware auth). Handled internally by Better Auth Polar plugin. |

## Auth Setup

Auth is powered by **Better Auth v1.2.8** configured in `lib/auth.ts`.

- **Database adapter**: Drizzle (PostgreSQL, Neon)
- **Social providers**: Google OAuth (`GOOGLE_CLIENT_ID` / `GOOGLE_CLIENT_SECRET`)
- **Session caching**: Cookie cache, 5-minute TTL
- **Plugins**:
  - `polar()` — creates Polar customer on sign-up, wires checkout/portal/usage/webhooks
  - `nextCookies()` — Next.js server-side cookie integration
- **Client** (`lib/auth-client.ts`): `createAuthClient` with `organizationClient()` and `polarClient()` plugins
- **Middleware** (`middleware.ts`): Uses `getSessionCookie` to guard `/dashboard/**` routes; redirects authenticated users away from `/sign-in` and `/sign-up`. Polar webhook path `/api/payments/webhooks` is explicitly excluded.

## DB Schema / ORM

ORM: **Drizzle ORM** with `drizzle-orm/neon-http` driver.
Config: `drizzle.config.ts` — schema at `./db/schema.ts`, migrations output to `./db/migrations`.

### Tables

**`user`** — Core user record (Better Auth managed)
- `id` (text PK), `name`, `email` (unique), `emailVerified`, `image`, `createdAt`, `updatedAt`

**`session`** — Active sessions (Better Auth managed)
- `id`, `expiresAt`, `token` (unique), `ipAddress`, `userAgent`, `userId` (FK → user, cascade delete), `createdAt`, `updatedAt`

**`account`** — OAuth provider accounts (Better Auth managed)
- `id`, `accountId`, `providerId`, `userId` (FK → user, cascade delete), `accessToken`, `refreshToken`, `idToken`, token expiry timestamps, `scope`, `password`, `createdAt`, `updatedAt`

**`verification`** — Email/magic-link verification tokens (Better Auth managed)
- `id`, `identifier`, `value`, `expiresAt`, `createdAt`, `updatedAt`

**`subscription`** — Polar.sh subscription records (webhook-driven upsert)
- `id` (PK, Polar subscription ID), `createdAt`, `modifiedAt`, `amount`, `currency`, `recurringInterval`, `status`, `currentPeriodStart`, `currentPeriodEnd`, `cancelAtPeriodEnd`, `canceledAt`, `startedAt`, `endsAt`, `endedAt`, `customerId`, `productId`, `discountId`, `checkoutId`, `customerCancellationReason`, `customerCancellationComment`, `metadata` (JSON string), `customFieldData` (JSON string), `userId` (FK → user, nullable)

Subscription records are upserted via `onConflictDoUpdate` in the `subscription.created/active/canceled/revoked/uncanceled/updated` webhook handler inside `lib/auth.ts`.

## Payment Integration

Provider: **Polar.sh** (sandbox mode — `server: "sandbox"` in `lib/auth.ts`; change to `"production"` for live).

**Flow:**
1. User clicks "Get Started" on `/pricing` or `/` pricing table
2. `authClient.checkout({ products: [productId], slug })` is called client-side
3. Polar redirects to checkout; on success returns to `NEXT_PUBLIC_APP_URL/${POLAR_SUCCESS_URL}`
4. Polar fires subscription webhooks to `/api/auth/[...all]` (handled by Better Auth Polar plugin)
5. Webhook handler upserts `subscription` table row with full subscription data
6. `getSubscriptionDetails()` / `isUserSubscribed()` server helpers read the `subscription` table to gate content

**Pricing tiers configured (README):**
- Starter: $99/month (env `NEXT_PUBLIC_STARTER_TIER` / `NEXT_PUBLIC_STARTER_SLUG`)
- Professional: $499/month (mentioned in README; second tier not fully wired in current pricing-table.tsx — only Starter tier card is rendered)

**Customer portal**: `authClient.customer.portal()` opens Polar self-service portal. Used in Settings billing tab and payment page.

## Environment Variables

```env
# App
NEXT_PUBLIC_APP_URL=             # e.g. https://your-domain.com

# Database (Neon PostgreSQL)
DATABASE_URL=                    # Neon connection string

# Better Auth
BETTER_AUTH_SECRET=              # Random secret for token signing

# Google OAuth
GOOGLE_CLIENT_ID=
GOOGLE_CLIENT_SECRET=

# Polar.sh (payments)
POLAR_ACCESS_TOKEN=              # Polar API access token
POLAR_WEBHOOK_SECRET=            # Polar webhook signing secret
POLAR_SUCCESS_URL=               # Path after successful checkout (e.g. "success")

# Polar product IDs (public — used client-side)
NEXT_PUBLIC_STARTER_TIER=        # Polar product ID for Starter plan
NEXT_PUBLIC_STARTER_SLUG=        # Polar product slug for Starter plan

# OpenAI (AI chat)
OPENAI_API_KEY=

# Cloudflare R2 (file storage)
CLOUDFLARE_ACCOUNT_ID=
R2_UPLOAD_IMAGE_ACCESS_KEY_ID=
R2_UPLOAD_IMAGE_SECRET_ACCESS_KEY=
R2_UPLOAD_IMAGE_BUCKET_NAME=
```

Note: `lib/upload-image.ts` currently hardcodes the public R2 CDN base URL (`pub-6f0cf05705c7412b93a792350f3b3aa5.r2.dev`). This should be moved to an env var (`NEXT_PUBLIC_R2_PUBLIC_URL`) when customizing.

## Codegraph Coverage

| Metric | Value |
|---|---|
| Files indexed | 77 |
| Nodes | 574 |
| Edges | 1,048 |
| DB size | 0.96 MB |
| Languages | TypeScript (18 files), TSX (57 files), JavaScript (2 files) |
| Node kinds | import (263), function (143), file (77), constant (41), property (33), type_alias (9), interface (8) |
| Index location | `.codegraph/` in repo root |

Run `codegraph query` or `codegraph status` from the repo root to navigate the graph. Re-index after major refactors with `codegraph init --index .`.
