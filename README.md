# Web-Based Interactive Model Visualizer

**Team AVJS** · CS 5001 / 5002 Senior Design · University of Cincinnati

> **Goal:** Create a web-based model visualizer that allows users to quickly change layers of the model as well as create models based on images uploaded via AI.

This project builds an AI-assisted WebGL tool for viewing and exploring volumetric data in the browser. A user can upload an image, have it turned into a 3D model, save it to their account, and then rotate, zoom, and switch between layers of the model. Example uses include brain cross-sections for medical students, rock and sediment layers for geologists, powertrain parts for engineers, and building floors for emergency responders.

---

## Team

| Member | GitHub | Component owner |
|---|---|---|
| Abby Kraft | [@abkatchill](https://github.com/abkatchill) | Image Upload |
| Veronica Malusky | [@maluskvp](https://github.com/maluskvp) | Login / Sign-up |
| Jake Martin | [@jakeMartin614](https://github.com/jakeMartin614) | 3D Model Switcher |
| Sophia Munoz | [@munozsophia](https://github.com/munozsophia) | 3D Model Viewer |

**Advisor:** Professor Ming Tang

Full contact details and bios are in [Project_Description.md](Project_Description.md).

---

## How it works

```mermaid
flowchart LR
    U([User]) -->|email + password| L[Login / Sign-up]
    L -->|session token| S
    U -->|PNG/JPEG ≤ 20 MB| UP[Image Upload]
    UP -->|image| AI[[AI 3D Model API<br/>external]]
    AI -->|GLB| UP
    UP -->|validated GLB| DB[(PostgreSQL +<br/>model file storage)]
    DB -->|model list, 5 per page| S[3D Model Switcher]
    S -->|modelId + glbUrl + visible layers| V[3D Model Viewer]
    DB -.->|GLB file| V
    V -->|interactive model or error| U
```

| Component | Responsibility |
|---|---|
| **Login / Sign-up** | Authenticates the user and issues a session token so models are saved to their own account. |
| **Image Upload** | Validates an uploaded image, sends it to the AI service, and checks the returned 3D model (GLB) before saving it. |
| **3D Model Switcher** | Shows the user's saved models a page at a time and lets them pick one and choose which layers are visible. |
| **3D Model Viewer** | Renders the selected model in the browser so the user can rotate, zoom, and switch layers, and shows a clear error if it can't. |

Architecture: **client-server** for login, storage, and the switcher; a **pipeline** for image → AI → validated GLB; and a **layered**, client-side design for the viewer. The reasoning is in the design documents below.

Every component now has a data model, defined behavior, an interface with error codes, and documented edge cases (D2).

---

## Repository contents

```
.
├── README.md                  ← you are here
├── Project_Description.md     Team members and project topic
├── Constraints_Essay.md       Economic, social, and professional constraints
├── User_Stories.md            Stakeholder map, user stories US-01 to US-05, use case UC-01
├── bios/                      Team member professional bios
└── Design_Diagrams/
    ├── D0_High_Level_Design.pdf    Components, interfaces, data flow, architecture patterns
    ├── D1_Detailed_Design.pdf      Data model, core algorithms, build vs. reuse, API, tech choices
    ├── D2_Detailed_Design.pdf      Remaining components, edge cases, traceability, rationale
    └── Peer_Review_Notes.md        Peer design review feedback and action items
```

### Design documents

| Document | Week | Status |
|---|---|---|
| [D0 – High-Level Design](Design_Diagrams/D0_High_Level_Design.pdf) | 5 | ✅ Submitted |
| [D1 – Detailed Design, Part 1](Design_Diagrams/D1_Detailed_Design.pdf) | 6 | ✅ Submitted |
| [D2 – Detailed Design, Part 2](Design_Diagrams/D2_Detailed_Design.pdf) | 7 | ✅ Submitted |
| [Peer Design Review Notes](Design_Diagrams/Peer_Review_Notes.md) | 7 | ✅ Submitted |

Together with the user stories, D0–D2 make up the Assignment #4 design package. The design phase is now closed; next, each component becomes a task in the project plan.

---

## Tech stack

Chosen and justified in D1.

| Layer | Choice |
|---|---|
| Front end | React with Ant Design (upload UI) |
| 3D rendering | WebGL in the browser (client-side) |
| Model format | GLB (binary glTF) |
| Database | PostgreSQL (users, models, layers, generation jobs) |
| File storage | GLB files stored separately; the database keeps their URLs |
| Authentication | Hashed passwords and session tokens |
| 3D generation | Hosted AI image-to-3D API |

---

## Open questions

Carried forward from D2 and the peer review:

- How a single uploaded image produces the separate layers the viewer displays.
- Which AI image-to-3D service to use, and a backup plan if its results are poor.
- Prompt-based model generation (out of scope for now).

---

## Course milestones

- [x] Project selection and constraints essay
- [x] User stories and use cases
- [x] D0 – High-level architecture
- [x] D1 – Detailed design, part 1
- [x] D2 – Detailed design, part 2
- [x] Peer Design Review
- [ ] Project plan and task assignments
- [ ] Technical specification
- [ ] Test plan
- [ ] Oral presentations