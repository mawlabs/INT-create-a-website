# createawebsite.ca — configurator brief

Status: v2 · 24 Nov 2026 · Owner and code owner: Angelique Roussos (Monkeys at Work / MAW Labs) · Source: the build
plan "createawebsite.ca — Claude Code build plan" (Claude Docs, 24 Nov 2026) and Angelique's answers of the same day

## 0. Read this first

**What this is.** createawebsite.ca sells a done-for-you website at a fixed price of $1,830. A visitor answers a few
questions, sees their site assembled from a section kit, swaps sections and colour palettes, and asks us to build it.
After the deposit is paid they give us their content, uploaded or drafted with AI, and the Monkeys at Work team builds
the site in WordPress with Elementor from a brief and delivers it in 10 business days. The customer can then edit their
own site. Any legal business, anywhere, can order one. It runs under the Monkeys at Work business. MVP launch target:
the week of 8 February 2027.

**What it is not.** Not an online site builder: nobody builds their site on createawebsite.ca; they edit it in
WordPress after delivery. Not a template gallery. Not a customer account system. Not an automatic site generator:
people build the final site from the brief.

**A fresh start.** This repository was cleared on 24 Nov 2026. The earlier quote-first lander (its kit, quote engine,
site check, workflows and decision log) is not carried forward as code. It stays in git history at commit `3dcc7f6` on
branch `claude/new-session-dpotje`, and §1 lists the few things worth taking from it.

**Not every decision is made.** §3 lists what Angelique has settled and what is still **proposed**. A phase may
proceed on a proposed default only where §3 says that decision does not block it. When Angelique confirms or changes
one, update §3 and `docs/DECISIONS.md` in the same pull request.

**Every session starts the same way:** read `CLAUDE.md`, this brief and `docs/DECISIONS.md`; do only the named phase
(§13); open one pull request; log every choice this brief left open in `DECISIONS.md`.

**When the brief is silent**, pick the simplest option that keeps Lighthouse ≥ 95 and the rules in §11, and log it.
**When it needs a fact only Angelique has**, leave `TODO(angelique): …` and add it to §14 and `DECISIONS.md`. Never
invent prices, promises, timelines, legal text, testimonials or example sites.

## 1. What carries over from the previous project

No code. These rules and findings do:

- **Bilingual by law.** Every route, string, meta tag, alt text, error and email exists in EN and FR-CA. Machine-drafted
  French carries `_review: true` and cannot ship to production until Angelique clears it. The build fails if an EN key
  has no FR twin.
- **Weight and accessibility.** Lighthouse mobile ≥ 95 in all four categories on public pages, no framework runtime in
  the browser, WCAG 2.1 AA, and zero third-party requests before analytics consent (Quebec Law 25).
- **createawebsite.ca's own brand** is the Monkeys at Work "Menu du jour" sub-brand. Restore its assets from commit
  `3dcc7f6` in phase 1 rather than recreating them: `packages/lander-kit/styles/tokens.css`,
  `packages/lander-kit/fonts/` (Satoshi Black and Bold, IBM Plex Sans and Mono, subset woff2) and
  `packages/lander-kit/assets/` (the MAW wordmark and icon). Customer previews never use this brand (§5).
- **SiteGround** serves the static site. It terminates TLS in front of Apache, so an HTTPS redirect in `.htaccess` must
  check both `HTTPS` and `X-Forwarded-Proto`, or every page loops. The staging docroot `www/caw.mawlabs.ca/public_html`
  already exists; staging builds carry `noindex` everywhere.
- **Lighthouse caps SEO on `noindex` pages**, so only the SEO category is exempted there, and only there.
- **French labels run longer.** Give fixed-height rows to anything whose height would otherwise depend on text
  metrics, or the page shifts when the web fonts load.

## 2. The offer and MVP scope

**The offer.** Every number below lives in one data file, `src/data/offer.ts`, read by the site and by the server
functions. Nothing retypes it.

| Term | Value | Status |
|---|---|---|
| Price | $1,830 CAD, before tax | Confirmed |
| Tax | Charged according to the purchaser's location, under Monkeys at Work's registration numbers. Prices are shown before tax. | Confirmed |
| Licences | The first year of Elementor Pro, and of WPML for sites with the second-language add-on, included in the price. Who pays for renewals after that: `TODO(angelique)`. | Under consideration |
| Deposit | 50% ($915 before tax), invoiced after "Build this" | Proposed |
| Pages | Up to 3. Home is always one; the customer picks the others from Services, About and Contact. | Proposed |
| Revisions | One round | Proposed |
| Delivery | 10 business days from the day content is complete | From the plan |
| Balance | Invoiced at delivery | Proposed |
| Disclosure | The site says plainly that it is template-based, and that this is why it costs what it does | From the plan |

