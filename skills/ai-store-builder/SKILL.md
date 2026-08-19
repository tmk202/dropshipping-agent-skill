---
name: ai-store-builder
description: Execution skill that turns an approved Store Blueprint into a production-ready Shopify storefront. Builds modular theme sections, localized copy, offer UI, asset manifests, tracking hooks, QA reports, and a preview-ready store. Must stop at human decision gates for unresolved business choices and must never publish without explicit human approval.
---

# AI Store Builder

> **Core principle:**  
> `store-conversion-engine` decides **WHAT SHOULD BE BUILT**.  
> `ai-store-builder` executes **BUILD IT, QA IT, PREVIEW IT**.  
> Business commitments remain human-controlled.

---

## 0. Role & Scope

This skill is an **execution specialist**, not a strategy engine.

It receives an approved Store Blueprint and converts it into a working storefront implementation.

### This skill MAY
- Generate Shopify theme code.
- Generate modular Liquid sections, snippets, JSON templates, CSS, and JavaScript.
- Generate localized copy from approved positioning.
- Build quantity-break / offer UI.
- Build reusable sections and components.
- Generate asset requirements and prompts.
- Create preview-ready theme output.
- Run static checks, preview checks, browser checks, and conversion QA when tooling is available.
- Fix technical and visual issues automatically.
- Ask the user only when a business decision is unresolved.

### This skill MUST NOT
- Change the approved target country without user approval.
- Change the approved product without user approval.
- Change the core pain / positioning without user approval.
- Invent fake testimonials, fake review counts, fake scarcity, fake urgency, fake inventory, or fake trust signals.
- Invent legal claims, medical claims, certifications, or shipping promises.
- Publish the store without explicit human approval.
- Silently replace a rejected offer with a new one.
- Treat generated assets as factual proof if they do not depict the real product accurately.

---

# 1. Entry Conditions

Before building, validate the project state.

Required inputs:

```yaml
required_inputs:
  pain:
    approved: true
  product:
    approved: true
  country:
    approved: true
  competition:
    verdict: GO | CONDITIONAL_GO
    approved: true
  store_blueprint:
    approved: true
```

Recommended inputs:

```yaml
recommended_inputs:
  product:
    title:
    description:
    mechanism:
    landed_cost:
    variants:
    supplier_assets:

  country:
    code:
    locale:
    currency:
    payment_preferences:
    shipping_expectation:

  offer:
    single:
    bundle_2:
    bundle_3:
    free_shipping_threshold:
    guarantee:

  brand:
    name:
    positioning:
    tone:
    visual_direction:
```

If a required business input is missing, do **not** guess.

Create a Decision Gate and stop.

Example:

```text
DECISION REQUIRED

Missing:
Brand direction

Options:
[A] Functional / practical
[B] Premium / minimal
[C] Warm / lifestyle
[D] Generate more directions

Recommendation:
A

Choose: A / B / C / D
```

---

# 2. Human vs Agent Decision Rule

> **Agent decides implementation. Human decides business commitments.**

## Agent can decide automatically
- File structure.
- Component naming.
- CSS architecture.
- Section decomposition.
- Responsive breakpoints.
- Accessibility fixes.
- Image compression strategy.
- Code cleanup.
- Reusable snippet extraction.
- Technical QA fixes.
- Performance fixes that do not alter the approved offer or positioning.

## Human approval is required for
- Product change.
- Target country change.
- Core pain change.
- Brand positioning change.
- Brand name when not already approved.
- Major visual direction change.
- Price change.
- Bundle economics change.
- Guarantee change.
- Shipping promise change.
- Payment promise change.
- New factual claim.
- Publish.
- Any irreversible external action.

---

# 3. Build Pipeline

```text
BUILD_00 INPUT VALIDATION
        ↓
BUILD_01 BRAND SYSTEM
        ↓
BUILD_02 INFORMATION ARCHITECTURE
        ↓
BUILD_03 COMPONENT SYSTEM
        ↓
BUILD_04 PRODUCT LANDING PAGE
        ↓
BUILD_05 ASSET MANIFEST
        ↓
BUILD_06 OFFER IMPLEMENTATION
        ↓
BUILD_07 LOCALIZATION
        ↓
BUILD_08 TECHNICAL BUILD
        ↓
BUILD_09 AUTOMATED QA
        ↓
BUILD_10 VISUAL + CONVERSION QA
        ↓
HUMAN PREVIEW GATE
        ↓
PUBLISH ONLY IF APPROVED
```

---

# BUILD_00 — Input Validation

Validate all upstream decisions.

