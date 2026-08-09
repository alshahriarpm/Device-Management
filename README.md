# Device Management

Interactive HTML prototype for **Shomvob HRIS — Device Management & Device-Change Approval**.

Self-contained, single-file demo (`device-management-prototype.html`) — no backend, no build step. Open it directly in a browser. Web (Admin) and Mobile (Employee) views share one in-memory store, so actions on one side are reflected live on the other.

## What it demonstrates

Binding each employee login to approved devices, with admin oversight for device changes.

- **Mobile app**: hardware-bound device ID with a hard block at the login gate. A new/unrecognized device forces a device-change request before the employee can log in.
- **Web portal**: no reliable hardware fingerprint in a browser, so untrusted browsers are challenged via OTP step-up or a hard admin-gate (configurable), backed by a signed trust cookie.
- **Registration limit vs. session limit**: how many devices can be *on file* per employee is a separate control from how many can be *logged in concurrently* — the session limit is what actually stops shared/buddy logins.
- **Admin review queue**: approve/reject device-change requests, with urgency flags (Lost/Stolen/Damaged/Upgraded/Other), per-employee change-frequency signals, and a self-request lockout (an admin can't approve their own request — routes to a platform-assisted/break-glass path instead).
- **Device registry actions**: Reset (clears binding so next login registers fresh, history preserved), Block (terminates active session immediately), Unblock.
- **Audit trail**: full per-employee device history/timeline for every action (register, approve, reject, reset, block, unblock).

## Structure

- `#surface-web` — Admin portal: sidebar nav + 3 tabs
  - **Employee Devices** — registry with block/unblock/reset actions
  - **Change Requests** — approve/reject queue
  - **Settings** — device-binding toggle, registration/session limits, per-platform enforcement mode, trust-cookie lifetime, default urgency per reason category
- `#surface-mobile` — Employee app: phone-frame simulation
  - Login (with a demo toggle for "registered device" vs. "new phone")
  - Blocked / new-device screen
  - Device-change request flow (reason picker + optional detail + urgent flag)
  - Pending-approval screen with a progress stepper
  - Home, Quick Access grid, and a dedicated Device Management screen

## State model

All demo state lives in a single in-memory `store` object (seeded on load, reset via the "Restart mobile demo" button):

- `settings` — company policy toggles (binding enabled, reg/session limits, web auth mode, cookie lifetime, default urgency by reason)
- `devices[]` — registered devices per employee (id, platform, OS, registered/last-login dates, status)
- `requests[]` — device-change requests (requester, current/requested device, reason, urgency, status, who acted and when)
- `history{}` — per-employee audit timeline
- `mobile` — the simulated employee session (which screen is showing, form state, etc.)

`EMP` holds the demo employee directory; `ADMIN` is the logged-in web admin (Rezaul Karim).

## Known issue

The mobile screen-rendering functions (`renderMobile`, `mobileLogin`, `startRequest`, `pickReason`, `resetMobileDemo`) are defined twice in the script — an earlier prototype pass and a later redesign that matches the real Shomvob HR app. The second block overrides the first via JS function redeclaration, making the first block dead code. Candidate cleanup: remove the first (superseded) block.

## Running it

No build step required — just open `device-management-prototype.html` in a browser.
