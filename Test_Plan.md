<div align="center">

# Test and Validation Plan

**Team AVJS** · Web-Based Interactive Model Visualizer

Abby Kraft · Veronica Malusky · Jake Martin · Sophia Munoz

*How we will prove the system works, and that it is the right system.*

**Draft 0.1** · Traces to [Requirements Document](Requirements_Document.md) **v0.1** and [Technical Specification](Technical_Specification.md) **v0.1** · Last updated 2026-11-06

**Test Lead** _name_ · Each component owner writes the test cases for theor own component.

</div>

---

# 📝 Version and Change Log 

| Version | Date | Change | Author |
|---|---|---|---|
| 0.1 | 2026-11-06 | Initial draft | Team |



<!-- Target Length: 5-8 pages. keep the eight part names below as section headings. when TC IDs are final, copy them into the Test Case column of the traceability matrix in Technical_Specification.md  and log the change in the version log-->

---

## 1. 🎯 Scope

<!-- What is being tested this cycle, by requirement ID and component, and what is not. The out-of-scope list names deferred Won't and Could features, known limitations, and third-party components you will not retest. -->

### In Scope

| Component | Owner | Requirements Tested | Edge Cases Covered |
|---|---|---|---|
| Login / Sign-up | Veronica Malusky | REQ-01, REQ-02, NFR-03 | EC-L-01 to EC-L-06 |
| Image Upload | Abby Kraft | REQ-03, NFR-02 | EC-U-01 to EC-U-09 |
| 3D Model Switcher | Jake Martin | REQ-04, NFR-01 | EC-S-01 to EC-S-06 |
| 3D Model Viewer | Sophia Munoz | REQ-05, REQ-08, REQ-09, REQ-10, REQ-11, NFR-04, NFR-05, NFR-06, NFR-07 | EC-V-01 to EC-V-09 |
| Whole System | Team | End-to-end flows across all four components (Section 4.5) | — |

### Out of Scope

| Item | Reason |
|---|---|
| Prompt-Based Model Generation | Won't have this release (Requirements Document, MoSCoW) |
| Immersive Avatar Walkthrough | Won't have this release |
| Sharing Models Between Users | Won't have this release |
| Notes Attached to Model Sections | Could have. First to cut, so not tested unless built |
| REQ-06 Slicing of Volumetric Models | Not designed yet (orphaned in the traceability matrix until Q-02 to Q-04 are answered) |
| REQ-07 Importing an Existing GLB File | Not designed yet (orphaned in the traceability matrix) |
| AI 3D Model API Internals | Third-party service. We test our AiClient's handling of its responses, timeouts, and errors, not the service itself |
| Rendering Library Internals | Third-party, open-source library chosen in T16; we test our use of it, not the library |
| _Known Limitations_ | _e.g., browsers older than the versions in Section 5_ |

---

## 2. Verification and Validation Strategy

<!-- Verification and validation strategy. Half a page saying which requirements are verified against the spec by the team and which are validated against stakeholder needs by users or your advisor, plus a test-pyramid table of layer, approximate count, and coverage focus. Hardware, sensor, and machine learning projects also state variables, metrics, tolerance, and trials. -->

**Verification (are we building the product right?)** _Which requirements the team checks against the Requirements Document and Technical Specification, and how: unit, integration, and system tests, code review._
The team will check every requirement that has a number or an exact behavior in the Requirements Document against the Technical specification and the D2 edge cases. With those that are covered being REQ-01 to REQ-05 then REQ-08 to REQ-10 and then NFR-01 to NFR-05 and NFR-07. The developers write unit tests as they build each function and task, and integration tests check each component boundary in the Technical Specification and system tests will be running the full application. All team members will review eachothers code before it will be counted as done.

**Validation (are we building the right product?)** _Which requirements are judged by users or the advisor: for example NFR-06 with six first-time users, REQ-11 with a screen-reader user (US-05), and an advisor walkthrough of the US-01 to US-04 scenarios with sample models._


### Test Pyramid

| Layer | Approx. Count | Coverage Focus | Run by |
|---|---|---|---|
| Unit | ~ 50 | Single functions: input validation, error mapping, GLB validation, camera fitting | Developers, automated |
| Integration | ~ 12 | Component boundaries from the Technical Specification: auth -> Switcher, upload -> AI API, Switcher -> Viewer | Developers, per pull request |
| System | ~ 60 | End-to-end flows and all non-functional thresholds | Test lead and component owners |
| Acceptance | ~ 8 | Stakeholder scenarios, usability, accessibility | Advisor and test users |

### AI Generation Trials
The image-to-3D step is a third-party machine-learning service, so it is evaluated as an experiment rather than a single pass or fail.
 
| Variable | Metric | Tolerance | Trials |
|---|---|---|---|
| _Test image set (e.g., 10 images, one per stakeholder domain)_ | _Valid GLB produced_ | _e.g., at least 8 of 10_ | _Each image run 3 times_ |

---

## 3. Success Criteria

<!-- A table of measurable success criteria: metric, target threshold, how it is measured, and the Week 9 requirement ID behind it. Cover performance, reliability, usability, and accuracy as they apply. -->

