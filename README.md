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
    ├── logo.png            Primary logo (for light backgrounds)
    ├── logo-white.png      Reversed logo (for the dark footer)
    ├── favicon.ico         Browser tab icon
    ├── apple-touch-icon.png Home-screen icon for iOS
    └── og-image.png        Social share preview (1200×630)
```

Everything uses **relative paths**, so the site works at a repository subpath
(`username.github.io/repo-name/`) without changes.

---

## 2. Before you publish — replace the placeholders

Open `index.html` and search for these. They appear in the contact section and the footer.

| Placeholder | Replace with |
|---|---|
| `[COMPANY EMAIL]` | Your real email address |
| `[PHONE NUMBER]` | Your real phone number |
| `[ADDRESS]` | Your physical or postal address |

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

> The `assets` folder must keep its name and stay next to `index.html`, or the logo
> and icons will not load.

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

## 4. Adding your own photos

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

## 5. Connecting the contact form to Supabase

Right now the form validates, shows a success state and logs the payload to the browser
console. **It does not send anything anywhere.** Wire it up like this.

### Step 1 — Create the table

In the Supabase SQL editor:

```sql
create table public.leads (
  id              bigint generated always as identity primary key,
  name            text not null,
  company         text not null,
  email           text not null,
  phone           text,
  industry        text,
  currently_using text,
  problem         text not null,
  build_options   jsonb default '[]'::jsonb,
  has_attachment  boolean default false,
  created_at      timestamptz default now()
);

alter table public.leads enable row level security;

-- Anonymous visitors may submit, but may never read what others submitted.
create policy "anon can insert leads"
  on public.leads for insert
  to anon
  with check (true);
```

Row Level Security with an insert-only policy is what makes it safe to call from the
browser. Without it, anyone could read your entire lead list.

### Step 2 — Add a config file

Create `assets/config.js`:

```js
window.IHUBSA_CONFIG = {
  leadEndpoint: 'https://YOUR-PROJECT.supabase.co/rest/v1/leads',
  anonKey: 'YOUR-PUBLISHABLE-ANON-KEY'
};
```

Reference it in `index.html`, just before the closing `</body>` tag and **above** the
main `<script>` block:

```html
<script src="assets/config.js"></script>
```

### Step 3 — Enable the fetch call

In `index.html`, find the comment `SUPABASE INTEGRATION POINT` inside the form submit
handler and uncomment the `fetch(...)` block below it. Add the `Prefer` header:

```js
headers: {
  'Content-Type': 'application/json',
  'apikey': window.IHUBSA_CONFIG.anonKey,
  'Authorization': 'Bearer ' + window.IHUBSA_CONFIG.anonKey,
  'Prefer': 'return=minimal'
}
```

### Security — read this before you commit anything

- The **anon / publishable key is designed to be public.** It is safe in frontend code
  *only* when RLS is enabled on every table it can reach. Enable RLS first.
- **Never put the `service_role` key in this repository.** It bypasses RLS entirely and
  gives full read/write access to your database. If one is ever committed, rotate it
  immediately in the Supabase dashboard.
- Anything requiring a secret (sending notification emails, calling a paid API, writing to
  a table anon must not touch) belongs in a **Supabase Edge Function**, where the secret
  lives server-side. Post the form to the function's URL instead of straight to the table.

### File uploads

The file input is wired up in the UI but is not uploaded anywhere. To store spreadsheets,
create a Supabase Storage bucket with an insert-only policy and upload the file before
inserting the lead row, then save the returned path on the record.

---

## 6. Future functionality

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

## 7. Customising

**Brand colours** — edit the CSS variables at the top of the `<style>` block:

```css
--blue: #073F68;    /* primary */
--orange: #FF7012;  /* accent — CTAs, highlights, hover states */
```

Everything else derives from these, so changing them restyles the whole site.

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

## 8. What's already handled

- **Responsive** — tested at 390px, 820px, 1080px and 1440px. No horizontal scrolling.
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

## 9. Troubleshooting

**Logo missing after deploying** — the `assets` folder wasn't uploaded, or was uploaded
inside another folder. It must sit directly beside `index.html`.

**Site shows a 404** — check that the file is named exactly `index.html` (lowercase) and is
in the repository root, and that Pages is set to the `main` branch, `/ (root)` folder.

**Changes not appearing** — Pages caches. Wait a minute, then hard-refresh
(Ctrl+Shift+R, or Cmd+Shift+R on a Mac).

**Form does nothing** — expected until section 5 is done. Open the browser console (F12)
and you'll see the captured payload logged.

---

© 2026 iHubSA. All rights reserved.
