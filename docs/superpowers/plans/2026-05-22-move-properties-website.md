# MOVE Properties Website Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a clean, minimal real estate marketing and tenant-tools website for MOVE Properties using Next.js, Tailwind CSS, and shadcn/ui, deployable to Vercel.

**Architecture:** Static Next.js App Router site with no backend. All content (properties, units, team) lives in TypeScript data files edited directly. Tenant tools are mock UI only — a payment button linking to a placeholder URL and a maintenance form that shows a success toast on submit.

**Tech Stack:** Next.js 15 (App Router), TypeScript, Tailwind CSS, shadcn/ui, Sonner (toasts), Vercel

---

## File Map

| File | Responsibility |
|---|---|
| `types/index.ts` | Property, Unit, TeamMember TypeScript interfaces |
| `data/properties.ts` | Mock property (building) records |
| `data/units.ts` | Mock unit records linked to properties |
| `data/team.ts` | Mock team member records |
| `app/layout.tsx` | Root layout — Navbar, Footer, Toaster |
| `app/globals.css` | Global styles (shadcn/ui base) |
| `app/page.tsx` | Home page — hero, intro, featured properties |
| `app/properties/page.tsx` | All properties grid |
| `app/properties/[id]/page.tsx` | Property detail + units list |
| `app/team/page.tsx` | Team bios page |
| `app/tenants/page.tsx` | Tenant tools page |
| `components/layout/Navbar.tsx` | Sticky top nav with mobile menu |
| `components/layout/Footer.tsx` | Site footer |
| `components/properties/PropertyCard.tsx` | Building card for grid |
| `components/properties/UnitCard.tsx` | Unit card for property detail |
| `components/team/TeamMemberCard.tsx` | Team member bio card |
| `components/tenants/RentPaymentSection.tsx` | Pay Rent card with link button |
| `components/tenants/MaintenanceRequestForm.tsx` | Maintenance request form with toast |
| `next.config.ts` | Next.js config — allows placehold.co images |

---

## Task 1: Scaffold Next.js Project and Install Dependencies

**Files:**
- Create: `package.json`, `tsconfig.json`, `next.config.ts`, `tailwind.config.ts`, `app/globals.css` (all via scaffolding)

- [ ] **Step 1: Run create-next-app in the current directory**

Run from `/Users/russellmorganspence/Documents/Real Estate Investing/MOVE`:

```bash
npx create-next-app@latest . --typescript --tailwind --eslint --app --no-src-dir --import-alias "@/*"
```

When prompted about the directory not being empty (due to `docs/`), type `y` and press Enter.

When asked about `Would you like to use Turbopack?`, answer `No`.

Expected: Next.js project files created in the current directory including `app/`, `components/`, `public/`, `package.json`, `tsconfig.json`, `next.config.ts`, `tailwind.config.ts`.

- [ ] **Step 2: Initialize shadcn/ui**

```bash
npx shadcn@latest init
```

When prompted:
- Style: **Default**
- Base color: **Neutral**
- CSS variables: **Yes**

Expected: `components/ui/` directory created, `app/globals.css` updated with CSS variables, `components.json` created.

- [ ] **Step 3: Add required shadcn/ui components**

```bash
npx shadcn@latest add button card badge input textarea label select sonner
```

Expected: Component files created in `components/ui/` — `button.tsx`, `card.tsx`, `badge.tsx`, `input.tsx`, `textarea.tsx`, `label.tsx`, `select.tsx`, `sonner.tsx`.

- [ ] **Step 4: Update next.config.ts to allow placeholder images**

Replace the contents of `next.config.ts` with:

```ts
import type { NextConfig } from 'next'

const nextConfig: NextConfig = {
  images: {
    remotePatterns: [
      {
        protocol: 'https',
        hostname: 'placehold.co',
      },
    ],
  },
}

export default nextConfig
```

- [ ] **Step 5: Verify TypeScript compiles cleanly**

```bash
npx tsc --noEmit
```

Expected: No errors output.

- [ ] **Step 6: Commit**

```bash
git add -A
git commit -m "feat: scaffold Next.js project with Tailwind and shadcn/ui"
```

---

## Task 2: TypeScript Types and Mock Data

