# Web Dev Foundations — HTML, CSS & the DOM (Masterclass Notes)

> Built from Lectures 1–2 of the Web Dev sprint (the **CogniDeck** flashcard project).
> Scope: **HTML structure, CSS styling, vanilla JavaScript + DOM manipulation, and browser APIs.** Tailwind, React, backend/APIs, and GitHub collaboration are intentionally left out — they belong to later lectures.

---

## 0. The Mental Model: What Are We Actually Building?

A web page is **three layers stacked on top of each other**, each with one job. This separation is the single most important idea in front-end development, and interviewers test whether you truly understand *why* the split exists.

| Layer | Technology | One-line job | Analogy (a house) |
|-------|-----------|--------------|-------------------|
| **Structure** | HTML | What content exists & how it's organized | The walls, rooms, doors |
| **Presentation** | CSS | How that content looks | Paint, furniture, decor |
| **Behavior** | JavaScript | What happens when the user interacts | Electricity, plumbing, the doorbell that *does* something |

### Why this layering matters (the real insight)

A button is the perfect teaching example:

```
HTML  → the button exists and says "Save"
CSS   → the button is blue, rounded, has padding
JS    → clicking the button actually saves data
```

Without JS, a button is just a pretty rectangle. It **looks** clickable but does nothing. Interactivity — "when X happens, do Y" — is *exclusively* JavaScript's job in the browser.

> **Interview framing:** "Why separate HTML, CSS, and JS instead of mixing them?"
> **Weak answer:** "It's cleaner."
> **Strong answer:** "**Separation of concerns.** Each layer changes for different reasons and at different rates. A designer can restyle the whole site by touching only CSS. A bug in interaction logic is isolated to JS. Mixing them produces *spaghetti code* that's impossible to debug as the project grows. Separation also enables caching (CSS/JS files cached separately), parallel teamwork, and reuse."

### Where this fits in the larger journey
The course roadmap is: **HTML → CSS → JS → (Tailwind) → (React) → (backend/APIs/auth/DB) → (BaaS) → (GitHub).** The key argument the instructor makes: **frameworks change, fundamentals don't.** jQuery dominated for a decade, then React replaced it — but both are *just JavaScript with different syntax*. If your JS fundamentals are strong, switching frameworks is trivial. **This is why you never skip vanilla JS to jump straight to React.**

---

## 1. Tooling & Setup (Briefly, but Know the "Why")

- **Editor/IDE:** VS Code (an *IDE* = Integrated Development Environment — editor + tools combined).
- **Live Server extension:** Gives a "Go Live" button that opens your page in the browser and **auto-reloads on every save**.

### The concept behind Live Server
Without it, you'd open `index.html` directly in Chrome and **manually refresh after every change**. Live Server creates a live connection between your editor and the browser, so edits reflect instantly. This is a *hot-reload* workflow — a productivity multiplier, not magic.

- **Emmet boilerplate:** typing `!` + Enter in an `.html` file generates the standard HTML skeleton.

---

## 2. HTML Deep Dive

### 2.1 The Anatomy of Every HTML Document

```html
<!DOCTYPE html>          <!-- 1. Tells the browser: this is HTML5 -->
<html lang="en">         <!-- 2. Root element; lang helps SEO & screen readers -->
  <head>                 <!-- 3. Metadata + links to external resources -->
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>CogniDeck</title>
  </head>
  <body>                 <!-- 4. Everything the user actually SEES -->
    ...
  </body>
</html>
```

**How the browser reads this:** top to bottom, line by line. When it hits `<!DOCTYPE html>` on line 1, it knows to parse the rest as HTML. This top-to-bottom reading is a recurring theme — it explains the CSS cascade, why `<link>` goes in `<head>`, and why `<script>` placement matters.

### 2.2 `<head>` vs `<body>` — The Core Distinction

| `<head>` | `<body>` |
|----------|----------|
| **Not visible** on the page | **Everything visible**: text, images, buttons, links |
| Holds *links to external things*: CSS files, Google Fonts, CDNs, page title, favicon, meta tags | The actual content & structure |

**Rule of thumb:** anything you're *linking from outside* (stylesheet, font, icon library via CDN) goes in `<head>`.

### 2.3 Tags, Elements & Attributes

```html
<button class="btn" id="saveBtn">Save</button>
   ↑      ↑              ↑          ↑       ↑
opening  attribute    attribute  content  closing
 tag                                        tag
```

- **Most tags come in pairs:** `<body>...</body>`.
- **Some are self-closing** (no content, no closing tag): `<meta>`, `<input>`, `<img>`, `<link>`.
- **Attributes** provide extra information to a tag: `lang="en"`, `type="text"`, `href="..."`, `class="..."`.

> **Interview gotcha:** "What's the difference between a tag, an element, and an attribute?"
> - **Tag** = the markup token (`<button>`).
> - **Element** = the opening tag + content + closing tag as a whole unit.
> - **Attribute** = a key/value pair inside the opening tag that configures the element.

### 2.4 Semantic HTML — A Major Interview Topic

In the old days everyone nested `<div>` inside `<div>` inside `<div>`. The problem: **a `<div>` has no meaning.** It tells the browser, search engines, and screen readers *nothing* about what it contains.

**Semantic elements** carry meaning: `<header>`, `<footer>`, `<main>`, `<aside>`, `<section>`, `<nav>`, `<article>`, `<h1>`–`<h6>`.

