---
name: supplier-product-discovery-enrichment
description: Supplier discovery and enrichment skill that converts an approved product concept into an exact fulfillable supplier product. Default supplier is CJ Dropshipping API. Phase A searches and ranks CJ supplier candidates using product-fit, cost, stock, freight, warehouse, variants, media, and fulfillment suitability, then stops for human selection when a business choice remains. Phase B enriches the selected CJ PID with complete operational truth and media: VID/SKU/cost/stock/warehouse/freight/dimensions/main image/gallery/variant images/videos, performs media QA, and produces a Supplier Product Pack + Store Asset Pack for Store Conversion and AI Store Builder.
---

# Supplier Product Discovery & Enrichment

> **Core principle:**  
> Product Research identifies **what kind of product should win**.  
> Supplier Discovery identifies **which exact supplier product will fulfill it**.  
> Enrichment then retrieves **everything real about that exact product**.

Do not enrich an abstract product concept.

---

# 0. ORCHESTRATOR POSITION

```text
01 pain-point-research
02 winning-product-research
03 market-validation
04 competition-intelligence
        ↓
HUMAN GO
        ↓
05 supplier-product-discovery-enrichment
        │
        ├── PHASE A — DISCOVERY
        │       ↓
        │    CJ candidates
        │       ↓
        │    HUMAN SELECT
        │       ↓
        └── PHASE B — ENRICHMENT
                ↓
           exact CJ PID / VID
           cost / stock / freight
           images / videos
           asset pack
                ↓
06 store-conversion-engine
07 ai-store-builder
08 shopify-commerce-deployer
09 ecom-growth-engine
10 scale-operations-engine
```

This skill does not deploy Shopify.

---

# 1. INPUT MODEL

The input is a **product concept**, not necessarily a supplier listing.

Example:

```yaml
product_concept:
  canonical_name: "Reusable pet hair remover"
  pain: "Pet hair embedded in fabric"
  mechanism: "Manual scraping / lifting"
  target_country: "DE"
  target_price_band:
    min:
    max:
  target_cost_max:
  required_attributes: []
  prohibited_attributes: []
  preferred_supplier_pid:
```

If the user provides a CJ product URL or `preferred_supplier_pid`, treat it as
`USER_PROVIDED_PRODUCT`.

Resolve and validate it first, then show a confirmation gate.

Example:

```text
SUPPLIER PRODUCT CONFIRMATION

CJ Product:
...

PID:
...

Main image:
...

Cost:
...

Stock:
...

Freight estimate:
...

Product match:
94/100

[A] USE THIS PRODUCT
[B] SEARCH ALTERNATIVES
[C] SEND ANOTHER LINK

Recommendation:
A

Choose:
A / B / C
```

STOP.

Only `[A]` sets:

```yaml
selected_supplier_product:
  approved: true
```

A supplied link/PID is a candidate chosen by the user, but enrichment still
requires explicit confirmation of the resolved product.

If the supplied product fails critical fit checks, explain the issue and offer
`SEARCH ALTERNATIVES`; do not silently replace it.

---


# 1A. SUPPLIER PRODUCT INPUT MODES

The exact supplier product must be approved by the human before enrichment.

Supported entry modes:

```text
MODE A — USER_PROVIDED_PRODUCT
User sends:
- CJ product URL
or
- CJ PID

Agent:
→ resolve product
→ fetch lightweight preview data
→ show confirmation
→ STOP
→ enrich only after user confirms


MODE B — AGENT_DISCOVERY
User has no supplier link/PID

Agent:
→ search CJ
→ shortlist 3–5 candidates
→ show image + PID + cost + stock + freight + variants + risks
→ STOP
→ user selects exact product
→ enrichment unlocks
```

Default:

```yaml
supplier_selection:
  mode: HUMAN_REQUIRED
  auto_select: false
```

Automatic supplier selection is forbidden unless the user explicitly enables:

```env
AUTO_SUPPLIER_SELECTION=true
```

Even when enabled, the agent must record why the selected product satisfies the
approved product concept and must not silently substitute another supplier product later.


# 2. ENTRY CONDITIONS

Required:

```yaml
pain:
  approved: true

product:
  approved: true

country:
  approved: true

competition:
  approved: true
  verdict: GO | CONDITIONAL_GO
```

Do not begin supplier sourcing before the upstream product concept is approved.

---

