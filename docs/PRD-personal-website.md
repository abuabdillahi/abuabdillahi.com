---
title: Personal website — initial build
labels: [needs-triage]
---

## Problem Statement

I don't have a personal website. My work history, projects, services, and contact details are scattered across LinkedIn, GitHub, and the three project subdomains (limber, chargraph, catpik) — no single page tells a recruiter, client, or potential mentee who I am and what I offer.

I operate across three markets (Saudi Arabia, UAE, UK), and the Saudi market in particular makes Arabic-language presence valuable. I want bilingual capability but I'm not yet ready to commit to publishing Arabic content — I need to be able to switch it on later without a code change to the page surface.

I also want a small set of always-true signals visible from the hero: am I open to new work, what am I doing right now, what's my main throughput claim. And a place to drop blog posts when I start writing them, without rebuilding the site.

## Solution

A single bilingual-capable personal website at `abuabdillahi.com`, built with Next.js (App Router, TypeScript), statically exported, and deployed to Cloudflare Pages.

The site has two routes:

- **`/`** — a single long-scrolling page covering: hero with live availability pill, about, services (for companies / for individuals), selected projects (limber, CharGraph, CatPik with screenshots), experience (cards-with-cover layout — Aajil, Noala, Noda, Hurdle.bio, JLR), field notes preview (coming-soon state), and contact split into company and individual CTAs.
- **`/blog`** — blog index in coming-soon state with topic chips. Individual blog post pages will be added when I have my first real post.

Light/dark/system theme toggle and EN/AR language toggle, both persisted in localStorage. Arabic is gated by a build-time feature flag (`NEXT_PUBLIC_ENABLE_ARABIC`); when off, the AR toggle is hidden and the AR strings tree-shake out of the production bundle, but they remain in source so I can flip the flag without rewriting copy. Mobile gets a hamburger drawer because the existing prototype simply hides nav links below 760px with no replacement.

Visual choices locked: purple accent (`#7A5AE0`), compact density, editorial typography (Instrument Serif display + IBM Plex Sans body), serif headlines, lede hero layout, tracks services, screenshot projects, cards experience, punchy tone, empty field-notes state.

## User Stories

