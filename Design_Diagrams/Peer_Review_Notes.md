# Peer Design Review Notes

**Project:** Web-Based Interactive Model Visualizer
**Goal:** Create a web-based model visualizer that allows users to quickly change layers of the model as well as create models based on images uploaded via AI.

**Our team (reviewed):** Abby Kraft, Veronica Malusky, Jake Martin, Sophia Munoz
**Reviewing team:** Kyle Russell, Shashank Sathiyanarayanan, Jaxon Polo, Arleen Monteiro, Nyla Spencer (Transparent OPV team)
**Additional feedback:** Professor Ming Tang (advisor)

---

## What the reviewers liked

- Algorithm entries were easy to follow.
- Input is checked on both the front end and the server.
- The 3D Model Viewer cancels stale loads and frees old materials.
- Loading the model list separately from the GLB file keeps the switcher fast.

---

## Feedback received

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

---

## Summary

**Most feedback:** Risk, mainly around the AI service and the login flow. Feasibility was close behind, centered on how the AI step produces layers.

**Top two issues from the review:**
1. How the image-to-3D step actually produces the layers the viewer needs (PR-01, PR-02).
2. The missing failure and security decisions for the AI service and login (PR-04 to PR-06).

**Top two issues we raised while reviewing the Transparent OPV team:**
1. Their scientific core (generator, AVT/PCE prediction models, and decision-gate thresholds) is not yet demonstrated or approved, and the entire pipeline depends on it.
2. Their requirements and peer review sections were still placeholders, so their traceability table and rationale could not be checked against real requirements.