# 3. CJ AUTH — AUTH_REQUIRED

Default supplier mode:

```text
CJ_API
```

Required secret:

```env
CJ_API_KEY=
```

If missing:

```text
AUTH REQUIRED — CJ API

Supplier discovery requires CJ API access.

Please:
1. Enable/install the CJ API app.
2. Create an API Key.
3. Store it securely as:
   CJ_API_KEY
4. Reply:
   CJ API CONNECTED
```

Do not request account passwords.

---

# 4. CJ TOKEN LIFECYCLE

User supplies only:

```text
CJ_API_KEY
```

Agent obtains runtime authentication itself.

```text
CJ_API_KEY
    ↓
CJ Auth
    ↓
Runtime Access Token
+
Expiry / Refresh Metadata
```

Rules:

- Backend only.
- Never log secret/token.
- Never hard-code TTL.
- Read expiry metadata returned by CJ.
- Refresh/reacquire before expiry.
- Auth retry once after successful refresh.
- Persistent failure → `AUTH_REQUIRED`.

---

# 5. PHASE A — SUPPLIER PRODUCT DISCOVERY

## Goal

Convert:

```text
PRODUCT CONCEPT
```

into:

```text
EXACT CJ PRODUCT
```

Example:

```text
Reusable pet hair remover
        ↓
CJ Search
        ↓
Candidate A
Candidate B
Candidate C
        ↓
Human / policy selection
        ↓
CJ PID = exact supplier product
```

---

# 6. DISCOVERY SEARCH PLAN

Build CJ search queries from:

```text
Canonical product name
Mechanism
Pain language
Product synonyms
Material
Use case
Target country
Required attributes
```

Example:

```text
pet hair remover
reusable pet hair scraper
fabric hair remover
carpet pet hair tool
```

Do not rely on one keyword.

---

# 7. DISCOVERY FILTERS

When supported by supplier data/API, filter or rank by:

```text
Product match
Mechanism match
Target-country availability
Supplier cost
Stock
Warehouse
Variant quality
Weight
Dimensions
Freight potential
Main-image quality
Gallery count
Video availability
Supplier/product reliability signals
Listing / connection suitability
```

Do not choose only by:

```text
lowest price
highest listing count
prettiest image
```

---

# 8. CANDIDATE NORMALIZATION

For each CJ candidate, build:

```yaml
supplier_candidate:

  supplier: CJ

  identity:
    pid:
    sku:
    raw_title:

  match:
    product_concept:
    mechanism:
    required_attributes:
    score:

  economics:
    product_cost:
    indicative_freight:
    landed_estimate:

  operations:
    stock_snapshot:
    warehouse:
    weight:
    variants_count:

  media:
    main_image:
    gallery_available:
    video_available:

  risks: []

  confidence:
```

---

# 9. SUPPLIER CANDIDATE SCORE

Recommended score /100:

```text
Product / mechanism fit        25
Target-country logistics       15
Unit economics                 15
Stock / supply quality         10
Variant suitability            10
Media quality                  10
Freight attractiveness         10
Supplier / fulfillment fit      5
```

This score is a decision aid, not proof of profitability.

---

# 10. DISCOVERY OUTPUT UI

If more than one commercially valid candidate remains:

```text
================================
SUPPLIER PRODUCT DECISION
================================

Product concept:
Reusable pet hair remover

[A] CJ Product A
PID: ...
Match: 94/100
Cost: ...
Stock: ...
Freight estimate: ...
Variants: ...
Images: 8
Video: YES
Why:
- ...
Risk:
- ...

[B] CJ Product B
PID: ...
Match: 89/100
...

[C] CJ Product C
...

RECOMMENDATION:
A

Choose:
A
B
C
COMPARE A B
RESEARCH MORE
```

Then STOP.

Human chooses the exact supplier product.

---

# 11. SUPPLIER SELECTION HARD GATE

Default:

```text
supplier_product_selection = HUMAN_REQUIRED
AUTO_SUPPLIER_SELECTION=false
```

Hard rule:

```text
IF selected_supplier_product.approved != true
THEN PHASE B ENRICHMENT MUST NOT RUN
```

The agent must not auto-select from a shortlist.

The agent must not interpret its own recommendation as approval.

The agent must not interpret a high score as approval.

The agent must not interpret a user-provided URL as approval until the resolved
CJ product has been shown back to the user for confirmation.

