# MOON Restaurant OS

MOON is a multi-tenant restaurant SaaS platform for customer QR ordering, restaurant operations, staff access control, billing, financial history, content management, and future payment integrations.

The product is being built so one codebase can serve many independent restaurant businesses and branches while keeping each tenant's data isolated.

> **Public showcase:** This repository presents MOON at a portfolio/product level. The working source repository remains private.

## Current status

MOON is under active development.

The original foundation-only stage is complete. The project now includes real database architecture, authentication, customer ordering, staff permissions, restaurant settings, multilingual customer UI, financial history, and the first complete table-service Restaurant Operations vertical slice.

### Current implementation status

| Area | Status |
| --- | --- |
| Multi-tenant business and branch foundation | Built |
| Supabase Auth, RLS, staff and customer identity flows | Built |
| Customer QR table ordering | Built |
| Customer takeaway ordering foundation | Built |
| MINIMAL customer menu template | Built |
| Customer account, profile, avatar and email-change flow | Built |
| Restaurant settings and branding | Built |
| About page CMS and restaurant information | Built |
| Seven-language customer interface | Built |
| Arabic RTL customer UI | Built |
| Permission-driven staff access | Built |
| Financial currency snapshots and currency-change architecture | Built |
| Order, bill, payment and closure history foundation | Built |
| Restaurant Operations table-service vertical slice | Built |
| Realtime order, served, payment and table-release refresh | Built |
| Counter / walk-in ordering workflow | Next |
| Unpaid / walkout workflow | Next |
| Voids and refunds | Next |
| Day closing operations | Next |
| Reservations, KDS, inventory and full reports | Planned |
| Payment-provider and integrated POS connections | Planned |
| Customer QR self-payment | Planned |
| MODERN and LUXURY customer templates | Planned |

MOON should not yet be treated as production-ready. Remaining operational modules, deployment configuration, payment-provider integrations, legal review, and launch hardening still need to be completed.

## Restaurant Operations model

MOON intentionally keeps the front-of-house order lifecycle simple:

```text
NEW -> SERVED -> PAID/CLOSED
```

Waiters are not required to click through kitchen states such as Accepted, Preparing, or Ready.

Kitchen-specific preparation states can be added later inside a Kitchen Display System without forcing those states into the waiter workflow.

### Current table-service flow

```text
Table
-> Order Entry
-> NEW
-> SERVED
-> Open Bill
-> Cash or Card
-> Confirm Payment
-> Bill Closed
-> Table Empty
-> History
```

The current implementation supports:

- manual staff ordering from a real restaurant menu
- QR customer orders and staff-entered orders sharing the same open table bill
- multiple orders on one table without creating unnecessary separate bills
- server-authoritative menu prices and modifier prices
- sold-out and unavailable-item validation
- permission-controlled Order Entry
- Live Orders with NEW and SERVED only
- server-authoritative Mark Served behavior
- manual Cash payment confirmation
- manual external-terminal Card payment confirmation
- a centralized settlement engine
- idempotent payment confirmation
- partial-payment-ready bill settlement architecture
- automatic bill closing after full settlement
- automatic table release after the open bill is fully settled
- Order History, Bill History and Payment History retention
- Supabase Realtime refresh for operational order and bill changes

## Order Entry

The staff Order Entry workspace uses the same menu source of truth as the customer QR menu.

The restaurant does not maintain a separate waiter menu.

If an owner changes a menu item's category, price, modifiers, availability, or sold-out state, Order Entry reads from the same underlying menu data.

Desktop and tablet use a two-area workflow:

```text
CURRENT ORDER | MENU / PRODUCT CATALOG
```

Mobile uses a catalogue-first interface with a persistent current-order control instead of squeezing the desktop layout onto a phone.

## Payments architecture

### Supported today

MOON currently supports staff-confirmed manual payments:

- `CASH`
- `CARD`

For Card, the current workflow assumes the restaurant uses an external physical bank terminal. After the terminal approves the payment, authorized staff confirm the Card payment in MOON.

MOON does not claim to have verified that external terminal transaction.

### Designed for future integrations

The bill settlement engine is intentionally separated from the payment source so future integrations can use the same settlement path.

Planned payment sources include:

- integrated physical POS terminals
- Turkish banks and payment providers
- online payment providers
- customer QR-menu payment
- verified provider webhooks

The core settlement rule is based on successful payments against the open bill, not on a UI button directly changing a table to Empty.

## Permission-driven staff experience

MOON does not rely on role names alone for navigation or authorization.

Roles can provide defaults, but actual access is resolved from the permissions assigned to the staff account.

This affects both:

- visible desktop/mobile navigation
- server-side authorization

For example, two users with the same Waiter role can see different modules if one has payment permissions and the other does not.

On mobile, the bottom navigation is permission-driven and the center `+` launcher exposes the remaining modules and actions the logged-in user is allowed to access.

## Customer interface localization

Customer-facing MOON UI currently supports exactly:

- Turkish `tr` as the default
- English `en`
- German `de`
- French `fr`
- Arabic `ar`
- Spanish `es`
- Italian `it`

Arabic uses RTL-aware rendering.

Viewer locale controls formatting while the stored financial currency controls the monetary unit.