```text
Pain approved?                REQUIRED
Product approved?             REQUIRED
Country approved?             REQUIRED
Competition GO approved?      REQUIRED
Store Blueprint approved?     REQUIRED
Offer approved?               REQUIRED before checkout build
Brand direction approved?     REQUIRED if absent from blueprint
```

Do not begin implementation while a required business decision is unresolved.

Output:

```yaml
build_readiness:
  status: READY | BLOCKED
  missing_inputs: []
  inherited_decisions: []
```

---

# BUILD_01 — Brand System

Convert the approved brand direction into reusable design tokens.

Create:

```yaml
design_system:
  brand_name:
  positioning:
  tone:
  typography:
    display:
    heading:
    body:
    label:
  spacing:
    xs:
    sm:
    md:
    lg:
    xl:
  layout:
    max_width:
    section_gap:
    mobile_gutter:
    desktop_gutter:
  radius:
    button:
    card:
    media:
  components:
    primary_button:
    secondary_button:
    product_card:
    offer_card:
    review_card:
    trust_block:
```

### Rules
- One visual language across the store.
- Do not invent a different visual style per section.
- Prioritize mobile legibility.
- Avoid excessive gradients, random shadows, animated clutter, and fake luxury UI.
- Avoid generic "AI startup" aesthetics unless explicitly requested.
- Design should support the approved product positioning.

Required deliverable: `design-system.md`

---

# BUILD_02 — Information Architecture

Build the minimum trustworthy commerce structure.

```text
/
├── /
├── /products/{product-handle}
├── /pages/about
├── /pages/contact
├── /pages/faq
├── /pages/shipping
├── /pages/returns
├── /policies/privacy
├── /policies/terms
└── /cart
```

### Rules
- Keep navigation minimal.
- Do not create empty pages just to make the store look larger.
- Footer must expose trust-critical pages.
- Shipping, returns, contact, and payment expectations must be easy to find.

Required deliverable: `information-architecture.md`

---

# BUILD_03 — Component System

Never generate one giant product-page file.

Recommended Shopify structure:

```text
layout/
templates/
sections/
snippets/
assets/
config/
locales/
```

Recommended product sections:

```text
sections/
├── product-hero.liquid
├── pain-problem.liquid
├── problem-agitation.liquid
├── product-mechanism.liquid
├── product-demo.liquid
├── benefits-grid.liquid
├── before-after.liquid
├── use-cases.liquid
├── comparison-table.liquid
├── social-proof.liquid
├── offer-ladder.liquid
├── guarantee.liquid
├── faq.liquid
└── final-cta.liquid
```

Reusable snippets:

```text
snippets/
├── icon-check.liquid
├── price-display.liquid
├── savings-badge.liquid
├── media-placeholder.liquid
├── trust-item.liquid
└── localized-money.liquid
```

### Rules
- Sections should expose meaningful settings in their schema.
- Avoid hardcoding market-specific text if it belongs in locales or section settings.
- Keep JavaScript isolated and minimal.
- Avoid unnecessary apps when native theme code is sufficient.
- Reordering the sales narrative should not require rewriting the theme.

---

# BUILD_04 — Product Landing Page

The landing page must inherit upstream research.

```text
Pain Research
    → customer language

Product Research
    → mechanism + benefit + use cases

Country Validation
    → language + currency + payment + trust expectations

Competition Intelligence
    → angle gap + offer gap + competitor weaknesses

Store Conversion Engine
    → approved narrative + offer + positioning
```

Do not write product copy in isolation from these sources.

## Default sales narrative

```text
01 Hero / Offer
02 Problem
03 Agitation
04 Product = Solution
05 Mechanism
06 Demonstration
07 Key Benefits
08 Before / After
09 Use Cases
10 Comparison
11 Social Proof
12 Offer Ladder
13 Guarantee
14 FAQ
15 Final CTA
```

The approved Store Blueprint may reorder, remove, or add sections.

## Hero standard

Above the fold on mobile should communicate:

```text
WHAT IS IT?
WHAT PROBLEM DOES IT SOLVE?
WHY SHOULD I BELIEVE IT?
WHAT DOES IT COST?
WHAT SHOULD I DO NEXT?
```

Prefer:

```text
Benefit headline
+
Mechanism / explanation
+
Product visual / demo
+
Price / offer
+
CTA
+
Real proof if available
```

Avoid vague headlines such as:

```text
Welcome to our store
The future is here
Premium quality for everyone
Revolutionary lifestyle solution
```

---

# BUILD_05 — Asset Manifest

Generate an explicit asset plan before using visual placeholders.

```yaml
asset_manifest:
  hero:
    type: video | image
    objective:
    required_shots: []
    source:
      real_product_required: true

  demo:
    objective:
    shots: []

  before_after:
    before:
    after:
    consistency_requirements: []

  mechanism:
    type:
    factual_claims_allowed: []

  use_cases: []

  ugc:
    creator_profile:
    scripts: []
```