Automatic selection is allowed only when the user explicitly enables:

```env
AUTO_SUPPLIER_SELECTION=true
```

Recommended default remains human approval because the exact supplier product affects:

```text
cost
quality
shipping
appearance
variants
fulfillment
refund risk
```

---

# 12. PHASE A SUCCESS CONDITION

Phase A succeeds when:

```yaml
selected_supplier_product:
  supplier: CJ
  pid:
  approved: true
```

Only now may Phase B run.

---

# 13. PHASE B — PRODUCT ENRICHMENT

Input:

```text
Exact approved CJ PID
```

Goal:

```text
Retrieve complete supplier truth
+
retrieve complete usable media
+
prepare downstream product/asset packs
```

---

# 14. COMPLETE PRODUCT DETAIL INGESTION

Fetch and normalize:

```yaml
supplier_product_raw:

  supplier: CJ

  product:
    pid:
    sku:
    raw_title:
    raw_description:
    category:
    material:
    weight:
    dimensions:

  variants:
    - vid:
      sku:
      option_values:
      cost:
      weight:
      dimensions:
      stock_snapshot:
      image_url:

  warehouses: []

  stock:
    total_snapshot:
    by_variant: []
    by_warehouse: []

  shipping:
    destination_country:
    methods: []
    freight_estimate:
    estimated_transit:
    captured_at:

  media:
    main_image:
    gallery: []
    variant_images: []
    videos: []
```

Do not invent missing data.

Use:

```text
UNKNOWN
NOT_PROVIDED
NOT_VERIFIED
```

---

# 15. IMAGE INGESTION — REQUIRED

Explicitly collect:

```text
Main image
Gallery/image set
Variant images
Useful description-embedded images
```

An empty generic `media: []` is not sufficient.

Normalize:

```yaml
raw_images:

  main:
    source_url:
    source_type: MAIN
    cj_pid:

  gallery:
    - source_url:
      source_type: GALLERY
      cj_pid:

  variants:
    - source_url:
      source_type: VARIANT
      cj_pid:
      cj_vid:
      option_values:
```

---

# 16. VIDEO INGESTION — REQUIRED WHEN AVAILABLE

Retrieve product video metadata when available.

Normalize:

```yaml
raw_videos:
  - source_url:
    cover_url:
    duration:
    width:
    height:
    video_type:
    copyright_status:
    free_status:
    purchased_status:
    rights_status:
    cj_pid:
```

If usage rights are unclear:

```text
RIGHTS_REVIEW_REQUIRED
```

Do not silently treat unclear video rights as approved.

---

# 17. MEDIA CACHE / STORAGE PLAN

Preferred:

```text
CJ media source
    ↓
Download / controlled cache
    ↓
Project asset storage
    ↓
Optimize
    ↓
Store Builder / Shopify
```

Do not intentionally make the final storefront permanently dependent on supplier hotlinks.

If caching is unavailable:

```text
preserve source_url
+
CACHE_REQUIRED
```

---

# 18. MEDIA QA

Every asset receives:

```text
Resolution
Sharpness
Product match
Variant match
Watermark
Foreign text
Supplier branding
Background quality
Compression
Cropping
Duplicate check
Useful angle
Mechanism usefulness
Lifestyle usefulness
```

Statuses:

```text
KEEP
REWORK
DROP
REVIEW
```

Example:

```yaml
asset:
  asset_id: IMG_004
  role_candidate: PRODUCT_GALLERY
  product_match: PASS
  watermark: PASS
  foreign_text: FAIL
  status: REWORK
  reason:
    - embedded supplier text
```

---

# 19. PRODUCT TRUTH VS MARKETING ASSET

## PRODUCT TRUTH

Must remain faithful:

```text
shape
color
variant
included components
material appearance
dimensions
physical controls
mechanism
```

## MARKETING ASSET

May be created/transformed:

```text
lifestyle composition
brand background
benefit graphic
problem scene
comparison graphic
mechanism diagram
hero composition
```

Rules:

- Preserve real product identity.
- Do not fake product performance.
- Do not fabricate certifications.
- Do not fabricate before/after evidence.
- Do not create a materially different product.

---

# 20. MEDIA ROLE ASSIGNMENT

Classify assets into:

