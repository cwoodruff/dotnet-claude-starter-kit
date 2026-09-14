---
description: Security-focused review of an ASP.NET Core file
---

Review the file or files I name for security problems. Read them fully first.

Check every one of these categories:

1. SQL injection, including raw SQL, `FromSqlRaw`, and string-built queries
2. Missing or insufficient input validation on anything user supplied
3. Authentication and authorization gaps on endpoints that need them
4. Sensitive data exposure: EF entities returned directly, secrets in responses,
   over-broad DTOs, personally identifying data in logs
5. Missing rate limiting on expensive or unauthenticated endpoints
6. CSRF exposure on state-changing endpoints
7. Insecure deserialization and unbounded payload sizes

Output a table with these columns, most severe first:

| Severity | Location (file:line) | Issue | Remediation |

Severity is Critical, High, Medium or Low. Be specific in Remediation: name the
change, do not say "add validation".

If you find nothing in a category, say so explicitly rather than staying silent.
Do not modify any files. This is a review.