**Why it matters (two concrete reasons):**
1. **SEO** — Google's crawler ranks pages partly by understanding their structure. A page built from meaningless `<div>`s ranks worse than one with proper `<header>`/`<main>`/`<article>`.
2. **Accessibility** — screen readers use semantic landmarks to let visually-impaired users navigate ("jump to main content").

> **The rule the instructor enforces:** *"Every time you get a chance to use a semantic element, use it. Don't reach for `<div>` by default."*

> **Interview question:** "Is there a *functional* difference between `<div>` and `<section>` in the browser's rendering?"
> **Strong answer:** "Visually, no — both are block-level boxes with no default styling. The difference is **semantic**: `<section>` communicates a thematic grouping to crawlers and assistive tech. The browser renders them identically, but the *meaning* — which drives SEO and accessibility — differs."

### 2.5 `class` vs `id` — Convention Is Everything

| | `class` | `id` |
|---|---------|------|
| Uniqueness | Reusable — many elements can share it | **Unique** — one per page |
| Multiple per element? | Yes (`class="btn btn-primary"`) | No |
| **Primary use (convention)** | **Styling (CSS)** | **Selecting in JavaScript** |

```html
<button class="btn btn-primary" id="saveFlashcardBtn">Save</button>
```
- `class="btn btn-primary"` → two classes, both used by CSS for styling.
- `id="saveFlashcardBtn"` → unique handle used by JS to grab this exact button.

> **Why the convention?** It's not enforced by the browser — you *can* style by `id` and select by `class`. But the team convention (classes for CSS, ids for JS hooks) keeps responsibilities clear and avoids accidental coupling between styling and behavior.

### 2.6 Forms: The `for` / `id` Connection

```html
<label for="flashcardTitle">Title</label>
<input type="text" id="flashcardTitle" />
```

When `label`'s `for` **matches** the `input`'s `id`, the two are **linked**: clicking the label focuses (highlights) the input. This improves UX (bigger click target) and accessibility (screen readers announce the label when the input is focused).

**Input `type` controls behavior:**

| `type` | Effect |
|--------|--------|
| `text` | plain text |
| `password` | masked dots |
| `date` | date picker |
| `color` | color picker |
| `checkbox`, `email`, `file`, `hidden` | … |

**`<textarea>` vs `<input>`:** a textarea is multi-line and (by default) drag-resizable.
**`placeholder`** = the hint text shown when the field is empty.

> **How to learn any tag/property:** the instructor's repeated advice — go to **MDN** (`<thing> MDN`). MDN lists every attribute, value, and browser-compatibility table. *"My job is to explain the code I write; your job is to explore the full API on MDN."*

### 2.7 Icons via Font Awesome + the CDN Concept

Instead of drawing SVG icons by hand, use a library like **Font Awesome**:

```html
<i class="fas fa-layer-group"></i>
```

But the `<i class="...">` alone shows **nothing** — the browser doesn't know what `fa-layer-group` means. You must load Font Awesome's CSS, which contains the actual icon definitions. The easy way: a **CDN link** in `<head>`.

#### What is a CDN, and why does it exist?

**CDN = Content Delivery Network.**

**The problem it solves — latency.** Suppose a file lives on a server in the US, and a user in India requests it. The request travels across the globe and back → noticeable delay (latency).

**The solution.** A CDN copies your common files (CSS, JS, fonts) to servers **distributed worldwide**. A user's request is routed to the **nearest** server.

```
WITHOUT CDN:                      WITH CDN:
India user ───────────► US server   India user ──► India edge server (copy)
          ◄───────────              (fast, low latency)
   (long distance = high latency)
```

> **Interview nuance the instructor adds:** for **production** apps you usually **install the package** (e.g., via npm) rather than rely on a third-party CDN — so you control the version and don't break if the CDN goes down. CDN links are great for **getting started / prototyping**.

---

## 3. CSS Deep Dive

### 3.1 Three Ways to Write CSS — and Why We Pick One

1. **Inline** (`style="..."` on the element) — avoid.
2. **Internal** (`<style>` block in `<head>`) — avoid for real projects.
3. **External** (separate `styles.css` linked via `<link>`) — ✅ **the right way.**

```html
<link rel="stylesheet" href="./styles.css" />
```

**Why external?** Separation of concerns again. As projects grow, styles-in-HTML become unmaintainable spaghetti. Keep HTML in `.html`, styles in `.css`, behavior in `.js`.

### 3.2 CSS Variables (Custom Properties)

```css
:root {
  --primary: #4361ee;
  --shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  --transition: all 0.3s ease;
}
```
- `:root` = a selector targeting the document root (essentially the whole page) — variables defined here are **global**.
- Use them: `color: var(--primary);`

**Why they exist (the killer use case):** Imagine you've used your brand color in **400 places**. The client says *"change the primary color."* Without variables → edit 400 lines. With variables → **change one line** (`--primary`) and every usage updates automatically.

