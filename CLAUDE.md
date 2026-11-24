# maw-landers — notes for Claude Code

Monorepo for Monkeys at Work (MAW) funnel landing sites: static Astro sites sharing one kit (`packages/lander-kit`), each deployed to its own SiteGround docroot by GitHub Actions. First site: `createawebsite.ca`. Sister sites (`wphandyman.ca`, weappear landers) reuse the kit later.

**Read `docs/createawebsite-brief.md` and then `docs/DECISIONS.md` before doing anything.** The brief is the original spec (scope, routes, SEO, privacy, deploy, definition of done). The 7 Sept 2026 evening entry in DECISIONS.md records Angelique's direction change that supersedes parts of it: the site is **quote-first** (an on-page instant quote mirroring the MAW chatbot, leads into the MAW `process-lead` pipeline) and uses the **"Menu du jour" sub-brand** design system, not the brief's §5 page copy or §8 tokens. Where both are silent, choose the simplest option that keeps Lighthouse ≥ 95 and log the choice in `docs/DECISIONS.md`.

**Configurator build (from 24 Nov 2026): read `docs/configurator-brief.md` first, then the 24 Nov 2026 entry in `docs/DECISIONS.md`.** The site's main offer becomes a fixed-price, template-based website: intake questions, a preview assembled from a section kit, a Stripe deposit, then content and a build brief for the team. That brief wins where it conflicts with the original brief or older DECISIONS entries, and its §1 lists each conflict. Its decisions are **proposed** until Angelique confirms them (§3); work one phase per session and per PR (§12).

## Layout

- `packages/lander-kit/` — tokens, fonts, base styles, Astro components, helpers (seo, schema, hubspot, analytics, i18n). No copy lives here.
- `sites/<name>/` — one Astro site per domain: pages, `src/i18n/{en,fr}.json`, `src/data/{pricing,versions,work}.ts`, `src/content/guides/`, and `public/api/audit.php` (the one server-side file).
- `packages/site-kit/` (from configurator phase 1) — the customer-site section kit: sections and variants, field schemas, style presets, industry recipes and placeholder copy. Its own tokens, never the MAW brand.
- `supabase/` (from configurator phase 3) — migrations and edge functions for orders, checkout, content and handoff.
- `sites.json` — name, domain, docroot, `deploy` flag; drives CI and deploy matrices.
- `docs/` — briefs, `DECISIONS.md`, `screens/`.

## Commands

- `pnpm install` — workspace install (current Node LTS, pnpm).
- `pnpm --filter createawebsite-ca dev | build | preview`
- `pnpm check` — `astro check` + FR-review and TODO counts across sites.
- `pnpm lhci` — Lighthouse CI against built output with the budgets in the brief §10.5.
- `pnpm test` — quote engine (every answer path) and audit engine (fixtures); `pnpm e2e` — browser checks of the quote flow, the audit tool, consent gating and the no-JS fallbacks against the built site.
- `node scripts/screens.mjs` — page screenshots at 390/1280; `node scripts/screens-audit.mjs` — the site-check result and the downloadable report, which only exist after a scan.
- `node scripts/audit-capture.mjs <domain…>` — capture real websites into `docs/corpus/` (needs internet; run it on a laptop, not here). `node scripts/audit-corpus.mjs` replays them through the engine offline and shows how often each check fires: that table is how a false positive becomes visible.
- `tools/selftest.php` — throwaway, uploaded by hand to a docroot, answers in one page load whether `audit.php` can work on that host. Never deployed; delete it after reading.
- `pnpm test:quote` — walks every answer path of the quote engine; `pnpm e2e` — browser checks of the quote flow, consent gating and no-JS fallbacks against the built site.

## Non-negotiables

1. **Brand at creation time.** Only the tokens in `packages/lander-kit/styles/tokens.css` (the MAW palette re-weighted for the "Menu du jour" sub-brand: Paper `#FBF4EA` page ground, charcoal ink, Deep Warmth leaders, Sunset for the brand dot and primary CTA, Power Blue for the MAW mark only). Satoshi Black/Bold for display, IBM Plex Sans for body, IBM Plex Mono for meta labels, all self-hosted. Perfect Sunset and Deep Warmth are never text colours on paper; buttons on them carry charcoal labels. The MAW wordmark comes from `packages/lander-kit/assets/`. If a brand asset is missing, stop and ask; do not ship placeholder styling to be fixed later. This governs createawebsite.ca's own pages; customer previews use `packages/site-kit` tokens and never show the MAW brand (configurator brief §5).
2. **Bilingual by law, not by preference.** Every route, string, meta tag, schema field, alt text, error and email exists in EN and FR-CA. Flag machine-drafted French with `_review: true`; `main` cannot deploy while the count is non-zero.
3. **Weight.** No framework runtime on the client. Vanilla TS islands only for the quote panel, the question form, the consent banner and the language bar, plus the configurator's intake, preview, content and order-status islands. No Stripe.js: checkout is a redirect to a URL our edge function returns. Zero third-party requests before consent; the lead endpoint is called only when the visitor submits the intake. Lighthouse mobile ≥ 95 in all four categories on every page (SEO exempted on the `noindex` 404 and thank-you pages) — CI fails otherwise.
4. **Accessibility.** WCAG 2.1 AA: keyboard-only works everywhere, visible focus, native controls (`radio`, `details`, `table`), `prefers-reduced-motion` respected, no scroll or load animations.
5. **Facts come from the brief, the MAW chat framework or Angelique.** Prices come from `src/data/pricing.ts` only, and the configurator offer's numbers from `src/data/offer.ts` only; the quote engine (`packages/lander-kit/lib/quote.ts`) must stay in step with the MAW chat's system prompt and pricing framework. Never invent testimonials, case studies, timelines, promises or legal text — leave `TODO(angelique): …` and list it in `docs/DECISIONS.md`.
6. **Secrets.** Only `PUBLIC_*` identifiers in `.env`; SSH keys and hosts live in GitHub Actions secrets. Stripe, Supabase service-role, Claude API, email and ClickUp keys live only in Supabase function secrets or GitHub Actions secrets, never in browser code or logs. Nothing else in git.
7. **Design defaults to avoid** (brief §8.5): uniform rounded cards with blurred shadows (radius is 0, shadows are hard offsets), gradient washes, uppercase eyebrow labels above headings on paper (the mono rule-flanked signage inside dark blocks is the design's own device), arrows on buttons, numbered markers on non-sequences, fade-up-on-scroll, stock hero photos, icon rows, emoji. For `packages/site-kit`, a preset may set its own radius and shadows; the rest of this list still applies.

## Working style

- One PR per brief section (for the configurator, one PR per phase in its §12); each PR includes 390 px and 1280 px screenshots in `docs/screens/`.
- Keep `docs/DECISIONS.md` current: date, decision, why, what it affects.
- Ask (don't guess) when: a brand asset is missing, copy is ambiguous, a legal text is needed, a budget can't be met without dropping something in the brief.
- Do not touch the MAW app repo from here; the chat-parameter change in brief §7.1 is a separate task for the MAW team.
