

# Rockets Jersey Inventory — Fullstack App (Supabase + Next.js)

## 0. Overview

A mobile-first PWA for managing Houston Rockets jersey inventory.  
Tracks counts by **Player → Variant (Home/Away/City) → Size** and supports quick operations like issuing, laundry management, giveaways, and receiving shipments.

### Tech Stack
- **Backend**: Supabase (Postgres + Auth + Edge Functions)
- **Frontend**: Next.js 14 (App Router) + Tailwind CSS + next-pwa
- **Optional Email**: SendGrid (for reorder alerts)
- **Deployment**: Vercel

---

## 1. MVP Scope

- **Players & Jerseys**: Counts by Player → Variant → Size
- **States**: Available, Laundry, GivenAway, OnOrder (display only), Damaged
- **Core Flows (1–3 taps)**:
  - Issue (decrement Available)
  - Send to Laundry
  - Return from Laundry
  - Mark Giveaway
  - Receive shipment
- **Reorder Alerts**: Simple threshold per Player/Variant/Size
- **Audit Trail**: Event log for every adjustment
- **Mobile-first UI**: PWA shell, large touch targets

Nice-to-have:
- SMS alerts (Twilio stub)
- Barcode scan
- Locker minimums

---

## 2. Roles

**Inventory Manager (Primary)**  
- Perform all operations  
- Manage thresholds, players, variants, users  
- Correct counts  

---

## 3. Jersey Lifecycle (Simplified)

Available → Issue → In Use → Laundry → Return → Available  
Terminal: GivenAway, Damaged  
Procurement: OnOrder → Receive → Available

---

## 4. Reorder Rule

- Per SKU threshold: Available ≤ MinThreshold → flag as Reorder
- Configurable: MinThreshold, LeadTimeDays (display only)
- Trigger:
  - On create/update of any event affecting Available
  - Optional daily sweep job

---

## 5. Dashboard (Landing Page)

- Player cards: photo, name/number; chips per variant → size rows (Avail | Laundry | OnOrder)
- Reorder panel: sortable list of low-stock items
- Quick actions: Issue, Laundry, Giveaway, Receive
- Search/Filter: player, variant, size

Acceptance Criteria:
- Loads ≤ 2s on mid-range phone over 4G
- Low-stock items show red badge and appear in Reorder panel

---

## 6. Notifications (MVP)

- **Channels**: In-app banners/toasts; optional email via SendGrid
- **Template**:


