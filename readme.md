# Random Quote Generator

A minimal, single-file web app that fetches a random quote and author from a browser-friendly API, with a polished UI, robust loading/error states, and accessible semantics.

## Features

- Fetches a random quote from `https://dummyjson.com/quotes/random`.
- “Refresh” button triggers a new fetch (buttons disable during loading).
- “Copy” copies the quote + author to the clipboard (with a safe fallback).
- Loading skeleton and status text for clear feedback.
- Validates non-empty quote text; retries once on failure.
- Per-attempt network timeout for reliability.
- Accessibility: semantic `blockquote` + `cite`, `aria-live` status, `aria-busy` during loads.
- Safe rendering via `textContent` to avoid XSS.
- Caches the last successful quote to `localStorage` and renders it instantly on startup.
- Prevents showing duplicate quotes within a session (best-effort, respects API id or text).
- Keyboard shortcuts: `r` or Space = refresh, `c` = copy.
 - Favorites: save/unsave the current quote, view/remove favorites in a modal; persisted in `localStorage`.
 - Auto-refresh: toggleable with interval options (30s/60s/120s); pauses when tab is hidden.

## Quick Start

1) Open `index.html` directly in a modern browser.

If your environment blocks network requests for `file://` pages, serve locally:

```bash
# from the project directory
python -m http.server 8000
# then open http://localhost:8000/
```

## Project Structure

- `index.html` – Self-contained app with:
  - Styles: Apple‑ish card, buttons, skeleton, animations.
  - Markup: `blockquote#quoteText`, `cite#quoteAuthor`, status region, buttons.
  - Script: Fetch logic, validation, rendering, copy to clipboard.

## Key Code Points

- `QUOTE_URL`: API endpoint. Replace if you prefer a different source.
- `normalizeQuotePayload(data)`: Normalizes varying API shapes into `{ text, author }`.
- `isValidQuoteText(text)`: Ensures quote is non-empty and not a placeholder.
- `fetchQuoteOnce(signal)`: Single fetch with the AbortController signal; 9s timeout used by caller.
- `fetchAndRenderQuote()`: Orchestrates loading UI, attempts, validation, duplicate-avoidance, and final render.
- `quoteKey(q)`: Computes a session-stable key for duplicate detection.
- `renderQuote()`: Renders and persists the latest quote to `localStorage`.
- `copyToClipboard()`: Uses `navigator.clipboard` and falls back to a temporary textarea.
 - Favorites: `toggleFavorite()`, `loadFavs()`, `saveFavs()`, `renderFavsList()` manage persistence and UI.
 - Auto-refresh: `setAuto(on)`, `scheduleAuto()`, `currentInterval()`; integrates with visibility changes.

## Configuration

- Change API: edit `QUOTE_URL` near the top of the script.
- Timeout per attempt: currently 9000ms via an `AbortController` inside `fetchAndRenderQuote()`.
- Styling: tweak CSS variables in `:root` (colors, radii, shadow).

## Troubleshooting

- CORS or network errors: run a local server (see Quick Start) and ensure internet access.
- Clipboard blocked: the app falls back to a textarea copy; some browsers require user gesture.
- Empty quotes: the app validates and retries once; persistent empties will show a friendly error.

## Possible Enhancements

- Share as image: export the card to PNG via canvas.
- Max-quote length guard or multiline truncation.
- Basic unit tests for `normalizeQuotePayload` and `isValidQuoteText` in a small test harness.
