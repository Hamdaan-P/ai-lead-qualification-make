# Sample payloads

## tally-webhook-warm-lead.json

A real webhook body that Tally sent to Make during testing (test 15, "Farhan"), cleaned for publishing.
All test data is synthetic: an invented lead using my own test inbox.

**What was changed before publishing**

| Field | Change |
|---|---|
| Email | Replaced with `farhan@example.com` |
| Company | The form's autofill had put the email address here; replaced with `Farhan Prints`, the value corrected in the Sheet and CRM |
| `submissionPdfUrl`, `submissionPreviewUrl` | Removed, because they contained private access tokens |
| `formId` | Replaced with `YOUR_FORM_ID` so the live demo form can't be targeted |

**How to read it**

- Each answer is in `data.fields` and identified by a question `key`. The scenario matches on these keys, not on position.
- Dropdowns (Company Size, Budget) send an **option ID**, not the text. Module [2] Parse Lead Fields looks the ID up in that field's `options` list. Here, Budget resolves to `$1k-$5k`.
- This lead was scored **warm, 6** and routed to `follow_up` (CRM record + Gmail draft).
- On its first attempt Gemini returned a real 503 "high demand" error. The error route logged and alerted, and the 2nd automatic retry succeeded.
