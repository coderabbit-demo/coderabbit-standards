# Orders, discounts & inventory

These rules are mandatory for any service that creates, prices, or changes an order. They
protect three invariants that downstream services (fulfillment, finance, analytics) rely on.

## Order lines are physical goods

- Every order line references a real catalog SKU and has an integer `quantity >= 1`.
- Never represent a discount, credit, fee, or adjustment as an order line. Do not add a
  line with a negative or zero quantity, a negative unit price, or a synthetic SKU such as
  `DISCOUNT-*`.
- Database constraints that guard these invariants (`CHECK (quantity > 0)`,
  `CHECK (unit_price_cents >= 0)`) must not be dropped or relaxed to make a change pass.

**Rationale:** fulfillment builds pick lists from order lines. A non-physical or
negative-quantity line produces an impossible pick task and corrupts stock counts.

## Discounts are order-level adjustments

- A discount is stored on the order (`discount_code`, `discount_cents`), never as a line.
- `discountCents` is an integer between `0` and `subtotalCents`, inclusive. Clamp fixed-amount
  discounts to the subtotal; reject percentages outside `1..100`.
- `netCents = subtotalCents - discountCents` and is therefore never negative. A negative net
  amount is a defect, not a credit.
- Compute `subtotalCents` from order lines only. Never subtract a discount from the subtotal
  and then subtract it again for the net.
- Percentage discounts: `Math.round(subtotalCents * percent / 100)`, rounded once, in cents.

## One code per order, applied idempotently

- An order carries at most one discount code. Applying the same code again is a no-op;
  applying a different code replaces the previous one and must be explicit.
- Respect `active`, `starts_at`/`expires_at`, `min_subtotal_cents`, and
  `max_redemptions`. Discount codes are case-insensitive and stored upper-case.
- Only orders in `cart` status may change their discount. Placed orders are immutable.

## Redemption is atomic

- Checking `max_redemptions`, incrementing `times_redeemed`, and updating the order happen in
  a single database transaction. A check followed by a separate write is a race condition
  that lets a limited code be redeemed more times than allowed.
