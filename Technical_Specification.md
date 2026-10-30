<div align="center">

# Technical Specification Document

**Team AVJS** · Web-Based Interactive Model Visualizer

Abby Kraft · Veronica Malusky · Jake Martin · Sophia Munoz

*How the system will be built.*

**Version 0.2** · Traces to [Requirements Document](Requirements_Document.md) **v0.1** · Last updated 2026-10-29

</div>

Terms like **Model**, **Layer**, and **Session Token** are defined once in the [Requirements Document Glossary](Requirements_Document.md#-glossary).

---

## 📝 Version and Change Log

| Version | Date | Change | Author |
|---|---|---|---|
| 0.1 | 2026-10-28 | Initial draft | Team |

---

## 🏗️ Architecture Summary

### System Overview

The Web-Based Interactive Model Visualizer is designed to allow users to upload images and generate 3D models that can be saved and viewed through the browser 3D viewer. The system is broken down into individual components that handle user interaction, image processing, storage, and 3D visualization.

See the block diagram in [D0 – High-Level Design](Design_Diagrams/D0_High_Level_Design.pdf).

**Major Components:**

| Component | Responsibility | Owner |
|---|---|---|
| Login / Sign-up | Handles account registration, login, and authentication | Veronica Malusky |
| Image Upload | Receives image files, checks for valid file format and size, and sends the image to the AI 3D Model API | Abby Kraft |
| 3D Model Switcher | Displays the user's saved models and lets the user select a single model and send it to the viewer | Jake Martin |
| 3D Model Viewer | Loads the selected model and renders it in the browser with WebGL, handles camera controls and layer visibility, and shows errors instead of a blank canvas | Sophia Munoz |
| AI 3D Model API *(external)* | Generates a 3D model from an image and returns it to the system to be saved | — |

**Deployment Model:** The application is browser based. The UI and the 3D Model Viewer run in the user's browser, and the server-side authentication, processing, and storage run in the hosting environment.

**Data Flow:**
1. User opens the web interface.
1. User logs in and receives a session token.
1. User submits an image to the Image Upload component.
1. The system validates the image and sends it to the AI 3D Model API.
1. The model is generated as a GLB mesh (or a volumetric dataset, see Q-02 to Q-04).
1. The GLB file is saved to file storage, and its metadata and layers are saved in the database.
1. The system returns the user's available models to the 3D Model Switcher.
1. The user selects a model, and the 3D Model Switcher passes its modelId, glbUrl, and visible layers to the 3D Model Viewer, which downloads the GLB directly from file storage and renders it in the browser. Rotating or switching layers needs no server request.

---

## 🔌 Interfaces and Data Formats

Each component boundary below lists its inputs, outputs, format, protocol, and error behavior, with an example payload. All HTTP endpoints are collected in the [API Endpoints](#api-endpoints) table after the boundaries.

---

### Boundary: _Browser -> Registration_

| | |
|---|---|
| **Inputs** | `Email` (string, valid email, max 254 characters), `Password` (string, 8–72 characters), `FirstName` (string), `LastName` (string), `IsSignup` (bit, 1 for sign-up) |
| **Outputs** | Registration result: `{userId: int, firstName: string, lastName: string}` |
| **Format** | JSON |
| **Protocol** | HTTPS POST to `/api/v1/authenticate` |
| **Error Behavior** | Rejects the request if the email already exists in the database (`EMAIL_ALREADY_TAKEN`); invalid or missing fields return `INVALID_INPUT` |

```json
{
  "Email": "example@mail.uc.edu",
  "Password": "Password1",
  "FirstName": "Test",
  "LastName": "Example",
  "IsSignup": 1
}
```

---

### Boundary: _Browser -> Authentication_

| | |
|---|---|
| **Inputs** | `Email` (string), `Password` (string), `IsSignup` (bit, 0 for login) |
| **Outputs** | Session result: `sessionToken` (string) and `user: {userId: int, firstName: string, lastName: string}` |
| **Format** | JSON |
| **Protocol** | HTTPS POST to `/api/v1/authenticate` |
| **Error Behavior** | Rejects the login attempt if the credentials are incorrect (`INVALID_CREDENTIALS`); after 5 failed attempts in 15 minutes the account is locked for 15 minutes (`TOO_MANY_ATTEMPTS`) |

```json
{
  "Email": "example@mail.uc.edu",
  "Password": "Password1",
  "FirstName": "",
  "LastName": "",
  "IsSignup": 0
}
```

---

### Boundary: _Authentication -> 3D Model Switcher_

| | |
|---|---|
| **Inputs** | Session result: `sessionToken` (string) and `user: {userId: int, firstName: string, lastName: string}` |
| **Outputs** | One page of the user's models (5 per page), newest first, each with its layers |
| **Format** | JSON |
| **Protocol** | HTTPS GET to `/api/v1/authenticate` |
| **Error Behavior** | Invalid or expired token returns 401 and the user is sent back to login. A model that doesn't belong to the user returns 404, the same as a missing model (EC-S-05). If the list can't be loaded, the Switcher shows an error banner with Retry (EC-S-06). |

```json
{
  "modelId": 2,
  "name": "Model Example",
  "glbUrl": "/api/v1/models/model_2",
  "vertices": 45218,
  "triangles": 87342,
  "layers": [
    { "layerId": 101, "name": "Model Example 1", "visible": true },
    { "layerId": 102, "name": "Model Example 2", "visible": true }
  ]
}
```

---

### Boundary: _Browser Client Upload Form -> Image Upload Controller_

| | |
|---|---|
| **Inputs** | `file` (binary image, PNG or JPEG, ≤ 20 MB), `name` (string, 1–255 characters), `resolution` (integer, 256–4096, optional), `Authorization` (HTTP Bearer header with sessionToken) |
| **Outputs** | JSON model payload containing metadata, geometry counts, and the asset URL |
| **Format** | Request: multipart/form-data. Response: application/json |
| **Protocol** | HTTPS POST |
| **Error Behavior** | The client blocks invalid extensions and files over 20 MB. The server validates input and returns standard HTTP status codes with structured error messages (400, 401, 413, 415, 422, 500). |

```json
{
  "ModelID": 2,
  "Name": "Model Example",
  "Format": "glb",
  "URL": "/api/v1/models/model_2",
  "Vertices": 45218,
  "Triangles": 87342,
  "Generated": 1,
  "Error": ""
}
```

---

### Boundary: _AiClient -> External AI 3D Model API_

| | |
|---|---|
| **Inputs** | Normalized binary image stream or buffer (image/png or image/jpeg), prompt parameters |
| **Outputs** | Binary GLB stream (model/gltf-binary) |
| **Format** | Binary payload over a REST API |
| **Protocol** | HTTPS POST |
| **Error Behavior** | Handled by AiClient. A 60-second timeout triggers an automatic retry. Rate limits (HTTP 429) trigger exponential backoff retries after 2, 4, and 8 seconds, up to three times, before setting the job status to FAILED and returning error code `AI_TIMEOUT` or `AI_RATE_LIMIT`. |

Example of the GenerationJob record AiClient writes when generation fails:

```json
{
  "jobId": 17,
  "uploadId": 9,
  "status": "FAILED",
  "errorCode": "AI_RATE_LIMIT",
  "attempt": 3
}
```

---

### Boundary: _Image Upload Backend -> 3D Model Switcher_

| | |
|---|---|
| **Inputs** | Completed Model records (`model_id`, `name`, `glb_url`, `vertices`, `triangles`) and their Layer rows; request includes the sessionToken and a page number |
| **Outputs** | One page of the user's models (5 per page), newest first, each with its layers |
| **Format** | JSON |
| **Protocol** | HTTPS GET |
| **Error Behavior** | Invalid or expired token returns 401 and the user is sent back to login. A model that doesn't belong to the user returns 404, the same as a missing model (EC-S-05). If the list can't be loaded, the Switcher shows an error banner with Retry (EC-S-06). |

```json
{
  "modelId": 2,
  "name": "Model Example",
  "glbUrl": "/api/v1/models/model_2",
  "vertices": 45218,
  "triangles": 87342,
  "layers": [
    { "layerId": 101, "name": "Model Example 1", "visible": true },
    { "layerId": 102, "name": "Model Example 2", "visible": true }
  ]
}
```

---

### Boundary: _3D Model Switcher -> 3D Model Viewer_

| | |
|---|---|
| **Inputs** | `modelId` (int, required, ≥ 1), `glbUrl` (string, required, `/api/v1/models/model_[ID]`), `layerState` (map of layerId → visible boolean, optional; defaults to each layer's `default_visible`) |
| **Outputs** | `LoadResult`: `modelId` (int), `vertices` (int), `triangles` (int), `layerIds` (int[]), `loadMs` (int, milliseconds) |
| **Format** | JavaScript method call `loadModel(modelId, glbUrl, layerState)`. The GLB file is downloaded as binary glTF |
| **Protocol** | In-browser function call. GLB downloaded over HTTPS |
| **Error Behavior** | Returns `{code, message, recoverable}`: `UNSUPPORTED_BROWSER` (no WebGL), `LOAD_TIMEOUT` (over 15 s), `LOAD_FAILED` (network error, 403, or 404), `PARSE_FAILED` (corrupt GLB), `MODEL_TOO_LARGE` (over 50 MB or 2,000,000 triangles), `CANCELLED` (a newer selection replaced it. Ignored by the Switcher) |

```json
{
  "request": { "modelId": 2, "glbUrl": "/api/v1/models/model_2", "layerState": { "101": true, "102": false } },
  "success": { "modelId": 2, "vertices": 45218, "triangles": 87342, "layerIds": [101, 102], "loadMs": 1840 },
  "error":   { "code": "LOAD_TIMEOUT", "message": "The model took too long to load.", "recoverable": true }
}
```

---

### Boundary: _User <-> 3D Model Viewer_

| | |
|---|---|
| **Inputs** | Mouse, touch, and keyboard events (rotate, zoom, pan, reset); layer toggles through `setLayerVisibility(layerId: int, visible: boolean)` |
| **Outputs** | Rendered model on the WebGL canvas; text description from `getDescription()` for screen readers |
| **Format** | Browser input events in; rendered frames and an ARIA live-region text description out |
| **Protocol** | Browser event handlers (no network) |
| **Error Behavior** | `NO_MODEL_LOADED` if controls are used before a model is ready; an unknown `layerId` is ignored and logged; if WebGL is unavailable, only the text description is shown |

```json
{
  "description": "Model Example. Visible layers: Model Example 1. 45,218 vertices."
}
```

---

### API Endpoints

| Method | Endpoint | Purpose | Request | Success response | Errors |
|---|---|---|---|---|---|
| POST | `/api/v1/authenticate` | Register a user account (`IsSignup: 1`) | JSON registration payload | `{userId, firstName, lastName}` | 400 `INVALID_INPUT`; 409 `EMAIL_ALREADY_TAKEN` (email already used) |
| POST | `/api/v1/authenticate` | Log in (`IsSignup: 0`) | JSON login payload | `{sessionToken, user: {userId, firstName, lastName}}` | 400 `INVALID_INPUT`; 401 `INVALID_CREDENTIALS` (incorrect email or password); 429 `TOO_MANY_ATTEMPTS`; 503 |
| POST | `/api/v1/upload` | Upload an image and generate a model | multipart/form-data: `file`, `name`, `resolution` (optional); Bearer sessionToken | Model JSON (see Image Upload boundary) | 400, 401, 413, 415, 422, 500 |
| GET | `/api/v1/models?page=N` | List the user's saved models, 5 per page | Bearer sessionToken | `{models: [{modelId, name, glbUrl, vertices, triangles}], page}` | 401, 503 |
| GET | `/api/v1/models/{modelId}/layers` | Get a model's layers | Bearer sessionToken | `[{layerId, name, defaultVisible}]` | 401, 404 (not found or not the user's) |
| GET | `/api/v1/models/model_[ID]` | Download the model's GLB file | Bearer sessionToken | GLB binary | 401, 404 |

### 3D Model Viewer Methods

The 3D Model Viewer has no HTTP endpoints. These are the methods the 3D Model Switcher calls in the browser.

| Method | Inputs | Output | Errors |
|---|---|---|---|
| `loadModel` | `modelId`, `glbUrl`, `layerState` | `Promise<LoadResult>` | See the 3D Model Switcher → 3D Model Viewer boundary |
| `setLayerVisibility` | `layerId` (int), `visible` (boolean) | void | `NO_MODEL_LOADED` |
| `resetCamera` | none | void | `NO_MODEL_LOADED` |
| `getDescription` | none | string | none |
| `dispose` | none | void (frees GPU memory) | none |

### Database Schema

See the data model in [D1 – Detailed Design](Design_Diagrams/D1_Detailed_Design.pdf).

| Table | Key fields | Notes |
|---|---|---|
| User | `user_id` (PK, int), `email` (varchar(255), unique, indexed), `password_hash` (varchar(255)), `created_at` (timestamp) | Unique index on email prevents duplicate accounts when two sign-ups happen at once (EC-L-02) |
| Upload | `upload_id` (PK, int), `user_id` (FK -> User.user_id), `image_hash` (varchar(64), indexed), `size_bytes` (bigint), `uploaded_at` (timestamp) | Stores metadata for every upload attempt; the image_hash index avoids paying for a new AI generation when the same image is re-uploaded |
| GenerationJob | `job_id` (PK, int), `upload_id` (FK -> Upload.upload_id), `status` (enum, indexed), `error_code` (varchar(64)), `attempt` (int), `started_at` (timestamp), `finished_at` (timestamp) | Supports multiple retry attempts per upload; the status index supports scanning for timed-out jobs |
| Model | `model_id` (PK, int), `user_id` (FK -> User.user_id, indexed), `job_id` (FK → GenerationJob.job_id), `name` (varchar(255)), `glb_url` (text), `vertices` (int), `triangles` (int), `created_at` (timestamp) | Stores user-owned models. The GLB binary is kept in file storage and referenced by glb_url. Triangle and vertex counts let the 3D Model Viewer check size before downloading (EC-V-01) |
| Layer | `layer_id` (PK, int), `model_id` (FK -> Model.model_id, indexed), `name` (varchar(255)), `node_index` (int), `default_visible` (boolean) | One row per switchable part of a model; the model_id index loads all of a model's layers in one lookup |

---

## 🧰 The Technology Stack

| Layer | Choice | Justification |
|---|---|---|
| Front End | React with Ant Design (MIT) | Free and open source (CON-01). Ant Design's Upload component checks the file type and size in the browser before sending (REQ-03, NFR-02) |
| 3D Rendering | WebGL through an open-source rendering library and GLB loader, chosen in task T16 (candidate: three.js, MIT) | Free (CON-01); client-side rendering needs no server round trip (NFR-05); a GLB loads in a single request (NFR-04) |
| Back End | | |
| Database | PostgreSQL | Free and open source (CON-01); the relational model enforces one user per email and each model's owner (REQ-02, REQ-04, NFR-03) |
| File Storage | GLB files stored separately from the database; the database keeps their URLs | Keeps queries fast for the model list (NFR-01) |
| Authentication | Salted password hashing and session tokens | Required by NFR-03 |
| 3D Generation | Hosted AI image-to-3D API, chosen in task T1 | Generating per request costs less than running our own GPUs (CON-01); the provider is swappable behind AiClient (Q-01) |
| Hosting | | |

---

## 🔒 Known Constraints

| ID | Constraint | What it rules out or forces |
|---|---|---|
| CON-01 | No budget for paid hardware or software. Limited to UC labs, personal laptops and phones, and free tools | Rules out paid hardware, paid software, and commercial licenses. Forces free, open-source libraries and a pay-per-request AI service instead of our own GPU servers. |
| CON-02 | Team is all Computer Science majors and needs guidance on model rendering and automation | Forces research and test builds before implementation (tasks T1 and T16) instead of building directly. |
| CON-03 | The project must comply with applicable property rights, software licensing requirements, and data privacy rules | Forces the team to check each library's license, follow the AI service's terms of use, and keep each user's images and models private to that user. |

---

## 🔗 The Traceability Matrix

| ID | Description | Design Component (D0/D1/D2) | Status | Test Case |
| ------ | ----------- | --------------------------- | ------ | --------- |
| REQ-01 | Log in before using the app | D2 Login/Sign-up state machine; `POST /api/v1/authenticate` | Designed | TBD |
| REQ-02 | Sign up with a new account | D2 Login/Sign-up; User table (unique email) | Designed | TBD |
| REQ-03 | Upload an image and generate a model | D1 Algorithm 1 (generation pipeline), Algorithm 2 (GLB validation) | Designed | TBD |
| REQ-04 | Store each user's models for the switcher | D1 Model table; D2 Switcher model list | Designed | TBD |
| REQ-05 | Select a stored model and display it in WebGL | D0 Switcher -> Viewer interface; D2 `loadModel` | Designed | TBD |
| REQ-06 | View layers and take slices of a volumetric model | D1 Layer table (layers only) | Planned | TBD |
| REQ-07 | Import an existing GLB file | ORPHANED | Planned | TBD |
| REQ-08 | Camera controls by mouse, touch, and keyboard | D2 3D Model Viewer behavior (render view) | Designed | TBD |
| REQ-09 | Show or hide a layer without reloading | D1 Layer table; D2 `setLayerVisibility`, Viewer data model | Designed | TBD |
| REQ-10 | Error message with Retry or Back | D2 Viewer state machine (Error, Unsupported states); EC-V-01, EC-V-03, EC-V-06, EC-V-08 | Designed | TBD |
| REQ-11 | Screen-reader description and keyboard-only use | D2 `getDescription`; EC-V-08 | Designed | TBD |
| NFR-01 | Model list within 2 s, 5 per page | D2 Switcher paging (design rationale 4); `Model.user_id` index | Designed | TBD |
| NFR-02 | App usable during generation | D1 GenerationJob; D2 EC-U-04 | Designed | TBD |
| NFR-03 | Hashed passwords, session tokens, lockout | D2 Login/Sign-up; EC-L-01, EC-L-04 | Designed | TBD |
| NFR-04 | Model displayed within 15 s | D2 `loadModel` timeout; EC-V-03 | Designed | TBD |
| NFR-05 | 30+ fps up to 500,000 triangles | D2 Viewer complexity; EC-V-01, EC-V-05 | Designed | TBD |
| NFR-06 | First-time user toggles a layer within 30 s | D2 Switcher layer toggles; Viewer `setLayerVisibility` | Designed | TBD |
| NFR-07 | No blank canvas; context loss recovered once | D2 Viewer state machine; EC-V-06, EC-V-09 | Designed | TBD |
| CON-01 | No budget | Free, open-source stack (see Technology Stack) | Designed | — |
| CON-02 | Team needs guidance on rendering | Tasks T1 and T16 research and test builds | Planned | — |
| CON-03 | Licensing and privacy rules | MIT-licensed libraries; models private to their owner (EC-S-05) | Designed | — |

**Orphaned Rows:**

- **REQ-07 (import a GLB):** no design component yet. The upload path in D1 only accepts PNG and JPEG images. This needs a design decision: either extend Image Upload to accept GLB files (validated by Algorithm 2) or move REQ-07 to Won't.
- **REQ-06 (slicing):** only partly designed. Layers exist in D1, but no design covers slicing a volume. Status is Planned until Q-02 to Q-04 are answered.

---

## 🧑‍🏫 Record of Advisor Review

| | |
|---|---|
| **Date** |2026-10-29 |
| **Advisor** | Professor Ming Tang |
| **Attendance** |Entire Team |
| **Discussion** |About overall design and ideas to implement, making it different from applications already out there and utilizing technology that the professor can provide us. |
| **Sign-off** | Pending <!-- e.g. "Approved v1.0 on YYYY-MM-DD" or "Pending" --> |

**Changes it produced:**

| Change | Document and Section | Logged in Version |
|---|---|---|
| | | |

---

<sub>Related: [Requirements Document](Requirements_Document.md) · [D0](Design_Diagrams/D0_High_Level_Design.pdf) · [D1](Design_Diagrams/D1_Detailed_Design.pdf) · [D2](Design_Diagrams/D2_Detailed_Design.pdf)</sub>