| Metric | Target threshold | How it is measured | Requirement |
|---|---|---|---|
| Model list load time | <= 2 s for a user with 500 saved models, 5 per page | Browser dev tools Network timing, 20 loads, slowest counted | NFR-01 |
| New model appears after generation | <= 5 s after the job finishes; Switcher and Viewer stay usable during the job | Stopwatch from job status change to list update, 10 runs | NFR-02 |
| Unauthorized requests rejected | 100% of model and upload requests without a valid token return 401 | Scripted requests with missing, invalid, and expired tokens | NFR-03 |
| Account lockout | Locked for 15 min after 5 failed logins within 15 min | Scripted login attempts | NFR-03 |
| Model display time | <= 15 s for a 50 MB model | Viewer `loadMs` value, 10 loads on a 25 Mbps throttled connection | NFR-04 |
| Frame rate | >= 30 fps for a 500,000-triangle model in Chrome, Firefox, and Safari | Browser performance monitor during 60 s of continuous rotation | NFR-05 |
| First-time layer toggle | >= 5 of 6 test users show or hide a layer within 30 s on the first try | Moderated usability session, stopwatch | NFR-06 |
| Failed loads handled | 0 blank canvases. Every failure shows a message within 15 s. One lost WebGL context recovered automatically | Forced-failure test cases TC-062, TC-070 to TC-073, and TC-086 to TC-088 | NFR-07 |
| Invalid uploads rejected | 100% of invalid files (wrong type, 0 bytes, > 20 MB) rejected with the documented error | | REQ-03 |
| _Generation accuracy_ | _See Section 2 trials_ | | REQ-03 |

---

## 4. Test Cases

<!-- Test cases. At least 3 cases for every major requirement, and every Must requirement is major. Use the five-column template below, and include at least one edge case or failure condition in each group. Number the IDs to match the Test Case column of your Week 9 traceability matrix. -->

**Numbering:** TC-001–019 Login / Sign-up · TC-020–039 Image Upload · TC-040–059 3D Model Switcher · TC-060–089 3D Model Viewer · TC-090–099 system and acceptance.

**Status** is Pass, Fail, Blocked, or Not Run. Every group includes at least one edge case or failure condition, marked **(edge)**.

## 4.1 Login / Sign-up · Veronica Malusky

<!-- At least 3 cases each for REQ-01, REQ-02, and NFR-03 (all Must). Use EC-L-01 to EC-L-06 for the edge cases. -->


**REQ-01 · The system shall prompt user for login (Must)**
| ID | Description | Steps | Expected Result | Status |
|---|---|---|---|---|
| TC-001 | Start up page requires log in (REQ-01) | 1. Run the program <br>2. Click Login <br> 3. Validate log in page on load | Login page is the landing page, user can log in or sign up | Not Run |
| TC-002 | Login successful (REQ-01) | 1. Enter valid user name and password <br>2. Validate the login is a success and the user navigates to the main page| Login success authentication given and navigation to home page occurs | Not Run |
| TC-003 | Invalid Login (REQ-01) | 1. Enter valid username and invalid password <br> 2. Click Login <br> 3. Error should occur| 400 INVALID_CREDENTIALS ERROR (HTTP response) | Not Run |
| TC-006 | Invalid Login (REQ-01) | 1. Enter invalid username and valid password <br> 2. Click Login <br> 3. Error should occur| 400 INVALID_CREDENTIALS ERROR (HTTP response) | Not Run |
| TC-007 | Invalid Login (REQ-01) | 1. Click Login (username and password empty) <br> 2. Error should occur | INVALID_CREDENTIALS ERROR (No HTTP request should be sent) | Not Run |
| TC-008 | On failed authentication, results should be logged if http request sent (REQ-01) | 1. Repeat TC-003 <br> 2. In DB run the query SELECT * FROM ErrorLog ORDER BY CreatedOn desc | INVALID_CREDENTIALS ERROR should be logged in the ErrorLog table | Not Run |
| TC-009 | On failed authentication, results should be logged if http request sent (REQ-01) | 1. Repeat TC-004 <br> 2. In DB run the query SELECT * FROM ErrorLog ORDER BY CreatedOn desc | INVALID_CREDENTIALS ERROR should be logged in the ErrorLog table | Not Run |
| TC-010 | On failed authentication, results should be logged if http request sent (REQ-01) | 1. Repeat TC-004 <br> 2. In DB run the query SELECT * FROM ErrorLog ORDER BY CreatedOn desc | In this case the INVALID_CREDENTIALS ERROR should not display because the request should not be sent to the backend | Not Run |


