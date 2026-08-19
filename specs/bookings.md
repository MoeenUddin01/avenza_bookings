# Bookings & Guest Invite Specification

## 1. Overview
This specification details `Venue Booking`, `Booking Guest RSVP`, and the public shareable invite link page (`/b/:token`).

---

## 2. DocType Schemas

### A. `Venue Booking` (Main DocType)
* **Autoname**: `format:VB-{YYYY}-{#####}`

| Fieldname | Fieldtype | Details | Description |
|-----------|-----------|---------|-------------|
| `venue` | Link | `Venue` | Target venue |
| `customer_name` | Data | Mandatory | Booker full name |
| `customer_email` | Data | Mandatory | Booker email |
| `customer_phone` | Data | Mandatory | Booker phone |
| `booking_date` | Date | Mandatory | Booking date |
| `start_time / end_time` | Time | Mandatory | Slot window |
| `base_amount` | Currency | - | Base price |
| `pricing_adjustments` | Currency | - | Surcharges |
| `total_amount` | Currency | Mandatory | Final amount |
| `payment_status` | Select | `UNPAID\nPAID\nREFUNDED` | Payment status |
| `payment_transaction_id` | Data | - | Mock gateway ref |
| `invite_token` | Data | Unique | Secret slug for shareable link |
| `notes` | Text | - | Customer notes |
| `rsvps` | Table | `Booking Guest RSVP` | Guest responses |

### B. `Booking Guest RSVP` (Child DocType)
* **Subtable**: `istable: 1`

| Fieldname | Fieldtype | Details | Description |
|-----------|-----------|---------|-------------|
| `guest_name` | Data | Mandatory | Guest name |
| `guest_email` | Data | Optional | Guest email |
| `status` | Select | `GOING\nMAYBE\nDECLINED` | Attendance |
| `submitted_at` | Datetime | Mandatory | RSVP timestamp |

---

## 3. Public Shareable Guest Invite Link (`/b/:token`)

- **URL Pattern**: `/b/:token` (e.g. `/b/x892jkls92`)
- **API**: `avenza_bookings.api.booking.get_booking_invite(invite_token)`
- **Features**:
  - Displays Venue title, cover image, address, rules, date, and time.
  - Interactive RSVP submission form (`submit_guest_rsvp`).
  - Google Calendar integration & `.ics` file download.
  - Attendance counter (Going, Maybe, Declined) and public guest list.
