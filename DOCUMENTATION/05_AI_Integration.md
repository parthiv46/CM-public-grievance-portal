# AI integration

## Live model path

If `GEMINI_API_KEY` is set in the server environment, `POST /api/analyze` sends the complaint description to the Gemini Interactions API with a JSON schema response format. `GEMINI_MODEL` selects the model, defaulting to `gemini-3.8-flash`. The prompt requests a category, department, priority, short summary and optional duplicate note. The server validates the returned category, department and priority against allowed values before returning it. The request sets `store: false`.

Implementation references: [Gemini Interactions API](https://ai.google.dev/gemini-api/docs/interactions) · [Gemini structured outputs](https://ai.google.dev/gemini-api/docs/structured-output).

The server key is never sent to the browser. Configure usage limits and review the model provider’s current data terms before using any non-fictional content. This prototype has no model timeout/retry policy or automated quality evaluation.

## No-key fallback

Without a configured key, the endpoint returns a local keyword-based suggestion and sets `modeLabel` to `Local fallback · human review required`. If the model call fails, the response label also states that Gemini is unavailable. The fallback is included to keep the interface demonstrable, but it is **not** a completed AI integration and should not be described as one.

## Human review and safeguards

- Suggestions never assign a department or change grievance status automatically.
- Citizens can edit the category and priority before submission; CMO staff review routing.
- The CMO reviews an officer’s action-taken report before a case becomes resolved.
- The prompt avoids inferring personal facts, and the public site asks users not to submit sensitive information.
- Use fictional descriptions in demos. The model is an assistive triage tool, not an official decision-maker.