**REQ-02 · The system shall allow new users to sign up (Must)**
| ID | Description | Steps | Expected Result | Status |
|---|---|---|---|---|
| TC-011 | Sign up functionality (REQ-02) | 1. Click Sign up button on login screen <br>2. Enter user information <br> 3. Click Sign Up button  | User should get popup that signup was successful and get sent back to the login screen | Not Run |
| TC-012 | Sign up does not happen for already existing email (REQ-02) | 1. Click Sign up button on login screen <br> 2. Enter user information with a username that already exists in the db and a password <br> 3. Click Sign Up button | EMAIL_ALREADY_TAKEN ERROR (HTTP response) | Not Run |
| TC-013 | On attempted signup for an existing email the error should be logged (REQ-02) | 1. Repeat TC-012 <br> 2. In DB run the query SELECT * FROM ErrorLog ORDER BY CreatedOn desc |  EMAIL_ALREADY_TAKEN should be logged in ErrorLog table | Not Run |
| TC-014 | On attempted signup with empty credentials error should display (REQ-01) | 1. Click Sign up button on login screen <br> 2. Enter user information with a valid email but leave password blank or email and password blank or email blank and password entered <br> 3. Click Sign Up button  | Validation error should display prompting the user that username or password is blank (No HTTP request sent) | Not Run |
| TC-015 | On attempted signup for an existing email the error should be logged (REQ-02) | 1. Repeat TC-014 - TC-016 <br> 2. In DB run the query SELECT * FROM ErrorLog ORDER BY CreatedOn desc |  In this case the EMAIL_ALREADY_TAKEN ERROR should not display because the request should not be sent to the backend | Not Run |

**NFR-03 · The system shall store passwords only as salted hashes, reject any model or upload request without a valid session token (401), and lock an account for 15 minutes after 5 failed logins within 15 minutes (Must)**

| ID | Description | Steps | Expected Result | Status |
|---|---|---|---|---|
| TC-016 | Passwords are stored as salted hash | 1. In DB run the query SELECT * FROM User | The password stored in the db should not be visible and easy to make out, should only be a salted hash | Not Run |
| TC-017 | Invalid token session does not allow user to upload or view models  | 1. attempt to view or upload model with inactive token session | Request is rejected with HTTP 401 Unauthorized. Error gets logged in ErrorLog table | Not Run |
| TC-018 | Lock out an account for 15 minutes after 5 failed logins within 15 minutes  | 1. Give false password credentials 5 times in 15 minutes | System should lock out account for 15 minutes | Not Run |
| TC-019 | Attempt to login while account is locked  | 1. Repeat TC-018 to lockout account <br> 2. Attempt to login with valid email and password (same email as TC-018) | Request should be denied and the user will not be able to sign in until lockout is cleared | Not Run |

## 4.2 Image Upload · Abby Kraft

For the testing the AI 3D Model API is replaced by the local `ai-stub` server (Section 5) in every case except TC-020 and the trials in Section 2.
<!-- At least 3 cases for REQ-03 (Must) and NFR-02. Use EC-U-01 to EC-U-09 for the edge cases. -->

 **REQ-03 · Upload an image and Generate a Model (Must)**

| ID | Description | Steps | Expected Result | Status |
|---|---|---|---|---|
| TC-020 | Valid image becomes a saved model (REQ-03) | 1. Log in as `upload-test@avjs.test`.<br>2. Choose `object-clear.png` then enter name "Test_Table", and click `Upload`.<br>3. Wait for result. | Progress bar will be shown as job runs. Response is JSON with `ModelID`, `Name` "Test_Table" `format` "glb", IRL ending with `model_[ModelID]`, Vertices, Triangles (For mesh not for gaussian), `Generated` 1, and an empty `Error`. Model appears in Switcher list and opens in the Viewer.| Not Run |
| TC-021 | **(edge)** File sizing limits (REQ-03, EC-U-01) | 1. In browser upload `size20mb.png`, that is exactly max size, upload `size20mb_plus1.png`, one byte more, and then also `empty.png`, which is zero bytes. <br>2. Send all three files at the same time utilizing `curl` to `POST /api/v1/upload`. | The file that is 20 MB is accepted where the larger file will return 413 and the empty file returns 400, each with a message. | Not Run |
| TC-022 | **(edge)** Image dimensions (REQ-03, EC-U-02) | 1. Upload `large_5000px.png` (5000 x 4000 px).<br>2. Upload `small_200px.png` (200 x 200 px)| The large image (5000 px) is downscaled to the max of 4096 px on its longest side before being sent to the AI service and the upload succeeds. The small (200 px) image returns 400 with "image too small" message. | Not Run |
| TC-023 | **(edge)** Wrong file type - Not Renamed (REQ-03, EC-U-06) | 1. In browser choose `test_photo.gif`.<br>2. Using `curl` send `test_photo.gif`. | The browser blocks `test_photo.gif` before sending and no job is created. | Not Run |
| TC-024 | **(edge)** Wrong file type - Renamed (REQ-03, EC-U-06) | 1. In browser choose `fake.png` (text file renamed to.png).<br>2. Using `curl` send `fake.png`. | The server returns 415 for `fake.png` since the header bytes are checked not the extension. | Not Run |
| TC-025 | **(edge)** AI service does not answer (REQ-03, EC-U-03) | 1. Set `ai-stub` to its `timeout` mode, where it never answers.<br>2. Upload `object-clear.png` start stopwatch.<br>3. Read `GenerationJob` row and the `ai-stub` request log. | The stub log will show two requests that are about 60 seconds apart because of the one automativ retry. At around the 120-130 second mark, the user sees a "Couldn't build a model" message which is 422. The job is `FAILED` with error code `AI_TIMEOUT`. No `Model` row exists and the user can upload again. | Not Run |
| TC-026 | **(edge)** AI service rate limit (REQ-03, EC-U-08) | 1. Set `ai-stub` to its `rate-limit` mode, will always return 429.<br>2. Upload `object-clear.png`.<br>3. Read timestamps in stub request log. | The AI stub recieves four requests in total, the first retries about 2, 4, 8 seconds later, each within 1 second. The job is then `FAILED` with error code `AI_RATE_LIMIT` and the user sees "service busy" message. | Not Run |
| TC-027 | **(edge)** AI service returns an empty or damaged model (REQ-03, EC-U-07) | 1. Set `ai-stub` to return `stub-empty.glb`, which has 0 bytes.<br>2. Upload `no-object.png`.<br>3. Repeat the same thing with `stub-truncated.glb` which has a valid header, declare length longer than the file. | Both uploads return 422 with a message "couldn't build a model from this image". The job is `FAILED`, no `Model` row and no GLB file are saved, the uploaded image is kept so the user can retry. | Not Run |
| TC-028 | **(edge)** File storage fails after generation successful (REQ-03, EC-U-09) | 1. Make the file storage location read-only.<br>2. Upload `object-clear.png` with `ai-stub` in `ok` mode.<br>3. Check the storage write attempts, the job and the error log | Save attempted 3 times, the job is then `FAILED`, an error row is written to the error log, no `Model` row exists. The user sees an error message. After storage is writable again, uploading the same image succeeds | Not Run |
| TC-029 | **(edge)** Same image uploaded twice, and double click of upload (REQ-03, EC-U-05) | 1. Upload `object-clear.png` and double click upload quickly.<br>2. After it finishes upload `object-clear.png` again.<br> Read `ai-stub` request log. | The double click will only send one request since the button is disabled while the job runs. The second upload returns the existing `ModelID` with no new request to the stub, the log shows 1 request in total. | Not Run |
| TC-030 | **(edge)** Upload without a valid login - no token (REQ-03) | 1. Using `curl` send `clear_object.png` to `POST /api/v1/upload` with no token. | Returns 401 and no `Upload` or `GenerationJob` row is created. | Not Run |
| TC-031 | **(edge)** Upload without a valid login - expired token (REQ-03) | 1. Using `curl` send `clear_object.png` to `POST /api/v1/upload` with an expired token. | Returns 401 and no `Upload` or `GenerationJob` row is created.| Not Run |

 **NFR-02 · Stay Usable During Generation (Should)**

