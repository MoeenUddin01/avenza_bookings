# 00. Overview & Architecture Specification

## 1. Product Vision
**Avenza Bookings** is a multi-tenant, slot-based venue booking platform. It enables venue publishers (e.g. owners of sports turfs, event halls, studio spaces, meeting rooms) to list properties, manage operating hours and blackout dates, configure dynamic pricing (weekend rates, peak hour rates), and accept online slot bookings.

Customers can discover venues, select time slots, view dynamic pricing breakdowns, lock slots temporarily, complete mock payment checkouts, and generate shareable guest invite pages with `.ics` calendar exports and RSVP features.

---

## 2. Tech Stack
- **Backend**: Frappe Framework (Python 3.10+, MariaDB/PostgreSQL, Redis)
  - DocTypes for Data Persistence
  - Whitelisted REST APIs (`@frappe.whitelist()`)
  - Frappe background workers & scheduled hooks (`hooks.py`)
- **Frontend**: Frappe UI + Vue 3 SPA (Single Page Application)
  - Components: `frappe-ui` library (`Button`, `Dialog`, `TextInput`, `Select`, `Badge`, `Toast`, etc.)
  - Router: `vue-router` for single-page client routing
  - Styling: Tailwind CSS using Frappe UI semantic design tokens (`surface-*`, `ink-*`, `outline-*`)
  - Icons: Lucide icons via CSS classes (`lucide-*`)

---

## 3. High-Level Architecture

```mermaid
graph TD
    Client[Frappe UI Vue 3 SPA] -->|REST / Frappe useCall| APIs[Frappe Whitelisted APIs]
    APIs -->|ORM / frappe.db| DocTypes[Frappe DocTypes / MariaDB]
    APIs -->|Locking & Cache| Redis[Redis Lock Store]
    
    subgraph Frontend Routes
        Discovery[/ Public Marketplace & Search]
        VenueDetail[/v/:slug - Slot Selector & Price Calc]
        Checkout[/checkout - Lock & Mock Gateway]
        Invite[/b/:token - Public Guest RSVP & Calendar]
        HostStudio[/host/* - Venue & Schedule Management]
    end
    
    subgraph Core DocTypes
        Venue[Venue Property]
        PricingRule[Venue Pricing Rule]
        BlackoutDate[Venue Blackout Date]
        SlotLock[Venue Slot Lock]
        Booking[Venue Booking]
        GuestRSVP[Booking Guest RSVP]
    end
```

---

## 4. User Roles & Security Model
1. **System Manager / Administrator**: Full access to Desk and all DocTypes.
2. **Venue Host**: Registered user with role `Venue Host`. Can create, modify, and manage only their owned `Venue` records and view associated bookings in the Host Studio.
3. **Customer / Guest**: Public unauthenticated user or authenticated customer. Can view published venues, query available slots, hold temporary slot locks, create bookings via payment, and access invite links.

---

## 5. Directory Structure
```
avenza_bookings/
├── avenza_bookings/
│   ├── avenza_bookings/
│   │   ├── doctype/
│   │   │   ├── venue/
│   │   │   ├── venue_pricing_rule/
│   │   │   ├── venue_blackout_date/
│   │   │   ├── venue_slot_lock/
│   │   │   ├── venue_booking/
│   │   │   └── booking_guest_rsvp/
│   │   └── api/
│   │       ├── venue.py
│   │       ├── booking.py
│   │       └── payment.py
│   ├── frontend/
│   │   ├── src/
│   │   │   ├── components/
│   │   │   ├── pages/
│   │   │   ├── router.js
│   │   │   └── main.js
│   │   ├── package.json
│   │   └── vite.config.js
│   └── hooks.py
└── specs/
    ├── 00_overview_and_architecture.md
    ├── 01_backend_doctypes_and_schema.md
    ├── 02_api_and_business_logic.md
    ├── 03_frontend_frappe_ui_design.md
    └── 04_phase_wise_implementation_plan.md
```