**Files:**
- Create: `types/index.ts`
- Create: `data/properties.ts`
- Create: `data/units.ts`
- Create: `data/team.ts`

- [ ] **Step 1: Create `types/index.ts`**

```ts
export interface Property {
  id: string
  name: string
  address: string
  city: string
  yearBuilt?: number
  totalUnits: number
  images: string[]
  amenities: string[]
  description: string
}

export interface Unit {
  id: string
  propertyId: string
  unitNumber: string
  bedrooms: number
  bathrooms: number
  sqft: number
  rentPrice: number
  available: boolean
  availableDate?: string
  images: string[]
  amenities: string[]
  description: string
}

export interface TeamMember {
  name: string
  title: string
  bio: string
  photo: string
}
```

- [ ] **Step 2: Create `data/properties.ts`**

```ts
import { Property } from '@/types'

export const properties: Property[] = [
  {
    id: 'the-monroe',
    name: 'The Monroe',
    address: '123 Monroe Street',
    city: 'Chicago, IL',
    yearBuilt: 1998,
    totalUnits: 6,
    images: ['https://placehold.co/800x600/e5e7eb/6b7280?text=The+Monroe'],
    amenities: ['Parking', 'Laundry Room', 'Bike Storage'],
    description:
      'A well-maintained six-unit building in the heart of Lincoln Square. Close to shops, restaurants, and the Brown Line.',
  },
  {
    id: 'the-lakeview',
    name: 'The Lakeview',
    address: '456 Lakeview Avenue',
    city: 'Chicago, IL',
    yearBuilt: 2005,
    totalUnits: 4,
    images: ['https://placehold.co/800x600/e5e7eb/6b7280?text=The+Lakeview'],
    amenities: ['Rooftop Deck', 'Intercom', 'Storage Units'],
    description:
      'A modern four-unit building steps from Lake Michigan with stunning rooftop views.',
  },
  {
    id: 'the-clark',
    name: 'The Clark',
    address: '789 Clark Street',
    city: 'Chicago, IL',
    yearBuilt: 2012,
    totalUnits: 8,
    images: ['https://placehold.co/800x600/e5e7eb/6b7280?text=The+Clark'],
    amenities: ['Gym', 'Package Room', 'Pet Friendly'],
    description:
      'A newer eight-unit building in Wicker Park with modern finishes and in-building amenities.',
  },
]
```

- [ ] **Step 3: Create `data/units.ts`**

```ts
import { Unit } from '@/types'

export const units: Unit[] = [
  {
    id: 'monroe-1a',
    propertyId: 'the-monroe',
    unitNumber: '1A',
    bedrooms: 1,
    bathrooms: 1,
    sqft: 650,
    rentPrice: 1400,
    available: true,
    images: ['https://placehold.co/800x600/f3f4f6/9ca3af?text=Unit+1A'],
    amenities: ['In-unit Laundry', 'Central AC'],
    description: 'Cozy ground-floor one-bedroom with updated kitchen and hardwood floors.',
  },
  {
    id: 'monroe-2b',
    propertyId: 'the-monroe',
    unitNumber: '2B',
    bedrooms: 2,
    bathrooms: 1,
    sqft: 950,
    rentPrice: 1900,
    available: false,
    availableDate: 'August 1, 2026',
    images: ['https://placehold.co/800x600/f3f4f6/9ca3af?text=Unit+2B'],
    amenities: ['Balcony', 'Central AC', 'Dishwasher'],
    description: 'Bright two-bedroom on the second floor with a private balcony.',
  },
  {
    id: 'lakeview-1a',
    propertyId: 'the-lakeview',
    unitNumber: '1A',
    bedrooms: 2,
    bathrooms: 2,
    sqft: 1100,
    rentPrice: 2400,
    available: true,
    images: ['https://placehold.co/800x600/f3f4f6/9ca3af?text=Unit+1A'],
    amenities: ['In-unit Laundry', 'Private Patio', 'Stainless Appliances'],
    description: 'Spacious two-bed, two-bath garden unit with private outdoor patio.',
  },
  {
    id: 'clark-3c',
    propertyId: 'the-clark',
    unitNumber: '3C',
    bedrooms: 1,
    bathrooms: 1,
    sqft: 700,
    rentPrice: 1600,
    available: true,
    images: ['https://placehold.co/800x600/f3f4f6/9ca3af?text=Unit+3C'],
    amenities: ['Gym Access', 'High-speed Internet', 'Pet Friendly'],
    description: 'Modern one-bedroom on the third floor with city views and access to building gym.',
  },
]
```

