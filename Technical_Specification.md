<div align="center">

# Technical Specification Document

**Team AVJS** · Web-Based Interactive Model Visualizer

Abby Kraft · Veronica Malusky · Jake Martin · Sophia Munoz

*How the system will be built.*

**Version 0.1** · Traces to [Requirements Document](Requirements_Document.md) **v0.1** · Last updated 2026-10-27

</div>

Terms like **Model**, **Layer**, and **Session Token** are defined once in the [Requirements Document Glossary](Requirements_Document.md#-glossary).

---

# 📝 Version and Change Log 

| Version | Date | Change | Author |
|---|---|---|---|
| 0.1 | YYYY-MM-DD | Initial draft | |

---

## 🏗️ Architecture Summary
an architecture summary of about half a page naming the major components, the deployment model, and how data flows between them, referencing your D0 diagram
For every component boundary: inputs, outputs, formats (JSON schema, database schema, message payloads), protocol, and error behavior.

See the block diagram in [D0 - High-Level Design](Design_Diagrams/D0_High_Level_Design.pdf).

**Major Components:**

| Component | Responsibility | Owner |
|---|---|---|
| Login / Sign-up | | Veronica Malusky |
| Image Upload | | Abby Kraft |
| 3D Model Switcher | | Jake Martin |
| 3D Model Viewer | | Sophia Munoz |
| AI 3D Model API *(external)* | | — |

**Deployment Model:**

**Data Flow:**

---

## 🔌 Interfaces and Data Formats

<!-- Repeat this block for every component boundary. Formats: JSON schema, database schema, or message payload. -->

### Boundary: _Component A -> Component B_

| | |
|---|---|
| **Inputs** | |
| **Outputs** | |
| **Format** | |
| **Protocol** | |
| **Error Behavior** | |

```json
{
  "example": "payload"
}
```

### API Endpoints

<!-- Required for any API. One row per endpoint. -->
 
| Method | Endpoint | Purpose | Request | Success response | Errors |
|---|---|---|---|---|---|
| POST | `/api/v1/...` | | | | |
| GET | `/api/v1/...` | | | |

### Database Schema
 
<!-- From the D1 data model. Link the diagram; list tables and key fields. -->
 
See the data model in [D1 – Detailed Design](Design_Diagrams/D1_Detailed_Design.pdf).
 
| Table | Key fields | Notes |
|---|---|---|
| User | | |
| Upload | | |
| GenerationJob | | |
| Model | | |
| Layer | | |
 
---

## 🧰 The Technology Stack
An endpoint table is required for any API.
Then a stack table: layer, choice, and a justification tied to a REQ, NFR, or CON ID. Restate each CON-## item with what it rules out or forces.
 
| Layer | Choice | Justification |
|---|---|---|
| Front End | | |
| 3D Rendering | | |
| Back End | | |
| Database | | |
| File Storage | | |
| Authentication | | |
| 3D Generation | | |
| Hosting | | |
 
---

## 🔒 Known Constraints
Restate each CON-## item with what it rules out or forces.
 
| ID | Constraint | What it rules out or forces |
|---|---|---|
| CON-01 | | |
| CON-02 | | |
| CON-03 | | |
 
---

## 🔗 The Traceability Matrix
 Use exactly these columns, one row per REQ, NFR, and CON ID. Status is Designed, In progress, or Planned, and the test case is the Week 10 test ID where you know it.


| ID | Description | Design Component (D0/D1/D2) | Status | Test Case |
| ------ | ----------- | --------------------------- | ------ | --------- |
| REQ-01 | ...         | ...                         |  | TC-0  |
| REQ-02 | | | | |
| REQ-03 | | | | |
| REQ-04 | | | | |
| REQ-05 | | | | |
| REQ-06 | | | | |
| NFR-01 | | | | |
| NFR-02 | | | | |
| NFR-03 | | | | |
| NFR-04 | | | | |
| CON-01 | | | | |

**Orphaned Rows:** None.

<!-- If any row has no design component, write ORPHANED in its Design Component cell and explain here whether it is dropped (log it as Withdrawn), deferred, or needs a design decision. -->

---

## 🧑‍🏫 Record of Advisor Review

| | |
|---|---|
| **Date** | |
| **Advisor** | Professor Ming Tang |
| **Attendance** | |
| **Discussion** | |
| **Sign-off** | <!-- e.g. "Approved v1.0 on YYYY-MM-DD" or "Pending" --> |

**Changes it produced:**

| Change | Document and section | Logged in version |
|---|---|---|
| | | |
 
---

<sub>Related: [Requirements Document](Requirements_Document.md) · [D0](Design_Diagrams/D0_High_Level_Design.pdf) · [D1](Design_Diagrams/D1_Detailed_Design.pdf) · [D2](Design_Diagrams/D2_Detailed_Design.pdf)</sub>