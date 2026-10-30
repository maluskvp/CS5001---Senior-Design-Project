<div align="center">

# Requirements Document

**Team AVJS** · Web-Based Interactive Model Visualizer

Abby Kraft · Veronica Malusky · Jake Martin · Sophia Munoz

*What the system must do, and for whom.*

**Version 0.1** · Last updated 2026-10-29

</div>

---

# 📝 Version and Change Log 

| Version | Date | Change | Author |
|---|---|---|---|
| 0.1 | 2026-10-29 | Initial draft | Team |

---

## 🎯 Purpose and Scope

**Purpose:** Have a system that allows for interactive viewing of 3D models that can have volumetric data to view the layers of the model

**In Scope:**

- Individual user experiences
- Image -> 3D model rendering
- Handle volumetric models
- Interact with the model
- Pick what saved model the user wants to use

**Out of Scope:**

- Prompted-AI generative 3D models
- User interactive avatars to walk through models
- Shared models between users

**Goal:** Create a web-based model visualizer that allows users to quickly change layers of the model as well as create models based on images uploaded via AI.

---

## 👥 Stakeholders and User Goals
<!-- from week 4 User_Stories.md -->

| Type | Stakeholder | User Goal | Source Story |
|---|---|---|---|
| Primary | Medical student | View a 3D brain model and its cross-sections for study | [US-01](User_Stories.md#us-01--medical-student) |
| Primary | Mechanical engineer | Inspect a car's transmission and powertrain in 3D | [US-02](User_Stories.md#us-02--mechanical-engineer) |
| Primary | Geologist | Explore rock, sediment, and structural layers | [US-03](User_Stories.md#us-03--geologist) |
| Primary | Emergency response team member | Quickly see a building's floors and hidden rooms | [US-04](User_Stories.md#us-04--emergency-response-team-member) |
| Hidden | Low-vision or screen-reader student | Get the model's information as text, by keyboard | [US-05](User_Stories.md#us-05--low-vision-or-screen-reader-student) |
| Secondary | Instructor / professor | Open a saved model in class and switch layers live without re-uploading it | No dedicated story |
| Secondary | Data provider | Images they supply are used only to generate that user’s model and aren’t shared | No dedicated story |
| Hidden | Downstream tools or systems |The model API, database, and file storage recieve well-formed requests that are within their limits and stored GLB files are validated. | No story (D1 API contract)|
| Hidden | Account holders (cybersecurity) | Protected information regarding accounts, passwords, and images stay secure and private from other users.| No story (D0 component C1)|

### User Stories

The Week 4 [User Stories](User_Stories.md) (US-01 to US-05) describe specific stakeholders. The stories below restate them as general system needs so that every requirement (functional/non-funcitonal) traces to at least one story.

| ID | As a user, I want... | So that... | Requirements | Based on |
|---|---|---|---|---|
| US-N-01 | to log in and view my saved models | my work is kept in my own account between visits | REQ-01 | All stakeholders |
| US-N-02 | to sign up if I don't have an account | I can start saving my own models | REQ-02 | All stakeholders |
| US-N-03 | to upload an image and have it turned into a 3D model | I don't need 3D modeling skills to get a model | REQ-03, REQ-07 | US-01 to US-04 |
| US-N-04 | to interact with my model in the browser | I can inspect it from any angle and see its layers | REQ-05, REQ-08, REQ-10, NFR-05, NFR-06, NFR-07 | US-01 to US-04 |
| US-N-05 | to pick which of my models to view | I can switch between models without re-uploading them | REQ-04 | US-01 to US-04 |
| US-N-06 | the system to handle many saved models | my model list stays fast as it grows | NFR-01 | All stakeholders |
| US-N-07 | the system to stay responsive while it works on tasks | I can keep using the app while a model is generated or loaded | NFR-02, NFR-04 | All stakeholders |
| US-N-08 | my information and connection to be secure | other users can't see my account, images, or models | NFR-03 | Account holders (cybersecurity) |

---

## 📖 Glossary

_Defining every Domain Term and Role Name_

| Term | Definition |
|---|---|
| **User** | Person using the WebGL for any desired goal |
| **Model** | 3D generated model |
| **GLB** | File type for a 3D model |
| **Layer** | Structured subset of a 3D dataset that contains values for a specific property at defined spatial locations |
| **Triangle Count** | Number of triangular faces in a mesh representation of a 3D model |
| **WebGL** | The browser's built-in graphics API that draws 3D graphics using the device's GPU, without plugins |
| **Image Upload** | Component in the program that will render a 3D model from an image |
| **Login / Sign-up** | Component in the program that has user login to use the features in the program |
| **3D Model Switcher** | Component in the program that contains user saved 3D models and allows the user to swap between the models |
| **3D Model Viewer** | Component in the program that renders the selected model in the browser so the user can rotate, zoom, and switch layers, and shows an error message if the model can't be displayed |
| **AI 3D Model API** | The API that will be used to render the image applies in the Image Upload component into a 3D model for the user to view in the 3D Model Viewer |
| **Generation Job** | A record of one attempt to turn an uploaded image into a 3D model through the AI 3D Model API. It tracks the job's status (queued, running, succeeded, or failed), error code, attempt number, and start/finish times. One upload can have several generation jobs if a failed attempt is retried, and only a successful job produces a Model |
| **Session Token** | Token that will authenticate the user so the connection can be secure and enable them to use the program |
| **Image Hash** | A fingerprint calculated from an uploaded image's file contents (e.g. SHA-256). Two identical images always produce the same hash, so the system can recognize a re-upload of the same image and return the existing model instead of having a new AI generation. Stored in the Upload table's `image_hash` field |

---

## ⚙️ Functional Requirements

| ID : REQ - ## | Statement | MoSCoW Priority | Source Story | Verification Method |
| :--- | :--- | :---: | ---: | :---: |
| REQ-01 | The system shall prompt user for login | Must Have | US-N-01  | Test |
| REQ-02 | The system shall allow new users to sign up | Must Have | US-N-02 | Test |
| REQ-03 | The system shall allow users to upload images and turn the uploaded images into 3D models | Must Have | US-N-03 | Test |
| REQ-04 | The system shall store individual user models in the switcher to allow the user to choose what model they want to view | Must Have | US-N-05 | Test |
| REQ-05 | The system shall allow the user to select a model stored in the switcher and display it in a WebGL | Must Have | US-N-04 | Demonstration |
| REQ-06 | The system shall enable user to view multiple layers of the model and take slicings to view the full volume of a inputted model | Should Have | [US-01](User_Stories.md#us-01--medical-student) | Demonstration |
| REQ-07 | The system shall allow the user import an already rendered 3D GLB model file to the system | Should Have | US-N-03 | Test |
| REQ-08 | The system shall let the user rotate, zoom, pan, and reset the camera on a displayed model using a mouse, touch, or keyboard | Must Have | US-N-04 | Demonstration |
| REQ-09 | The system shall show or hide an individual layer of the displayed model without reloading the model file | Must Have | [US-01](User_Stories.md#us-01--medical-student), [US-03](User_Stories.md#us-03--geologist) | Test |
| REQ-10 | The system shall show an error message with a Retry or Back option when a model fails to load, times out, exceeds the size limit, or cannot be rendered in the user's browser | Should Have | US-N-04 | Test |
| REQ-11 | The system shall provide a text description of the displayed model (name, visible layers, and vertex count) that a screen reader can read, and allow all viewer controls to be used by keyboard alone | Should Have | [US-05](User_Stories.md#us-05--low-vision-or-screen-reader-student) | Test |

---

## 📏 Non-Functional Requirements

| Requirement | ID : NFR - ## | Statement | MoSCoW Priority | Source Story | Verification Method |
| :--- | :--- | :--- | :---: | ---: | :---: |
| | NFR-01 | The system shall list a user's saved models, 5 per page, within 2 seconds for a user with up to 500 saved models | Should Have | US-N-06 | Test |
| | NFR-02 | The system shall keep the 3D Model Switcher and 3D Model Viewer usable while a generation job runs, and show the new model in the list within 5 seconds of the job finishing | Should Have | US-N-07 | Test |
| | NFR-03 | The system shall store passwords only as salted hashes, reject any model or upload request without a valid session token (401), and lock an account for 15 minutes after 5 failed logins within 15 minutes | Must Have | US-N-08 | Test |
| | NFR-04 | The system shall display a selected model of up to 50 MB within 15 seconds on a typical home or campus connection | Must Have | US-N-07 | Test |
| | NFR-05 | The system shall render a displayed model of up to 500,000 triangles at 30 frames per second or more on a mid-range laptop in Chrome, Firefox, and Safari | Should Have | US-N-04 | Test |
| | NFR-06 | The system shall let a first-time user show or hide a layer within 30 seconds, with at least 5 of 6 test users succeeding on the first try | Should Have | US-N-04 | Demonstration |
| | NFR-07 | The system shall never leave a blank canvas: every failed model load shows an error message within 15 seconds, and a lost WebGL context is recovered automatically once | Must Have | US-N-04 | Test |


## 📌 MoSCoW Summary

**✅ Must:**

_Non-negotiable. The project fails without it._

- Allow for individual user experience and store user models
- User must be able to view the 3D model
- User must be able to switch between their saved models
- User must be able to upload an image that then gets rendered as a 3D model
- Rotate, zoom, and pan a model, show or hide individual layers


**👍 Should:**

_Important, but the project can ship without it if needed._

- Allow a user to import an already created GLB file
- Allow a user to upload an image and continue working on the program while the AI renders the image into a 3D model
- Implement volumetric data rendering so we can take cuttings of a model
- Clear error messages when a model can't load, screen-reader text description and keyboard controls

**💡 Could:**

_Desirable, low impact if left out. First to cut under time pressure._

- Allow sections of the model to contain notes and store that information and display it on model load

**🚫 Won't (this release):**

_Explicitly out of scope for this release. Document it so it doesn't quietly
creep back in._

- Prompt-based model generation
- Immersive avatar walkthrough
- Model sharing between users.

---

## 🔒 Inherited Constraints

| ID | Category | Constraint | Source |
|---|---|---|---|
| CON-01 | Economic | No budget for paid hardware or software. Limited to UC Labs, personal laptops and phones, and free tools | [Constraints Essay](Constraints_Essay.md#-economic) |
| CON-02 | Professional | Team is all computer science majors and needs guidance on model rendering and automation | [Constraints Essay](Constraints_Essay.md#-professional) |
| CON-03 | Legal | The project must comply with applicable property rights, software licensing requirements, and data privacy rules | |

---

## ❓ Assumptions and Open Questions

| ID | Type | Description | Owner | Date |
|---|---|---|---|---|
| Q-01 | Open Question | Which AI image-to-3D service will be used and does it return GLB. What is the backup plan if results are poor? (Peer review PR-02) | Abby Kraft | 2026-10-28|
  | Q-02 | Open Question | Will the AI handle rendering of volumetric models as well as regular 3D models | Veronica Malusky | 2026-10-28 |
| Q-03 | Open Question |  Should we handle glb files or just volumetric models files (.nrrd, .nii.gz, .vdb | Veronica Malusky | 2026-10-28 |
| Q-04 | Open Question |  What generation method is needed to produce volumetric models | Veronica Malusky | 2026-10-28 |
| A-01 | Assumption | The AI will render volumetric data and we will have some way of rendering whether or not the model the user choses has volume to it | Veronica Malusky | 2026-10-28 |
| A-02 | Assumption | The uploaded images show a recognizable object, and if there is no clear object, it may fail to generate (EC-U-07) | Abby Kraft |2026-10-28 |

---

<sub>Related: [User Stories](User_Stories.md) · [Technical Specification](Technical_Specification.md) · [Design Diagrams](Design_Diagrams/)</sub>