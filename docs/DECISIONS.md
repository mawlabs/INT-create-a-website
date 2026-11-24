# Decisions — createawebsite.ca

Kept by Claude Code. One entry per choice the brief left open, plus every `TODO(angelique)`. Newest first.

The open decisions and the inputs each phase needs are in `docs/configurator-brief.md` §3 and §14; keep those tables
and this log in step.

The previous project's log (the quote-first lander, 7–17 Sept 2026) is in git history at commit `3dcc7f6`, in
`docs/DECISIONS.md`.

## 2026-11-24 — Angelique's answers, and a fresh start

Angelique reviewed the first draft of the brief and ruled:

- **Start fresh.** The repository's old contents are removed: the lander kit, the quote engine, the site check, the
  workflows, the decision log and the screenshots. Nothing is carried as code. The brief's §1 lists the rules and the
  hosting lessons that carry over, and the "Menu du jour" brand assets are restored from commit `3dcc7f6` in phase 1.
  This also settles decision 3 (there is no old home page) and drops the site-check item.
- **Price: $1,830 CAD** before tax, not $2,000. The deposit stays at a proposed 50% ($915).
- **Angelique is the code owner.** `CODEOWNERS` waits for her personal GitHub login. It cannot be `MAWAdmin`: Claude
  sessions open pull requests from that account, and GitHub does not let a pull request's author approve it.
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

Decisions 1, 2, 5 and 6 are proposed; decision 4 is confirmed for the price only. Items A (no `main` branch), B
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