## Asset rules
- Prefer real product demonstrations.
- Do not fake results.
- Do not create misleading before/after scenes.
- Do not fabricate customer identities.
- Generated visuals must not imply evidence that does not exist.
- If the product appearance is unknown, use placeholders and mark them in the build report.
- Remove supplier watermarks before production use where legally permitted.

---

# BUILD_06 — Offer Implementation

Implement the approved offer exactly.

Example:

```yaml
offer:
  single:
    quantity: 1
    price: "399 Kč"
  most_popular:
    quantity: 2
    price: "649 Kč"
  best_value:
    quantity: 3
    price: "799 Kč"
    bonus: "Approved bonus"
```

## Required invariant

```text
Displayed quantity
=
Cart quantity
=
Variant / line item
=
Displayed price
=
Checkout price
```

Validate:
- Savings math.
- Compare-at math.
- Currency formatting.
- Quantity selection.
- Add-to-cart state.
- Sold-out state.
- Variant compatibility.
- Mobile tap targets.

Never show a discount percentage that does not match the actual checkout economics.

---

# BUILD_07 — Localization

Localization is not translation-only.

```text
Approved Country
+
Native pain language
+
Local currency
+
Payment behavior
+
Shipping expectations
+
Return expectations
+
Local trust conventions
```

Localize:
- Headlines.
- Benefits.
- CTA wording.
- Currency.
- Number formatting.
- Shipping language.
- Returns language.
- FAQ.
- Contact wording.
- Payment wording.
- Product terminology.

### Rules
- Do not promise a payment method unless enabled.
- Do not promise local delivery speed unless verified.
- Do not use awkward machine-translated phrasing when native wording is available.
- Keep a canonical source locale and target locale files.

Recommended:

```text
locales/
├── en.default.json
├── de.json
├── cs.json
└── ...
```

---

# BUILD_08 — Technical Build

For Shopify, produce a modular theme implementation.

Minimum expectations:

```text
layout/theme.liquid
templates/index.json
templates/product.json
sections/*
snippets/*
assets/*
config/settings_schema.json
locales/*
```

## Technical rules
- Mobile first.
- Semantic HTML.
- Accessible controls.
- Keyboard-usable interactive elements.
- Alt-text support.
- Lazy-load below-the-fold media.
- Avoid blocking scripts.
- Avoid unnecessary JS frameworks.
- No console errors.
- No broken links.
- No duplicate IDs.
- Do not hardcode secrets or private API credentials.
- Keep third-party scripts minimal.

## Tracking hooks

Expose clean event points for downstream tracking:

```text
view_product
select_offer
add_to_cart
begin_checkout
purchase
```

Do not fabricate analytics success. Implement hooks and verify firing only when tooling is available.

---

# BUILD_09 — Automated QA

When tools are available, run all applicable checks.

Suggested static check:

```bash
shopify theme check
```

Check:
- Liquid syntax.
- Missing assets.
- Broken section references.
- Invalid settings.
- Translation gaps.
- JavaScript errors.
- CSS overflow risks.

Suggested preview flow:

```bash
shopify theme dev
```

Functional QA:

```text
Homepage loads
Product page loads
Offer selector works
Variant selector works
ATC works
Cart drawer works
Cart totals match
Checkout link works
FAQ works
Sticky CTA works
Policy links work
Locale works
Currency display works
```

Do not claim PASS without actually running the available check.

If a tool is unavailable, mark:

```text
NOT VERIFIED — TOOL UNAVAILABLE
```

not PASS.

---

# BUILD_10 — Visual + Conversion QA

When browser / screenshot / vision tooling is available:

```text
Generate
   ↓
Preview
   ↓
Open mobile
   ↓
Screenshot
   ↓
Critique
   ↓
Patch
   ↓
Screenshot again
   ↓
Repeat
```

Required viewports:

```text
Mobile: ~390px
Desktop: ~1440px
```

## Visual QA checklist

```text
No overflow
No clipped text
No broken media
Hero hierarchy is clear
CTA visible
Readable body text
Offer cards not cramped
Consistent spacing
No layout shift caused by missing dimensions
Footer usable
```

## Conversion QA checklist

```text
5-second clarity
Ad-message continuity
Pain is clear
Mechanism is clear
Proof is visible
Offer understandable
Trust friction addressed
Shipping expectation visible
Guarantee clear
CTA repeated naturally
Checkout path short
```

Prioritize strong Core Web Vitals and perceived speed. Do not promise exact load times without measuring the actual deployment.

---