- [ ] **Step 4: Create `data/team.ts`**

Update the placeholder names with the actual partner names when known.

```ts
import { TeamMember } from '@/types'

export const team: TeamMember[] = [
  {
    name: 'Russell Morgan-Spence',
    title: 'Co-Founder & Acquisitions',
    bio: 'Russell leads property acquisitions and investment strategy for MOVE Properties. With a background in finance and a passion for real estate, he identifies opportunities that deliver value for both investors and tenants.',
    photo: 'https://placehold.co/400x500/e5e7eb/6b7280?text=Russell',
  },
  {
    name: 'Partner Two',
    title: 'Co-Founder & Operations',
    bio: 'Operations-focused partner responsible for day-to-day property management, vendor relationships, and ensuring tenants have an exceptional experience in every MOVE Properties building.',
    photo: 'https://placehold.co/400x500/e5e7eb/6b7280?text=Partner+2',
  },
  {
    name: 'Partner Three',
    title: 'Co-Founder & Finance',
    bio: 'Finance-focused partner overseeing budgeting, accounting, and financial planning across the portfolio. Ensures MOVE Properties maintains healthy margins while investing in quality improvements.',
    photo: 'https://placehold.co/400x500/e5e7eb/6b7280?text=Partner+3',
  },
]
```

- [ ] **Step 5: Verify TypeScript compiles cleanly**

```bash
npx tsc --noEmit
```

Expected: No errors.

- [ ] **Step 6: Commit**

```bash
git add types/ data/
git commit -m "feat: add TypeScript types and mock data for properties, units, and team"
```

---

## Task 3: Root Layout, Navbar, and Footer

**Files:**
- Create: `components/layout/Navbar.tsx`
- Create: `components/layout/Footer.tsx`
- Modify: `app/layout.tsx`

- [ ] **Step 1: Create `components/layout/Navbar.tsx`**

```tsx
'use client'

import Link from 'next/link'
import { useState } from 'react'
import { Menu, X } from 'lucide-react'
import { Button } from '@/components/ui/button'

const navLinks = [
  { href: '/properties', label: 'Properties' },
  { href: '/team', label: 'Team' },
  { href: '/tenants', label: 'Tenant Tools' },
]

export function Navbar() {
  const [open, setOpen] = useState(false)

  return (
    <header className="sticky top-0 z-50 w-full border-b bg-white">
      <div className="container mx-auto flex h-16 items-center justify-between px-4">
        <Link href="/" className="text-xl font-semibold tracking-tight">
          MOVE Properties
        </Link>
        <nav className="hidden gap-6 md:flex">
          {navLinks.map(({ href, label }) => (
            <Link
              key={href}
              href={href}
              className="text-sm font-medium text-muted-foreground hover:text-foreground transition-colors"
            >
              {label}
            </Link>
          ))}
        </nav>
        <Button
          variant="ghost"
          size="icon"
          className="md:hidden"
          onClick={() => setOpen(!open)}
          aria-label="Toggle menu"
        >
          {open ? <X className="h-5 w-5" /> : <Menu className="h-5 w-5" />}
        </Button>
      </div>
      {open && (
        <div className="border-t bg-white md:hidden">
          <nav className="flex flex-col gap-3 px-4 py-3">
            {navLinks.map(({ href, label }) => (
              <Link
                key={href}
                href={href}
                className="text-sm font-medium"
                onClick={() => setOpen(false)}
              >
                {label}
              </Link>
            ))}
          </nav>
        </div>
      )}
    </header>
  )
}
```

- [ ] **Step 2: Create `components/layout/Footer.tsx`**

