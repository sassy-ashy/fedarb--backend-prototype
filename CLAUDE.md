# My Fedarb — ADR Case Management Prototype

## What this is

A single-file interactive HTML/CSS/JS prototype of "My Fedarb," an ADR
(arbitration/mediation) case management web app. It was built by stitching
together 14 static HTML mockup screens (dashboard, cases table, a 7-step new
case wizard, and billing) into one navigable, click-through app with a shared
sidebar and working tab/view switching.

There is no backend, no build step, and no framework. It's one file:
`index.html`. Open it directly in a browser — nothing to install or run.
(It was renamed from `my-fedarb-prototype.html` to `index.html` so static
hosts serve it at the root URL.)

## Why it's built this way

- **One persistent sidebar, not one per screen.** The original 14 mockups
  each embedded their own copy of the sidebar. The prototype factors that out
  into a single shared `<aside class="sidebar">` so navigation actually works
  across views instead of being static per-screenshot chrome.
- **Everything is one file on purpose.** This was built as a fast, shareable
  prototype, not production code. If this project grows past "click through
  the flow," it should probably be split into components/pages — but don't
  do that reflexively; confirm with the user first, since the single-file
  format is also what makes it trivially easy to email or drop into a static
  host.
- **Toast notifications stand in for a backend.** Primary actions (Send
  invoice, Record payment, Process refund, Convert to case, etc.) show a
  toast confirmation instead of persisting anything. Nothing written to a
  form field survives a page reload or view switch.
- **Sample data is hardcoded in JS, not fetched.** See the `cases`,
  `invoices`, and `payments` arrays near the top of the `<script>` block.
  Six cases, six invoices, five payments — enough to demonstrate the table
  and detail views, not exhaustive.
- **Some sidebar destinations are honest placeholders.** Accounting,
  Accounts, Contacts, Reports, Zoom, and DocuSign were never part of the
  source mockups, so they render a generic "not part of this prototype yet"
  empty state (`showPlaceholder()`) rather than invented screens. Don't
  flesh these out without checking what the user actually wants there first.

## Structure inside the file

- `<style>` — all CSS, using custom properties (`--bg`, `--gold`, `--border`,
  etc.) for the dark navy/gold color system. Reuse these variables for any
  new UI rather than hardcoding new colors.
- Sidebar nav items call `showView(name)` (for Dashboard/Cases/Billing) or
  `showPlaceholder(name, title, icon, text)` (for the stub pages).
- `#view-cases` → row click or "New case" button calls `openCase()`, which
  opens `#view-newcase` and resets it to tab 1.
- `#view-newcase` is the 8-tab wizard: 1 Details, 2 Case history, 3 Hearing &
  conf. call, 4 Document submission, 5 Agreement & deposit, 6 Inquiry
  details, 7 Case information, 8 Documents. Tabs are toggled by
  `showWizardTab(n)`; markup lives in `<div class="wizard-tab" id="wtab-N">`.
  Tab 8 (Documents) has two sections: a client upload dropzone/list, and four
  "FedArb documents" cards (Invoices, Payments, Agreements, Misc) that link
  back into the relevant Billing tab.
- `#view-billing` has 5 sub-tabs (Create invoice, Create payment, Create
  refund, Invoices, Payments) toggled by `showBillingTab(name)`, matching
  `<div class="billing-tab" id="btab-NAME">`.
- Chips (invoice type, payment type) toggle `.active` via a single delegated
  click listener near the bottom of the script — don't add one-off chip
  handlers, extend that listener instead.
- `renderCases()`, `renderInvoices()`, `renderPayments()` build the three
  tables from the JS data arrays at load time.

## Known gaps / not yet done

- **Never visually verified in a real browser.** This was built and edited
  entirely by hand-authoring HTML/CSS/JS — the sandbox used to render/QA it
  was down for the whole session, so nobody has actually clicked through it
  pixel-by-pixel yet. Treat the first real click-through as a QA pass, not a
  formality — check tab switching, chip toggling, and the Documents tab
  layout specifically, since those were the most recently added.
- Tabler Icons are loaded from a CDN (`cdn.jsdelivr.net`) — that link was
  never confirmed reachable (network checks timed out mid-build). If icons
  aren't rendering, that's the first thing to check; the app is fully
  functional without them either way since nothing depends on icon glyphs
  for interactivity.
- Only Acme Corp v. Meridian Industries has full wizard data. Every case row
  in the Cases table opens the same demo record when clicked — that's a
  known simplification, not a bug.
- No mobile/responsive layout — this was designed at desktop width only.

## Working conventions

- Keep it a single HTML file unless the user explicitly asks to split it up.
- Match the existing dark navy/gold palette (CSS custom properties in
  `:root`) — don't introduce new one-off colors.
- New interactive elements should follow the existing pattern: inline
  `onclick` handlers calling named functions, toast feedback for anything
  that would hit a backend in a real app.
- This repo is pushed to `https://github.com/sassy-ashy/fedarb--backend-prototype`.
  Normal flow for changes: `git add .`, `git commit -m "..."`, `git push`.
