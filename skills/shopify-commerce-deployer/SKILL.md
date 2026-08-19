---
name: shopify-commerce-deployer
description: Production Shopify deployment and CJ integration skill using HYBRID_CJ_API_APP mode. Shopify API owns storefront/product/theme/brand deployment. CJ API is required for supplier intelligence, shop detection, Shopify-product discovery inside CJ, CJ product/variant lookup, and automated Shopify↔CJ product connection. The official CJdropshipping Shopify App remains the runtime owner for inventory sync, Shopify order sync, fulfillment, and tracking. Enforces AUTH_REQUIRED, HUMAN_REQUIRED, token lifecycle safety, single-owner rules, variant-mapping verification, test-order QA, and hard publish gates.
---

# Shopify Commerce Deployer — v4 HYBRID_CJ_API_APP

> **Default architecture**
>
> **AI + Shopify API** = Store / Product / Theme / Brand / Pricing  
> **AI + CJ API** = Supplier Intelligence / Shop Detection / Product Connection / Variant Mapping  
> **CJ Shopify App** = Inventory / Orders / Fulfillment / Tracking

This skill should automate as much as possible while preserving explicit human control over credentials, ambiguous mappings, pricing, test-order actions, publishing, and money-moving actions.

---

# 0. ORCHESTRATOR PLACEMENT

Recommended workflow:

```text
01 pain-point-research
02 winning-product-research
03 market-validation
04 competition-intelligence
05 store-conversion-engine
06 ai-store-builder
07 shopify-commerce-deployer
08 ecom-growth-engine
09 scale-operations-engine
```

Transition:

```text
AI Store Builder
    ↓
HUMAN APPROVES CURRENT BUILD
    ↓
Shopify Auth
    ↓
CJ API Auth
    ↓
CJ Shopify App Authorization Check
    ↓
CJ Supplier Intelligence
    ↓
Shopify DRAFT Product
    ↓
Pricing Approval
    ↓
Theme Preview
    ↓
CJ API Detects Authorized Shopify Shop
    ↓
CJ API Queries Store Product
    ↓
AI Resolves Variant Mapping
    ↓
CJ API Creates Product Connection
    ↓
Verify CJ Inventory / Order / Tracking Ownership
    ↓
CJ CONNECTION QA
    ↓
TEST ORDER GATE
    ↓
PUBLISH GATE
    ↓
Ecom Growth
```

---

# 1. INTEGRATION MODES

Supported modes:

```text
HYBRID_CJ_API_APP   ← DEFAULT / RECOMMENDED
CJ_API_FULL
ALIEXPRESS_DSERS
CUSTOM_SUPPLIER
```

## HYBRID_CJ_API_APP

### Shopify API owns

```text
Storefront content
Customer-facing product content
Pricing
Theme
Metafields
Collections
SEO
Brand presentation
```

### CJ API owns automation for

```text
Authentication token acquisition
Supplier product search/detail
Supplier variants
Supplier SKU
Cost
Stock snapshot
Warehouses
Freight estimate
Authorized shop detection
Shop product query
Shop product detail
Shopify platform product/variant discovery inside CJ
CJ product connection creation
Connection verification
```

### CJ Shopify App owns runtime fulfillment plumbing

```text
Live inventory sync
Shopify → CJ order sync
CJ fulfillment flow
Tracking → Shopify
```

---

# 2. SINGLE-OWNER RULE

Every responsibility must have exactly one writer.

| Responsibility | Owner |
|---|---|
| Shopify theme | AI + Shopify |
| Product content | AI + Shopify |
| Pricing | AI + Human Approval |
| Supplier intelligence | AI + CJ API |
| CJ shop detection | AI + CJ API |
| Product connection | AI + CJ API |
| Variant mapping decision | AI, Human if ambiguous |
| Live inventory sync | CJ Shopify App |
| Shopify→CJ order sync | CJ Shopify App |
| CJ fulfillment | CJ |
| Tracking→Shopify | CJ Shopify App |
| Monitoring / exceptions | AI |

