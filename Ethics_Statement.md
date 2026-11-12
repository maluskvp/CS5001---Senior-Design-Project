<div align="center">

# Ethics and Responsible-Use Statement

**Team AVJS** · Web-Based Interactive Model Visualizer

Abby Kraft · Veronica Malusky · Jake Martin · Sophia Munoz

*How our project earns that trust*

**Draft 0.1** · Traces to [Requirements Document](Requirements_Document.md) **v0.1**,  [Technical Specification](Technical_Specification.md) **v0.1**, and [Test Plan](Test_Plan.md) **v0.1** · Last updated 2026-11-06
</div>

---

# 📝 Version and Change Log 

| Version | Date | Change | Author |
|---|---|---|---|
| 0.1 | 2026-11-06 | Initial draft | Team |

---


## Privacy Summary

| Data Item | Source | Purpose | Consent | Storage | Access | Retention |
| --- | --- | --- | --- | --- | --- | --- |
| Email  | Sign in screen (POST /api/v1/authenticate) | Register the user for access to the program | Voluntarily entered by user | PostgreSQL database | backend authentication system, user can view their name and emial | Until account deletion |
| Password  | Sign in screen (POST /api/v1/authenticate) | Register the user for access to the program | Voluntarily entered by user | PostgreSQL database | backend authentication system, user can view their name and emial | Until account deletion |
| FirstName  | Sign in screen (POST /api/v1/authenticate) | Register the user for access to the program | Voluntarily entered by user | PostgreSQL database | backend authentication system, user can view their name and emial | Until account deletion |
| LastName  | Sign in screen (POST /api/v1/authenticate) | Register the user for access to the program | Voluntarily entered by user | PostgreSQL database | backend authentication system, user can view their name and emial | Until account deletion |
| Session Token | Backend handled given to user in response to POST to /api/v1/authenticate | Maintain authentication session between user and program allows access to upload and models | User agrees to log in and connect to session | Session Storage | Backend authentication system and authorized frontend requests | Until expiration, log out or project terminates |
| Failed log in counter | Backend authentication system | Track unsuccessful login attempts and lockout user from account | Necessary for account security, mentioned in registration notice | PostgreSQL database | Backend authentication system | Successful login, or resets after 15 minute lockout or inactivty |
| Authentication failure logs | Backend authentication system | Track unsuccessful login attempts and lockout user from account | Necessary for account security, mentioned in registration notice | PostgresSQL database | Backend authentication system | Until account deletion |
| Uploaded image (up to 20MB) | Upload screen (`POST /api/v1/upload`) | Generate 3D model, kept so a failed generation can be retried | There is an Upload notice before the first upload that states the image is sent to a third-party AI service, and the user confirms they have the right to upload it. | Private file storage, not publicly reachable | Image Upload service and the AI provider during generation, owners account only | Image bytes can be deleted once the model is saved, or possibly 7 days after a failed job |
| Image hash (SHA-256) and size | Computed by the server | To recognize a repeat upload and avoid a second AI generation | Part of the upload | `Upload.image_hash` (indexed) and `size_bytes` | Image Upload service | Deleted with the account |
| Image sent to the AI 3D Model API | `AiClient` over HTTPS | Third party generation | Same upload notice | Held by the provider under its terms (Provider chosen in task T1) | The provider. We send no name, email, or user ID with the image | Per the provider's terms... |
| `GenerationJob` record (status, error code, attempt, times) | Server | Retry and diagnose failed generations | System record | `GenerationJob` table | Image Upload service. Team members for debugging. | Deleted with the upload |
| Generated model and metadata | AI service output after validation | Viewing and switching models | The user chose to generate it and named it | The file in file storage, metadata in the `Model` table | Owner only, API returns 404 for another users model | Deleted with the account, no seld-service delete in this release |
| Model file downloaded by the 3D Model Viewer (GLB, or Gaussian splat file) | `GET /api/v1/models/model_[ID]`, sent with the user's Bearer session token | Render the selected model in the browser | Covered by the user choosing the model in the 3D Model Switcher | Browser memory only (`ViewerState.scene`); the browser's HTTP cache may keep a copy | The signed-in owner's browser tab; the endpoint returns 401 without a token and 404 for another user's model | Freed from memory when the user selects another model or closes the tab (`dispose()`); the browser cache follows the browser's own rules |
| Layer visibility and camera position | The user's clicks, touches, and keys in the 3D Model Viewer | Show the chosen layers and view angle | User action | Browser memory only (`ViewerState`, `LayerVisibility`); never sent to the server | The user's own browser tab | Discarded when the user selects another model or closes the tab |
| Screen-reader text description | Generated in the browser by `getDescription()` from the model's name, visible layers, and vertex count | Accessibility for screen-reader users (REQ-11, US-05) | Shown to every user; contains only the model's own metadata | Browser memory only | The user's own browser tab | Discarded with the model |
|3d model | model switcher  | change the currently viewed model | user must volutarily change the model/ the default modle that loads is predetrimeined and not indidual user data | Browser memory only (ViewerState.scene); the browser's HTTP cache may keep a copy and the server may keep an ecrypted copy | only accessable to the user | Retained for the entirety of the users profile existance |
| indivdual layer data | 3d model layer switcher | change the layers of the the 3d mdoel | user must voluntarily change the layer to change the layers | Browser memory only (ViewerState.scene); the browser's HTTP cache may keep a copy and stored encypted on the server | only accessable to the user | the entirety of the users existance  |

