# Spaces & Venues Specification

## 1. Overview
This specification details the `Venue`, `Venue Pricing Rule`, and `Venue Blackout Date` DocType schemas and management.

---

## 2. DocType Schemas

### A. `Venue` (Main DocType)
* **Autoname**: `field:slug`

| Fieldname | Fieldtype | Details | Mandatory | Description |
|-----------|-----------|---------|-----------|-------------|
| `title` | Data | - | Yes | Venue / turf / room title |
| `slug` | Data | Unique | Yes | URL-friendly identifier |
| `owner_user` | Link | `User` | Yes | Host owner |
| `is_published` | Check | Default: 1 | No | Marketplace visibility |
| `description` | Text Editor | - | No | Overview |
| `address` | Small Text | - | Yes | Location address |
| `city` | Data | - | Yes | City filter |
| `rules_and_amenities` | Small Text | - | No | Usage policies |
| `slot_duration_mins` | Select | `30\n60\n90\n120` | Yes | Duration per slot (Default: `60`) |
| `opening_time` | Time | Default: `08:00:00` | Yes | Daily start time |
| `closing_time` | Time | Default: `22:00:00` | Yes | Daily end time |
| `base_price_per_slot` | Currency | Min: 0 | Yes | Base rate per slot |
| `cover_image` | Attach Image | - | No | Primary photo |
| `pricing_rules` | Table | `Venue Pricing Rule` | No | Child table for surge pricing |
| `blackout_dates` | Table | `Venue Blackout Date` | No | Child table for maintenance dates |

### B. `Venue Pricing Rule` (Child DocType)
* **Subtable**: `istable: 1`

| Fieldname | Fieldtype | Details | Description |
|-----------|-----------|---------|-------------|
| `rule_name` | Data | Mandatory | Rule label (e.g. Weekend Surge) |
| `apply_on` | Select | `Day of Week\nPeak Hours\nSpecific Date Range` | Scope |
| `days_of_week` | Data | e.g. `Saturday, Sunday` | Days trigger |
| `start_time / end_time` | Time | Optional | Peak time window |
| `start_date / end_date` | Date | Optional | Override date range |
| `adjustment_type` | Select | `Percentage Increase\nFixed Amount Add\nOverride Flat Price` | Calculation mode |
| `value` | Float | e.g. `20` (+20%), `15` (+$15) | Adjustment value |

### C. `Venue Blackout Date` (Child DocType)
* **Subtable**: `istable: 1`

| Fieldname | Fieldtype | Details | Description |
|-----------|-----------|---------|-------------|
| `title` | Data | Mandatory | Blackout reason |
| `start_date / end_date` | Date | Mandatory | Blackout range |
| `full_day` | Check | Default: 1 | Block all slots if checked |
| `start_time / end_time` | Time | Optional | Partial day window |