Merchant-authored restaurant content is not silently machine-translated. The restaurant's configured content language remains the source language for merchant content.

## Customer account features

The customer system currently includes:

- customer signup and login
- email or phone identity lookup
- customer profile
- display name
- avatar upload/remove
- restaurant membership identity
- durable preferred locale
- secure email-change confirmation
- localized email-change messages

Transactional email is sent through Resend when configured.

## Restaurant settings and content

Restaurant owners can currently manage areas including:

- business name
- business email and phone
- branch details
- opening hours
- logo
- Home hero image
- About hero image
- brand colors
- tagline
- story
- year established
- founder name
- brand values
- city and country
- website
- Instagram, Facebook, TikTok and WhatsApp links
- business content language
- About page sections

Home and About hero images are independent.

About content uses normalized repeatable sections instead of fixed image/style fields.

## Financial architecture

MOON stores immutable currency snapshots on financial records so historical transactions do not silently change when a restaurant later changes its operating currency.

Current financial foundations include:

- order currency snapshots
- order-item currency snapshots
- open-bill currency snapshots
- payment currency snapshots
- business-day-closure currency snapshots
- owner currency-change workflow before financial lock
- platform-admin-assisted currency workflow
- currency-change audit history
- mixed-currency-safe historical totals

Historical money is grouped by currency instead of incorrectly combining values from different currencies.

## Technology stack

| Layer | Technology |
| --- | --- |
| Framework | Next.js 16 App Router |
| UI | React 19 + TypeScript |
| Database | Supabase Postgres |
| Authentication | Supabase Auth |
| Authorization | RLS + capability-based application checks |
| Realtime | Supabase Realtime |
| Storage | Supabase Storage |
| Shared rate limiting | Upstash Redis |
| Validation | Zod |
| Transactional email | Resend HTTP API |
| QR generation | `qrcode` |
| Unit / integration tests | Vitest |
| Browser E2E | Playwright |
| Linting | ESLint |
| Formatting | Prettier |
| Deployment target | Vercel |

## Project architecture

```text
MOON/
├── app/
│   ├── dashboard/[businessSlug]/
│   │   ├── _shell/             Permission-driven operations shell
│   │   ├── live-orders/        NEW and SERVED operations queue
│   │   ├── order-entry/        Staff menu/order workspace
│   │   ├── tables/             Table and open-bill operations
│   │   ├── orders/             Order history
│   │   ├── bills/              Bill history
│   │   ├── payments/           Payment history
│   │   ├── closures/           Closure history foundation
│   │   └── settings/           Restaurant settings and content
│   ├── menu/                    Public table QR experience
│   ├── takeaway/                Public takeaway experience
│   ├── admin/                   Platform-admin tools
│   └── (legal)/                 Privacy and Terms routes
│
├── lib/
│   ├── auth/                    Identity and provisioning safety
│   ├── email/                   Transactional email
│   ├── history/                 Financial history helpers
│   ├── i18n/                    Locale registry and dictionaries
│   ├── legal/                   Product legal-content modules
│   ├── order-entry/             Order-entry types and helpers
│   ├── permissions/             Capability-driven module access
│   ├── realtime/                Operations realtime client logic
│   ├── staff/                   Staff capability helpers
│   └── supabase/                Supabase client boundaries
│
├── supabase/
│   └── migrations/              Versioned database migrations
│
├── tests/
│   ├── unit/
│   ├── integration/
│   └── e2e/
│
└── types/
    └── database.ts
```

## Security principles currently implemented

- tenant and branch boundaries are enforced server-side
- sensitive Supabase service-role access stays server-only
- browser-submitted financial totals are not trusted as authoritative
- menu and modifier prices are revalidated server-side
- staff actions are capability-controlled
- payment confirmation is idempotent
- concurrent settlement attempts are protected
- fully settled bills close through a centralized settlement path
- table occupancy is derived from the open-bill state
- historical financial currency is preserved
- local environment and AI/MCP configuration are excluded from source control

## Important current limitations

The following areas are intentionally not represented as complete:

- counter / walk-in order workflow
- unpaid / walkout handling
- payment voids and refunds
- day closing execution
- Kitchen Display System
- reservations
- operational inventory
- full Reports and Analytics
- owner/staff dashboard localization
- live bank / payment-provider integrations
- customer self-payment from the QR menu
- final subscription and billing product
- complete platform-admin product
- MODERN and LUXURY customer templates
- final production legal review and production deployment configuration

## Near-term roadmap

1. Counter / walk-in orders
2. Unpaid / walkout handling
3. Payment correction, void and refund architecture
4. Day closing operations
5. Kitchen Display System and broader staff operations
6. Reservations
7. Inventory
8. Reports and Analytics
9. Provider-integrated payments and QR self-payment
10. Remaining launch and platform-admin work

## My role

**Johnpaul Ogonna Onyeje** — product concept, workflow architecture, UI/UX direction, requirements definition, testing, iteration, and AI-assisted development.

MOON is an independent product project focused on translating complex restaurant operations into clear, permission-aware digital workflows.

## Repository note

This is the public showcase repository for MOON Restaurant OS. The working source repository remains private and contains the implementation, migrations, tests, and development configuration.