Forbidden overlap:

```text
CJ App inventory sync
+
AI inventory writer
= FORBIDDEN
```

```text
CJ App order sync
+
AI creates the same CJ order
= FORBIDDEN
```

```text
CJ App tracking sync
+
AI creates duplicate Shopify fulfillment
= FORBIDDEN
```

If ownership is unclear:

```text
STOP
→ HUMAN_REQUIRED
```

---

# 3. SHOPIFY AUTHENTICATION

For an eligible Shopify Dev Dashboard app:

User configures securely:

```env
SHOPIFY_SHOP_DOMAIN=
SHOPIFY_CLIENT_ID=
SHOPIFY_CLIENT_SECRET=
SHOPIFY_API_VERSION=
```

Agent acquires runtime access token programmatically.

Rules:

- Never ask for a static Admin Access Token.
- Never print `SHOPIFY_CLIENT_SECRET`.
- Never print runtime token.
- Keep runtime token server-side only.
- Reacquire before expiration.
- If this store/app model does not support the configured grant, stop with `AUTH_REQUIRED` and request the appropriate Shopify authorization flow.

Optional:

```env
SHOPIFY_APP_AUTOMATION_TOKEN=
```

This is for supported CLI / CI automation and must not be treated as the Admin API runtime token.

---

# 4. CJ API AUTHENTICATION — REQUIRED IN DEFAULT MODE

`HYBRID_CJ_API_APP` requires:

```env
CJ_API_KEY=
```

## Human setup gate

If missing:

```text
AUTH REQUIRED — CJ API

I need a CJ API Key to continue.

Please configure the CJ API integration in your CJ account:

1. Install / enable the CJ API app.
2. Create an API Key.
3. Store it securely as:
   CJ_API_KEY
4. Reply:
   CJ API CONNECTED

Do not paste account passwords.
Do not place secrets in source control.
```

Then the agent verifies the credential.

## Runtime token lifecycle

Conceptual flow:

```text
CJ_API_KEY
    ↓
CJ authentication endpoint
    ↓
CJ Access Token
+
Refresh Token / Expiry Metadata
    ↓
Secure runtime cache
```

Rules:

- User supplies only `CJ_API_KEY`.
- Agent obtains the CJ access token itself.
- Do not hard-code token TTL.
- Read expiry values returned by CJ.
- Track `accessTokenExpiryDate` when available.
- Track refresh expiry when available.
- Refresh/reacquire before expiry.
- Never log CJ access token.
- Never expose CJ token to storefront/frontend.
- On auth failure caused by expiration, refresh/reacquire once and safely retry.
- Repeated auth failure → `AUTH_REQUIRED`.

---

# 5. CJ SHOPIFY APP AUTHORIZATION — REQUIRED RUNTIME BRIDGE

CJ API access does not replace Shopify↔CJ store authorization.

The system must verify that the Shopify store is already connected/authorized in CJ.

Agent should attempt to detect authorized shops via CJ API.

Conceptual check:

```text
CJ API
→ GET SHOPS
→ find Shopify shop matching SHOPIFY_SHOP_DOMAIN
```

Expected state:

```yaml
cj_shop:
  found: true
  platform: shopify
  authorized: true
  sync_inventory:
  fulfillment_status:
  country:
  currency:
```

If the Shopify shop is not found or not authorized:

```text
AUTH REQUIRED — CJ SHOPIFY STORE

Your CJ API is connected, but the Shopify store is not authorized in CJ.

Please:

1. Install the official CJdropshipping Shopify App.
2. Sign in to CJ.
3. Authorize this Shopify store in CJ.
4. Reply:
   CJ STORE CONNECTED

I will verify the shop through CJ API before continuing.
```

Do not pretend the app/store authorization succeeded.

---

# 6. CONFIGURATION

Recommended:

```env
SHOPIFY_INTEGRATION_MODE=HYBRID_CJ_API_APP

# Shopify
SHOPIFY_SHOP_DOMAIN=
SHOPIFY_CLIENT_ID=
SHOPIFY_CLIENT_SECRET=
SHOPIFY_API_VERSION=

# Optional CLI / CI
SHOPIFY_APP_AUTOMATION_TOKEN=

# CJ
CJ_API_KEY=

# Safety
AUTO_PUBLISH=false
AUTO_PAY_SUPPLIER=false
```

In this mode:

```text
AI live inventory writer = DISABLED
AI CJ order creator      = DISABLED
AI tracking writer       = DISABLED
```

unless the user explicitly switches to `CJ_API_FULL`.

---

# 7. ENTRY CONDITIONS

Required upstream state:

```yaml
product:
  approved: true

country:
  approved: true

competition:
  approved: true
  verdict: GO | CONDITIONAL_GO

store_blueprint:
  approved: true

store_build:
  approved: true
```

If missing:

```text
BLOCK
```

---

# 8. CONNECTION WIZARD

## Shopify verification

Check:

```text
Shop domain
Client ID
Client Secret
Runtime Admin token
Products permission
Locations where needed
Orders read where monitoring is needed
Theme deployment path
```

Output:

```yaml
shopify_connection:
  status: CONNECTED | AUTH_REQUIRED | BLOCKED
  shop_domain:
  auth_mode:
  runtime_token_valid:
  missing_permissions: []
```

## CJ API verification

Check:

```text
API key present
CJ runtime token acquisition
Product access
Variant access
Stock access
Freight access
Shop access
Shop product access
Product connection access
```

Output:

```yaml
cj_api:
  status: CONNECTED | AUTH_REQUIRED | BLOCKED
  runtime_token_valid:
  access_token_expires_at:
  capabilities:
    products:
    variants:
    stock:
    freight:
    shops:
    shop_products:
    product_connection:
```

## CJ Shopify Store verification

Output:

```yaml
cj_shopify_app:
  shop_detected_by_api:
  platform: shopify
  authorized:
  inventory_sync:
  order_sync_ready:
  tracking_sync_ready:
```

---

# 9. CJ SUPPLIER PRODUCT INGESTION

Use CJ API to fetch operational supplier truth:

```yaml
supplier_product:
  supplier: CJ
  supplier_product_id:
  raw_title:
  raw_description:
  media: []

  variants:
    - cj_variant_id:
      supplier_sku:
      option_values:
      cost:
      weight:
      dimensions:
      stock_snapshot:

  warehouses: []
  freight_estimate:
  source_metadata:
```

Supplier information is not final customer-facing copy.

---

# 10. PRODUCT NORMALIZATION

## CJ is source of truth for

```text
CJ PID
CJ VID
Supplier SKU
Cost
Weight
Dimensions
Variant attributes
Stock snapshot
Warehouse
Freight
```

## Upstream engines are source of truth for

```text
Product title
Positioning
Pain
Benefits
Description
Offer
Price
FAQ
Comparison
Localization
SEO
Brand
```

Pipeline:

```text
CJ RAW DATA
    ↓
Operational normalization
    ↓
Approved Store Blueprint
    ↓
Localized Shopify Product
```

---

# 11. SHOPIFY DRAFT PRODUCT

Create the approved customer-facing product in Shopify as:

```text
DRAFT
```

Populate:

```text
Title
Handle
Description
Media
Options
Variants
SKU
Weight
Price
Compare-at
Tags
Collections
SEO
Metafields
```

Capture:

```yaml
shopify_product:
  product_id:
  platform_product_id:
  variants:
    - shopify_variant_id:
      platform_variant_id:
      sku:
      option_values:
```

Do not activate yet.

---

# 12. PRICING APPROVAL GATE

Use current CJ cost/freight data and upstream offer.

Present:

```text
PRICING CONFIRMATION

1x:
...

2x:
...

3x:
...

CJ cost:
...

Freight estimate:
...

Estimated contribution:
...

Choose:
[A] APPROVE
[B] CHANGE PRICE
[C] RECALCULATE FREIGHT
[D] CHANGE CJ PRODUCT
```

