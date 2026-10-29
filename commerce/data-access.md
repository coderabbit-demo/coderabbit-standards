# Data access in commerce services

## Parameterized SQL only

- Every **value** that reaches SQL is a bound parameter (`?` or `:name`). Never build SQL with
  template literals, string concatenation, or `sql.raw` — including values that "look safe"
  such as path IDs or discount codes.
- **Identifiers** (column names, sort direction) cannot be bound parameters. When one must
  vary, map the request input to a fixed, allow-listed identifier
  (`{ price: "price_cents", name: "name" }[sort]`) and reject anything else with `400`. Never
  interpolate the raw request value, even into `ORDER BY`.

## Validate before the database

- Validate request bodies and path parameters at the route boundary and reject malformed
  input with `400` before any query runs. IDs are validated against their expected format.

## Multi-statement writes are transactions

- Any change that writes more than one row or table runs inside a single transaction, and
  either fully commits or fully rolls back.

## Service-to-service calls

- Services own their data. Never read or write another service's database; call its API.
- Every outbound call carries a bounded timeout (`AbortSignal.timeout(...)`) and its failure
  is surfaced as a `502`/`503`, never silently treated as an empty result.
