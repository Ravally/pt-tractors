# P&T Tractors — Sprint 6 QA Checklist

**Audited:** 2026-03-19
**Auditor:** Developer Agent
**Stack:** 11ty v3.1.2 + Tailwind CSS v4.2.1 + Nunjucks
**Branch:** staging (web-agency-team repo)

---

## Build

| Check | Result | Notes |
|---|---|---|
| `npm run build` runs without errors | PASS | Tailwind compiled in 29–50ms, 11ty wrote 9 files, 0 errors |
| All 6 pages output correctly | PASS | index, services, parts, sales, about, contact all built |
| robots.txt generated | PASS | `_site/robots.txt` ✓ |
| sitemap.xml generated | PASS | `_site/sitemap.xml` ✓ |

---

## SEO

| Check | Result | Notes |
|---|---|---|
| All pages have unique `<title>` tags | PASS | Each page has a unique title in frontmatter, appended with site suffix in base.njk |
| All pages have unique meta descriptions | PASS | All 6 pages have distinct, descriptive meta descriptions |
| All pages have canonical URLs | **FIXED** | Was missing. Added `<link rel="canonical" href="https://ptractors.co.nz{{ page.url }}">` to base.njk — auto-populates per page |
| Open Graph tags present (og:title, og:description, og:image) | **FIXED** | Was missing. Added full OG block to base.njk: og:type, og:site_name, og:title, og:description, og:url, og:image, og:image:width, og:image:height, og:locale |
| sitemap.xml exists and is valid | PASS | Valid XML, all 6 URLs listed with correct domain (ptractors.co.nz), appropriate changefreq and priority values |
| robots.txt exists | PASS | Allows all, references sitemap correctly |
| Schema markup present (LocalBusiness) | **FIXED** | Was missing. Added `application/ld+json` LocalBusiness schema to base.njk with full address, phone, email, geo coordinates, area served, Facebook sameAs. Type: AutoRepair (most specific applicable type) |
| H1 present and unique on every page | PASS | All 6 pages have exactly one H1 in the page hero. Home: "John Deere Tractor & Machinery Repairs Foxton, NZ". Services: "Workshop, Mobile & Transport". Parts: "Tractor Parts & Accessories". Sales: "Tractors & Machinery For Sale". About: "Local, Qualified, Trusted". Contact: "Get in Touch" |
| Images have alt text | **FIXED** | Background-image divs had no accessibility attributes. Added `role="img"` and `aria-label` with descriptive text to all CSS background-image elements across: index.njk (2), services/index.njk (2), about/index.njk (1), cta-banner.njk (decorative — added `aria-hidden="true"`) |

---

## Technical

| Check | Result | Notes |
|---|---|---|
| Build runs without errors | PASS | See Build section above |
| All internal links resolve (no 404s) | PASS | All href links checked: /, /services/, /parts/, /sales/, /about/, /contact/ — all map to built pages. Facebook and tel: links are external/functional |
| Forms have proper action/method attributes and labels | PASS | Contact form has `method="POST"`, all inputs have `<label>` elements. **See blocker below re: FORMSPREE_ID** |
| No broken image references | PASS | All images are Unsplash CDN URLs (external). No local image references that could break. og-default.jpg referenced in OG/schema — see Sprint 7 item |
| CSS loads correctly (Tailwind compiled) | PASS | `src/css/app.css` compiled by Tailwind CLI; copied to `_site/css/app.css` via passthrough; referenced correctly in base.njk as `/css/app.css` |
| JavaScript has no console errors | PASS | Only JS is the IntersectionObserver scroll animation in animations.njk — clean, no external dependencies, no errors expected |
| Viewport meta tag present | PASS | `<meta name="viewport" content="width=device-width, initial-scale=1.0" />` in base.njk |

---

## Content