**How Consent is Obtained:**

Consent is obtained with a notice on the sign-up screen showing a ticked box before creating the account. The notice will say what we collect, which is their email, name, and a password hash, and that uploaded images are sent to a third-party AI service, that models are kept private to the account, and the hosting provider keeps standard access logs, and all data is deleted by a certain date that will be updated by the team. The upload screen will show a second notice, that third-party AI service, right to upload, models are approximations, before the first upload and it records that the user confirme it. Use of the system is not treated as consent. A user can request the team to delete their data by emailing the address in the README, and the team deletes it within a weeks time.

**Removed Fields:**

- N/A

## Security Summary

### How We Addressed:

#### Default Credentials:

- No account is created by default.
- The database and file storage are created with new, generated credentials for each environment, never the providers' defaults, and the application does not use the default `postgres` account.
- The test accounts (`@avjs.test`) exist only in development and staging, with generated passwords kept in the team password manager. They are deleted before the pilot environment is used.
- The local `ai-stub` server used for testing has no credentials and only listens on the developer's own machine.

#### Unauthenticated Endpoints:

- Every endpoint in the Technical Specification requires a valid session token in the `Authorization: Bearer` header, except `POST /api/v1/authenticate`. A missing, malformed, or expired token returns 401 (NFR-03).
- Endpoints that take a model ID also check that the signed-in user owns the model. Anyone else gets 404, so IDs can't be guessed to read other users' models (EC-S-05).
- The 3D Model Viewer downloads model files through the same token-protected `GET /api/v1/models/model_[ID]` endpoint. Model files are never served from a public URL, so a copied link does not work without the owner's token.
- The sign-up and login endpoint has to be public, so it is protected by input validation and parameterized queries (EC-L-05).

#### Secrets in Source Control:

- Secrets live only in environment variables supplied by the hosting platform's secret store: the database connection string, the AI service API key, file storage credentials, and the token signing key. Nothing is written in source files.
- The 3D Model Viewer runs entirely in the browser and holds no secrets. The only credential it uses is the user's own session token.
- If a secret is found in the history, we treat it as leaked: we rotate it, remove it, and record it here.

### Result of search of our git history

### Self-Audit Questions Table

| Questions | Answers |
| -------- | -------- |
| Which endpoints work without a token? | Only the `POST /api/v1/authenticate`. TC-010 checks the other four endpoints and returns 401 without a valid token |
| Is traffic encrypted? | Yes, the application is served only over HTTPS and HTTP is redirected. |
| What stops guessing a password? | Lockout after 5 failures in 15 minutes (TC-011) and the same error message for a wrong email and password (TC-002). |
| Can a user open another user's model file? | No. The model file endpoint checks ownership and returns 404 (EC-S-05); the 3D Model Viewer has no other way to fetch files. |

