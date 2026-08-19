# Avenza Bookings - Development Progress Track

**Overall Status**: 🟡 Planning & Specs Completed | Ready for Phase 1 Implementation

---

## 📊 Phase Progress Summary

| Phase | Milestone | Status | Target Deliverables |
|-------|-----------|--------|---------------------|
| **Phase 0** | Architecture & Specifications | ✅ Complete | Complete 5 phase spec docs in `specs/` |
| **Phase 1** | Backend Data Modeling & DocTypes | ⏳ Pending | Scaffold `Venue`, `Pricing Rule`, `Blackout Date`, `Slot Lock`, `Booking`, `RSVP` |
| **Phase 2** | Core Business Logic & APIs | ⏳ Pending | Implement slot calculation, pricing engine, lock, & mock checkout APIs |
| **Phase 3** | Frontend Setup & Frappe UI SPA | ⏳ Pending | Scaffold Vite + Vue 3 + Frappe UI inside `avenza_bookings/frontend` |
| **Phase 4** | Host Studio | ⏳ Pending | Build Host Dashboard, Venue Editor, Schedule & Pricing Rule Builder |
| **Phase 5** | Guest Portal & Invite Pages | ⏳ Pending | Build Marketplace, Slot Selector, Checkout Modal, & Public `/b/:token` Invite Page |
| **Phase 6** | E2E Testing & Polishing | ⏳ Pending | Unit tests, concurrency lock tests, UI polish |

---

## 📝 Detailed Task Checklist

### Phase 0: System Architecture & Specifications
- [x] Conduct product interview and resolve core architectural choices (`/grill-me`).
- [x] Create `specs/00_overview_and_architecture.md`.
- [x] Create `specs/01_backend_doctypes_and_schema.md`.
- [x] Create `specs/02_api_and_business_logic.md`.
- [x] Create `specs/03_frontend_frappe_ui_design.md`.
- [x] Create `specs/04_phase_wise_implementation_plan.md`.
- [x] Create project implementation plan and handover artifact.

### Phase 1: Backend Data Model & Schemas
- [ ] Create `Venue` DocType with fields, JSON definition, and python controller.
- [ ] Create `Venue Pricing Rule` child DocType.
- [ ] Create `Venue Blackout Date` child DocType.
- [ ] Create `Venue Slot Lock` DocType.
- [ ] Create `Venue Booking` DocType.
- [ ] Create `Booking Guest RSVP` child DocType.
- [ ] Register lock cleanup scheduled event in `hooks.py`.
- [ ] Run `bench migrate`.

### Phase 2: Core APIs & Business Logic
- [ ] `avenza_bookings.api.venue.get_venues` & `get_venue_details`.
- [ ] Dynamic slot generator (`get_available_slots`) considering operating hours, duration, blackout dates, and active locks.
- [ ] Dynamic pricing engine (`calculate_slot_price`) evaluating day-of-week, peak hours, and custom date range rules.
- [ ] Concurrency control API (`lock_slot`) with 15-minute expiration timer and database lock.
- [ ] Mock payment integration (`process_mock_payment`) creating confirmed bookings and generating share tokens.
- [ ] Share link APIs (`get_booking_invite` & `submit_guest_rsvp`).
- [ ] Write Python unit tests for pricing, slot availability, and lock expiry.

### Phase 3: Frontend Scaffolding (Frappe UI Vue 3 SPA)
- [ ] Initialize Vue 3 Vite application in `avenza_bookings/frontend`.
- [ ] Install and configure `frappe-ui` and Tailwind CSS design tokens.
- [ ] Set up `vue-router` for client routes (`/`, `/v/:slug`, `/checkout`, `/b/:token`, `/host/*`).
- [ ] Configure Vite proxy for backend communication with `avenza.localhost`.

### Phase 4: Host Studio Development
- [ ] Build Host Dashboard layout and navigation.
- [ ] Build Venue Creation and Edit form with cover image upload.
- [ ] Build Dynamic Pricing Rule builder interface.
- [ ] Build Blackout Date scheduler interface.

### Phase 5: Guest Booking Flow & Shareable Invites
- [ ] Build Venue Discovery Marketplace page (`/`).
- [ ] Build Venue Detail page (`/v/:slug`) with date picker and dynamic slot matrix.
- [ ] Build Checkout modal with 15-minute countdown timer and mock payment gateway.
- [ ] Build Public Guest Invite page (`/b/:token`) with address, rules, `.ics` export, and guest RSVP form.

### Phase 6: Verification & Final Polish
- [ ] End-to-end multi-user concurrency testing.
- [ ] Visual polish using Frappe UI design tokens (`surface-*`, `ink-*`, `outline-*`).
- [ ] Verify cleanDesk integration and background worker execution.

---

## 🎯 Current Immediate Next Step
- Begin **Phase 1: Backend Data Modeling & DocTypes** by creating the Frappe DocTypes.