| ID | Description | Steps | Expected Result | Status |
|---|---|---|---|---|
| TC-032 | The application stays usable while a job runs (NFR-02) | 1. Set `ai-stub` to a 30 second delay.<br>2. Upload `object-clear.png`.<br>3. While it runs, select an existing model in the Switcher, rotate it, and hide one layer. | Every action in step 3 responds as normal (model loads within 15 seconds, rotate and the layer toggle), the uploads progress indicator stays visible. | Not Run |
| TC-033 | The new model appears within 5 seconds of the job finishing (NFR-02) | 1. Set `ai-stub` to a 20 second delay.<br>2. Upload `object-clear.png` and stay on Switcher list.<br>3. Start a stopwatch when the stub sends its answer and stop it when the new model appears and repeat 5 times. | The new model appears at the top of the list without reloading the page, within 5 seconds all 5 runs. | Not Run |
| TC-034 | **(edge)** The tab is closed during generation (NFR-02, EC-U-04) | 1. Set `ai-stub` to a 30 second delay.<br>2. Upload `object-clear.png` then close the tab after 5 seconds.<br>3. After 60 seconds open the app and log in again. | Job continued on the server and finished, the new model is in the Switcher list and opens in the Viewer. | Not Run |

 **TBD Will be Updated Along the Course of the Project**

| ID | Description | Steps | Expected Result | Status |
|---|---|---|---|---|
| TC-035 | _What is tested_ () | 1. … | _Specific, measurable outcome_ | Not Run |
| TC-036 | _What is tested_ () | 1. … | _Specific, measurable outcome_ | Not Run |
| TC-037 | _What is tested_ () | 1. … | _Specific, measurable outcome_ | Not Run |
| TC-038 | _What is tested_ () | 1. … | _Specific, measurable outcome_ | Not Run |
| TC-039 | _What is tested_ () | 1. … | _Specific, measurable outcome_ | Not Run |


### 4.3 3D Model Switcher · Jake Martin
 

 REQ-04: The system shall store individual user models in the switcher to allow the user to choose what model they want to view
| ID | Description | Steps | Expected Result | Status |
|---|---|---|---|---|
| TC-040 | Switcher loads models | 1. User successfully logins 2. observe correct list of user saved models in switcher 3. upload new image/model 4. wait for model processing to complete 5. observe switcher list | User's individual models are successfully stored and listed in the switcher, and the latest/top model automatically loads into the viewer | Not Run |
| TC-041 | Switcher allows selecting different user models | 1. User logins 2. observe the top model is loaded in viewer 3. select second model from switcher 4. observe viewer updates 5. select third model from switcher 6. observe viewer updates 7. select first model again  | User can freely choose and switch between any stored models in the switcher, and the viewer renders each selected model correctly | Not Run |
| TC-042 | Switcher behavior with empty model repository(Edge case:EC-S-06) | 1. User with 0 saved models logs in 2. open switcher dropdown/panel 3. observe switcher list state 4. attempt to select an item from empty listl| Switcher sucessfully displays no itmes to be selected | Not Run |


NRF-01:The system shall list a user's saved models, 5 per page, within 2 seconds for a user with up to 500 saved models

