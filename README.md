# iHubSA — Website

Marketing and lead-generation site for **iHubSA**, positioning the company around
*"Turning Business Problems Into Digital Solutions."*

Static HTML/CSS/JS. No build step, no framework, no dependencies. Opens straight from
the filesystem and deploys to GitHub Pages as-is.

---

## 1. Files

```
ihubsa-website/
├── index.html              Entire site — markup, CSS and JS in one file
├── README.md               This file
├── robots.txt              Crawler rules
├── sitemap.xml             Sitemap for search engines
└── assets/
    ├── favicon.ico          Browser tab icon
    ├── apple-touch-icon.png Home-screen icon for iOS
    ├── og-image.png         Social share preview (1200×630)
    ├── logo.png             Brand asset — not used by the page, kept for your other work
    └── logo-white.png       Brand asset — reversed version, same
```

### Dark theme

The site is a **dark theme** throughout — deep navy-black surfaces, orange accents,
soft coloured glow for depth. Everything is driven by CSS variables at the top of the
`<style>` block, so the whole site restyles from a handful of values:

```css
--void:#040A11;    /* page background        */
--surface:#08131E; /* alternate sections     */
--card:#0C1B29;    /* cards                  */
--card-2:#112537;  /* card hover             */
--ink:#EDF4FA;     /* headings               */
--body:#A3BACD;    /* body copy              */
--muted:#7691A8;   /* secondary text         */
--faint:#6E8BA3;   /* small labels, captions */
--orange:#FF7012;  /* accent, CTAs           */
--blue-soft:#4FA3DE; /* charts, icons        */
```

Depth on dark comes from **rim-light and glow**, not drop shadows — `--rim` puts a 1px
highlight on the top edge of raised surfaces, and blurred colour orbs sit behind sections.
Drop shadows alone are invisible on a black page.

All text combinations were checked against **WCAG AA** (4.5:1 minimum for small text).
If you change `--body`, `--muted` or `--faint`, re-check them — on dark backgrounds it is
very easy to pick a grey that looks fine to you and is unreadable to someone else.

The one deliberate exception to the dark palette is the **Excel spreadsheet** in the
"Your Blueprint" section. It stays light because that is what a spreadsheet looks like —
and the contrast against the dark page makes the "before" state land harder.

---

**The logo is built into `index.html` as inline vector (SVG).** It is not loaded from a
file, so the branding renders correctly even if the `assets` folder is missing, and it
stays sharp on any screen at any size. One copy is defined once and reused in both the
header and footer, recoloured for the dark background with CSS variables:

```css
.logo   { --logo-ink:#FFFFFF; --logo-accent:#FF7012; }  /* header + footer */
```

Everything uses **relative paths**, so the site works at a repository subpath
(`username.github.io/repo-name/`) without changes.

---

## 2. Before you publish — replace the placeholders

Open `index.html` and search for these. They appear in the contact section and the footer.

| Placeholder | Replace with |
|---|---|
| `[ADDRESS]` | Your physical or postal address — 1 place, in the footer |

That is the only placeholder left. The email is set to **enquiries@ihub-sa.co.za**
throughout, and no phone number appears anywhere on the site.

To change the email later, edit the `ENQUIRY_EMAIL` constant at the top of the script
block, then the two visible `mailto:` links (contact panel and footer).

Don't want to publish a street address? Delete the `<li>` containing `[ADDRESS]` from the
footer's Contact column — the layout closes up on its own.

Also update:

- **Social links** — the three `<a href="#">` links in the footer (`f-social`).
- **Legal links** — Privacy Policy, Terms & Conditions and POPIA in the footer bottom.
- **Sitemap + robots** — replace `yourusername.github.io/ihubsa-website` with your real URL
  in `sitemap.xml` and `robots.txt`.

Nothing on the site invents a client name, a statistic or a testimonial. The case studies are
labelled "Example Project" and the demos are labelled as sample data. Keep it that way until
you have real customers who have agreed to be named.

---

## 3. Deploy to GitHub Pages

**Create the repository**

1. Go to <https://github.com/new>
2. Name it (e.g. `ihubsa-website`), set it **Public**, click **Create repository**

**Upload the files**

1. Click **Add file → Upload files**
2. Drag in `index.html`, `README.md`, `robots.txt`, `sitemap.xml` **and the whole `assets` folder**
3. Click **Commit changes**

> The `assets` folder must keep its name and sit directly next to `index.html`, or the
> favicon and the social share preview will not load. The logo itself is inline, so it
> renders either way.

**Turn on Pages**

1. **Settings → Pages**
2. Source: **Deploy from a branch**
3. Branch: **main**, folder: **/ (root)** → **Save**
4. Wait 1–2 minutes

Live at `https://<username>.github.io/<repo-name>/`

**Updating later**

Edit `index.html`, commit, and Pages redeploys in about a minute.

```bash
git add .
git commit -m "Update contact details"
git push
```

---

