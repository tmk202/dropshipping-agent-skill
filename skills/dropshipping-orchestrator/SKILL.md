---
name: dropshipping-orchestrator
description: Meta-skill that coordinates all dropshipping research, enrichment, and execution skills as a gated state machine. Routes each stage, enforces human approval for business decisions, supports backtracking, confidence-based research expansion, and standardized decision outputs.
---

# Dropshipping Orchestrator

## Mission
Coordinate the specialist skills in this repository. This skill MUST NOT duplicate specialist research knowledge. Its job is to control **what runs next, what input is passed, when the agent may proceed automatically, and when the user must approve a business decision**.

## Core rule
> **Agent decides implementation. Human decides business commitments.**

The agent may autonomously choose queries, sources, deduplication strategy, clustering depth, scoring calculations, evidence collection, code structure, component hierarchy, supplier enrichment payloads, and integration plumbing. The agent MUST pause for explicit user approval at business decision gates and authorization gates.

## Stage machine (10 Stages)

| Stage | Specialist skill | Decision type | Next stage |
|---|---|---|---|
| 01 Pain Discovery | `pain-point-research` | `HUMAN_REQUIRED` | 02 |
| 02 Product Selection | `winning-product-research` | `HUMAN_REQUIRED` | 03 |
| 03 Country Selection | `market-validation` | `HUMAN_REQUIRED` | 04 |
| 04 Competition Intelligence | `competition-intelligence` | `AUTO` research + `HUMAN_REQUIRED` Go/No-Go | 05 |
| 05 Supplier Discovery & Enrichment | `supplier-product-discovery-enrichment` | `AUTH_REQUIRED` CJ API + `HUMAN_REQUIRED` Select CJ PID | 06 |
| 06 Store & Offer Blueprint | `store-conversion-engine` | `HUMAN_REQUIRED` for positioning/offer blueprint | 07 |
| 07 AI Store Builder | `ai-store-builder` | `AUTO` build/QA + `HUMAN_REQUIRED` Preview approval | 08 |
| 08 Shopify Commerce Deployer | `shopify-commerce-deployer` | `AUTO` sync/deploy + `AUTH_REQUIRED` + `HUMAN_REQUIRED` Publish | 09 |
| 09 Creative / Tracking / Paid Test | `ecom-growth-engine` | `HUMAN_REQUIRED` for test budget and launch | 10 |
| 10 Scale / Operations | `scale-operations-engine` | `HUMAN_REQUIRED` for material scale, supplier, pricing or operational commitments | loop |

## Stage lock
Never run a downstream stage when its prerequisite business decision is not approved.

```text
IF previous_stage.approved != true
THEN next_stage.status = LOCKED
```

Examples:
- No product research commitment until a pain has been approved.
- No country competition scan until a country has been approved.
- No supplier enrichment until Go/No-Go has been approved.
- No store blueprint commitment until Supplier Product Pack & Economics are grounded.
- No store code build until Store Blueprint has been approved.
- No Shopify commerce deployment until Store Build preview has been approved.
- No paid launch until Store preview, live deployment & test budget have been approved.

## Decision types

### AUTO
The agent may complete the work and proceed if no business commitment is created.

### AUTH_REQUIRED
The agent MUST prompt the user for secure credentials (CJ API key, Shopify Admin API token / Dev App) and verify connectivity before proceeding.

### HUMAN_REQUIRED
The agent MUST present options, recommendation, evidence, risks and wait for the user to choose.

### HUMAN_IF_AMBIGUOUS
The agent may proceed only when one option is clearly superior **and** the action is reversible and non-material. Otherwise pause for approval.

## Confidence policy

```text
confidence >= 85  -> present recommendation normally
confidence 65-84  -> present recommendation + uncertainty
confidence < 65   -> RESEARCH_MORE before asking user to decide
```

Low confidence is not permission to guess.

## Mandatory human gates
The following MUST never be silently committed by the agent:
- final pain selection
- final product selection
- target country selection
- Go / Conditional-Go / No-Go
- supplier product selection (if multiple distinct candidate products exist)
- brand positioning & store blueprint
- store preview sign-off
- Shopify & Supplier credential authorizations (`AUTH_REQUIRED`)
- live store publishing / theme activation (`HUMAN_REQUIRED`)
- final launch offer & retail pricing when it materially affects economics
- supplier payment approval (unless `AUTO_WITH_LIMITS` policy set)
- paid test budget
- major price change
- supplier switch
- material scaling decision
- entering a new country with spend

## Decision Gate output contract
At every human gate, output this structure:

```markdown
## DECISION REQUIRED
**Stage:** <stage id and name>
**Context:** <selected upstream pain/product/country>

### Options
[A] <option>
- Score: <0-100 if applicable>
- Confidence: <LOW|MEDIUM|HIGH>
- Why: <2-4 evidence-backed bullets>
- Risks: <1-3 bullets>

[B] <option>
...

### Recommendation
**Recommended:** <A/B/...>
**Reason:** <short risk-adjusted rationale>

### Choose
`A` · `B` · `C` · `COMPARE A B` · `RESEARCH MORE` · `GO BACK`
```

