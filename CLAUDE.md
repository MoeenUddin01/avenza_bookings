# Avenza Bookings

Multi-tenant, slot-based venue booking platform on Frappe Framework with a Frappe UI (Vue 3) SPA frontend.

## Bench & Site

- Bench: `/home/moeen/frappe/frappe-bench`
- App: `/home/moeen/frappe/frappe-bench/apps/avenza_bookings`
- Site: `avenza.localhost`
- Installed apps: `frappe`, `erpnext`, `payments`, `avenza_bookings`

## Commands

```bash
# Always from bench root:
cd /home/moeen/frappe/frappe-bench

bench --site avenza.localhost migrate        # run after DocType changes
bench --site avenza.localhost console        # interactive Python shell
bench --site avenza.localhost run-tests --app avenza_bookings  # run tests
bench start                                 # start dev server (redis, web, worker)
```

Never `cd` into the app directory to run bench commands — bench expects to run from `/home/moeen/frappe/frappe-bench`.

## Project Structure

```
avenza_bookings/                    # repo root
├── avenza_bookings/                # outer Python package
│   ├── avenza_bookings/            # inner module (DocTypes live here)
│   │   └── doctype/               # one subdirectory per DocType
│   ├── hooks.py                    # app hooks, scheduler events
│   ├── modules.txt                 # registered Frappe modules
│   └── patches.txt                 # data migration patches
├── specs/                          # SDD feature specifications
│   ├── authentication.md           # Roles, permissions, API security
│   ├── users.md                    # Host profiles, customer contact data
│   ├── spaces.md                   # Venue, Pricing Rule, Blackout Date schemas
│   ├── availability.md             # Dynamic slots, pricing engine, 15m locks
│   ├── bookings.md                 # Booking schema, guest RSVP, shareable link
│   ├── payments.md                 # Mock payment gateway, checkout timer
│   └── 04_phase_wise_implementation_plan.md # Master phase-wise roadmap
├── PROGRESS.md                     # delivery milestone tracker
└── plan.html                       # visual architecture canvas
```

## DocTypes (Phase 1 — planned)

| DocType | Kind | Purpose |
|---------|------|---------|
| Venue | Main | Property listing with operating hours, base price, slug |
| Venue Pricing Rule | Child of Venue | Day-of-week / peak-hour / date-range pricing overrides |
| Venue Blackout Date | Child of Venue | Non-availability windows |
| Venue Slot Lock | Main | 15-minute temporary hold during checkout (concurrency control) |
| Venue Booking | Main | Confirmed reservation with payment status and invite token |
| Booking Guest RSVP | Child of Venue Booking | Guest attendance response for shareable invite links |

## Key Architectural Decisions

1. **Dynamic slot generation** — slots are computed on-the-fly from opening/closing times and slot duration; no pre-populated slot table.
2. **15-minute slot locks** — `Venue Slot Lock` with `expires_at` prevents double-booking during checkout. Background scheduler clears expired locks every 5 minutes.
3. **Rule-based pricing** — `Venue Pricing Rule` child table with `Percentage Increase`, `Fixed Amount Add`, and `Override Flat Price` adjustment types.
4. **Shareable invite links** — each `Venue Booking` gets a unique `invite_token` powering the public `/b/:token` guest RSVP page.

## Coding Conventions

- Python: tabs for indentation, 110-char line limit, Ruff linter (see `pyproject.toml`)
- DocType creation: use Frappe desk UI or `bench` scaffold commands — never create DocType directories manually with `mkdir`
- APIs: `@frappe.whitelist(allow_guest=True)` for public endpoints, placed in `avenza_bookings/avenza_bookings/api/`
- Frontend: Frappe UI components (`Button`, `Dialog`, `TextInput`, `Select`, `Badge`, `Toast`), Tailwind CSS semantic tokens (`bg-surface-*`, `text-ink-*`, `border-outline-*`), Lucide icons via CSS classes

## Current Status

Phase 0 (Specifications & Architecture) is complete. Phase 1 (Backend DocType scaffolding) is next. Track progress in `PROGRESS.md`.

---

# Spec-Driven Development Workflow

## Core Principle

This project uses Spec-Driven Development (SDD). Do NOT immediately start writing code when given a new feature request. The workflow is:

1. Understand the request.
2. Inspect the existing codebase and project architecture.
3. Inspect existing specifications in specs/.
4. Create or update the appropriate specification.
5. Break the feature into small, independently shippable phases ("tracer bullets").
6. Review the specification before implementation.
7. Implement one approved phase at a time.
8. Test and verify the implementation.
9. Reconcile the specification with the actual implementation.
10. Only then move to the next phase.

The specification is the source of truth for intended behavior.

---

# 1. Before Starting Any Feature

When a new feature or change is requested:

- Do NOT immediately modify code.
- First inspect the relevant parts of the repository.
- Read CLAUDE.md.
- Inspect relevant files under specs/.
- Inspect existing architecture, models, APIs, services, frontend components, and tests.
- Identify existing patterns that should be reused.
- Identify dependencies and possible conflicts.

Do not reinvent existing functionality without a reason.

---

# 2. Specifications

All feature specifications must live under:

    specs/

Use a clear and consistent naming convention. Example:

```text
specs/
├── authentication.md
├── users.md
├── spaces.md
├── availability.md
├── bookings.md
└── payments.md
```
