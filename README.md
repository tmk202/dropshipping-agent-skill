# 🚀 Dropshipping & DTC Agent Skills Suite

> A complete, production-grade **Autonomous DTC AI Framework** governed by the **Dropshipping Orchestrator (Gated State Machine)** across the **6-Phase Closed-Loop Architecture (DISCOVER $\rightarrow$ BUILD $\rightarrow$ VALIDATE $\rightarrow$ GROW $\rightarrow$ DEFEND $\rightarrow$ LEARN)**.

---

## 🧭 META-SKILL: DROPSHIPPING ORCHESTRATOR

The system is coordinated by **[`skills/dropshipping-orchestrator`](./skills/dropshipping-orchestrator/SKILL.md)** under the fundamental rule:

> **Agent decides implementation. Human decides business commitments.**

The agent autonomously handles queries, sources, deduplication, semantic clustering, scoring, and deep research, but **MUST pause for explicit human approval at key business decision gates**.

---

## 🚦 STAGE MACHINE & GATED WORKFLOW

| Stage | Specialist Skill | Decision Type | Next Stage |
|---|---|---|---|
| **01. Pain Discovery** | `pain-point-research` | `HUMAN_REQUIRED` | 02 |
| **02. Product Selection** | `winning-product-research` | `HUMAN_REQUIRED` | 03 |
| **03. Country Selection** | `market-validation` | `HUMAN_REQUIRED` | 04 |
| **04. Competition Intelligence** | `competition-intelligence` | `AUTO` Research + `HUMAN_REQUIRED` Go/No-Go | 05 |
| **05. Store & Offer** | `store-conversion-engine` | `HUMAN_REQUIRED` for positioning/offer commitment | 06 |
| **06. Creative / Tracking / Paid Test** | `ecom-growth-engine` | `HUMAN_REQUIRED` for test budget and launch | 07 |
| **07. Scale / Operations** | `scale-operations-engine` | `HUMAN_REQUIRED` for material scale, supplier, or expansion | Loop |

```
[BƯỚC 1: SĂN NỖI ĐAU]                 --> skills/pain-point-research       [HUMAN_REQUIRED]
        |
        v
[BƯỚC 2: CHỌN SẢN PHẨM WIN]           --> skills/winning-product-research  [HUMAN_REQUIRED]
        |
        v
[BƯỚC 3: CHỌN QUỐC GIA MỤC TIÊU]      --> skills/market-validation         [HUMAN_REQUIRED]
        |
        v
[BƯỚC 4: RÀ QUÉT ĐỐI THỦ & GO/NO-GO]  --> skills/competition-intelligence  [AUTO + HUMAN GO/NO-GO]
        |
        v
[BƯỚC 5: DỰNG STORE & OFFER]          --> skills/store-conversion-engine   [HUMAN_REQUIRED]
        |
        v
[BƯỚC 6: TẠO AD, TEST & ĐO PHỄU]      --> skills/ecom-growth-engine        [HUMAN_REQUIRED BUDGET]
        |
        v
[BƯỚC 7: VẬN HÀNH, DEFEND & SCALE]    --> skills/scale-operations-engine   [HUMAN_REQUIRED SCALE]
```

---

## 🧪 Real-World Case Study
👉 **Xem báo cáo thực tế áp dụng trọn vẹn 7 bước cho sản phẩm Gôm Tẩy Cặn Vôi (CZ & DE):**  
**[`examples/limescale-eraser-case-study.md`](./examples/limescale-eraser-case-study.md)** *(Điểm Cơ Hội 96/100, 0 Ads Competition tại Séc, Margin 80%)*.

---

## 📂 Danh Sách Toàn Bộ Skills Trong Kho

| Danh mục | Skill | Đường dẫn | Mục đích cốt lõi |
| :--- | :--- | :--- | :--- |
| 👑 **Orchestrator** | **`dropshipping-orchestrator`** | [`skills/dropshipping-orchestrator/`](./skills/dropshipping-orchestrator/SKILL.md) | **Meta-skill điều phối toàn bộ workflow**: Kiểm soát Stage Lock, Gated Decisions, Backtracking, Confidence Policy. |
| 🔬 **Stage 01** | **`pain-point-research`** | [`skills/pain-point-research/`](./skills/pain-point-research/SKILL.md) | Săn tìm ngôn ngữ nỗi đau, phân cụm semantic, bóc tách review 1–3★ tìm *Solution Gap*. |
| 📦 **Stage 02** | **`winning-product-research`** | [`skills/winning-product-research/`](./skills/winning-product-research/SKILL.md) | 20 tiêu chí lọc sản phẩm win, Scorecard 100 điểm, Unit margin $\ge 70\%$, logistics an toàn. |
| 🌍 **Stage 03** | **`market-validation`** | [`skills/market-validation/`](./skills/market-validation/SKILL.md) | Ma trận $\text{Product} \times \text{Pain} \times \text{Country}$, *Geographic Arbitrage*, cổng thanh toán bản địa. |
| 🔍 **Stage 04** | **`competition-intelligence`** | [`skills/competition-intelligence/`](./skills/competition-intelligence/SKILL.md) | 7 tầng quét đối thủ, Ad longevity, Price map, tìm **Angle Gap** & tạo báo cáo **"WHY NOW?" Go/No-Go**. |
| 🏪 **Stage 05** | **`store-conversion-engine`** | [`skills/store-conversion-engine/`](./skills/store-conversion-engine/SKILL.md) | Hệ thống Store chuyển đổi cao (10 tiêu chuẩn DTC), Visual Sales Letter 13 tầng, Offer Ladder. |
| 🚀 **Stage 06** | **`ecom-growth-engine`** | [`skills/ecom-growth-engine/`](./skills/ecom-growth-engine/SKILL.md) | Creative Matrix (25–50 concepts), Tracking QA, Paid Testing có kiểm soát, Chẩn đoán phễu (Case A–E). |
| 🛡️ **Stage 07** | **`scale-operations-engine`** | [`skills/scale-operations-engine/`](./skills/scale-operations-engine/SKILL.md) | Private Agent 3PL, Creative Factory hàng tuần, Niche Brand Evolution, Vòng lặp học tập khép kín. |

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
