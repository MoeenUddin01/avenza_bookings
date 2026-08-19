# Authentication & Role Permissions Specification

## 1. Overview
This specification details authentication flows, user roles, and security policies for **Avenza Bookings**.

---

## 2. User Roles & Access Matrix

| Role | Target Users | Permissions & Access Scope |
|------|--------------|----------------------------|
| **System Manager** | System Administrator | Full CRUD access to Frappe Desk and all DocTypes. |
| **Venue Host** | Publisher / Turf Owner | Access to Host Studio (`/host/*`). Can create, edit, and manage only `Venue`, `Venue Pricing Rule`, and `Venue Blackout Date` records where `owner_user == session.user`. Can view bookings for owned venues. |
| **Customer / Guest** | Booker / Invite Recipient | Public access (`allow_guest=True`) to discover published venues, check slot availability, lock slots, complete mock checkout, view shareable invites (`/b/:token`), and submit RSVPs. |

---

## 3. Whitelisted API Security Policies
- Public APIs (`get_venues`, `get_venue_details`, `get_available_slots`, `lock_slot`, `process_mock_payment`, `get_booking_invite`, `submit_guest_rsvp`) are decorated with `@frappe.whitelist(allow_guest=True)`.
- Host Studio management APIs require session authentication and verify `venue.owner_user == frappe.session.user`.