> This is the same DRY (Don't Repeat Yourself) principle behind functions in JS and reusable classes in CSS.

### 3.3 The CSS Reset

```css
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
```

- `*` = the **universal selector** — selects *every* element.
- This **strips the browser's default styles.**

#### Why reset? (Cross-browser consistency)

Every browser (Chrome, Safari, Edge, Brave) applies its **own default styles** — e.g., a default margin on `<body>`. If you design in Chrome accounting for *its* defaults, then a user opens your site in Safari (different defaults), **your layout breaks.**

The fix: wipe defaults to zero and start from a known, consistent baseline. *"I don't care what the browser offers by default — strip it away."*

> **How to see defaults:** right-click → **Inspect** → hover `<body>` → you'll see a brown margin band you never set. That's the browser's default.

### 3.4 The Box Model — Absolutely Fundamental

Every HTML element is rendered as a **rectangular box** with four layers:

```
┌─────────────────────────────────────┐
│              MARGIN                   │  ← space OUTSIDE the border (separates elements)
│   ┌───────────────────────────────┐   │
│   │           BORDER              │   │  ← the edge line
│   │   ┌───────────────────────┐   │   │
│   │   │       PADDING         │   │   │  ← space between content and border
│   │   │   ┌───────────────┐   │   │   │
│   │   │   │   CONTENT     │   │   │   │  ← text / image
│   │   │   └───────────────┘   │   │   │
│   │   └───────────────────────┘   │   │
│   └───────────────────────────────┘   │
└─────────────────────────────────────┘
```

- **Content** — the text or image.
- **Padding** — space *inside*, between content and border. (Shows as **green** in DevTools.)
- **Border** — the visible edge.
- **Margin** — space *outside* the border, pushing other elements away. (Shows as **brown/orange** in DevTools.)

#### `box-sizing: content-box` (default) vs `border-box`

This is the part that trips everyone up.

**Default (`content-box`):** width applies to **content only**. Padding and border are **added on top**.
```
width: 500px + border 10px each side = 520px actual rendered width  ❌ unexpected
```

**`border-box`:** width includes content + padding + border. The box stays **exactly the width you set**; the *content shrinks* to make room for padding/border.
```
width: 500px with border 10px each side = 500px actual rendered width  ✅ predictable
```

> **Why everyone sets `box-sizing: border-box` globally:** it makes layout math intuitive — `width: 500px` *means* 500px on screen, period. This is one of the most common front-end interview questions. Be able to draw the two scenarios.

### 3.5 The Cascade: Top-to-Bottom Wins

The browser reads CSS top to bottom. **When two rules conflict, the *later* one wins.**

```css
.box { margin: 0; }      /* line 16 */
.box { margin: 20px; }   /* line 20 → THIS wins */
```

> This "later overrides earlier" rule (for equal specificity) is half of how the **C**ascade in **C**ascading **S**tyle **S**heets works. (The other half — *specificity* — is implied by `id` > `class` > tag, worth knowing for interviews.)

Same applies to file loading: `<link>` order in `<head>` matters. The convention used in the project: load external stuff (Google Fonts, Font Awesome CDN) **first**, then your own `styles.css` **last**, so your styles can override library defaults.

### 3.6 Selectors Used in the Project

```css
body { ... }              /* tag selector */
.container { ... }        /* class selector (dot prefix) */
#statsDisplay { ... }     /* id selector (hash prefix) */
.logo i { ... }           /* descendant: <i> INSIDE .logo */
.btn:hover { ... }        /* pseudo-class: on hover */
.form-control:focus { ... } /* pseudo-class: when focused */
```

- **`.logo i`** is a *descendant selector* — "any `<i>` nested anywhere inside an element with class `logo`." Lets you be specific without adding classes to every icon.

### 3.7 Typography

```css
body {
  font-family: "Inter", sans-serif;
  line-height: 1.6;
}
```

- **`font-family` with fallbacks:** `"Inter", sans-serif` means *"use Inter; if it's unavailable, fall back to the system sans-serif font."* You can chain several. Always end with a generic family (`sans-serif`, `serif`) as a guaranteed fallback.
- **Google Fonts:** grab the `<link>` embed code, place it in `<head>` (before your own CSS), and now `Inter` is available.
- **`line-height: 1.6`** — vertical spacing between lines of text. Default ≈ 1; higher values space lines out for readability.
- **`color`** — always refers to **text color**.
- **`font-weight`** — boldness, `100`–`900` (`700` = bold).

### 3.8 Centering a Container: `max-width` + `margin: 0 auto`

```css
.container {
  max-width: 1200px;
  margin: 0 auto;
}
```

**How it works:**
1. `max-width: 1200px` caps the element's width. On a wide screen, there's now leftover space on the sides.
2. `margin: 0 auto` → shorthand for *top/bottom = 0, left/right = auto*. `auto` tells the browser to **split the leftover space equally** left and right → element is centered.

> **Critical gotcha (an interview favorite):** `margin: auto` only centers horizontally **if the element has a constrained width.** Without `max-width`/`width`, a block element fills 100% — there's no leftover space to distribute, so `auto` does nothing. **First constrain width, then auto-margin centers it.**

**Margin shorthand recap:**
- `margin: 0;` → all four sides.
- `margin: 0 auto;` → vertical | horizontal.
- (also exists: 3-value and 4-value forms.)

### 3.9 Units: `rem`, `px`, `%`, `fr`

| Unit | Meaning |
|------|---------|
| `px` | absolute pixels — fixed |
| `rem` | relative to **root font size**. Default `1rem = 16px`, so `2rem = 32px`, `1.5rem = 24px` |
| `%` | relative to the **parent element's** corresponding dimension |
| `fr` | "fraction" — used in Grid to divide available space |

**`%` example:** `width: 100%` on an input → if its parent is 1000px and you set `50%`, the input becomes 500px. The browser computes it from the parent's width at render time.

> **Why prefer `rem` over `px` for spacing/fonts?** Accessibility & scalability — if a user bumps their browser's base font size, `rem`-based layouts scale with them; `px` won't.

### 3.10 Flexbox

```css
header {
  display: flex;              /* children sit side-by-side (a "flex row") */
  justify-content: space-between; /* push children to opposite ends */
  align-items: center;        /* vertically center children */
}
.logo { display: flex; align-items: center; gap: 0.5rem; }
```

| Property | What it does |
|----------|--------------|
| `display: flex` | Children line up in a row (instead of stacking) |
| `justify-content` | Alignment along the **main (horizontal) axis** — `space-between` pushes items to the edges with space in between |
| `align-items` | Alignment along the **cross (vertical) axis** — `center` vertically centers |
| `gap` | Consistent spacing **between** flex children |

> **Teaching trick from the lecture:** to *see* `align-items: center` work, you need height — they temporarily added `height: 200px` to the header so the vertical centering was visible, then removed it. (Pure teaching aid, not real styling.)

### 3.11 Block vs Inline Elements

```html
<p>some text</p>        <!-- BLOCK: takes full width, pushes next element to a new line -->
<span>some text</span>  <!-- INLINE: takes only its content's width, sits side-by-side -->
```

| Block (`<p>`, `<div>`, `<h1>`, `<section>`) | Inline (`<span>`, `<i>`, `<a>`) |
|---|---|
| Takes the **full available width** | Takes only the **width of its content** |
| Forces the next element onto a **new line** | Allows neighbors to sit **side-by-side** |

You can override this with `display`: `display: block`, `display: inline`, `display: inline-flex`, etc.

> **`display: inline-flex`** (used on `.btn`): the button behaves as inline (flows with text, sizes to content) **but** its *children* are laid out with flexbox (icon + text aligned via `align-items: center` and spaced with `gap`).

### 3.12 Buttons, Border-Radius, Cursor

```css
.btn {
  padding: 0.75rem 1.5rem;   /* vertical | horizontal */
  border: none;
  border-radius: 12px;        /* rounded corners */
  cursor: pointer;            /* hand cursor → signals "clickable" */
  transition: all 0.3s ease;
}
```
- **`border-radius`** rounds corners. `0` = sharp.
- **`cursor: pointer`** changes the arrow to a hand on hover — a *visual affordance* telling users "this is clickable."

### 3.13 Hover States & Transitions — UX Polish

```css
.btn-primary:hover {
  background-color: var(--secondary);
  transform: translateY(-2px);   /* lift the button up 2px */
}
```

- **`:hover`** applies styles only while the mouse is over the element.
- **`transform: translateY(-2px)`** nudges the element up 2px → a subtle "lift" effect.

**The role of `transition`:** without it, hover changes happen **instantly and jerkily**. `transition: all 0.3s ease` makes every animatable property change **smoothly over 0.3 seconds**.

```
No transition:   [normal] ──CLICK──► [hover]      (jarring snap)
With transition: [normal] ──smooth glide 0.3s──► [hover]  (polished)
```

> **Interview insight:** transitions are about *perceived quality*. Same logic, but smooth animation makes an app feel professional. Know that `transition` animates *between* two states (e.g., normal ↔ hover, normal ↔ focus).

### 3.14 Focus State (Inputs)

```css
.form-control:focus {
  outline: none;                              /* remove default focus ring */
  border-color: var(--primary);              /* custom highlight */
  box-shadow: 0 0 0 2px rgba(67,97,238,0.2);  /* soft glow */
}
```
- `:focus` triggers when the user clicks into / tabs to an input.
- `outline: none` removes the browser's default (often ugly) focus outline — but **you should always replace it** with your own visible focus indicator (here, border + box-shadow) for accessibility.

### 3.15 `box-shadow`, `overflow`, `resize`

```css
.flashcard { box-shadow: var(--shadow); overflow: hidden; }
textarea.form-control { min-height: 100px; resize: vertical; }
```
- **`box-shadow`** adds depth — makes an element appear lifted off the page.
- **`overflow: hidden`** — if content exceeds the element's size, the excess is **clipped/hidden** instead of spilling out.
- **`resize: vertical`** — lets users drag-resize a textarea **only vertically** (default allows both directions).

### 3.16 CSS Grid

```css
.main-content {
  display: grid;
  grid-template-columns: 1fr 2fr;  /* two columns; 2nd is twice as wide */
  gap: 2rem;
}
```

- **`grid-template-columns: 1fr 2fr`** → two columns sharing space in a 1:2 ratio. `1fr 1fr` = equal halves. Add a third value → third column.
- **`gap`** → spacing between grid tracks.

#### Responsive auto-fitting grid

```css
.flashcards-container {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  gap: 1.5rem;
}
```
Read it as: *"Make as many columns as fit, each at least 300px wide; if there's leftover space, stretch them (`1fr`) to fill it."*
- `minmax(300px, 1fr)` → each column is **minimum 300px, maximum 1 fraction** of free space.
- `auto-fill` → automatically create more columns (4th, 5th…) as the screen widens, as long as each stays ≥ 300px.

This gives a **fluid, responsive card layout with zero media queries.**

#### Spanning across all columns

```css
.empty-state { grid-column: 1 / -1; }
```
`1 / -1` = start at the **first** grid line, end at the **last** (`-1`). The element spans **every column** — used so the "No flashcards" message stretches full width instead of sitting in one column.

### 3.17 Media Queries — Responsive Design

```css
@media (max-width: 768px) {
  .main-content { grid-template-columns: 1fr; }
}
```

**Meaning:** *"When the viewport is 768px wide or narrower, use a single column."* Above 768px, the earlier two-column rule applies.

**Why this is non-negotiable:** users view your site on phones, tablets, laptops, big monitors. The same layout can't look good everywhere. Media queries let one stylesheet adapt to screen size.

> **You can test this** in DevTools by dragging the viewport width — watch the layout flip from 2 columns to 1 as you cross 768px.

> **Interview term:** "mobile-first" vs "desktop-first." `max-width` queries = desktop-first (start with desktop styles, override for smaller screens). `min-width` queries = mobile-first. Both are valid; the project uses desktop-first here.

### 3.18 The `position` Property — A Top Interview Topic

```css
.flashcard-form { position: sticky; top: 2rem; }
.flashcard-actions { position: absolute; bottom: 1rem; right: 1rem; }
.study-mode { position: fixed; top: 0; left: 0; }
```

| Value | Behavior |
|-------|----------|
| **`static`** | Default. Element sits in normal document flow. |
| **`relative`** | Stays in flow, but can be offset (`top`/`left`) relative to its *original* position. |
| **`absolute`** | **Removed from normal flow.** Positioned relative to the nearest *positioned* ancestor. Other elements behave as if it isn't there. |
| **`sticky`** | Acts normal until you scroll past a threshold (e.g., `top: 2rem`), then "sticks" in place. |
| **`fixed`** | Removed from flow, positioned relative to the **viewport** — stays put even when scrolling. |

**In the project:**
- `position: sticky; top: 2rem` on the form → form scrolls normally, then sticks 2rem from the top once you scroll far enough (great for keeping the create-form visible).
- `position: absolute; bottom/right: 1rem` on card action buttons → pins them to the card's bottom-right corner (the card is the positioning context).
- `position: fixed` on the study modal & toast → they overlay everything and ignore scroll.

> **Key concept:** `absolute` and `fixed` **remove the element from normal document flow** ("taken out of the page"). You then place it precisely using `top`/`right`/`bottom`/`left`.

### 3.19 Colors: Hex vs RGBA

```css
background-color: rgba(0, 0, 0, 0.8);
```
- **`rgb(red, green, blue)`** — each channel 0–255. `(0,0,0)` = black.
- **The `a` in `rgba`** = **alpha / opacity**, `0` (transparent) → `1` (opaque). `0.8` = 80% opaque black.

**Used for the modal overlay:** a semi-transparent black backdrop that dims the page behind the popup while still showing it faintly.

### 3.20 The 3D Card Flip (CSS that powers the study modal)

This is the trickiest CSS in the project. Goal: clicking "Flip" rotates a card to reveal the answer on its back.

```css
.study-card {
  transform-style: preserve-3d;   /* enable real 3D space for children */
  transition: transform 0.6s;     /* animate the flip smoothly */
}
.study-card.flipped {
  transform: rotateY(180deg);     /* flip around vertical axis */
}
.study-card-front,
.study-card-back {
  position: absolute;
  backface-visibility: hidden;    /* hide the side facing away */
}
.study-card-back {
  transform: rotateY(180deg);     /* pre-flip the back so it reads correctly */
}
```

**How it works, step by step:**
1. Front and back faces are stacked on top of each other (`position: absolute`).
2. `backface-visibility: hidden` → when a face is rotated away from you, it becomes invisible.
3. The **back** is pre-rotated 180° so that when the whole card flips, the back ends up facing you the right way around.
4. Adding `.flipped` rotates the entire card 180° on the Y-axis. Now the front faces away (hidden) and the back faces you.
5. `transition: transform 0.6s` makes it a smooth flip, not an instant swap.

```
Initial:   [FRONT visible] | [back rotated 180°, hidden behind]
Add .flipped → rotateY(180deg):
           [front now hidden] | [BACK now visible & readable]
```

JS just **toggles the `.flipped` class** — CSS does all the animation.

---

## 4. JavaScript & the DOM

### 4.1 What Is the DOM?

**DOM = Document Object Model.** When the browser parses your HTML, it builds an in-memory **tree of objects** representing every element. JavaScript can read and modify this tree — and any change is **immediately reflected on the page**. This is **DOM manipulation**: adding, removing, or changing HTML/CSS via JS.

`document` = the JS object representing the entire page (the root of the DOM tree).

### 4.2 Selecting Elements

```js
const saveBtn = document.getElementById("saveFlashcardBtn");  // by id
const editBtn = flashcardElement.querySelector(".edit-btn");  // by CSS selector, within a parent
```

| Method | Selects |
|--------|---------|
| `getElementById("x")` | the element with `id="x"` |
| `querySelector(".x")` | the **first** element matching a CSS selector |
| `getElementsByClassName("x")` | all elements with that class (a collection) |

> **Note on `querySelector`:** it takes a **CSS selector string** — `.edit-btn` (class), `#id` (id), `div` (tag). You can call it on `document` (whole page) **or on a specific element** (`flashcardElement.querySelector(...)`) to search only *within* that element. The project uses the latter to find each card's own buttons.

### 4.3 Events & Event Listeners — How Interactivity Works

```js
saveBtn.addEventListener("click", saveFlashcard);
```

- **Event** = something that happens (click, scroll, mouseover, keypress, zoom…).
- **`addEventListener(eventType, callback)`** — "when this event fires on this element, run this function."
- **Callback function** = the function you *hand to* the browser to call later. You don't call it yourself; the browser calls it when the event occurs.

```
You: "Hey browser, whenever saveBtn is CLICKED, run saveFlashcard()."
Browser: stores that instruction, runs saveFlashcard() on each click.
```

**Passing arguments to a callback** — wrap it in an arrow function:
```js
editBtn.addEventListener("click", () => {
  editFlashcard(card.id);   // need to pass the id, so wrap in () => {}
});
```

> **Why the wrapper?** `addEventListener` calls your callback with the *event object*, not your custom arguments. To pass `card.id`, you provide an arrow function that calls `editFlashcard(card.id)` when invoked.

### 4.4 `let` vs `const`

```js
const saveBtn = document.getElementById("saveFlashcardBtn"); // never reassigned
let flashcards = JSON.parse(localStorage.getItem("flashcards")) || [];  // gets reassigned later
```

| `const` | `let` |
|---------|-------|
| Value **cannot be reassigned** | Value **can be reassigned** |
| Use when the binding is fixed | Use when you'll reassign (e.g., `flashcards = flashcards.filter(...)`) |

> **Default to `const`.** Reach for `let` only when you know you'll reassign. (`var` exists but is avoided in modern code due to scoping quirks — an interview talking point.)

### 4.5 Template Literals

```js
statsDisplay.textContent = `${count} flashcard${count !== 1 ? "s" : ""}`;
```

- Backticks `` ` `` instead of quotes.
- **`${...}`** embeds any JS expression directly in the string.
- Cleaner than the old `"text " + variable + " more"` concatenation.

**Bonus — a ternary doing pluralization:** `count !== 1 ? "s" : ""` → appends "s" unless the count is exactly 1 ("1 flashcard" vs "3 flashcards"). Small UX detail that interviewers notice.

### 4.6 Creating & Injecting HTML with JS

```js
const flashcardElement = document.createElement("div");  // create a <div> in memory
flashcardElement.className = "flashcard";                // set its class
flashcardElement.dataset.id = card.id;                   // set data-id attribute
flashcardElement.innerHTML = `<h3>${card.title}</h3>...`;// set inner HTML
flashcardsContainer.appendChild(flashcardElement);       // attach to the page
```

| Operation | Method |
|-----------|--------|
| Create an element | `document.createElement("div")` |
| Set its class | `element.className = "..."` |
| Set a data attribute | `element.dataset.id = ...` (creates `data-id="..."`) |
| Set inner HTML | `element.innerHTML = "..."` |
| Set plain text | `element.textContent` / `element.innerText` |
| Attach to DOM | `parent.appendChild(child)` |

> **`innerHTML` vs `textContent`:** `innerHTML` parses the string as HTML (tags become real elements). `textContent`/`innerText` treat the string as plain text. The toast uses `textContent` (just a message); cards use `innerHTML` (need `<h3>`, buttons, etc.).
> **Security note for interviews:** injecting user input via `innerHTML` is an **XSS risk**. For untrusted content prefer `textContent`. (Fine here since it's the user's own local data.)

### 4.7 Accessing Object Properties

Each flashcard is an **object**:
```js
{ id: "...", title: "...", front: "...", back: "...", createdAt: "..." }
```
Access with dot notation: `card.title`, `card.front`, `card.back`, `card.id`.

### 4.8 Array Methods (Heavily Used — and Heavily Interviewed)

#### `forEach` — do something for each element
```js
flashcards.forEach((card) => {
  // build & append a DOM element for each card
});
```
Iterates the array, calling your callback once per element. **Returns nothing** — used for side effects.

```js
const nums = [1, 2, 3, 4, 5];
nums.forEach((num) => console.log(num * 2)); // 2, 4, 6, 8, 10
```

#### `filter` — keep elements that pass a test (returns a NEW array)
```js
flashcards = flashcards.filter((c) => c.id !== id);  // drop the card with this id
```
The callback returns `true`/`false`. Elements where it returns `true` are kept.

```js
const nums = [1, 2, 3, 4, 5];
const odds = nums.filter((n) => n % 2 !== 0); // [1, 3, 5]
```

**How `filter` works under the hood:**
```
For each element → run callback → returns true?  → keep it in the new array
                                  returns false? → exclude it
Original array is UNCHANGED; a new array is returned.
```

#### `findIndex` — locate an element's position
```js
const cardIndex = flashcards.findIndex((card) => card.id === id);
const card = flashcards[cardIndex];
```
Returns the **index** of the first element where the callback is `true` (or `-1` if none).

> **Interview note:** `forEach`, `filter`, `map`, `find`, `findIndex`, `reduce` are *the* core array methods. Know which **mutate vs return new** (`filter`/`map` return new; `forEach` returns nothing; none of these mutate the original). These reappear constantly in React.

### 4.9 The "Functional Programming" Style Used Here

The whole project follows **one function = one job**:
- `saveFlashcard()` — save
- `showToast()` — show notification
- `clearForm()` — reset inputs
- `saveToLocalStorage()` — persist
- `updateStats()` — update the count
- `updateFlashcardsDisplay()` — re-render cards
- `editFlashcard()`, `deleteFlashcard()`, `startStudySession()`, `flipStudyCard()`, `closeStudySession()`

**Why?** Reusability (call `showToast` from 10 places, defined once) and maintainability (a 500-line "god function" is undebuggable). As projects grow, small focused functions keep them comprehensible.

### 4.10 `classList` — Toggling Classes from JS

This is how JS and CSS collaborate:

```js
studyMode.classList.add("active");      // add a class
studyMode.classList.remove("active");   // remove a class
studyCard.classList.toggle("flipped");  // add if absent, remove if present
```

**The pattern:** CSS defines the *look* of a state (`.active` → visible, `.flipped` → rotated). JS just **adds/removes the class** at the right moment. This is the cleanest way to drive UI state changes — and the conceptual seed of how React thinks about state-driven rendering.

- **`toggle`** is especially elegant: one function flips the flip-card on/off.

### 4.11 Re-rendering: The Vanilla JS Pain Point

```js
function updateFlashcardsDisplay() {
  if (flashcards.length === 0) {
    flashcardsContainer.innerHTML = `...empty state...`;
    return;
  }
  flashcardsContainer.innerHTML = "";          // wipe ALL existing cards
  flashcards.forEach((card) => { /* rebuild every card from scratch */ });
}
```

To add **one** new card, vanilla JS **clears everything and rebuilds the entire list.** Even if 8 cards already exist and you add a 9th, all 9 are destroyed and recreated.

> **Why does the instructor highlight this?** It's the **motivation for React** (future lecture). React lets you just *add the 9th item to an array* and it figures out the minimal DOM update ("re-rendering") automatically. Feeling this pain in vanilla JS is *the point* — it's why you'll appreciate React later. (Don't optimize this with `prepend`/manual diffing here; that complexity is exactly what React solves.)

---

## 5. Browser APIs (Web APIs)

### 5.1 Core JS vs Browser APIs — A Crucial Distinction

Some things you use in browser JS are **not part of the JavaScript language** — they're provided by the **browser environment**:

- `setTimeout` / `setInterval`
- `localStorage` / `sessionStorage`
- `document` & all DOM methods
- `fetch`, `alert`, `confirm`, geolocation, etc.

These are **Web APIs / Browser APIs** — the browser hands them to your JS. (MDN lists them all under "Web APIs.")

> **Interview-critical:** "Is `setTimeout` part of JavaScript?" → **No.** It's a browser-provided Web API (Node.js provides its own version). Core JS is the language (syntax, objects, arrays, functions); the runtime environment adds APIs. **Browser support for these APIs varies** — some exist in Chrome but not yet in Safari/Edge — so always check MDN's compatibility table before using a new one.

### 5.2 `setTimeout` — Delayed Execution

```js
setTimeout(() => {
  toast.classList.remove("show");
}, 3000);  // run this function after 3000 milliseconds (3 seconds)
```
Takes **(callback function, delay in milliseconds)**. Used to auto-hide the toast notification after 3 seconds.

### 5.3 `localStorage` vs `sessionStorage` — Persistence

Every page has a key-value storage box, viewable in **DevTools → Application → Storage**.

| | `localStorage` | `sessionStorage` |
|---|---------------|------------------|
| **Lifetime** | **Permanent** — survives tab/browser close; persists until explicitly deleted | **Temporary** — wiped when the tab/browser closes |
| Use case | Persisting user data across sessions (flashcards here) | Per-session scratch data |

```js
localStorage.setItem("flashcards", JSON.stringify(flashcards)); // save
let flashcards = JSON.parse(localStorage.getItem("flashcards")) || []; // load
```

> **Why local, not session, for flashcards?** You want cards to survive a refresh/close. With `localStorage`, hitting refresh re-reads saved cards. Without persistence, refresh would wipe everything (state lives only in a JS variable, which resets on reload).

> **Interview depth:** `localStorage` stores **strings only**, is **synchronous**, ~5–10MB limit, same-origin scoped, and **not secure** (don't store tokens/passwords). For larger/structured data → IndexedDB.

### 5.4 `JSON.stringify` & `JSON.parse` — The String Bridge

**The problem:** `localStorage` can only store **strings**. But `flashcards` is a JavaScript **array of objects.**

```js
// SAVING: array  →  JSON string
localStorage.setItem("flashcards", JSON.stringify(flashcards));

// LOADING: JSON string  →  array (so you can use .filter, .forEach, etc.)
let flashcards = JSON.parse(localStorage.getItem("flashcards")) || [];
```

```
JS array/object  ──JSON.stringify──►  string  ──store──►  localStorage
localStorage  ──read──►  string  ──JSON.parse──►  JS array/object
```

- **`JSON.stringify`** — converts a JS value into a JSON string (so it can be stored).
- **`JSON.parse`** — converts a JSON string back into a usable JS value (so you can run array operations on it).
- **`|| []`** — fallback: if nothing is stored yet (`getItem` returns `null`), default to an empty array.

> **Why both directions matter:** what comes *out* of `localStorage` is always a string. You **must** `JSON.parse` it before calling array methods — calling `.filter()` on a raw string would fail.

### 5.5 `confirm()` — Native Confirmation Dialog

```js
function deleteFlashcard(id) {
  if (confirm("Are you sure you want to delete this flashcard?")) {
    // user clicked OK → confirm() returned true
    flashcards = flashcards.filter((card) => card.id !== id);
    saveToLocalStorage();
    updateFlashcardsDisplay();
    showToast("Flashcard deleted", "success");
  }
}
```
`confirm()` shows a native OK/Cancel dialog and **returns `true` (OK) or `false` (Cancel)**. A simple guard against accidental destructive actions.

### 5.6 DevTools — Your Daily Workspace

Right-click → **Inspect** opens the **dev console**, where you'll spend most of your dev time:

| Tab | Use |
|-----|-----|
| **Elements** | Live HTML tree + the CSS applied to each element; toggle properties on/off to see their effect |
| **Console** | `console.log` output, errors |
| **Sources** | Your JS/CSS files; debugging |
| **Network** | Requests/responses |
| **Application** | **localStorage / sessionStorage** inspection |

> **Practical habit:** toggle a CSS property in Elements (e.g., turn off `display: flex`) to *see* what it does. This is how you build intuition for each property fast.

---

## 6. Putting It Together — How a Click Flows Through the System

Walking the full **"Save a flashcard"** path ties every concept together. This is exactly the kind of end-to-end trace an interviewer loves.

```
1. User types title/front/back, clicks "Save"
        │
2. addEventListener("click", saveFlashcard) fires the callback
        │
3. saveFlashcard():
     • reads input values via getElementById(...).value.trim()
     • validates → if empty, showToast("...", "error") and return
     • builds a flashcard OBJECT { id: Date.now()..., title, front, back, createdAt }
     • flashcards.push(newFlashcard)        ← update state (let array)
     • saveToLocalStorage()                 ← JSON.stringify → localStorage + updateStats()
     • updateFlashcardsDisplay()            ← wipe container, forEach → rebuild cards
     • clearForm()                          ← reset inputs
     • showToast("saved!", "success")       ← textContent + classList.add + setTimeout removal
        │
4. DOM updates → user sees the new card + a toast that auto-hides in 3s
        │
5. On refresh → JSON.parse(localStorage) restores cards → updateStats() + updateFlashcardsDisplay() run globally
```

**Edit flow:** click ✏️ → `editFlashcard(id)` → `findIndex` to locate the card → populate the form fields with its values → `filter` it out of the array (so it disappears from the list while being edited) → save + re-render. Re-saving creates the updated version.

**Delete flow:** click 🗑️ → `confirm()` → `filter` out the card by id → save + re-render + toast.

**Study flow:** click 🎓 → `startStudySession(card)` → `classList.add("active")` reveals the modal (CSS `opacity 0→1`, `pointer-events none→all`) → `updateStudyCard(card)` injects the card's text → click "Flip" → `classList.toggle("flipped")` → CSS 3D rotation reveals the answer → click ✕ → `closeStudySession()` removes both `active` and `flipped` (so it opens fresh next time, not pre-flipped).

> **Subtle bug fixed live in the lecture:** `closeStudySession` must also remove `flipped`. Otherwise, a card you left flipped reopens already showing the answer. Small state-reset detail — but exactly the kind of edge case interviewers probe.

---

## 7. Interview Rapid-Fire (Self-Test)

**HTML**
- Difference between `<div>` and `<section>`? → semantic meaning (SEO/accessibility), identical rendering.
- What does the `for` attribute on `<label>` do? → links to an input's `id`; clicking the label focuses the input.
- Self-closing tags? → `<meta>`, `<input>`, `<img>`, `<link>`.

**CSS**
- Explain the box model and `box-sizing: border-box`. → (draw the two width scenarios).
- How does `margin: 0 auto` center an element, and what's required? → needs constrained width.
- `position` values? → static / relative / absolute / sticky / fixed; which leave normal flow (absolute, fixed).
- What does `transition` do, and why? → smoothly animates between states; perceived quality.
- Why a CSS reset? → eliminate cross-browser default inconsistencies.
- `1fr 2fr` in grid? → proportional column sizing.
- Purpose of media queries? → responsive layouts per screen size.
- Why CSS variables? → single source of truth; change once, update everywhere.

**JavaScript / DOM**
- What is the DOM? → in-memory object tree of the page; JS manipulates it.
- `forEach` vs `filter` vs `findIndex`? → side effects / new filtered array / index lookup.
- `let` vs `const`? → reassignable vs not.
- Is `setTimeout`/`localStorage` part of JavaScript? → No — browser Web APIs.
- `localStorage` vs `sessionStorage`? → permanent vs tab-lifetime; strings only.
- Why `JSON.stringify`/`JSON.parse` around `localStorage`? → it only stores strings; convert both ways.
- `innerHTML` vs `textContent`? → parses HTML vs plain text; XSS consideration.
- How do JS and CSS cooperate for UI state? → `classList.add/remove/toggle` a class whose look is defined in CSS.
- Why does adding one card rebuild the whole list, and how does React improve this? → vanilla has no diffing; React re-renders minimally from state.

---

## 8. Key Takeaways (The Compressed Mental Model)

1. **Three layers, three jobs:** HTML = structure, CSS = looks, JS = behavior. Separation of concerns is *the* organizing principle.
2. **The browser reads top-to-bottom** — this explains the CSS cascade, `<head>` placement, and load order.
3. **The box model + `border-box`** is the foundation of all layout. Internalize it.
4. **Flexbox for 1D, Grid for 2D** layouts; media queries make them responsive.
5. **`position`** controls how elements sit in (or escape) the document flow.
6. **The DOM** is a manipulable object tree; you select elements, listen for events, and modify them.
7. **Class-toggling** (`classList`) is how JS drives CSS-defined states — clean and declarative.
8. **Browser APIs ≠ core JS.** `localStorage`, `setTimeout`, `document` come from the environment, and support varies.
9. **`localStorage` stores strings only** → always `JSON.stringify` / `JSON.parse`.
10. **Vanilla JS re-rendering is painful by design** — that pain is the motivation for React later.
11. **Use MDN relentlessly.** Every property/method has a full reference + browser-compatibility table.
12. **When using AI later: understand every line you accept.** Code you don't understand is code you can't debug.

> These notes cover everything through the completed **CogniDeck** vanilla project. Next topics (Tailwind, React, backend, GitHub) build directly on these foundations — especially the re-rendering pain point and the class-toggling/state mental model, which React formalizes.
