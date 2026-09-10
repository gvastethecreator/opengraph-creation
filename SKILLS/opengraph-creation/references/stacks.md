# Stack Integration

Read only the relevant stack. Inspect the installed version and existing metadata owner before applying a current documentation example. Keep canonical page data, card content, and metadata connected to the same route record.

## Next.js

For App Router, inspect `metadata`, `generateMetadata`, `metadataBase`, and `opengraph-image.*` / `twitter-image.*` in the affected route and its parents. File-based metadata can override config-based metadata. Keep an existing native generator when it fits; repair its content or assets at the owning segment.

Use a static metadata image file or `ImageResponse` through the native image route when appropriate. Check the installed version's handling of `params`, runtime, caching, fonts, and supported CSS. Browser CSS support does not imply support in an image renderer. For Pages Router, use its existing head integration rather than copying App Router APIs.

Page-specific content belongs with the route or a shared data function used by that route and its image generator. Do not assume a root layout receives every page's title, search parameters, or current pathname. When replacing an existing renderer, update only the file convention that owns the affected route and remove the obsolete implementation within that scope.

- [Image file conventions](https://nextjs.org/docs/app/api-reference/file-conventions/metadata/opengraph-image).
- [generateMetadata and precedence](https://nextjs.org/docs/app/api-reference/functions/generate-metadata).
- [ImageResponse](https://nextjs.org/docs/app/api-reference/functions/image-response).

## Astro and static generators

Use the existing layout or head partial and the configured site/base URL. For Astro, require a configured `Astro.site` when deriving a production canonical URL. Use explicit public route data, including locale when relevant. Build-time environment values and content changes require a rebuild; a runtime environment variable cannot update already-generated HTML.

For Hugo, inspect `.Permalink` and `baseURL`; for Jekyll, inspect `absolute_url`, `url`, and `baseurl`; for Eleventy, use the project's URL filter and page data. Account for base paths, trailing slashes, and localized routes. Existing SEO plugins may already emit image metadata. A generated static image avoids adding a request-time service when the build already knows the page content.

- [Astro configuration](https://docs.astro.build/en/reference/configuration-reference/).

## Rails and Django

Use the server layout's existing title, description, and canonical helpers. Keep URL construction in one helper that uses a URL parser and encoder; string concatenation breaks existing queries, fragments, and cache-version values. Render through normal ERB or Django escaping. Do not globally clear environment variables in test setup; scope configuration changes to the relevant test and restore them.

For development preview, use `Rails.env.development?` or the project's explicit Django development setting. Django's template `debug` value depends on context processor and request configuration; it is not a universal development flag.

Use a trusted asset pipeline for logos. Reading and inserting a local SVG does not justify treating arbitrary uploads as safe HTML. Preserve SVG definitions and explicit sizing. Inertia integrations must account for their actual SSR/head owner and avoid duplicate tags from client components.

## Laravel

Use Blade and the project's metadata owner. Render cards through an existing local image library or an explicit build command, store them in the public asset pipeline, and pass their URLs and real dimensions into the layout. Reuse the canonical route record for page and card content.

If the project needs request-time generation, resolve a public content identifier on the server and bound rendering work. Serve a typed image response with the chosen cache policy. A browser-based renderer should run in a controlled worker or build environment; do not create an endpoint that navigates arbitrary request URLs.

## Express, Fastify, Hono, and other server templates

Use the actual framework's view-data interface; `req.query` and `res.locals` are Express patterns, not shared APIs across these frameworks. Resolve canonical URLs from configured origin plus public route data, and use the language's URL parser and query encoder. Development preview must require an explicit development environment, not merely an environment that is not production.

## Client-rendered applications

Inspect the HTML returned for each route without JavaScript. A shared `index.html` may support a single site card, but hydration-only metadata cannot establish distinct route previews. Use the existing SSR/prerender path if available. If per-route server output is absent, state that architectural requirement before proposing wider application changes. Generating an image does not repair missing crawler-visible page metadata.
