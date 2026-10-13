# 01 — Sanity.io Headless CMS Migration Architecture (`harigurus`)

## Context & Engineering Problem
In the initial version of the `harigurus` production application (20,170+ LOC), all ceremony definitions, descriptions, pricing tiers (`priceWithArticles` vs. `priceWithoutArticles`), and category groupings were stored inside a monolithic 670-line JavaScript file (`src/Data/AllServicesList.js`).

Whenever the operations team needed to update ceremony pricing, fix a description, or launch a seasonal festival package, an engineer had to manually modify the JavaScript bundle and trigger a full production rebuild.

---

## Solution Architected (`integrate-sanity` Branch)
I designed and implemented a **Headless CMS architecture** using **Sanity.io** (`commits 300821f, c4ee4d1, 23b886c`):

1. **Custom Content Schemas (`sanity/schemas/`):**
   - `services.js`: Top-level ceremony categories (Purohithya, Astrology, Cook Services, Pooja Services).
   - `serviceitem.js`: Individual ceremony packages with rich-text procedures, dual pricing models (with/without Pooja Samagri articles), slug generation, and image references.
2. **Client & Image Pipeline (`src/utils/client.js` & `src/utils/image.js`):**
   - Configured `@sanity/client` with CDN edge caching enabled (`useCdn: true`) for sub-50ms read latencies.
   - Built an image URL builder wrapper around `@sanity/image-url` for automatic WebP negotiation and responsive cropping.
3. **Dynamic Route Refactoring (`src/pages/serviceDetails/ServicesDetails.js`):**
   - Replaced synchronous static array lookups with asynchronous GROQ queries keyed by route parameters.
