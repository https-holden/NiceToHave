---
type: research
area: walkthrough
created: 2026-10-26
---

# Walkthrough

The rules of a first-visit walkthrough: a short sequence of cards, each pointing at one part of the page, shown the first time someone opens a screen and never again unless they ask. Written 26 Oct 2026 from the astrology app's Tarot Studio tour (eleven steps, shipped 25 Oct 2026). A toolkit element built from this note keeps the shape and takes the host app's own palette, fonts and voice.

## What it is

A card that floats beside a highlighted part of the page, with Back, Next and Skip, a progress bar, and the rest of the page dimmed around a cut-out. It is for a screen dense enough that a new person would not find its parts alone. Not for a simple page, and not a substitute for labels.

## The copy is one file

Every word lives in one file the owner edits: a list of steps, each `{ id, title, body, target? }`, plus the button labels and a map from target name to selector. Reordering, merging or rewording a step touches nothing else. In a body line, `**word**` sets a word in bold and a line starting `- ` is a bullet. A line is about two lines on a phone, and bolds the one thing to notice. The copy is plain and warm, not salesy.

## Targets

- Each target is a `data-tour="<name>"` attribute on the element it rings, found through the target map. The component knows names, not the page's structure.
- A step with no target, or whose target is not on screen (inert, hidden, collapsed, off the side of a phone), shows as a card in the middle of the page instead of failing.
- A step may open what it points at (bring a hidden pane out) by telling the page which step is active; the page makes the same moves it would for a user.

## Look, as shape (the host supplies the colours and fonts)

- A portal over the page. An SVG scrim darkens the ground with a hole per target; the scrim takes no pointer events, so a step that says "click here" works.
- A 2px ring around the target in the host's accent, padded about 4px, following the target's own corner radius. No shadow unless the host's system uses shadows.
- The card sits below, above, right or left of the target (gap about 12px, at least 16px from the screen edge), inside the target's foot when the target is too big, centred when there is no target. Under about 640px wide it docks to the bottom of the screen.
- A progress bar at the top of the card (`role="progressbar"`, value text "Step n of m").

## Behaviour

1. Skip, Done and Escape all close it, and all count as seen.
2. Keys: Escape closes from anywhere. In the card, the arrow keys step, Tab stays inside, and no key reaches the page's own hotkeys.
3. Focus goes to Next on each step and back to whatever opened the tour when it closes. The card is `role="dialog"`, `aria-modal="false"`, labelled by its title and described by its body.
4. The ring is re-measured every frame and on scroll, resize and a short settle timer (about 260ms), because a hidden tab runs no frames.
5. Under `prefers-reduced-motion` the card's movement and the scroll to a target are dropped.
6. It is offered only where the person can act on the page (never in a read-only or view-as mode).

## Seen

- Seen is recorded once, per account when the app has accounts (a nullable `tour_seen_at` style column written only while still empty), per browser otherwise. A replay never writes.
- A permanent "Show the walkthrough again" entry lives in the screen's settings.
- A query flag (`?tour=on`) forces it on a dev preview, so every step can be checked at desktop and phone width in light and dark.

## For the element

No dependency: one component, one copy file, one small "seen" adapter the host implements (server column or localStorage). The placement maths is pure and testable without a browser. Each host passes its own tokens and copy; nothing in the element names a colour. Reference implementation, for the port: `web/components/tarot/table/studio-tour.tsx` and `web/lib/tarot/table/tour.ts` in the Astrevelar repo.