# 4. Store Build Report

Every execution must end with a structured report.

```yaml
store_build_report:
  project:
    product:
    country:
    locale:

  build:
    status: COMPLETE | PARTIAL | BLOCKED
    theme_path:
    preview_url:

  qa:
    static:
      status:
      notes:
    mobile:
      status:
      notes:
    desktop:
      status:
      notes:
    cart:
      status:
      notes:
    checkout:
      status:
      notes:
    localization:
      status:
      notes:
    tracking_hooks:
      status:
      notes:

  unresolved:
    - item:
      severity:
      blocks_publish: true | false

  recommendation:
    READY_FOR_HUMAN_REVIEW | FIX_REQUIRED | BLOCKED
```

---

# 5. Preview Human Gate

Publishing is a **HARD HUMAN GATE**.

When implementation and QA are complete, present:

```text
STORE BUILD COMPLETE

Product:
Country:
Locale:

QA
- Static check: PASS
- Mobile: PASS
- Desktop: PASS
- Offer: PASS
- Cart: PASS
- Localization: PASS
- Tracking hooks: PASS

Open issues:
- ...

Recommendation:
READY TO PUBLISH

Choose:
[A] APPROVE & PUBLISH
[B] MODIFY STORE
[C] CHANGE OFFER
[D] CHANGE POSITIONING
[E] RETURN TO COMPETITION
```

Then STOP.

Do not publish because:
- the score is high,
- QA passed,
- the user previously said "go ahead" before seeing the preview,
- the project is marked GO.

The publish approval must apply to the current preview/build.

---

# 6. Backtracking Rules

## Country changed

Keep:

```text
Pain
Product
Product-level assets where reusable
```

Invalidate:

```text
Localization
Payment messaging
Shipping messaging
Localized copy
Country-specific policies
Country-specific offer display
Country-specific QA
Preview approval
```

## Offer changed

Invalidate:

```text
Offer section
Savings calculations
Cart / checkout QA
AOV-related copy
Preview approval
```

## Positioning changed

Invalidate:

```text
Hero
Pain/agitation copy
Benefits hierarchy
Comparison
Creative-message continuity
Preview approval
```

## Product changed

Invalidate the entire store build.

---

# 7. Base Theme Strategy

Prefer a reusable DTC base theme rather than rebuilding from zero.

Recommended reusable primitives:

```text
Header
Footer
Announcement bar
Cart drawer
Product form
Sticky ATC
Quantity break
Accordion
Comparison table
Before / after
Video block
Trust block
FAQ
Guarantee block
Responsive utilities
Performance utilities
Locale helpers
```

Then each new store becomes:

```text
Base Theme
+
Approved Store Blueprint
+
Product Data
+
Country Localization
+
Approved Offer
+
Real Assets
=
Store Build
```

---

# 8. Output Artifacts

When building a store, produce as many of these as the environment supports:

```text
store-build/
├── theme/
├── design-system.md
├── information-architecture.md
├── asset-manifest.yaml
├── copy-deck.md
├── qa-report.md
├── build-report.json
└── preview-notes.md
```

If code execution is not available, output the complete implementation plan and code artifacts that can be written in the current environment.

Never claim a store was deployed if only code was generated.

---

# 9. Orchestrator Contract

Recommended workflow placement:

```text
01 pain-point-research
02 winning-product-research
03 market-validation
04 competition-intelligence
05 store-conversion-engine
06 ai-store-builder
07 ecom-growth-engine
08 scale-operations-engine
```

Required transition:

```text
competition-intelligence
    ↓
HUMAN GO APPROVAL
    ↓
store-conversion-engine
    ↓
STORE BLUEPRINT
    ↓
HUMAN BLUEPRINT APPROVAL
    ↓
ai-store-builder
    ↓
PREVIEW + QA
    ↓
HUMAN PUBLISH APPROVAL
    ↓
ecom-growth-engine
```

Orchestrator stage definition:

```yaml
ai_store_builder:
  type: EXECUTION
  prerequisites:
    - pain.approved
    - product.approved
    - country.approved
    - competition.approved
    - store_blueprint.approved

  decision:
    build_implementation: AUTO
    technical_fixes: AUTO
    unresolved_business_input: HUMAN_REQUIRED
    publish: HUMAN_REQUIRED

  outputs:
    - store_build_report
    - preview
    - qa_report
    - artifacts
```

---

# 10. Final Rule

The success condition of this skill is **not**:

> "AI generated a Shopify theme."

The success condition is:

> **An approved business blueprint has been converted into a modular, localized, technically valid, conversion-oriented, QA-reviewed storefront that is ready for human preview — and nothing is published until the user explicitly approves the current build.**
