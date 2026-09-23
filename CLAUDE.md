# HHA Margins - project context for Claude Code

Read this first. It describes what is in this repository, where everything lives and how the pieces connect. Wait for my instructions before changing anything.

## What this is
A marketing website plus a demo finance dashboard for **HHA Margins**, a bookkeeping and CFO reporting firm that works only with home care / home health agencies (HHA = home health agency). The site sells the service; the dashboard is a working demo of what clients get, filled with sample data for a fictional client, "Meridian Home Care Services", FY 2025.

## File format
Every page is a single `*.dc.html` file ("Design Component"). Each file contains:
- `<helmet>`: title, meta, favicon, Google Fonts links, and a small `<style>` block (resets, keyframes, mobile media queries).
- `<x-dc>` ... `</x-dc>`: the template. Plain HTML with **inline styles** plus `{{ path }}` holes, `<sc-for list="{{ items }}" as="x">` loops and `<sc-if value="{{ flag }}">` conditionals. Holes are dotted lookups only, never expressions.
- `<script type="text/x-dc" data-dc-script>`: a `class Component extends DCLogic { state; renderVals() { return {...} } }` class, React-class-like. `renderVals()` returns every value the template reads.
- `support.js` is the runtime that renders these files. It must sit next to the pages. Do not edit it.

If we move to a framework (Next.js, Astro, etc.), each `.dc.html` maps cleanly to one page component: template = JSX, `renderVals()` = derived data, `state` = component state.

## Folder layout
```
/
├─ HHA Margins Landing.dc.html      home page
├─ HHA Margins Services.dc.html     6 services in detail
├─ HHA Margins Pricing.dc.html      2 plans + comparison table + add-ons
├─ HHA Margins About.dc.html        how we work + team roles (no names/photos)
├─ HHA Margins Resources.dc.html    featured article + 9 article cards, filter + search
├─ HHA Margins Contact.dc.html      margin-review enquiry form (not wired)
├─ HHA Margins Tour.dc.html         7-tab product tour showing the live dashboard
├─ HHA Margins HIPAA.dc.html        HIPAA practices page
├─ HHA Margins Legal.dc.html        Privacy + Terms (tab switch; ?terms opens Terms)
├─ HHA Margins 404.dc.html          not-found page (noindex)
├─ HHAMargins Dashboard.dc.html     demo dashboard app (note: no space in filename)
├─ support.js                       DC runtime
├─ _ds/modernist-.../styles.css     design-system stylesheet loaded by the dashboard
├─ _ds/modernist-.../_ds_bundle.js  design-system bundle loaded by the dashboard
├─ README.md                        short owner notes
└─ CLAUDE.md                        this file
```
Pages link to each other by exact filename (with spaces, e.g. `href="HHA Margins Pricing.dc.html"`). Renaming a file means updating every link to it.

## Brand and styling
- Colors: ink/navy `#0c1b31`, navy 2 `#1d2b40`, brand blue `#1e3a5f`, accent red `#c0392b`, deep red text `#98301f`, green `#1e7a55`, light green `#7fd1a8`, salmon `#f0a595`, bright blue `#4a9eff`. Text greys `#465569`, `#6c7a8c`, `#9aa5b4`. Borders `#dde2ea`, `#e6eaf1`, `#eceff5`. Surfaces: page `#ffffff`, card tint `#f7f9fc`, dashboard ground `#f4f6fa`.
- Fonts: **Plus Jakarta Sans** (500-800) for everything; **Archivo** (500-900) for numbers via the `.num` class (tabular figures).
- Logo: navy rounded square `#1e3a5f` with a white pulse-line SVG path `M3 12h3.5l2-5 3.5 11 3-6h5.5`. The favicon is the same mark as an inline SVG data URI in each `<helmet>`.
- Copy rule: no long dashes (em/en). Use a plain hyphen "-".
- Rounded cards (12-20px radius), pill buttons/filters, dark navy feature panels, check (green) / cross (red) badges.

## Shared header and footer
The header and footer are **copied inline into every page** (not a shared component), so they paint instantly. Change one, change all ten website pages (a find-and-replace script is easiest).
- Header: logo + "HHA Margins", desktop nav `.hm-dnav` (Services, Pricing, About, Resources, Contact, "Product tour" button). The active page link is bold with a red underline.
- Mobile nav: `<details class="hm-mnav">` hamburger, shown under 900px by the helmet media query; opens a full-width panel with all pages + two CTAs. CSS-only, no JS.
- Footer: brand blurb, LinkedIn + Instagram buttons, columns Services / Company (includes HIPAA compliance) / Get in touch, bottom row Privacy, Terms, HIPAA.

