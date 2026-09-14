---
description: Security-focused review of ASP.NET Core code (Razor Pages and APIs)
---

Review the file or files I name for security problems. Read them fully first, and
include any `.cshtml` or `PageModel` partner file even if I only named one of them.

## Check every category that applies

### Everywhere

1. SQL injection: raw SQL, `FromSqlRaw`, string-built queries, dynamic LINQ
2. Missing or insufficient validation on anything user supplied
3. Authentication and authorization gaps
4. Sensitive data exposure: EF entities returned or bound directly, secrets in
   responses or logs, personally identifying data in telemetry
5. Missing rate limiting on expensive or unauthenticated paths
6. Insecure deserialization and unbounded payload or upload sizes

### Razor Pages

7. **Overposting.** `[BindProperty]` bound to an EF entity or a domain model
   instead of a purpose-built `InputModel`. Also `TryUpdateModelAsync` without an
   explicit include list. Report the exact properties an attacker could set.
8. **Antiforgery.** `IgnoreAntiforgeryToken`, a disabled global filter, or a
   JavaScript post that does not send `RequestVerificationToken`.
9. **XSS.** `@Html.Raw()`, `HtmlString`, or `MarkupString` applied to anything a
   user can influence, directly or through the database.
10. **Open redirect.** `Redirect()` or `RedirectToPage` fed a user-supplied URL or
    return path. Should be `LocalRedirect()`.
11. **Page authorization.** A PageModel with no `[Authorize]` and no covering
    convention in `Program.cs`. Check the conventions before reporting.
12. **State leakage.** Sensitive values in `TempData`, `ViewData`, or hidden form
    fields that the server then trusts on the way back in.

### HTTP API

13. CSRF exposure on cookie-authenticated state-changing endpoints
14. EF entities returned instead of DTOs
15. Missing `[Authorize]` or an over-broad policy

## Output

A table, most severe first:

| Severity | Location (file:line) | Issue | Remediation |

Severity is Critical, High, Medium or Low. Be specific in Remediation: name the
change. "Add validation" is not a remediation. "Bind to a nested `InputModel`
exposing only `BillingAddress` and `Total`" is.

State explicitly when a category has no findings rather than staying silent, and
say which categories did not apply to the files you read.

Do not modify any files. This is a review.