Stop.

---

# 13. THEME PREVIEW DEPLOYMENT

Deploy preview/unpublished version only.

Rules:

```text
AUTO_PUBLISH=false
Preview before live
Current live theme must not be replaced automatically
```

Output:

```yaml
theme:
  deployed:
  preview_url:
  live: false
```

---

# 14. DETECT SHOPIFY SHOP INSIDE CJ VIA API

Agent should query CJ shops and match the authorized Shopify store.

Matching priority:

```text
1. exact normalized myshopify domain
2. exact platform shop identifier
3. explicit known CJ shop ID
```

Do not match by display name alone when multiple shops exist.

Output:

```yaml
cj_shop_mapping:
  cj_shop_id:
  shopify_domain:
  platform: shopify
  authorized:
  match_confidence:
```

If multiple candidates:

```text
HUMAN_IF_AMBIGUOUS
```

---

# 15. QUERY SHOPIFY PRODUCT INSIDE CJ

After Shopify DRAFT product exists, use CJ shop product APIs to locate the product CJ sees from the authorized Shopify store.

Expected discovery fields may include:

```text
CJ shop product identifier
platformProductId
platformVariantId
SKU
Price
Weight
Connection status
```

Matching priority:

```text
platformProductId
→ platformVariantId
→ SKU
→ normalized option values
```

Never rely only on title matching.

If the product is not visible in CJ yet:

```text
retry with bounded delay
then BLOCK with:
CJ_STORE_PRODUCT_NOT_VISIBLE
```

---

# 16. AUTOMATED VARIANT RESOLUTION

Construct candidate mapping:

```text
Shopify Variant
    ↓
Option values / SKU / quantity semantics
    ↓
CJ Variant candidates
```

Mapping statuses:

```text
UNMAPPED
CANDIDATE
VERIFIED
AMBIGUOUS
BLOCKED
```

Agent may automatically mark `VERIFIED` only when mapping is unambiguous.

Example one-to-one:

```text
Shopify:
Black / Large

CJ:
Black / Large / VID123

→ VERIFIED
```

Example bundle:

```text
Shopify:
2 Pack

CJ:
Single Unit / VID123

Fulfillment semantics:
VID123 × quantity 2

→ may be VERIFIED if blueprint explicitly defines a quantity bundle
```

Example ambiguous:

```text
Shopify:
2 Pack

CJ candidates:
[A] Single Unit VID123 ×2
[B] Factory 2-Pack VID999 ×1
```

Agent must ask:

```text
CJ VARIANT DECISION REQUIRED

Shopify:
2 Pack

[A] CJ VID123 × 2
[B] CJ VID999 × 1

Recommendation:
B

Choose A / B
```

Stop.

---

# 17. CREATE PRODUCT CONNECTION VIA CJ API

When:

```text
CJ shop identified
+
Shopify product visible in CJ
+
CJ supplier product selected
+
Variant mapping VERIFIED
```

the agent should create the Shopify↔CJ product connection through CJ API.

Conceptual payload relationship:

```text
CJ SHOP PRODUCT
platformProductId
platformVariantId

↕

CJ SUPPLIER PRODUCT
CJ PID
CJ VID
```

Persist mapping:

```yaml
cj_product_connection:
  cj_shop_id:
  platform_product_id:
  cj_product_id:
  status:
  variants:
    - platform_variant_id:
      cj_variant_id:
      quantity_multiplier:
      status: VERIFIED
```

Rules:

- Product connection creation must be idempotent.
- Before creating, query existing connection state.
- If equivalent connection already exists, reuse it.
- If a conflicting connection exists, stop with `HUMAN_REQUIRED`.
- Never silently replace a different supplier connection.

---

# 18. VERIFY PRODUCT CONNECTION

After creation, query CJ again.

Verify:

```text
Shopify Product connected?      YES
All active variants connected?  YES
Correct CJ PID?                  YES
Correct CJ VIDs?                 YES
Bundle multiplier correct?       YES
```