1. As a visitor, I want to see who Idris is and what he does within the first screen so I can decide whether to keep reading.
2. As a recruiter, I want to immediately see whether Idris is open to new opportunities so I know whether to reach out.
3. As a recruiter, I want to download Idris's CV as a PDF so I can share it internally.
4. As a hiring manager in KSA, I want to read the site in Arabic so I can evaluate Idris in the language we'll work in (when AR flag is enabled).
5. As a hiring manager, I want to see Idris's full work history with company branding so I can place him in context.
6. As a potential client, I want to see what services Idris offers and which fit my company vs. me as an individual so I can scope the right conversation.
7. As a potential mentee, I want a dedicated CTA for individual coaching so I don't feel I'm wasting Idris's time with a generic enquiry.
8. As a visitor on a mobile device, I want to navigate between sections without scrolling endlessly so I can find what I want quickly.
9. As a visitor on a mobile device, I want to read the site comfortably on a 360px-wide screen so I'm not pinching to zoom.
10. As a visitor browsing at night, I want the site to render in dark mode without flashing white on load so it doesn't hurt my eyes.
11. As a visitor, I want my theme choice (light/dark/system) to persist across page loads so I don't have to set it every visit.
12. As a visitor whose OS theme changes during the day (e.g. macOS auto), I want the site to follow that change when set to "system" so it stays consistent with my environment.
13. As an Arabic-language visitor (when AR flag is enabled), I want the layout to flip RTL automatically when I switch languages so it reads naturally.
14. As an Arabic-language visitor, I want numbers and code snippets to stay LTR even when the surrounding text is RTL so they're readable.
15. As an Arabic-language visitor, I want my language choice to persist across page loads so I don't have to re-switch every visit.
16. As a visitor, I want to see Idris's previous projects with real screenshots and tech tags so I can judge his work.
17. As a visitor, I want to click through to live demos and source code for each project so I can verify the work.
18. As a visitor, I want to see Idris's experience presented as cards with company covers and logos so it feels visually anchored rather than a wall of text.
19. As a visitor interested in his work history, I want a "Full CV (PDF)" link in the Experience section so I can download a printable version.
20. As a visitor, I want to see a clear "Field Notes coming soon" message when there are no blog posts yet, rather than a dead or broken page.
21. As a visitor at the Field Notes section, I want to see what topics Idris plans to write about so I have a sense of whether to come back.
22. As a visitor, I want to email Idris for company work via a clearly-labelled CTA so my email is routed correctly.
23. As a visitor, I want to email Idris for individual mentorship via a separate clearly-labelled CTA so my email is routed correctly.
24. As Idris, I want to update my availability state ("available" / "consulting only" / "selective" / "closed") by editing a single constant in source and redeploying so I don't need any backend.
25. As Idris, I want to enable Arabic by flipping one env var and redeploying so I can launch in two languages without a code change.
26. As Idris, I want Arabic translations to live in the same content modules as English (paired `{en, ar}`) so I have one place to edit copy for both languages.
27. As Idris, I want all visual layout choices (variant pickers from the design's Tweaks panel) baked into source code as the final picks so future-me isn't maintaining unused alternates.
28. As Idris, I want to write a blog post in the future and have it appear under `/blog/[slug]` so I don't need another site for writing — even though that route doesn't exist in this initial build.
29. As Idris, I want the site to be statically exported so it serves from Cloudflare's edge with no backend to run, secure, or pay for.
30. As Idris, I want image assets pre-processed at build time into multiple sizes and modern formats (AVIF / WebP / JPEG) so mobile visitors don't download 3000px source files.
31. As Idris, I want analytics via Cloudflare's proxy auto-injection so I don't add a script tag, cookie banner, or third-party dependency for traffic data.
32. As Idris, I want a polished social-share preview (Open Graph image) when the URL is pasted into Slack/LinkedIn/iMessage so the link looks like it belongs to a real person.
33. As Idris, I want a `robots.txt` and `sitemap.xml` generated so search engines can crawl the site cleanly.
34. As Idris, I want the four lib modules tested in isolation so I can refactor them later with confidence — without a brittle test of every UI element.
35. As Idris, I want a completeness test on the content module that fails when an EN string is missing an AR translation so I don't ship a half-translated section when I enable Arabic.
36. As Idris, I want a test on the i18n module that confirms AR strings never leak into the rendered output when the flag is off, so I can't accidentally ship Arabic content during an English-only deploy.

## Implementation Decisions

**Stack:** Next.js (App Router) + TypeScript + pnpm. Static export (`output: "export"`). Deployed to Cloudflare Pages.

**Styling:** CSS verbatim from the design prototype, dropped into `app/globals.css`. The prototype's CSS uses modern primitives (`oklch()`, `color-mix()`, logical properties, `data-*` attribute selectors) that map cleanly to React markup with zero translation cost. No Tailwind, no CSS-in-JS.

**Markup translation:** HTML structure is recreated as React components per section. The `data-*` attributes on `<html>` (theme, density, etc.) are preserved exactly so the existing CSS continues to work unmodified.

**Locked variant values** (from the design's `TWEAK_DEFAULTS`, with two overrides applied):

| Attribute | Value |
| --- | --- |
| `data-density` | `compact` |
| `data-display` | `serif` |
| `data-type` | `editorial` |
| `data-hero-layout` | `lede` |
| `data-services` | `tracks` |
| `data-projects` | `screenshots` |
| `data-experience` | `cards` |
| `data-tone` | `punchy` |
| `--accent` | `#7A5AE0` (purple) |
| `data-fn-state` | `empty` (override; design default was `populated`) |
| `data-theme` | runtime-resolved against `system` preference |

**Tweaks panel:** not shipped. Variant alternates are picked and committed; alternate CSS / markup is removed.

**Module breakdown:**

- **`lib/content`** — Pure-data TypeScript modules exporting structured copy and assets per section. Bilingual entries are `{ en: string, ar: string }` pairs. Validated by a schema check (Zod or hand-rolled) so the test suite can assert completeness. Includes the `availabilityState` constant (`"available" | "consulting" | "selective" | "closed"`).

- **`lib/i18n`** — Exposes a `<T>` component (`<T en="..." ar="..." />`), a `pickLocale(content, lang)` helper for selecting from `{en, ar}` objects, and the `isArabicEnabled` constant (which reads `process.env.NEXT_PUBLIC_ENABLE_ARABIC` at build time and is statically `false` or `true`). When `isArabicEnabled === false`, the `<T>` component is a thin wrapper that always renders the `en` prop, and the `ar` strings tree-shake out via dead-code elimination.

- **`lib/theme`** — Exports `<ThemeScript />`, an inline blocking `<script>` rendered in `<head>` before paint, which reads localStorage and resolves `system` against `prefers-color-scheme` before any visible markup paints. Also exports a `useTheme()` hook for the theme toggle menu and a `<MediaQueryListener />` component that updates the resolved theme when the OS theme changes while a tab is open.

- **`lib/lang`** — Analogue of `lib/theme` for `en` / `ar`. Inline `<LangScript />` for FOUC prevention, `useLang()` hook for the toggle. The entire module is gated by `isArabicEnabled` — when the AR flag is off, exports are inert and the script tag is not rendered.

- **`lib/images`** — `scripts/optimize-images.ts` (run as a `prebuild` step) walks `public/_source/` with `sharp` and emits `.avif` / `.webp` / `.jpg` variants at 640w/1280w/1920w into `public/_optimized/`. A custom loader in `next.config.js` resolves `<Image src="...">` paths to the right variant + `srcset`.

**UI components** are presentational and compose the lib modules: `Nav`, `MobileNavDrawer` (new — fills the prototype's gap below 760px), `Hero`, `About`, `Services`, `Projects`, `Experience`, `FieldNotes`, `Contact`, `Footer`, plus the small `AvailabilityPill`, `ThemeMenu`, `LangToggle`.

**Feature flag contract:**
- Single env var: `NEXT_PUBLIC_ENABLE_ARABIC` (build-time, `"true"` / `"false"`).
- When `false`: `<html lang="en">` only; no AR toggle in the nav; `<T>` always renders English; `lib/lang` is a no-op; AR string fields in `lib/content` remain in source but are not referenced.
- When `true`: the existing toggle UI activates; localStorage drives the `lang` attribute; RTL layout flips via the existing `html[lang="ar"]` selectors in the design CSS.

**Persistence:** `localStorage` keys mirror the prototype: `theme` (`"light" | "dark" | "system"`), `lang` (`"en" | "ar"`). Inline blocking script in `<head>` applies the stored value to `<html>` before paint — required because static export has no per-request server response to set the right class.

**Routing:** `app/` layout with two pages. No middleware, no API routes.

**Contact:** plain `mailto:` links to `mail@abuabdillahi.com` with split subjects ("for companies" vs "for individuals — mentorship").

**Assets:** all images from `personal-website/project/assets/` in the design bundle copied into `public/_source/`, then optimized to `public/_optimized/` at build. `IdrisCV.pdf` served verbatim from `/public/IdrisCV.pdf`. A new `og.png` (1200×630) generated for social-share preview using the same Riyadh skyline SVG primitives from the hero.

**Deviations from the design prototype (deliberate):**
- Portrait placeholder removed from About section; About grid reverts to its 2-column base (1.3fr bio + 1fr stats).
- "Recently · SAR 20M / mo throughput" stat-row removed from About stats rail.
- Mobile hamburger drawer added (the prototype hides nav at <760px with no replacement).
- Tweaks panel and all variant alternates removed.
- "Designed to travel" iPhone mockup strip removed (user's explicit feedback in the design chat).
- Field Notes empty-state email capture form removed (user opted out of half-built email collection).

**Analytics:** None embedded in the page. Cloudflare's proxy auto-injects Web Analytics when the site is orange-clouded — no token or script tag in source.

## Testing Decisions

**What makes a good test here:** test external behaviour through the module's public interface; never assert on implementation details (class names, internal state shape, render counts). A test that breaks when I refactor the internals but keep the same outward contract is a bad test. Snapshot tests are avoided except for the content schema validation (which is effectively a structural assertion, not a UI snapshot).

**Module test plans:**

- **`lib/content`** — One completeness check that walks every exported content tree and asserts every `en` field has a non-empty matching `ar` field (and vice versa). One shape test per module that fails if the structure regresses (e.g. a Service is missing `name` or `description`). Type-level tests via TypeScript itself for the rest.

- **`lib/i18n`** — Behavioural tests on `<T>`: (1) with the flag off, `<T en="Hello" ar="مرحبا" />` always renders `Hello` regardless of any ambient `lang` attribute; (2) with the flag on and `lang="en"`, renders `Hello`; (3) with the flag on and `lang="ar"`, renders `مرحبا`. Plus a test that `pickLocale` returns the right string from `{ en, ar }` objects. The flag-off behaviour is the most important — it's the regression guard that prevents accidental Arabic shipping when AR is disabled.

- **`lib/theme`** — Behavioural tests on `useTheme()`: (1) initial value reads from localStorage; (2) setting a value writes to localStorage; (3) when value is `system`, the resolved attribute follows `matchMedia('(prefers-color-scheme: dark)')`; (4) when `system` and the OS theme changes, the resolved attribute updates without a reload. Tests use jsdom + a mocked `localStorage` and `matchMedia`.

- **`lib/lang`** — Same shape as `lib/theme` tests, plus: when `isArabicEnabled` is `false`, every export is inert (calling `useLang()` returns a fixed `en` value and never writes localStorage).

**Out of test scope:** UI components, route composition, the image build pipeline. The image script is verified by checking output files exist in the build artifact, not by unit-testing `sharp`.

**Prior art:** None — this is the first code in the repo. The four lib modules will set the testing pattern for everything that comes after.

**Tooling:** Vitest (faster than Jest, native TS, ESM-first) + `@testing-library/react` for the `<T>` component test. jsdom environment.

## Out of Scope

- **Individual blog post page** (`/blog/[slug]`). Will be added when the first real post is written, alongside the MDX/markdown pipeline decision.
- **Email capture backend.** The "subscribe" form in the Field Notes empty state is removed from this build; if/when a newsletter service is chosen, it gets wired in later.
- **Tweaks panel** and all alternate layout variants (other hero layouts, services layouts, projects layouts, experience layouts, type pairings, tone variants). Selected variants are committed; alternates removed.
- **API routes / server backend.** Static export only.
- **Analytics script tag.** Cloudflare proxy handles it.
- **Authentication, admin UI, CMS.** Content lives in TypeScript modules edited by hand.
- **Multi-locale beyond EN/AR.** The `<T>` component is two-prop; if a third locale is needed later, it'll be a refactor.
- **Real-time availability changes.** Hardcoded constant, redeploy to change.
- **Portrait photo asset.** Removed by user request.
- **iPhone mockup strip / "Designed to travel" section.** Removed by user request.
- **Dynamic Open Graph image per page.** One static `og.png` for the whole site.

## Further Notes

- The site will be proxied through Cloudflare (orange-cloud DNS), so Cloudflare Web Analytics gets auto-injected without a code change.
- Domain target: `abuabdillahi.com` (root). Subdomains `limber.`, `chargraph.`, `catpik.` are already live with the project demos and remain independent.
- Copy edits are expected after the first build lands; user will sweep through the EN strings post-implementation. The structure is designed to make that a one-file edit per section.
- Arabic translations in the design were authored by the design assistant, not a native speaker. A native review is needed before flipping `NEXT_PUBLIC_ENABLE_ARABIC=true` in production.
- All design imagery (company covers, logos, project screenshots) is taken verbatim from the design bundle's `project/assets/` directory.
- The five locked-variant choices come from a separate "tweaks-off" build of the design (`PUVyB6PZPAbRmtYr5fgQ8A`) that the user pinned as the canonical final look.
