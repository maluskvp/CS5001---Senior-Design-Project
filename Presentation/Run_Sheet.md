<div align="center">

# Presentation Run Sheet
 
**Team AVJS** · Web-Based Interactive Model Visualizer
 
Abby Kraft · Veronica Malusky · Jake Martin · Sophia Munoz
 
*Fall Design Presentation · 10 minutes presenting + 5 minutes of questions*
 
Last updated 2026-11-19
</div>

---
 
## Run Sheet
 
| Slide | Title | Planned start | Seconds | Speaker | Hand-off line |
|---|---|---:|---:|---|---|
| 1 | Web-Based Interactive Model Visualizer | 0:00 | 30 | Jake | "…interacted with through the browser 3D viewer." |
| 2 | Team and Advisor | 0:30 | 30 | Jake | "…Director of UC's eXtended Reality (XR) Lab." |
| 3 | Project Abstract | 1:00 | 55 | Jake | "…privacy, cost, and performance. Veronica will now walk through who we're building this for." |
| 4 | User Stories | 1:55 | 60 | Veronica | "…keep using the app while a model is generated or loaded." |
| 5 | Design Diagram – Entire System | 2:55 | 80 | Veronica | "…and that's the end of our design diagram. Abby will cover the constraints that shaped it." |
| 6 | Project Constraints | 4:15 | 60 | Abby | "…evaluate AI-service terms." |
| 7 | Progress and Evidence | 5:15 | 55 | Abby | "…99 test cases written." |
| 8 | Accomplishments by End of Term | 6:10 | 55 | Abby | "…complete designs of systems and models. Sophia will explain how we're dividing the work." |
| 9 | Division of Work | 7:05 | 50 | Sophia | "…code review, rehearsal, and Expo setup." |
| 10 | Expo Demo | 7:55 | 75 | Sophia | "…rotate, zoom, and switch controls respond." |
| 11 | Thank You | 9:10 | 6 | Sophia | "Thank you for listening. Does anyone have any questions?" |
| | **End** | **9:16** | | | |

---

## Speaking Roles

| Role | Member |
|---|---|
| Opening (slides 1–3) | Jake Martin |
| Technical design (slides 4–5) | Veronica Malusky |
| Constraints and progress (slides 6–8) | Abby Kraft |
| Work division, demo, and close (slides 9–11) | Sophia Munoz |
| Timekeeper | Abby Kraft |

---

## Anticipated Questions

| # | Question | Owner | Short answer |
|---|---|---|---|
| 1 | What happens to uploaded images? | Abby | They're kept in private file storage under the user's account only until the model is saved, or 7 days after a failed generation, so a failure can be retried. We store a hash of the image so a repeat upload reuses the existing model instead of paying for a new generation. |
| 2 | How will generation cost be controlled? | Abby | We'll use a pay-per-request service on its free tier, the image hash prevents paying twice for the same image, and the 20 MB upload limit keeps requests small. |
| 3 | What if generation takes several minutes? | Abby | The job runs on the server, so the user can keep using the app or even close the tab. If the AI service doesn't respond within 60 seconds, it retries once, then shows an error with a retry option. |
| 4 | Which AI service are you using? | Abby | Our advisor told us not to build our own pipeline, so we're comparing existing image-to-3D models such as TRELLIS. We'll pick one and document a backup by mid-January (task T1). |
| 5 | How do you keep users' models private? | Veronica | Every request needs a valid session token, passwords are stored only as salted hashes, and a request for another user's model returns "not found" so model IDs can't be guessed. |
| 6 | Why run the viewer in the browser instead of an app? | Sophia | Nothing to install, it works on any laptop, and rendering on the user's device means smooth rotation with no server delay and no GPU servers for us to pay for. |
| 7 | How will the viewer handle large or complex models? | Sophia | Models over 50 MB or 2 million triangles are rejected before downloading. Our target is showing a 50 MB model within 15 seconds at 30 frames per second, and we have test cases for both. |
| 8 | Is the viewer accessible? | Sophia | Yes. All controls work by keyboard, and screen-reader users get a text description of the model and its visible layers. This came from our accessibility user story. |
| 9 | How do you know the system works? | Veronica | Our test plan has 99 test cases tied to specific requirements, with measurable pass and fail thresholds and at least three cases per must-have requirement. |
| 10 | Why are you considering Gaussian splatting instead of meshes? | Sophia | Splats look more realistic, some image-to-3D models output them directly, and they render in Three.js, which we already use. We haven't decided yet whether to support splats only or splats plus a mesh fallback. |

---

## Rehearsal Log

| Date | Attendees | Total time | Notes and changes |
|---|---|---:|---|
| 11-17-2026 | Abby Kraft, Veronica Malusky, Sophia Munoz, Jake Martin | 10:22 | Proper handling of the introduction page |
| 11-19-2026 | Abby Kraft, Veronica Malusky, Sophia Munoz, Jake Martin | 8:35 | Final Runthrough |

---

## Peer Reviews

Our team reviewed three other teams' recorded presentations, chosen from teams listed close to ours in the Senior Design Team Table.
 
| Team / project | Reviewer | Review |
|---|---|---|
| TradeIO | Team | Even at a very quick glance, it is clear the slides are highly polished and very modern! That said, your video does not appear to be working right now; it is just 5 seconds of white. It is overall a very interesting idea, and I think it would be a great tool to get people started in the world of stock trading. I also like that you say directly that this tool will not let people actually trade, as it draws a clear line between what it can and cannot do. I do think it is very important to tell users that stock trading involves risk and that AI can make mistakes. Overall great work, and I hope to see it at Expo! |
| Data-Day | Team | This is definitely an interesting idea! I personally have not spent much time working with this kind of thing, but making things more accessible is always a worthwhile endeavor. I also think you were clear overall in your video, which is always a plus. For Expo, you might want a plan in case the Wi-Fi is bad. When Expo has been on campus in the past, UC_Secure has been a little sketchy, and when it has been off campus, Wi-Fi sometimes is not provided. It might be best to connect to a hotspot so you know you will have a solid connection. |
| SkillForge | Team | This is absolutely an interesting idea! One thing I am interested to see is how this training compares to more traditional robot training methods. I can see the value in how quickly it allows for rapid development of robot movement models. I also think this is a great academic project with plenty of CS merit! |