## Home page sections (HHA Margins Landing.dc.html), top to bottom
1. Hero: headline, CTAs, and a **live dashboard preview**: an iframe of `HHAMargins Dashboard.dc.html?embed=1`, scaled from 1440px to fit (`fitFrame` + ResizeObserver). The iframe `src` is set after page load (stored in `data-src`) to keep the page fast.
2. Pain points (3 cards).
3. Margin calculator (`#calculator`): 4 custom range sliders (class `hm-range`, fill via `--p`), live revenue / labor / net / margin outputs and a stacked bar. Logic lives in `extraVals()`.
4. Services (6 cards).
5. Dashboard showcase (dark panel with 3 floating stat cards).
6. Live demo (`#demo`): demo credentials with copy buttons, "Open the demo" and "Take the tour".
7. How it works (3 steps).
8. Month end, before and after (two timelines).
9. Proof band (4 stats).
10. Is it a fit (`#fit`): "A good fit if" / "Probably not if" cards.
11. Testimonials: auto-scrolling marquee (`@keyframes marquee`, list duplicated for a seamless loop, pauses on hover). Data: `loopQuotes`. **Sample quotes, replace before launch.**
12. Systems strip (EVV / payroll / accounting tool names).
13. Pricing (`#pricing`): Essentials $499/month starting (weekly dashboard updates) and Custom "Let's talk" (daily updates).
14. Security and data (`#security`), Who does the work (`#about`), Resources teaser (`#resources`), FAQ (`#faq`, accordion), closing CTA (`#call`), footer.
- Motion: `[data-reveal]` elements fade up on scroll (IntersectionObserver); `[data-count]` numbers count up when visible; `prefers-reduced-motion` disables all animation.
- SEO: title, description, Open Graph, and a JSON-LD `AccountingService` block (name, description, Austin TX, areaServed US, phone, email, price range, offers, sameAs socials) in `<helmet>`.

## Other pages
- **Services**: 6 service blocks (anchors `#close #margin #ar #payroll #cash #compliance`), each with what is included + the deliverable. Data in `renderVals()`.
- **Pricing**: 2 plan cards, comparison table (icon cells or text pills such as Weekly/Daily), add-ons (free margin review, cleanup from $750, catch-up $199/month, tax handoff included). No monthly/annual toggle.
- **About**: values (4), team roles (lead accountant, bookkeeper, margin analyst, AR & compliance specialist). No names or photos by design.
- **Resources**: featured article with payer-margin bars; 9 article cards (category tint panel + big stat), category pills with counts, search; newsletter band. Articles do not exist yet (links are `#`).
- **Contact**: intro on top; below, contact detail cards (left) aligned with the form (right). Form fields, client-count pills, notes, success state. **Not connected to email/CRM.**
- **Tour**: 7 tabs (Overview, Financial statements, Money owed, Cash & banking, Cost & labor, Clients & care, KPIs & reference). One iframe loads the dashboard once; switching tabs sends `postMessage({ hhaScreen: id })` so the screen swaps instantly. Credentials bar with copy buttons and "Open login". Per-tab headline, screens, 4 benefits, 3 questions, previous/next.
- **HIPAA**: pledges, 6 safeguards (3x2 grid), what we work with / never need, team conduct, BAA request CTA, not-legal-advice note.
- **Legal**: Privacy and Terms template text (**needs lawyer review**).
- **404**: "This page did not reconcile." with links home and to key pages; `noindex`.

## Dashboard (HHAMargins Dashboard.dc.html)
- Flow: sign-in screen (any credentials work; demo shown is demo@hhamargins.com / homecare2026), then a ~0.75s splash, then the app.
- URL params: `?embed=1` skips the lock screen (used by iframes); `&screen=<id>` opens a given screen.
- `postMessage({ hhaScreen: "<id>" })` from a parent window switches screens (used by the Tour).
- Data and structure are constants at the top of the logic script: monthly arrays (revenue, AR, etc.), `PAGES` (groups and their member screens), `NAV` (sidebar sections), `T` (title/blurb per screen).
- Screen ids: `overview`; financial `pl bs equity cf`; money owed `ar ap`; cash `bank loc`; cost `payroll overhead fa`; care `clients caregivers missed`; reference `kpi coa`.
- Screens with multiple members show a sub-tab bar; tables support search, dropdown filters, quick-view chips and click-to-sort; the period selector drives date windows.
- Overview: KPI cards with sparklines, revenue + net margin chart (click a month), "Where every dollar goes" donut, bank accounts, line of credit, payer mix, growth chart, attention items.
- Mobile (under 900px): dark top bar with a menu button; the sidebar becomes a **right-side drawer** (`.hm-side` + `.hm-open`, scrim `.hm-scrim`); the drawer closes when you pick a screen. Grids inside `.hm-main` collapse to one column.
- It loads `_ds/.../styles.css` and `_ds_bundle.js`; keep those paths or remove the references deliberately.

## Placeholders to replace before launch
- Phone (512) 555-0137, email hello@hhamargins.com, domain hhamargins.com, LinkedIn/Instagram URLs (footer on every page + JSON-LD on the home page).
- Testimonials (home page `loopQuotes`).
- Legal and HIPAA wording (review by counsel).
- Resource articles (currently cards only).

## Not built yet / known gaps
- No backend: contact form, newsletter and dashboard login do nothing server-side.
- No real client data or auth in the dashboard; everything is sample data in the file.
- No sitemap.xml / robots.txt, no analytics, no cookie notice, no social share image (og:image).
- Header/footer are duplicated per page (intentional for speed; a framework migration should turn them into shared components).
