# Payments & Checkout Specification

## 1. Overview
This specification details checkout flow, 15-minute countdown timers, and mock payment gateway simulation.

---

## 2. Checkout & Mock Payment Flow

### API: `avenza_bookings.api.booking.process_mock_payment(lock_id, customer_name, customer_email, customer_phone, notes, payment_token)`

1. Validate `lock_id`: Must exist, `status == 'ACTIVE'`, and `expires_at > now()`. If expired, reject checkout with warning message.
2. Gateway Simulation:
   - If `payment_token == 'FAIL'`, reject transaction and return payment failure status.
   - If payment succeeds:
     1. Generate 12-char unique random secret `invite_token`.
     2. Insert `Venue Booking` record with `payment_status = 'PAID'`.
     3. Update `Venue Slot Lock` status to `CONVERTED`.
     4. Return `{ "status": "SUCCESS", "booking_id": booking.name, "invite_token": booking.invite_token }`.

---

## 3. Checkout Modal UI Requirements
- Embedded 15-minute countdown timer synced with `expires_at`.
- Auto-closes modal with warning toast if timer hits `00:00`.
- Mock payment buttons: `Pay Now` (triggers mock success) vs `Simulate Failure`.
