# QA Checklist: e.ggtimer.com Countdown Timer
**Target:** https://e.ggtimer.com  
**Role:** Senior QA / QA Lead  
**Date:** 22 April 2026  
**Scope:** Functional · UI/UX · Edge Cases · Negative Testing · URL Routing as API

---

## Table of Contents
1. [Positive Testing (Happy Path)](#1-positive-testing-happy-path)
2. [Negative Testing](#2-negative-testing)
3. [Edge Cases & Boundary Values](#3-edge-cases--boundary-values)
4. [URL Label Patterns & Timer Sequences](#4-url-label-patterns--timer-sequences)
5. [URL Query Parameters](#5-url-query-parameters)
6. [Settings Panel & Local Persistence](#6-settings-panel--local-persistence)
7. [UI/UX & Responsiveness](#7-uiux--responsiveness)
8. [URL Routing as API (Client-Side Contract)](#8-url-routing-as-api-client-side-contract)

---

## 1. Positive Testing (Happy Path)

### 1.1 Homepage Input Field
> The homepage provides a free-form text field + **Start** button. Pressing **Enter** is equivalent to clicking **Start**.

| # | Input | Expected Result | Pass/Fail | Notes |
|---|-------|-----------------|-----------|-------|
| 1.1.1 | `30` (bare number, no unit) | Defaults to **30 minutes**; navigates to `/30` and starts 30:00 | | |
| 1.1.2 | `15 minutes` | Navigates to `/15minutes`; displays `15:00`, auto-starts | | |
| 1.1.3 | `60 seconds` | Navigates to `/60seconds`; displays `01:00`, auto-starts | | |
| 1.1.4 | `5 days 3 hours` | Navigates to corresponding path; displays `123:00:00`, auto-starts | | |
| 1.1.5 | `45m` (shorthand unit) | Displays `45:00`, auto-starts | | |
| 1.1.6 | `9pm` (clock time, AM/PM) | Countdown to 9:00 PM today (or tomorrow if past); correct HH:MM:SS | | |
| 1.1.7 | `17:30` (24-hour clock) | Countdown to 5:30 PM today | | |
| 1.1.8 | `2030-01-01` (ISO 8601 date) | Countdown to 1 January 2030; large HH:MM:SS display | | |
| 1.1.9 | `1 January 2030` (natural language date) | Same result as 1.1.8 | | |
| 1.1.10 | `week` | Countdown of exactly 7 days (`168:00:00`) | | |
| 1.1.11 | `Mothers Day` (holiday name) | Countdown to next Mother's Day (second Sunday in May) | | |
| 1.1.12 | `new years day` | Countdown to 1 January next calendar year | | |
| 1.1.13 | Press **Enter** key to submit | Identical behavior to clicking **Start** | | |

### 1.2 URL-Based Timer Initialization
> Timer auto-starts immediately on page load; no user action required.

| # | URL Path | Expected Display | Expected Format | Pass/Fail | Notes |
|---|----------|-----------------|-----------------|-----------|-------|
| 1.2.1 | `/5minutes` | `05:00` | MM:SS | | |
| 1.2.2 | `/10minutes` | `10:00` | MM:SS | | |
| 1.2.3 | `/15minutes` | `15:00` | MM:SS | | |
| 1.2.4 | `/30minutes` | `30:00` | MM:SS | | |
| 1.2.5 | `/90seconds` | `01:30` | MM:SS | | |
| 1.2.6 | `/45m` | `45:00` | MM:SS | | |
| 1.2.7 | `/1hour` | `01:00:00` | HH:MM:SS | | |
| 1.2.8 | `/2hours` | `02:00:00` | HH:MM:SS | | |
| 1.2.9 | `/5 days 3 hours` (or URL-encoded equivalent) | `123:00:00` | HH:MM:SS | | |

### 1.3 Named Preset / Special Timers

| # | URL Path | Expected Behavior | Pass/Fail | Notes |
|---|----------|-------------------|-----------|-------|
| 1.3.1 | `/pomodoro` | Starts **25:00** Work phase; on completion auto-transitions to **5:00** Break phase | | |
| 1.3.2 | `/pomodoro` (full cycle) | Work 25:00 → Break 5:00 → Work 25:00 (loop continues indefinitely) | | |
| 1.3.3 | `/52-17` | Starts **52:00** Work phase; on completion transitions to **17:00** Break | | |
| 1.3.4 | `/52-17` (full cycle) | Work 52:00 → Break 17:00 → Work 52:00 (loop continues) | | |

### 1.4 Timer Controls

| # | Action | Expected Result | Pass/Fail | Notes |
|---|--------|-----------------|-----------|-------|
| 1.4.1 | Click **Pause** | Countdown halts; current time frozen on display | | |
| 1.4.2 | Click **Resume** after pause | Countdown resumes from the paused time — not from initial value | | |
| 1.4.3 | Click **Restart** | Timer resets to initial value and immediately starts counting down again | | |
| 1.4.4 | Click **Stop** | Navigates to homepage (`https://e.ggtimer.com/`) | | |
| 1.4.5 | Pause → Resume multiple times | Each resume continues exactly from the last paused time | | |

### 1.5 Completion Behavior

| # | Test | Expected Result | Pass/Fail | Notes |
|---|------|-----------------|-----------|-------|
| 1.5.1 | Timer reaches `00:00` | Display shows `00:00` before any completion actions fire | | |
| 1.5.2 | Audio enabled — timer ends | Alert sound (Beep by default) plays on completion | | |
| 1.5.3 | Timer ends | Distinct visual completion state (color change/animation/overlay) is shown | | |
| 1.5.4 | System notifications enabled — timer ends | Browser notification fires even when tab is in background | | |

### 1.6 Display Format

| # | Test | Expected Result | Pass/Fail | Notes |
|---|------|-----------------|-----------|-------|
| 1.6.1 | Timer with duration < 1 hour | Displays in **MM:SS** format | | |
| 1.6.2 | Timer with duration ≥ 1 hour | Displays in **HH:MM:SS** format | | |
| 1.6.3 | Timer crosses 1-hour boundary (e.g., starting at `01:00:00` and counting down) | Format switches from `HH:MM:SS` to `MM:SS` exactly at `59:59` | | |
| 1.6.4 | Browser tab title | Updates live each second with remaining time (e.g., `"04:59 – E.ggTimer"`) | | |

### 1.7 Audio Activation

| # | Test | Expected Result | Pass/Fail | Notes |
|---|------|-----------------|-----------|-------|
| 1.7.1 | Navigate directly to a timer URL | "Enable Audio" / interaction nudge is displayed | | |
| 1.7.2 | Click anywhere on the page | Audio is enabled; nudge dismisses | | |
| 1.7.3 | Press any keyboard key | Audio is enabled; nudge dismisses | | |
| 1.7.4 | Click "Do Not Show This Again" | Nudge suppressed on all future visits (persisted in `localStorage`) | | |
| 1.7.5 | Click "Enable Audio" button | Audio enabled immediately | | |

---

## 2. Negative Testing

### 2.1 Invalid Homepage Input

| # | Input | Expected Result | Pass/Fail | Notes |
|---|-------|-----------------|-----------|-------|
| 2.1.1 | Empty input + click **Start** | No navigation; field highlighted or error shown; no crash | | |
| 2.1.2 | `0` | Timer at `00:00` — should not auto-start or show graceful message | | |
| 2.1.3 | `0 minutes` | Same as 2.1.2 | | |
| 2.1.4 | `-5` | Rejected; error or redirect to homepage; no negative timer | | |
| 2.1.5 | `-5 minutes` | Rejected gracefully; no countdown runs | | |
| 2.1.6 | `abc` (nonsense text, not a holiday) | Graceful error or redirect to homepage; no JS exception | | |
| 2.1.7 | `!@#$%^&*` (special characters) | Graceful handling; page does not crash | | |
| 2.1.8 | `<script>alert(1)</script>` (XSS) | Input sanitized; no script executed; reflected value escaped in DOM | | |
| 2.1.9 | `'; DROP TABLE timers; --` (SQLi) | No server-side error; treated as unknown input | | |
| 2.1.10 | Input of 1000+ characters | No crash; input truncated or gracefully rejected | | |

### 2.2 Invalid URL Paths

| # | URL Path | Expected Result | Pass/Fail | Notes |
|---|----------|-----------------|-----------|-------|
| 2.2.1 | `/abc` (unknown string, not a holiday) | Redirect to homepage or a defined error state; no JS crash | | |
| 2.2.2 | `/0minutes` | Display `00:00`; timer does not start; or redirect to home | | |
| 2.2.3 | `/-5minutes` | Redirect to homepage or validation message; no negative countdown | | |
| 2.2.4 | `/minutes` (no number prefix) | Graceful redirect or error; no crash | | |
| 2.2.5 | `/0.5seconds` (decimal) | Defined behavior: rounds to 1s, rejects, or redirects | | |
| 2.2.6 | `/5%20minutes` (URL-encoded space) | Decoded and handled as `/5 minutes`; displays `05:00` | | |
| 2.2.7 | `/999999hours` (extreme large value) | Browser does not freeze; either shows enormous time or redirects gracefully | | |
| 2.2.8 | `/<script>alert(1)</script>` (XSS in path) | Path sanitized; no script executed; reflected value escaped | | |
| 2.2.9 | URL path > 2000 characters | Returns 414 URI Too Long or redirects gracefully; no server crash | | |
| 2.2.10 | `/9pm` when it is already past 9 PM | Countdown to 9 PM **tomorrow**; not a negative value | | |

### 2.3 Rapid / Concurrent Actions

| # | Action | Expected Result | Pass/Fail | Notes |
|---|--------|-----------------|-----------|-------|
| 2.3.1 | Click **Pause** then **Resume** in rapid succession (< 200ms apart) | Final state is consistent (running or paused); no duplicate intervals | | |
| 2.3.2 | Click **Restart** multiple times quickly | Timer resets cleanly each time; no stacked `setInterval` callbacks | | |
| 2.3.3 | Click **Stop** then immediately press browser Back | Page state is clean; no broken timer remnants | | |

---

## 3. Edge Cases & Boundary Values

### 3.1 Time Boundary Inputs

| # | URL / Input | Expected Result | Pass/Fail | Notes |
|---|-------------|-----------------|-----------|-------|
| 3.1.1 | `/1second` | Displays `00:01`; completes after exactly 1 second and fires alert | | |
| 3.1.2 | `/1minute` | Displays `01:00`; completes after exactly 60 seconds | | |
| 3.1.3 | `/59minutes` | Displays `59:00` (MM:SS format) | | |
| 3.1.4 | `/60minutes` | Displays `01:00:00` (format switches to HH:MM:SS at this boundary) | | |
| 3.1.5 | `/24hours` | Displays `24:00:00`; runs without observable drift over spot-check intervals | | |
| 3.1.6 | `/7days` or `week` | Displays `168:00:00`; auto-starts | | |

### 3.2 Interaction at the Final Second

| # | Action | Expected Result | Pass/Fail | Notes |
|---|--------|-----------------|-----------|-------|
| 3.2.1 | Pause at `00:01` | Timer freezes; completion does not trigger | | |
| 3.2.2 | Pause at `00:01`, then Resume | Counts down to `00:00`, triggers audio + visual completion | | |
| 3.2.3 | Restart at `00:01` | Timer resets to full initial value and restarts cleanly | | |
| 3.2.4 | Stop at `00:01` | Navigates to homepage; no completion event fires | | |
| 3.2.5 | Last-second transition `00:01 → 00:00` | No skip (never jumps from `00:02` directly to `00:00`) | | |

### 3.3 Browser & Device Interruptions

| # | Scenario | Expected Result | Pass/Fail | Notes |
|---|----------|-----------------|-----------|-------|
| 3.3.1 | Switch to a different tab mid-timer | Timer continues running in background (verify via `Page Visibility API` behavior) | | |
| 3.3.2 | Return to tab after background period | Displayed time is accurate (no freeze drift); timer has counted down correctly | | |
| 3.3.3 | Lock device screen while timer is running | On wake: timer reflects elapsed time; no crash | | |
| 3.3.4 | Page refresh (`F5`) mid-timer | Timer resets to initial value (no persistence expected); page loads cleanly | | |
| 3.3.5 | Press browser **Back** from a timer page | Expected navigation (to previous page or home); no broken timer state left | | |
| 3.3.6 | Two tabs open simultaneously (`/5minutes` and `/10minutes`) | Each tab runs independently; no cross-tab interference | | |

### 3.4 Cycle Timer Edge Cases

| # | Scenario | Expected Result | Pass/Fail | Notes |
|---|----------|-----------------|-----------|-------|
| 3.4.1 | Pause during Pomodoro **Break** phase | Break timer freezes; Work phase does not start | | |
| 3.4.2 | Restart during Pomodoro **Break** phase | Resets to the start of Break (or Work — behavior should be defined and consistent) | | |
| 3.4.3 | Stop during Pomodoro Break phase | Returns to homepage cleanly | | |
| 3.4.4 | Full 52-17 cycle runs uninterrupted | Work 52:00 → Break 17:00 → Work 52:00 loop without drift | | |

---

## 4. URL Label Patterns & Timer Sequences

### 4.1 URL Label Patterns

| # | URL Pattern | Example | Expected Behavior | Pass/Fail | Notes |
|---|-------------|---------|-------------------|-----------|-------|
| 4.1.1 | `/(time)/until/(label)` | `/3minutes/until/Party!` | Timer `03:00` displayed with label **"Party!"** | | |
| 4.1.2 | `/(label)/for/(time)` | `/Lunch/for/30minutes` | Timer `30:00` with label **"Lunch"** | | |
| 4.1.3 | `/(label)/in/(time)` | `/Standup/in/15minutes` | Timer `15:00` with label **"Standup"** | | |
| 4.1.4 | `/(label)/at/(time)` | `/Meeting/at/9pm` | Countdown to 9 PM with label **"Meeting"** | | |
| 4.1.5 | Label with URL-encoded spaces | `/Hello%20World/in/5minutes` | Label decoded and displayed as **"Hello World"** | | |
| 4.1.6 | Label with emoji | `/🎉/in/5minutes` | Emoji rendered correctly in label display | | |
| 4.1.7 | Missing time part | `/Lunch/for/` | Graceful error or redirect; no crash | | |
| 4.1.8 | Missing label part | `//in/5minutes` | Graceful handling; timer starts without label or redirects | | |
| 4.1.9 | XSS in label | `/<img src=x onerror=alert(1)>/in/5m` | Label sanitized; no script/handler executed | | |
| 4.1.10 | Label with reserved URL characters (unencoded) | `/Label&More/in/5m` | Handled gracefully; no URL parsing error | | |

### 4.2 Timer Sequences

| # | URL Path | Expected Behavior | Pass/Fail | Notes |
|---|----------|-------------------|-----------|-------|
| 4.2.1 | `/1h/20m` | 1-hour timer runs; on completion auto-transitions to 20-minute timer | | |
| 4.2.2 | `/1h/20m/5m` | Three-stage sequence: 1h → 20m → 5m; each runs in order | | |
| 4.2.3 | `/5minutes/10minutes/15minutes` | Three-stage with verbose units; runs 5m → 10m → 15m | | |
| 4.2.4 | Audio alert per segment | Alert fires at the end of **each** individual segment | | |
| 4.2.5 | Visual segment indicator | UI shows which segment is active (e.g., "2 of 3" or progress dots) | | |
| 4.2.6 | Pause mid-sequence | Pauses the current segment; does not advance to next segment | | |
| 4.2.7 | Restart mid-sequence | Restarts the **current** segment from its initial value | | |
| 4.2.8 | Stop mid-sequence | Navigates to homepage; sequence is terminated | | |
| 4.2.9 | Sequence with one invalid segment | Defined behavior: skip invalid segment, stop, or show error | | |
| 4.2.10 | Single-item sequence `/5m` | Behaves identically to `/5minutes` (normal timer) | | |

---

## 5. URL Query Parameters

> All parameters are appended to a valid timer URL, e.g. `/5minutes?param=value`.  
> Per documentation: URL params override locally saved settings for that visit.

### 5.1 `?muted=`

| # | Parameter | Expected Result | Pass/Fail | Notes |
|---|-----------|-----------------|-----------|-------|
| 5.1.1 | `?muted=true` | Audio muted by default; mute icon reflects muted state | | |
| 5.1.2 | `?muted=false` | Audio unmuted (still subject to browser gesture policy) | | |
| 5.1.3 | `?muted=TRUE` (uppercase) | Treated identically to `true`; or falls back to default | | |
| 5.1.4 | `?muted=1` | Defined behavior: treated as `true`, or passed to default | | |
| 5.1.5 | `?muted=invalid` | Falls back to default setting; no crash | | |

### 5.2 `?notify=`

| # | Parameter | Expected Result | Pass/Fail | Notes |
|---|-----------|-----------------|-----------|-------|
| 5.2.1 | `?notify=true` | System notification fires on completion | | |
| 5.2.2 | `?notify=false` | No system notification at end of timer | | |
| 5.2.3 | `?notify=true` with notifications **denied** in browser | No notification (browser permission respected); no crash | | |
| 5.2.4 | `?notify=true`, tab in background | Notification fires while tab is not visible | | |
| 5.2.5 | `?notify=invalid` | Falls back to default (`true`); no crash | | |

### 5.3 `?hideToolbar=`

| # | Parameter | Expected Result | Pass/Fail | Notes |
|---|-----------|-----------------|-----------|-------|
| 5.3.1 | `?hideToolbar=true` | Toolbar hidden on load; user can still open it manually | | |
| 5.3.2 | `?hideToolbar=false` | Toolbar visible by default | | |
| 5.3.3 | `?hideToolbar=invalid` | Falls back to default (`false`); no crash | | |

### 5.4 `?hideNudge=`

| # | Parameter | Expected Result | Pass/Fail | Notes |
|---|-----------|-----------------|-----------|-------|
| 5.4.1 | `?hideNudge=true` | Audio interaction reminder does not appear | | |
| 5.4.2 | `?hideNudge=false` | Reminder appears for ≥ 12 seconds before auto-dismissing | | |
| 5.4.3 | `?hideNudge=invalid` | Falls back to default (`false`); no crash | | |

### 5.5 `?volume=`

| # | Parameter | Expected Result | Pass/Fail | Notes |
|---|-----------|-----------------|-----------|-------|
| 5.5.1 | `?volume=1` | Full-volume alert on completion | | |
| 5.5.2 | `?volume=0.5` | Alert plays at 50% volume | | |
| 5.5.3 | `?volume=0` | Alert is silent (same effect as muted) | | |
| 5.5.4 | `?volume=2` (above max) | Clamped to `1`; no distortion/crash | | |
| 5.5.5 | `?volume=-1` (negative) | Clamped to `0`; no crash | | |
| 5.5.6 | `?volume=abc` (non-numeric) | Falls back to default (`1`); no crash | | |
| 5.5.7 | `?volume=` (empty) | Falls back to default; no crash | | |

### 5.6 `?theme=`

| # | Parameter | Expected Result | Pass/Fail | Notes |
|---|-----------|-----------------|-----------|-------|
| 5.6.1 | `?theme=gg_timer_classic` | Classic theme applied | | |
| 5.6.2 | `?theme=gg_timer_digital` | Digital theme applied | | |
| 5.6.3 | `?theme=gg_timer_dotmatrix` | Dot Matrix theme applied | | |
| 5.6.4 | `?theme=gg_timer_largebar` | Progress Bar theme applied | | |
| 5.6.5 | `?theme=gg_timer_sunset` | Sunset theme applied | | |
| 5.6.6 | `?theme=gg_timer_slow` | Slow theme applied | | |
| 5.6.7 | `?theme=gg_timer_bsod` | CrowdStrike (BSOD) theme applied | | |
| 5.6.8 | `?theme=gg_timer_train` | Train theme applied | | |
| 5.6.9 | `?theme=gg_timer_ugly` | Ugly theme applied | | |
| 5.6.10 | `?theme=invalid_theme` | Falls back to default (Classic); no crash | | |
| 5.6.11 | `?theme=` (empty) | Falls back to default; no crash | | |

### 5.7 `?sound=`

| # | Parameter | Expected Result | Pass/Fail | Notes |
|---|-----------|-----------------|-----------|-------|
| 5.7.1 | `?sound=gg_sound_beep` | Beep sound plays on completion | | |
| 5.7.2 | `?sound=gg_sound_ring` | Ring sound plays on completion | | |
| 5.7.3 | `?sound=invalid` | Falls back to default (Beep); no crash | | |
| 5.7.4 | `?sound=` (empty) | Falls back to default; no crash | | |

### 5.8 Combined Parameters

| # | URL | Expected Result | Pass/Fail | Notes |
|---|-----|-----------------|-----------|-------|
| 5.8.1 | `/5minutes?muted=true&hideNudge=true&theme=gg_timer_digital` | All three params applied simultaneously | | |
| 5.8.2 | `/5minutes?volume=0.5&sound=gg_sound_ring&notify=false` | All three params applied correctly | | |
| 5.8.3 | URL param vs. saved preference | URL param overrides the locally saved setting for that visit | | |
| 5.8.4 | Duplicate param key | `/5m?volume=1&volume=0.5` — last value wins (or first); behavior consistent | | |

---

## 6. Settings Panel & Local Persistence

> Settings are saved in `localStorage`. Use DevTools → Application → Local Storage to verify.

### 6.1 Settings Panel Functionality

| # | Test | Expected Result | Pass/Fail | Notes |
|---|------|-----------------|-----------|-------|
| 6.1.1 | Open Settings panel | Panel opens without error | | |
| 6.1.2 | Close Settings panel | Panel closes; timer continues uninterrupted | | |
| 6.1.3 | Change **Theme** in settings | Timer visual updates immediately without page reload | | |
| 6.1.4 | Change **Alert Sound** in settings | Updated sound plays on next timer completion | | |
| 6.1.5 | Toggle **Mute Alert Sound** | Mute state toggles; icon and behavior update | | |
| 6.1.6 | Adjust **Volume** slider | Volume level changes; "Volume Test Audio" button plays at new level | | |
| 6.1.7 | Click **Volume Test Audio** | Alert sound plays at the currently set volume | | |
| 6.1.8 | Toggle **Notifications** | Notification state toggles; browser permission requested if enabling for first time | | |
| 6.1.9 | Toggle **Hide Interaction Nudge** | Nudge appears or is suppressed on next direct URL visit accordingly | | |

### 6.2 Settings Persistence

| # | Test | Expected Result | Pass/Fail | Notes |
|---|------|-----------------|-----------|-------|
| 6.2.1 | Change Theme → reload page | Theme persists after reload | | |
| 6.2.2 | Change Sound → reload page | Sound preference persists | | |
| 6.2.3 | Mute → reload page | Mute state persists | | |
| 6.2.4 | Set Volume to 0.3 → reload page | Volume remains at 0.3 | | |
| 6.2.5 | Disable Notifications → reload page | Notifications remain disabled | | |
| 6.2.6 | Hide Nudge → reload page | Nudge remains hidden | | |
| 6.2.7 | URL `?theme=gg_timer_digital` with Classic saved | Digital theme used for this visit; Classic still saved in `localStorage` | | |
| 6.2.8 | Clear `localStorage` in DevTools → reload | All settings reset to documented factory defaults | | |
| 6.2.9 | Different browser / private/incognito window | Does **not** inherit settings from normal session | | |

---

## 7. UI/UX & Responsiveness

### 7.1 Timer Display Accuracy

| # | Test | Expected Result | Pass/Fail | Notes |
|---|------|-----------------|-----------|-------|
| 7.1.1 | Digits decrement each second | Smooth decrement; no flicker, stutter, or skipped seconds | | |
| 7.1.2 | Format boundary crossing | Switches from `HH:MM:SS` to `MM:SS` exactly at `59:59`; no layout jump | | |
| 7.1.3 | Paused state indicator | Visually distinct from the running state (e.g., different color, icon, or label) | | |
| 7.1.4 | Completion state indicator | Visually distinct from running/paused (e.g., animation, color change, overlay) | | |
| 7.1.5 | Labeled timer | Label rendered visibly and correctly alongside the countdown | | |
| 7.1.6 | Timer with all 9 themes | Each theme renders without layout breakage or overlapping elements | | |

### 7.2 Responsive Design

| # | Viewport Width | Expected Result | Pass/Fail | Notes |
|---|----------------|-----------------|-----------|-------|
| 7.2.1 | 375 px (mobile portrait) | Timer and controls fully visible; no horizontal overflow | | |
| 7.2.2 | 414 px (iPhone Plus / Pro Max) | Layout correct | | |
| 7.2.3 | 768 px (tablet portrait) | Layout correct; controls accessible | | |
| 7.2.4 | 1024 px (tablet landscape / small laptop) | Layout correct | | |
| 7.2.5 | 1280 px (desktop) | Full layout correct | | |
| 7.2.6 | Touch control on mobile | Pause, Resume, Restart, Stop all respond to tap | | |

### 7.3 Accessibility

| # | Test | Expected Result | Pass/Fail | Notes |
|---|------|-----------------|-----------|-------|
| 7.3.1 | Keyboard navigation | All controls reachable via **Tab** and activated by **Enter** or **Space** | | |
| 7.3.2 | Screen reader live region | Countdown updates announced to screen readers at reasonable intervals (ARIA live region) | | |
| 7.3.3 | Color contrast | Timer digits and controls meet WCAG 2.1 AA contrast ratio (4.5:1 for text) | | |
| 7.3.4 | Focus indicator | Focused control has a visible focus ring | | |

---

## 8. URL Routing as API (Client-Side Contract)

> e.ggtimer.com has no backend REST API. The URL path and query string are the primary interface contract. Tests below validate HTTP-level, security, and browser-API behavior.

### 8.1 HTTP Response Codes

| # | Request | Expected HTTP Status | Expected Behavior | Pass/Fail | Notes |
|---|---------|---------------------|-------------------|-----------|-------|
| 8.1.1 | `GET /` | 200 | Homepage renders with input field and preset links | | |
| 8.1.2 | `GET /5minutes` | 200 | Page renders; 5-minute timer starts | | |
| 8.1.3 | `GET /1hour` | 200 | Page renders; 1-hour timer starts | | |
| 8.1.4 | `GET /pomodoro` | 200 | Pomodoro cycle starts | | |
| 8.1.5 | `GET /52-17` | 200 | 52-17 cycle starts | | |
| 8.1.6 | `GET /nonexistentpath` | 200 or 404 | Defined error state; **zero** uncaught JS errors in console | | |
| 8.1.7 | `GET /5minutes?theme=gg_timer_digital` | 200 | Page renders with Digital theme | | |

### 8.2 URL Case Sensitivity

| # | Test | Expected Result | Pass/Fail | Notes |
|---|------|-----------------|-----------|-------|
| 8.2.1 | `/5minutes` vs `/5Minutes` vs `/5MINUTES` | Consistent behavior across all variants (all work or all error out the same way) | | |
| 8.2.2 | `/Pomodoro` vs `/pomodoro` vs `/POMODORO` | Consistent behavior | | |

### 8.3 Transport Security

| # | Test | Expected Result | Pass/Fail | Notes |
|---|------|-----------------|-----------|-------|
| 8.3.1 | `http://e.ggtimer.com/5minutes` | Redirects to `https://` (HTTP 301 or 302) | | |
| 8.3.2 | HTTPS TLS certificate | Valid; not expired; issued by trusted CA | | |
| 8.3.3 | `Strict-Transport-Security` header | Present in HTTP response | | |

### 8.4 Client-Side Security

| # | Test | Expected Result | Pass/Fail | Notes |
|---|------|-----------------|-----------|-------|
| 8.4.1 | XSS via URL path | Path is sanitized; no reflected script execution | | |
| 8.4.2 | XSS via label parameter | Label value HTML-escaped in DOM output | | |
| 8.4.3 | `Content-Security-Policy` header | Present and sufficiently restrictive (check for `unsafe-inline` absence) | | |
| 8.4.4 | `localStorage` contents | No sensitive personal data stored (only settings values) | | |
| 8.4.5 | Network requests | No unexpected third-party tracking requests (verify in DevTools → Network tab) | | |
| 8.4.6 | `X-Frame-Options` or `frame-ancestors` CSP | Page cannot be embedded in a malicious iframe (clickjacking protection) | | |

### 8.5 Browser API Contract

| # | Test | Expected Result | Pass/Fail | Notes |
|---|------|-----------------|-----------|-------|
| 8.5.1 | `AudioContext` initialization | Created only **after** user gesture (Web Audio API policy); not on page load | | |
| 8.5.2 | `Page Visibility API` (`visibilitychange`) | Timer continues when tab is hidden; no pause/freeze due to visibility handler | | |
| 8.5.3 | Time drift over 10 minutes | Displayed time drifts ≤ 1 second from wall-clock elapsed time (verify with stopwatch) | | |
| 8.5.4 | Timer mechanism | `setInterval` or `requestAnimationFrame`; verify no stacked intervals after Restart in DevTools | | |
| 8.5.5 | `?notify=true` with notifications denied | No uncaught `NotAllowedError`; failure handled silently | | |

### 8.6 Performance

| # | Test | Expected Result | Pass/Fail | Notes |
|---|------|-----------------|-----------|-------|
| 8.6.1 | Initial page load (Fast 3G throttle) | Page fully interactive in < 2 seconds (Chrome DevTools → Network → Fast 3G) | | |
| 8.6.2 | Memory after 30-minute timer | Heap size stable; no progressive growth (Chrome DevTools → Memory profiler) | | |
| 8.6.3 | CPU during idle countdown | CPU usage < 5% during normal countdown (Chrome DevTools → Performance monitor) | | |
| 8.6.4 | CPU during long countdown (1+ hour) | CPU usage remains stable; no runaway interval accumulation | | |

---

