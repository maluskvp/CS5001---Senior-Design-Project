<div align="center">

# Task List

**Team AVJS** · Web-Based Interactive Model Visualizer · Assignment #5

*Every task begins with a verb, names the Design Diagram module it concerns, and has one member of primary responsibility.*

</div>

---

## 👥 Task Owners

| Tasks | Owner | Design Diagram Module |
|---|---|---|
| [T1 - T5](#-image-upload) | Abby Kraft | Image Upload |
| [T6 - T10](#-login--sign-up) | Veronica Malusky | Login / Sign-up |
| [T11 - T15](#-3d-model-switcher) | Jake Martin | 3D Model Switcher |
| [T16 - T20](#-3d-model-viewer) | Sophia Munoz | 3D Model Viewer |

---

## 🖼️ Image Upload

- **T1:** Research and select the image-to-3D AI service mentioned in D1, and develop the AiClient, including a documented backup plan. Owner: Abby Kraft
- **T2:** Develop Image upload front end screen and UploadController endpoint. Owner: Abby Kraft
- **T3:** Develop the GenerationJob worker and the GlbValidator from algorithms 1 and 2. Owner: Abby Kraft
- **T4:** Develop ModelStore.save including model and layer rows with duplicate detection. Owner: Abby Kraft
- **T5:** Test the Image Upload pipeline against Algorithm 1 and 2 edge cases and the EC-U rows. Owner: Abby Kraft

## 🔐 Login / Sign-up

- **T6:** Research login / sign-up requirements set in D0 and D2 Design Diagrams. Owner: Veronica Malusky
- **T7:** Design Login/Sign-up screen layout for front end - include some error handling in the front end to reduce http traffic. Owner: Veronica Malusky
- **T8:** Develop the individual login and sign-up components, include the email validation and error handling established in D2 Design Diagram. Owner: Veronica Malusky
- **T9:** Develop session handling tokens to allow for user authentication. Owner: Veronica Malusky
- **T10:** Test the login/sign-up screen against edge cases established in D2 Design Diagrams EC-L-01 to EC-L-06. Owner: Veronica Malusky

## 🗂️ 3D Model Switcher

- **T11:** Develop the shared codebase framework (repository structure, React front end, backend project, and database connection) used by every component, including the 3D Model Switcher. Owner: Jake Martin
- **T12:** Develop the 3D Model Switcher backend endpoints for the paged model list (5 per page) and a model’s layers, with ownership checks and unit tests. Owner: Jake Martin
- **T13:** Develop the 3D Model Switcher front end: model list, model selection, and layer toggles that send the selection to the 3D Model Viewer. Owner: Jake Martin
- **T14:** Refine the 3D Model Switcher’s styling, including the empty state and error banner with Retry. Owner: Jake Martin
- **T15:** Test the 3D Model Switcher against edge cases EC-S-01 to EC-S-06 and its integration with Image Upload and the 3D Model Viewer. Owner: Jake Martin

## 🧊 3D Model Viewer

- **T16:** Research and select the WebGL rendering library and GLB loader for the 3D Model Viewer, building a test page that renders a sample GLB. Owner: Sophia Munoz
- **T17:** Develop the 3D Model Viewer's model loading (download the GLB from glbUrl, parse it, fit the camera, cancel stale loads, and free the previous model). Owner: Sophia Munoz
- **T18:** Develop camera controls (rotate, zoom, pan, reset) and layer visibility for the 3D Model Viewer. Owner: Sophia Munoz
- **T19:** Develop error states and accessibility for the 3D Model Viewer (load, timeout, size, and WebGL errors; keyboard controls; screen-reader text description). Owner: Sophia Munoz
- **T20:** Test and validate the 3D Model Viewer's integration with the 3D Model Switcher against edge cases EC-V-01 to EC-V-09. Owner: Sophia Munoz

---

## ✅ Done When

Each task is checkable, meaning it is finished when its "done when" condition is met.

| ID | Estimate (h) | Done when |
|---|---|---|
| T1 | 17 | Service is chosen and a test image has been produced, a GLB that opens in a glTF viewer. The comparison is named and a backup plan is in the repo. AiClient returns a GLB for a test image, times out at 60 seconds and retries a simulated 429 after 2, 4, and 8 seconds. |
| T2 | 21 | A 0 byte file and over 20 MB file and a non-image that is renamed to .png are rejected in the browser with error popup. Each documented error is returned for the matching bad request, images over 4096 px get downscaled and valid upload returns the documented JSON. The button is disabled while a job runs. |
| T3 | 20 | Upload creates a GenerationJob that ends as done or failed. Forced timeout retries once then returns 422. Closing tab mid-job still ends with a result, the image is then kept for a retry. Validator accepts a good GLB and rejects empty, oversize, and wrong-version files with reason. |
| T4 | 15 | Valid GLB is written to file storage with rows Model, glb_url, and Layer. A forced storage failure retries three times and then marks the job failed and logs it. |
| T5 | 13 | Every case listed in T5 has a written pass or fail result and the must fix failures are fixed, and an uploaded PNG shows in the switcher list and opens correctly in the Viewer. |
| T6 | 8 | Requirements are gathered from all design documents implementation is ready to be started |
| T7 | 10 | User navigation is applied and an account can be added then logged in successfully to the landing page |
| T8 | 10 | All the requirements gathered from T6 are established, error handling and user sign in |
| T9 | 8 | Token authentication is used for users, the token will authenticate them and provide access to the WebGL |
| T10 | 10 | All edge cases for login/sign up pass (EC-L-01 to EC-L-06) |
| T11 | 10 | Every member can clone the repo, run the front end and backend locally, and reach the database |
| T12 | 20 | Endpoints return only the signed-in user’s models, 5 per page; another user’s modelId returns 404; unit tests pass |
| T13 | 20 | Selecting a model sends modelId, glbUrl, and visible layers to the Viewer; toggling a layer updates without reloading the list |
| T14 | 10 | Advisor and team approve the look; the empty state and error banner match the D2 design |
| T15 | 20 | Each EC-S case has a written pass or fail result; must-fix failures are fixed; a new upload appears at the top of the list |
| T16 | 9 | Sample GLBs (Khronos glTF sample models, plus an AI-generated one if available) render at 30+ fps in Chrome, Firefox, and Safari; library choice recorded |
| T17 | 22 | A model opens from the switcher's `glbUrl`; switching models 10 times shows only the latest and memory doesn't grow |
| T18 | 17 | Mouse, touch, and keyboard controls work; toggling a layer updates without reloading the file |
| T19 | 17 | Each error code shows its message; keyboard-only use works; a screen reader reads the model description (US-05) |
| T20 | 13 | All EC-V-01 to EC-V-09 cases pass; a selected model is shown within 15s |

<sub>Estimates use (O + 4M + P) / 6 from the owner's optimistic, most likely, and pessimistic hours, before the 15-20% buffer. Based off EcoRoute section of Week 08 slides. The full schedule, effort matrix, and risk register are in the `Planning/` folder.</sub>

<sub>Last updated 2026-10-28</sub>