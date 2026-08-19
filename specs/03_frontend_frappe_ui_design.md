# 03. Frontend Frappe UI Design Specification

## Overview
This specification documents the single-page Vue 3 application built inside `avenza_bookings/frontend` utilizing `frappe-ui` components and Tailwind CSS.

---

## 1. App Shell Architecture

```mermaid
graph TD
    Shell["🏠 App Shell — NavBar: Logo | Marketplace | My Bookings | Host Studio"]

    Shell --> R1["/ — Marketplace\nVenue Grid + Hero Search + Filter Drawer"]
    Shell --> R2["/v/:slug — Venue Detail\nDate Picker + Dynamic Slot Matrix + Checkout Bar"]
    Shell --> R3["/checkout — Mock Payment Modal\nCustomer Form + 15-min Countdown + Pay Now"]
    Shell --> R4["/b/:token — Public Guest Invite\nEvent Info + Map + .ics Export + RSVP Form"]
    Shell --> R5["/host/* — Host Studio\nVenue Editor + Pricing Rules + Blackout Scheduler"]

    R2 -->|"Select Slot → Lock"| R3
    R3 -->|"Payment Success"| R4
```

---

## 2. Page & Component Breakdown

### A. Marketplace / Venue Discovery (`/`)

```mermaid
graph TD
    MA["📍 / — Marketplace Page"]
    MA --> MA1["Hero Section\nSearch: City + Date + Slot Duration"]
    MA --> MA2["Venue Cards Grid\ncover_image · title · city · base_price · duration"]
    MA --> MA3["Filter Drawer\nFilter by: City · Price Range · Amenities"]
```

### B. Venue Detail & Slot Booking (`/v/:slug`)

```mermaid
graph TD
    VD["/v/:slug — Venue Detail Page"]
    VD --> VD1["Venue Overview\nTitle · Address · Cover Gallery · Rules & Amenities"]
    VD --> VD2["Interactive Date Picker\nfrappe-ui DatePicker / Custom Calendar Bar"]
    VD --> VD3["Real-time Slot Matrix"]
    VD3 --> VD3A["🟢 Available — Dynamic price shown"]
    VD3 --> VD3B["🟡 Locked — Disabled, another session active"]
    VD3 --> VD3C["🔴 Booked — Disabled, confirmed booking"]
    VD --> VD4["Checkout Bar / Drawer\nSelected Slot · Price Breakdown · 15-min Timer · Book Now"]
```

### C. Checkout & Mock Payment Modal

```mermaid
graph TD
    CO["💳 /checkout — Checkout Modal\nfrappe-ui Dialog overlay"]
    CO --> CO1["Customer Details Form\nTextInput: Name · Email · Phone · Notes"]
    CO --> CO2["Lock Expiration Bar\nLive countdown: 14:59 → 00:00\nAuto-closes modal on expiry with toast warning"]
    CO --> CO3["Payment Simulator"]
    CO3 --> CO3A["✅ Pay Now — Triggers mock success"]
    CO3 --> CO3B["❌ Simulate Failure — Triggers mock error"]
```

### D. Public Shareable Guest Invite Page (`/b/:token`)

```mermaid
graph TD
    GI["/b/:token — Public Guest Invite Page"]
    GI --> GI1["Event Header\nVenue Name · Date · Time Slot · Host Contact"]
    GI --> GI2["Location & Rules Section\nPhysical Address · Venue Rules"]
    GI --> GI3["Calendar Actions\n📅 Add to Google Calendar\n⬇ Download .ics File"]
    GI --> GI4["Guest RSVP Form\nName · Email\nResponse: Going / Maybe / Declined"]
    GI --> GI5["Attendance Counter & Guest List"]
```

### E. Host Studio (`/host/venues`)

```mermaid
graph TD
    HS["/host/* — Host Studio"]
    HS --> HS1["Host Dashboard\nTabs: Venues · Bookings Calendar · Pricing Rules"]
    HS --> HS2["Venue Form\nFormControls: TextInput · TextEditor · Select · Image Upload"]
    HS --> HS3["Dynamic Pricing Matrix Builder\nAdd/Edit: Weekend Rates · Peak Hour Surcharges"]
    HS --> HS4["Blackout Date Manager\nMark Maintenance Days / Full-day Closures"]
```

---

## 3. Design System & Frappe UI Guidelines
- **Color Tokens**: `bg-surface-base`, `bg-surface-gray-1`, `text-ink-gray-9`, `text-ink-gray-5`, `border-outline-gray-2`.
- **Button Styling**: Use `<Button variant="solid" theme="blue">` or `variant="outline"`. Never hand-roll `<button class="bg-blue-500">`.
- **Overlay & Alerts**: `frappe-ui` `<Dialog>` with `v-model:open`, imperative `toast.success()` / `toast.error()`.
- **Icon Set**: CSS classes e.g. `<span class="lucide-calendar size-4" />`.
