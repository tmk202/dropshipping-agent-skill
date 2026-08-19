---
name: ecom-growth-engine
description: End-to-End DTC Growth, Creative Intelligence, Paid Testing & Scaling Engine. Guides exact step-by-step execution from Voice-of-Customer creative matrix generation, full-funnel tracking QA, controlled paid testing, funnel diagnostics, Kill/Iterate/Scale decision rules, to horizontal geo-expansion and closed-loop feedback learning.
---

# End-to-End DTC Growth & Execution Engine (10-Engine Master System)

> **Nguyên tắc tối thượng:**  
> Research tìm ra thứ **"có vẻ sẽ win"**.  
> Testing tìm ra thứ **"thực sự win"**.  
> Scaling tìm xem **"winner lớn đến đâu"**.  
> Feedback Loop giúp lần research tiếp theo **thông minh hơn lần trước**.

$$\mathbf{Pain \rightarrow Product \rightarrow Country \rightarrow Store/Offer \rightarrow Creative \rightarrow Tracking \rightarrow Paid\ Test \rightarrow Analyze \rightarrow Iterate \rightarrow Scale \rightarrow Learn}$$

```
+-----------------------------------------------------------------------------------------------------------------------+
|                                    10-ENGINE CLOSED-LOOP ARCHITECTURE                                                 |
|                                                                                                                       |
|  [01 Pain Discovery] -> [02 Winning Product] -> [03 Country Geo] -> [04 Store & Offer]                                |
|                                                                             |                                         |
|                                                                             v                                         |
|  [08 Decision Engine] <- [07 Paid Testing]   <- [06 Tracking QA] <- [05 Creative Intelligence]                        |
|        |                                                                                                              |
|        +---> [KILL]    (Tắt ngay, không lãng phí ngân sách)                                                           |
|        +---> [ITERATE] (Giữ Hook mạnh -> Đổi Body / Offer / Landing Page)                                             |
|        +---> [SCALE]   -> [09 Scale & Retention] -> [10 Feedback Learning Engine] ------------------------------------+
|                                                                                               (Học dữ liệu trả về 01) |
+-----------------------------------------------------------------------------------------------------------------------+
```

---

## 🎯 BẮT ĐẦU TỪ ĐÂU? (PLAYBOOK THỰC THI STEP-BY-STEP KHI CÓ SẢN PHẨM MỚI)

Khi đã chọn được tổ hợp **$\text{Product} \times \text{Pain} \times \text{Country}$**, không bao giờ bật Ads bừa bãi. Thực thi tuần tự theo 6 bước:

---

### BƯỚC 1: XÂY DỰNG CREATIVE MATRIX (TỪ VOICE OF CUSTOMER)

Không nghĩ: *1 sản phẩm = 1 video*.  
Luôn nghĩ: **1 Sản phẩm $\rightarrow$ Nhiều Nỗi đau $\rightarrow$ 5–10 Angles $\rightarrow$ Mỗi Angle 5 Hooks $\rightarrow$ 50 Concepts.**

#### 1. Lấy Hook trực tiếp từ dữ liệu Pain Engine (Reddit/Amazon/TikTok):
* *Khách nói:* "I've vacuumed my couch 3 times and the pet hair is STILL stuck."  
  $\rightarrow$ *Ad Hook 1s:* **"Vacuumed your couch 3 times and the hair is STILL stuck?"**
* *Khách nói:* "Vinegar just runs down the glass and leaves white cloudy spots."  
  $\rightarrow$ *Ad Hook 1s:* **"Stop spraying vinegar on your shower glass. It's doing nothing."**

#### 2. Phân tầng 5 Angles cốt lõi:
* **Angle A (Frustration / Bế tắc):** "I tried everything to clean X until THIS happened."
* **Angle B (Speed / Tiết kiệm thời gian):** "The 30-second way to completely remove X."
* **Angle C (Comparison / So sánh trực tiếp):** Ordinary Solution (Thất bại) vs New Solution (Sạch bong).
* **Angle D (Money / Tiết kiệm chi phí):** "Stop wasting $50 every month on disposable refills."
* **Angle E (Discovery / Bất ngờ):** "I didn't know this existed until last week."