Output:

```yaml
cj_connection_verification:
  status: VERIFIED | PARTIAL | BLOCKED
  mapped_variants:
  total_variants:
  conflicts: []
```

No launch if not VERIFIED.

---

# 19. CJ APP INVENTORY OWNERSHIP

In default mode:

```text
LIVE INVENTORY OWNER
=
CJ Shopify App
```

AI may read CJ stock for monitoring but must not run a competing Shopify inventory write loop.

Verify:

```text
CJ inventory sync enabled
Store/product connection verified
Inventory source configured correctly
AI inventory writer disabled
```

Output:

```yaml
inventory:
  owner: CJ_SHOPIFY_APP
  cj_sync_verified:
  ai_writer_enabled: false
```

---

# 20. CJ APP ORDER OWNERSHIP

Default:

```text
Shopify Order
    ↓
CJ Shopify App
    ↓
CJ Store Order
```

AI must not also create the supplier order through CJ API.

AI monitors:

```text
Order sync success
Missing CJ order
Wrong mapping
Stock issue
Freight anomaly
Duplicate anomaly
```

---

# 21. CJ APP TRACKING OWNERSHIP

Default:

```text
CJ Fulfillment
    ↓
CJ Tracking
    ↓
CJ Shopify App
    ↓
Shopify
```

AI monitors only.

Do not create duplicate Shopify fulfillment/tracking entries.

---

# 22. CJ CONNECTION QA GATE

Before test order:

```text
CJ CONNECTION QA

Shopify:
CONNECTED

CJ API:
CONNECTED

CJ Shopify Store:
AUTHORIZED

Shopify Product:
DRAFT

CJ Product:
...

CJ Product Connection:
VERIFIED

Variant Mapping:
X / X VERIFIED

Inventory Owner:
CJ Shopify App

Order Owner:
CJ Shopify App

Tracking Owner:
CJ Shopify App

AI Duplicate Writers:
DISABLED

Recommendation:
READY FOR TEST ORDER

Choose:
[A] RUN TEST ORDER
[B] FIX MAPPING
[C] CHANGE CJ PRODUCT
[D] RECONNECT STORE
[E] ABORT
```

Stop.

---

# 23. TEST ORDER GATE

Do not automatically create/pay a real supplier order without permission.

When user chooses to test:

```text
Create / place Shopify test order
    ↓
Verify Shopify order
    ↓
Verify order appears in CJ
    ↓
Verify mapped CJ variant
    ↓
Verify quantity multiplier
    ↓
Verify address / shipping data
    ↓
Verify no duplicate supplier order
```

Default:

```text
AUTO_PAY_SUPPLIER=false
```

Output:

```yaml
test_order:
  shopify_order:
  cj_order_detected:
  correct_variant:
  correct_quantity:
  duplicate_detected:
  supplier_payment_performed: false
  status: PASS | FAIL | NOT_VERIFIED
```

---

# 24. LIVE QA

Verify:

```text
Homepage
Product
Theme
Mobile
Desktop
Currency
Localization
Variants
Pricing
Offer
ATC
Cart
Checkout handoff
Policies

Shopify auth
CJ API auth
CJ shop authorization
CJ product connection
Variant mapping
Inventory ownership
Order sync readiness
Tracking sync readiness
```

Use only:

```text
PASS
FAIL
NOT_VERIFIED
```

---

# 25. PUBLISH GATE

Hard gate:

```text
DEPLOYMENT READY

Shopify:
CONNECTED

CJ API:
CONNECTED

CJ Shopify Store:
AUTHORIZED

CJ Product Connection:
VERIFIED

Variant Mapping:
VERIFIED

Inventory:
CJ APP

Orders:
CJ APP

Tracking:
CJ APP

Theme:
PREVIEW READY

Test Order:
PASS

Recommendation:
READY TO PUBLISH

Choose:
[A] PUBLISH
[B] MODIFY STORE
[C] FIX CJ CONNECTION
[D] CHANGE PRODUCT
[E] ABORT
```

