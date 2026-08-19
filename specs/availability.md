# Availability & Slot Engine Specification

## 1. Overview
This specification details the dynamic slot generation engine, dynamic pricing calculation matrix, 15-minute slot lock concurrency model, and background scheduler cleanup.

---

## 2. Dynamic Slot Generation Pipeline

### API: `avenza_bookings.api.venue.get_available_slots(venue_slug, booking_date)`

1. Fetch `Venue` record by `venue_slug`.
2. Generate candidate slot intervals from `opening_time` to `closing_time` in increments of `slot_duration_mins`.
3. Check `Venue Blackout Date` records. If `booking_date` falls in full blackout, return empty slots list.
4. Query active `Venue Slot Lock` (`status='ACTIVE'`, `expires_at > now()`) and `Venue Booking` (`payment_status='PAID'`) records.
5. Classify candidate slots:
   - 🔴 **BOOKED**: Overlaps with a paid booking.
   - 🟡 **LOCKED**: Overlaps with an active 15-minute lock.
   - 🟢 **AVAILABLE**: Available for booking. Evaluate dynamic price using `calculate_slot_price()`.

---

## 3. Dynamic Pricing Matrix

### API: `avenza_bookings.api.venue.calculate_slot_price(venue, booking_date, start_time, end_time)`

1. Start with `venue.base_price_per_slot`.
2. Evaluate `Venue Pricing Rule` child records:
   - **Day of Week**: If day of `booking_date` matches `days_of_week`.
   - **Peak Hours**: If `start_time` and `end_time` overlap peak window.
   - **Date Range**: If `booking_date` is between `start_date` and `end_date`.
3. Apply price adjustments:
   - `Percentage Increase`: `price += base_price * (value / 100)`
   - `Fixed Amount Add`: `price += value`
   - `Override Flat Price`: `price = value`

---

## 4. Concurrency Control & Slot Locking

### API: `avenza_bookings.api.booking.lock_slot(venue, booking_date, start_time, end_time, session_token)`

- Database locking: Executed inside transaction with `SELECT ... FOR UPDATE` on `Venue Slot Lock`.
- Insert `Venue Slot Lock` record with `expires_at = now() + 15 minutes` and `status = 'ACTIVE'`.
- Prevents double-booking during checkout.

### Background Scheduler Task
- `avenza_bookings.tasks.clear_expired_slot_locks`
- Runs every 5 minutes (`hooks.py` `scheduler_events.cron: "*/5 * * * *"`).
- Updates `Venue Slot Lock` records where `status = 'ACTIVE'` and `expires_at < now()` to `status = 'EXPIRED'`.
