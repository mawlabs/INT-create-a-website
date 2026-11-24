# createawebsite.ca — notes for Claude Code

createawebsite.ca sells a done-for-you website at a fixed price, under the Monkeys at Work (MAW) business. A visitor
answers a few questions, previews a site assembled from a section kit, asks us to build it, pays a deposit, and gives us
their content; the team builds the site in WordPress with Elementor from a brief and delivers in 10 business days, and
the customer can then edit it. Any legal business, anywhere, can order.

**Read `docs/configurator-brief.md` and then `docs/DECISIONS.md` before doing anything.** The brief is the spec. Its §3
says which decisions are confirmed and which are only proposed; its §13 lists the phases. Work one phase per session
and per pull request, and do only the phase you were given.

The repository was cleared on 24 Nov 2026 for a fresh start. The earlier quote-first lander is in git history at commit
`3dcc7f6` (branch `claude/new-session-dpotje`); restore its brand assets from there when the brief says to (§1), and
nothing else.

## Layout (as the phases build it)

- `packages/site-kit/` — the customer-site section kit behind the preview: sections and variants, field schemas, style
  presets, colour palettes, page recipes and generic placeholder copy. Its own tokens, never the MAW brand.
- `packages/site-kit/elementor/` (from phase 1b) — the Elementor twin of every variant, preset and palette. A variant is
  not offered in the preview until its twin matches it.
- `sites/createawebsite-ca/` — the public site, the intake and preview, the customer's order page and the admin panel.
  `src/data/offer.ts` holds every price and term; `src/i18n/{en,fr}.json` holds every string.
- `supabase/` — migrations and edge functions for orders, content, AI drafting, handoff and admin actions.
- `docs/` — the brief, `DECISIONS.md`, `screens/`.

Commands arrive with phase 1 and are listed here when they exist.

## Non-negotiables

1. **Bilingual by law.** Every route, string, meta tag, alt text, error and email exists in EN and FR-CA, including the
   admin panel. Flag machine-drafted French with `_review: true`; production cannot deploy while any remain.
2. **Weight.** No framework runtime in the browser; vanilla TS islands only. Zero third-party requests before analytics
   consent. Lighthouse mobile ≥ 95 in all four categories on every public page (SEO exempted only on `noindex` pages;
   the admin panel is exempt from the budget, not from accessibility).
3. **Accessibility.** WCAG 2.1 AA: keyboard-only works everywhere, visible focus, native controls,
   `prefers-reduced-motion` respected, no scroll or load animations. Every kit variant passes with every preset and
   palette.
4. **Facts come from the brief or Angelique.** Prices and terms come from `src/data/offer.ts` only. Never invent
   prices, testimonials, example sites, timelines, promises or legal text — leave `TODO(angelique): …` and list it in
   `docs/DECISIONS.md`. AI-drafted customer copy never invents facts either (brief §8).
5. **Secrets.** Only `PUBLIC_*` identifiers in `.env`. Supabase service-role, Claude API, email, ClickUp, SiteGround
   SSH and (from phase 7) Stripe keys live only in Supabase function secrets or GitHub Actions secrets — never in git,
   browser code or logs.
6. **Data.** Row-level security on every Supabase table; the browser never reads a table directly. Customer files stay
   in a private bucket. The order link token is stored only as a hash.
7. **Two brands, never mixed.** createawebsite.ca's own pages use the MAW "Menu du jour" sub-brand (brief §1). Customer
   previews use `packages/site-kit` tokens; customer sites use the matching Elementor global colours and fonts.

## Working style

- One pull request per phase, with 390 px and 1280 px screenshots in `docs/screens/`.
- Keep `docs/DECISIONS.md` current: date, decision, why, what it affects.
- Ask (don't guess) when: copy is ambiguous, a legal text is needed, a brand asset is missing, or a budget can't be met
  without dropping something in the brief.
- Do not touch the MAW app repo from here.
