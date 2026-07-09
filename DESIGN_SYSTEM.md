# RM30 Design System — structural rules

Shared foundations for every RM30 project. This document accompanies
`theme.css` (same folder / same Gist): the CSS declares **what** exists
(rigid scales + color-role contract), this document declares **how** it is
used.

How everything fits together:

```
RM30 (shared)                        theme.css + DESIGN_SYSTEM.md   ← you are here
        │  each project imports it and fills the roles
        ▼
[project]/apps/blueprints/theme.css   identity: real values for the --rm-* roles
        │
        ▼
[project]/apps/{landing,mobile}/      real components, tokens only
        (web-only projects: the app root itself, no apps/ folder)
```

---

## 0. The token contract

### Rigid scales (identical everywhere, never extended per project)

| Scale | Steps | Note |
|---|---|---|
| spacing | `0 · px · 0.5 · 1 · 2 · 3 · 4 · 5 · 6 · 8 · 10 · 12 · 16 · 20` (4px grid) | Tailwind's dynamic scale is disabled: off-scale = error |
| type | `xs · sm · base · lg · xl · 2xl · 3xl · 4xl` (paired line-height) | all in `rem` |
| font-weight | `normal · medium · semibold · bold` (400/500/600/700) | |
| leading | `tight · normal · relaxed` (1.25/1.5/1.75) | |
| radius | `none · sm · md · lg · xl · 2xl · full` | components use the roles below |
| semantic radius | `card` (=lg) · `button` (=md) · `input` (=md) · `pill` (=full) | re-pointable to another step, never off-scale |
| shadow | `xs · sm · md · lg · xl · 2xl` | web-first; on mobile prefer `bg-raised` |

### Color roles (abstract — each project fills them, as light/dark pairs)

| Group | Roles | Use |
|---|---|---|
| Brand | `brand-primary` · `brand-deep` · `brand-soft` | identity, primary actions, hover/pressed, chips/highlights |
| Surfaces | `bg-main` · `bg-surface` · `bg-raised` | page · cards/panels · raised layer/hover |
| Lines | `line` · `line-strong` | dividers · interactive borders (inputs) |
| Text | `text-main` · `text-soft` · `text-faint` | primary · secondary · tertiary/decorative |
| Semantic | `ok`/`ok-soft` · `warn`/`warn-soft` · `crit`/`crit-soft` | outcome, attention, error; `-soft` = muted background for the full color |
| Data (opt.) | `series-1..4` | categorical palette for charts |

**The golden rules** (apply to every line of UI code, web and mobile):

1. **Tokens only.** No hex, no arbitrary `px`, no off-scale value — not as
   inline style, not as an arbitrary class like `p-[13px]` or `text-[#333]`.
2. **Roles only, never values.** Write `bg-bg-surface`, not "white";
   `text-crit`, not "red". Tailwind's default palette is disabled on purpose.
3. **Need a new color?** Add a *role* in the project theme (complete
   light/dark pair), never a value in the component.
4. **At most one "signature moment" per component.** The rest is sober and
   reusable.

---

## 1. Mirrored Web / Mobile components

Scope: mirroring applies **only where the project has a mobile app**.
Web-only projects still follow tokens and roles.

A component is "mirrored" when a user moving from web to mobile recognizes
the **same object**: same name, same variants, same visual hierarchy, same
color roles.

### Identical by construction (non-negotiable)

- **Color roles**: same variant ⇒ same roles. If the `ok` Badge is
  `bg-ok-soft` + `text-ok` on web, it is on mobile too.
- **Spacing**: same scale steps (px-2 is px-2 everywhere).
- **Typography**: same scale step and same weight for the same element.
- **Radius**: same semantic roles (`rounded-pill` is a pill everywhere).
- **Component API**: same name, same variants/props, same states
  (default/hover-pressed/disabled/loading) with the same semantics.

### May (and must) diverge: native chrome

- **Primitives**: `span`/`div` vs `View`/`Text` — on React Native text color
  is NOT inherited from the container: `text-*` classes live on the `<Text>`,
  `bg-*` classes on the `<View>`.
- **Navigation and platform containers**: tab bars, headers, sheets,
  modals — use the native pattern, don't imitate the web.
- **Interaction feedback**: `hover:` only exists on web; on mobile the
  equivalent is the pressed state (+ optional haptics).
- **Elevation**: web may use `shadow-*`; on mobile prefer a surface change
  (`bg-raised`).
- **Fonts**: on mobile each weight is a dedicated font file; the weight
  *names* (`font-medium`, …) stay identical.

Rule of thumb: **tokens and structure never diverge; only the way the
platform mounts them does.**

---

## 2. Dark mode

Roles exist as **light/dark pairs** defined in the theme
(`:root` / `.dark`). Consequences:

1. **Components don't know dark mode exists.** You write `bg-bg-surface`
   once: the pair flips underneath. Using `dark:` for a role color inside a
   component is a bug (it means a role is missing or a pair is wrong).
2. **No role is born without its dark half.** A new role with only the light
   half doesn't get merged.
3. **Per-platform activation** — the theme only defines the pairs:
   - **Web**: `.dark` class on `html` (toggle) and/or a
     `prefers-color-scheme` mapping; the web project declares
     `@custom-variant dark (&:where(.dark, .dark *));` in its own globals
     for the legitimate structural uses of `dark:` (rare and justified).
   - **Mobile**: the settings store sets the color scheme; uniwind resolves
     the pair. No class, no logic in components.