#### 3. Thiết lập Creative Matrix trước khi bấm máy:
```markdown
| Creative ID | Pain Target | Angle | Hook (1–3s đầu) | Visual Format |
| :---: | :--- | :--- | :--- | :--- |
| **C01** | Cặn vôi kính | Frustration | "Xịt nước tẩy chỉ chảy tuột..." | Before / After cận cảnh |
| **C02** | Cặn vôi kính | Comparison | Bình xịt Savo vs Cục gôm Nano | Split-screen so sánh |
| **C03** | Vòi nước xỉn | Speed | "Làm mới vòi inox trong đúng 15 giây" | Satisfying Demo nhanh |
| **C04** | Đáy nồi cháy | Money | "Đừng vứt chảo cháy đi..." | Problem -> Payoff |
| **C05** | Nhà tắm mờ | Discovery | "Mẹo dọn nhà tắm của người Séc" | Native UGC kể chuyện |
```

---

### BƯỚC 2: TRACKING QA & VERIFY TOÀN BỘ PHỄU

**Tuyệt đối không bật ads trước khi test order thành công.**

#### Checklist kiểm tra:
* [ ] **Pixel & CAPI:** Cài đặt Meta Pixel + Conversion API (Deduped).
* [ ] **Tự đặt 1 Test Order thật:** Đi trọn vẹn: $\text{Ad click} \rightarrow \text{LP} \rightarrow \text{ATC} \rightarrow \text{Initiate Checkout} \rightarrow \text{Test Card/Dobírka} \rightarrow \text{Thank You Page}$.
* [ ] **Kiểm tra 5 Event chuẩn:** `ViewContent`, `AddToCart`, `InitiateCheckout`, `AddPaymentInfo`, `Purchase` (Kiểm tra đúng số tiền và tiền tệ bản địa: EUR / CZK).
* [ ] **Cấu trúc UTM Link chuẩn:**
  `?utm_source=meta&utm_medium=cpc&utm_campaign={{campaign.name}}&utm_content={{ad.name}}`

---

### BƯỚC 3: LAUNCH PAID TESTING (CONTROLLED BUDGET)

* **Cấu trúc chiến dịch Test:**
  * 1 Campaign CBO / ABO (Tối ưu Purchase).
  * 3–5 Ad Sets (Broad / Interest ngách bản địa).
  * Mỗi Ad Set chứa **2–3 Creatives** (Tổng batch đầu: **6–10 creatives**).
* **Ngân sách Test:** Đặt ngân sách bằng $2 - 3\times \text{Target CAC}$ cho mỗi Ad Set trong 48–72h để tìm tín hiệu.
* **Mục tiêu giai đoạn 1:** Tìm kiếm **Creative–Market Fit**, chưa phải tối đa hóa lợi nhuận.

---

### BƯỚC 4: CHẨN ĐOÁN PHỄU (FUNNEL DIAGNOSTICS)

Đọc toàn bộ hành trình phễu, **không kết luận sản phẩm fail chỉ vì ROAS thấp**:

```markdown
+---------------------------------------------------------------------------------------------------+
| BẢNG CHẨN ĐOÁN VÀ ĐIỀU TRỊ ĐIỂM GÃY PHỄU                                                          |
|                                                                                                   |
| [Case A] CTR thấp (<1.2%), CPC cao ($>1.5)                                                        |
|   -> NGUYÊN NHÂN: Hook yếu, Thumbnail chán, sai tệp audience.                                     |
|   -> XỬ LÝ: Thay 3 giây đầu của Video (Đổi Hook), không sửa Landing Page.                         |
|                                                                                                   |
| [Case B] CTR cao (>2.5%), CPC rẻ, nhưng ATC thấp (<3%)                                            |
|   -> NGUYÊN NHÂN: Gãy liên kết thông điệp (Ad hứa 1 đằng, Web mở ra 1 nẻo), Above-the-fold yếu.  |
|   -> XỬ LÝ: Sửa Headline Hero, đưa Video Demo và Giá neo lên ngay màn hình đầu tiên.             |
|                                                                                                   |
| [Case C] CTR tốt, ATC cao (>8%), nhưng Initiate Checkout / Purchase thấp                         |
|   -> NGUYÊN NHÂN: Giá ship ẩn gây sốc, thiếu cổng thanh toán bản địa (thiếu Klarna/iDEAL/Dobírka).|
|   -> XỬ LÝ: Thêm Freeship threshold, tích hợp cổng thanh toán quen thuộc của quốc gia đó.         |
|                                                                                                   |
| [Case D] Purchase nhiều nhưng CAC cao -> Không có lãi                                             |
|   -> NGUYÊN NHÂN: AOV quá thấp hoặc giá bán chưa tối ưu.                                          |
|   -> XỬ LÝ: Nâng cấp Offer Ladder (Thêm gói Combo 2x, 3x kèm Free Gift) để kéo AOV lên.          |
|                                                                                                   |
| [Case E] CTR cao, ATC cao, CVR > 3%, CAC thấp, Contribution Margin dương                          |
|   -> WINNER SIGNAL! Kích hoạt ngay Scaling Engine.                                                |
+---------------------------------------------------------------------------------------------------+
```

