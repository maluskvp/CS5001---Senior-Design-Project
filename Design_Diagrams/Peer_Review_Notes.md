# Peer Design Review Notes

**Project:** Web-Based Interactive Model Visualizer
**Goal:** Create a web-based model visualizer that allows users to quickly change layers of the model as well as create models based on images uploaded via AI.

**Our team (reviewed):** Abby Kraft, Veronica Malusky, Jake Martin, Sophia Munoz
**Reviewing teams:**
- Kyle Russell, Shashank Sathiyanarayanan, Jaxon Polo, Arleen Monteiro, Nyla Spencer (Transparent OPV team)
- Allison Oh, Aditya Pawar (Model Recommend team), reviewed October 28, 2026

**Additional feedback:** Professor Ming Tang (advisor)

---

## What the reviewers liked

**Transparent OPV team**
- Algorithm entries were easy to follow.
- Input is checked on both the front end and the server.
- The 3D Model Viewer cancels stale loads and frees old materials.
- Loading the model list separately from the GLB file keeps the switcher fast.

**Model Recommend team**
- The edge-case table is the strongest part: 30 rows covering all four categories for each component, each with a decided behavior and a named handler.
- The 3D Model Viewer is fully specified: a "no persistent data" model, a step list, a state machine with error paths, and method signatures with error codes and examples.
- Good defensive choices: checking input on both the front end and the server, cancelling stale loads, hashing uploads so the same image isn't paid for twice, and returning 404 for other users' models.

---

## Feedback received

<sub>Sections below are from the Transparent OPV team unless marked otherwise.</sub>

### Feasibility
- The overview mentions volumetric slicing, but a GLB file is a surface mesh. It isn't clear how one uploaded image turns into layers.
- The AI image-to-3D step is the core of the project, but the document doesn't name the model or service, or whether it's realistic this semester.
- Signup claimed "no racing," but two people signing up with the same email at the same moment could both pass the check. A unique constraint on the email column would prevent this.

### Clarity
- Field names and types change between sections (`email` vs `Email`; `sessionToken` is a "token" in Login but a "string" in the Switcher).
- One `/auth` endpoint with a `Signup` bit makes it hard to tell which errors each case returns. Two endpoints might be simpler.
- The complexity notes were hard to read. Listing each step with its cost would help.

### Risk
- If the AI service fails, the design retries once and gives up. There's no plan for a broken mesh. Keeping the uploaded image so the user can retry would be a safety net.
- It wasn't clear whether the model URL checks that the requester owns the model.
- Token expiry, token storage, and logout weren't defined, and both Argon2id and bcrypt were listed without picking one.
- There was no size limit on GLB files, which could cause trouble on lower-end devices.

### From the Model Recommend team

Their rubric estimate for our D2 was **about 56 of 100**: Completeness 22/35, Traceability 9/25, Rationale 13/20, Peer review 12/20.

- **Completeness:** The 3D Model Switcher has no interface or state machine. The Login/Sign-up "data model" is a flow diagram, not a data model. The Image Uploader says only "completed in D1." Glue components (configuration, logging, token handling) aren't designed, and the D0 interfaces aren't revisited.
- **Traceability:** The table lists UI elements (username text box, sign-in button) instead of requirements, with no requirement IDs, no non-functional rows, and no figures or EC IDs cited. A "DELETE BEFORE TURNIN" note was left in a column header.
- **Rationale:** Four of six entries don't name the alternative or the accepted trade-off, and none reference the D0 decision log.
- **Peer review:** Owner and due date were blank on every action item, and several must-fix items from the first review weren't applied yet.
- **Clarity:** Some HTTP codes don't match their meaning (401 for a taken email, 400 for wrong credentials), the login request sends empty FirstName and LastName fields, and there are typos in the viewer interface (`Promis<LoadResult>`, `setLayerVisbility`, `model_[1D]`, "1 later") and inconsistent field casing.
- **Risk:** The 9-character password minimum and 243-character email limit look arbitrary (the common email maximum is 254).

### Additional feedback from the advisor and our own review
- Professor Tang suggested an immersive walkthrough of the 3D model with an avatar.
- Loading every saved model into the switcher at once won't scale as users save more models.

---

## Action items

