# Gemini output schema

Set in module [12] with **JSON Output** and Make's no-code response schema.
Gemini must return exactly these keys, so later modules can map them safely.

| Key | Type | Required | Description |
|---|---|---|---|
| `score` | integer (1–10) | Yes | Lead quality score from 1 (spam) to 10 (hottest) |
| `category` | enum: hot / warm / cold / spam | Yes | Lead category |
| `summary` | string | Yes | One sentence, max 20 words, describing what the lead wants |
| `reply_draft` | string | Yes | Reply email body, 80-120 words; empty string if spam |

## Example output (illustrative, shortened)

The shape of a typical hot-lead answer. The reply is cut short here; real drafts are 80–120 words.

```json
{
  "score": 9,
  "category": "hot",
  "summary": "Logistics firm wants shipment-status emails and invoice data entry automated within a month.",
  "reply_draft": "Hi Priya,\n\nThank you for reaching out to Nimbus Automation...\n\nThe Nimbus Automation Team"
}
```

## Why a schema matters here

- **`category` is an enum**, so the Category Router can route on it with no fallback route: only the four allowed values can ever arrive.
- **`score` is a bounded integer**, so the Sheet's Score column and the daily-summary counts stay numeric.
- **All four keys are required**, so a module never receives a half-filled answer. If the AI call fails completely, the error route handles it.
