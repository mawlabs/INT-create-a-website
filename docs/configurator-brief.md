# createawebsite.ca — configurator brief

Status: v1 draft · 24 Nov 2026 · Owner: Angelique Roussos (Monkeys at Work / MAW Labs) · Source: the build plan
"createawebsite.ca — Claude Code build plan" (Claude Docs, 24 Nov 2026) · Written in phase 0

## 0. Read this first

**What this is.** createawebsite.ca becomes a place to buy a done-for-you website at a fixed price. A visitor
answers a few questions, sees their site assembled from a section kit, swaps sections and colours, and pays a
deposit. Then they give us their content, uploaded or drafted with AI, and the Monkeys at Work team builds the site
from a brief and delivers it in 10 business days. Launch target: the week of 8 February 2027.

**What it is not.** Not a site builder: the customer never edits a live site. Not a template gallery. Not a
customer account system. Not an automatic site generator: people build the final site from the brief.

**How this relates to the other docs.**
- `docs/createawebsite-brief.md` (7 Sept 2026) and the `docs/DECISIONS.md` entries since then still govern
  everything this brief does not change: bilingual rules, accessibility, Lighthouse budgets, consent, deploy, and the
  "Menu du jour" design system for createawebsite.ca's own pages.
- Where this brief conflicts with them, this brief wins. §1 lists every conflict, so nothing is overridden silently.
- `CLAUDE.md` non-negotiables stay in force. §1 lists the three that this build extends.

**Not every decision is made.** Nine decisions belong to Angelique, plus seven items found while writing this brief
(§3). Each has a working default, marked **proposed**. A phase may proceed on a proposed default only where §3 says
that decision does not block it. When Angelique confirms or changes one, update §3 and `DECISIONS.md` in the same
pull request.

**Every session starts the same way:** read `CLAUDE.md`, this brief and `docs/DECISIONS.md`; do only the named phase
(§12); open one pull request; log every choice this brief left open in `DECISIONS.md`.

**When the brief is silent**, pick the simplest option that keeps Lighthouse ≥ 95 and the rules in §10, and log it.
**When it needs a fact only Angelique has**, leave `TODO(angelique): …` and add it to §13 and `DECISIONS.md`. Never
invent prices, promises, timelines, legal text, testimonials or example sites.

## 1. What changes from the existing site

