# Week 1 — Building the Helpdesk UI

Over the coming weeks we will build a **support helpdesk** application, adding a new layer each week as we learn new
concepts. By the end it will even have its own **local AI agent** that drafts
replies to customers.

This week is all about **installation** and **UI/UX**. You will:

1. Install your tools and scaffold an empty Next.js app.
2. Drop in a starter helpdesk (provided on the moodle).
3. Work through 7 exercises that help practice core UI/UX skills by changing how it looks
   and feels.

There is **no database and no AI yet** — every screen uses fake sample data. That
is deliberate: this week we focus purely on the interface.


> Keep the `add-on-files` folder somewhere safe.

---

## Part A — Setup (do this once)

### 1. Install Node.js

Download and install the **LTS** version of Node.js from <https://nodejs.org>.
Then open a terminal (PowerShell on Windows) and check it worked:

```powershell
node -v
npm -v
```

You should see version numbers (Node 18 or newer). If you get "command not found",
restart your terminal or computer and try again.

### 2. Create an empty Next.js app

Run this **exact** command. We pin the version so everyone gets the same setup:

```powershell
npx create-next-app@15.3.9 helpdesk-lab --js --tailwind --app --eslint --no-src-dir --import-alias "@/*" --turbopack
```

What the flags mean (good to know, not to memorise):

| Flag | Meaning |
|------|---------|
| `--js` | JavaScript, not TypeScript |
| `--tailwind` | Install Tailwind CSS for styling |
| `--app` | Use the modern App Router |
| `--eslint` | Add a linter that flags mistakes |
| `--no-src-dir` | Keep `app/` at the top level |
| `--import-alias "@/*"` | Lets us write `@/components/...` instead of long paths |
| `--turbopack` | Faster dev server |

> Note: If you ever run plain `create-next-app@latest`, you may get an unstable
> "preview" version that breaks on Windows. That's why we pin to `15.3.9`.

Then start the dev server:

```powershell
cd helpdesk-lab
npm run dev
```

Open <http://localhost:3000> — you should see the default Next.js welcome page.
Press `Ctrl + C` in the terminal to stop the server.

### 3. Add the starter helpdesk files

You will a folder called **`add-on-files`** on moodle. Copy its contents into
your `helpdesk-lab` project, so the folders merge:

```
add-on-files/app/         →  helpdesk-lab/app/        (overwrite globals.css, layout.js, page.js)
add-on-files/components/  →  helpdesk-lab/components/  (new folder)
add-on-files/data/        →  helpdesk-lab/data/        (new folder)
```

When asked to replace `globals.css`, `layout.js` and `page.js` — say **yes**.

Run the app again:

```powershell
npm run dev
```

You should now see the **HD Helpdesk** — a dashboard, a sidebar, tickets, and so
on. If you do, setup is complete. 

### What's in the project

```
app/
  globals.css          ← design tokens (colours, spacing) — you'll edit this a lot
  layout.js            ← the app shell (sidebar + topbar)
  page.js              ← Dashboard
  tickets/page.js      ← list of tickets
  tickets/[id]/page.js ← one ticket (note the "AI suggested reply" panel!)
  new/page.js          ← new-ticket form
  settings/page.js     ← settings
components/             ← reusable building blocks (Button, StatusBadge, …)
data/                  ← fake tickets & stats (no database this week)
```

---

## Part B — UI/UX Exercises

Work through these in order. For each one: make the change, **save the file**, and
watch the browser update automatically.

> Keep the dev server running (`npm run dev`) the whole time.

### Exercise 1 — Design tokens & theming 

**Concept:** a *design system* keeps an app consistent by defining colours once and
reusing them everywhere. These are called **design tokens**.

1. Open `app/globals.css` and find the `@theme { … }` block.
2. Change `--color-brand` from `#4f46e5` to a colour of your choice (try
   `#0d9488` teal, or `#db2777` pink).
3. Also update `--color-brand-dark` (a slightly darker shade) and
   `--color-brand-soft` (a very light tint).

**Notice:** the sidebar, buttons, links, and active menu item **all** change from
one edit. That's the power of tokens — you never hunt through the whole app.

**Success:** the whole app is re-branded in your colour, and nothing looks
out of place.

### Exercise 2 — Typography & spacing

**Concept:** *visual hierarchy* — size, weight and whitespace guide the eye to
what matters most.

1. On the Dashboard (`app/page.js`), find the `<h1>` heading. Its classes include
   `text-2xl font-semibold`. Try `text-3xl font-bold` and see the difference.