```text
HERO_PRODUCT
PRODUCT_GALLERY
PRODUCT_ANGLE
DETAIL_CLOSEUP
MECHANISM_SOURCE
VARIANT
DIMENSIONS
WHATS_INCLUDED
DEMO
USE_CASE_SOURCE
BEFORE_AFTER_SOURCE
LIFESTYLE_SOURCE
INFOGRAPHIC_SOURCE
UGC_SOURCE
```

---

# 21. PRODUCT GALLERY PLAN

Attempt to provide:

```text
01 Hero
02 Alternate angle
03 Close-up/detail
04 How-it-works source
05 Benefit/proof source
06 Use-case source
07 Dimensions/what's included
08 Variant media
09 Demo video
10 Missing trust/guarantee graphic requirement
```

CJ does not need to provide all 10.

Missing marketing assets become generation requirements.

---

# 22. VARIANT VALIDATION

Every sellable CJ variant should have when available:

```text
VID
SKU
option values
cost
weight
dimensions
stock snapshot
variant image
```

Validate:

```text
variant image ↔ correct option
color
size
pack count
```

Ambiguous mappings:

```text
HUMAN_IF_AMBIGUOUS
```

---

# 23. FREIGHT ENRICHMENT

Freight must use the approved target country.

Input:

```text
country
CJ PID / VID
quantity scenarios
warehouse options
```

Output:

```yaml
freight:
  destination_country:
  captured_at:
  scenarios:
    - quantity: 1
      cj_vid:
      method:
      cost:
      estimated_transit:
    - quantity: 2
      ...
    - quantity: 3
      ...
```

Do not present generic freight as exact country-specific freight.

---

# 24. SUPPLIER ECONOMICS

Create:

```yaml
supplier_economics:
  currency:

  one_unit:
    product_cost:
    freight:
    landed_pre_tax:

  two_units:
    product_cost:
    freight:
    landed_pre_tax:

  three_units:
    product_cost:
    freight:
    landed_pre_tax:

  captured_at:
```

This is input to the Store Conversion offer engine.

Final retail pricing remains downstream / human-approved.

---

# 25. BUNDLE FULFILLMENT CANDIDATES

Record fulfillment possibilities.

Example:

```yaml
bundle_candidates:

  - store_offer: TWO_PACK

    candidates:

      - method: MULTIPLIER
        cj_vid: VID_SINGLE
        quantity_multiplier: 2
        landed_cost:

      - method: SUPPLIER_BUNDLE
        cj_vid: VID_TWO_PACK
        quantity_multiplier: 1
        landed_cost:
```

If both are materially different:

```text
HUMAN_REQUIRED
```

or defer final choice to Commerce Deployer if the Store Offer has not yet been designed.

---

# 26. SUPPLIER PRODUCT PACK

Produce:

```yaml
supplier_product_pack:

  identity:
    supplier: CJ
    pid:
    sku:

  source_product_concept:
    name:
    mechanism:

  product_match:
    score:
    confidence:

  facts:
    category:
    material:
    weight:
    dimensions:
    mechanism_facts: []

  variants: []

  warehouses: []

  stock_snapshot: {}

  freight: {}

  economics: {}

  bundle_candidates: []

  media_summary:
    main_image:
    gallery_count:
    variant_image_count:
    video_count:

  captured_at:
```

---

# 27. STORE ASSET PACK

Produce:

```yaml
store_asset_pack:

  product:
    supplier: CJ
    pid:

  hero:
    asset_id:
    status:

  gallery: []

  variants: []

  videos: []

  generation_inputs:

    mechanism:
      real_product_reference_assets: []

    lifestyle:
      real_product_reference_assets: []

    benefits:
      real_product_reference_assets: []

  missing_assets: []
```

This becomes a primary input to AI Store Builder.

---

# 28. DOWNSTREAM CONTRACT — STORE CONVERSION

Input:

```text
Pain
Product Concept
Country
Competition
Supplier Product Pack
Store Asset Pack
```

Use supplier reality to constrain:

```text
offer
bundle strategy
mechanism claims
shipping messaging
proof plan
visual narrative
economics
```

Do not design offers that cannot be fulfilled by the selected supplier product.

---

# 29. DOWNSTREAM CONTRACT — AI STORE BUILDER

Input:

```text
Store Blueprint
Supplier Product Pack
Store Asset Pack
```

Asset priority:

```text
1. Real product-truth assets
2. Approved transformations using those real references
3. Generated marketing assets for missing roles
```

