# Users & Host Profile Specification

## 1. Overview
This specification covers user profiles, host management, and customer contact data handling.

---

## 2. User Types & Entities

### A. Venue Host Profile
- Extends standard Frappe `User` with role `Venue Host`.
- Owns `Venue` properties linked via `Venue.owner_user`.
- Manages operating schedules, blackout dates, and dynamic pricing rules in Host Studio.

### B. Booker / Customer
- Customer contact info is captured directly during slot checkout:
  - `customer_name` (Data, Mandatory)
  - `customer_email` (Data, Mandatory)
  - `customer_phone` (Data, Mandatory)
- Associated with `Venue Booking` records.

---

## 3. Data Protection & Privacy
- Public invite links (`/b/:token`) display event details, date, time slot, host contact, and guest RSVP list.
- Customer phone numbers are masked on public invite pages.
