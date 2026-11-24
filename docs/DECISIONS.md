# Decisions — createawebsite.ca

Kept by Claude Code. One entry per choice the brief left open, plus every `TODO(angelique)`. Newest first.

The open decisions and the inputs each phase needs are in `docs/configurator-brief.md` §3 and §14; keep those tables
and this log in step.

The previous project's log (the quote-first lander, 7–17 Sept 2026) is in git history at commit `3dcc7f6`, in
`docs/DECISIONS.md`.

## 2026-11-24 (night) — a design round before the kit

Angelique agreed to settle the design before building the kit. Phase 1a is a Figma file she runs outside the repo (three
style directions for customer sites, two palettes each, and wireframes of the configurator flow), to choose from.
Phase 1 starts only once she has picked a direction. Why: the kit has dozens of variants and each gets an Elementor
twin, so changing the look after phase 1 or 1b costs double. Spec in the brief, §13 "Phase 1a".

## 2026-11-24 (evening) — visual system first; contact details; tax by location; licences

- **Visual system first.** Angelique wants a system that works visually before anything else: phases 1 and 2 (kit,
  intake, preview, swaps) on staging, with no backend. Orders and admin, content and AI, the Elementor library, the
  public site and payments become modules built afterwards, one at a time; their dates are reset once she has seen it.
  Phase 1b moves after phase 2 accordingly, and staging shows every variant whether or not its Elementor twin exists
  yet; the twin-gating rule applies to the live preview from launch.
- **Business name and contact details in the intake.** Phone, email, address or service area and opening hours, each
  optional, go into the spec (`contact`) and appear in the preview's header, contact section and footer.
- **Tax follows the purchaser's location**, under Monkeys at Work's registration numbers. "Build this" asks for country
  and province or state; prices are shown before tax. This replaces the open question about customers outside Canada.
  Phase 7's Stripe Checkout can calculate tax by location.
- **Under consideration: the first year of Elementor Pro, and of WPML for second-language sites, included** in the
  price. Renewals after that are open.

## 2026-11-24 (later) — decisions 1, 2 and 5: any business, Elementor, a paid second language

Angelique answered the three decisions that blocked phase 1:

- **Decision 1: any legal business, anywhere**, not one industry. The kit is generic: placeholder copy reads for any
  business and takes the customer's business name and type (`businessType` in the spec, in their own words). The team
  checks each order before invoicing the deposit and cancels any it will not build. What counts as illegal (proposed:
  in Canada or where the customer operates) goes in the terms of sale. Not decided: whether GST/QST applies to
  customers outside Canada (MAW's accountant), and prices stay in CAD for everyone (proposed).
- **Decision 2: customer sites are WordPress with Elementor, so customers can edit them.** This brings the cost the
  plan named: two matching libraries. The HTML kit still drives the preview; each section variant gets an Elementor
  template with the same id, presets become Elementor global fonts and palettes Elementor global colours, all exported
  into `packages/site-kit/elementor/`. A new **phase 1b** builds that library alongside phase 2, on a staging WordPress
  with Elementor Pro (item H). Rule: no variant is offered in the preview until its Elementor twin matches it in
  screenshots, so a customer is never shown something the team cannot build. Proposed: Elementor Pro on Hello
  Elementor, hosted by Monkeys at Work; the licence, and who owns it after handover, are open.
- **Decision 5: one language included, the second a paid add-on with a paid multilingual plugin.** Proposed: WPML,
  which works with Elementor. The MVP offers English and French customer sites; other languages are open.
- **Decision 6 follows:** customers edit their own content, so the care plan is hosting, updates, backups and security,
  not the route for edits. Price and whether it is required are still open.
- The build brief now names the Elementor template for each section and the global settings for the preset and
  palette. Phase 6's test builds are done in Elementor and handed over with an account that can edit the site; the
  WordPress role and handover guide are open.

## 2026-11-24 — Angelique's answers, and a fresh start

Angelique reviewed the first draft of the brief and ruled:

- **Start fresh.** The repository's old contents are removed: the lander kit, the quote engine, the site check, the
  workflows, the decision log and the screenshots. Nothing is carried as code. The brief's §1 lists the rules and the
  hosting lessons that carry over, and the "Menu du jour" brand assets are restored from commit `3dcc7f6` in phase 1.
  This also settles decision 3 (there is no old home page) and drops the site-check item.
- **Price: $1,830 CAD** before tax, not $2,000. The deposit stays at a proposed 50% ($915).
- **Angelique is the code owner, as `MAWAdmin`.** Claude sessions open pull requests from the same account, and GitHub
  does not let a pull request's author approve it, so `main` requires no approval: Angelique reviews each pull request
  and merges it herself as admin. `.github/CODEOWNERS` names `MAWAdmin`.
- **`main` is created** at `3dcc7f6`, so the fresh start goes in as a reviewable pull request. Making it the default
  branch is Angelique's step in GitHub: this session's GitHub access cannot change repository settings.
- **Colour palettes, not single colours.** Fixed five-colour palettes, browsed the way coolors.co shows them. Each
  palette maps its colours to the kit's roles and is checked for AA contrast at build time.
- **Stripe is the last phase, after the MVP.** In the MVP, "Build this" submits the order; Monkeys at Work invoices
  the deposit, and the team marks it received in the admin panel, which sends the customer their private link. Phase 7
  moves the deposit to Stripe Checkout.
- **An admin panel is in.** It replaces the plan's "no admin panel" rule and the draft's ClickUp-status webhook. The
  team moves orders through their states there; ClickUp keeps one task per order as the team's to-do.
- **AI drafting as proposed:** `claude-opus-5-5`, structured outputs, server-side refusal fallbacks, no invented facts.
- **Tax under the Monkeys at Work umbrella:** MAW's GST/QST registration numbers on every invoice.
- **`wphandyman.ca`:** the draft's note that it did not support choosing home trades was wrong and is withdrawn.
  Decision 1 keeps the plan's reasoning.

### Choices the brief made as a result

- **The private order link is sent only once the deposit is paid**, so uploads and AI drafting (which costs money per
  call) are never open to an unpaid order. Before that the customer gets an order-received email saying the invoice is
  on its way.
- **An unpaid order is cancelled and its personal data deleted after 14 days** (proposed, item F).
- **Admin sign-in is Supabase Auth with emailed sign-in links**, limited to an allowlist of Monkeys at Work addresses
  and enforced by row-level security on the server. No passwords.
- **The admin panel lives at `/admin/` on the same site**, `noindex`, in vanilla TS, bilingual, outside the Lighthouse
  budget but inside WCAG 2.1 AA.
- **Every order change is recorded** in `order_events`, naming who made it.
- **Customer files stay in private storage** and are downloaded from the admin panel, not copied into ClickUp or email.
- **Phase 1 now includes the foundation**: workspace, CI, deploy and staging workflows, and the restored brand, as well
  as the kit. It keeps its 14 Dec target, which makes it the tightest phase.

### Still open

(Superseded in part by the entry above.) Decisions 1, 2, 5 and 6 are proposed; decision 4 is confirmed for the price
only. Items A (no `main` branch), B
(Supabase project), C (email provider), D (which invoicing tool), E (ClickUp list), F (retention) and G (admin
allowlist) are open. See the brief §3 and §14.

## 2026-11-24 — direction change: a fixed-price configurator

Angelique's build plan of 24 Nov 2026 ("createawebsite.ca — Claude Code build plan", in Claude Docs) set the product:
a done-for-you website at a fixed price, sold from a short intake and an assembled preview, built by the team from a
brief and delivered in 10 business days, with the site saying plainly that it is template-based. Seven phases, one
pull request each.

Choices the brief made where the plan was silent, still standing after the answers above:

- **Home is always one of the pages.** The customer picks the rest, from Services, About and Contact, up to the limit.
- **Placeholder images are labelled blocks**, not stock photos.
- **Preset fonts must be licensed for customer sites** (OFL or similar) and self-hosted.
- **The order token** is 32 random bytes, stored only as a hash, and carried in the URL fragment so it never reaches
  server logs.
- **A new Supabase project** for createawebsite, with its migrations and functions in this repo (proposed, item B).
- **Upload limits:** JPEG, PNG, WebP; SVG and PDF for logos only; 10 MB per file; 40 files per order. SVGs are never
  rendered on our pages.
- **No Stripe.js, when Stripe arrives in phase 7.** Checkout is a redirect to a URL our function returns, so there is
  still no third-party script before consent.
