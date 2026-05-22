# MOVE Properties Website — Design Spec

**Date:** 2026-05-22  
**Status:** Approved

## Overview

A marketing and tenant-facing website for MOVE Properties, a real estate holding company with three co-founding partners. The site showcases available rental units, property specs, team bios, and provides lightweight tenant tools (rent payment link and maintenance request form). Built to be deployed to Vercel with zero infrastructure management.

---

## Tech Stack

| Layer | Choice |
|---|---|
| Framework | Next.js 14+ (App Router) |
| Language | TypeScript |
| Styling | Tailwind CSS |
| Component Library | shadcn/ui |
| Deployment | Vercel (auto-deploy on push to `main`) |

**Aesthetic:** Clean and minimal — white/light backgrounds, generous whitespace, modern sans-serif typography.

---

## Pages & Routing

| Route | Description |
|---|---|
| `/` | Home — hero section, company intro, featured properties preview |
| `/properties` | Listings — grid of all properties (buildings) |
| `/properties/[id]` | Property detail — building info, photos, and all units |
| `/team` | Team — three partner bios |
| `/tenants` | Tenant Tools — rent payment link + maintenance request form |

A persistent `Navbar` and `Footer` wrap all pages.

---

## Data Models

All content is managed via static TypeScript files in `/data`. No database or CMS. Update listings by editing these files directly.

### `data/properties.ts` — Buildings

```ts
{
  id: string
  name: string           // e.g. "The Monroe"
  address: string
  city: string
  yearBuilt?: number
  totalUnits: number
  images: string[]       // paths to /public/images/properties/
  amenities: string[]    // shared building amenities e.g. ["Parking", "Rooftop"]
  description: string
}
```

### `data/units.ts` — Rentable Spaces

```ts
{
  id: string
  propertyId: string     // foreign key linking to a Property
  unitNumber: string     // e.g. "2B"
  bedrooms: number
  bathrooms: number
  sqft: number
  rentPrice: number
  available: boolean
  availableDate?: string // e.g. "July 1, 2026"
  images: string[]       // paths to /public/images/units/
  amenities: string[]    // unit-specific e.g. ["In-unit laundry", "Private balcony"]
  description: string
}
```

### `data/team.ts` — Partners

```ts
{
  name: string
  title: string          // e.g. "Co-Founder & Operations"
  bio: string
  photo: string          // path to /public/images/team/
}
```

All files ship with mock/placeholder data and images so the site looks populated from day one.

---

## Components

### Layout
- **`Navbar`** — Logo left, nav links right (`Properties`, `Team`, `Tenants`). Hamburger menu on mobile.
- **`Footer`** — Company name, nav links, contact email.

### Property Pages
- **`PropertyCard`** — Building thumbnail, name, address, unit count, amenities preview. Used in `/properties` grid.
- **`UnitCard`** — Unit photo, bed/bath/sqft badge row, rent price, availability badge (green "Available" / gray "Unavailable"). Used on property detail page.

### Team Page
- **`TeamMemberCard`** — Headshot, name, title, bio. Three rendered side by side.

### Tenant Tools Page
- **`RentPaymentSection`** — Card with a "Pay Rent" button. Links to a configurable URL (Venmo, Zelle, PayPal, or placeholder). No auth required.
- **`MaintenanceRequestForm`** — Fields: Name, Unit Number, Issue Type (dropdown: Plumbing, Electrical, HVAC, Appliance, Other), Description. Submit shows a success toast via shadcn/ui. No backend — form data is not persisted at this stage.

### Shared
- **`Badge`** — Reusable pill for availability status, bed/bath counts, amenity tags. Provided by shadcn/ui.

---

## Project Structure

```
move-properties/
├── app/
│   ├── page.tsx                  # Home
│   ├── properties/
│   │   ├── page.tsx              # All properties grid
│   │   └── [id]/page.tsx         # Property detail + units list
│   ├── team/
│   │   └── page.tsx
│   └── tenants/
│       └── page.tsx
├── components/
│   ├── layout/
│   │   ├── Navbar.tsx
│   │   └── Footer.tsx
│   ├── properties/
│   │   ├── PropertyCard.tsx
│   │   └── UnitCard.tsx
│   ├── team/
│   │   └── TeamMemberCard.tsx
│   └── tenants/
│       ├── RentPaymentSection.tsx
│       └── MaintenanceRequestForm.tsx
├── data/
│   ├── properties.ts
│   ├── units.ts
│   └── team.ts
└── public/
    └── images/
        ├── properties/
        ├── units/
        └── team/
```

---

## Deployment

1. Push project to a GitHub repository
2. Connect the repo to Vercel (one-click setup at vercel.com)
3. Every push to `main` triggers an automatic deploy
4. Free tier is sufficient for this scale

No environment variables or server configuration required at launch.

---

## Out of Scope (for now)

- Tenant authentication / login
- Real payment processing (Stripe, etc.)
- Backend persistence for maintenance requests
- CMS / admin panel for managing listings
- Email notifications on form submissions
