# CM Kerala Citizen Connect

A full-stack recruitment assessment prototype for a citizen grievance portal. It demonstrates citizen registration, grievance submission and tracking, CMO review and routing, department officer updates, action-taken reports, status history, feedback, file attachments, contact enquiries, service statistics and an AI triage assistant.

> **Prototype notice:** This project is not operated, approved or endorsed by the Government of Kerala or the Chief Minister’s Office. Use fictional records only. The built-in accounts and sample grievances are for demonstration.

## Run locally

Requires Node.js 22 and a MongoDB database (MongoDB Atlas is suitable). The application uses the official MongoDB Node.js driver.

```bash
npm.cmd install
npm.cmd start
```

Before starting, add `MONGODB_URI`, `MONGODB_DB=cm_kerala_portal` and a random `SESSION_SECRET` to `.env`. Preserve your existing Gemini key when editing the file. The first connection creates the application collections and seeds fictional demo accounts and sample grievances when the database is empty. Open [http://localhost:3000](http://localhost:3000).

### Demo accounts

| Workspace | Email | Password |
|---|---|---|
| Citizen | `citizen@kerala.demo` | `Citizen123!` |
| CMO administrator | `admin@kerala.demo` | `Admin123!` |
| Department officer (Public Works & Roads) | `officer@kerala.demo` | `Officer123!` |

Choose a demo account from the sign-in dialog to fill its credentials. The application stores passwords as scrypt hashes. These accounts are intentionally public demonstration accounts and must not be used for real information.

## AI configuration

Create a Gemini API key in [Google AI Studio](https://aistudio.google.com/app/apikey), then add it as `GEMINI_API_KEY` in your local `.env` or Vercel's Environment Variables. Preserve the other settings already in `.env`. Restart the local server after changing `.env`; it is excluded from Git. On Render, add the key in the service's Environment settings.

The assistant sends a bounded grievance description to the Gemini Interactions API with structured JSON output when `GEMINI_API_KEY` is set. The key stays on the server and requests set `store: false`. Set `GEMINI_MODEL` to a model available to your account; the default is `gemini-3.8-flash`. If no key is configured or the API call fails, a local keyword-based demonstration fallback labels itself as a fallback in the interface. That fallback is **not** a completed AI integration. Every suggestion requires human review and cannot make an official decision.

## Prototype architecture

- **Frontend:** semantic HTML, responsive CSS and vanilla JavaScript, served by the Node server.
- **Backend:** Node.js HTTP server with JSON REST endpoints, role checks and password hashing.
- **Persistence:** MongoDB collections for users, departments, grievances, status history, assignments, escalations, attachment metadata and bytes, feedback and contact enquiries. Indexes cover account email, grievance references, ownership and history.
- **Authentication:** signed bearer tokens expire after eight hours. Set a long random `SESSION_SECRET`; signing out removes the token from that browser, but does not revoke a copied token before it expires.
- **AI:** optional server-side model API with explicit fallback labeling and human review.

Diagrams and deeper specifications are in [`DOCUMENTATION/`](DOCUMENTATION/).

## Deploy to Vercel

The repository includes [`vercel.json`](vercel.json), a static `public/` site and a Node.js function for the `/api/*` routes. Import this GitHub repository into Vercel with the **Other** framework preset. The configuration sets `public` as the output directory; leave the build command empty. Add these Environment Variables in Vercel for Preview and Production:

- `MONGODB_URI` — your MongoDB Atlas connection URI.
- `MONGODB_DB` — `cm_kerala_portal`.
- `SESSION_SECRET` — a long random string; generate one with `node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"`.
- `GEMINI_API_KEY` — optional, for live Gemini assistance.
- `GEMINI_MODEL` — optional; defaults to `gemini-3.8-flash`.

Redeploy after adding environment variables. Confirm `<your-vercel-url>/api/health` returns `{"status":"ok"}`, then sign in and try the citizen, CMO and officer demo workflows. Attachments are limited to 3 MB to fit the serverless request limit. For a low-traffic assessment demo, this setup is suitable; the app still keeps an in-memory data cache, so concurrent serverless instances can overwrite stale record versions. For reliable concurrent use, move the API to a persistent single server or refactor every mutation to targeted MongoDB operations. Vercel's [Other framework build settings](https://vercel.com/docs/builds/configure-a-build) and [Node.js Functions](https://vercel.com/docs/functions/runtimes/node-js) explain the hosting model.

The repository also includes [`render.yaml`](render.yaml) as an alternative for running the app as a persistent Node service.
## Main workflow

1. Sign in as the citizen and submit a fictional concern. The portal returns a reference number and records `Submitted` in history.
2. Sign in as the CMO administrator, open the grievance, choose a department and record the review. The assigned case enters `In Progress` (or stays `Under Review` when selected).
3. Sign in as the department officer. Review assigned cases and enter investigation notes. Selecting `Resolved` submits an action-taken report to the CMO; it does not resolve the case by itself.
4. Sign in as the CMO administrator, review the report and record a decision and citizen-facing response. This is where the grievance can be resolved, reopened or returned to `In Progress`.
5. Sign in as the citizen to view the final response and history, submit feedback or request reopening.

Unauthenticated tracking by reference is available from the public site. Private grievance records and attachment retrieval require the correct role and ownership/assignment.

## Data reset

To reset the demo, remove the application collections from the configured MongoDB database; the server seeds fictional demo data again when the collections are empty. Keep `.env` and real secrets out of version control.