**In the MVP**
- One section kit, for any legal business, worldwide (decision 1)
- A matching Elementor template library, so the team builds each site in WordPress and the customer can edit it
  (decision 2)
- Intake questions, assembled preview, section and palette swaps
- "Build this" submits the order; the deposit is invoiced by Monkeys at Work and marked received in the admin panel
- Post-payment content flow: the customer uploads their own content, or AI drafts it from follow-up questions
- A build brief for each order, and a ClickUp task for the team
- A team admin panel (§9)
- Finished example sites shown publicly; the full kit stays unbrowsable

**After the MVP:** online deposit payment through Stripe Checkout, as the last phase (§13, phase 7).

**Not planned**
- Industry-specific kits or placeholder copy: one generic kit for now
- Customer accounts or logins
- Automatic generation or deployment of the final site
- A "% of this site already built" counter
- A browsable template gallery or index of the kit
- Anything in the MAW app repo

## 3. Decisions

### 3.1 The plan's nine

| # | Decision | Answer or proposal | Status | Blocks |
|---|---|---|---|---|
| 1 | Who can order | Any business, anywhere, as long as it is legal. One generic kit; its placeholder copy reads for any business and takes the customer's business name and type. Before invoicing the deposit, the team checks each order and cancels any it will not build. What counts as illegal (proposed: in Canada or where the customer operates) goes in the terms of sale, `TODO(angelique)`. | Confirmed | Phase 1 |
| 2 | What the customer's finished site is built on | WordPress with Elementor, so customers can edit their own site. This means two matching libraries: the HTML kit that drives the preview, and an Elementor template for every section variant, with presets as Elementor global fonts and palettes as Elementor global colours (§5, phase 1b). Proposed: Elementor Pro on the Hello Elementor theme, hosted by Monkeys at Work. Under consideration: the first year of Elementor Pro included (§2). Which licence, and who owns it after handover, `TODO(angelique)`. | Confirmed (Elementor); hosting proposed | Phase 1b |
| 3 | The old quote-first home page | Settled by the fresh start: there is no old page. A customer who needs something outside the kit is linked to the Monkeys at Work quote chat on monkeysat.work. | Proposed | Phase 5 |
| 4 | Price, page limit, revisions, deposit, refund terms | Price $1,830 confirmed. The rest as in §2; refund terms `TODO(angelique)`. | Partly confirmed | Phase 3 |
| 5 | Is the customer's site bilingual at this price? | One language included; the second is a paid add-on that includes a paid multilingual plugin (proposed: WPML, which works with Elementor; its first year included, under consideration). Add-on price `TODO(angelique)`. The intake asks for the site language; the MVP offers English and French, and other languages are `TODO(angelique)`. | Confirmed | Phase 1 |
| 6 | Monthly hosting and care plan | Offered from launch: hosting, WordPress, Elementor and plugin updates, backups and security. Customers edit their own content, so the plan is not where edits go. Price and whether it is required `TODO(angelique)`. | Proposed | Phase 3 |
| 7 | Stripe account | Needed for phase 7 only, after the MVP | Not blocking | Phase 7 |
| 8 | Code owner | Angelique, as `MAWAdmin` (`.github/CODEOWNERS`). Claude sessions also open pull requests as `MAWAdmin`, and GitHub does not let an author approve their own pull request, so `main` requires no approval: Angelique reviews each pull request and merges it herself as admin. | Confirmed | — |
| 9 | Launch blockers | Production SiteGround access (SSH secrets and docroot), the GA4 ID, the FR review pass, the privacy officer's name, the terms of sale | Open | Phase 6 |

### 3.2 Other items

