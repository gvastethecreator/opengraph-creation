---
name: opengraph-creation
description: "Create, integrate, or repair Open Graph images and social link previews, using a project-owned generation pipeline and page metadata."
---

# Open Graph Creation

Deliver the requested image, metadata change, integration, or diagnosis. Use the site's existing brand and rendering stack. Generate and serve the images through code and assets owned by the project. This skill builds our own system; it does not integrate hosted card-generation services.

## 1. Identify the work

Read the affected public page, metadata owner, image assets, canonical URL configuration, and relevant package versions. Locate existing image routes, framework file conventions, CMS metadata, and plugins before adding another owner.

Choose the route that fits the request:

- **Image only:** produce the requested asset and an accurate preview; integrate it only when requested.
- **Metadata repair:** fix the affected metadata or image URL; an existing usable image does not need a new renderer.
- **Per-page images or a new system:** build or extend the project-owned pipeline in [generation.md](references/generation.md). Reuse the installed native renderer or build tools. Read only the relevant section of [stacks.md](references/stacks.md). Keep the generated assets on the project's own origin or asset domain. Replace an existing external renderer within the affected scope once the new path is ready.

Derive ordinary design choices from the site. Ask only for a missing fact that changes the output, such as an unknown production origin needed for integration. Continue independent local work while required deployment configuration is missing. Done when the scope, implementation owner, page data, and delivery path are clear.

## 2. Design the card

Use the actual logo, fonts, colors, and relevant page content. A 1200 x 630 canvas is a practical default for a landscape share card, not an Open Graph protocol requirement. Follow explicit platform or campaign dimensions when supplied.

A 1200 x 630 PNG or JPEG under 5 MB fits the documented limits of both large consumers:

| Consumer | Documented limits |
|---|---|
| [Meta](https://developers.facebook.com/docs/sharing/webmasters/images) | Minimum 200 x 200; at least 600 x 315 for the large link preview; 1200 x 630 recommended; at most 8 MB; keep close to 1.91:1 to avoid cropping |
| [LinkedIn](https://www.linkedin.com/help/linkedin/answer/a521928) | Minimum 1200 x 627; at most 5 MB; 1.91:1 recommended; under 401 px wide renders as a small thumbnail |

Check X's current card documentation before promising its limits; X may crop differently.

Give the card a clear focal point and headline. Preserve the page's distinguishing meaning when shortening copy; strip only a known brand suffix, not every phrase separated by punctuation. Empty descriptions may be omitted. Match the content's language and ensure glyph coverage.

Measure the rendered result. Character counts alone do not predict wrapping across fonts, scripts, or long words. Fix clipping by editing copy, spacing, line count, or type size within a readable range. `overflow: hidden` can constrain the canvas but is not evidence that the text fits. Inspect the full image and a thumbnail view.

Keep SVG `viewBox`, `<defs>`, gradient, mask, and clip-path references intact. Use trusted local SVGs or sanitize untrusted markup through the project's existing boundary. Do not flatten a logo by deleting definitions. Wait for fonts and images before capturing a browser-rendered card. Export a format supported by the intended consumers; PNG or JPEG is a common choice, and a source SVG is not a universally supported final card.

Done when the asset is legible, branded, dimensionally correct, and visually inspected, or the missing rendering capability is stated.

## 3. Wire page metadata

Reuse the canonical page data for title, description, image, and URL. Resolve public absolute HTTPS URLs from a configured canonical origin and the route's public identity, never an untrusted request host. Preserve locale or content parameters only when they are part of that identity; omit fragments, tracking, and render-only parameters from `og:url`.

For a shared primary image, adapt this structure through the framework's metadata API or escaped server template:

```html
<meta property="og:title" content="PAGE_TITLE">
<meta property="og:type" content="website">
<meta property="og:url" content="CANONICAL_PAGE_URL">
<meta property="og:description" content="PAGE_DESCRIPTION">
<meta property="og:image" content="ABSOLUTE_IMAGE_URL">
<meta property="og:image:type" content="ACTUAL_IMAGE_MIME_TYPE">
<meta property="og:image:width" content="ACTUAL_WIDTH">
<meta property="og:image:height" content="ACTUAL_HEIGHT">
<meta property="og:image:alt" content="DESCRIPTION_OF_THE_IMAGE">
<meta name="twitter:card" content="summary_large_image">
<meta name="twitter:title" content="PAGE_TITLE">
<meta name="twitter:description" content="PAGE_DESCRIPTION">
<meta name="twitter:image" content="ABSOLUTE_IMAGE_URL">
<meta name="twitter:image:alt" content="DESCRIPTION_OF_THE_IMAGE">
```

LinkedIn requires `og:title`, `og:image`, `og:description`, and `og:url`. Meta asks for `og:image:width` and `og:image:height` so the image loads properly the first time the page is shared. Choose `og:type` for the actual content. Metadata values and dimensions must describe the real asset. Use a single metadata owner per route to avoid accidental conflicts. Open Graph allows ordered image arrays: preserve intentional variants and keep each image's structured properties next to it. X/Twitter may use a deliberately different crop.

Emit crawler-readable metadata in the served HTML or the framework's supported bot response. Hydration-only head updates do not establish per-route social previews. Escape text and attributes once at their output boundary; do not blindly unescape already-rendered HTML or inject CMS content as raw markup.

Done when the affected routes have correct, non-conflicting metadata and no private values in public image URLs.

## 4. Verify the delivery path

At the final editing boundary, inspect existing coverage and select the checks that prove the changed behavior:

- Fetch the affected page as a social crawler without cookies or authentication. Inspect its HTML, canonical URL, image tags, status, and redirects. For route-specific cards, check that two distinct pages do not share stale page data. For example: `curl -sSL -A "facebookexternalhit/1.1" -D headers.txt -o page.html "<page-url>"`, then read the `og:` and `twitter:` tags in `page.html`. A `robots.txt` rule can block a crawler; Meta may cache `robots.txt` for up to 24 hours.
- Fetch and decode the image. Check status, image MIME type, actual pixel dimensions, byte size against the limits above, and redirect destination. A `.png` suffix does not prove the response is an image. For example: `curl -sSL -D image-headers.txt -o card.bin "<image-url>"`, then decode `card.bin` with an image library to read its format and size.
- Inspect the rendered card using representative content and the longest or otherwise problematic supplied title. Check logo integrity, loaded fonts, contrast, glyphs, and clipping. A local HTML preview does not prove the exported image or production renderer is correct.
- Exercise the changed failure class: absent data, a rejected URL, missing generation configuration, or an image failure. Use existing tests where they can observe that behavior; do not add a runner or clear global test environment variables.
- If live access or deployment is outside this task, record those checks as pending. Do not submit private URLs to external validators. Image/CDN cache and social-platform cache are separate. Meta caches images by URL and does not update them until the URL changes, so a changed card needs a new image URL from the existing pipeline. The [Meta Sharing Debugger](https://developers.facebook.com/tools/debug) re-scrapes a public page when authorized; LinkedIn's Post Inspector shows what LinkedIn reads.

Done when each claim has its matching local, rendered, or live evidence. Report the files or artifact, checks completed, skipped checks and their reason, and any concrete remaining configuration or deployment step. Local validation is not platform acceptance.

## Sources

Use the [Open Graph protocol](https://ogp.me/) for required properties, image descriptions, and ordered arrays. Check the selected framework and social consumer's current documentation for implementation details and limits. The linked stack and generation references contain the relevant starting points.

Regression prompts in `evals/` support skill maintenance; they are not extra steps in ordinary card work.
