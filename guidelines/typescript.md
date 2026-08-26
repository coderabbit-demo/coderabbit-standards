# TypeScript service conventions

## Shell execution

- Never pass user-controlled or interpolated input to a shell. Do not use
  `child_process.exec` or `execSync` with template literals. Use `execFile("cmd", [args])`
  with an argument array so inputs cannot be interpreted by a shell.

## Input validation before side effects

- Validate request input before it reaches a side effect (a write, an outbound call, or a
  shell). Malformed input must be rejected with a `400` before any service call runs.

## No unbounded outbound calls

- Every outbound `fetch` from a request handler must carry a bounded timeout
  (`AbortSignal.timeout(...)`). A handler must not stay pending on an unresponsive dependency.