```tsx
import Link from 'next/link'

export function Footer() {
  return (
    <footer className="mt-auto border-t bg-white">
      <div className="container mx-auto flex flex-col items-center justify-between gap-4 px-4 py-8 md:flex-row">
        <p className="text-sm font-semibold">MOVE Properties</p>
        <nav className="flex gap-6">
          <Link
            href="/properties"
            className="text-sm text-muted-foreground hover:text-foreground transition-colors"
          >
            Properties
          </Link>
          <Link
            href="/team"
            className="text-sm text-muted-foreground hover:text-foreground transition-colors"
          >
            Team
          </Link>
          <Link
            href="/tenants"
            className="text-sm text-muted-foreground hover:text-foreground transition-colors"
          >
            Tenant Tools
          </Link>
        </nav>
        <p className="text-sm text-muted-foreground">contact@moveproperties.com</p>
      </div>
    </footer>
  )
}
```

- [ ] **Step 3: Replace `app/layout.tsx`**

```tsx
import type { Metadata } from 'next'
import { Inter } from 'next/font/google'
import './globals.css'
import { Navbar } from '@/components/layout/Navbar'
import { Footer } from '@/components/layout/Footer'
import { Toaster } from '@/components/ui/sonner'

const inter = Inter({ subsets: ['latin'] })

export const metadata: Metadata = {
  title: 'MOVE Properties',
  description: 'Quality rentals. Reliable management.',
}

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en">
      <body className={`${inter.className} flex min-h-screen flex-col`}>
        <Navbar />
        <main className="flex-1">{children}</main>
        <Footer />
        <Toaster />
      </body>
    </html>
  )
}
```

- [ ] **Step 4: Verify TypeScript compiles cleanly**

```bash
npx tsc --noEmit
```

Expected: No errors.

- [ ] **Step 5: Commit**

```bash
git add components/layout/ app/layout.tsx
git commit -m "feat: add Navbar, Footer, and root layout"
```

---

## Task 4: Home Page

**Files:**
- Modify: `app/page.tsx`

- [ ] **Step 1: Replace `app/page.tsx`**

```tsx
import Link from 'next/link'
import { Button } from '@/components/ui/button'
import { PropertyCard } from '@/components/properties/PropertyCard'
import { properties } from '@/data/properties'

export default function HomePage() {
  const featured = properties.slice(0, 3)

  return (
    <div>
      {/* Hero */}
      <section className="bg-neutral-50 px-4 py-24">
        <div className="container mx-auto max-w-3xl text-center">
          <h1 className="mb-4 text-5xl font-semibold tracking-tight">
            Quality Rentals.<br />Reliable Management.
          </h1>
          <p className="mb-8 text-lg text-muted-foreground">
            MOVE Properties owns and manages a curated portfolio of residential rental properties.
            We&apos;re committed to exceptional spaces and transparent tenant relationships.
          </p>
          <Button asChild size="lg">
            <Link href="/properties">View Properties</Link>
          </Button>
        </div>
      </section>

      {/* Featured properties */}
      <section className="px-4 py-20">
        <div className="container mx-auto">
          <h2 className="mb-8 text-2xl font-semibold">Our Properties</h2>
          <div className="grid gap-6 sm:grid-cols-2 lg:grid-cols-3">
            {featured.map((property) => (
              <PropertyCard key={property.id} property={property} />
            ))}
          </div>
          <div className="mt-10 text-center">
            <Button variant="outline" asChild>
              <Link href="/properties">See All Properties</Link>
            </Button>
          </div>
        </div>
      </section>

      {/* About */}
      <section className="bg-neutral-50 px-4 py-20">
        <div className="container mx-auto max-w-2xl text-center">
          <h2 className="mb-4 text-2xl font-semibold">About MOVE Properties</h2>
          <p className="leading-relaxed text-muted-foreground">
            Founded by three partners with a shared vision, MOVE Properties acquires and manages
            residential rental properties. We believe great housing starts with great management —
            responsive maintenance, transparent pricing, and communities worth coming home to.
          </p>
        </div>
      </section>
    </div>
  )
}
```

Note: `PropertyCard` doesn't exist yet — TypeScript will error until Task 5 is complete. That's expected at this stage.

- [ ] **Step 2: Commit**

```bash
git add app/page.tsx
git commit -m "feat: add home page with hero, featured properties, and about sections"
```

---

## Task 5: PropertyCard and Properties Grid Page

**Files:**
- Create: `components/properties/PropertyCard.tsx`
- Create: `app/properties/page.tsx`

- [ ] **Step 1: Create `components/properties/PropertyCard.tsx`**

