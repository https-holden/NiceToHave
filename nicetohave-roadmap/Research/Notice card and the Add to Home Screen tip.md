---
type: research
area: notice-card
created: 2026-10-26
---

# Notice card and the Add to Home Screen tip

The rules of a small, closable, show-once instruction card, and of the one use of it every web app wants: a tip that tells a phone how to add the app to its Home Screen. Written 26 Oct 2026 from the owner's astrology app, where the card is the "Using the Tarot Studio" notice and the install tip is its first user. A toolkit element built from this note keeps the shape and takes the host app's own palette, fonts and voice.

## What it is

A card at the top of a page saying one short thing a person may act on once, with an X that puts it away and keeps it away. Several can stack. It is for messages with a beginning and an end: a tip, a one-time heads-up. Not for context that stays true on every visit, not for errors, not for anything the page cannot work without.

## Slots

| Slot | What goes in it |
|---|---|
| `id` | A stable string naming the message, never the page. One X silences it everywhere it appears. A materially changed message gets a new id, which is how it returns to people who dismissed the old one. |
| `icon` | An icon drawn in a small tinted tile at the left. |
| `title` | One short bold line. The card is a `<section aria-labelledby>` named by it; the heading level is a prop so the page outline stays in order. |
| body | A line of prose, or a short ordered list (three items, not ten). |
| `action` | Optional: a link or a button, styled as the system's text link, or one filled primary. |
| `tone` | `default`, or one emphasis tone. Both from existing tokens; no new colour. |
| `expiresDays` | Optional. A dismissed card may return after this many days. Omitted, it is gone for good. |
| `onDismiss` | Called once the card is gone and remembered. A server write goes here when the dismissal must follow an account. |
| `onShown` | Called once when the card first appears. A "seen" count goes here. |

## Look, as shape (the host supplies the colours and fonts)

- A grid of three columns: icon tile, body, close button. Gap about 12px; padding about 14px, less on the close side.
- Corners about 11px, a 1px hairline border, a panel one tonal step off the page. No shadow.
- Icon tile 36px square, radius 8px, the icon in the host's accent colour.
- Title in the display face, about 15px, weight 600. Body in the reading face one step down, muted ink. List numerals hang in the gutter in the accent colour, tabular.
- Emphasis tone: the tile filled with the accent and the icon in the page colour; the border tinted with the accent.
- The close button is a real button at least 44px square, `type="button"`, `aria-label` "Dismiss this notice", pulled into the corner with negative margins so the card does not grow for it. Hover styles only for hover-capable pointers.

## Behaviour

1. Render nothing until mounted and the memory is read, so a remembered card never flashes. In React: `useSyncExternalStore` with a server snapshot of "unknown".
2. The X collapses the card: a grid row from `1fr` to `0fr` (no measured height) over about 260ms with an ease-out curve, opacity to zero over about 200ms. On `transitionend` the id is recorded and `onDismiss` called; a fallback timer (duration plus 100ms) finishes it if the transition never ends, and a guard stops it running twice.
3. Under `prefers-reduced-motion` it goes at once, in the script and in CSS.
4. A later mount with the id remembered renders nothing and does not call `onShown`.
5. It listens for its own change event and the `storage` event, so dismissing in one tab hides it in another.
6. No entrance animation. No focus is moved on dismiss.

## Storage

- One namespaced `localStorage` key for the whole app, holding `{ id: dismissedAtISO }`. One key, not one per card.
- Every read and write in try/catch, including the access to `window.localStorage` itself. A private window, a full quota or no storage simply forgets. A corrupt value reads as empty; an unparseable date counts as lapsed, never as permanent. An empty memory removes the key.
- The memory is per browser. If a dismissal must follow an account, keep a server column and use the browser memory on top, never instead.
- The pure memory module is tested on its own with a fake storage (a Map, and one that throws); the component with jsdom.

## The Add to Home Screen tip

- A notice card with a phone icon and a title naming the app ("Add <App> to your Home Screen").
- The body is the steps for this phone, as an ordered list. iOS Safari: tap Share, then **Add to Home Screen**. Android Chrome: tap the menu, then **Add to Home screen**, then **Install**. The action link swaps to the other platform's steps ("On Android? See those steps").
- Never shown when the app is already installed: `navigator.standalone` (older iOS) or `matchMedia('(display-mode: standalone)')`, watched so it reacts.
- A budget, not a nag: it shows on at most three days (counted in UTC days, not page loads; a day already counted keeps its card until it ends), and never again once dismissed.
- It waits its turn: not on a first visit's welcome, not beside another modal or notice of higher priority.
- After it is dismissed, the same instructions stay reachable somewhere permanent (a settings or help page).
- The reference app does not use `beforeinstallprompt`. iOS Safari has none, and the steps card is the one flow that works on both.

## For the element

Logic (the memory, the budget, the platform and installed checks) is separable from the UI and has no framework in it, so an iOS port can reuse the rules. The UI is one component with the slots above. Each host passes its own tokens; nothing in the element names a colour.
