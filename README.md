# 🏛️ Harigurus — Commercial Production Engineering Case Study (8 Months Tenure)

[![Live Production](https://img.shields.io/badge/Live_Production-https%3A%2F%2Fharigurus.com-FF6B00?style=for-the-badge&logo=googlechrome&logoColor=white)](https://harigurus.com)
[![Next.js SSR](https://img.shields.io/badge/Next.js_SSR-000000?style=for-the-badge&logo=nextdotjs&logoColor=white)](https://nextjs.org)
[![React](https://img.shields.io/badge/React-61DAFB?style=for-the-badge&logo=react&logoColor=black)](https://react.dev)
[![Sanity CMS](https://img.shields.io/badge/Sanity_Headless_CMS-F03E2F?style=for-the-badge&logo=sanity&logoColor=white)](https://www.sanity.io)
[![Node.js & Express](https://img.shields.io/badge/Node.js_%26_Express-339933?style=for-the-badge&logo=nodedotjs&logoColor=white)](https://nodejs.org)
[![MongoDB](https://img.shields.io/badge/MongoDB_Atlas-47A248?style=for-the-badge&logo=mongodb&logoColor=white)](https://mongodb.com)

> **Role:** Full-Stack Software Engineer (8 Months Commercial Production Tenure)  
> **Codebase Scale:** 3-Repository Production Ecosystem (`harigurus`, `harigurus-shop`, `harigurusBackend` — **22,160+ Lines of Code**)  
> **Live Platform:** [https://harigurus.com](https://harigurus.com)  
> *Note: To respect client intellectual property, the raw production repositories remain private under the `harigurus` organization. All commits are cryptographically linked and verified on my GitHub contribution graph. This repository documents the system architecture, engineering migrations, and sanitized code patterns I delivered.*

---

## 🧭 System Architecture Overview

Harigurus is an enterprise booking and e-commerce platform for traditional religious ceremonies, astrology consultations, and ceremonial merchandise (`PoojaSamagri`). The production architecture spans **three decoupled repositories**:

```mermaid
flowchart TD
    subgraph ClientTier ["Client & Edge Delivery Tier"]
        MainApp["harigurus (Core React SPA - 20,170 LOC)"]
        ShopApp["harigurus-shop (Next.js SSR Storefront - 1,343 LOC)"]
    end

    subgraph ContentAndAPITier ["Headless CMS & Transaction API Tier"]
        SanityCMS["Sanity.io Headless CMS & GROQ Engine"]
        ExpressAPI["harigurusBackend (Node.js + Express REST API)"]
    end

    subgraph DataAndThirdParty ["Persistence & Telecom Gateways"]
        MongoAtlas[("MongoDB Atlas Cluster")]
        SMSGateway["Fast2SMS & Twilio OTP Gateways"]
        EmailGateway["EmailJS & PhonePe QR Payment Flow"]
    end

    MainApp -->|"Live GROQ Queries"| SanityCMS
    ShopApp -->|"SSR getServerSideProps + GROQ"| SanityCMS
    MainApp -->|"POST /otp, /login-with-order, /order"| ExpressAPI
    ExpressAPI -->|"Mongoose Models"| MongoAtlas
    ExpressAPI -->|"4-Digit Mobile OTP"| SMSGateway
    MainApp -->|"Order Receipts"| EmailGateway
```

---

## 🛠️ Key Engineering Contributions & Production Deliverables

### 1. Sole-Architected the Next.js SSR E-Commerce Storefront (`harigurus-shop`)
- **Ownership:** Authored **15 out of 15 commits (100% repository ownership)** across 63 files from initial architecture to production deployment.
- **Problem Solved:** Customers booking ceremonies needed a fast, SEO-indexed storefront to purchase ceremonial kits and articles (`PoojaSamagri`) with dynamic category filtering and Indian Rupee (`INR`) localization.
- **Technical Implementation:**
  - Built Server-Side Rendered (SSR) dynamic product routes (`pages/products/[slug].js` and `pages/[slug].js`) using Next.js `getServerSideProps` to ensure instant SEO indexing and zero layout shift.
  - Designed custom Sanity.io content schemas (`sanity/schemas/product.js` and `categories.js`) supporting multi-image galleries, SKU metadata, seller attribution, and dynamic category references.
  - Implemented `Intl.NumberFormat('en-IN', { style: 'currency', currency: 'INR' })` formatting pipeline and responsive Material-UI v5 + TailwindCSS product grids (`CategoriesList.js`, `ProductsList.js`, `ProductDetails.js`).

```javascript
// Sanitized Production Pattern: Dynamic SSR Product Resolution (pages/products/[slug].js)
export async function getServerSideProps(context) {
  return {
    props: { slug: context.params.slug },
  };
}

// GROQ Query fetching live product document by slug from Sanity CDN
const product = await client.fetch(
  `*[_type == "product" && slug.current == $slug][0]`,
  { slug }
);
```

---

### 2. Headless CMS Migration on Core Platform (`harigurus` — `integrate-sanity` Branch)
- **Verified Commits:** `300821f` (*Integrate Sanity*), `c4ee4d1` (*Integrated CMS*), `23b886c` (*Made Service Details completely dynamic*).
- **Problem Solved:** Originally, all ceremony descriptions, pricing tiers (`priceWithArticles` vs. `priceWithoutArticles`), and schedules were hardcoded inside a 670-line static file (`src/Data/AllServicesList.js`). Every minor price or text change required an engineer to edit code and redeploy the entire React application.
- **Technical Implementation:**
  - Architected the Sanity Studio workspace (`sanity/schemas/services.js`, `serviceitem.js`, `purohityas.js`) inside the core repository.
  - Refactored `ServicesDetails.js` (251 lines), `AllServices.js`, and `Services.js` to dynamically query Sanity's content lake via GROQ and resolve image assets via `@sanity/image-url`.
  - Enabled non-technical operations staff to publish new ceremonies and adjust pricing in real time with **zero engineering downtime**.

---

### 3. Multi-Stage OTP Checkout & Production Database Cutover (`harigurusBackend` & `Checkout.js`)
- **Verified Commits:** `7d64da3` (*Production DB cutover to harigurus*), `7207419` (*Storefront navigation integration*).
- **Architecture Highlights:**
  - **940-Line Checkout State Machine (`src/pages/Checkout/Checkout.js`):**
    - Validates Indian mobile numbers on the client (`validator/lib/isMobilePhone` with `en-IN` locale).
    - Dispatches a 4-digit SMS OTP (`POST /otp`) via **Fast2SMS / Twilio** and auto-triggers inline verification (`POST /login-with-order`) as soon as the 4th digit is typed.
    - Dynamically recalculates ceremony pricing based on `Online` vs. `Offline` mode and customizable Pooja Article checklists (`PoojaArticles.js`).
  - **Express + MongoDB Backend (`harigurusBackend`):**
    - JWT token issuance (`jsonwebtoken`), `bcryptjs` password hashing (cost factor 12), and Google OAuth 2.0 (`passport-google-oauth20`).
    - Relational Mongoose order linking (`User.findById(userId).populate('orders')`) and environment-isolated MongoDB Atlas clusters for development vs. production traffic.

---

## 📂 Deep-Dive Technical Documentation

- 📄 [**01 — Sanity.io Headless CMS Migration Architecture**](./docs/01-SANITY-HEADLESS-CMS-MIGRATION.md)
- 📄 [**02 — Next.js SSR E-Commerce Storefront (`harigurus-shop`)**](./docs/02-NEXTJS-SSR-ECOMMERCE-SHOP.md)
- 📄 [**03 — Multi-Stage OTP Checkout & Express Backend Architecture**](./docs/03-OTP-CHECKOUT-AND-BACKEND-CUTOVER.md)
