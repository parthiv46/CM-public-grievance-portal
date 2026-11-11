# API documentation

All endpoints are same-origin. JSON request bodies use `Content-Type: application/json`; authenticated calls use `Authorization: Bearer <token>`. Error responses have the shape `{ "message": "..." }`.

| Method | Path | Access | Purpose |
|---|---|---|---|
| GET | `/api/health` | Public | Health check |
| GET | `/api/departments` | Public | List departments |
| GET | `/api/stats` | CMO admin | Retrieve total, pending, in-progress, resolved, overdue, district, department and status statistics |
| POST | `/api/register` | Public | Register citizen; returns user and token |
| POST | `/api/login` | Public | Sign in; returns user and token |
| POST | `/api/logout` | Signed in | Revoke in-memory session |
| GET | `/api/me` | Signed in | Current user and department |
| POST | `/api/analyze` | Citizen | Analyze a grievance description |
| GET | `/api/track/:reference` | Public | Public status and history lookup |
| GET | `/api/grievances` | Signed in | Role-filtered grievance list |
| POST | `/api/grievances` | Citizen | Submit grievance and optional attachment |
| GET | `/api/grievances/:id` | Authorized owner/role | Retrieve grievance details |
| POST | `/api/grievances/:id/assign` | CMO admin | Assign department and record review |
| POST | `/api/grievances/:id/escalate` | CMO admin | Escalate an open grievance and record follow-up |
| POST | `/api/grievances/:id/status` | Assigned officer or admin | Record status/investigation update |
| POST | `/api/grievances/:id/review` | CMO admin | Review action-taken report and outcome |
| POST | `/api/grievances/:id/feedback` | Owning citizen | Submit feedback or request reopening |
| GET | `/api/grievances/:id/file` | Authorized owner/role | Retrieve attachment bytes |
| POST | `/api/enquiries` | Public | Save a contact enquiry |

## Example: submit a grievance

```json
{
  "district": "Ernakulam",
  "category": "Roads & Transport",
  "subject": "Road needs repair near the clinic",
  "description": "A fictional example describing the location and impact of a road issue.",
  "priority": "Medium",
  "attachment": null,
  "ai": null
}
```

The API returns the new record, including a generated `reference`, `status: "Submitted"`, and a first history entry. File attachment bodies use `{ "name", "type", "data" }`, where `data` is a `data:` URL containing base64 bytes. Files are limited to 4 MB.

## Grievance statuses

`Submitted` → `Under Review` → `In Progress` → CMO-reviewed `Resolved` (or `Reopened`). When an officer selects `Resolved`, the server records the action-taken report and keeps the public status `In Progress` until the CMO reviews it.