## 4. The "Trusted by" logo wall

Below the hero there is a `Trusted by` section with six placeholder slots.

**It currently shows placeholders, not real clients.** Before publishing, either fill it
or delete it — an empty logo wall is worse than no logo wall.

To add a real client:

```html
<span class="logo-slot"><img src="assets/clients/acme.svg" alt="Acme Construction"></span>
```

Logos are auto-greyscaled and dimmed, then brighten to full colour on hover, so any
colour logo sits correctly on the dark background. SVG or transparent PNG, roughly
120 × 40px. The placeholder styling disappears automatically once an `<img>` is present.

Only add organisations that have **agreed in writing** to be named. To remove the section
entirely, delete the whole `<section class="trusted">` block.

---

## 5. Adding your own photos

The About section has three photo slots that currently show labelled placeholders.

1. Save your images into `assets/` as `team-1.jpg`, `team-2.jpg`, `team-3.jpg`
2. In `index.html`, find the `PHOTO SLOTS` comment block
3. Uncomment the `<img>` line in each `<figure class="photo">`

```html
<figure class="photo">
  <img src="assets/team-1.jpg" alt="The iHubSA team at work">
  <span class="ph">…</span>
</figure>
```

The image covers the placeholder automatically. Recommended: **800 × 1000px portrait**,
JPG, around 200KB each. Compress at <https://squoosh.app> before uploading.

To remove a slot entirely, delete its whole `<figure>` block.

---

## 6. How enquiries reach you

**Live.** The form posts to a Supabase Edge Function which saves the enquiry and
emails **enquiries@ihub-sa.co.za**.

```
form  ->  ihub-website-lead (Edge Function)  ->  ihub_leads table
                                             ->  Resend  ->  enquiries@ihub-sa.co.za
```

| Piece | Where |
|---|---|
| Endpoint | `ziladpnlfajtiboavwvn` → Edge Functions → `ihub-website-lead` |
| Table | `public.ihub_leads` in the same project |
| Notification | Resend, from `website@ihub-sa.co.za`, reply-to the enquirer |

### Why there is no API key in the page

`LEAD_ENDPOINT` at the top of the script is the only thing the frontend knows.
There is no Supabase key and no Resend key in `index.html`, because:

- the function runs with `verify_jwt` off (a public form has no JWT to present),
  so no key is needed to call it;
- `ihub_leads` has RLS enabled with **no policies at all**, which denies
  everything — the endpoint cannot be used to read the lead list;
- the Resend key lives in Supabase secrets, server-side, and never reaches a browser.

A Resend key in frontend code would let anyone send email as your domain. That is
why this needs a server at all, and why the key must stay there.

### The lead is saved before the email is attempted

If Resend fails, the row is still written and the reason is recorded in
`ihub_leads.notify_error`. Nothing is lost silently. Check for problems with:

```sql
select id, created_at, company, email, notified_at, notify_error
from ihub_leads order by created_at desc limit 20;
```

`notified_at` set = the email went. `notify_error` set = it saved but did not send,
and the column says why.

### If the endpoint is unreachable

The form falls back to opening the visitor's mail client, exactly as before, and
the success panel changes wording to say so. A dropped connection costs you the
database record but not the enquiry.

### Spam handling

A hidden `website_url` honeypot field sits in the form. Humans never fill it;
bots fill everything. When it arrives populated the function returns `200` without
saving, so the bot believes it succeeded and does not retry. If spam ever gets
through, the next step is a per-IP rate limit or Cloudflare Turnstile.

### Changing the destination address

`TO_ADDRESS` at the top of the Edge Function, then redeploy. The address shown on
the page is separate — that is `ENQUIRY_EMAIL` in `index.html` plus the two visible
`mailto:` links.

---

## 7. Future functionality

The structure supports adding, in roughly this order:

| Feature | Where it goes |
|---|---|
| Lead capture | Supabase table + RLS (section 5) |
| Email notification on new lead | Edge Function triggered by a database webhook |
| File uploads | Supabase Storage bucket |
| Admin lead dashboard | New page + Supabase Auth, restricted by RLS |
| Customer login / portal | Supabase Auth + role-based RLS policies |
| Live demo applications | Replace the sample data in the `DEMOS` array with real queries |
| Subscriptions | Payment provider + Edge Functions |

---

## 8. Customising

**Brand colours** — edit the CSS variables at the top of the `<style>` block:

```css
--blue: #073F68;    /* primary */
--orange: #FF7012;  /* accent — CTAs, highlights, hover states */
```

Everything else derives from these, so changing them restyles the whole site.

**3D chrome objects** — the floating sphere, torus, star and cone are hand-built inline
SVG, not renders or a 3D library. Chrome is faked with *hard* gradient bands (adjacent
stops at nearly the same offset, e.g. `.735` / `.755`) where the surface sweeps past a
reflected horizon; smooth ramps read as frosted plastic instead. They cost about 6KB
total and need no dependency.