2. In `app/globals.css`, change `--radius-card` (e.g. from `0.875rem` to `1.25rem`
   for rounder cards, or `0.25rem` for sharper ones).
3. Find a `space-y-6` on a page and try `space-y-8` or `space-y-4` to change the
   gaps between sections.

**Success:** headings clearly stand out from body text, and the spacing feels
comfortable — not cramped, not empty.

### Exercise 3 — Component variants

**Concept:** a single component can have several *variants*. Colour communicates
meaning (green = good, amber = waiting, red = danger).

1. Open `components/StatusBadge.jsx`. Study how each status maps to a colour pair.
   Change the `open` colours and watch every "Open" badge update across the app.
2. Open `components/Button.jsx`. Find the `VARIANTS` object. Tweak the `ghost`
   variant so it looks more distinct from `primary`.
3. **Challenge:** add a brand-new status, e.g. `urgent`, with its own colour, then
   give one ticket that status in `data/tickets.js`.

**Success:** statuses are instantly recognisable by colour, and your buttons
have a clear primary / secondary / danger hierarchy.

### Exercise 4 — Responsive layout 

**Concept:** *responsive design* — the layout must work on phones as well as
laptops. Tailwind uses prefixes like `sm:` `md:` `lg:` to apply styles at
different screen widths.

Right now the app is **not** responsive. Open it, then in your browser press
`F12` → click the phone/tablet icon to preview a small screen. Notice the sidebar
squashes the content.

1. **Stack the stat cards.** In `app/page.js` the cards use `grid-cols-4`. Change
   it to `grid-cols-1 sm:grid-cols-2 lg:grid-cols-4` so they stack on phones.
2. **Hide the sidebar on small screens.** In `components/Sidebar.jsx`, add
   `hidden md:flex` to the `<aside>` so it disappears on phones. (The ☰ menu
   button already lives in the topbar for mobile — wiring it to actually open the
   menu is an optional stretch goal once we cover interactivity.)

**Success:** at phone width the cards stack in one column and the layout no
longer overflows sideways.

### Exercise 5 — Interaction states

**Concept:** good UI gives *feedback*. Elements should react to hover, focus and
clicks, and screens should handle "empty" and "loading" gracefully.

1. On `components/TicketRow.jsx` the row already has `hover:bg-canvas`. Add a
   subtle `active:` style too.
2. In `components/Button.jsx`, add an `active:` state (e.g. `active:scale-95`) so
   buttons feel "pressable".
3. **Empty state:** in `app/tickets/page.js`, imagine the list was empty. Add a
   friendly message ("No tickets yet 🎉") that would show when there are none.

**Success:** buttons and rows visibly respond to the mouse, and you have a
designed empty state rather than a blank space.

### Exercise 6 — Accessibility ♿

**Concept:** *inclusive design* — the app should work for everyone, including
keyboard and screen-reader users.

1. **Keyboard focus:** click into the page, then press `Tab` repeatedly. Can you
   see where focus is? Improve the focus ring in `components/Button.jsx`
   (the `focus-visible:outline-*` classes).
2. **Labels:** check that the search box in `components/Topbar.jsx` and the menu
   button have helpful `aria-label`s (the menu button already does — add one to
   the search if missing).
3. **Contrast:** make sure your Exercise 1 brand colour has enough contrast
   against white text. Test it at <https://webaim.org/resources/contrastchecker/>
   (aim for a ratio of at least 4.5:1).

**Success:** you can navigate the whole app with the keyboard and always see
where you are, and your colours pass the contrast check.

### Exercise 7 — To Do on your own

Pick **one** improvement to the look or feel of the app and
implement it. Ideas:

- A nicer empty/loading state with a skeleton placeholder.
- A coloured left-border on ticket rows based on priority.
- A hover "lift" effect (shadow) on the dashboard cards.
- A small avatar colour that varies per customer.


## Troubleshooting

| Problem | Fix |
|---------|-----|
| `npm run dev` errors about a missing module | Make sure you ran the command **inside** `helpdesk-lab`, and that `npm install` finished during setup. |
| Page is blank / unstyled | Confirm `app/globals.css` was overwritten with the provided one (it must start with `@import "tailwindcss";`). |
| `@/components/...` not found | You must have created the app with `--import-alias "@/*"`. Re-run setup if needed. |
| Colours don't change | You edited the `@theme` block in `app/globals.css` and **saved**? The browser updates on save. |
| Port 3000 in use | Stop other servers, or run `npm run dev -- -p 3001`. |
