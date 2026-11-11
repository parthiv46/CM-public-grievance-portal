# Database design

The application uses MongoDB collections through the official Node.js driver. Seed accounts and sample grievances are fictional. The diagram describes the collection relationships; each collection stores documents with a stable string `id`.

```mermaid
erDiagram
  USERS ||--o{ GRIEVANCES : submits
  DEPARTMENTS ||--o{ USERS : includes
  DEPARTMENTS ||--o{ ASSIGNMENTS : receives
  GRIEVANCES ||--o{ ASSIGNMENTS : routes
  GRIEVANCES ||--o{ GRIEVANCE_HISTORY : records
  GRIEVANCES ||--o{ ESCALATIONS : escalates
  GRIEVANCES ||--o{ ATTACHMENTS : includes
  GRIEVANCES ||--o{ CITIZEN_FEEDBACK : receives
  USERS ||--o{ CONTACT_ENQUIRIES : sends
  USERS { string id PK string name string email UK string mobile string role string departmentId FK string passwordHash }
  DEPARTMENTS { string id PK string name string description }
  GRIEVANCES { string id PK string reference UK string citizenId FK string departmentId FK string district string category string subject string description string priority string status datetime createdAt datetime updatedAt }
  GRIEVANCE_HISTORY { string status string note string actorId string actorName string actorRole datetime createdAt }
  ASSIGNMENTS { string id PK string grievanceId FK string departmentId FK string assignedBy datetime assignedAt string note }
  ESCALATIONS { string id PK string grievanceId FK string escalatedBy datetime createdAt string note }
  ATTACHMENTS { string id PK string grievanceId FK string name string type string dataBase64 }
  CITIZEN_FEEDBACK { string message string citizenName datetime createdAt }
  CONTACT_ENQUIRIES { string id PK string name string email string subject string message datetime createdAt }
```

Grievance documents retain a snapshot of status history for convenient reads; the canonical events are also stored in `grievanceHistory`. A grievance stores attachment metadata and references a separate `attachments` document containing the base64 file bytes (maximum 4 MB upload, within MongoDB's BSON document limit). Status timestamps use ISO 8601 strings. Unique indexes protect account emails and grievance references; additional indexes support ownership, department and history lookups.

The demo currently keeps a single in-memory view of records and flushes changes to MongoDB. Run one app instance to avoid stale in-memory data. A production version should use per-request database operations, durable sessions, GridFS or object storage for larger uploads, and backup/retention policies.