Position them with the `.o-*` classes. Each carries two data attributes:

```html
<div class="obj o-torus deep" data-speed=".09" data-spin="-.02">
```

- `data-speed` — parallax rate. Higher = moves faster as you scroll = reads as closer.
- `data-spin` — degrees of rotation per pixel scrolled. Omit for no rotation.
- `.deep` / `.mid` — depth layers: dimmer, blurred, further back.

**Do not put `filter: drop-shadow()` on these.** It rebuilds an alpha silhouette of the
whole SVG on every parallax frame and cost ~10ms/frame when measured — enough to drop a
mid-range phone below 60fps. On a near-black page it buys nothing visually anyway; depth
comes from scale, opacity and blur instead. With it removed the objects measure as free.

Only `.o-sphere` renders in front of content (`z-index:3`), so it sits in the grid gutter
and is hidden below 1080px. The rest sit behind text and are harmless anywhere.

**Cursor tilt** — the hero mockup, bento cards and demo cards lean toward the pointer.
JavaScript writes `--tx` / `--ty` (rotation) and `--mx` / `--my` (glow position) and CSS
consumes them, so nothing animates from script. Disabled under `prefers-reduced-motion`,
on touch devices (`hover: none`), and below 820px.

**Before → After morph** — the "From Spreadsheet to Smart Business System" section is a
single card that transforms in place rather than two columns side by side. The eight rows
are matched pairs, so each one swaps to its counterpart:

```html
<li class="mrow" style="--i:3">
  <span class="m-b">…Manual calculations</span>
  <span class="m-a">…Calculations handled by the system</span>
</li>
```

Both states occupy the same grid cell and cross-fade; `--i` staggers each row by 55ms so
the change sweeps down the list rather than snapping. It plays itself once when scrolled
into view, and the Before/After buttons let anyone replay or scrub it.

To edit, change the text in `.m-b` / `.m-a`. To add a row, copy an `<li class="mrow">` and
give it the next `--i` value. Keep the pairs genuinely matched — the whole effect depends
on each "after" being the direct answer to its "before".

Under `prefers-reduced-motion` the transitions are switched off and it never auto-plays,
but the toggle still works. With JavaScript disabled a `<noscript>` block unstacks both
lists so all the content is still readable.

**Bento grid** — the "What We Build" section is a 3-column bento layout. Cards take
`b-w2` (two columns) or `b-w3` (full width); the rest span one. Several contain a
`.b-visual` block holding a small inline-SVG interface preview. To add a card, copy an
existing `<article class="bento-card">` and adjust its span class.

**Icons** — all icons are inline SVG `<symbol>` definitions in the sprite at the top of
`<body>`. Add one with a new `id`, then use it anywhere:

```html
<svg class="icon"><use href="#i-your-icon"/></svg>
```

**Demo applications** — the six demos are defined in the `DEMOS` array in the script at
the bottom. Each has `id`, `icon`, `name`, `blurb`, `chart`, `kpis` and a `body()` function.
Copy one to add another. Helper functions `bar()`, `kpi()`, `tbl()`, `donut()`, `spark()`
and `miniBars()` build the charts.

**Automation tool** — the checkbox options and their mapped outputs are in the `OPTIONS`
array; the always-included items are in `BASE`.

---

## 9. What's already handled

- **Responsive** — tested at 390px, 820px, 1080px and 1440px. No horizontal scrolling.
- **Contrast** — every text/background pair verified against WCAG AA.
- **Scroll performance** — measured; the 3D objects add no measurable frame cost.
- **Accessibility** — semantic landmarks, labelled form fields, ARIA on the menu and modal,
  visible focus rings, keyboard-navigable, focus trapped in the modal, Escape closes it.
- **Reduced motion** — all animation is disabled for visitors who ask for it in their OS.
- **SEO** — title, meta description, Open Graph tags, JSON-LD organisation schema,
  a single `<h1>`, ordered headings, `robots.txt` and `sitemap.xml`.
- **Performance** — one HTML file, one webfont, five small images. No JS libraries.
  Charts are inline SVG rather than a charting library.
- **Fonts** — Inter from Google Fonts, with a full system-font fallback stack if it is
  blocked or slow, so text always renders.

---

## 10. Troubleshooting

**Logo missing** — shouldn't happen any more: the logo is inline vector inside
`index.html`. If the *favicon* or *social preview* is missing, the `assets` folder wasn't
uploaded, or went inside another folder. It must sit directly beside `index.html`.

**Site shows a 404** — check that the file is named exactly `index.html` (lowercase) and is
in the repository root, and that Pages is set to the `main` branch, `/ (root)` folder.

**Changes not appearing** — Pages caches. Wait a minute, then hard-refresh
(Ctrl+Shift+R, or Cmd+Shift+R on a Mac).

**Form does nothing** — expected until section 5 is done. Open the browser console (F12)
and you'll see the captured payload logged.

---

© 2026 iHubSA. All rights reserved.
