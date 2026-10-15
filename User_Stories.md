<div align="center">

# User Stories and Use Cases

**Team AVJS** · Web-Based Interactive Model Visualizer · Assignment 4

</div>

---

## 🗺️ Stakeholder Map

| Type | Who | Stakeholders |
|---|---|---|
| **Primary** | People who open and use the system themselves | Medical student · Mechanical engineer · Geologist · Emergency response team member |
| **Secondary** | People affected by the system without using it directly | Instructor / professor · Data provider |
| **Hidden** | Accessibility needs or downstream teams; concerns that are easy to miss and often only heard about after deployment | Low-vision / screen-reader user · Downstream tools or systems |

---

## 📖 User Stories

| ID | Stakeholder | Author |
|---|---|---|
| [US-01](#us-01--medical-student) | 🧠 Medical student | Veronica Malusky |
| [US-02](#us-02--mechanical-engineer) | ⚙️ Mechanical engineer | Sophia Munoz |
| [US-03](#us-03--geologist) | 🪨 Geologist | Abby Kraft |
| [US-04](#us-04--emergency-response-team-member) | 🚒 Emergency response team member | Jake Martin |
| [US-05](#us-05--low-vision-or-screen-reader-student) | 🦯 Low-vision or screen-reader student | Abby Kraft |

### US-01 · Medical Student
*Author: Veronica Malusky*

> As a medical student, I want a 3D model of the human brain and to view a bisection of the brain, so that I have an accurate volumetric model to use for my studies.

**INVEST:** The medical student is independent from other stakeholders. What they get out of it is negotiable: it could be actual patient models or premade examples. It is a valuable tool for diagnosing and learning, and it is a small part of what the app should be able to do. It will be testable for whether it works. There are no listed UI elements.

### US-02 · Mechanical Engineer
*Author: Sophia Munoz*

> As a mechanical engineer at an automotive company, I want to access a 3D model of our car so I can investigate the transmission and powertrain to zero in on a mechanical issue that has appeared.

**INVEST:** The mechanical engineer is independent from other stakeholders. What they get out of it is negotiable: it could be actual engine models or premade examples of some basic engines. It is a valuable tool for learning about new engines, and it is a small part of what the app should be able to do. It will be testable for whether it works. There are no listed UI elements.

### US-03 · Geologist
*Author: Abby Kraft*

> As a geologist, I want an effective and accessible 3D model that can express the different layers of rock types, sediments, and structural boundaries, so I can better understand and use the model to further my studies and research, as well as educate others about geology.

**INVEST:** The geologist is independent from other stakeholders. What they get out of it is negotiable: it could be highly detailed or not very detailed. It is a valuable tool for learning about rocks and the layers of the earth, and it is a small part of what the app should be able to do. It will be testable for whether it works and is accurate. There are no listed UI elements.

### US-04 · Emergency Response Team Member
*Author: Jake Martin*

> As a member of an emergency response team, being able to quickly see a 3D model of a building lets me quickly grasp how the building should be searched, as well as see rooms that are not visible from outside, in situations such as an office building fire.

**INVEST:** The emergency response team member is independent from other stakeholders. What they get out of it is negotiable: it could be highly detailed or not very detailed, for example including air ducts or not. It is a valuable tool for potentially saving lives during a search, and it is a small part of what the app should be able to do. It will be testable for whether it works and is accurate. There are no listed UI elements.

### US-05 · Low-Vision or Screen-Reader Student
*Author: Abby Kraft*

> As a low-vision student who uses a screen reader, I want to get the anatomical or structural information shown in a 3D model as text, through a screen reader and the keyboard, so that, like my fellow students, I can complete the same coursework independently without needing someone to describe the model to me.

**INVEST:** The low-vision or screen-reader student is independent from other stakeholders. What they get out of it is negotiable: it could be detailed and complex, or as simple as a keyboard shortcut. It is very valuable, since without it a whole group of users can't use the application at all, and it supports accessibility obligations. It is estimable because the scope is the same information a sighted user gets from viewing the model, so the team can size it. It is still a single task, and it will be testable and accurate.

---

## 🔄 Use Cases

### UC-01 · Visualize Volumetric Data

| | |
|---|---|
| **Primary actor** | User (researcher, medical student, professor, etc.) |
| **Secondary actors** | WebGL rendering · AI to interpret data and produce a 3D model · Storage system |

**Preconditions**
- The user has access to the application.
- WebGL is supported by the user's browser and the application.
- The user has a volumetric model.

#### ✅ Main Success Flow
1. The user writes a prompt to the AI to get a volumetric model.
2. The system interprets the user's prompt and obtains a model for the user.
3. The user approves the model generated by the AI.
4. The system renders the model in WebGL as a 3D model.
5. The user can rotate, zoom in, and move the model on screen.
6. The user selects the bisection (slice of model) option.
7. The system renders the volumetric data and displays it on screen.

#### 🔀 Alternate Success Flow
1. The user uploads a volumetric model.
2. The system verifies the model is valid to be rendered.
3. The user selects the model to be rendered in WebGL.
4. The system renders and displays the 3D model.
5. The user can rotate, zoom in, and move the model on screen, and highlights sections they want to investigate.
6. The system registers the selected portion of the model, and the AI displays information about the highlighted portion.

#### ⚠️ Exception Flow
1. The user uploads an invalid model.
2. The system displays an error message and rejects the file.
3. The user can re-upload a valid model that can be rendered in WebGL.

**Postcondition:** The model renders successfully and the user can manipulate it as desired. The AI successfully creates a model that is valid for WebGL, and displays information about the model if the user asks.

---

<sub>Last updated 2026-10-14</sub>