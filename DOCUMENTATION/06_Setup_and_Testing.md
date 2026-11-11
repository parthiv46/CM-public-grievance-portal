# Setup and manual verification guide

## Requirements

- Node.js 18 or newer.
- No package installation is required. The project uses Node.js built-in modules only.

## Setup

1. Copy `.env.example` to `.env` only if configuring the optional model integration. Keep real keys private. The server reads environment variables directly; the sample file is a reference and is not auto-loaded.
2. Start with `npm start` or `node server.js`.
3. Visit `http://localhost:3000` and sign in with one of the demo accounts listed in the root README.
4. The first server start creates the data store. Stop the server before manually resetting local data.

## Manual verification cases

These are suggested evaluation steps; they are not a claim that automated tests have been run.

1. Browse Home, About, How it works, Departments, Contact and FAQ at desktop and mobile widths.
2. Sign in as citizen, submit a fictional grievance without an attachment, and confirm that a reference and initial history entry appear.
3. Submit another fictional grievance with a small image or PDF; open the record and download the attachment.
4. Use public tracking with the sample reference `KER-2026-A8K4Q2`; confirm status, department and history appear without exposing the citizen account fields.
5. Sign in as CMO admin; filter the inbox by status and district, open the new record, assign it to a department and add a review note.
6. Sign in as the assigned department officer; record an investigation update and an action-taken report. Selecting Resolved should submit the report for CMO review while the status remains In Progress.
7. Sign in as CMO admin; review the action report and record a citizen-facing resolution. Confirm the resolution and status history are visible to the citizen.
8. Sign in as the citizen; add feedback or request reopening on a resolved case.
9. Submit a contact enquiry and confirm a success message is shown.
10. Configure a valid model key and submit a fictional description; confirm returned suggestions carry a human-review label. Without a key, confirm the local fallback is explicitly identified.

## Troubleshooting

- **Port already in use:** set `PORT` to another local port before starting the server.
- **Stale browser token after restart:** sign out, clear the app’s local storage token, then sign in again; sessions are intentionally memory-only.
- **AI fallback appears:** confirm that `OPENAI_API_KEY` is available to the server process and that the model is enabled for the key. The fallback remains clearly labeled.
- **Reset demo records:** stop the server, remove `data/store.json` and files in `data/uploads/`, then start again.