After emitting a Decision Gate, STOP. Do not execute the next stage in the same turn unless the user has already explicitly chosen that option in the current request.

## Stage-specific gates

### Stage 01 — Pain Discovery
Specialist returns a ranked shortlist. The orchestrator presents 3-5 strongest pains. User chooses one pain or requests deeper research.

### Stage 02 — Product Selection
Input MUST include the approved pain. Present 3-5 candidate products when possible. User selects the product.

### Stage 03 — Country Selection
Input MUST include approved pain + product. Present the strongest target countries with local evidence and risk-adjusted scores.

### Stage 04 — Competition + Go/No-Go
Competition research itself runs automatically once the country is approved. The resulting launch verdict is a hard gate (`GO` / `CONDITIONAL GO` / `NO-GO`).

### Stage 05 — Supplier Product Discovery & Enrichment
Specialist (`supplier-product-discovery-enrichment`) operates in two sequential phases:
1. **Discovery Phase:** Searches CJ catalog based on the approved Product Concept and Country. Ranks candidate supplier products and presents top options to the user. `HUMAN_REQUIRED` gate to select the exact CJ Product (PID).
2. **Enrichment Phase (Locked until PID chosen):** Queries CJ API to extract exact PID, VIDs, real unit costs, inventory stock, warehouse locations, shipping line estimates (freight report), high-res product photos, variant photos, demo videos, and runs automated Media QA.
- **Outputs:** `supplier_product_pack`, `store_asset_pack`, `freight_report`, `enrichment_report`.
- **Downstream impact:** Constrains Store Conversion Engine, AI Store Builder, and Shopify Commerce Deployer with ground-truth supplier data.

### Stage 06 — Store & Offer Blueprint
Specialist (`store-conversion-engine`) consumes the Supplier Product Pack and Store Asset Pack. Autonomously generates brand positioning drafts, 13-stage visual sales narrative, quantity break offer ladders, and structured `store-blueprint.json`. User approval is required before locking positioning, pricing/offer or checkout strategy.

### Stage 07 — AI Store Builder
Specialist (`ai-store-builder`) autonomously generates clean production code, component hierarchy, responsive styling, accessibility, performance optimization, and technical QA checks based on the approved Store Blueprint and Store Asset Pack. Outputs store build report, live preview, and QA report. User approval required on preview before commerce deployment.

### Stage 08 — Shopify Commerce Deployer
Specialist (`shopify-commerce-deployer`) operates under **HYBRID_CJ_API_APP** architecture:
- **Shopify API:** Deploys store theme, creates draft products, sets approved retail pricing, brand content, and metafields.
- **CJ API:** Reuses Supplier Product Pack to discover/verify product inside CJ and create product connections without duplicate research.
- **CJ Shopify App:** Handles runtime inventory sync, order routing, fulfillment, and tracking sync (Single-Owner principle).
- **Gates:**
  - `AUTH_REQUIRED`: Shopify Dev Dashboard App (Client ID/Secret) & CJ API Key
  - `HUMAN_REQUIRED`: Retail price confirmation, Test-order verification, and Live Theme Publish Gate
  - `HUMAN_IF_AMBIGUOUS`: Variant/SKU mapping ambiguity resolution

### Stage 09 — Creative / Tracking / Paid Test
Specialist (`ecom-growth-engine`) autonomously handles Creative Matrix generation (Hooks/Angles) and tracking QA (Pixel/CAPI). Actual paid launch requires approved budget, country, offer, live store readiness and tracking verification.

### Stage 10 — Scale / Operations
Specialist (`scale-operations-engine`) handles performance analytics, creative factory loops, and supplier SLAs automatically. Material spend increases, supplier switches, major price changes, new market expansion and other consequential operational commitments require user approval.

## Backtracking
The workflow is not one-way. When the user changes an upstream decision, invalidate dependent downstream stages.

```text
Change pain         -> invalidate product, country, competition, enrichment, store blueprint, store build, deployment, growth, scale
Change product      -> invalidate country, competition, enrichment, store blueprint, store build, Shopify product mapping, deployment, growth, scale
Change country      -> invalidate competition, localized store blueprint, store build, deployment, localized creative, paid test, scale
Change positioning  -> invalidate hero/copy hierarchy, store blueprint, store build & preview approval, theme deploy
Change offer        -> invalidate offer UI, savings math, Shopify product variant pricing, cart/checkout QA, store preview, creative claims, paid test assumptions
```

Never rerun unaffected upstream research unnecessarily.

## State management
Persist workflow state using `project-state.json` following `project-state.schema.json` in this skill folder.

Before doing work:
1. Read state.
2. Identify current stage.
3. Verify prerequisites.
4. Route to the correct specialist.
5. Validate specialist output.
6. Update state.
7. Either continue automatically or emit a Decision Gate.

## User input handling
Interpret concise answers such as `A`, `Germany`, `GO`, `2`, or `compare A B` against the active pending decision.

If a user changes direction, update state and backtrack instead of forcing the old path.
