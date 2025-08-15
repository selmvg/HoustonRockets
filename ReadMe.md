Here is the FRD

1) MVP Scope 

Players & Jerseys: track counts by Player → Variant (Home/Away/City) → Size.

States: Available, Laundry, GivenAway, OnOrder (display only), Damaged.

Core Flows (1–3 taps): Issue (decrement Available), Send to Laundry, Return from Laundry, Mark Giveaway, Receive shipment.

Reorder Alerts: simple threshold per Player/Variant/Size (no complex forecast), surfaces on Dashboard + optional Email.

Audit Trail: event log for every adjustment.

Mobile-first UI: PWA shell (installable), large touch targets.

Nice-to-have if time allows: SMS alerts, barcode scan, locker minimums for upcoming game.

Out of scope for 24h: vendor API/EDI, finance/AP, multi‑warehouse, advanced safety stock math.

2) Roles

Inventory Manager (Primary): perform all operations; receives alerts (manage thresholds, players, variants, users; can correct counts).

3) Jersey Lifecycle (Simplified)

Available → (Issue) → In Use → Laundry → (Return) → Available

Terminal/alt events: GivenAway, Damaged. Procurement: OnOrder → Receive → Available.

Events & required fields

Issue: player, variant, size, qty, note(optional)

Laundry: Send: player (optional for bulk), variant/size, qty, batch(optional), ETA(optional)

Laundry: Return: variant/size, qty, damaged_qty(optional)

Giveaway: player, variant/size, qty, note(optional)

Receive: variant/size, qty, PO/ref(optional)

Adjust (Admin): variant/size, qty delta, reason

4) Reorder Rule (Simple)

Per SKU threshold: When Available ≤ MinThreshold, flag as Reorder.

Configurable: MinThreshold, LeadTimeDays (for display only on PO card).

Alert Triggers

On create/update of any event affecting Available.

Daily sweep job (optional) to re-check.

5) Dashboard (Landing)

Player cards: photo, name/number; chips per variant (Home/Away/City) → size rows: Avail | Laundry | OnOrder.

Reorder panel: sortable list of items at/below threshold with suggested order qty = MinThreshold × 2 − Available (capped ≥ 1).

Quick actions: Issue, Laundry, Giveaway, Receive.

Search/Filter: player, variant, size; sticky search on mobile.

Acceptance

Loads ≤ 2s on mid‑range phone over 4G.

Any item at/below threshold shows red badge and appears in Reorder panel.

6) Notifications (MVP)

Channels: In‑app banners/toasts. Email via SendGrid (if key available). SMS (Twilio) is optional stub.

Templates

Subject: Reorder: {player} {variant} {size}

Body: Available {x} ≤ Min {m}. Suggested order {q}. Lead time ~{L} days.

7) Data Model (Minimal)

Player(id, name, number, status, photo_url)

JerseyVariant(id, season, type{Home|Away|City}, colorway)

JerseySKU(id, variant_id, size, nike_sku)

InventoryBalance(id, sku_id, player_id, available, laundry, on_order, given_away, damaged, updated_at)

ReorderSetting(id, sku_id, player_id, min_threshold, lead_time_days)

Event(id, type, player_id?, sku_id, qty, meta_json, created_by, created_at)

User(id, name, role{ADMIN|MANAGER}, email, phone, status)

Notes

Team‑level (unassigned) stock: player_id = NULL in InventoryBalance.

Keep counts denormalized in InventoryBalance; recalc via transactional updates on event write.

8) API (MVP)

Auth: Basic login (JWT).Dashboard: GET /dashboard/summary (rollups + reorder list).Players: GET /players, GET /players/{id}.Events: POST /events/issue | laundry/send | laundry/return | giveaway | receive | adjust.Settings: GET/PUT /reorder/{player_id}/{sku_id}.

9) UI/UX (Mobile PWA)

Sticky bottom bar with the 4 quick actions.

Big tap targets (≥44px), chip counters, skeleton loaders.

PWA install, offline queue for event posts (sync on reconnect) if time permits.

Accessibility: WCAG AA contrast; labels for screen readers.

10) Non‑Functional

Perf: API p95 < 500ms; dashboard TTI ≤ 2s.

Security: JWT, role checks at endpoints; TLS via host.

Reliability: Daily backup (if Postgres) or file‑based (if H2). Simple server logs.

11) Demo Script (what to show)**

Open Dashboard → see at least one Reorder badge.

Hit Issue on a player/size → Available decrements, audit log updated.

Send to Laundry, then Return part of it.

Mark Giveaway → shows in player detail & report.

Receive shipment → badge clears.

(If enabled) Receive email alert on threshold breach.