| ID | Description | Steps | Expected Result | Status |
|---|---|---|---|---|
| TC-050 | Standard pagination of saved models list | 1. User logs in with an account containing 25 saved models 2. open saved models list 3. observe items on page 1 4. click next page 5. observe items on page 2 6. navigate through remaining pages | Exactly 5 models are displayed per page, pagination controls operate cleanly, and model cards load without missing items | Not Run |
| TC-051 | Listing latency with maximum database capacity of 500 saved models | 1. Log in with a test account populated with exactly 500 saved models 2. trigger model list fetch 3. start performance timer 4. observe complete rendering of initial 5 models 5. record response time | Model list returns and displays the first 5 models completely within 2.0 seconds under 500-model load | Not Run |
| TC-052 | Rapid page traversal under heavy model volume(Edge case: EC-S-05) | 1. Log in with 500-model test account 2. navigate to page 50 3. click rapidly between consecutive pages (e.g., page 50 to 55) 4. observe fetch response and rendering time for each page jump| Each 5-item page request completes within 2 seconds without timeout errors, race conditions, or dropped page states| Not Run |
 
### 4.4 3D Model Viewer · Sophia Munoz

Test data for these cases is listed in Section 5: `small.glb` (about 2 MB, 3 named layers), `large.glb` (about 48 MB, 500,000 triangles), `flat.glb` (a single plane), `corrupt.glb`, and `oversize.glb` (51 MB).

**REQ-05 · Display a Selected Model (Must)**
 
| ID | Description | Steps | Expected Result | Status |
|---|---|---|---|---|
| TC-060 | A selected model opens in the Viewer (REQ-05) | 1. Log in as `viewer-test@avjs.test`.<br>2. In the 3D Model Switcher, select `small.glb`.<br>3. Wait for the loading indicator to disappear. | The model is drawn on the canvas, centered and fully in view, within 15 s; `loadMs` is recorded in the console | Not Run |
| TC-061 | Rapid switching shows only the latest model (REQ-05, EC-V-04) | 1. Log in as `viewer-test@avjs.test`.<br>2. Click 10 different models in the Switcher, one per second.<br>3. Wait 15 s.<br>4. Open browser dev tools → Memory and take a heap snapshot; repeat steps 2–4 once more. | Only the 10th model is shown; no earlier model appears at any point; the second heap snapshot is within 10% of the first | Not Run |
| TC-062 | **(edge)** Model file is missing (REQ-05, EC-V-06) | 1. As the test admin, delete the GLB file for `small.glb` from file storage but keep its database row.<br>2. Log in and select `small.glb` in the Switcher. | Viewer shows the `LOAD_FAILED` message with Retry and Back within 15 s; the canvas is never blank without a message | Not Run |

**REQ-08 · Camera Controls (Must)**
 
| ID | Description | Steps | Expected Result | Status |
|---|---|---|---|---|
| TC-063 | Mouse controls rotate, zoom, and pan (REQ-08) | 1. Open `small.glb`.<br>2. Left-drag 200 px to the right.<br>3. Scroll the wheel 5 notches toward the screen.<br>4. Right-drag 100 px up.<br>5. Click **Reset view**. | Step 2 rotates the model; step 3 zooms in; step 4 moves the model up; step 5 returns the camera to the starting view | Not Run |
| TC-064 | Keyboard controls work without a mouse (REQ-08, REQ-11) | 1. Open `small.glb`.<br>2. Press Tab until the canvas has focus.<br>3. Press each arrow key, then `+` and `-`, then `R`. | Arrow keys rotate, `+` and `-` zoom, `R` resets the view; a visible focus outline is shown on the canvas | Not Run |
| TC-065 | Touch controls on a phone (REQ-08) | 1. On the test phone (Section 5), open `small.glb`.<br>2. Drag one finger across the model.<br>3. Pinch out with two fingers.<br>4. Drag with two fingers. | One-finger drag rotates, pinch zooms, two-finger drag pans; the page itself does not scroll or zoom | Not Run |
| TC-066 | **(edge)** Controls used before a model loads (REQ-08, EC-V-02) | 1. Throttle the network to "Slow 3G" in dev tools.<br>2. Select `large.glb` and immediately press `R` and drag on the canvas.<br>3. Separately, open `flat.glb`. | Step 2: no crash and no error popup; input is ignored until the model loads. Step 3: `flat.glb` is shown at a default distance, not zoomed infinitely in or out | Not Run |

**REQ-09 · Show or Hide a Layer (Must)**
 
| ID | Description | Steps | Expected Result | Status |
|---|---|---|---|---|
| TC-067 | Hiding a layer does not reload the file (REQ-09) | 1. Open `small.glb`.<br>2. Open dev tools → Network and clear it.<br>3. In the Switcher, uncheck layer 2. | Layer 2 disappears within 1 s; layers 1 and 3 stay visible; the Network tab shows no new GLB request | Not Run |
| TC-068 | Showing a layer again restores it (REQ-09) | 1. Continue from TC-067.<br>2. Check layer 2 again.<br>3. Uncheck all three layers, then check all three. | Layer 2 reappears in the same position; with all unchecked the canvas shows an empty scene (not an error); all three return when checked | Not Run |
| TC-069 | **(edge)** Layer ID not in the model is ignored (REQ-09, EC-V-07) | 1. Open `small.glb`.<br>2. In the browser console, run `viewer.setLayerVisibility(99999, false)`. | No error is shown to the user and the model is unchanged; one warning naming layer 99999 appears in the console | Not Run |
 