## Licensing Summary

### Third-Party Dependencies, Datasets, and Creative Assets

| Type | Name | Version / Access Date | License | SPDX Identifier | Permissive or Copyleft | Obligations | Compatible with Project License? | Decision |
|---|---|---|---|---|---|---|---|---|
| Dependency | React | Exact version from lockfile | MIT License | `MIT` | Permissive | Keep copyright and license notice in distributed copies | Yes | Keep |
| Dependency | Ant Design, including the Upload component | Exact version from lockfile | MIT License | `MIT` | Permissive | Keep copyright and license notice | Yes | Keep |
| Dependency | Three.js (`three`), including its GLTFLoader add-on | Exact version from lockfile | MIT License | `MIT` | Permissive | Keep copyright and license notice | Yes | Keep; renders models in the 3D Model Viewer |
| Dependency | Spark (`@sparkjsdev/spark`), Gaussian splat renderer for Three.js | Exact version from lockfile, if adopted | MIT License | `MIT` | Permissive | Keep copyright and license notice | Yes | Keep if the team adopts Gaussian splats; decided after advisor review |
| Service | PostgreSQL | Version of the hosting plan | PostgreSQL License | `PostgreSQL` | Permissive | No obligations for using it as a hosted service | Yes | Keep |
| Service | AI 3D model API | Access date recorded when chosen | Provider's terms of service and API terms | N/A | Custom terms | Follow usage limits and rules on uploads. Check who owns the generated output and whether the provider keeps or reuses uploaded images. | Conditional | Decided in T1; we will replace any provider whose terms conflict with the privacy summary or our project license |
| Dataset | Khronos glTF Sample Assets (used in T16 and the 3D Model Viewer test cases) | Access date and model names recorded for each model used | Varies by model; most are CC0 or CC BY 4.0, a few are more restrictive | `CC0-1.0`, `CC-BY-4.0` (per model) | Permissive | CC BY 4.0 models: credit the author and license as listed in each model's `metadata.json`. Do not use models marked non-commercial (CC BY-NC) or with trademark limits | Yes, for CC0 and CC BY 4.0 models only | Keep only CC0 and CC BY 4.0 models; credits listed in the Attribution List |
| Dataset | Sample models for the US-01 to US-04 demos | Access date and source URL recorded for each | [License name] | [SPDX ID or N/A] | Permissive | Credit the author and source; do not change the license terms | Yes if chosen as stated | Keep only models with these licenses |
| Dataset | Team-made test images and files (`object-clear.png`, `small.glb`, `large.glb`, `flat.glb`, `corrupt.glb`, `oversize.glb`, and the rest in the Test Plan) | Created during the project | Created by the team | Project license | Permissive | None beyond our project license | Yes | Keep |
| Dataset | [Dataset name] | [Version or access date] | [License name] | [SPDX ID or N/A] | [Permissive / Copyleft / Custom] | [Attribution, citation, share-alike, etc.] | [Yes / No / Conditional] | [Decision] |
| Creative Asset | [Image, icon, font, audio, etc.] | [Version or access date] | [License name] | [SPDX ID or N/A] | [Permissive / Copyleft / Custom] | [Attribution, license notice, modification disclosure, etc.] | [Yes / No / Conditional] | [Decision] |

### Project License

- **Project license:** MIT License
- **SPDX identifier:** `MIT`
- **License category:** Permissive
- **License file:** `LICENSE.txt`

### Compatibility Statement

The project is licensed under **the MIT License**, with the SPDX identifier **`MIT`**. Each dependency, dataset, and creative asset listed above was reviewed for compatibility with the project's license and intended distribution method.

All retained items are compatible with the project's license, provided that the obligations listed in the table are satisfied.

### Copyleft Review

- **Copyleft items:** None
- **Copyleft type:** N/A

### Final Decision