---

### BƯỚC 5: QUY TẮC RA QUYẾT ĐỊNH (KILL / ITERATE / SCALE)

Sau 48–72h chạy test, phân loại từng Creative vào đúng 3 ngã rẽ:

1. ❌ **KILL (Tắt ngay):** Chi phí đạt $1.5\times \text{Target CAC}$ mà không có ATC hoặc CTR $< 1\%$. Tắt để bảo toàn vốn.
2. 🔄 **ITERATE (Tối ưu biến thể):** Ad có CTR cao, có lượt Add to Cart nhưng chưa sinh lời:
   * *Giữ nguyên Hook 3s đầu* $\rightarrow$ Thay đổi Body demo hoặc đổi Offer trên Landing Page.
3. 🚀 **SCALE (Vít ngân sách):** Creative có chỉ số đẹp và sinh lời:
   * **Không bao giờ dừng Creative khi đã tìm thấy Winner!**
   * Ngay lập tức nhân bản thành **6 biến thể (Winning Variations Loop):**
     * `V2:` Cùng Hook $\rightarrow$ Thay Creator/KOL khác.
     * `V3:` Cùng Hook $\rightarrow$ Cắt ngắn nhịp điệu nhanh hơn (Fast-paced).
     * `V4:` Cùng Concept $\rightarrow$ Thử nghiệm góc quay Before/After mới.
     * `V5:` Cùng Pain $\rightarrow$ Thử nghiệm tình huống sử dụng khác (Bồn rửa bát thay vì kính tắm).
     * `V6:` Cùng Concept $\rightarrow$ Tệp nhân vật khác (Người lớn tuổi / Thanh niên).

---

### BƯỚC 6: SCALING & TĂNG TRƯỞNG LTV (VERTICAL & HORIZONTAL)

1. **Vertical Scale (Mở rộng ngân sách):**
   * Tăng ngân sách 20% mỗi 24–48h trên các Ad Set / Campaign đang sinh lời.
2. **Horizontal Scale (Mở rộng địa lý Geo-Arbitrage):**
   * Khi Winner chạy tốt ở 🇩🇪 Đức $\rightarrow$ Ngay lập tức mở rộng sang 🇦🇹 Áo, 🇨🇭 Thụy Sĩ.
   * Khi Winner chạy tốt ở 🇨🇿 Séc $\rightarrow$ Ngay lập tức mở rộng sang 🇸🇰 Slovakia, 🇵🇱 Ba Lan.
   * Dùng lại toàn bộ kịch bản và Landing Page đã được chứng minh.
3. **Backend LTV Retention:**
   * Sau khi có khách mua sản phẩm mồi (Acquisition Product):
   * Kích hoạt Email/SMS Flow: Chăm sóc hướng dẫn sử dụng sau 3 ngày $\rightarrow$ Upsell sản phẩm bổ trợ sau 7 ngày $\rightarrow$ Mời mua Refill tiêu hao sau 30 ngày.

---

### BƯỚC 7: CLOSED-LOOP FEEDBACK ENGINE (HỌC TẬP TỰ ĐỘNG)

Mọi dữ liệu thực chiến sau khi chạy Ads phải được nạp ngược lại vào **Research Engines (01, 02, 03)**:
* *Góc Pain nào tạo ra CTR cao nhất?*
* *Mức giá $19.90 hay $24.90 cho Conversion Rate tốt hơn?*
* *Quốc gia nào có chi phí CAC rẻ nhất?*
* *Hook nào giữ chân người xem quá 3 giây tốt nhất?*

👉 **Vòng lặp học tập này giúp các đợt nghiên cứu sản phẩm tiếp theo có độ chính xác và xác suất win tăng dần theo thời gian.**
