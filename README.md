# Checklist Timer — QA Testing Project

Manual QA checklist for **[e.ggtimer.com](https://e.ggtimer.com)**, a client-side countdown timer web application.

## Contents

| File | Description |
|------|-------------|
| [CHECKLIST.md](CHECKLIST.md) | Full test case checklist (180+ cases across 8 sections) |
| [CLAUDE.md](CLAUDE.md) | Testing strategy template and AI guidance notes |

## Checklist Structure

| Section | Scope |
|---------|-------|
| 1. Positive Testing | Homepage input formats, URL-based timer init, controls, completion, audio |
| 2. Negative Testing | Invalid inputs, invalid URLs, XSS/SQLi, rapid actions |
| 3. Edge Cases & Boundaries | 1-second timer, last-second interactions, device interruptions, cycle edge cases |
| 4. URL Labels & Sequences | All 4 label URL patterns, multi-stage timer sequences |
| 5. URL Query Parameters | `?muted`, `?notify`, `?hideToolbar`, `?hideNudge`, `?volume`, `?theme`, `?sound` |
| 6. Settings Panel | All 9 settings, `localStorage` persistence, param override precedence |
| 7. UI/UX & Responsiveness | Display accuracy, 6 viewport widths, keyboard and screen reader accessibility |
| 8. URL Routing as API | HTTP status codes, transport security (HTTPS/HSTS/TLS), CSP, browser API contract, performance |

## Target Application

- **URL:** https://e.ggtimer.com
- **Type:** Purely client-side; no backend REST API
- **Primary interface:** URL path and query string parameters
- **Key features tested:** Free-form time input, named presets (Pomodoro, 52-17), timer sequences, 9 themes, 2 alert sounds, `localStorage`-persisted settings

## How to Use

1. Open [CHECKLIST.md](CHECKLIST.md) in any Markdown viewer (GitHub, VS Code Preview, Notion, etc.)
2. For each test case, fill in the **Pass/Fail** column and add observations to **Notes**
3. Retest failed cases after fixes and update the column accordingly