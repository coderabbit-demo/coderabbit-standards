# Data access in commerce services

## Parameterized SQL only

- Every value that reaches SQL is a bound parameter (`?` or `:name`). Never build SQL with
  template literals, string concatenation, or `sql.raw` — including values that "look safe"
  such as path IDs, discount codes, or sort keys.
- Identifiers that must vary (sort column, direction) come from an explicit allow-list.

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