| ID | Criterion | Issue (raised by) | Action | Owner | Priority | Due | Status |
|---|---|---|---|---|---|---|---|
| PR-01 | Feasibility | Unclear where layers come from (OPV team) | Explain how layers are made from one image, or tone down the volumetric claim | Abby | Must-fix | Week 8 | Open |
| PR-02 | Feasibility | AI model or service not named (OPV team) | Name the image-to-3D service and a backup plan if results are poor | Abby | Must-fix | Week 8 | Open |
| PR-03 | Feasibility | Two signups with the same email can collide (OPV team) | Add a unique constraint on `User.email` | Veronica | Must-fix | Week 8 | Addressed in D2 (EC-L-02) |
| PR-04 | Risk | No fallback if the AI fails or returns a broken mesh (OPV team) | Keep the uploaded image for retry; validate the returned GLB | Abby | Must-fix | Week 8 | Addressed in D2 (EC-U-03, EC-U-07, EC-U-09) |
| PR-05 | Risk | Model URL ownership unclear (OPV team) | Check ownership on the model endpoint; state how the token is sent | Veronica, Jake | Must-fix | Week 8 | Partly addressed: ownership check in D2 (EC-S-05); token transport still open |
| PR-06 | Risk | Auth details missing (OPV team) | Pick one hashing method; set token expiry; decide token storage and logout | Veronica | Must-fix | Week 8 | Open |
| PR-07 | Risk | No GLB size limit (OPV team) | Set a maximum file size and triangle count | Sophia | Worth considering | Week 8 | Addressed in D2 (EC-V-01: 50 MB / 2,000,000 triangles) |
| PR-08 | Clarity | Field names and types don't match across sections (OPV team) | Use the same names and types in Login, Switcher, and Viewer | Team | Must-fix | Week 8 | Open |
| PR-09 | Clarity | `/auth` endpoint does too much (OPV team) | Split into separate signup and login endpoints | Veronica | Worth considering | Week 8 | Open |
| PR-10 | Clarity | Complexity notes hard to read (OPV team) | List each step with its cost | Team | Worth considering | Week 8 | Open |
| PR-11 | Feasibility | Prompt-based generation idea (OPV team) | Save for future work | — | Out of scope for now | — | Deferred |
| PR-12 | Feasibility | All saved models loaded into the switcher at once (team review) | Page the model list from the backend (about 5 per page) | Jake | Worth considering | Week 8 | Addressed in D2 (Design rationale 4) |
| PR-13 | Completeness | Immersive walkthrough with an avatar (Professor Tang) | Revisit once layers and slicing work | — | Out of scope for now | — | Deferred |
| AP-01 | Completeness | 3D Model Switcher has no interface or state machine (Model Recommend team) | Add the paged model list and layers endpoints with error codes, and a state machine (Loading, Empty, Error, Ready) | Jake | Must-fix | Week 10 | Partly addressed: endpoints in the Technical Specification; state machine open |
| AP-02 | Completeness | Glue components not designed (Model Recommend team) | Give configuration (20 MB, 15 s, 50 MB, 2,000,000-triangle limits), logging, and session-token handling a data model, behavior, and interface | Veronica | Must-fix | Week 10 | Open |
| AP-03 | Completeness | Login/Sign-up "data model" is a flow diagram (Model Recommend team) | Add the User entity with fields and types, unique email, password hash, and the failed-login count EC-L-04 needs | Veronica | Must-fix | Week 10 | Partly addressed: User table in the Technical Specification; lockout fields open |
| AP-04 | Completeness | Image Uploader only says "completed in D1" (Model Recommend team) | Point to the D1 sections and add what changed (hash lookup in EC-U-05, retry rules in EC-U-08 and EC-U-09) | Abby | Worth considering | Week 10 | Partly addressed: Image Upload and AiClient boundaries in the Technical Specification |
| AP-05 | Completeness | Traceability table lists UI elements, not requirements (Model Recommend team) | Rebuild from the user stories with requirement IDs, non-functional rows, and cited design elements and EC IDs; explain every gap | Team | Must-fix | Week 10 | Addressed in the Requirements Document and Technical Specification traceability matrix |
| AP-06 | Completeness | Four rationale entries skip the required form (Model Recommend team) | Name the alternative and the accepted trade-off in each, and reference the D0 decision log | Team | Must-fix | Week 10 | Open |
| AP-07 | Completeness | Earlier must-fix items not applied (Model Recommend team) | Remove "no racing" from the signup steps, pick Argon2id or bcrypt, make sessionToken one type everywhere, explain where layers come from, and fill in owner and due date for every action item | Team | Must-fix | Week 10 | Partly addressed: owners and due dates filled in; see PR-01, PR-06, PR-08 for the rest |
| AP-08 | Clarity | HTTP codes don't match their meaning (Model Recommend team) | Use 409 for a taken email and 401 for wrong credentials; drop the empty FirstName and LastName fields from the login request | Veronica | Worth considering | Week 10 | Partly addressed: 409 and 401 in the Technical Specification; login request fields open |
| AP-09 | Clarity | Typos in the viewer interface definitions (Model Recommend team) | Fix `Promis<LoadResult>`, `setLayerVisbility`, `model_[1D]`, and "1 later"; make field casing consistent | Sophia | Worth considering | Week 10 | Addressed in the Technical Specification |
| AP-10 | Risk | Password and email limits look arbitrary (Model Recommend team) | Explain the password minimum and use the common 254-character email maximum | Veronica | Worth considering | Week 10 | Addressed in the Technical Specification (8–72 character passwords, 254-character emails) |

---

## Summary

**Most feedback:** Completeness, with 8 of 23 items (Feasibility, Risk, and Clarity had 5 each). The Transparent OPV team focused on risk around the AI service and login; the Model Recommend team focused on missing designs (the 3D Model Switcher interface, glue components, the User entity) and the traceability table.

**Top two issues from the Transparent OPV team:**
1. How the image-to-3D step actually produces the layers the viewer needs (PR-01, PR-02).
2. The missing failure and security decisions for the AI service and login (PR-04 to PR-06).

**Top two issues from the Model Recommend team:**
1. The traceability table traced UI widgets instead of requirements, so it couldn't show that each requirement is met or where the gaps are (AP-05).
2. The 3D Model Switcher interface and the glue components (configuration, logging, token handling) weren't designed yet (AP-01, AP-02).

**Top two issues we raised while reviewing the Transparent OPV team:**
1. Their scientific core (generator, AVT/PCE prediction models, and decision-gate thresholds) is not yet demonstrated or approved, and the entire pipeline depends on it.
2. Their requirements and peer review sections were still placeholders, so their traceability table and rationale could not be checked against real requirements.

**Top two issues we raised while reviewing the Model Recommend team:**
1. Confidential client text can still reach outside AI vendors, because prompt sanitization only removes emails and phone numbers.
2. Several known problems were documented as gaps but not resolved: fixes to their D0 interfaces and conflicts with their own acceptance criteria.