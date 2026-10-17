# Tilly · A little space

First working source version for an iPad mini planner. Warm paper, taupe, sage and blush; Life and Work filters; combined Today; daily, weekly and monthly calendar views; tasks and shopping; recurring daily tasks; goals; routines; dated brain dump; dated typed and handwritten notebooks; export/import backup; tap-to-talk with spoken replies and typed command fallback.

## Current status

- Source and logic checks complete. Not deployed and not connected to real calendars.
- Browser visual QA and real iPad Pencil/microphone tests remain outstanding.
- No calendar events have been created or changed during development.
- Typed notes, tasks, goals, habits and vector ink save in localStorage on the device/browser. They do not sync across devices. Export backups in Settings. Clearing Safari data removes them.
- Calendar integration is implemented using Microsoft authorization code + PKCE and Graph. It requires a Microsoft app registration and live testing with both accounts.
- This version has no app login or cloud notebook storage. Outlook uses its own sign-in. No personal records are bundled in the source. Add a private login and durable per-user storage before treating this as the final app.
- iCloud is not integrated. Outlook invitations accepted into connected calendars appear as calendar events, subject to account access.
- Only owned/writable calendars support event changes. Calendar refreshes every minute while open, with automatic refresh paused during note entry and on the handwriting page. It refreshes on return to the app; it does not run in the background when closed.
- Voice uses browser speech recognition, not a general AI agent. Speech processing may use the browser vendor's service. Only microphone sessions started by a tap are used. Stop or hide the app to end listening. Safari support and microphone permissions must be tested on the actual iPad. Keyboard dictation works as a fallback in the command box.
- No offline service worker yet; an internet connection is needed to load the app and access calendars/voice services.

## GitHub and Netlify

1. Create a **private** GitHub repository named `tilly-planner`. Initialize with a README so it has a `main` branch.
2. Put this folder's files in the repository root (index.html must be at root).
3. In Netlify choose Add new project → Import an existing project → GitHub → tilly-planner.
4. No build command. Publish directory: `.`. The included netlify.toml supplies configuration.
5. `.netlify/`, `.env` and node_modules are ignored; do not commit secrets or local Netlify project identity.
6. Configure site-load protection or app login before using on a shared device or adding cloud storage. Current notebook data is browser-local and not protected by an app login.
7. In Safari visit the deployed HTTPS URL → Share → Add to Home Screen.

## Microsoft calendar setup

Register a dedicated application in Microsoft Entra (do not reuse an unrelated app without checking its owner/settings).

- Supported accounts: accounts in any organisational directory **and personal Microsoft accounts**.
- Platform: Single-page application (SPA).
- Redirect URI: the exact deployed origin plus `/`, e.g. `https://your-planner.netlify.app/`.
- Microsoft Graph delegated permissions: `User.Read`, `Calendars.ReadWrite`; consent also requests openid, profile and offline_access.
- Do not create or paste a client secret into this app. A public SPA uses PKCE.
- Copy the Application (client) ID into Connections & settings and save.
- Connect Life with Personal Outlook; connect Work with Work DM Outlook. The app rejects connecting the same account to both sections.
- Account policy may require administrator consent. Do not bypass organisation policy.
- OAuth access and refresh tokens stay in sessionStorage. They are not in exports or the repository. Reconnect when Microsoft requires a new interactive sign-in or browser session storage is lost.
- Graph reads accessible calendars, including calendars marked read-only; only writable calendars appear for appointment creation.

## Voice examples

- “Add milk to my shopping list” → previews task, then “yes” saves.
- “Add a work task to call Paul tomorrow” → previews task for tomorrow.
- “Note remember the keys” → saves a dated notebook note.
- “Show tomorrow”, “open goals”, “show work”.
- “What’s on today?” → refreshes Outlook then reads the schedule.
- “Add a work appointment tomorrow at 10am for one hour called Team meeting” → opens the event form; check calendar and say “save appointment” or “cancel”.
- “Stop listening”.

Appointments currently require an explicit Life/Work section, today/tomorrow or ISO date, AM/PM time, duration and title for voice parsing. The parser asks for missing information rather than guessing. Editing/deleting existing appointments is available by tapping the event; voice editing and general conversational AI are not implemented.

## Validation before real use

Use a dedicated test calendar in each account, not invitations to other people:

- Create in Outlook → Refresh in planner → correct section, calendar and time.
- Create in planner → check Outlook and Apple Calendar.
- Edit and delete a test appointment in planner → check Outlook; include recurring occurrence and all-day event cases. Editing an all-day event here converts it to a timed event and the form explains this.
- Verify daylight-saving dates and both iPad orientations.
- Verify Pencil strokes, erasing, undo, refresh persistence and backup restoration.
- Test microphone permission denial, command misrecognition, task confirmation/cancellation and voice stop.
- Verify duplicate accounts are rejected and token expiry produces a reconnect prompt.

Developer checks passed: JavaScript syntax; page rendering with a DOM stub; year and leap-day rollover; HTML escaping; voice task confirmation/cancellation, Life/Work routing, dictation, ambiguous calendar request rejection, disconnected calendar error and date navigation. These are not a substitute for browser/device and live OAuth testing.
