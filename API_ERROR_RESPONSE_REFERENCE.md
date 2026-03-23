# API Error Response Reference
## Frontend-Facing APIs — 2xx / 4xx / 5xx Status Codes
### Unimetacare-ABDM | March 2026

---

## Overview

All frontend-facing APIs use the following response patterns. **Callback routes** (`/api/callback/*`) are ABDM webhooks — not called by the frontend.

**Standard error body** (from global error handler):
```json
{ "status": false, "message": "<error message>" }
```

**Note:** Some middlewares (`validateAdminKey`, `validateApiKey`, `apiKeyController`) use `success` instead of `status` — see [Inconsistencies](#inconsistencies) section.

---

## 1. `/api/m1/abha` — ABHA Enrollment & Login

### 1.1 POST `/token` — Generate JWT
| Status | When | Response |
|--------|------|----------|
| **200** | Success | `{ status: true, message, data: token, meta: {...} }` |
| **400** | Missing/invalid request body | `{ status: false, message: "API key is required" }` |
| **401** | Invalid/revoked/suspended/expired key | `{ status: false, message: "Invalid API key" }` (or variant) |
| **403** | Admin key used on this endpoint | `{ success: false, message: "Admin API key cannot be used on this endpoint" }` |
| **429** | Rate limit (200 req/min global) | `{ status: false, message: "Too many requests..." }` |
| **500** | Server error | `{ status: false, message: "Internal Server Error" }` |

---

### 1.2 Enrollment — Aadhaar
| Endpoint | 2xx | 4xx | 5xx |
|----------|-----|-----|-----|
| `POST /aadhar/enroll/send-otp` | **200** Success | **400** Invalid Aadhaar, encryption failed; **401** No/invalid JWT; **404** ABDM resource; **422** Invalid request; **429** OTP limit (3/5min) or global | **500** Server; **502** ABHA server error; **503** Network |
| `POST /aadhar/enroll/verify-otp` | **200** Success | Same + **400** Missing txnId/otp/mobile | Same |
| `POST /mobile/enroll/send-otp` | **200** Success | Same | Same |
| `POST /mobile/enroll/verify-otp` | **200** Success | Same | Same |
| `POST /aadhar/enroll/email-verification` | **200** Success | **400** Missing email/token | Same |
| `POST /aadhar/enroll/abha-suggestion` | **200** Success | **400** Missing txnId, etc. | Same |
| `POST /aadhar/enroll/create/abha-address` | **200** Success | **400** Missing txnId, abhaAddress, preferred | Same |
| `POST /aadhar/enroll/get-profile` | **200** Success | **400** | Same |
| `POST /aadhar/enroll/download-card` | **200** Success | **400** | Same |

---

### 1.3 Login
| Endpoint | 2xx | 4xx | 5xx |
|----------|-----|-----|-----|
| `POST /mobile/login/send-otp` | **200** Success | **400, 401, 404, 422, 429** | **500, 502, 503** |
| `POST /mobile/login/verify-otp` | **200** Success | Same | Same |
| `POST /mobile/login/verify-user` | **200** Success | Same | Same |
| `POST /aadhar/login/abha-number/send-otp` | **200** Success | Same | Same |
| `POST /aadhar/login/abha-number/verify-otp` | **200** Success | Same | Same |
| `POST /mobile/login/abha-number/send-otp` | **200** Success | Same | Same |
| `POST /mobile/login/abha-number/verify-otp` | **200** Success | Same | Same |
| `POST /aadhar-number/login/send-otp` | **200** Success | Same | Same |
| `POST /aadhar-number/login/verify-otp` | **200** Success | Same | Same |
| `POST /aadhar/login/abha-address/send-otp` | **200** Success | Same | Same |
| `POST /aadhar/login/abha-address/verify-otp` | **200** Success | Same | Same |
| `POST /aadhar/login/abha-address/fetch/profile` | **200** Success | Same | Same |
| `POST /aadhar/login/abha-address/download/card` | **200** Success | Same | Same |
| `POST /mobile/login/abha-address/send-otp` | **200** Success | Same | Same |
| `POST /mobile/login/abha-address/verify-otp` | **200** Success | Same | Same |
| `POST /login/get-profile` | **200** Success | Same | Same |
| `POST /login/qr-code` | **200** Success | Same | Same |
| `POST /login/download` | **200** Success | Same | Same |

---

### 1.4 Search
| Endpoint | 2xx | 4xx | 5xx |
|----------|-----|-----|-----|
| `POST /search/abha` | **200** Success | **400, 401, 404, 422, 429** | **500, 502, 503** |
| `POST /search/request/otp` | **200** Success | Same | Same |
| `POST /search/verify/otp` | **200** Success | Same | Same |

---

## 2. `/api/m2` — HIP Care Context Linking

| Endpoint | 2xx | 4xx | 5xx |
|----------|-----|-----|-----|
| `POST /link/token-generate` | **200** Success | **400, 401, 404, 422, 429** | **500, 502, 503** |
| `POST /multiple/link-carecontext` | **200** Success | Same | Same |
| `POST /request/hip/on-notify` | **202** Accepted | Same | Same |
| `POST /request/hip/on-request` | **200** Success | Same | Same |
| `POST /notification/push/url` | **200** Success | Same | Same |
| `POST /health-info/notify` | **200** Success | Same | Same |
| `POST /link/deep-link` | **200** Success | Same | Same |

---

## 3. `/api/m3` — Consent & Health Data

| Endpoint | 2xx | 4xx | 5xx |
|----------|-----|-----|-----|
| `POST /create/consent` | **200** Success | **400, 401, 404, 422**; **429** Consent init limit (5/min) | **500, 502, 503** |
| `POST /consent/status` | **200** Success | **400, 404, 422** (no JWT required) | Same |
| `POST /consent/fetch` | **200** Success | **400, 401, 404, 422** | Same |
| `POST /consent/notify` | **200** Success | **400, 404, 422** | Same |
| `POST /consent/health-info/request` | **200** Success | **400, 401, 404, 422** | Same |
| `POST /consent/decrypt` | **200** Success | **400, 401** | Same |
| `POST /consent/response-data` | **200** Success | Same | Same |
| `POST /consent/bundle-data` | **200** Success | Same | Same |
| `POST /consent/records` | **200** Success | Same | Same |

---

## 4. `/api/abdm` — Simplified Wrappers

| Endpoint | 2xx | 4xx | 5xx |
|----------|-----|-----|-----|
| `POST /consent/request` | **200** Success | **400, 401, 404, 422** | **500, 502, 503** |
| `GET /consent/:consentId/records` | **200** Success | **400, 401, 404** | Same |

---

## 5. `/api/fidelius` — ECDH Encryption

| Endpoint | 2xx | 4xx | 5xx |
|----------|-----|-----|-----|
| `GET /generate-keys` | **200** Success | **429** Fidelius limit (20/min) | **500** Server / Fidelius CLI error |
| `POST /encrypt` | **200** Success | **400** Missing params; **429** | Same |
| `POST /decrypt` | **200** Success | **400** Missing encryptedData, nonces, keys; **429** | Same |

---

## 6. `/api/phr` — PHR Enrollment & Login

All PHR endpoints require `isLoggedIn` (JWT).

| Category | Endpoints | 2xx | 4xx | 5xx |
|----------|------------|-----|-----|-----|
| **Data** | `POST /data/encrypt` | **200** | **400, 401, 429** | **500** |
| **Enrollment — Aadhaar linked** | `POST /abha/enroll/aadhaar/link/mobile/request/otp` | **200** | **400, 401, 404, 422, 429** | **500, 502, 503** |
| **Enrollment — Mobile** | `POST /mobile/enrollment/request/otp`, `verify/otp`, `abha-address/suggestions`, etc. | **200** | Same | Same |
| **Enrollment — ABHA number** | `POST /abha/enrollment/mobile/otp/request`, `verify`, etc. | **200** | Same | Same |
| **Enrollment — Aadhaar** | `POST /abha/enrollment/aadhaar/otp/request`, `verify`, etc. | **200** | Same | Same |
| **Login — Mobile** | `POST /login/mobile/request/otp`, `verify/otp`, `verify/user` | **200** | Same | Same |
| **Login — Email** | `POST /login/email/request/otp`, `verify/otp`, `verify/user` | **200** | Same | Same |
| **Login — ABHA address** | `POST /login/abha-address/request/otp`, `abha/address/login/verify/otp` | **200** | Same | Same |
| **Login — ABHA number (Aadhaar)** | `POST /login/abha-number/aadhaar/request/otp`, etc. | **200** | Same | Same |
| **Login — ABHA number (Mobile)** | `POST /login/abha-number/mobile/request/otp`, etc. | **200** | Same | Same |
| **Login — Password** | `POST /login/abha-address/mobile/search`, `verify/password` | **200** | Same | Same |
| **Login — Aadhaar number** | `POST /login/aadhaar-number/mobile/request/otp`, etc. | **200** | Same | Same |
| **Profile** | `POST /login/get/profile`, `update/profile`, `get/QR-Code`, etc. | **200** | Same | Same |
| **Other** | `POST /login/switch/profile`, `logout`, `login/refresh/token` | **200** | Same | Same |

---

## 7. `/api/admin/keys` — API Key Management (Admin)

Uses `x-api-key` header with `API_KEY` env. Not typically called from Unilink-EMR frontend.

| Endpoint | 2xx | 4xx | 5xx |
|----------|-----|-----|-----|
| `POST /` | **201** Created | **400** Missing/invalid body; **401** Missing key; **403** Invalid key; **409** Duplicate phone | **500** Misconfig |
| `GET /` | **200** Success | **401, 403** | **500** |
| `GET /:keyId/logs` | **200** Success | **401, 403, 404** | **500** |
| `PATCH /:keyId/revoke` | **200** Success | **401, 403, 404, 400** (already revoked) | **500** |

**Admin error format:** `{ success: false, message: "..." }` (different from global `status`).

---

## 8. Standard 4xx / 5xx Codes

| Code | Source | Example Message |
|------|--------|-----------------|
| **400** | BadRequestError, validation | "Valid 12-digit Aadhaar number is required" |
| **401** | UnauthorizedError, isLoggedIn | "Authorization token is missing or malformed." |
| **401** | validateAdminKey / validateApiKey | "Missing x-api-key header" / "Invalid API key" |
| **403** | validateAdminKey / blockAdminKey | "Invalid API key" / "Admin API key cannot be used on this endpoint" |
| **403** | validateApiKey | "API key has been revoked" / "suspended" / "expired" |
| **404** | NotFoundError | "Resource not found" |
| **409** | apiKeyController | "An active API key already exists for this phone number" |
| **422** | handleAbhaError (ABDM 422) | "Invalid or expired OTP" |
| **429** | Rate limiters | "Too many requests..." / "Too many OTP requests..." |
| **500** | DatabaseError, unhandled | "Internal Server Error" |
| **502** | handleAbhaError (ABDM 5xx) | "ABHA server error. Please try again later." |
| **503** | handleAbhaError (network) | "Network error. Please check your connection." |

---

## Inconsistencies

| Location | Uses | Should align with |
|----------|------|-------------------|
| Global error handler (app.js) | `status: false` | — (canonical) |
| verifyAbdmSignature | `status: false` | ✅ |
| Rate limiters | `status: false` | ✅ |
| validateAdminKey | `success: false` | ❌ → `status: false` |
| validateApiKey | `success: false` | ❌ → `status: false` |
| apiKeyController | `success: true/false` | ❌ → `status` for consistency |
| abhaM1Controller success | `success` or `status` | Mixed in codebase |

**Recommendation:** Standardize all error responses to `{ status: false, message }` (and success to `{ status: true, ... }`) so the frontend can rely on a single schema.

---

## PHR Routes Missing catchAsync

The following PHR routes do **not** use `catchAsync`. Unhandled promise rejections may not reach the global error handler correctly:

- `/login/mobile/request/otp` through `/logout`
- Most routes from line 149 onward in `phrEnrollmentRoute.js`

**Recommendation:** Wrap all async PHR handlers with `catchAsync` for consistent error handling.

---

*Document generated: March 2026*
