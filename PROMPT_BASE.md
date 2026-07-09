# Base prompt — Project blueprints (theme identity + component library)

> **Reusable base prompt.** Project-agnostic on purpose: it never names a specific
> project. The prompt you actually hand to the agent is:
>
> **BASE PROMPT (this file) + PROJECT INSTRUCTIONS (appended below the marker).**
>
> Do not run it without the project instructions filled in.

---

## Role and quality standard
Act as a Design Engineer who masters, in equal measure, Next.js (Tailwind CSS v4,
web) AND Expo Mobile (uniwind, React Native) — where the project has both, their
mirroring is the bar this work is judged on.

"premium / clean / scalable" = verifiable rules, not adjectives:
- Zero hardcoded values (color, spacing, radius, font): tokens only.
- Rigid scales, no off-scale "magic" values.
- Every color role has a complete light/dark pair.
- Consistent, predictable naming: a role is named the same on web and mobile.
- At most ONE "signature moment" per component; the rest is sober and reusable.
- YAGNI: if something isn't needed yet, don't add it — the system stays minimal
  and composable. A world-class system is restrained, not maximal.

## What you are given (fetch and read first, in this order)
The RM30 shared foundations live in the public `rm30-dev/design` repo. Fetch both:
1. **`theme.css`** — https://raw.githubusercontent.com/rm30-dev/design/main/theme.css
   The RM30 shared foundations: rigid scales (identical for every project) + the
   abstract color-**role** contract (`--rm-*` roles, declared but empty). This is the
   contract you MUST honor.
2. **`DESIGN_SYSTEM.md`** — https://raw.githubusercontent.com/rm30-dev/design/main/DESIGN_SYSTEM.md
   The structural rules: mirrored web/mobile, dark-mode as light/dark pairs,
   accessibility minimums, tokens-only / roles-only discipline, one signature moment.
   Non-negotiable.
3. **The project's current visual identity** — see PROJECT INSTRUCTIONS for where it
   lives (existing tokens, palette, typography, components, brand assets).

## Prime directive — IMPROVE, don't replace
The project already has a visual identity. Your job is to **refine it into the RM30
role system, not invent a new look.**
- First, **extract** the current identity: brand colors, typography, radius feel,
  spacing rhythm, and the existing "signature" moments — from the project's real
  code/design (paths in the project instructions).
- Then **map and improve** it onto the `--rm-*` roles: keep what defines the brand;
  improve token discipline, accessibility (AA contrast), dark-mode completeness, and
  consistency.
- Every deviation from the current look must be a **justified improvement**
  (contrast, consistency, a missing dark value), never novelty for its own sake.
  Nothing already good is discarded just to change it.

---

## The work runs in TWO GATED STAGES
**Do NOT start Stage B until the user has explicitly approved Stage A.**

Place all output in the project's blueprints folder (from the project instructions):
monorepo → `[project]/apps/blueprints/`; single web app → `[project]/blueprints/`.

### STAGE A — Project theme + proof, then STOP for approval
Deliverables:

1. **`theme.css`** — layers on top of the RM30 shared `theme.css` (the fetched file
   above) and fills **every** `--rm-*` role as a **complete light/dark pair** with the
   project's real identity values. The shared file is imported, never edited — for the
   web preview via
   `@import url("https://raw.githubusercontent.com/rm30-dev/design/main/theme.css");`
   (the app-integration mechanism for mobile is decided in a later phase).
   - No role left as a placeholder.
   - A project-specific extra role is allowed **only** via the documented pattern
     (a new `--rm-*` light/dark pair + one line in the project's own `@theme inline`).
   - No hardcoded value leaks into anything downstream: the identity lives entirely
     in the `--rm-*` values.

2. **Design rationale** (a `README.md` in the blueprints folder, or a header comment
   block in `theme.css`) — a few sentences, intent not essay:
   *"Briefly describe the visual mood, the colour theory behind the palette, and the
   feeling the user should experience."* The palette choices must be traceable to
   this rationale.

3. **`preview.html`** — a SELF-CONTAINED page that demonstrates the theme **in
   practice**, so the user can approve the direction by looking at it:
   - Real example components (e.g. badge, button, card, input, list row, a small
     representative page/section) — enough to show the identity, not the full library.
   - Shown in **both light and dark**, side by side or toggleable.
   - Uses **only** the theme's roles/scales (no hex, no off-scale value).
   - It must visually **prove** the mood described in the rationale.
   - Fully inline (no external CSS/JS/fonts/images via network); embed what you need.

**Then STOP.** Present the rationale + `preview.html` and ask the user to confirm the
direction before proceeding. Iterate on feedback until approved.

### STAGE B — Component library (only after Stage A is approved)
- Build the HTML component library in the blueprints folder covering **all** the
  project's components — mirroring the app's real vocabulary — **driven by the
  approved `theme.css`** (generated from the tokens, not hand-transcribed), in light
  and dark, with the relevant states (default / selected / disabled / loading /
  empty / error).
- Where a component-polishing tool is available (e.g. impeccable.style), use it to
  refine the library and enforce the tokens; otherwise skip it.

---

## Constraints (both stages)
- Honor `DESIGN_SYSTEM.md` to the letter: tokens only, roles only, light/dark pairs,
  AA contrast, mirrored web/mobile **where the project has a mobile app**, one
  signature moment per component.
- Tailwind CSS v4 syntax throughout.
- **Blueprints only** — do not modify the real app source in this phase. (Wiring the
  theme into the running apps is a later phase.)
- All HTML is self-contained: no network dependencies.

## Verification before handing back Stage A
- Every `--rm-*` role is filled, both light and dark; no placeholder remains.
- `preview.html` renders correctly in both themes using tokens only (grep your own
  output for stray hex / arbitrary `[...]` classes — there must be none).
- The rationale is present and the preview visibly matches it.
- List the roles you assigned and confirm the identity was **extracted and improved**,
  not replaced.

---

<!-- ============================================================================
     APPEND PROJECT INSTRUCTIONS BELOW THIS MARKER
     - which project, where its blueprints folder goes
     - where the current visual identity lives (files/URLs to extract from)
     - how the RM30 shared theme.css is made available to the project
     - brand specifics to preserve (fonts, signature colours, platform feel)
     - the project's component inventory (or how to derive it) for Stage B
     ============================================================================ -->
