# 01. Backend DocTypes & Database Schema Specification

## Overview
This specification details the schema, fields, validation, and child tables for all Frappe DocTypes in **Avenza Bookings**.

---

## 1. `Venue` (Main DocType)
* **Module**: Avenza Bookings
* **Is Subtable**: No
* **Autoname**: `field:slug` or `format:VEN-{YYYY}-{#####}`

### Fields

| Fieldname | Fieldtype | Options / Details | Mandatory | Description |
|-----------|-----------|-------------------|-----------|-------------|
| `title` | Data | - | Yes | Name of the venue / turf / room |
| `slug` | Data | Unique, Name field | Yes | URL-friendly unique identifier (e.g., `green-field-turf`) |
| `owner_user` | Link | `User` | Yes | Host user who owns this venue |
| `is_published` | Check | Default: 1 | No | Visibility flag for public marketplace |
| `description` | Text Editor | - | No | Rich HTML overview of the venue |
| `address` | Small Text | - | Yes | Location / physical address |
| `city` | Data | - | Yes | City for search filtering |
| `rules_and_amenities` | Small Text | - | No | Usage policies (e.g. "No spiked shoes") |
| `slot_duration_mins` | Select | `30\n60\n90\n120` | Yes | Duration per booking slot (Default: `60`) |
| `opening_time` | Time | Default: `08:00:00` | Yes | Daily operating start time |
| `closing_time` | Time | Default: `22:00:00` | Yes | Daily operating end time |
| `base_price_per_slot` | Currency | Min: 0 | Yes | Base rate charged per slot |
| `currency` | Link | `Currency` | Yes | Default: `USD` or local currency |
| `cover_image` | Attach Image | - | No | Primary listing photo |
| `pricing_rules` | Table | `Venue Pricing Rule` | No | Child table for dynamic pricing |
| `blackout_dates` | Table | `Venue Blackout Date` | No | Child table for holiday / maintenance shutdowns |

---

## 2. `Venue Pricing Rule` (Child DocType)
* **Is Subtable**: Yes (`istable: 1`)

### Fields

| Fieldname | Fieldtype | Details | Description |
|-----------|-----------|---------|-------------|
| `rule_name` | Data | e.g. "Weekend Surge", "Night Lights" | Rule label |
| `apply_on` | Select | `Day of Week\nPeak Hours\nSpecific Date Range` | Rule scope |
| `days_of_week` | MultiCheck / Data | `Saturday, Sunday` | Days trigger applies |
| `start_time` | Time | Optional | Peak time window start |
| `end_time` | Time | Optional | Peak time window end |
| `start_date` | Date | Optional | Date range start override |
| `end_date` | Date | Optional | Date range end override |
| `adjustment_type` | Select | `Percentage Increase\nFixed Amount Add\nOverride Flat Price` | Pricing calculation mode |
| `value` | Float | e.g. `20` (+20%), `15` (+$15) | Value of adjustment |

---

## 3. `Venue Blackout Date` (Child DocType)
* **Is Subtable**: Yes (`istable: 1`)

### Fields

| Fieldname | Fieldtype | Details | Description |
|-----------|-----------|---------|-------------|
| `title` | Data | e.g. "Maintenance Day", "National Holiday" | Reason for blackout |
| `start_date` | Date | Mandatory | Blackout start date |
| `end_date` | Date | Mandatory | Blackout end date |
| `full_day` | Check | Default: 1 | If checked, blocks all slots for full date range |
| `start_time` | Time | Optional | If partial day blackout |
| `end_time` | Time | Optional | If partial day blackout |

---

## 4. `Venue Slot Lock` (DocType)
* **Is Subtable**: No
* **Autoname**: `hash`
* **Purpose**: Prevents double-booking during checkout. Auto-cleared by background worker after 15 mins.

### Fields

| Fieldname | Fieldtype | Details | Description |
|-----------|-----------|---------|-------------|
| `venue` | Link | `Venue` | Mandatory | Target venue |
| `booking_date` | Date | Mandatory | Date of the slot |
| `start_time` | Time | Mandatory | Slot start time |
| `end_time` | Time | Mandatory | Slot end time |
| `locked_by_session` | Data | Mandatory | Guest session token / customer ID |
| `expires_at` | Datetime | Mandatory | Lock expiration timestamp (now + 15m) |
| `status` | Select | `ACTIVE\nCONVERTED\nEXPIRED` | Status flag |

---

## 5. `Venue Booking` (DocType)
* **Is Subtable**: No
* **Autoname**: `format:VB-{YYYY}-{#####}`

### Fields

| Fieldname | Fieldtype | Details | Description |
|-----------|-----------|---------|-------------|
| `venue` | Link | `Venue` | Target venue |
| `customer_name` | Data | Mandatory | Full name of booker |
| `customer_email` | Data | Mandatory | Email for invite & receipt |
| `customer_phone` | Data | Mandatory | Contact number |
| `booking_date` | Date | Mandatory | Booking date |
| `start_time` | Time | Mandatory | Slot start |
| `end_time` | Time | Mandatory | Slot end |
| `base_amount` | Currency | - | Base slot rate before rules |
| `pricing_adjustments` | Currency | - | Surcharges / discounts applied |
| `total_amount` | Currency | Mandatory | Final price paid |
| `payment_status` | Select | `UNPAID\nPAID\nREFUNDED` | Payment gateway state |
| `payment_transaction_id` | Data | - | Mock / Gateway transaction ref |
| `invite_token` | Data | Unique, Indexed | Secret slug for shareable invite link |
| `notes` | Text | - | Special requests from customer |
| `rsvps` | Table | `Booking Guest RSVP` | Child table for invite RSVPs |

---

## 6. `Booking Guest RSVP` (Child DocType)
* **Is Subtable**: Yes (`istable: 1`)

### Fields

| Fieldname | Fieldtype | Details | Description |
|-----------|-----------|---------|-------------|
| `guest_name` | Data | Mandatory | Guest name |
| `guest_email` | Data | Optional | Guest email |
| `status` | Select | `GOING\nMAYBE\nDECLINED` | Attendance response |
| `submitted_at` | Datetime | Mandatory | RSVP timestamp |
