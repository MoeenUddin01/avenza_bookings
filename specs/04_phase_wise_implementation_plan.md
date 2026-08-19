# 04. Phase-Wise Implementation Plan

## Overview
This document outlines the step-by-step development sequence for **Avenza Bookings**, ensuring incremental building and testable milestones.

---

## Phase 1: Backend Data Modeling & DocTypes
* **Goals**:
  1. Scaffold custom DocTypes: `Venue`, `Venue Pricing Rule`, `Venue Blackout Date`, `Venue Slot Lock`, `Venue Booking`, `Booking Guest RSVP`.
  2. Configure permissions & roles (`Venue Host`, `System Manager`, `Guest`).
  3. Register scheduler hook for auto-expiring slot locks in `hooks.py`.
* **Deliverables**: DocType JSON files committed, migration executed (`bench migrate`).

---

## Phase 2: Core Business Logic & Whitelisted APIs
* **Goals**:
  1. Implement `get_venue_details` and `get_venues` in `avenza_bookings/api/venue.py`.
  2. Implement dynamic slot generator and `calculate_slot_price` in `avenza_bookings/api/venue.py`.
  3. Implement `lock_slot`, `process_mock_payment`, `get_booking_invite`, and `submit_guest_rsvp` in `avenza_bookings/api/booking.py`.
* **Deliverables**: Comprehensive Python unit tests passing in `avenza_bookings/tests`.

---

## Phase 3: Frappe UI Vue 3 App Setup
* **Goals**:
  1. Bootstrap Vite + Vue 3 + Frappe UI SPA structure inside `avenza_bookings/frontend`.
  2. Configure `vite.config.js`, Tailwind CSS with `frappe-ui` tokens, and router setup.
  3. Add proxy rules for Frappe backend API communication.
* **Deliverables**: Frontend builds cleanly with `npm run build` and runs locally.

---

## Phase 4: Host Studio Development
* **Goals**:
  1. Build Host Dashboard for creating & managing venues.
  2. Build Operating Hours & Dynamic Pricing Rule editor.
  3. Build Blackout Date manager.
* **Deliverables**: Host can log in, add venue, set weekend surcharge, and set blackout dates.

---

## Phase 5: Guest Booking Flow & Shareable Invites
* **Goals**:
  1. Build Marketplace Discovery page (`/`).
  2. Build Venue Detail page (`/v/:slug`) with interactive date picker and real-time slot grid.
  3. Build 15-minute Slot Lock & Mock Checkout modal.
  4. Build Public Shareable Invite page (`/b/:token`) with Google Calendar / `.ics` download and guest RSVP.
* **Deliverables**: Complete customer flow working end-to-end.

---

## Phase 6: End-to-End Testing & Verification
* **Goals**:
  1. Verify concurrency lock behavior across multiple sessions.
  2. Verify dynamic pricing calculations for weekday vs weekend vs peak hours.
  3. Run automated tests and verify clean desk UI integration.
* **Deliverables**: Tested production-ready application.