```tsx
import Image from 'next/image'
import Link from 'next/link'
import { Card, CardContent, CardHeader } from '@/components/ui/card'
import { Badge } from '@/components/ui/badge'
import type { Property } from '@/types'

interface PropertyCardProps {
  property: Property
}

export function PropertyCard({ property }: PropertyCardProps) {
  return (
    <Link href={`/properties/${property.id}`} className="block">
      <Card className="overflow-hidden transition-shadow hover:shadow-md">
        <div className="relative h-48 w-full">
          <Image
            src={property.images[0]}
            alt={property.name}
            fill
            className="object-cover"
          />
        </div>
        <CardHeader className="pb-2">
          <h3 className="text-lg font-semibold">{property.name}</h3>
          <p className="text-sm text-muted-foreground">
            {property.address}, {property.city}
          </p>
        </CardHeader>
        <CardContent>
          <p className="mb-3 text-sm text-muted-foreground">{property.totalUnits} units</p>
          <div className="flex flex-wrap gap-1">
            {property.amenities.slice(0, 3).map((amenity) => (
              <Badge key={amenity} variant="secondary">
                {amenity}
              </Badge>
            ))}
          </div>
        </CardContent>
      </Card>
    </Link>
  )
}
```

- [ ] **Step 2: Create `app/properties/page.tsx`**

```tsx
import { PropertyCard } from '@/components/properties/PropertyCard'
import { properties } from '@/data/properties'

export default function PropertiesPage() {
  return (
    <div className="container mx-auto px-4 py-16">
      <h1 className="mb-2 text-3xl font-semibold">Our Properties</h1>
      <p className="mb-10 text-muted-foreground">Browse our portfolio of residential buildings.</p>
      <div className="grid gap-6 sm:grid-cols-2 lg:grid-cols-3">
        {properties.map((property) => (
          <PropertyCard key={property.id} property={property} />
        ))}
      </div>
    </div>
  )
}
```

- [ ] **Step 3: Verify TypeScript compiles cleanly**

```bash
npx tsc --noEmit
```

Expected: No errors.

- [ ] **Step 4: Commit**

```bash
git add components/properties/PropertyCard.tsx app/properties/page.tsx
git commit -m "feat: add PropertyCard component and properties grid page"
```

---

## Task 6: UnitCard and Property Detail Page

**Files:**
- Create: `components/properties/UnitCard.tsx`
- Create: `app/properties/[id]/page.tsx`

- [ ] **Step 1: Create `components/properties/UnitCard.tsx`**

```tsx
import Image from 'next/image'
import { Card, CardContent, CardHeader } from '@/components/ui/card'
import { Badge } from '@/components/ui/badge'
import type { Unit } from '@/types'

interface UnitCardProps {
  unit: Unit
}

export function UnitCard({ unit }: UnitCardProps) {
  const availabilityLabel = unit.available
    ? 'Available'
    : unit.availableDate
    ? `Available ${unit.availableDate}`
    : 'Unavailable'

  return (
    <Card className="overflow-hidden">
      <div className="relative h-48 w-full">
        <Image
          src={unit.images[0]}
          alt={`Unit ${unit.unitNumber}`}
          fill
          className="object-cover"
        />
      </div>
      <CardHeader className="pb-2">
        <div className="flex items-center justify-between">
          <h3 className="font-semibold">Unit {unit.unitNumber}</h3>
          <Badge variant={unit.available ? 'default' : 'secondary'}>
            {availabilityLabel}
          </Badge>
        </div>
        <p className="text-lg font-semibold">${unit.rentPrice.toLocaleString()}/mo</p>
      </CardHeader>
      <CardContent>
        <div className="mb-3 flex flex-wrap gap-2">
          <Badge variant="outline">{unit.bedrooms} bed</Badge>
          <Badge variant="outline">{unit.bathrooms} bath</Badge>
          <Badge variant="outline">{unit.sqft.toLocaleString()} sqft</Badge>
        </div>
        <div className="flex flex-wrap gap-1">
          {unit.amenities.map((amenity) => (
            <Badge key={amenity} variant="secondary">
              {amenity}
            </Badge>
          ))}
        </div>
        <p className="mt-3 text-sm text-muted-foreground">{unit.description}</p>
      </CardContent>
    </Card>
  )
}
```