Stop.

Only `[A]` for the current preview counts as approval.

---

# 26. AFTER PUBLISH

After explicit approval:

```text
Activate approved Shopify product
Publish approved theme
Re-verify CJ product connection
Re-verify variant mapping
Re-verify inventory sync
Re-verify order sync
Re-verify tracking sync
Enable monitoring
```

Any post-publish variant mutation must invalidate the previous CJ mapping verification.

---

# 27. MONITORING

Monitor:

```text
CJ shop authorization
Broken product connections
Variant mapping drift
CJ stock warnings
Supplier cost changes
Freight changes
Shopify order sync
Missing CJ orders
Duplicate anomalies
Tracking sync
Fulfillment delay
```

Alert codes:

```text
CJ_AUTH_EXPIRED
CJ_STORE_NOT_AUTHORIZED
CJ_STORE_PRODUCT_NOT_VISIBLE
CJ_CONNECTION_BROKEN
CJ_VARIANT_MAPPING_AMBIGUOUS
CJ_VARIANT_MAPPING_DRIFT
CJ_ORDER_NOT_SYNCED
CJ_DUPLICATE_ORDER_RISK
CJ_TRACKING_NOT_SYNCED
CJ_OUT_OF_STOCK
CJ_COST_CHANGED
CJ_FREIGHT_SPIKE
```

---

# 28. FALLBACK TO CJ_API_FULL

Do not switch automatically.

If CJ App runtime sync fails:

```text
[A] FIX CJ APP
[B] SWITCH TO CJ_API_FULL
[C] HOLD ORDERS
```

If `[B]` is approved:

```text
Disable CJ App ownership for the affected responsibility first
Enable API writer second
Configure idempotency
Configure payment policy
Verify no duplicate route
```

Never run both owners simultaneously.

---

# 29. STATE MODEL

```json
{
  "commerce_deployment": {
    "mode": "HYBRID_CJ_API_APP",

    "shopify": {
      "connected": false
    },

    "cj_api": {
      "api_key_configured": false,
      "runtime_token_valid": false,
      "access_token_expires_at": null
    },

    "cj_shop": {
      "detected": false,
      "authorized": false,
      "cj_shop_id": null
    },

    "shopify_product": {
      "status": "DRAFT",
      "platform_product_id": null
    },

    "cj_product": {
      "cj_product_id": null
    },

    "cj_product_connection": {
      "status": "UNMAPPED"
    },

    "ownership": {
      "inventory": "CJ_SHOPIFY_APP",
      "orders": "CJ_SHOPIFY_APP",
      "tracking": "CJ_SHOPIFY_APP"
    },

    "test_order": {
      "status": "NOT_VERIFIED"
    },

    "publish_approved": false
  }
}
```

---

# 30. OUTPUT ARTIFACTS

Produce:

```text
commerce-deploy/
├── shopify-connection-report.json
├── cj-auth-report.json
├── cj-shop-detection.json
├── supplier-product.raw.json
├── supplier-product.normalized.json
├── shopify-product-payload.json
├── cj-shop-product.json
├── cj-variant-candidates.json
├── cj-product-connection.json
├── cj-connection-verification.json
├── ownership-matrix.yaml
├── live-qa-report.md
├── test-order-report.json
├── deployment-report.json
└── publish-gate.md
```

Never include secrets.

---

# 31. FINAL SUCCESS CONDITION

The skill succeeds when:

```text
Shopify authenticated
+
CJ API authenticated
+
CJ Shopify store detected and authorized
+
CJ supplier product validated
+
Shopify customer-facing DRAFT product created
+
CJ sees the Shopify product
+
Variant mapping verified
+
CJ API product connection created
+
CJ App owns inventory/order/tracking
+
AI duplicate writers disabled
+
Theme preview deployed
+
Test order verified
+
Human receives final Publish Gate
```

> **CJ API automates the connection. CJ Shopify App operates the fulfillment bridge. Shopify remains the customer-facing brand system.**
