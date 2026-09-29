# Assignment 4 - User Stories and Use Cases
## Stakeholder Map
Primary: Those who are opening and using themselves.
- Medical Student
- Mechanical Engineer
- Geologist
- Emergency Response Team Member

Secondary: People affected by the system without using it directly.
- Instructor / Professor
- Data Provider

Hidden: Accessibility needs or downstream teams, those concerns heard about after deployed that are easy to miss.
- Low-vision / screen-reader user
- Downstream tools or systems

## User stories

US-01 - Veronica Malusky:
- stakeholder category: Medical Student

As a medical student I want to be able to have a 3D model of the human brain and view the bisectional of the brain so that I can view an accurate model and volumetric model to use for my studies.

INVEST: The Medical student is indepent from other Stakeholder, what they get out of it is negotiable in terms of it could be actual patient models or pre made examples, This is of course a vauable tool for diagnosing and learing, This is also a small part of what the app should be able to do. Of course, it will be testable for if this can work. There are also no listed UI elements.

US-02 - Sophia Munoz:
- stakeholder category: Mechanical Engineer

As a mechanical engineer at an automotive company, I want to be able to access a 3D model of our car so I can investigate the transmission and powetrain to zero in on a mechanical issue that has appeared.

INVEST:The Mechanical Engineer is indepent from other Stakeholder, what they get out of it is negotiable in terms of it could be actual engine models or pre made examples of some basic engines, This is of course a vauable tool for learing about new engines, This is also a small part of what the app should be able to do. Of course, it will be testable for if this can work. There are also no listed UI elements.

US-03 - Abby Kraft 
- stakeholder category: Geologist

As a geologist I want to be able to have an effective and accessible way to have a 3D model that can express the different layers of rock types, sediments, and structural boundaries. I want this so I can better understand and utilize the model to further my studies and research as well as educating those about geology.

INVEST:The Geologist is indepent from other Stakeholder, what they get out of it is negotiable in terms of it could be highly detailed or not very detailed, This is of course a vauable tool for learing about rocks and layers of the earth, This is also a small part of what the app should be able to do. Of course, it will be testable for if this can work and is acurate. There are also no listed UI elements.

US-04 - Jake Martin
- stakeholder category: Emergency response team member

As a member of an emergency response team being able to quickly see a 3d model of a building allows me to quickly grasp how a building should be searched as well as seeing rooms that are not visible from the outside of the building in situations such as an office building fire. 

INVEST:The Emergency response team member is independent from other Stakeholders, what they get out of it is negotiable in terms of it could be highly detailed or not very detailed IE inculding things like air ducts or not, This is of course a vauable tool for potentailly saving lives durring a search, This is also a small part of what the app should be able to do, of course it will be testable for if this can work and is acurate. There are also no listed UI elements.

US-05 - Abby Kraft
- stakeholder category: Low-vision or Screen-reader Student

As a low-vision student who uses a screen reader, I want to be able to get the anatomical or structural information shown in a 3D model as text through a screen reader and through the keyboard, so like my fellow students, I can complete the same coursework independently without needing someone to describe the model to me. 

INVEST: The low-vision or screen-reader student is independent from other stakeholders, what is gotten out of it is negotiable in terms of it could be detailed and complex or possibly as simple as a keyboard shortcut. This would be a very valuable tool since a whole group of users can't use the application at all as well as certain accessibility obligations. As for estimable the scope is the same information that a person with normal functionality sight can get from viewing the model so the team can size it. This is still just one task, which will be testable and accurate.

## Use cases

#### UC-01 - Visualize Volumetric Data

Primary Actor: User (researcher, medical student, professor, etc.)

Secondary Actors: 

- WebGL rendering
- AI to interpret data and obtain a 3D model
- Storage system

Hidden 
Preconditions:

- User has access to the application
- WebGL is supported by the users browser and application
- User has a volumetric model

Main Success Flow:

1. User writes prompt into AI to get a volumetric model
2. System interprets users prompt and obtains model for user
2. User approves the model that gets generated by the AI
4. System renders model into WebGL into a 3D model
5. User is able to rotate, zoom in, and move model on screen
6. User selects bisectional (slice of model) option
7. System renders the volumetric data to display on the screen to the user

Alternate Success Flow:

1. User uploads volumetric model
2. System verfies model is valid to be rendered
3. User selects the model to be rendered in the WebGL
3. System renders model and displays 3D model
3. User is able to rotate, zoom in, and move model on screen and highlights sections they want to investigate
3. System registers selected portion of the model and AI will display information about the highlighted portion of the model

Exception Flow:

1. User uploads invalid model
2. System displays error message and rejects the file 
3. User gets the ability to reupload a valid model that can be rendered in the WebGL

Post Condition:

Model is able to be rendered successfully and manipulated by the user as desired. AI is able to successfully create a model that is valid for the WebGL, and also display information regarding the model if prompted by the user




_Last updated: 2026-09-28_
