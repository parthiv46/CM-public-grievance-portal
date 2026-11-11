# Setup and manual verification guide

## Requirements

- Node.js 18 or newer.
- A MongoDB Atlas or self-hosted MongoDB connection URI.
- npm. The project uses the official MongoDB Node.js driver.

## Setup

1. Create a MongoDB database and database user. Copy the connection URI from MongoDB Atlas or your MongoDB provider.
2. Add `MONGODB_URI` and `MONGODB_DB=cm_kerala_portal` to `.env`. Preserve any existing Gemini key in that file. Keep `.env` private; it is ignored by Git.
3. In Windows PowerShell, run `npm.cmd install` and `npm.cmd start`. In shells where npm scripts are enabled, run `npm install` and `npm start`.
4. Visit `http://localhost:3000` and sign in with one of the demo accounts listed in the root README.
5. On an empty database, startup creates the MongoDB collections, indexes and fictional sample records.

## Manual verification cases

These are suggested evaluation steps; they are not a claim that automated tests have been run.

1. Browse Home, About, How it works, Departments, Contact and FAQ at desktop and mobile widths.
2. Sign in as citizen, submit a fictional grievance without an attachment, and confirm that a reference and initial history entry appear.
3. Submit another fictional grievance with a small image or PDF; open the record and download the attachment.
4. Use public tracking with the sample reference `KER-2026-A8K4Q2`; confirm status, department and history appear without exposing citizen account fields.
5. Sign in as CMO admin; filter the inbox by status, district, category and priority. Assign a case and add a review note.
6. Call `GET /api/stats` with the CMO bearer token and confirm totals, overdue count and district/department/status breakdowns are returned.
7. Sign in as the assigned department officer; record an investigation update and action-taken report. Selecting Resolved submits the report to the CMO while the public status stays In Progress.
8. Sign in as CMO admin; review the action report and record a citizen-facing resolution. Confirm the resolution and status history are visible to the citizen.
9. Sign in as the citizen; add feedback or request reopening on a resolved case.
10. Submit a contact enquiry and confirm a success message is shown.
11. Configure a valid Gemini key and submit a fictional description; confirm suggestions carry a human-review label. Without a key, confirm the local fallback is explicitly identified.

## Troubleshooting

- **MongoDB connection fails:** check the Atlas network access list, database user credentials and `MONGODB_URI` in `.env`.
- **Stale browser token after restart:** sign out, clear the app's local storage token, then sign in again. Sessions are intentionally memory-only.
- **AI fallback appears:** check that `GEMINI_API_KEY` is available to the server and the selected model is enabled for that key.
- **Reset demo records:** remove the app collections from MongoDB. The server seeds sample records again when the database is empty.
