# 03 — Multi-Stage OTP Checkout & Express Backend Architecture

## Overview
The Harigurus booking flow (`Checkout.js` — 940 lines) and its backing REST API (`harigurusBackend`) handle real-world ceremony scheduling, user verification, and order persistence.

---

## Checkout & Authentication Pipeline

```mermaid
sequenceDiagram
    participant U as Customer Browser (Checkout.js)
    participant API as Express API (harigurusBackend)
    participant SMS as Fast2SMS / Twilio Gateway
    participant DB as MongoDB Atlas (harigurus)

    U->>U: Validate Indian Mobile (validator en-IN)
    U->>API: POST /otp { mobile, order: true }
    API->>DB: Upsert 4-Digit OTP in OTP Collection
    API->>SMS: Dispatch SMS OTP
    U->>U: Auto-trigger on 4th OTP digit typed
    U->>API: POST /login-with-order { userDetails, otp }
    API->>DB: Verify OTP & Find/Create User
    API-->>U: Return Signed JWT Token
    U->>API: POST /order (Header: Auth JWT)
    API->>DB: Save Order & Push Reference to User.orders
    API-->>U: 201 Order Confirmed
```

---

## Key Backend Security & Persistence Features
- **Password & OTP Hybrid Auth (`controllers/user-controllers.js`):**
  - Supports both passwordless 4-digit SMS OTP login and `bcryptjs` (12 salt rounds) password authentication, plus **Google OAuth 2.0** (`passport-google-oauth20`).
- **Production Database Isolation (`commit 7d64da3`):**
  - Separated development and live customer databases on MongoDB Atlas (`harigurus`) with strict Mongoose schema validation (`mongoose-unique-validator`).
