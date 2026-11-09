# Test cases

All tests used **synthetic leads** (invented names and companies, my own test inbox) submitted through the live Tally form, and were run manually with Make's **Run once** unless noted.
Each result was checked in the actual record (Sheet row, Slack message, Zoho lead, Gmail draft), not only by Make's green ticks.

Test numbers follow the order the tests were run, so tests 16–17 appear under the daily summary scenario.

**Legend:** ✅ passed · ⚠️ passed with an issue to watch · 🔧 found a bug, fixed, then re-tested

## Main scenario: Lead Qualification – Main

| # | Test | Input (synthetic) | Expected | Result | What it proved |
|---|---|---|---|---|---|
| 1 | Hot lead | Priya Raman, Bluewave Logistics | hot, score 8–10 | hot, 9 ✅ | Scoring rules work for a clear, well-funded need |
| 2 | Prompt injection | "Test Hacker", Acme Fake Ltd; message tells the AI to ignore its rules | spam, score 1, empty reply | spam, 1, empty reply ✅ | Security rule: lead text is treated as data, not instructions |
| 3 | Warm lead | Arjun Mehta, Greenleaf Bakery | warm, 5–7 | Gemini 503 first; retried → warm, 6 ✅ | Warm band works; first sign that error handling was needed |
| 4 | Cold lead | Kavya S, student | cold, 2–4 | cold, 2 ✅ | Low-intent leads are not over-scored |
| 5 | Hot, end to end | Rahul Iyer, Coastline Freight | Leads row + Slack alert | hot, 10; row saved; Slack alert ✅ | Save-then-branch design and the hot route |
| 6 | Duplicate | Rahul resubmitted with the same email | Logged as duplicate; no AI call | Log Duplicate only; Gemini skipped ✅ | Dedupe check stops repeats before spending AI credits |
| 7 | Hot + CRM, blank company | Meera Lakshmi Nair, no company | Zoho lead; Company "Not provided"; correct name split | hot, 9; Zoho lead created 🔧 name split failed → fixed → ✅ | Required CRM fields filled safely; multi-word last name handled |
| 8 | CRM out of sync | Meera resubmitted after her Sheet row was deleted | Same Zoho record updated, not a second one | Same record updated ✅ | Upsert on Email is a second line of defence against duplicates |
| 9 | Warm + CRM | Ananya Krishnan | Warm route creates Zoho lead | warm, 6; Zoho ✅ 🔧 Sheet turned "1-10" into a date → columns set to Plain text | Data-type handling in Google Sheets (and formula-injection protection) |
| 10 | Warm + Gmail draft | Vikram Rao, Sunrise Yoga Studio | Draft reply with paragraphs | Draft created 🔧 one squashed paragraph → prompt layout rule + line-break conversion | Formatting AI text for email |
| 11 | Warm re-test | Divya Menon, Menon Dental Care | Paragraphs; no prices or promises | Paragraphs ✅; no timeline or promise ✅; "20-minute" call missing ⚠️ | A prompt rule is a request, not a guarantee |
| 12 | Hot + blank company | Karthik Subramanian | Slack shows "Not provided"; Zoho + draft | hot, 9; Slack, Zoho, draft ✅; "20-minute" missing ⚠️ | Blank optional fields handled across all three apps |
| 13 | AI failure drill | Sneha Pillai; model name deliberately broken | Errors row + Slack alert + run parked | 404 → Errors row, Slack alert, parked; nothing saved to Leads ✅ | Error route works; failures happen before any write |
| 14 | Automatic retry | Scenario briefly activated with test 13 parked | Retry runs on schedule | Attempts 0 → 1; same 404 replayed ✅ | Retries need an active scenario and replay the stored snapshot |
| 15 | Real outage recovery | Farhan (one-word name) during a real Gemini 503 | Retry until success; full result | 503 twice → 2nd retry succeeded → Resolved; warm, 6; Zoho; draft ✅ | Recovery from a real provider outage with no duplicate rows |
| 18 | Invalid lead | Name temporarily made optional in Tally; submitted blank | Logged as invalid; no AI call | Log Invalid Lead only; Gemini skipped ✅; Name made Required again and verified ✅ | Second validation layer works when the form layer is bypassed |
| 19 | One-word name in CRM | Farhan | First Name empty, Last Name "Farhan" | Confirmed in Zoho Edit view ✅ | Name-split formula handles single names |

## Daily summary scenario: Lead Qualification – Daily Summary

| # | Test | Expected | Result | What it proved |
|---|---|---|---|---|
| 16 | Daily summary | One Slack message matching the Summary tab | 1 message; values matched the Sheet (1 / 0 / 1 / 2) ✅ | Counting in the Sheet + reading one row = exactly one message, about 2 credits |
| 17 | Follow-up reminder | Awaiting follow-up counts hot/warm leads 2+ days old with Status "new" | 6, with the newest lead correctly excluded ✅ 🔧 first showed 3 → formula fixed | Checking a formula against a known number catches silent errors |

## Known issues still being watched

- The reply sometimes leaves out the exact "20-minute discovery call" wording (tests 11–12); test 15 wrote "twenty-minute".
- Large budgets reach a score of 10 easily.
