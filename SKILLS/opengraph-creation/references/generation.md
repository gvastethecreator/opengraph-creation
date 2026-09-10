# Project-Owned Image Generation

Read when creating or changing the card generation system. Reuse the repository's package manager, assets, renderer, storage, and build/deploy contracts. The result consists of editable source, generated images or an image endpoint, and metadata that selects the correct card for each public page.

## Choose the execution model

- **Build-time generation:** prefer this when the build already knows the public pages and their content. Render one image per content/template version and publish the images with the matching HTML. A single-page site may need only one generated asset.
- **Request-time generation:** use this when public content changes independently of builds or the existing framework already owns a suitable image route. Resolve a public content identifier, render on our server, and cache by the content and template version. Do not add a separate service when a native route is sufficient.

For a browser renderer, use the installed browser engine or supported automation tools, with fixed dimensions and an explicit font/image readiness condition. Keep browser work in the build or a controlled server worker. For native image APIs, use their supported CSS, font formats, and runtime. Select the renderer from project evidence; a skill should not force a new dependency on every site.

Done when the execution model matches content freshness and the deployment runtime, and its dependencies are declared in the target repository.

## Define the input contract

Use one public page record for both metadata and card content. The minimum useful inputs are the canonical page identity, locale, headline, optional description, selected template, and versioned brand assets/fonts. Keep optional visual inputs tied to real product content; do not invent badges, claims, or logos to fill space.

- Build canonical identity from the configured production HTTPS origin and the eligible public route. A URL parser with a base still accepts another absolute origin; validate the resulting origin and reject embedded credentials.
- Preserve only known public query keys and values needed for that content, such as the supported locale. Remove fragments and known tracking values. Reject unknown or sensitive parameters and ineligible routes rather than sanitizing a private link into a public record.
- Resolve content from the project's public registry or data source. Do not accept an arbitrary page URL to screenshot, remote asset URL to fetch, filesystem path, or HTML document from an unauthenticated request.
- Treat text as data at the renderer's escaping boundary. Use trusted asset identifiers or local files from the declared asset root. Handle unsupported glyphs and absent optional descriptions explicitly.

A malformed record is a visible build error or an explicit endpoint error, following the project's error contract. Do not silently serve an unrelated generic card to conceal failed generation. Keep the earlier working integration active until a requested replacement is ready, then remove its obsolete path.

## Render and publish consistently

Separate the page-data adapter, reusable card template, renderer, and metadata integration only as far as the project needs. Small sites can keep these concerns in a compact module and a build command; avoid scaffolding a service, queue, or database without a concrete requirement.

Define image identity from all visual inputs: normalized content, locale, template version, fonts, and brand assets. Use stable serialization and the existing content-hash convention. Do not hash only the route if the text or template can change. A path such as `/og/<content-hash>.png` is an example, not a required naming scheme.

For build output, render to a temporary destination, verify the image, then publish it and its page-to-image mapping together. A partial render must not replace the previous usable output or leave HTML pointing to a nonexistent image. Keep generated output outside source assets. Remove superseded files only when the actual deployment/cache retention contract makes that safe.

For an endpoint, bound input size and rendering time, and use the framework's existing cache and concurrency controls. Return a real image response with its MIME type and appropriate caching. Cache immutable image identities for reuse; define revalidation for mutable URLs. An unknown public identifier or renderer failure must not become a cached success response.

Metadata uses the generated image URL and actual width, height, type, and alt text. `og:url` remains the page's canonical identity; image versions do not change it. Keep shared data route-specific so a parent layout does not put the home page's card on every article.

## Done conditions

The target project has an executable generation command or native endpoint, reusable template source, declared local assets/fonts, and metadata wiring. At closeout:

1. Generate a real card using the production renderer and inspect the exported image, not just a browser approximation of the template.
2. Check the affected page's crawler-visible metadata and retrieve its image without cookies. Compare dimensions and MIME type with the actual decoded file.
3. If card identity or data selection changed, verify that different content or template inputs update the card and unchanged inputs keep a stable identity. Inspect existing coverage before adding a focused regression.
4. Exercise the relevant failure path, such as invalid public input, missing required font, or interrupted build, at the layer that can observe it. Do not duplicate proof across helper and end-to-end tests.
5. Document the generation command and the template/content/asset locations in the project's existing documentation. Report local proof separately from deployment and social-platform acceptance.

Do not claim live integration without a target project and its actual deployment evidence. A reusable skill definition describes how to build the system; it is not itself a deployed image service.
