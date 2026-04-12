# Team Handoff Chain

The full agent workflow from user request to ship.

```
USER ARRIVES (idea, problem, broken code, vague request — anything)
    ↓
ARCHITECT AGENT [MODE: DIAGNOSE]
  Reads request → identifies what's needed → recommends team + order
  Asks: "Activate Demo Agent? (Y/N)"
  Asks: "Deploy when done? (Y/N)"
  Flags bottlenecks before any agent starts work
    ↓
ARCHITECT AGENT [MODE: DESIGN]
  Produces: system design, auth model, security model, testing strategy
  Hands off to all agents with specific briefs
    ↓
  ┌─────────────────────────────────────┐
  │                                     │
UI/UX AGENT [MODE: FLOW → WIREFRAME → DESIGN → SPEC]
  Produces: user flows, wireframes,     │
  visual design, component specs,       │
  auth UX for all states                │
  Hands to: Full Stack Agent            │
                                        │
SECURITY AGENT [MODE: DESIGN-REVIEW]   │
  Reviews auth model + security design  │
  Produces: auth constraints for        │
  Full Stack Agent                      │
  │                                     │
  └─────────────────────────────────────┘
    ↓ (both complete before build starts)
FULL STACK AGENT [MODE: BUILD]
  Receives: Arch doc + UI/UX specs + Security auth constraints
  Produces: working code + unit tests + auth implementation
  ← DEMO AGENT [MODE: CAPTURE] runs silently alongside
    Captures screenshot after each phase completes
    ↓
LOCAL REVIEW AGENT [MODE: REVIEW]
  Starts app locally → opens browser → takes screenshot
  Waits for human: LGTM | FEEDBACK | STOP
  Chain pauses here until human responds
    ↓
  ┌─────────────────────────────────────┐
  │                                     │
QA AGENT [MODE: TEST-RUN]              SECURITY AGENT [MODE: CODE-AUDIT]
  Tests all flows including auth        Audits auth implementation + code
  Produces: bug report                  Produces: vulnerability report
  Hands to: Full Stack Agent            Hands to: Full Stack Agent
  │                                     │
  └─────────────────────────────────────┘
    ↓ (Full Stack Agent fixes all findings)
SECURITY AGENT [MODE: LAUNCH-AUDIT]
  Final auth + security sign-off
  Verdict: APPROVED | APPROVED WITH CONDITIONS | BLOCKED
    ↓
DEMO AGENT [MODE: RENDER]
  Assembles captured screenshots
  Renders 60-second MP4 via Remotion
  Atari CRT aesthetic applied to video
  Output: [project-name]-demo.mp4
    ↓
DEVOPS AGENT [MODE: DEPLOY]
  Asks: Vercel + Cloudflare Workers? (default) or other?
  Deploys frontend → Vercel
  Deploys backend → Cloudflare Workers
  Writes README with screenshot + live links
  Commits and pushes everything
    ↓
SHIP
  ✓ Live URL
  ✓ 60-second demo video
  ✓ README with screenshot
  All from one prompt
```