4. **In dark, elevation is expressed through surfaces** (`bg-main` →
   `bg-surface` → `bg-raised`), not by darkening shadows.

---

## 3. Accessibility (minimum constraints, not aspirations)

**Contrast (WCAG AA)** — constrains the *values* a project assigns to the
roles, in both pairs:

- `text-main` ≥ 4.5:1 on **every** `bg-*` surface.
- `text-soft` ≥ 4.5:1 on `bg-surface`.
- `text-faint` is exempt because it is decorative only: never the sole
  carrier of information, never for essential text.
- Every full semantic color ≥ 4.5:1 on its **own** `-soft` (e.g. `ok` on
  `ok-soft`) and ≥ 3:1 on `bg-surface`.
- `line-strong` ≥ 3:1 on `bg-surface` (borders of interactive components).
- Text ≥ 24px (or ≥ 18.5px bold): threshold relaxed to 3:1.

**Touch/click targets**

- Minimum interactive area **44×44** (pt iOS / dp Android / px web):
  `size-12` (48px) satisfies it; a smaller icon carries the padding it
  needs, never a smaller target.
- Adjacent targets separated by at least `spacing-2`.

**Visible focus (web)**

- Never remove the outline without replacing it. Standard:
  `outline: 2px solid brand-primary; outline-offset: 2px` on
  `:focus-visible`.

**Scalable text**

- Web: the type scale is in `rem` — never `px` for text; user zoom must
  keep working.
- Mobile: `allowFontScaling` stays on; layouts must survive text at ~1.3×
  (no fixed heights on text containers).

**Motion**: honor `prefers-reduced-motion` (web) / system reduce-motion
(mobile) for every non-essential animation.

---

## 4. The "perfect" mirrored example: Badge

Same component, same variants, same tokens. Only the platform primitives
change. No hardcoded value in either version: fill the roles in the project
theme and both change skin without touching a line.

### Web — React + Tailwind v4

```tsx
// Badge.tsx (web)
const TONES = {
  brand: 'bg-brand-soft text-brand-deep',
  ok:    'bg-ok-soft text-ok',
  warn:  'bg-warn-soft text-warn',
  crit:  'bg-crit-soft text-crit',
} as const

export function Badge({
  tone = 'brand',
  children,
}: {
  tone?: keyof typeof TONES
  children: React.ReactNode
}) {
  return (
    <span
      className={`inline-flex items-center rounded-pill px-2 py-0.5 text-xs font-medium ${TONES[tone]}`}
    >
      {children}
    </span>
  )
}
```

### Mobile — React Native + uniwind

```tsx
// Badge.tsx (mobile)
import { Text, View } from 'react-native'

const TONES = {
  brand: { box: 'bg-brand-soft', label: 'text-brand-deep' },
  ok:    { box: 'bg-ok-soft',    label: 'text-ok' },
  warn:  { box: 'bg-warn-soft',  label: 'text-warn' },
  crit:  { box: 'bg-crit-soft',  label: 'text-crit' },
} as const

export function Badge({
  tone = 'brand',
  children,
}: {
  tone?: keyof typeof TONES
  children: string
}) {
  return (
    <View
      className={`self-start flex-row items-center rounded-pill px-2 py-0.5 ${TONES[tone].box}`}
    >
      <Text className={`text-xs font-medium ${TONES[tone].label}`}>
        {children}
      </Text>
    </View>
  )
}
```

### Why it's mirrored — token-by-token audit

| Class | Token in theme.css | Web | Mobile |
|---|---|---|---|
| `bg-ok-soft` (per tone) | `--color-ok-soft` → `--rm-ok-soft` pair | ✅ | ✅ (on the `View`) |
| `text-ok` (per tone) | `--color-ok` → `--rm-ok` pair | ✅ | ✅ (on the `Text`) |
| `rounded-pill` | `--radius-pill` (= `--radius-full`) | ✅ | ✅ |
| `px-2` / `py-0.5` | `--spacing-2` / `--spacing-0.5` | ✅ | ✅ |
| `text-xs` | `--text-xs` + paired line-height | ✅ | ✅ |
| `font-medium` | `--font-weight-medium` | ✅ | ✅ (via the weight's font file) |

Divergences — all of them, and only, platform-level:

- `span` + color inheritance ↔ `View`/`Text` with color classes on the
  `Text` (RN doesn't inherit color from the container).
- `inline-flex` (web inline flow) ↔ `self-start flex-row` (RN is flex-only;
  stretching must be prevented).
- Dark mode: zero `dark:` in both — the `--rm-*` pairs flip on their own.

---

## 5. Checklist for every new component

- [ ] Tokens only: no hex, no off-scale value, no arbitrary class.
- [ ] Roles only: no color "by name" (the default palette doesn't exist).
- [ ] No `dark:` for role colors; any new role is born as a pair.
- [ ] If the project has mobile: same API, variants, and roles on the other platform.
- [ ] AA: text ≥ 4.5:1, targets ≥ 44, visible focus, scalable text.
- [ ] At most one signature moment; the rest sober.
