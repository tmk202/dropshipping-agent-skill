# 🚀 Dropshipping & DTC Agent Skills Suite

> A complete, production-grade **Autonomous DTC AI Framework** governed by the **Dropshipping Orchestrator (Gated State Machine)** across the **6-Phase Closed-Loop Architecture (DISCOVER $\rightarrow$ BUILD $\rightarrow$ VALIDATE $\rightarrow$ GROW $\rightarrow$ DEFEND $\rightarrow$ LEARN)**.

---

## 🧭 META-SKILL: DROPSHIPPING ORCHESTRATOR

The system is coordinated by **[`skills/dropshipping-orchestrator`](./skills/dropshipping-orchestrator/SKILL.md)** under the fundamental rule:

> **Agent decides implementation. Human decides business commitments.**

The agent autonomously handles queries, sources, deduplication, semantic clustering, supplier intelligence, scoring, code implementation, and integration plumbing, but **MUST pause for explicit human approval at key business decision gates and credential authorizations**.

---

## 🚦 STAGE MACHINE & GATED WORKFLOW (10 STAGES)

| Stage | Specialist Skill | Decision Type | Next Stage |
|---|---|---|---|
| **01. Pain Discovery** | `pain-point-research` | `HUMAN_REQUIRED` | 02 |
| **02. Product Selection** | `winning-product-research` | `HUMAN_REQUIRED` | 03 |
| **03. Country Selection** | `market-validation` | `HUMAN_REQUIRED` | 04 |
| **04. Competition Intelligence** | `competition-intelligence` | `AUTO` Research + `HUMAN_REQUIRED` Go/No-Go | 05 |
| **05. Supplier Discovery & Enrichment** | `supplier-product-discovery-enrichment` | `AUTH_REQUIRED` CJ API + `HUMAN_REQUIRED` Select CJ PID | 06 |
| **06. Store & Offer Blueprint** | `store-conversion-engine` | `HUMAN_REQUIRED` for positioning/offer blueprint | 07 |
| **07. AI Store Builder** | `ai-store-builder` | `AUTO` Build/QA + `HUMAN_REQUIRED` Preview approval | 08 |
| **08. Shopify Commerce Deployer** | `shopify-commerce-deployer` | `AUTO` Sync/Deploy + `AUTH_REQUIRED` + `HUMAN_REQUIRED` Publish | 09 |
| **09. Creative / Tracking / Paid Test** | `ecom-growth-engine` | `HUMAN_REQUIRED` for test budget and launch | 10 |
| **10. Scale / Operations** | `scale-operations-engine` | `HUMAN_REQUIRED` for material scale, supplier, or expansion | Loop |

```text
[01. SĂN NỖI ĐAU]                      --> skills/pain-point-research                [HUMAN_REQUIRED]
        |
        v
[02. CHỌN SẢN PHẨM WIN]                --> skills/winning-product-research           [HUMAN_REQUIRED]
        |
        v
[03. CHỌN QUỐC GIA MỤC TIÊU]           --> skills/market-validation                  [HUMAN_REQUIRED]
        |
        v
[04. RÀ QUÉT ĐỐI THỦ & GO/NO-GO]       --> skills/competition-intelligence           [AUTO + HUMAN GO/NO-GO]
        |
        v
[05. TÌM & LÀM GIÀU DỮ LIỆU CJ]        --> skills/supplier-product-discovery-enrichment [AUTH CJ + CHỌN PID + AUTO ENRICH]
        |
        v
[06. STORE BLUEPRINT & OFFER]          --> skills/store-conversion-engine            [HUMAN_REQUIRED BLUEPRINT]
        |
        v
[07. TỰ ĐỘNG BUILD CODE STORE]        --> skills/ai-store-builder                   [AUTO BUILD + HUMAN PREVIEW]
        |
        v
[08. DEPLOY & KẾT NỐI SHOPIFY ↔ CJ]   --> skills/shopify-commerce-deployer          [AUTH + HUMAN PUBLISH GATE]
        |
        v
[09. TẠO AD, TEST & ĐO PHỄU]           --> skills/ecom-growth-engine                 [HUMAN_REQUIRED BUDGET]
        |
        v
[10. VẬN HÀNH, DEFEND & SCALE]         --> skills/scale-operations-engine            [HUMAN_REQUIRED SCALE]
```