**REQ-10 · Error Messages (Should)**
 
| ID | Description | Steps | Expected Result | Status |
|---|---|---|---|---|
| TC-070 | **(edge)** Load timeout (REQ-10, EC-V-03) | 1. In dev tools, set a custom throttle of 50 kbps.<br>2. Select `large.glb`.<br>3. Start a stopwatch. | `LOAD_TIMEOUT` message with Retry appears between 15 and 17 s; Retry starts a new load | Not Run |
| TC-071 | **(edge)** Corrupt file (REQ-10, EC-V-06) | 1. Seed `corrupt.glb` for the test account.<br>2. Select it in the Switcher. | `PARSE_FAILED` message with Back appears within 15 s; the previous model's memory is freed | Not Run |
| TC-072 | **(edge)** File over the size limit (REQ-10, EC-V-01) | 1. Seed `oversize.glb` (51 MB) for the test account.<br>2. Open dev tools → Network.<br>3. Select it in the Switcher. | `MODEL_TOO_LARGE` message appears immediately; the Network tab shows the GLB was never downloaded | Not Run |
| TC-073 | **(edge)** Browser without WebGL (REQ-10, EC-V-08) | 1. In Firefox, set `webgl.disabled` to `true` in `about:config`.<br>2. Log in and select `small.glb`. | `UNSUPPORTED_BROWSER` message is shown, along with the model's text description; no blank canvas | Not Run |
 
**REQ-11 · Screen Reader and Keyboard Access (Should)**
 
| ID | Description | Steps | Expected Result | Status |
|---|---|---|---|---|
| TC-074 | Screen reader reads the model description (REQ-11, US-05) | 1. Turn on NVDA (Windows) or VoiceOver (macOS).<br>2. Using only the keyboard, select `small.glb`. | The screen reader announces the model name, the visible layer names, and the vertex count within 5 s of the load finishing | Not Run |
| TC-075 | Description updates after a layer change (REQ-11) | 1. Continue from TC-074.<br>2. Using the keyboard, uncheck layer 2. | The screen reader announces the updated list of visible layers, without layer 2 | Not Run |
| TC-076 | **(edge)** Whole flow with keyboard only (REQ-11) | 1. Unplug the mouse.<br>2. From the login screen, log in, select a model, rotate it, hide a layer, and reset the view. | Every step can be completed with Tab, Enter, Space, and arrow keys; focus is never trapped | Not Run |
 
**NFR-04 · Model Display Time (Must)**
 
| ID | Description | Steps | Expected Result | Status |
|---|---|---|---|---|
| TC-077 | 50 MB model within 15 s (NFR-04) | 1. In dev tools, set a custom throttle of 25 Mbps.<br>2. Clear the browser cache.<br>3. Select `large.glb` and record `loadMs`.<br>4. Repeat steps 2–3 for 10 loads. | All 10 values of `loadMs` are <= 15,000 | Not Run |
| TC-078 | Small model loads quickly (NFR-04) | 1. Repeat TC-077 with `small.glb`. | All 10 values of `loadMs` are <= 15,000; record the median for the report | Not Run |
| TC-079 | **(edge)** Exactly at the size limit (NFR-04, EC-V-01) | 1. Seed a 50.0 MB GLB.<br>2. Select it on the 25 Mbps throttle. | The model loads (it is not rejected as too large) and `loadMs` <= 15,000 | Not Run |
 
**NFR-05 · Frame Rate (Should)**
 
| ID | Description | Steps | Expected Result | Status |
|---|---|---|---|---|
| TC-080 | 30 fps at 500,000 triangles (NFR-05) | 1. On the mid-range test laptop (Section 5), open `large.glb` in Chrome.<br>2. Turn on dev tools -> Rendering -> Frame rendering stats.<br>3. Drag-rotate continuously for 60 s.<br>4. Repeat in Firefox and Safari. | Average frame rate >= 30 fps in all three browsers | Not Run |
| TC-081 | Frame rate holds while toggling layers (NFR-05) | 1. Repeat TC-080 in Chrome while toggling a layer every 5 s. | Average frame rate >= 30 fps | Not Run |
| TC-082 | **(edge)** Rendering pauses in a hidden tab (NFR-05, EC-V-05) | 1. Open `large.glb`.<br>2. Switch to another tab for 30 s.<br>3. Return. | Dev tools Performance shows no frames drawn while hidden. The model is shown unchanged on return | Not Run |
 
**NFR-06 · First-Time Layer Toggle (Should)** · *validation*
 
| ID | Description | Steps | Expected Result | Status |
|---|---|---|---|---|
| TC-083 | First-time users find the layer toggle (NFR-06) | 1. Recruit 6 people who have not seen the app.<br>2. Log each in with `small.glb` open.<br>3. Say only: "Hide the second part of this model."<br>4. Time each person and give no hints. | At least 5 of 6 succeed on the first try within 30 s | Not Run |
| TC-084 | Users can bring a layer back (NFR-06) | 1. Continue with the same 6 people.<br>2. Say: "Show it again." | At least 5 of 6 succeed within 30 s | Not Run |
| TC-085 | **(edge)** Model with many layers (NFR-06) | 1. Seed a GLB with 20 named layers.<br>2. Repeat TC-083 asking for a specific named layer. | At least 5 of 6 succeed within 30 s | Not Run |
 
