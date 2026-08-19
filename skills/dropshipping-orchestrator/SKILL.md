---
name: dropshipping-orchestrator
description: Meta-skill that coordinates all dropshipping research and execution skills as a gated state machine. Routes each stage, enforces human approval for business decisions, supports backtracking, confidence-based research expansion, and standardized decision outputs.
---

# Dropshipping Orchestrator

## Mission
Coordinate the specialist skills in this repository. This skill MUST NOT duplicate specialist research knowledge. Its job is to control **what runs next, what input is passed, when the agent may proceed automatically, and when the user must approve a business decision**.

## Core rule
> **Agent decides implementation. Human decides business commitments.**

The agent may autonomously choose queries, sources, deduplication strategy, clustering depth, scoring calculations, evidence collection, and research depth. The agent MUST pause for explicit user approval at business decision gates.

## Stage machine

| Stage | Specialist skill | Decision type | Next stage |
|---|---|---|---|
| 01 Pain Discovery | `pain-point-research` | `HUMAN_REQUIRED` | 02 |
| 02 Product Selection | `winning-product-research` | `HUMAN_REQUIRED` | 03 |
| 03 Country Selection | `market-validation` | `HUMAN_REQUIRED` | 04 |
| 04 Competition Intelligence | `competition-intelligence` | `AUTO` research + `HUMAN_REQUIRED` Go/No-Go | 05 |
| 05 Store & Offer | `store-conversion-engine` | `HUMAN_REQUIRED` for positioning/offer commitment | 06 |
| 06 Creative / Tracking / Paid Test | `ecom-growth-engine` | `HUMAN_REQUIRED` for test budget and launch | 07 |
| 07 Scale / Operations | `scale-operations-engine` | `HUMAN_REQUIRED` for material scale, supplier, pricing or operational commitments | loop |

## Stage lock
Never run a downstream stage when its prerequisite business decision is not approved.

```text
IF previous_stage.approved != true
THEN next_stage.status = LOCKED
```

Examples:
- No product research commitment until a pain has been approved.
- No country competition scan until a country has been approved.
- No store build commitment until Go/No-Go has been approved.
- No paid launch until test budget has been approved.

## Decision types

### AUTO
The agent may complete the work and proceed if no business commitment is created.

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
- brand positioning
- final launch offer when it materially affects economics
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
Specialist returns a ranked shortlist. The orchestrator should normally present 3-5 strongest pains, not hundreds of raw candidates.

User chooses one pain or requests deeper research.

### Stage 02 — Product Selection
Input MUST include the approved pain. Present 3-5 candidate products when possible. User selects the product.

### Stage 03 — Country Selection
Input MUST include approved pain + product. Present the strongest target countries with local evidence and risk-adjusted scores.

The orchestrator MUST NOT silently choose the top-scoring country.

### Stage 04 — Competition + Go/No-Go
Competition research itself may run automatically once the country is approved. The resulting launch verdict is a hard gate.

Valid user choices:
- `GO` -> unlock Store & Offer
- `CONDITIONAL GO` -> record conditions, then unlock only if accepted
- `RESEARCH DEEPER`
- `CHANGE COUNTRY`
- `CHANGE PRODUCT`
- `NO-GO`

### Stage 05 — Store & Offer
The specialist may autonomously generate drafts, structures and recommendations. User approval is required before locking material positioning, price/offer or checkout strategy.

### Stage 06 — Creative / Tracking / Paid Test
Creative generation and tracking QA may be automatic. Actual paid launch requires approved budget, country, offer and tracking readiness.

### Stage 07 — Scale / Operations
Analysis is automatic. Material spend increases, supplier switches, major price changes, new market expansion and other consequential operational commitments require user approval.

## Backtracking
The workflow is not one-way. When the user changes an upstream decision, invalidate dependent downstream stages.

```text
Change pain     -> invalidate product, country, competition, store, growth, scale
Change product  -> invalidate country, competition, store, growth, scale
Change country  -> invalidate competition, localized store, localized creative, paid test, scale
Change offer    -> invalidate relevant store economics, creative claims, paid test assumptions
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

## Anti-patterns
Do NOT:
- ask the user to choose implementation details the agent can decide itself
- dump hundreds of raw findings before ranking them
- proceed past a human gate because one score is marginally higher
- confuse research score with proof of a winner
- silently change product, country, price, supplier or budget
- duplicate the content of specialist skills inside this orchestrator

## Success condition
The user should always know:
1. what stage is active,
2. what has already been approved,
3. what the agent is doing automatically,
4. what decision is required from the user,
5. what becomes unlocked after that decision.
