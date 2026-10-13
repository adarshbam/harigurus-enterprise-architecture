# 02 — Next.js SSR E-Commerce Storefront (`harigurus-shop`)

## Ownership & Scope
- **Repository:** `harigurus/harigurus-shop` (1,343 LOC | 63 Files)
- **Engineering Attribution:** **15 out of 15 commits (100% sole-authored by Adarsh Singh)**

---

## Technical Architecture
1. **Server-Side Rendering (`getServerSideProps`):**
   - Dynamic routes `pages/products/[slug].js` and `pages/[slug].js` resolve product slugs on the server to guarantee full search engine crawlability and OpenGraph metadata population.
2. **Sanity E-Commerce Schema Design (`sanity/schemas/product.js` & `categories.js`):**
   - Modeled product documents with title, slug, multi-image array, INR price, rating, review count, seller attribution, and relational category references.
3. **Localization & UI System:**
   - Implemented `Intl.NumberFormat('en-IN', { style: 'currency', currency: 'INR' })` formatting.
   - Combined **Material-UI v5 (`@mui/material`)** components with **TailwindCSS** utility classes for responsive mobile-first shopping grids (`CategoriesList.js`, `ProductsList.js`, `ProductDetails.js`).