**NFR-07 · No Blank Canvas (Must)**
 
| ID | Description | Steps | Expected Result | Status |
|---|---|---|---|---|
| TC-086 | **(edge)** Network drops during a load (NFR-07) | 1. Select `large.glb`.<br>2. After 2 s, set dev tools Network to Offline. | `LOAD_FAILED` message with Retry appears within 15 s. Setting the network back online and clicking Retry loads the model | Not Run |
| TC-087 | **(edge)** One lost WebGL context recovers (NFR-07, EC-V-09) | 1. Open `small.glb`.<br>2. In the console, run `viewer.debugLoseContext()` (uses the `WEBGL_lose_context` extension). | The model reappears automatically within 15 s without user action | Not Run |
| TC-088 | **(edge)** A second lost context shows an error (NFR-07, EC-V-09) | 1. Continue from TC-087.<br>2. Run `viewer.debugLoseContext()` again. | `CONTEXT_LOST` message is shown; the canvas is never blank without a message | Not Run |

### 4.5 System and Acceptance · Team
 
<!-- End-to-end flows across all four components, and the advisor's acceptance walkthrough. -->
 
| ID | Description | Steps | Expected Result | Status |
|---|---|---|---|---|
| TC-090 | New user signs up, uploads an image, and views the model (REQ-01 to REQ-05) | 1. … | _Specific, measurable outcome_ | Not Run |
| TC-091 | Advisor walkthrough of the US-01 to US-04 scenarios (validation) | 1. … | _Advisor confirms each scenario meets its user goal_ | Not Run |
| TC-092 | **(edge)** Returning user finds their saved models (REQ-01, REQ-04) | 1. Log in as `viewer-test@avjs.test` and note the model list.<br>2. Log out.<br>3. Close browser, open again, and log in. | The same models are listed in the same order, none are lost or duplicated | Not Run |
| TC-093 | Core flow in all three major browsers (NFR-05) | 1. Run TC-090 in Chrome, Firefox, and Safari on the test laptop. | TC-090 passes in all three browsers | Not Run |
| TC-094 | **(edge)** AI service is down, and saved models still work (REQ-03, NFR-07) | 1. Set `ai-stub` to `timeout` or switch off the connection to the AI service.<br>2. Try an upload.<br>3. Without logging out, select an existing model and interact with it. | The upload shows an error message and does not freeze the application. The exsisting model opens, and can be interacted with as normal. | Not Run |
| TC-095 | Data integrity after the entire upload test run (REQ-03, REQ-04) | 1. After running TC-020 to TC-032, query the `Model` table and list the files in the file storage.<br>2. Compare the two lists | 0 `Model` rows without a stored file and 0 stored files without a `Model` row. Every `FAILED` job has no `Model` row | Not Run |
| TC-096 | _..._ | 1. _..._ |  _Specific, measurable outcome_ | Not Run |
| TC-097 | _..._ | 1. _..._ |  _Specific, measurable outcome_ | Not Run |
| TC-098 | _..._ | 1. _..._ |  _Specific, measurable outcome_ | Not Run |
| TC-099 | _..._ | 1. _..._ |  _Specific, measurable outcome_ | Not Run |

---

## 5. Test Environment

<!-- Test environment. The hardware, software, data, and configuration the tests run against: operating system and browser versions, devices and sensors, seeded test data, network conditions. Distinguish development, staging, and pilot environments. -->

| | Development | Staging | Pilot |
|---|---|---|---|
| **Purpose** | Unit and integration tests while building | System tests before each milestone | Acceptance tests with the advisor and test users |
| **Where it runs** | Each member's laptop | _Hosting environment, test instance_ | _Hosting environment, demo instance_ |
| **Database** | Local PostgreSQL _version_ with seeded test data | Seeded test data, reset before each test run | Real accounts created for the session |
| **AI 3D Model API** | Local `ai-stub` server only| `ai-stub` by default, but the real service for TC-020 and the trial in Section 2, with the number of calls counted against the free allowance (CON-01) | The real service with a set of already generated models saved as a backup for the demo |
| **Network** | | | |
 
**Browsers and Devices**
 
| Item | Version or Model |
|---|---|
| Chrome | most recently released version |
| Mid-range test laptop (NFR-05) | i5 cpu, 1080 or better, 32GBs |
| Test phone (TC-065) <!-- maybe who knows might remove this and TC-065 --> | _model and OS version_ |
| Screen readers (REQ-11) | NVDA _version_ (Windows) |
 
**Seeded Test Data**
 