- [ ] **Step 2: Create `app/properties/[id]/page.tsx`**

```tsx
import { notFound } from 'next/navigation'
import Image from 'next/image'
import { Badge } from '@/components/ui/badge'
import { UnitCard } from '@/components/properties/UnitCard'
import { properties } from '@/data/properties'
import { units } from '@/data/units'

interface PropertyPageProps {
  params: Promise<{ id: string }>
}

export default async function PropertyPage({ params }: PropertyPageProps) {
  const { id } = await params
  const property = properties.find((p) => p.id === id)

  if (!property) notFound()

  const propertyUnits = units.filter((u) => u.propertyId === property.id)

  return (
    <div className="container mx-auto px-4 py-16">
      <div className="relative mb-8 h-72 w-full overflow-hidden rounded-xl">
        <Image
          src={property.images[0]}
          alt={property.name}
          fill
          className="object-cover"
        />
      </div>

      <h1 className="mb-1 text-3xl font-semibold">{property.name}</h1>
      <p className="mb-4 text-muted-foreground">
        {property.address}, {property.city}
        {property.yearBuilt && ` · Built ${property.yearBuilt}`}
      </p>

      <div className="mb-6 flex flex-wrap gap-2">
        {property.amenities.map((amenity) => (
          <Badge key={amenity} variant="secondary">
            {amenity}
          </Badge>
        ))}
      </div>

      <p className="mb-12 max-w-2xl text-muted-foreground">{property.description}</p>

      <h2 className="mb-6 text-2xl font-semibold">Units</h2>
      {propertyUnits.length === 0 ? (
        <p className="text-muted-foreground">No units listed for this property yet.</p>
      ) : (
        <div className="grid gap-6 sm:grid-cols-2 lg:grid-cols-3">
          {propertyUnits.map((unit) => (
            <UnitCard key={unit.id} unit={unit} />
          ))}
        </div>
      )}
    </div>
  )
}

export function generateStaticParams() {
  return properties.map((p) => ({ id: p.id }))
}
```

- [ ] **Step 3: Verify TypeScript compiles cleanly**

```bash
npx tsc --noEmit
```

Expected: No errors.

- [ ] **Step 4: Commit**

```bash
git add components/properties/UnitCard.tsx app/properties/
git commit -m "feat: add UnitCard component and property detail page"
```

---

## Task 7: TeamMemberCard and Team Page

**Files:**
- Create: `components/team/TeamMemberCard.tsx`
- Create: `app/team/page.tsx`

- [ ] **Step 1: Create `components/team/TeamMemberCard.tsx`**

```tsx
import Image from 'next/image'
import { Card, CardContent } from '@/components/ui/card'
import type { TeamMember } from '@/types'

interface TeamMemberCardProps {
  member: TeamMember
}

export function TeamMemberCard({ member }: TeamMemberCardProps) {
  return (
    <Card className="overflow-hidden">
      <div className="relative h-64 w-full">
        <Image
          src={member.photo}
          alt={member.name}
          fill
          className="object-cover object-top"
        />
      </div>
      <CardContent className="pt-5">
        <h3 className="text-lg font-semibold">{member.name}</h3>
        <p className="mb-3 text-sm text-muted-foreground">{member.title}</p>
        <p className="text-sm leading-relaxed">{member.bio}</p>
      </CardContent>
    </Card>
  )
}
```

- [ ] **Step 2: Create `app/team/page.tsx`**

```tsx
import { TeamMemberCard } from '@/components/team/TeamMemberCard'
import { team } from '@/data/team'

export default function TeamPage() {
  return (
    <div className="container mx-auto px-4 py-16">
      <h1 className="mb-2 text-3xl font-semibold">Our Team</h1>
      <p className="mb-10 text-muted-foreground">The partners behind MOVE Properties.</p>
      <div className="grid gap-8 sm:grid-cols-2 lg:grid-cols-3">
        {team.map((member) => (
          <TeamMemberCard key={member.name} member={member} />
        ))}
      </div>
    </div>
  )
}
```

- [ ] **Step 3: Verify TypeScript compiles cleanly**

```bash
npx tsc --noEmit
```

Expected: No errors.