| # | Item | Answer or proposal | Status | Blocks |
|---|---|---|---|---|
| A | **No `main` branch.** The repository's default branch is `claude/new-session-dpotje`. | `main` created on 24 Nov 2026. Angelique makes it the default branch in GitHub (Settings → General); this session's GitHub access cannot change repository settings. No required approval on `main` (decision 8). | Open | Phase 1's pull request |
| B | **Supabase project** | A new project for createawebsite, owned by Monkeys at Work, with migrations and functions in this repo under `supabase/`. The MAW app's project belongs to the MAW app repo. | Proposed | Phase 3 |
| C | **Transactional email** | One provider (Postmark or Resend), sending from a createawebsite.ca address with SPF and DKIM. Also used for admin sign-in links. | Open | Phase 3 |
| D | **Tax and invoicing** | Tax follows the purchaser's location, under Monkeys at Work's registration numbers; "Build this" asks for the purchaser's country and province or state. In the MVP the deposit and balance are invoiced with Monkeys at Work's usual invoicing tool (which one: `TODO(angelique)`); phase 7 moves the deposit to Stripe, which can calculate tax by location. Prices are in CAD for every customer (proposed). | Confirmed, tool open | Phase 3 |
| E | **ClickUp** | Which list build orders go in, and who is assigned. A token stored as a function secret. | Open | Phase 4 |
| F | **Retention** | How long orders, content and files are kept after delivery, and how long an unpaid order is kept (proposed: cancelled and deleted after 14 days). Law 25. | Open | Phase 3 |
| G | **Admin sign-in** | Who may sign in to the admin panel (an allowlist of Monkeys at Work addresses). | Open | Phase 3 |
| H | **Staging WordPress for phase 1b** | A staging WordPress site with Elementor Pro, and a way for Claude Code to reach it (an application password stored as a secret, and the host allowed in this environment's network settings). Without it, the team builds the Elementor templates by hand from the HTML kit. | Open | Phase 1b |

## 4. Repository layout

```
CLAUDE.md
README.md
.github/
  CODEOWNERS                        ← decision 8
  workflows/ci.yml                  ← phase 1: install, type check, tests, build, copy check, Lighthouse
  workflows/deploy.yml              ← phase 1: static site to SiteGround on push to main
  workflows/staging.yml             ← phase 1: noindex build to caw.mawlabs.ca on demand
  workflows/supabase.yml            ← phase 3: migrations and functions on push to main
docs/
  configurator-brief.md             ← this file
  DECISIONS.md
  screens/                          ← 390 px and 1280 px screenshots per pull request
packages/
  site-kit/                         ← phase 1, §5: the HTML kit behind the preview
    elementor/                      ← phase 1b: the matching Elementor templates and global settings, exported
sites/
  createawebsite-ca/                ← the public site, the intake and preview, the order page, the admin panel
    src/data/offer.ts               ← §2
    src/i18n/{en,fr}.json           ← every string
    src/brand/                      ← "Menu du jour" tokens, fonts, wordmark (restored, §1)
supabase/                           ← phase 3
  migrations/
  functions/<name>/index.ts         ← §7
  functions/_shared/
```

**Stack.** pnpm workspaces, current Node LTS, TypeScript, Astro with static output, plain CSS with custom properties,
vanilla TS islands. Supabase (Postgres, Auth, Storage, Edge Functions) for everything server-side. The site is static
on SiteGround; it talks to Supabase from the browser through edge functions only. Customer sites are WordPress with
Elementor (decision 2), built by the team and hosted by Monkeys at Work.

The edge functions never trust a price from the browser. They read the offer data themselves. Phase 3 picks how the
functions and the site share `offer.ts` (a JSON file both import, or a build step that copies it), and a test asserts
the copies match.

## 5. Section kit (`packages/site-kit`)

The kit is a set of section components with a few layout variants each, plus style presets and colour palettes. A site
is a list of section choices, one preset and one palette.

**Sections for the MVP** (3 or 4 variants each): header and navigation · hero · services · about · proof (reviews,
badges, service area) · gallery or past work · call to action · contact (form, phone, hours, map) · footer.

**Rules for every section**
- A variant changes layout only, never which content fields the section needs. Any variant can be swapped for another
  without new content.
- Each section declares its content fields in a schema (for example `headline`, `body`, `image`, `phone`), with a
  type, a maximum length and whether the field is required. The same schema drives the placeholder copy in the
  preview, the AI content questions (§8) and the build brief (§10).
- Colours, fonts and spacing come from CSS custom properties only, so a preset and a palette are each one small set of
  tokens.
- Placeholder copy is generic, written to read for any business, with the customer's business name, type and contact
  details filled in. It is stored as data, in EN and FR (FR carries `_review: true`).
- Placeholder images are labelled blocks ("Your photo: your work"), not stock photos.
- The preview uses kit tokens and the finished site uses the matching Elementor global settings; neither ever uses the
  Monkeys at Work brand.
- Every variant meets WCAG 2.1 AA with every preset and every palette.

**Style presets.** Three at launch, each a pairing of fonts, corner and spacing style. The customer picks a preset;
they are never asked to choose fonts by name. Preset fonts are licensed for use on customer sites (OFL or similar) and
self-hosted.

**Colour palettes.** Fixed palettes, shown the way coolors.co shows them: five colours side by side. The customer
browses the palettes, steps to the next one with a single control, and picks one. Each palette assigns its five
colours to roles the kit uses (for example background, surface, text, primary, accent), and a build-time check proves
every pairing the kit draws meets AA contrast with every preset. A palette that fails is not shipped. Proposed: about
24 hand-picked palettes at launch, stored as data. No free colour picker in the MVP.

**Page recipes.** A recipe is the default section order for a page type: Home, Services, About, Contact. The intake
picks recipes; swaps edit them.

**The Elementor twin.** Customers get a WordPress site built with Elementor (decision 2), so the HTML kit has a twin:
every section variant has a matching Elementor template with the same id, every preset maps to Elementor global fonts,
and every palette to Elementor global colours. The twins are exported into `packages/site-kit/elementor/` so they are
versioned with the kit. From launch, a variant is not offered in the live preview until its twin exists and matches it in screenshots at
390 px and 1280 px, so a customer is never shown something the team cannot build. Staging shows every variant, twin or
not, so the visual system can be judged before phase 1b.

**The spec.** What the customer chooses is one object, validated in the browser and again on the server, and stored on
the order unchanged:

```ts
type SiteSpec = {
  kitVersion: string;            // the kit release the spec was made with
  businessType: string;          // the customer's own words, e.g. "plumber" or "bakery" (decision 1)
  language: 'en' | 'fr';         // the included language (decision 5)
  secondLanguage?: 'en' | 'fr';  // the paid add-on, built with the multilingual plugin
  businessName: string;
  contact: { phone?: string; email?: string; address?: string; hours?: string }; // shown in header, contact, footer
  hasLogo: boolean;
  preset: PresetId;
  palette: PaletteId;
  pages: Array<{ type: PageType; sections: Array<{ section: SectionId; variant: VariantId }> }>;
};
```

**What "hidden" means.** There is no gallery page and no index of variants. A visitor only ever sees the sections in
their own preview, plus the finished examples on the public site. Anything shown in a browser can be copied, so the kit
is kept unbrowsable, not secret. The staging-only kit page from phase 1 exists only in `noindex` staging builds, and a
test asserts it is absent from the production output.

## 6. Customer flow

The customer goes from first question to submitted order in one sitting, with no login and no meeting.

1. **Intake.** A few plain questions: type of business, business name, contact details (phone, email, address or
   service area, opening hours; each optional), which pages (up to the page limit), colour palette, style preset,
   whether they have a logo, and the site language. The business name and contact details appear in the preview's
   header, contact section and footer from the first screen. Answers stay in the browser
   (`sessionStorage`) until "Build this", so a reload or the back button keeps their place.
2. **Preview.** The site is assembled from the page recipes with their business name, their business type and the
   generic placeholder copy. It works at phone and desktop widths, and the customer can switch between the two.
3. **Swaps.** For each section the customer can step through its variants, and can change the palette or preset.
   Nothing else is editable.
4. **Build this.** A summary shows what is included, from `offer.ts`: price, deposit, page count, one revision round,
   10 business days from the day content is complete, and the plain statement that the site is template-based. The
   customer enters their own name, email and phone, and their location (country and province or state), which sets
   the tax shown, and agrees to the terms and privacy notice. The order is saved as
   `submitted`, and they get an email saying the deposit invoice is on its way.
5. **Deposit.** The team checks the order first, and cancels it with a short email if it is for a business Monkeys at
   Work will not build for (decision 1). Otherwise Monkeys at Work sends the deposit invoice. When it is paid, the team marks it received in the admin
   panel, and the customer gets an email with a private link to continue. (Phase 7 replaces this step with Stripe
   Checkout.)
6. **Content.** From that link: "Do you already have your content?" If yes, they upload text, logo and photos per
   section. If no, AI asks short questions per section and drafts the copy, which they approve or edit (§8).
7. **Handoff.** When content is marked complete, the build brief is produced (§10) and the 10-day clock starts.

The private link is sent only after the deposit is paid, so AI drafting and uploads are never open to an unpaid order.

**Preview build.** Section variants are pre-rendered to HTML at build time and assembled in the browser by a vanilla TS
island. Proposed: the preview renders in a same-origin frame, so the kit's styles never mix with createawebsite.ca's
and the phone view uses the kit's real breakpoints. Variant fragments are fetched on demand rather than all inlined, to
keep first load within a 300 KB budget. Phase 2 may choose otherwise and logs why.

**Order states**

| State | Reached when | Set by |
|---|---|---|
| `submitted` | The customer presses "Build this" | `submit-order` |
| `deposit_paid` | The deposit invoice is paid | The team, in the admin panel |
| `content_in_progress` | The first content save | `order` |
| `content_complete` | The customer submits their content | `submit-content` |
| `in_build`, `in_review`, `delivered` | The team moves the order on | The team, in the admin panel |
| `cancelled` | An unpaid order passes the retention limit (item F), or the team cancels it | A schedule, or the team |

The customer's link always shows the current state.

**Emails** (in the order's language; wording `TODO(angelique)`): order received; deposit received, with the private
link; content received; delivered; reminders while content is unfinished.

**Edge cases**
- Preview abandoned before "Build this": nothing is stored server-side.
- Deposit never paid: the order is cancelled and its personal data deleted after the retention limit (item F).
- Payment made but content never finished: reminder emails at set intervals; intervals, wording and refund terms
  `TODO(angelique)`.
- Customer wants something outside the kit: a link to the quote chat on monkeysat.work.

## 7. Backend (Supabase)

**Tables.** Row-level security is on for every table. The public (anonymous) role has no policies, so a visitor's
browser can read nothing directly. Admin users read and write through policies tied to the allowlist (§9).
- `orders`: id, token hash, state, language, spec, customer name, email and phone, amounts, ClickUp task id, timestamps
  for each state.
- `order_content`: order, section, field, value, source (`customer` or `ai`), approved.
- `order_files`: order, section, field, storage path, type, size.
- `order_events`: every change to an order, with who made it (customer, system or an admin user) and when.
- AI usage counters per order (§8).

**Functions**

| Function | Called by | Does |
|---|---|---|
| `submit-order` | "Build this" | Validates the spec, saves the order as `submitted`, sends the order-received email; rate-limited per address |
| `order` | The customer's order page | Reads the order's state, spec summary and content; saves content fields |
| `upload-url` | The customer's order page | Returns a signed upload URL for one field, after checking type and size |
| `draft-content` | The customer's order page | AI drafting (§8) |
| `submit-content` | The customer's order page | Marks content complete, builds the brief, creates the ClickUp task, emails the team |
| `admin-order-action` | The admin panel | Marks the deposit received (issues the token and sends the link), moves states, cancels, resends the link |
| `reminders` | A schedule | Sends content reminders; cancels unpaid orders past the limit |

**The order link.** The token is 32 random bytes, base64url-encoded, and only its SHA-256 hash is stored. It rides in
the URL fragment (`/order/#t=…` and the French equivalent), which browsers never send to a server, and the order page
sends it to the functions in a header. The functions accept browser requests only from createawebsite.ca and the
staging origin.

## 8. AI content drafting

- **Where:** the `draft-content` edge function calls the Claude API with the official SDK (`@anthropic-ai/sdk`). The
  key is a function secret and never reaches the browser.
- **Model:** `claude-opus-5-5`, set in one constant. Set `output_config.effort` explicitly (start at `medium`) and
  measure draft quality before changing it.
- **Input, and only this:** the section's field schema, the customer's answers to that section's questions, the
  business name, the business type and the language. The function accepts no free-form prompt, so it cannot be used as a
  general AI service.
- **Output:** structured outputs (`output_config.format` with a JSON schema generated from the section's field schema,
  maximum lengths included). Validate the result against the schema again before saving it.
- **Never invent facts.** No licence numbers, years in business, awards, certifications, prices, guarantees or reviews
  unless the customer gave them. Where an answer is missing, the draft leaves a visible bracketed placeholder for the
  customer to fill, for example "[years in business]".
- **Refusals and cut-offs:** check `stop_reason` before reading the content. Server-side fallbacks are on
  (`fallbacks: "default"` with the `server-side-fallback-2026-07-01` beta), so a refused draft is retried on another
  model automatically. If the result is still a refusal, or stops at `max_tokens`, the customer is offered the
  write-it-yourself path, not an error page.
- **Limits:** proposed 3 drafts per section and 40 per order, counted before the call.
- **Approval:** the customer approves or edits every drafted field. An unapproved draft never reaches the brief.
- **Privacy:** customer answers are sent to Anthropic. The privacy page and consent text say so (§11).
- **Tests:** the API is mocked in CI; no live calls in tests.

## 9. Admin panel

A small, private part of the site for the Monkeys at Work team. It replaces the plan's "no admin panel" rule.

- **Where:** `/admin/` on the same site, `noindex`, excluded from the sitemap and `robots.txt`-disallowed. Vanilla TS
  like the rest of the site. The Lighthouse budget does not apply to it; WCAG 2.1 AA does.
- **Sign-in:** Supabase Auth with an emailed sign-in link, limited to an allowlist of Monkeys at Work addresses (item
  G). No passwords to manage. Row-level security enforces the allowlist on the server; hiding pages in the browser is
  not protection.
- **Language:** EN and FR, like everything else.
- **What it does:**
  - lists orders by state, with each order's due date (10 business days after `content_complete`)
  - shows one order: customer, spec, a preview of their chosen site, content and its source, files to download, and the
    `order_events` history
  - marks the deposit received, which sends the customer their private link
  - moves an order through `in_build`, `in_review` and `delivered`, or cancels it
  - resends the customer's link, and regenerates the build brief
- **What it does not do:** edit prices (they live in `offer.ts`), edit the kit, or create customer accounts.
- Every admin action writes an `order_events` row naming the admin user.

## 10. Team handoff

On `content_complete`, `submit-content` produces one build brief:

- order id and a link to the order in the admin panel
- customer name, email, phone and language
- preset and palette, with the matching Elementor global colours and fonts
- every page, with its sections and variants in order, and the Elementor template for each
- every field's content and its source (customer, or AI approved by the customer)
- the files
- any add-ons (second language with the multilingual plugin, care plan)
- the date content was completed and the due date, 10 business days later (statutory holidays `TODO(angelique)`)

It creates a ClickUp task in the list from item E with the brief and the admin link, and emails the team address
(`TODO(angelique)`). Files stay in private storage and are downloaded from the admin panel, so customer files are not
copied into ClickUp or email. The brief alone must be enough for a builder to start.

The team builds the site in WordPress with Elementor from a base install (theme, Elementor Pro and the agreed plugins),
inserting the named templates and the customer's content. At delivery the customer gets an account that can edit their
content. Which WordPress role it has, and what short guide comes with it, are `TODO(angelique)`.

## 11. Rules for this build

- Every string, route, email and error exists in EN and FR-CA; machine-drafted French is flagged `_review: true`.
- No framework runtime in the browser; vanilla TS islands only. Lighthouse mobile 95 or more on every public page.
- WCAG 2.1 AA: keyboard-only works, visible focus, native controls, reduced motion respected.
- Never invent prices, promises, testimonials, timelines or legal text. Leave `TODO(angelique)` and list it in
  `DECISIONS.md`.
- Only `PUBLIC_*` identifiers in `.env`. The Supabase service key, the Claude API key, the email provider key, the
  ClickUp token, SiteGround SSH keys, the staging WordPress application password and (from phase 7) Stripe keys live only in Supabase function secrets or GitHub
  Actions secrets. Never in the repo, in browser code or in logs.
- Every Supabase table has row-level security on. The customer's order link works only through an edge function and
  the token in §7.
- Uploads: images as JPEG, PNG or WebP; logos also as SVG or PDF; 10 MB per file and 40 files per order (proposed).
  Files go in a private bucket and are never served publicly. SVGs are never rendered on our pages.
- The AI drafting function accepts only the section field schema and the customer's answers, and is limited (§8).
- Zero third-party requests before analytics consent.
- Before launch, the privacy page and consent text cover orders, uploads, AI drafting (including that answers go to
  Anthropic) and retention (item F), under Law 25. The legal wording comes from Angelique.
- Prices and terms are read from `offer.ts` only.
- **Phase 7 (Stripe):** Checkout is a redirect to a URL our function returns; no Stripe.js on our pages. Test mode until
  Angelique switches it. Verify the webhook signature, and make the handler safe to receive the same event twice.

**Do not build:** customer accounts on createawebsite.ca; industry-specific kits; a gallery or index of the kit;
automated building or deployment of customer sites; anything in the MAW app repo.

**Review.** One pull request per phase, with 390 px and 1280 px screenshots in `docs/screens/`. Each gets an AI review
comment, and Angelique reviews and merges it as code owner (decision 8). Phases 3 and 4 handle personal data and admin access, so their review
covers the security rules above line by line.

## 12. Public site (phase 5)

- The home page sells the offer: what you get, the price, the 10-day delivery, the template disclosure, finished
  examples, an FAQ and a terms link. EN and FR.
- A link for work outside the kit goes to the quote chat on monkeysat.work.
- Examples are real sites from the phase 6 test builds. None are invented, and the section is not shown until there are
  some.
- Analytics (GA4) loads only after consent.

## 13. Phases

**Visual system first.** The first milestone is a system that works visually: phases 1a, 1 and 2, the design round,
the kit, intake, preview and swaps, running on staging with nothing stored and no backend. Everything after it is a module (orders and
admin, content and AI, the Elementor library, the public site, payments), built one at a time once Angelique has seen
the visual system. The modules' dates below are reset at that point.

Each phase is a separate Claude Code session and pull request. The dates are proposed targets, not commitments.

| Phase | Target | Claude Code builds | Done when |
|---|---|---|---|
| 0. Spec | 30 Nov 2026 | The fresh start; this brief; `DECISIONS.md`; `CLAUDE.md` and `README.md` | The open decisions are answered in the brief; nothing from the old project contradicts it |
| 1a. Design round | 3 Dec 2026 | Run by Angelique in a design tool (Claude Design), outside the repo: first the configurator tool itself, then style directions for customer sites (details below) | Angelique has approved the tool's design and picked a direction and its palettes, and `DECISIONS.md` records both with links to the design files |
| 1. Foundation and section kit | 14 Dec 2026 | The workspace, CI (type check, tests, copy check, Lighthouse), deploy and staging workflows, the restored brand; `packages/site-kit` with 9 sections and their variants, field schemas, 3 presets, the palettes and their contrast check, generic placeholder content and recipes; a staging-only kit page | Every variant renders at 390 px and 1280 px with every preset and palette; swapping variants needs no content change; CI is green; screenshots in `docs/screens/` |
| 1b. Elementor library (module) | After phase 2 | An Elementor template for every section variant, Elementor global fonts and colours for every preset and palette, exported into `packages/site-kit/elementor/`; on the staging WordPress from item H, or by the team if Claude Code cannot reach it | Every variant's twin matches the HTML kit in screenshots at 390 px and 1280 px; any variant without a matching twin is hidden from the preview |
| 2. Intake, preview, swaps | 4 Jan 2027 | Intake form, preview assembly island, variant, palette and preset swaps, the summary screen | A full run from first question to summary works on a phone with keyboard only; an end-to-end test covers it; the Lighthouse budget passes |
| 3. Orders and admin panel | 15 Jan 2027 | Supabase schema with row-level security, `submit-order`, emails, the admin panel with sign-in, the deposit-received action and the private order link | A submitted order appears in the admin panel; marking the deposit sends one working link; nobody outside the allowlist can read an order |
| 4. Content and handoff | 22 Jan 2027 | Upload path, AI question-and-draft path, order status page, the build brief, the ClickUp task, reminders | Both content paths end in a complete brief; the brief alone is enough for a builder to start; uploads are private to the order |
| 5. Public site | 29 Jan 2027 | Home page around the offer, finished examples, template disclosure, FAQ, terms link, EN and FR | Zero un-reviewed French and zero `TODO(angelique)` in shipped copy; every CI budget passes |
| 6. Test builds and MVP launch | 5 Feb 2027 | Fixes from three real test orders; the launch checklist | Three sites built in Elementor from briefs and handed over, with hours recorded and each customer able to edit their site; Angelique's review approved; the launch blockers (decision 9) closed |
| 7. Online deposit (after the MVP) | To be set | Stripe Checkout for the deposit with Monkeys at Work's tax numbers, the webhook, and the switch from manual deposit marking | A Stripe test payment moves one order to `deposit_paid` with the exact selections; a failed or repeated webhook changes nothing |

**Order matters.** Do not start phase 1 until Angelique has picked a direction in phase 1a. Phase 1 is now the longest: it builds the foundation as well as the kit, and the kit sets the
quality of everything a customer sees. Do not start phase 2 until Angelique has approved the kit on staging. Phase 1b
follows the visual system and must finish before phase 6's test builds. Phases 3 and 4 can overlap. Phase 5 depends on real example sites, so the three test builds can start as soon as phase 4 works.

### Phase 1a: the design round

Settle how customer sites and the configurator look before phase 1 builds dozens of variants and phase 1b rebuilds them
in Elementor. Angelique runs this round in a design tool (Claude Design since 24 Nov 2026; a Figma attempt produced a
website template instead of the tool), outside the repo. The configurator tool (item 6) comes first, on its own; the
customer-site directions (items 1–5) are a separate pass, so neither crowds out the other. Palette roles are Figma colour variables (one mode
per palette) and each direction's type is a set of text styles, mirroring the kit's tokens and Elementor's global
colours and fonts. Phase 1 builds the kit from the chosen frames.

1. **Three style directions for customer sites (A, B, C).** Each is one full home page: header and navigation, hero,
   services, about, proof, gallery, call to action, contact and footer, one layout each. Each direction pairs fonts,
   corner and spacing style the way a preset will (§5). Fonts are OFL-licensed (Google Fonts), so they can be self-hosted later.
2. **Two palettes per direction**, five colours each, assigned to roles (background, surface, text, primary, accent).
   Every text-on-colour pairing the page uses meets WCAG AA, with the contrast ratios listed beside each palette.
3. **Generic placeholder copy** that reads for any business (§5), with a sample business name and contact details.
   Show each direction for two different kinds of business (for example a plumber and a bakery) to prove the copy and
   layout are generic. Images are labelled blocks, never stock photos. Reviews in the proof section are visibly
   placeholders ("[Customer review]"), never invented quotes. No Monkeys at Work brand on these pages.
4. **One direction also in French**, to check that longer French text fits the layouts.
5. **Buildable in Elementor.** Layouts use auto layout, which maps to Elementor's flexbox containers: no scroll or load
   animations, no overlapping or free-positioned layers that Elementor's controls cannot reproduce. Note anything
   doubtful in the file.
6. **Configurator flow wireframes** in the Monkeys at Work "Menu du jour" brand, using its tokens and fonts restored
   from commit `3dcc7f6` (§1; the values are in the phase 1a prompt): intake (business type, name, contact details, pages, palette browsed coolors-style with
   a next control, style preset, logo, language), the preview with its phone and desktop toggle, stepping through a
   section's layouts, switching palette or style, and the "Build this" summary (price and deposit from §2, before tax,
   with the template disclosure). A clickable prototype between screens is enough.
7. **Every frame at 390 px and 1280 px wide.**

Done when Angelique has picked one direction and its palettes (or asked for a second round), and `DECISIONS.md`
records the choice and any changes she asked for.

**Outside the build:** how the first customers find the site needs its own plan, running alongside phases 1 to 5, so
that launch day has traffic.

## 14. Inputs needed from Angelique (blocking marked ●)

| # | Input | Used in | Needed by |
|---|---|---|---|
| ● | Make `main` the default branch in GitHub, Settings → General (item A) | CI, deploy | Phase 1's pull request |
| | Approve the kit on staging | §13 | Phase 2 |
| ● | A staging WordPress with Elementor Pro and access for Claude Code (item H); the Elementor Pro licence, who owns it after handover, and whether the first year of Elementor Pro and WPML is included (decision 2, §2) | §5 | Phase 1b |
| ● | Deposit, page limit, revisions, refund terms; second-language add-on price and plugin; care plan price and terms (decisions 4–6) | `offer.ts` | Phase 3 |
| | Customer site languages beyond English and French (decision 5) | §3 | Phase 3 |
| ● | Supabase project (item B); email provider account and DNS access (item C); the admin allowlist (item G); the retention periods (item F) | §7, §9 | Phase 3 |
| | Which invoicing tool sends the deposit and balance invoices (item D) | §6 | Phase 3 |
| ● | ClickUp list and assignee (item E); the team email address for briefs | §10 | Phase 4 |
| | Email wording, reminder intervals, statutory holidays; the customer's WordPress role and handover guide | §6, §10 | Phase 4 |
| ● | Privacy and consent wording; terms of sale, including what counts as illegal (decision 1) | §11 | Phase 5 |
| ● | Production SiteGround access, GA4 ID, FR review pass, privacy officer's name (decision 9) | §12 | Phase 6 |
| | Stripe account (decision 7) | §11 | Phase 7 |

## 15. Definition of done (MVP launch)

- [ ] A visitor goes from first question to a submitted order on a phone, keyboard only, in EN and in FR.
- [ ] A submitted order appears in the admin panel; marking the deposit sends one working private link.
- [ ] Both content paths end in a brief the team can build from, with a ClickUp task.
- [ ] The order link shows the right state at every step, and no other order's data can be reached with it.
- [ ] Nobody outside the admin allowlist can sign in or read an order.
- [ ] No secret appears in the repo, the browser bundle or the logs; every table has row-level security on.
- [ ] The AI drafting endpoint refuses anything but its schema and answers, and its limits are verified.
- [ ] Lighthouse mobile ≥ 95 on every public page, in CI; no third-party request before consent.
- [ ] Zero un-reviewed French and zero `TODO(angelique)` in shipped copy; the privacy page and terms are Angelique's
      wording.
- [ ] Three real sites built in Elementor from briefs and handed over, with hours recorded, each customer able to edit
      their own site.
- [ ] `docs/DECISIONS.md` current; screenshots at 390 px and 1280 px for every new page in `docs/screens/`.
