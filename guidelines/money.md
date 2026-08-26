# Money & currency

These rules are mandatory for any code that computes, stores, or returns monetary values.

## Integer cents only

- Monetary values are **integer cents** (for example `subtotalCents: number`), never a
  floating-point number of dollars.
- Never use a floating-point multiplier to compute a price or a discount. A discount of 10%
  is `Math.round(cents * 90 / 100)`, not `cents * 0.9` applied to a float total, and never
  `cents * 1.1`.
- Round only once, at the boundary, in cents. Do not accumulate rounding across steps.

**Rationale:** floating-point arithmetic on currency silently loses precision and produces
off-by-one-cent errors that reconcile incorrectly at scale.

## Names carry the unit

- A field holding cents ends in `Cents` (`subtotalCents`, `taxCents`, `totalCents`). A field
  without that suffix must not hold a monetary amount.
- Do not rename a `*Cents` field to drop the unit, and do not change its type to a float.

## Responses are allow-listed DTOs

- API responses must be explicit, allow-listed DTOs. Never return a raw persistence record
  that could carry credentials, tokens, or full payment data.