We decided to **retain** the third-party items identified above.
Permissively licensed items were retained when their attribution and license-notice requirements could be satisfied. Copyleft and custom-licensed items were reviewed separately to determine whether their obligations would apply to the complete project.

## Attribution List

| Category | Name | Source | Author or Provider | License | Attribution Required? | Location Used |
|---|---|---|---|---|---|---|
| Library | Three.js | https://github.com/mrdoob/three.js | Three.js contributors | MIT | Yes | 3D Model Viewer rendering |
| Library | GLTFLoader (Three.js add-on) | https://github.com/mrdoob/three.js/tree/dev/examples/jsm/loaders | Three.js contributors | MIT | Yes | 3D Model Viewer, loading GLB files |
| Library | Spark (if adopted) | https://github.com/sparkjsdev/spark | World Labs and Spark contributors | MIT | Yes | 3D Model Viewer, rendering Gaussian splats |
| Library | React | https://github.com/facebook/react | Meta Platforms, Inc. | MIT | Yes | Front-end UI |
| Library | React DOM | https://github.com/facebook/react/tree/main/packages/react-dom | Meta Platforms, Inc. | MIT | Yes | Front-end rendering |
| Library | ASP.NET Core | https://github.com/dotnet/aspnetcore | Microsoft and contributors | MIT | Yes | Backend API |
| Library | .NET Runtime | https://github.com/dotnet/runtime | Microsoft and contributors | MIT | Yes | Backend execution |
| Library | PostgreSQL | https://www.postgresql.org/ | PostgreSQL Global Development Group | PostgreSQL License | Yes | Database |
| Library | Npgsql | https://github.com/npgsql/npgsql | Npgsql contributors | PostgreSQL License | Yes | C# database connectivity |
| Library | Ant Design | https://github.com/ant-design/ant-design | Ant Design contributors | MIT | Yes | Front-end dependency; Upload component on the upload screen |
| Dataset | Khronos glTF Sample Assets | https://github.com/KhronosGroup/glTF-Sample-Assets | Individual model authors, credited per model in each `metadata.json` | CC0-1.0 or CC-BY-4.0 (per model) | Yes, for CC BY 4.0 models | T16 library test page and 3D Model Viewer test cases; list each model and its credit here when chosen |
| Non-Code Asset | Ant Design icons (`@ant-design/icons`) | https://github.com/ant-design/ant-design-icons | Ant Design contributors | MIT | Yes | Buttons and status icons in the front end |

## AI-use Disclosure

### Syllabus policy:

"Generative AI tools may be used for drafting and code assistance but not for the design decisions or written analysis that are assessed in this course."

The following table records every use of a generative AI tool during the project. Entries are organized using the two-column framework discussed in class: **Generally OK** and **Generally Not OK**.

### AI-Use Record

| Tool | Used By | Task | Column | How Verified |
|---|---|---|---|---|
| ChatGPT | Veronica Malusky | Generated test cases for example template for NFR-03 | Generally OK | Used output as a template of what should be focused on then wrote down own individual test cases for the requirement |
| Claude (Anthropic) | Abby Kraft | Explained the assignment requirements and reviewed for gaps and inconsistencies. | Generally OK | Each point checked against our own documents before any changes were made. |
| Claude (Anthropic) | Sophia Munoz | Formatting markdown template to base file submission | Generally OK | Template provided section and file title check against assignment requirements |
| Gemini | Jake Martin | reviewing work an ensuring no major mistakes are present  | Generally OK | all out put is carefully reviewed |

## Team Ownership Confirmation

Each member must sign a separate line confirming that the assessed project decisions and rationale were created by the team.

- **Member:** Abby Kraft  
  **Signature:** Abigail Kraft  
  **Date:** 11-11-2026

- **Member:** Veronica Malusky  
  **Signature:** Veronica Malusky  
  **Date:** 11-11-2026

- **Member:** Sophia Munoz  
  **Signature:** Sophia Munoz  
  **Date:** 11-11-2026

- **Member:** Jake Martin  
  **Signature:** Jake Martin  
  **Date:** 11-11-2026  