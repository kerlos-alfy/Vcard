<!-- Copilot / AI agent instructions for the Vcard repo -->
# Copilot Instructions — Vcard

Purpose
- Help AI agents understand the small static VCard site and be productive quickly.

Big picture
- This is a single-page, static vCard served from `index.html` (vanilla HTML/CSS/JS). The UI, data, and behavior are implemented inline in `index.html`.
- There is a `package.json` present with `next` scripts; the repo currently appears to be a static site, not a Next.js app. If maintainers intend to run a Next.js workflow, confirm and migrate `index.html` into a Next project structure first.

Key files and locations
- `index.html`: single source of truth — styling, DOM, scripts, vCard data, QR generation and download logic live here.
- `package.json`: contains `dev/build/start` (Next.js) scripts. Treat as template/legacy unless the maintainer confirms Next usage.
- `assets/images/` and `assets/cards/`: media used by the card and QR overlay. `assets/images/logo.png` is referenced in multiple places and has `onerror` fallbacks.
- `qr/`: (present) inspect if it contains pre-generated QR assets.

Important patterns & conventions (project-specific)
- Data-driven vCard: vCard content is centralized in the `vCardData` object inside `index.html`. Update this object to change contact data across features (vCard file download and QR code).
- Inline single-file approach: styles and behavior are directly embedded in `index.html`. Expect changes to be made inside this file rather than multiple JS/CSS modules.
- External CDN dependencies: fonts (`fonts.googleapis.com`), Font Awesome, and `qrcodejs` are loaded from CDNs; avoid changing runtime assumptions without updating offline or bundling strategy.
- Graceful image fallbacks: `onerror` handlers use `ui-avatars.com` — when adding images, keep the fallback pattern.
- Arabic/English comments: CSS contains Arabic comments and descriptive names — keep them if you edit styles to preserve intent.

Runtime & developer commands
- Static preview (recommended for current layout):
  - Serve locally from repo root: `npx serve .` or `python -m http.server 3000` and open `http://localhost:3000`.
- Package scripts (only if this is intended to be a Next.js project):
  - `npm run dev` (starts Next dev server)
  - `npm run build` then `npm start` (production build)
  - Confirm with maintainers before using these — `index.html` will not be used by default in Next.

Behavioral details agents should know
- vCard download: `downloadVCardWithImage()` converts the configured `photoUrl` to base64 and injects it into a generated `.vcf` file.
- QR generation: `toggleSheet()` lazily instantiates `QRCode` into `#qrcode` using the same minimal vCard content (BEGIN:VCARD..END:VCARD).
- Copy/share: `copyText()` uses `navigator.clipboard` and `showToast()` for feedback; `navigator.share` is used if supported.

What to change and how (examples)
- To update contact details: edit the `vCardData` object at the top of the inline script in `index.html`.
- To add another person card: add a new folder under `assets/images/` and duplicate the card markup & data, or convert the page to a data-driven template (recommended if multiple cards required).
- To replace `qrcodejs` with another QR library: update the `<script>` import and the `new QRCode(...)` call; keep the vCard text format used currently.

Risks, gotchas & notes for agents
- Do not assume a build step — changes to `index.html` take effect immediately when served as static files.
- If maintainers claim this is a Next.js project, avoid editing `index.html` directly; instead, request or create a proper `pages`/`app` structure and migrate assets.
- No test harness or linter configs are present; run local manual checks (serve + browser) after edits.

If you are unsure
- Ask the maintainer whether `package.json`/Next.js is intentional or leftover. That decision changes how you modify/structure the project.

Feedback request
- If anything important is missing or you want the file to document CI, deployment, or branching conventions, tell me what you expect and I'll update this file.
