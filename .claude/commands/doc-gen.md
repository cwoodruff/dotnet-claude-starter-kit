---
description: Generate XML documentation for all public members in a C# file
---

Add XML documentation comments to every public type and member in the file I name.

Rules:

- `<summary>` says what it does and why it exists, not what the signature already
  says. "Gets the customer" on `GetCustomer` is noise.
- Every parameter gets `<param>`. Document the constraint, not the type.
- `<returns>` describes the shape and the empty or null case.
- Document every exception the method can actually throw with `<exception>`.
- Async methods: say what the task completes with.
- `CancellationToken` parameters get a one-line `<param>`; do not pad them.
- Do not change any code. Comments only.
- Leave existing documentation alone unless it is wrong, and say so if it is.