Do not generate product identity from text alone when real supplier images exist.

---

# 30. DOWNSTREAM CONTRACT — SHOPIFY COMMERCE DEPLOYER

Reuse:

```text
CJ PID
CJ VIDs
Supplier product data
Asset Pack
Bundle candidates
```

Commerce Deployer should:

```text
create branded Shopify product
upload/import selected media
query Shopify product inside CJ
create CJ product connection
verify variant mapping
```

Do not repeat supplier discovery from zero unless invalidated/stale.

---

# 31. FAST_IMPORT MODE — OPTIONAL

Optional future mode:

```text
FAST_IMPORT
```

Flow:

```text
CJ List/Import
    ↓
Shopify raw supplier product
    ↓
AI rewrites / rebuilds brand presentation
```

This is not the default.

Default:

```text
STANDARD_BRAND_BUILD
```

Flow:

```text
Discover CJ
→ enrich
→ build branded Shopify product
→ connect Shopify product back to CJ
```

---

# 32. INVALIDATION RULES

## Product concept changed

Invalidate:

```text
Discovery candidates
Selected supplier product
All enrichment
All asset packs
Downstream supplier-dependent work
```

## CJ product changed

Invalidate:

```text
enrichment
media
economics
freight
variant assumptions
Store Blueprint supplier-dependent sections
Store Build product media
Commerce mapping
```

## Country changed

Invalidate:

```text
freight
warehouse preference
delivery assumptions
landed economics
```

Keep physical product media if product unchanged.

---

# 33. FRESHNESS

Timestamp:

```text
cost
stock
freight
availability
warehouse
```

Before pricing/publish, downstream skills should revalidate volatile fields when stale.

Do not unnecessarily refetch static media every time.

---

# 34. PHASE B QA

```text
SUPPLIER ENRICHMENT QA

Supplier:
CJ

Selected Product:
...

PID:
...

Product Match:
PASS

Variants:
X

Cost:
VERIFIED

Stock:
SNAPSHOT

Freight:
VERIFIED / NOT_VERIFIED

Main Image:
PASS

Gallery:
X KEEP
Y REWORK
Z DROP

Variant Images:
X / X

Videos:
X FOUND
Y APPROVED
Z RIGHTS REVIEW

Supplier Product Pack:
READY

Store Asset Pack:
READY

Recommendation:
READY FOR STORE CONVERSION
```

Critical unresolved identity/variant data:

```text
BLOCK
```

---

# 35. OUTPUT ARTIFACTS

```text
supplier-discovery-enrichment/
├── cj-auth-report.json
├── discovery-query-plan.json
├── supplier-candidates.json
├── supplier-decision.md
├── selected-supplier-product.json
├── supplier-product.raw.json
├── supplier-product-pack.json
├── supplier-economics.json
├── freight-report.json
├── raw-media-manifest.json
├── media-qa-report.json
├── asset-manifest.json
├── store-asset-pack.json
├── missing-assets.json
└── enrichment-report.md
```

Never include secrets/runtime tokens.

---

# 36. STATE MODEL

```json
{
  "supplier_discovery_enrichment": {

    "status": "DISCOVERY",

    "auth": {
      "cj_api_key_configured": false,
      "runtime_token_valid": false
    },

    "discovery": {
      "product_concept": null,
      "candidates_found": 0,
      "selected_pid": null,
      "approved": false
    },

    "enrichment": {
      "status": "LOCKED",
      "variants_count": 0,
      "freight_status": "NOT_VERIFIED",
      "images_found": 0,
      "videos_found": 0,
      "media_qa_complete": false
    },

    "outputs": {
      "supplier_product_pack": "NOT_READY",
      "store_asset_pack": "NOT_READY"
    }
  }
}
```

Hard rule:

```text
IF discovery.approved != true
THEN enrichment.status = LOCKED
```

---

# 37. FINAL SUCCESS CONDITION

The skill succeeds only when:

```text
Approved product concept
→ CJ searched
→ supplier candidates compared
→ exact CJ PID selected
→ exact product enriched
→ variants verified
→ country freight inspected
→ real images collected
→ variant images collected
→ videos collected when available
→ media QA completed
→ Supplier Product Pack ready
→ Store Asset Pack ready
```

> **First choose the real supplier product. Then enrich it. Then build the store around reality.**
