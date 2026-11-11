# CM Kerala Citizen Connect

A full-stack recruitment assessment prototype for a citizen grievance portal. It demonstrates citizen registration, grievance submission and tracking, CMO review and routing, department officer updates, action-taken reports, status history, feedback, file attachments, contact enquiries, service statistics and an AI triage assistant.

> **Prototype notice:** This project is not operated, approved or endorsed by the Government of Kerala or the Chief Minister’s Office. Use fictional records only. The built-in accounts and sample grievances are for demonstration.

## Run locally

Requires Node.js 18 or newer and a MongoDB database (MongoDB Atlas is suitable). The application uses the official MongoDB Node.js driver.

```bash
npm.cmd install
npm.cmd start
```

Before starting, add `MONGODB_URI` and `MONGODB_DB=cm_kerala_portal` to `.env`. To preserve the Gemini key already in your `.env`, add the MongoDB settings to that file instead of replacing it with `.env.example`. The first connection creates the application collections and seeds fictional demo accounts and sample grievances when the database is empty. Open [http://localhost:3000](http://localhost:3000).

### Demo accounts

| Workspace | Email | Password |
|---|---|---|
| Citizen | `citizen@kerala.demo` | `Citizen123!` |
| CMO administrator | `admin@kerala.demo` | `Admin123!` |
| Department officer (Public Works & Roads) | `officer@kerala.demo` | `Officer123!` |

Choose a demo account from the sign-in dialog to fill its credentials. The application stores passwords as scrypt hashes. These accounts are intentionally public demonstration accounts and must not be used for real information.

## AI configuration

Create a Gemini API key in [Google AI Studio](https://aistudio.google.com/app/apikey), copy `.env.example` to `.env`, and paste the key after `GEMINI_API_KEY=`. Restart the server after changing `.env`. The server loads this local file; `.env` is excluded from Git. On Render, add `GEMINI_API_KEY` and optionally `GEMINI_MODEL` in the service's Environment settings instead.

The assistant sends a bounded grievance description to the Gemini Interactions API with structured JSON output when `GEMINI_API_KEY` is set. The key stays on the server and requests set `store: false`. Set `GEMINI_MODEL` to a model available to your account; the default is `gemini-3.8-flash`. If no key is configured or the API call fails, a local keyword-based demonstration fallback labels itself as a fallback in the interface. That fallback is **not** a completed AI integration. Every suggestion requires human review and cannot make an official decision.

## Prototype architecture

- **Frontend:** semantic HTML, responsive CSS and vanilla JavaScript, served by the Node server.
- **Backend:** Node.js HTTP server with JSON REST endpoints, role checks and password hashing.
- **Persistence:** MongoDB collections for users, departments, grievances, status history, assignments, escalations, attachment metadata and bytes, feedback and contact enquiries. Indexes cover account email, grievance references, ownership and history.
- **Authentication:** bearer tokens held in process memory. Users sign in again after a server restart. This is suitable for a local demo, not production deployment.
- **AI:** optional server-side model API with explicit fallback labeling and human review.

Diagrams and deeper specifications are in [`DOCUMENTATION/`](DOCUMENTATION/).

## Deployment

The repository includes [`render.yaml`](render.yaml) for a Render Blueprint. In Render, create a Blueprint from this GitHub repository and provide `MONGODB_URI` and `GEMINI_API_KEY` when prompted. Add the Render service's outbound access to your MongoDB provider's allowlist if required. After deployment, confirm `<your-service-url>/api/health` returns `{"status":"ok"}`. Use the hosted URL as the application link and make a 3–5 minute screen recording of the citizen, CMO and officer workflows for the assessment submission. The Blueprint setup is described in [Render's official documentation](https://render.com/docs/blueprint-spec).

## Main workflow

1. Sign in as the citizen and submit a fictional concern. The portal returns a reference number and records `Submitted` in history.
2. Sign in as the CMO administrator, open the grievance, choose a department and record the review. The assigned case enters `In Progress` (or stays `Under Review` when selected).
3. Sign in as the department officer. Review assigned cases and enter investigation notes. Selecting `Resolved` submits an action-taken report to the CMO; it does not resolve the case by itself.
4. Sign in as the CMO administrator, review the report and record a decision and citizen-facing response. This is where the grievance can be resolved, reopened or returned to `In Progress`.
5. Sign in as the citizen to view the final response and history, submit feedback or request reopening.

Unauthenticated tracking by reference is available from the public site. Private grievance records and attachment retrieval require the correct role and ownership/assignment.

## Data reset

To reset the demo, remove the application collections from the configured MongoDB database; the server seeds fictional demo data again when the collections are empty. Keep `.env` and real secrets out of version control.