- [ ] **Step 4: Commit**

```bash
git add components/team/ app/team/
git commit -m "feat: add TeamMemberCard component and team page"
```

---

## Task 8: Tenant Tools Page

**Files:**
- Create: `components/tenants/RentPaymentSection.tsx`
- Create: `components/tenants/MaintenanceRequestForm.tsx`
- Create: `app/tenants/page.tsx`

- [ ] **Step 1: Create `components/tenants/RentPaymentSection.tsx`**

Replace `'#'` with the actual payment URL (Venmo, Zelle, PayPal, etc.) when ready.

```tsx
import { Card, CardContent, CardDescription, CardHeader, CardTitle } from '@/components/ui/card'
import { Button } from '@/components/ui/button'

const PAYMENT_URL = '#'

export function RentPaymentSection() {
  return (
    <Card>
      <CardHeader>
        <CardTitle>Pay Rent</CardTitle>
        <CardDescription>
          Click below to pay your rent through our payment portal.
        </CardDescription>
      </CardHeader>
      <CardContent>
        <Button asChild size="lg">
          <a href={PAYMENT_URL} target="_blank" rel="noopener noreferrer">
            Pay Rent
          </a>
        </Button>
      </CardContent>
    </Card>
  )
}
```

- [ ] **Step 2: Create `components/tenants/MaintenanceRequestForm.tsx`**

```tsx
'use client'

import { useState } from 'react'
import { toast } from 'sonner'
import { Card, CardContent, CardDescription, CardHeader, CardTitle } from '@/components/ui/card'
import { Button } from '@/components/ui/button'
import { Input } from '@/components/ui/input'
import { Label } from '@/components/ui/label'
import { Textarea } from '@/components/ui/textarea'
import {
  Select,
  SelectContent,
  SelectItem,
  SelectTrigger,
  SelectValue,
} from '@/components/ui/select'

const ISSUE_TYPES = ['Plumbing', 'Electrical', 'HVAC', 'Appliance', 'Other'] as const

export function MaintenanceRequestForm() {
  const [name, setName] = useState('')
  const [unitNumber, setUnitNumber] = useState('')
  const [issueType, setIssueType] = useState('')
  const [description, setDescription] = useState('')

  function handleSubmit(e: React.FormEvent) {
    e.preventDefault()
    if (!issueType) {
      toast.error('Please select an issue type.')
      return
    }
    toast.success("Request submitted! We'll be in touch within 24 hours.")
    setName('')
    setUnitNumber('')
    setIssueType('')
    setDescription('')
  }

  return (
    <Card>
      <CardHeader>
        <CardTitle>Submit a Maintenance Request</CardTitle>
        <CardDescription>
          Describe the issue and we&apos;ll follow up within 24 hours.
        </CardDescription>
      </CardHeader>
      <CardContent>
        <form onSubmit={handleSubmit} className="space-y-4">
          <div className="grid gap-4 sm:grid-cols-2">
            <div className="space-y-2">
              <Label htmlFor="name">Full Name</Label>
              <Input
                id="name"
                value={name}
                onChange={(e) => setName(e.target.value)}
                required
              />
            </div>
            <div className="space-y-2">
              <Label htmlFor="unit">Unit Number</Label>
              <Input
                id="unit"
                value={unitNumber}
                onChange={(e) => setUnitNumber(e.target.value)}
                placeholder="e.g. 2B"
                required
              />
            </div>
          </div>
          <div className="space-y-2">
            <Label htmlFor="issue">Issue Type</Label>
            <Select value={issueType} onValueChange={setIssueType}>
              <SelectTrigger id="issue">
                <SelectValue placeholder="Select an issue type" />
              </SelectTrigger>
              <SelectContent>
                {ISSUE_TYPES.map((type) => (
                  <SelectItem key={type} value={type}>
                    {type}
                  </SelectItem>
                ))}
              </SelectContent>
            </Select>
          </div>
          <div className="space-y-2">
            <Label htmlFor="description">Description</Label>
            <Textarea
              id="description"
              value={description}
              onChange={(e) => setDescription(e.target.value)}
              placeholder="Describe the issue in detail..."
              rows={4}
              required
            />
          </div>
          <Button type="submit" className="w-full">
            Submit Request
          </Button>
        </form>
      </CardContent>
    </Card>
  )
}
```

