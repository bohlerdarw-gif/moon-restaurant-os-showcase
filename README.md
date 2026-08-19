# MOON Restaurant OS

**A multi-tenant restaurant operating system for customer QR ordering, front-of-house operations, staff access, bills, payments, and restaurant management.**

> **Public showcase repository.** The working source code remains private. This repository presents the product, architecture, technical decisions, and current development status at a portfolio level.

---

## Overview

MOON is being built as a restaurant SaaS platform where one application can serve multiple independent restaurant businesses and branches while keeping tenant data isolated.

The goal is not to create another generic dashboard. MOON is designed around the actual operational flow of a restaurant: customers order, staff manage tables and orders, bills are settled, tables are released, and the business retains a reliable operational and financial history.

### Core product layers

| Layer | Purpose |
| --- | --- |
| **Customer Experience** | QR menu, table ordering, takeaway, customer accounts and multilingual UI |
| **Restaurant Operations** | Tables, order entry, live orders, bills, payments, history and staff workflows |
| **Restaurant Management** | Staff permissions, branding, business information, content and operational settings |
| **Platform Foundation** | Multi-tenancy, branch separation, authentication, authorization and platform-admin foundations |

---

## Current capabilities

MOON currently includes working foundations and product flows for:

- Multi-tenant businesses and branches
- Supabase authentication and Row Level Security
- Customer QR table ordering
- Takeaway ordering foundation
- Staff Order Entry using the same restaurant menu as the customer QR experience
- Table-service order lifecycle
- Live order operations
- Open bills and payment history
- Manual cash payment confirmation
- Manual external-terminal card payment confirmation
- Automatic bill closure and table release after settlement
- Realtime operational refresh with Supabase Realtime
- Permission-driven staff navigation and server-side authorization
- Restaurant branding and settings
- Restaurant information and About-page content management
- Customer accounts, profiles and avatars
- Seven-language customer interface
- Arabic RTL support
- Historical financial currency snapshots

### Restaurant operations flow

```text
TABLE
  ↓
ORDER ENTRY / QR ORDER
  ↓
NEW
  ↓
SERVED
  ↓
OPEN BILL
  ↓
CASH / CARD
  ↓
PAYMENT CONFIRMED
  ↓
BILL CLOSED
  ↓
TABLE RELEASED
  ↓
HISTORY
```

MOON intentionally keeps the front-of-house order lifecycle simple:

```text
NEW → SERVED → PAID / CLOSED
```

Kitchen-specific preparation states can live inside a future Kitchen Display System instead of forcing unnecessary kitchen-state clicks into the waiter workflow.

---

## Product principles

### One menu source of truth

Customer QR ordering and staff Order Entry read from the same restaurant menu data. Price, category, modifier, availability, and sold-out changes therefore stay consistent across customer and staff experiences.

### Permission-driven staff access

MOON does not rely only on job titles such as *Waiter* or *Manager*. Roles can provide defaults, while actual module access and actions are determined by the permissions assigned to each staff account.

### Server-authoritative financial operations

Financial totals, menu prices, modifiers, bill settlement, and payment state are validated server-side rather than trusting values submitted by the browser.

### Tenant isolation

Business and branch boundaries are enforced at the data and application layers so one restaurant cannot access another tenant's operational data.

### Historical financial integrity

Financial records keep immutable currency snapshots so changing a restaurant's operating currency later does not rewrite historical transactions.

---

## Technology stack

| Area | Technology |
| --- | --- |
| Framework | **Next.js 16 App Router** |
| UI | **React 19 + TypeScript** |
| Database | **Supabase Postgres** |
| Authentication | **Supabase Auth** |
| Authorization | **RLS + capability-based application checks** |
| Realtime | **Supabase Realtime** |
| Storage | **Supabase Storage** |
| Validation | **Zod** |
| Shared rate limiting | **Upstash Redis** |
| Transactional email | **Resend** |
| QR generation | **qrcode** |
| Unit / integration testing | **Vitest** |
| Browser E2E testing | **Playwright** |
| Code quality | **ESLint + Prettier** |
| Deployment target | **Vercel** |

---

## High-level architecture

```text
MOON
├── Customer Experience
│   ├── QR table menu
│   ├── Takeaway
│   ├── Customer account
│   └── Localization
│
├── Restaurant Operations
│   ├── Overview
│   ├── Tables
│   ├── Order Entry
│   ├── Live Orders
│   ├── Bills
│   ├── Payments
│   └── Operational history
│
├── Restaurant Management
│   ├── Staff & permissions
│   ├── Branding
│   ├── Business settings
│   └── Content management
│
└── Platform Foundation
    ├── Multi-tenancy
    ├── Branch isolation
    ├── Authentication / RLS
    ├── Realtime
    └── Platform-admin foundation
```

---

## Localization

The customer-facing interface currently supports:

- Turkish — default
- English
- German
- French
- Arabic
- Spanish
- Italian

Arabic uses RTL-aware rendering.

Merchant-authored restaurant content remains under the restaurant's control rather than being silently machine-translated.

---

## Development status

**MOON is under active development and is not yet production-ready.**

| Area | Status |
| --- | --- |
| Multi-tenant foundation | ✅ Built |
| Auth, RLS and identity flows | ✅ Built |
| QR table ordering | ✅ Built |
| Staff Order Entry | ✅ Built |
| Table-service operations | ✅ Built |
| Bills and manual payment settlement | ✅ Built |
| Realtime operational refresh | ✅ Built |
| Staff permissions | ✅ Built |
| Restaurant branding / content | ✅ Built |
| Seven-language customer UI | ✅ Built |
| Counter / walk-in workflow | 🔄 Next |
| Walkout / unpaid handling | 🔄 Next |
| Voids and refunds | 🔄 Next |
| Day closing | 🔄 Next |
| Kitchen Display System | 🧭 Planned |
| Reservations | 🧭 Planned |
| Inventory | 🧭 Planned |
| Full reports and analytics | 🧭 Planned |
| Integrated payment providers | 🧭 Planned |
| Customer QR self-payment | 🧭 Planned |
| Additional customer menu themes | 🧭 Planned |

---

## Near-term roadmap

1. Counter and walk-in ordering
2. Unpaid / walkout handling
3. Payment corrections, voids and refunds
4. Day-closing operations
5. Kitchen Display System
6. Reservations
7. Inventory
8. Reports and analytics
9. Provider-integrated payments
10. Customer QR self-payment

---

## Security and reliability decisions

The implementation is being developed around several non-negotiable rules:

- tenant and branch boundaries are enforced server-side
- sensitive service credentials remain server-only
- client-submitted financial totals are not authoritative
- menu and modifier pricing is revalidated before order creation
- staff actions are capability-controlled
- payment confirmation is idempotent
- concurrent settlement attempts are protected
- bills close through a centralized settlement path
- table occupancy follows open-bill state instead of UI-only flags
- historical transaction currency is preserved
- local secrets and development configuration are excluded from source control

---

## My role

**Johnpaul Ogonna Onyeje**

Product concept, workflow architecture, UI/UX direction, requirements definition, testing, iteration, and AI-assisted development.

MOON is an independent product project focused on translating complex restaurant operations into clear, permission-aware digital workflows.

---

## Repository note

This repository is intentionally a **public product showcase**, not the application source repository.

The private working repository contains the implementation, database migrations, tests, infrastructure configuration, and active development history.

**MOON Restaurant OS — built around the way restaurants actually operate.**