---

## 📂 Danh Sách Toàn Bộ Skills Trong Kho

| Danh mục | Skill | Đường dẫn | Mục đích cốt lõi |
| :--- | :--- | :--- | :--- |
| 👑 **Orchestrator** | **`dropshipping-orchestrator`** | [`skills/dropshipping-orchestrator/`](./skills/dropshipping-orchestrator/SKILL.md) | **Meta-skill điều phối toàn bộ workflow**: Kiểm soát Stage Lock, Gated Decisions, Backtracking, Confidence Policy. |
| 🔬 **Stage 01** | **`pain-point-research`** | [`skills/pain-point-research/`](./skills/pain-point-research/SKILL.md) | Săn tìm ngôn ngữ nỗi đau, phân cụm semantic, bóc tách review 1–3★ tìm *Solution Gap*. |
| 📦 **Stage 02** | **`winning-product-research`** | [`skills/winning-product-research/`](./skills/winning-product-research/SKILL.md) | 20 tiêu chí lọc sản phẩm win, Scorecard 100 điểm, Unit margin $\ge 70\%$, logistics an toàn. |
| 🌍 **Stage 03** | **`market-validation`** | [`skills/market-validation/`](./skills/market-validation/SKILL.md) | Ma trận $\text{Product} \times \text{Pain} \times \text{Country}$, *Geographic Arbitrage*, cổng thanh toán bản địa. |
| 🔍 **Stage 04** | **`competition-intelligence`** | [`skills/competition-intelligence/`](./skills/competition-intelligence/SKILL.md) | 7 tầng quét đối thủ, Ad longevity, Price map, tìm **Angle Gap** & tạo báo cáo **"WHY NOW?" Go/No-Go**. |
| 🧬 **Stage 05** | **`supplier-product-discovery-enrichment`** | [`skills/supplier-product-discovery-enrichment/`](./skills/supplier-product-discovery-enrichment/SKILL.md) | Tìm kiếm & so sánh sản phẩm trên CJ $\rightarrow$ Duyệt PID $\rightarrow$ Bóc tách VID, giá vốn, kho hàng, Media QA. |
| 🏪 **Stage 06** | **`store-conversion-engine`** | [`skills/store-conversion-engine/`](./skills/store-conversion-engine/SKILL.md) | Hệ thống Store chuyển đổi cao (10 tiêu chuẩn DTC), Visual Sales Letter 13 tầng, Offer Ladder, Blueprint JSON. |
| 🛠️ **Stage 07** | **`ai-store-builder`** | [`skills/ai-store-builder/`](./skills/ai-store-builder/SKILL.md) | Tự động sinh mã nguồn Store chuẩn DTC, component hierarchy, responsive UX, Technical QA & Preview. |
| 🔌 **Stage 08** | **`shopify-commerce-deployer`** | [`skills/shopify-commerce-deployer/`](./skills/shopify-commerce-deployer/SKILL.md) | Kết nối Shopify & Supplier, map variants, tạo sản phẩm, deploy theme unpublished, sync tồn kho & tracking. |
| 🚀 **Stage 09** | **`ecom-growth-engine`** | [`skills/ecom-growth-engine/`](./skills/ecom-growth-engine/SKILL.md) | Creative Matrix (25–50 concepts), Tracking QA, Paid Testing có kiểm soát, Chẩn đoán phễu (Case A–E). |
| 🛡️ **Stage 10** | **`scale-operations-engine`** | [`skills/scale-operations-engine/`](./skills/scale-operations-engine/SKILL.md) | Private Agent 3PL, Creative Factory hàng tuần, Niche Brand Evolution, Vòng lặp học tập khép kín. |

---

## 🚀 Cách Cài Đặt Vào AI Agent

### Dành cho Antigravity / Gemini IDE:
```bash
mkdir -p .agents/skills
cp -r skills/* .agents/skills/
```

### Dành cho Hermes Agent CLI:
```bash
mkdir -p ~/.hermes/skills
cp -r skills/* ~/.hermes/skills/
```

---

## 📄 License
MIT License. Được thiết kế chuyên biệt cho hệ thống tự động hóa nghiên cứu và vận hành DTC / Dropshipping tốc độ cao.