- [ ] **Step 3: Create `app/tenants/page.tsx`**

```tsx
import { RentPaymentSection } from '@/components/tenants/RentPaymentSection'
import { MaintenanceRequestForm } from '@/components/tenants/MaintenanceRequestForm'

export default function TenantsPage() {
  return (
    <div className="container mx-auto max-w-2xl px-4 py-16">
      <h1 className="mb-2 text-3xl font-semibold">Tenant Tools</h1>
      <p className="mb-10 text-muted-foreground">
        Resources for current MOVE Properties tenants.
      </p>
      <div className="space-y-8">
        <RentPaymentSection />
        <MaintenanceRequestForm />
      </div>
    </div>
  )
}
```

- [ ] **Step 4: Verify TypeScript compiles cleanly**

```bash
npx tsc --noEmit
```

Expected: No errors.

- [ ] **Step 5: Commit**

```bash
git add components/tenants/ app/tenants/
git commit -m "feat: add Tenant Tools page with rent payment section and maintenance form"
```

---

## Task 9: Final Build Verification and Deploy

**Files:**
- Modify: `README.md`

- [ ] **Step 1: Run full production build**

```bash
npx next build
```

Expected: Build completes successfully. Output shows routes for `/`, `/properties`, `/properties/[id]`, `/team`, `/tenants`. No TypeScript or ESLint errors.

If the build fails with TypeScript errors, fix them before proceeding.

- [ ] **Step 2: Verify the site works locally**

```bash
npx next dev
```

Open `http://localhost:3000` and manually verify:
- Home page loads with hero, property cards, and about section
- Clicking a property card navigates to the detail page with units
- `/properties` shows the full grid
- `/team` shows three team member cards
- `/tenants` shows the Pay Rent card and maintenance form
- Submitting the maintenance form shows a success toast
- Navbar links work on desktop and mobile (resize browser to test hamburger menu)
- Footer is visible on all pages

Stop the dev server with `Ctrl+C` when done.

- [ ] **Step 3: Create README.md**

```markdown
# MOVE Properties Website

Marketing and tenant-tools website for MOVE Properties, a residential real estate holding company.

## Tech Stack

- [Next.js 15](https://nextjs.org/) (App Router)
- [TypeScript](https://www.typescriptlang.org/)
- [Tailwind CSS](https://tailwindcss.com/)
- [shadcn/ui](https://ui.shadcn.com/)

## Getting Started

Install dependencies:

\`\`\`bash
npm install
\`\`\`

Run the development server:

\`\`\`bash
npm run dev
\`\`\`

Open [http://localhost:3000](http://localhost:3000).

## Updating Content

All site content lives in the `/data` directory:

| File | Contents |
|---|---|
| `data/properties.ts` | Buildings — name, address, amenities, photos |
| `data/units.ts` | Rentable units — bedrooms, price, availability |
| `data/team.ts` | Partner names, titles, bios, and photos |

Replace placeholder images by adding photo files to `public/images/` and updating the `images` or `photo` fields in the data files.

Update the `PAYMENT_URL` constant in `components/tenants/RentPaymentSection.tsx` with your actual payment link.

## Deployment

This site deploys automatically to Vercel on every push to `main`.

1. Push to GitHub
2. Connect the repo at [vercel.com](https://vercel.com) — no configuration needed
3. Every `git push origin main` triggers a new deploy
```

- [ ] **Step 4: Commit and push**

```bash
git add -A
git commit -m "feat: complete MOVE Properties website — all pages, components, and readme"
git push origin main
```

Expected: Push succeeds. Vercel auto-deploys the site (once the repo is connected at vercel.com).

---

## Post-Implementation Checklist

- [ ] Replace placeholder team names in `data/team.ts` with real partner names and bios
- [ ] Add real property photos to `public/images/` and update image paths in data files
- [ ] Add real headshots to `public/images/team/` and update photo paths in `data/team.ts`
- [ ] Update `PAYMENT_URL` in `components/tenants/RentPaymentSection.tsx` with actual payment link
- [ ] Update `contact@moveproperties.com` in `components/layout/Footer.tsx` with real email
- [ ] Connect GitHub repo to Vercel for automatic deploys
