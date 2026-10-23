---
tags: [data-engineering, http, ingestion]
source: [rin project docs, 2026-08]
---
# Classify the Response Body, Never the Status Code

An HTTP 200 is not success. A server can return 200 for a viewer-shell page instead of the requested file, an application-level rejection message, or a validly-empty export — all indistinguishable from a real result by status code alone. Classify by the response body's actual content instead: a magic-byte/content prefix for a real file (e.g. `PK` for xlsx/zip-based formats), a known fallback marker for a shell/interactive-viewer response, and known error-message text for app-level rejections.

## When to use
Any HTTP-based ingestion pulling files or reports from a system that can silently substitute a different (but still 200) response — report servers, BI tools, and any endpoint whose "success" page and "your request was misconfigured" page share a status code.

## Gotchas
- A source returning a genuinely empty result set is still a real 200 with valid (empty) content — don't conflate "no rows" with "wrong response." Check the request parameters before suspecting the transport.
- See [[Confounded Measurement Cascades]] for how trusting status codes over body content can chain into multiple wrong conclusions that look like independent evidence.

## Related
[[Confounded Measurement Cascades]]