| Data | Contents | Used by |
|---|---|---|
| Test accounts | `viewer-test@avjs.test` and _others_ | All components |
| `small.glb` | About 2 MB, 3 named layers | TC-060 to TC-069, TC-074 to TC-078, TC-083 to TC-084, TC-087 to TC-088 |
| `large.glb` | About 48 MB, 500,000 triangles | TC-066, TC-070, TC-077, TC-080 to TC-082, TC-086 |
| `flat.glb` | A single plane (zero-thickness bounding box) | TC-066 |
| `corrupt.glb` | Valid header, damaged binary chunk | TC-071 |
| `oversize.glb` | 51 MB | TC-072 |
| 50.0 MB GLB and 20-layer GLB | Boundary and usability cases | TC-079, TC-085 |
| Test images |  `object-clear.png`, `size20mb.png` (20MB image), `size20mb_plus1.png` (one more byte than max), `empty.png` (0 bytes), `large_5000px.png` (5000 x 4000 px), `small_200px.png` (200 x 200 px), `fake.png` (text file renamed to .png), `test_photo.gif`, `clear_object.png`,  | Image Upload, Section 2 trials |
| Stub model files | `stub-valid.glb` (same as `small.glb`), `stub-empty.glb` (0 bytes), `stub-truncated.glb` (valid header, declared length longer than the file)  | TC-025 to TC-032 |

---

## 6. Pass and Fail Criteria

<!-- Pass and fail criteria. The objective threshold that decides whether each test, and the plan as a whole, succeeded. State the pass condition and what counts as a failure, tied to the metrics in part 3. -->

**Each test case** passes when every part of its Expected Result is observed, and fails when any part is not. A test is **Blocked** when it can't run because something it depends on isn't ready (for example, the Viewer integration tests before the Switcher is built), it is not counted as a pass.

**Each metric** in Section 3 passes when it meets its target threshold on every run listed under "How it is measured." One run outside the threshold is a failure.

**The plan as a whole** passes when:
 
- [ ] Every test case for a **Must** requirement passes.
- [ ] At least 90% of test cases for **Should** requirements pass, and each failure has a logged defect with an owner.
- [ ] Every metric in Section 3 meets its threshold.
- [ ] No open defect blocks a Must requirement.
- [ ] The advisor signs off on the acceptance walkthrough (TC-091).

The plan **fails** if any Must test case fails at the CEAS Expo readiness check, or any Must metric misses its threshold

---

## 7. Test Schedule

<!-- A table of milestone, test activity, owner, and evidence, placing each layer against your Week 8 milestones: unit and integration during sprints, system and acceptance before reviews and demos. -->

Dates come from the [Milestones, Timeline, and Effort Matrix](Planning/Milestones_Timeline_and_Effort_Matrix.pdf). Core flows and anything touching data integrity are tested first.

| Milestone (Date) | Test Activity | Owner | Evidence |
|---|---|---|---|
| 3D Model Viewer library selected (1/13/2027) | Spike: render sample GLBs, measure frame rate (early check of NFR-05) | Sophia | Frame-rate notes and library choice in the repo |
| _Login/Sign-up backend (1/2027)_ | Unit and integration tests for login and sign-up | Veronica | _Test run log_ |
| Viewer renders a stored model (1/22/2027) | Unit tests; TC-060 to TC-062 | Sophia | Test run log, screenshots |
| Upload intake working (1/31/2027) | TC-021 to TC-024 | Abby | Test run log, request logs |
| _Login/Sign-up testing complete (2/10/2027)_ | _TC-001 to TC-0xx_ | Veronica | |
| Generation pipeline working (2/17/2027) | TC-020, TC-025 to TC-029 | Abby | Test run log, trial results |
| Viewer feature-complete (2/19/2027) | TC-063 to TC-076, TC-082, TC-086 to TC-088 | Sophia | Test run log |
| Models saved and listed (3/1/2027) | TC-028, TC-040 | Abby, Jake | Test run log, database comparison |
| Switcher–Viewer integration (3/12/2027) | Integration tests; TC-077 to TC-081 with real generated models | Sophia, Jake | Test run log, `loadMs` and fps records |
| _Switcher testing complete (3/20/2027)_ | _TC-040 to TC-0xx_ | Jake | |
| Protected buffer (3/22–4/9/2027) | System tests TC-090+; usability sessions TC-083 to TC-085; advisor walkthrough TC-091 | Team | Signed acceptance record, usability results |
| CEAS Expo (4/13/2027) | Final run of all Must test cases on the pilot environment | Team | Final test report |

---

## 8. Definition of Done

<!-- Definition of done. A checklist a feature must meet before your team calls it complete, and a project-level version of the same: tests, review, documentation, open defects, and sign-off. -->

### A feature is done when...

- [ ] Unit tests are written and passing.
- [ ] Integration-tested with the components it connects to.
- [ ] Every test case for its requirements passes, including the edge cases.
- [ ] Code is reviewed and approved by at least one other team member.
- [ ] Error messages match the Technical Specification.
- [ ] Documentation is updated (Technical Specification, README if needed).
- [ ] No open defects of high severity.
- [ ] The component owner signs off.

### The project is done when

- [ ] Every feature meets the feature checklist above.
- [ ] The plan-level pass criteria in Section 6 are met.
- [ ] Every requirement in the traceability matrix has a test case with a recorded result.
- [ ] All documents are current and their versions cross-referenced.
- [ ] The advisor signs off.

---

<sub>Related: [Requirements Document](Requirements_Document.md) · [Technical Specification](Technical_Specification.md) · [Tasklist](Tasklist.md) · [DEsign Diagrams](Design_Diagrams/)</sub>