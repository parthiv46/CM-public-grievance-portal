# Database design

The demo uses a JSON document store so it runs without database installation. The equivalent relational entities and relationships are shown below. Seed accounts and sample grievances are fictional.

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
  ATTACHMENTS { string name string type string file }
  CITIZEN_FEEDBACK { string message string citizenName datetime createdAt }
  CONTACT_ENQUIRIES { string id PK string name string email string subject string message datetime createdAt }
```

Each grievance embeds its history, optional attachment metadata, action-taken report, CMO response and citizen feedback. `data/store.json` contains top-level arrays for users, departments, grievances and enquiries. Upload bytes are separate files in `data/uploads/`; file names are random server-generated identifiers. The application stores timestamps as ISO 8601 strings.

For a multi-instance or production deployment, move records and file metadata to a managed database and object store, add indexes for references/ownership/status, and define retention and backup policies.