| Area | Today (7–17 Sept 2026) | After this brief |
|---|---|---|
| Offer | Custom quotes from the quote panel; a basic website is $2,500–$3,500 in `pricing.ts` | A fixed-price, template-based site (about $2,000, decision 4) is the main offer. Custom quotes stay for work outside the kit (decision 3). |
| Home page | Quote-first hero, site check, guide | Rewritten around the offer in phase 5. The quote panel moves to its own route (decision 3). |
| `pricing.ts` and the quote engine | Single source for the quote engine, mirroring the MAW chat framework | **Unchanged.** CLAUDE.md #5 keeps the engine in step with the MAW chat, so it is not edited to make $2,000 fit. The offer's numbers live in their own file (§4). The public copy explains the difference: template-based versus custom design. |
| Site check (`audit.php`) | On the home page; its public launch is blocked on privacy text, the corpus and the version source | Not part of the configurator launch (item D). The code stays. |
| Architecture | Static files on SiteGround plus one PHP endpoint | Adds Supabase (Postgres, storage, edge functions) and Stripe Checkout. createawebsite.ca's own pages stay static on SiteGround. |
| Brand (CLAUDE.md #1) | "Menu du jour" tokens on every page | "Menu du jour" on createawebsite.ca's own pages only. Customer previews use `packages/site-kit` tokens and never show the MAW brand. |
| Client script (CLAUDE.md #3) | Four islands: quote panel, question form, consent banner, language bar | Adds the intake, preview, content and order-status islands. Still vanilla TS, no framework runtime, and **no Stripe.js**: Checkout is a redirect to a URL our edge function returns. |
| Secrets (CLAUDE.md #6) | Only `PUBLIC_*` in `.env`; SSH keys in Actions | Adds server secrets (Stripe, Supabase service role, Claude API, email, ClickUp) that live only in Supabase function secrets or Actions secrets (§10). |

## 2. The offer and v1 scope

**The offer.** Every number below lives in `sites/createawebsite-ca/src/data/offer.ts` and nowhere else.

| Term | Proposed (decision 4) |
|---|---|
| Price | $2,000 CAD fixed, before tax |
| Deposit | 50%, paid at "Build this" |
| Pages | Up to 3. Home is always one; the customer picks the others from Services, About and Contact. |
| Revisions | One round |
| Delivery | 10 business days from the day content is complete |
| Disclosure | The site says plainly that it is template-based, and that this is why it costs what it does |

**In v1**
- One industry and one section kit (decision 1)
- Intake questions, assembled preview, section and colour swaps
- Deposit checkout at "Build this"
- Post-payment content flow: the customer uploads their own content, or AI drafts it from follow-up questions
- A build brief sent to the team for each paid order
- Finished example sites shown publicly; the full kit stays unbrowsable

**Not in v1**
- More than one industry, until the first has paid orders
- Customer accounts, logins, a dashboard or an admin panel
- Automatic generation or deployment of the final site
- A "% of this site already built" counter
- A browsable template gallery or index of the kit
- Anything in the MAW app repo; changes to `process-lead` are a separate task

## 3. Decisions

### 3.1 Angelique's nine

The first three block phase 0 in the build plan. They are recorded here as proposed so phase 1 can be prepared; phase
1 does not open its pull request until #1, #2, #5 and #8 are confirmed.

| # | Decision | Proposed | Why | Blocks | Status |
|---|---|---|---|---|---|
| 1 | First industry | Home trades: plumbers, contractors, handymen | One kit covers all three with different placeholder copy, and they buy before spring | Phase 1 | Proposed |
| 2 | What the customer's finished site is built on | The same Astro kit, hosted by Monkeys at Work | The preview becomes the product, and the build becomes content plus checks. The cost: customers cannot edit the site themselves, so edits go through the care plan (decision 6). A WordPress or Elementor build means keeping two matching libraries. | Phase 1 | Proposed |
| 3 | What happens to the current quote-first home page | The offer becomes the home page; the quote flow moves to its own route for custom work | One clear offer per page; the $2,500–$3,500 basic tier still has to be explained next to a $2,000 product either way | Phase 0 (this brief), phase 5 | Proposed |
| 4 | Price, page limit, revisions, deposit, refund terms | §2 table. Refund terms: `TODO(angelique)`. | A 50% deposit covers the work if a customer stops answering after content | Phase 3 | Proposed |
| 5 | Is the customer's site bilingual at this price? | One language included; the second is a paid add-on (price `TODO(angelique)`) | Many Quebec customers will need French, and two languages doubles the content step. Under this default the intake gains a site-language question. | Phase 1 | Proposed |
| 6 | Monthly hosting and care plan: price, optional or required | Offered at checkout from launch; price `TODO(angelique)` | Recurring revenue, and it is where edit requests go under decision 2 | Phase 3 | Proposed |
| 7 | Stripe account for Monkeys at Work | Confirm one exists, or open it now | Verification can take days | Phase 3 | Open |
| 8 | Code owner and reviewer for this repo | Assign before phase 1 opens its pull request; add a `CODEOWNERS` file and add this repo to the MAW Labs PR hand-off map | The Labs merge rule needs a code owner's approval. Today the repo has no `CODEOWNERS` file and is not in the hand-off map. | Phase 1 | Open |
| 9 | September's blockers in `DECISIONS.md` | Close them during phases 1 and 2 | SiteGround deploy secrets, HubSpot form IDs, GA4, the French review, the terms link and the privacy officer's name block any launch | Phase 6 | Open |

On decision 1: the build plan also cites `sites/wphandyman-ca` as already scaffolded. That scaffold is a one-line
`noindex` page, and the name reads as a WordPress-fixes lander rather than a trades one, so it is not evidence for
this choice either way.

On decision 2: if the Astro kit is confirmed, decide before phase 6 where customer site code lives. Proposed: a
separate repository that consumes `packages/site-kit`, so customer sites never ride on this repo's CI and deploy.

### 3.2 Found while writing this brief

| # | Item | Proposed | Blocks |
|---|---|---|---|
| A | **There is no `main` branch.** The default branch is `claude/new-session-dpotje`. `deploy.yml` runs on push to `main`, and the strict copy gate in `ci.yml` checks `main`, so neither has ever run. | Create `main` from the current head, make it the default branch, and require a code owner's approval to merge. | Phase 1's pull request |
| B | **Which Supabase project.** | A new Supabase project for createawebsite orders, owned by Monkeys at Work, with its migrations and functions in this repo under `supabase/`. The existing project's migrations belong to the MAW app repo, which this repo must not touch, and two repos migrating one database collide. It also keeps payment and upload data apart from the MAW app's. | Phase 3 |
| C | **Transactional email.** No mail transport exists in this repo. | One provider (Postmark or Resend) called from edge functions, sending from a createawebsite.ca address with SPF and DKIM set in DNS. Needs an account and a DNS change. | Phase 3 |
| D | **The site check at launch.** | Leave it off the launch home page. Its own blockers (privacy text, corpus, a real version source) stay open and do not gate the configurator. | Phase 5 |
| E | **Sales tax and the balance.** | GST and QST on the price and the deposit, with MAW's tax numbers on the receipt (Stripe Tax or fixed tax rates). The remaining balance is collected by a Stripe invoice sent at delivery. Both `TODO(angelique)`. | Phase 3 |
| F | **ClickUp for build orders.** | Which list, who is assigned, and an API token stored as a function secret. `TODO(angelique)`. | Phase 4 |
| G | **Order data retention.** | How long orders, content and files are kept after delivery (Law 25). `TODO(angelique)`. | Phase 4 |

## 4. Repo additions

```
packages/
  site-kit/                         ← phase 1
    sections/<section>/<Variant>.astro
    sections/<section>/schema.ts    ← the section's content fields (§5)
    presets/                        ← one token set per style preset
    lib/spec.ts                     ← SiteSpec type, validateSpec()
    lib/contrast.ts                 ← colour checks for the main-colour swatches
    industries/<industry>/          ← recipes.ts, placeholders.en.json, placeholders.fr.json
sites/createawebsite-ca/
  src/data/offer.ts                 ← §2 numbers, the single source for the site and the edge functions
  src/pages/…                       ← intake, preview, order pages, EN + FR (slugs chosen in phase 2, logged)
supabase/                           ← phase 3
  config.toml
  migrations/
  functions/<name>/index.ts         ← §7
  functions/_shared/
.github/workflows/supabase.yml      ← phase 3: migrations and functions deploy on push to main
```

- `offer.ts` is read by the Astro build and by the edge functions. Phase 3 picks the mechanism (for example a JSON
  file both import, or a build step that copies it), and a test asserts the two copies match.
- The edge functions never trust a price from the browser. They compute the amount from the offer data and the
  validated spec.

## 5. Section kit (`packages/site-kit`)

The kit is a set of section components with a few layout variants each, plus style presets. A site is a list of
section choices and one preset.

**Sections for v1** (3 or 4 variants each): header and navigation · hero · services · about · proof (reviews,
badges, service area) · gallery or past work · call to action · contact (form, phone, hours, map) · footer.

**Rules for every section**
- A variant changes layout only, never which content fields the section needs. Any variant can be swapped for
  another without new content.
- Each section declares its content fields in `schema.ts` (for example `headline`, `body`, `image`, `phone`), with a
  type, a maximum length and whether the field is required. The same schema drives the placeholder copy in the
  preview, the AI content questions (§8) and the build brief (§9).
- Colours, fonts and spacing come from CSS custom properties only, so a preset is one small set of tokens.
- Placeholder copy is written per industry, stored as data, and exists in EN and FR. FR carries `_review: true`, as in
  CLAUDE.md #2.
- Placeholder images are labelled blocks ("Your photo: a finished job"), not stock photos. No image licence to track,
  and nothing a customer could mistake for their own work.
- The customer's site uses kit tokens. The MAW "Menu du jour" brand applies to createawebsite.ca itself, never to a
  customer preview.
- Every variant meets WCAG 2.1 AA with every preset and every swatch.

**Style presets.** Three at launch, each a pairing of fonts, corner and spacing style. The customer picks a preset and
a main colour; they are never asked to choose fonts by name. Preset fonts must be licensed for use on customer sites
(OFL or similar) and self-hosted.

**Main colour.** A fixed set of swatches per preset (about eight), each checked in `contrast.ts` so text on the colour
and the colour on the page meet AA. No free colour picker in v1: an arbitrary colour can fail contrast and could not
be fixed without asking the customer again.

**Page recipes.** A recipe is the default section order for a page type in an industry: Home, Services, About,
Contact. The intake picks recipes; swaps edit them.

**The spec.** What the customer chooses is one object, validated by `validateSpec()` in the browser and again in the
edge function, and stored on the order unchanged:

```ts
type SiteSpec = {
  kitVersion: string;            // the kit release the spec was made with
  industry: IndustryId;          // 'trades' under decision 1
  language: 'en' | 'fr';         // plus a second language if the add-on is bought (decision 5)
  businessName: string;
  hasLogo: boolean;
  preset: PresetId;
  colour: SwatchId;
  pages: Array<{ type: PageType; sections: Array<{ section: SectionId; variant: VariantId }> }>;
};
```

**What "hidden" means.** There is no gallery page and no index of variants. A visitor only ever sees the sections in
their own preview, plus the finished examples on the public site. Anything shown in a browser can be copied, so the
kit is kept unbrowsable, not secret. The staging-only kit page built in phase 1 exists only in `PUBLIC_NOINDEX=1`
builds, and a test asserts it is absent from the production output.

## 6. Customer flow

The customer goes from first question to paid order in one sitting, with no login and no meeting.

1. **Intake.** Five or six plain questions: type of business, business name, which pages (up to the page limit),
   main colour, style preset, and whether they have a logo; plus the site language under decision 5. Answers are kept
   in the browser (`sessionStorage`) until checkout, so a reload or the back button keeps their place.
2. **Preview.** The site is assembled from the page recipes with their business name and the industry's placeholder
   copy. It works at phone and desktop widths, and the customer can switch between the two.
3. **Swaps.** For each section the customer can step through its variants, and can change the colour or preset.
   Nothing else is editable.
4. **Build this.** A summary shows what is included, from `offer.ts`: page count, one revision round, 10 business days
   from the day content is complete, and the plain statement that the site is template-based. The customer enters
   name, email and phone and agrees to the terms and privacy notice.
5. **Deposit.** The `create-checkout` function validates the spec, saves a `pending_payment` order and returns a Stripe
   Checkout URL. On payment the order moves to `paid` with the exact section choices, and the customer gets a
   confirmation email with a private link to continue. A pending order whose Checkout session expires is deleted.
6. **Content.** From that link: "Do you already have your content?" If yes, they upload text, logo and photos per
   section. If no, AI asks short questions per section and drafts the copy, which they approve or edit (§8).
7. **Handoff.** When content is marked complete, the team receives the build brief (§9) and the 10-day clock starts.

**Preview build.** Section variants are pre-rendered to HTML at build time and assembled in the browser by a vanilla TS
island. Proposed: the preview renders in a same-origin frame, so the kit's styles never mix with createawebsite.ca's,
and the phone view uses the kit's real breakpoints. The variant fragments are fetched on demand rather than all
inlined, to keep first load within the 300 KB budget. Phase 2 may choose otherwise and logs why.

**Order states:** `pending_payment` (internal, never shown) → `paid` → `content_in_progress` → `content_complete` →
`in_build` → `in_review` → `delivered`. The customer's link always shows the current state.

| Move | Made by |
|---|---|
| → `paid` | The Stripe webhook |
| → `content_in_progress` | The first content save |
| → `content_complete` | The customer submitting their content |
| → `in_build`, `in_review`, `delivered` | The team, through the ClickUp task status (proposed: a `clickup-webhook` function maps statuses to states, so nobody opens Supabase) |

**Emails** (EN or FR, by the order's language; wording `TODO(angelique)`): payment confirmation with the private link;
content received; delivered; reminders while content is unfinished.

**Edge cases to handle in v1**
- Preview abandoned before "Build this": nothing is stored server-side.
- Checkout cancelled or failed: back to the summary with the spec intact.
- Payment made but content never finished: reminder emails at set intervals. The intervals, wording and refund terms
  are `TODO(angelique)`.
- Customer wants something outside the kit: a link to the custom quote flow.

## 7. Backend (Supabase and Stripe)

**Tables** (all with row-level security on and no policies for the anonymous role, so the browser can read nothing
directly; edge functions use the service role):
- `orders` — id, token hash, status, locale, spec, customer name, email and phone, amounts and currency, Stripe
  session and payment ids (unique), ClickUp task id, timestamps for each state.
- `order_content` — order, section, field, value, source (`customer` or `ai`), approved.
- `order_files` — order, section, field, storage path, type, size.
- `stripe_events` — Stripe event id as the primary key, so a repeated webhook is a no-op.
- AI usage counters per order (§8).

**Functions**

| Function | Called by | Does |
|---|---|---|
| `create-checkout` | The "Build this" step | Validates the spec, computes the amount from the offer data, creates the pending order and the Checkout session |
| `stripe-webhook` | Stripe | Verifies the signature; marks the order `paid`; issues the order token; sends the confirmation email; deletes pending orders on `checkout.session.expired` |
| `order` | The order page | Reads the order's state, spec summary and content; saves content fields |
| `upload-url` | The order page | Returns a signed upload URL for one field, after checking type and size |
| `draft-content` | The order page | AI drafting (§8) |
| `submit-content` | The order page | Marks content complete, builds the brief, emails it and creates the ClickUp task |
| `clickup-webhook` | ClickUp | Verifies the signature; moves the order between the team's states |
| `reminders` | A schedule | Sends the content reminders |

**The order link.** The token is 32 random bytes, base64url-encoded; only its SHA-256 hash is stored. It rides in the
URL fragment (`/order/#t=…`, or the French equivalent), which browsers never send to a server, and the order page
sends it to the functions in a header. The functions accept requests only from createawebsite.ca and the staging
origin.

**Stripe.** Checkout in payment mode, CAD, for the deposit only. Test mode until phase 6. Tax and the balance follow
item E.

## 8. AI content drafting

- **Where:** the `draft-content` edge function calls the Claude API with the official SDK (`@anthropic-ai/sdk`). The
  key is a function secret and never reaches the browser.
- **Model:** `claude-opus-5-5`, set in one constant. Set `output_config.effort` explicitly (start at `medium`) and
  measure draft quality before changing it.
- **Input, and only this:** the section's field schema, the customer's answers to that section's questions, the
  business name, the industry and the language. The function accepts no free-form prompt, so it cannot be used as a
  general AI service.
- **Output:** structured outputs (`output_config.format` with a JSON schema generated from the section's field schema,
  maximum lengths included). Validate the result against the schema again before saving it.
- **Never invent facts.** No licence numbers, years in business, awards, certifications, prices, guarantees or reviews
  unless the customer gave them. Where an answer is missing, the draft leaves a visible bracketed placeholder for the
  customer to fill (for example "[years in business]").
- **Refusals and cut-offs:** check `stop_reason` before reading the content. Server-side fallbacks are on
  (`fallbacks: "default"` with the `server-side-fallback-2026-07-01` beta). If the result is still a refusal, or stops
  at `max_tokens`, the customer is offered the write-it-yourself path, not an error page.
- **Limits:** proposed 3 drafts per section and 40 per order, plus a per-address limit, counted before the call.
- **Approval:** the customer approves or edits every drafted field. An unapproved draft never reaches the brief.
- **Privacy:** customer answers are sent to Anthropic. The privacy page and consent text say so (§10).
- **Tests:** the API is mocked in CI. No live calls in tests.

## 9. Team handoff

On `content_complete`, `submit-content` produces one build brief and sends it two ways: by email to the team address
(`TODO(angelique)`) and as a ClickUp task in the list from item F.

**The brief contains:** order id and link; customer name, email, phone and language; preset and colour; every page
with its sections and variants in order; every field's content with its source (customer, or AI approved by the
customer); the files; any add-ons (second language, care plan); the date content was completed and the due date, 10
business days later (statutory holidays `TODO(angelique)`).

**Files** are attached to the ClickUp task, so the team works from one place and nobody needs Supabase access. The
brief alone must be enough for a builder to start.

## 10. Rules for this build

**Carried over from `CLAUDE.md`**
- Every string, route, email and error exists in EN and FR-CA; machine-drafted French is flagged `_review: true`.
- No framework runtime in the browser; vanilla TS islands only. Lighthouse mobile 95 or more on every page.
- WCAG 2.1 AA: keyboard-only works, native controls, reduced motion respected.
- Never invent prices, promises, testimonials, timelines or legal text. Leave `TODO(angelique)` and list it in
  `DECISIONS.md`.
- Only `PUBLIC_*` identifiers in `.env`.

**New for this build**
- The Stripe secret key, the Stripe webhook secret, the Supabase service key, the Claude API key, the email provider
  key and the ClickUp token live only in Supabase function secrets or GitHub Actions secrets. Never in the repo, never
  in browser code, never in logs.
- Use Stripe test mode until phase 6. Verify the webhook signature, and make the handler safe to receive the same event
  twice.
- Every Supabase table has row-level security on. The browser never reads `orders` directly; the order link works
  through an edge function and the token in §7.
- Uploads: images as JPEG, PNG or WebP; logos also as SVG or PDF; 10 MB per file and 40 files per order (proposed).
  Files go in a private bucket and are never served publicly. SVGs are never rendered on our pages.
- The AI drafting function only accepts the section field schema and the customer's answers, and is rate-limited
  (§8).
- Before launch, the privacy page and consent text cover payments, uploads, AI drafting (including that answers go to
  Anthropic) and retention (item G), under Law 25. The legal wording comes from Angelique.
- Price, deposit, page limit, revision count and delivery days are read from `offer.ts`, as `pricing.ts` works today.
- Zero third-party requests before consent still holds. Stripe is reached by navigating to Checkout, not by loading a
  script.

**Do not build:** customer accounts, a dashboard or an admin panel; a second industry; a gallery or index of the kit;
automated deployment of customer sites; anything in the MAW app repo.

**Review.** One pull request per phase, with 390 px and 1280 px screenshots in `docs/screens/`. Each goes through the
Labs review: the AI review comment, the code owner's approval and a ClickUp review task with Angelique watching.
Phases 3 and 4 handle money and personal data, so the review there covers the security checklist line by line.

## 11. Public site (phase 5)

- The home page is rewritten around the offer: what you get, the price, the 10-day delivery, the template disclosure,
  finished examples, an FAQ and a terms link. EN and FR.
- The custom quote flow keeps working on its own route, linked from the home page and from the "outside the kit" path.
- Examples are real sites from the phase 6 test builds. None are invented, and the section is not rendered until there
  are some (the same rule as `src/data/work.ts`).
- `pnpm check:copy:strict` returns zero French-review and zero `TODO(angelique)` items, and every CI budget passes.

## 12. Phases

Each phase is a separate Claude Code session and pull request. The dates are proposed targets, not commitments.

| Phase | Target | Claude Code builds | Done when |
|---|---|---|---|
| 0. Spec | 30 Nov 2026 | This brief; the `DECISIONS.md` entry recording the direction change; `CLAUDE.md` updated to point at both | The open decisions are answered in the brief; nothing in the old brief contradicts it silently |
| 1. Section kit | 14 Dec 2026 | `packages/site-kit`: 9 sections with variants, field schemas, 3 presets, swatches, placeholder content and recipes for the first industry; a staging-only kit page for review | Every variant renders at 390 px and 1280 px with every preset; swapping variants needs no content change; screenshots in `docs/screens/` |
| 2. Intake, preview, swaps | 4 Jan 2027 | Intake form, preview assembly island, variant and colour swaps, the order summary screen | A full run from first question to summary works on a phone with keyboard only; an end-to-end test covers it; the Lighthouse budget still passes |
| 3. Orders and deposit | 15 Jan 2027 | Supabase schema with row-level security, `create-checkout`, `stripe-webhook`, confirmation email, the private order link | A Stripe test payment creates one order with the exact selections; a failed or repeated webhook creates none, and no duplicate |
| 4. Content and handoff | 22 Jan 2027 | Upload path, AI question-and-draft path, order status page, the build brief by email and ClickUp, `clickup-webhook`, reminders | Both content paths end in a complete brief; the brief alone is enough for a builder to start; uploads are private to the order |
| 5. Public site | 29 Jan 2027 | Home page rewritten around the offer, finished examples, template disclosure, FAQ, terms link, EN and FR | `pnpm check:copy:strict` returns zero; every CI budget passes |
| 6. Test builds and launch | 5 Feb 2027 | Fixes from three real test orders; the Labs launch checklist | Three sites delivered from briefs with hours recorded; the code owner's review approved; September's launch blockers closed |

**Order matters.** Phase 1 is the long one and sets the quality of everything a customer sees; do not start phase 2
until Angelique has approved the kit on staging. Phases 3 and 4 can overlap. Phase 5 depends on real example sites, so
the three test builds can start as soon as phase 4 works.

**Outside the build:** how the first customers find the site needs its own plan, running alongside phases 1 to 5, so
that launch day has traffic.

## 13. Inputs needed from Angelique (blocking marked ●)

| # | Input | Used in | Needed by |
|---|---|---|---|
| ● | Confirm or change decisions 1, 2 and 5 | §3, §5 | Phase 1 |
| ● | Name the code owner; add the repo to the PR hand-off map (decision 8) | §10 | Phase 1's pull request |
| ● | Create `main` and make it the default branch (item A) | CI, deploy | Phase 1's pull request |
| | Approve the kit on staging | §12 | Phase 2 |
| ● | Price, deposit, page limit, revisions, refund terms; second-language add-on price; care plan price (decisions 4–6) | `offer.ts` | Phase 3 |
| ● | Stripe account, GST and QST numbers, how the balance is collected (decision 7, item E) | §7 | Phase 3 |
| ● | Supabase project (item B) and email provider account with DNS access (item C) | §7 | Phase 3 |
| ● | ClickUp list, assignee and token (item F); team email address for briefs | §9 | Phase 4 |
| | Email wording, reminder intervals, statutory holidays, retention period (item G) | §6, §9 | Phase 4 |
| ● | Privacy and consent wording for payments, uploads, AI drafting and retention; terms of sale | §10 | Phase 5 |
| ● | September's launch blockers (decision 9) and the FR review pass | `DECISIONS.md` | Phase 6 |

## 14. Definition of done (v1 launch)

- [ ] A visitor goes from first question to paid deposit on a phone, keyboard only, in EN and in FR.
- [ ] A Stripe live payment creates exactly one order with the exact spec; a repeated webhook creates nothing.
- [ ] Both content paths end in a brief the team can build from, delivered by email and ClickUp.
- [ ] The order link shows the right state at every step, and no other order's data can be reached with it.
- [ ] No secret appears in the repo, the browser bundle or the logs; every table has row-level security on.
- [ ] The AI drafting endpoint refuses anything but its schema and answers, and its limits are verified.
- [ ] Lighthouse mobile ≥ 95 on every page, in CI; no third-party request before consent.
- [ ] `pnpm check:copy:strict` returns zero; the privacy page and terms are Angelique's wording.
- [ ] Three real sites delivered from briefs, with hours recorded.
- [ ] `docs/DECISIONS.md` current; screenshots at 390 px and 1280 px for every new page in `docs/screens/`.