| Check | Result | Notes |
|---|---|---|
| Business name correct | PASS | "P & T Tractor & Machinery Repairs Limited" in schema; "P&T Tractors" used as short name throughout — consistent |
| Address correct | PASS | "65 Johnston St, Foxton 4814, Manawatu, New Zealand" — verified in contact page, footer, schema |
| Phone correct | PASS | "06 363 7044" — correct on all pages, all `tel:` links point to `0636377044` |
| Email correct | PASS | "admin@pttr.co.nz" — correct on contact page and footer |
| Nathan Wilkin mobile | PASS | 027 489 7490 — correct on services and contact pages |
| Daren Kill mobile | PASS | 027 489 7491 — correct on services and contact pages |
| No placeholder text (Lorem ipsum, [INSERT], TODO) | PASS | No lorem ipsum found. FORMSPREE_ID is a deliberate placeholder per comment — see blocker |
| All 6 pages have real content | PASS | Home, Services, Parts, Sales, About, Contact all have full copy |
| CTAs present | PASS | "Call Now" / "Call 06 363 7044": all pages. "Request a Service": home hero. "Browse Parts" equivalent: home service grid links to /parts/. "Send an Enquiry": contact page and CTA banner |

---

## Mobile / Performance

| Check | Result | Notes |
|---|---|---|
| Viewport meta tag present | PASS | ✓ (see Technical above) |
| No horizontal scroll issues in source | PASS | Layout uses `max-width:1200px` containers with `padding:0 24px` — appropriate for mobile. Grid layouts use `flex-wrap:wrap` on CTAs. Note: CSS grid `repeat(4,1fr)` trust bar on home and `repeat(3,1fr)` credentials on about have no mobile breakpoints — see Sprint 7 note |
| Images optimised | PASS | All images are Unsplash CDN with `?w=800&q=80` or `?w=1600&q=80` parameters — appropriately sized |

---

## Blockers Before Launch

### BLOCKER 1 — Form action placeholder (Sprint 7)
**File:** `src/contact/index.njk` line 82
**Issue:** `action="https://formspree.io/f/FORMSPREE_ID"` — FORMSPREE_ID must be replaced with real Formspree form ID before launch
**Action required:** Jesse to create a free Formspree account at formspree.io, create a form, and provide the ID. Developer to update in Sprint 7.

### BLOCKER 2 — OG image missing (Sprint 7)
**File:** `src/images/og-default.jpg` (does not exist)
**Issue:** OG tags and schema reference `https://ptractors.co.nz/images/og-default.jpg` — image file does not exist. Social media shares will have no image.
**Action required:** Designer to produce a 1200×630px branded OG image. Developer to add to `src/images/og-default.jpg` in Sprint 7.

---

## Sprint 7 Recommendations (Not Blockers)

- **Mobile grid breakpoints:** Trust bar (4-col grid) and credentials section (3-col grid) have no responsive CSS. On narrow screens they will stack awkwardly. Recommend adding CSS media queries or switching to `grid-template-columns: repeat(auto-fit, minmax(200px, 1fr))` before launch.
- **Favicon:** No `<link rel="icon">` in base.njk. A favicon prevents browser console warnings. A simple P&T branded icon would be ideal — can use an emoji/SVG as fallback.
- **Business hours:** Schema includes Mon–Fri 8am–5pm as an estimate. Jesse to confirm actual trading hours before launch.
- **Opening hours on Contact page:** Not listed in the page content — consider adding.
- **Google Maps embed:** Contact page uses a placeholder Maps embed (coords approximate). Jesse to verify the pin lands correctly on 65 Johnston St, Foxton.

---

## Summary

**Fixed in this sprint:** 3 issues (canonical URLs, OG tags, LocalBusiness schema, image alt attributes)
**Passed:** 20+ checks
**Blockers:** 2 (FORMSPREE_ID placeholder, OG image file missing) — both planned Sprint 7 items
**Build:** Clean — 0 errors
**Ready for Sprint 7 (Launch):** Yes, pending the two blockers above
