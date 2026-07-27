# BÀI 4 — MÔ HÌNH DỰ ĐOÁN (PREDICTION MODEL) TRONG DIGITAL TWIN
### Tổng hợp & hệ thống hóa kiến thức cốt lõi
*Nhập môn Công nghệ Song sinh Thực – Số và Ứng dụng — GV: Ngô Thành Trung, Computer Engineering Dept., SoICT, HUST*

---

## 0. THỐNG KÊ NỘI DUNG BÀI GIẢNG

Bài giảng gồm **~166 slide**, phân bổ như sau:

| Khối nội dung | Vị trí (slide) | Tỉ trọng | Ghi chú |
|---|---|---|---|
| Giới thiệu + Kiến trúc phân tầng 5 lớp | 1 – 9 | ~5% | Định vị bài học |
| Vai trò của Prediction Module | 10 – 18 | ~5% | Phần "vì sao" |
| So sánh Physics vs Data-driven + Xu hướng Hybrid | 19 – 21 | ~2% | Khung tư duy chính |
| **1. Physics-Based Model** | 22 – 122 | **~60%** | Trọng tâm lớn nhất |
| ├ Trục A: theo hiện tượng vật lý (8 loại) | 25 – 88 | ~38% | Dynamic → Multiphysics |
| └ Trục B: theo phương pháp xây dựng (6 loại) | 89 – 122 | ~20% | LPM → Multiphysics |
| **2. Data-Driven Model** | 123 – 166 | **~27%** | Bao gồm ôn lại ML/DL cơ bản |
| **3. Hybrid Model** | — | ~0% | **Có trong Outline nhưng KHÔNG có mục riêng** |

**Nhận xét quan trọng về cấu trúc:**
- Outline (slide 18) hứa 3 phần: Physics-based → Data-driven → Hybrid. Trên thực tế **Hybrid không có section riêng**, chỉ xuất hiện 2 lần dưới dạng "Xu hướng hiện nay" (slide 20–21 ở đầu và slide 132 ở cuối phần Data-driven). Đây chính là **thông điệp kết luận của cả bài**, không phải phần bị bỏ sót.
- Bài giảng nặng về **phân loại (taxonomy)** hơn là thuật toán. Nếu học để thi, ưu tiên nhớ **cây phân loại + bảng so sánh + công thức đại diện của từng nhóm**.
- Phần Data-driven từ slide 134 trở đi thực chất là **ôn lại nền tảng ML/DL** (hồi quy tuyến tính → gradient descent → SVM → neural network → CNN), không phải kiến thức đặc thù Digital Twin.

---

## 1. ĐỊNH VỊ BÀI HỌC: PREDICTION NẰM Ở ĐÂU?

Kiến trúc Digital Twin phân 5 tầng, đi từ dưới lên:

```
5. Visualization & Application Layer  →  Dashboard, 3D Visualization, AR/VR
             ↑
4. Analytics Layer                    →  ★ Prediction ★ , Update, Optimization,
                                          Anomaly Detection
             ↑
3. Data Platform Layer                →  Lưu trữ dữ liệu, Xử lý dữ liệu
             ↑
2. Connectivity Layer                 →  WiFi, 5G, MQTT, OPC-UA
             ↑
1. Physical Layer                     →  Thiết bị thực, Cảm biến
```

**Bài 4 chỉ đào sâu 1 trong 4 chức năng của tầng Analytics: Prediction.**

---

## 2. VÌ SAO PREDICTION MODULE LÀ "TRÁI TIM" CỦA DIGITAL TWIN

Ý tưởng nền tảng — sự khác biệt chỉ nằm ở **một chữ**:

| Không có Prediction Module | Có Prediction Module |
|---|---|
| "Hệ thống **đang** ở trạng thái nào?" | "Hệ thống **sẽ** ở trạng thái nào trong tương lai?" |
| → **Digital Shadow** (bóng số) | → **Digital Twin thông minh** |
| Chỉ giám sát, **phản ứng** khi sự cố xảy ra | Dự báo, tối ưu hóa, ra quyết định **chủ động** |

Ví dụ trong nhà máy thông minh, DT có thể dự đoán: nhiệt độ thiết bị trong 1 giờ tới; mức độ hao mòn máy móc trong vài ngày/vài tuần tới; sản lượng sản xuất của ca làm việc tiếp theo.

### 5 vai trò cụ thể (phải thuộc)

**① Hỗ trợ bảo trì dự đoán (Predictive Maintenance)** — *ứng dụng thành công nhất của DT*
- Ước lượng: Tuổi thọ còn lại (**RUL – Remaining Useful Life**), xác suất hỏng hóc, thời điểm cần bảo trì, phát hiện bất thường (Anomaly Detection).
- Luồng ví dụ: `Dữ liệu rung động hiện tại → Mô hình dự đoán → Ổ bi có khả năng hỏng sau 20 ngày → Lập kế hoạch bảo trì trước khi sự cố xảy ra`

**② Thực hiện phân tích "What-if"**
- Mô phỏng kịch bản tương lai **trước khi** áp dụng vào hệ thống thực.
- Ví dụ: Nếu nhu cầu sản xuất tăng 30%? Nếu một cảm biến bị lỗi? Nếu nhiệt độ môi trường tăng cao?

**③ Hỗ trợ ra quyết định tối ưu**
- Cung cấp: đánh giá rủi ro, lập kế hoạch nguồn lực, tối ưu hóa vận hành.
- Ví dụ lưới điện thông minh: `Dữ liệu tiêu thụ điện hiện tại → Mô hình dự đoán → Nhu cầu điện ngày mai → Kế hoạch phát điện tối ưu`

**④ Ước lượng các biến KHÔNG THỂ đo trực tiếp** *(vai trò hay bị bỏ quên nhưng rất quan trọng)*
- Nhiều đại lượng không gắn được cảm biến: mức độ mỏi bên trong cánh máy bay, mức độ suy giảm của pin, ứng suất bên trong cầu/công trình.
- Cách làm: **kết hợp dữ liệu cảm biến + mô hình vật lý** để ước lượng các "trạng thái ẩn".

**⑤ Gia tăng giá trị của Digital Twin** — biến Digital Shadow thành Digital Twin thông minh (xem bảng ở trên).

---

## 3. VÒNG LẶP DỰ ĐOÁN – CẬP NHẬT (PREDICT–UPDATE LOOP)

Đây là **sơ đồ xương sống** của DT hiện đại, hay được hỏi ở dạng vẽ lại:

```
        ┌──────────────────────────────────────────┐
        │                                          │
        ▼                                          │
   Hệ thống vật lý                                 │
        ↓                                          │
   Dữ liệu cảm biến                                │
        ↓                                          │
   ► Mô-đun cập nhật (Update Module)               │
        ↓                                          │
   Trạng thái hiện tại của Digital Twin            │
        ↓                                          │
   ► Mô-đun dự đoán (Prediction Module)            │
        ↓                                          │
   Ước lượng trạng thái tương lai                  │
        ↓                                          │
   Hỗ trợ ra quyết định / Điều khiển ──────────────┘
```

**Phân biệt hai mô-đun (câu hỏi kinh điển):**
- **Update Module** → Đồng bộ Digital Twin với dữ liệu thực (kéo DT về đúng hiện tại).
- **Prediction Module** → Dự đoán trạng thái tương lai (đẩy DT đi trước hiện tại).

---

## 4. BA NHÁNH CÔNG NGHỆ & KHUNG SO SÁNH GỐC

**Chức năng của Prediction Module:** dự báo trạng thái tương lai · ước lượng tuổi thọ còn lại · phát hiện bất thường · mô phỏng "What-if" · tối ưu hóa vận hành.

**Công nghệ thường dùng:**
1. **Physics-based Model**
2. **Data-driven Model** — Machine Learning, Deep Learning (LSTM, Transformer)
3. **Hybrid Model**

### ⭐ Bảng so sánh cốt lõi (slide 19 — bảng quan trọng nhất bài)

| Tiêu chí | Physics-Based | Data-Driven |
|---|---|---|
| Dựa trên | Định luật vật lý | Dữ liệu |
| Cần dữ liệu lớn | Không | Có |
| Giải thích được | **Cao** | Thấp |
| Độ chính xác khi ít dữ liệu | **Cao** | Thấp |
| Tốc độ tính toán | Chậm | **Nhanh** |
| Khả năng ngoại suy | **Tốt** | Hạn chế |

> **Cách nhớ:** Physics thắng ở *chất lượng tri thức* (giải thích, ngoại suy, ít dữ liệu vẫn chạy). Data-driven thắng ở *tốc độ và sự tiện lợi*. Đây chính là lý do sinh ra Hybrid.

### Ba kiểu tích hợp Hybrid (slide 21)

| Kiểu | Cơ chế |
|---|---|
| **Nối tiếp (Series)** | `Sensor Data → Model 1 → Model 2 → System Output` — mô hình này tạo dữ liệu đầu vào cho mô hình kia |
| **Song song (Parallel)** | `Sensor Data → {Model 1, Model 2, …, Model n} → Model Fusion → Output` — cùng dự đoán rồi dung hợp kết quả |
| **Tích hợp (Combined)** | Quy luật vật lý nằm **ngay trong cấu trúc thuật toán** (ví dụ: nhúng định luật vật lý vào hàm mất mát của Machine Learning) |

**Mục tiêu của Hybrid:** Physics Model đảm bảo **tính đúng đắn về mặt vật lý** — AI Model học **các hiện tượng phức tạp và phi tuyến**.

---

# PHẦN 1 — PHYSICS-BASED MODEL

## 5.1. Định nghĩa & hai trục phân loại

> **Physics-Based Model** là mô hình mô tả hành vi của hệ thống bằng **định luật vật lý, phương trình toán học và nguyên lý kỹ thuật**, thay vì học từ dữ liệu như Machine Learning.
> Trong Digital Twin, nó đóng vai trò là **"bộ não vật lý"**.

Bài giảng dùng **2 trục phân loại song song** (đây là điểm dễ rối nhất — cùng một mô hình có thể xuất hiện ở cả hai trục):

```
TRỤC A — theo HIỆN TƯỢNG vật lý     |  TRỤC B — theo PHƯƠNG PHÁP xây dựng
(mô hình hóa CÁI GÌ)                |  (mô hình hóa NHƯ THẾ NÀO)
────────────────────────────────────┼──────────────────────────────────────
├── Dynamic Models                  |  ├── Lumped Parameter Models
├── Structural Models               |  ├── ODE-Based Models
├── Thermal Models                  |  ├── PDE-Based Models
├── Fluid Models                    |  ├── FEM Models
├── Electrical Models               |  ├── CFD Models
├── Chemical Models                 |  └── Multiphysics Models
├── Electrochemical Models          |
└── Multiphysics Models             |
```

---

## 5.2. TRỤC A — 8 LOẠI THEO HIỆN TƯỢNG VẬT LÝ

### A1. Dynamic Models (Mô hình động lực học)

Mô tả **sự thay đổi của hệ thống theo thời gian**. Ví dụ: Robot, Drone, Xe tự hành, Động cơ điện.

**Khái niệm trạng thái (State)** — hệ thống được mô tả bằng **vector trạng thái x(t)**:
- Động cơ điện: `x1 = tốc độ quay`, `x2 = dòng điện`
- Xe tự hành: `x1 = vị trí`, `x2 = vận tốc`, `x3 = góc lái`

**Mô hình State-Space** — *cách biểu diễn phổ biến nhất trong Digital Twin*:

```
ẋ = f(x, u, t)
   x : vector trạng thái
   u : tín hiệu điều khiển
   t : thời gian
```

**Ví dụ 1 — Hệ Khối lượng–Lò xo–Giảm chấn (Mass-Spring-Damper):**
```
m·ẍ + c·ẋ + k·x = F
 │      │      │     └─ ngoại lực
 │      │      └─ lực đàn hồi
 │      └─ lực giảm chấn (ma sát nhớt, giảm xóc ô tô, cản không khí)
 └─ Newton
```

**Ví dụ 2 — Động cơ điện (bài toán tính tay hay ra thi):**
```
J · dω/dt = Tm − TL       ← phần mô-men dư làm rotor tăng tốc
```
| Ký hiệu | Ý nghĩa vật lý | Đơn vị SI |
|---|---|---|
| J | Mô-men quán tính của hệ quay | kg·m² |
| ω | Vận tốc góc của trục quay | rad/s |
| dω/dt | Gia tốc góc | rad/s² |
| Tm | Mô-men do động cơ sinh ra | N·m |
| TL | Mô-men tải hoặc lực cản | N·m |

**Bài toán mẫu:** J = 0.5 kg·m², ω = 100 rad/s, Tm = 60 N·m, TL = 40 N·m
```
dω/dt = (Tm − TL)/J = (60 − 40)/0.5 = 40 rad/s²
→ Digital Twin dự đoán: 1 giây nữa ω ≈ 140 rad/s
```

---

### A2. Structural Models (Mô hình kết cấu)

Mô tả **hành vi cơ học của kết cấu** khi chịu tải trọng, rung động, nhiệt độ hoặc môi trường.
**Mục tiêu:** dự đoán ứng suất, biến dạng, dao động, hư hỏng và tuổi thọ.

**Đối tượng:** Cầu · Tòa nhà · Máy bay · Tua-bin gió · Robot · Máy CNC · Đường sắt · Giàn khoan dầu khí.

**Chuỗi nhân quả (nhớ theo cặp trừu tượng ↔ cụ thể):**
```
Lực tác động  → Biến dạng      → Ứng suất tăng   → Hư hỏng
Xe tải qua cầu → Cầu bị uốn cong → Ứng suất tăng  → Xuất hiện vết nứt
```

**Prediction Module dùng Structural Model:**
```
Current Structure State → Structural Model → Stress Prediction
    → Fatigue Prediction → Failure Prediction
```

**Ví dụ DT máy bay** (dự đoán Wing Stress, Wing Deflection, Fatigue Damage):
```
Flight Data → Structural Twin → Fatigue Analysis → Remaining Useful Life
```

**Các loại Structural Models:**

| Loại | Nội dung | Áp dụng cho |
|---|---|---|
| **Linear Elastic Model** | Định luật Hooke: `σ = E·ε` | Vật liệu trong vùng đàn hồi |
| **Beam Models** (mô hình dầm) | — | Cầu, Cần cẩu, Cánh máy bay |
| **Plate/Shell Models** | Biến dạng của tấm mỏng | Thân máy bay, Vỏ tàu, Thân ô tô |
| **FEM** | **Công nghệ cốt lõi của Structural Digital Twin** | Kết cấu bất kỳ |

Trong `σ = E·ε`: **E** = Young's Modulus, **σ** = Stress, **ε** = Strain.
→ *E lớn → vật liệu cứng; E nhỏ → vật liệu mềm.*

**Luồng FEM:** `Kết cấu lớn → Chia thành hàng nghìn phần tử nhỏ → Giải phương trình vật lý → Stress Map`

**Structural Health Monitoring (SHM):**
```
Sensor Data → Structural Model → Damage Detection → Remaining Life Estimation
Cầu → Rung động bất thường → Structural Twin → Khả năng xuất hiện vết nứt
```

**Ví dụ DT cầu dây văng:** Cảm biến (ứng suất, nhiệt độ, rung động) → đồng bộ dữ liệu thời gian thực → Mô hình DT xuất ra: Chỉ số sức khỏe 98%, Dự đoán bảo trì 3 tháng, Ứng suất cáp OK → phản hồi tối ưu hóa hoạt động.

> **Câu chốt:** Structural Models là các Physics-Based Models mô phỏng ứng suất, biến dạng, dao động và hư hỏng của kết cấu; trong DT chúng thường được xây bằng **FEM** kết hợp **dữ liệu cảm biến** để thực hiện SHM, dự đoán tuổi thọ còn lại và phát hiện sớm nguy cơ hỏng hóc.

---

### A3. Thermal Models (Mô hình nhiệt)

Mô phỏng và dự đoán **phân bố nhiệt độ, dòng nhiệt và hiện tượng truyền nhiệt** theo thời gian thực.

**a) Phân loại theo mức độ chi tiết:**

| Loại | Đặc điểm | Toán học |
|---|---|---|
| **Lumped Parameter Thermal (0D)** | Toàn hệ có **một** nhiệt độ duy nhất, không xét phân bố bên trong | `C · dT/dt = Q_in − Q_out` |
| **Distributed Thermal (1D/2D/3D)** | Nhiệt độ thay đổi theo vị trí, mỗi điểm có nhiệt độ riêng | Dùng **PDE** |

Trong `C·dT/dt = Q_in − Q_out`: C = nhiệt dung, T = nhiệt độ, Q_in = nhiệt sinh ra, Q_out = nhiệt thoát ra.

**b) Phân loại theo cơ chế truyền nhiệt (3 cơ chế kinh điển):**

| Cơ chế | Công thức | Ý nghĩa & ví dụ |
|---|---|---|
| **Conduction** (Dẫn nhiệt) | `q = −k∇T` | Nhiệt truyền trong vật rắn. q = mật độ dòng nhiệt, k = hệ số dẫn nhiệt. *Ví dụ: thanh kim loại, pin, cánh tua-bin* |
| **Convection** (Đối lưu) | `dT/dt = −k(T − Ts)`<br>`T(t) = Ts + (T0 − Ts)·e^(−kt)` | Nhiệt truyền giữa bề mặt và chất lưu. *Ví dụ: CPU → quạt làm mát → không khí* |
| **Radiation** (Bức xạ) | `Q = εσA(T⁴ − T_env⁴)` — Stefan-Boltzmann | Nhiệt truyền dưới dạng sóng điện từ. *Ví dụ: mặt trời, lò nung, vệ tinh* |

Chi tiết công thức bức xạ:
| Ký hiệu | Ý nghĩa | Đơn vị |
|---|---|---|
| Q | Công suất nhiệt bức xạ trao đổi | W |
| ε | Hệ số phát xạ của vật liệu | không đơn vị, [0..1] |
| σ | Hằng số Stefan-Boltzmann | 5.67×10⁻⁸ W/(m²K⁴) |
| A | Diện tích bề mặt trao đổi nhiệt | m² |
| T / T_env | Nhiệt độ tuyệt đối của vật / môi trường | K (Kelvin) |

---

### A4. Fluid Models (Mô hình dòng chảy)

Mô tả hành vi của **chất lỏng (liquid) và chất khí (gas)**: Dòng chảy · Áp suất · Vận tốc · Nhiệt độ · Nhiễu loạn (Turbulence).

**Ứng dụng:** Đường ống dầu khí · Động cơ phản lực · Hệ thống cấp nước · Tua-bin gió · Nhà máy hóa chất.

**Phân loại theo số chiều:**

| Loại | Đặc điểm |
|---|---|
| **Lumped Fluid Models (0D)** | Dòng chảy không phân bố theo không gian (ví dụ bình chứa nước) |
| **Pipe Network Models (1D)** | Trạng thái dòng chảy phân bố theo chiều dài đường ống |
| **CFD (3D)** | Loại **mạnh nhất và phổ biến nhất** trong nghiên cứu |

**Hai phương trình cơ bản:**

**① Phương trình liên tục (Continuity Equation)** — *"Khối lượng không tự sinh ra cũng không tự mất đi"*
```
∂ρ/∂t + ∇·(ρv) = 0
```
| ρ | Khối lượng riêng của chất lưu | kg/m³ |
|---|---|---|
| t | Thời gian | s |
| v | Vector vận tốc dòng chảy | m/s |
| ρv | Mật độ dòng khối lượng | kg/(m²·s) |

**② Phương trình Navier-Stokes** — *"Định luật II Newton cho dòng!!!"*
```
ρ(∂v/∂t + v·∇v) = −∇p + μ∇²v + ρg
└─ Khối lượng × Gia tốc ─┘   │      │      └─ Trọng lực
                              │      └─ Lực nhớt
                              └─ Lực áp suất
```
| p | Áp suất | Pa |
|---|---|---|
| μ | Độ nhớt động lực học (Viscosity) | Pa·s |
| g | Gia tốc trọng trường | m/s² |

**Ví dụ DT đường ống:** Cảm biến vận tốc, áp suất, độ nhớt, lưu lượng, ăn mòn → đồng bộ dữ liệu → Bản sao số phân tích CFD & Navier-Stokes → giám sát nhiễu loạn và độ nhớt, mô phỏng tình huống khẩn cấp, tối ưu hóa vận chuyển chất lưu.

---

### A5. Electrical Models

DT dùng mô hình điện để dự đoán: Điện áp · Dòng điện · Công suất · Năng lượng tiêu thụ · Tổn hao điện · Hiệu suất hoạt động · **Trạng thái pin (SOC, SOH)** · Sự cố điện.

**Ba nhóm lớn:**

| Nhóm | Dựa trên | Dạng toán học |
|---|---|---|
| **1. Circuit Models** (RLC, ECM) | Ohm, Kirchhoff | ODE hoặc State-Space |
| **2. Electromagnetic Models** | Maxwell | PDE |
| **3. Electrochemical-Electrical Models** | Kết hợp điện học + hóa học + nhiệt học | Dùng cho pin & lưu trữ năng lượng |

> **Kết luận thực tiễn:** Trong Digital Twin công nghiệp, **Equivalent Circuit Models (ECM)** và **State-Space Electrical Models** là hai loại được dùng nhiều nhất, do **cân bằng tốt giữa độ chính xác và tốc độ mô phỏng thời gian thực**.

**Các định luật cơ bản:** `V = IR` (Ohm) · `P = VI` (công suất) · `ΣI_in = ΣI_out` (KCL) · `ΣV = 0` (KVL).

**Phân loại chi tiết (a → f):**

| Ký hiệu | Loại | Nội dung chính |
|---|---|---|
| **a** | **RLC Models** | `L·d²i/dt² + R·di/dt + (1/C)·i = V` — ODE bậc hai, có **dao động (oscillation)** và **cộng hưởng (resonance)** |
| **b** | **Lumped Parameter Circuit** | Giả thiết: linh kiện tập trung tại các điểm rời rạc, không xét phân bố điện trường theo không gian. Ví dụ: RC, RL, RLC, ECM |
| **c** | **Distributed Parameter** | Dùng khi kích thước lớn hoặc tần số cao (đường truyền tín hiệu, cáp điện dài, antenna). Phương trình **Telegraph** |
| **d** | **Equivalent Circuit Models (ECM)** | **Quan trọng nhất trong DT pin** — thay quá trình vật lý phức tạp bằng **phần tử điện tương đương** |
| **e** | **Nonlinear Circuit Models** | Khi linh kiện không còn tuyến tính (Diode, Transistor) |
| **f** | **State-Space Circuit Models** | `ẋ = Ax + Bu` ; `y = Cx + Du` |

**Phương trình Telegraph (c):**
```
∂V/∂x = −RI − L·∂I/∂t
∂I/∂x = −GV − C·∂V/∂t
```
| R | Resistance | Ω/m | Điện trở dây dẫn |
|---|---|---|---|
| L | Inductance | H/m | Điện cảm phân bố |
| C | Capacitance | F/m | Điện dung phân bố |
| G | Conductance | S/m | Điện dẫn rò phân bố |

**Chuỗi ECM cho pin (d) — nhớ theo thứ tự tăng dần độ phức tạp:**

```
Rint model  →  Thevenin Model  →  Dual Polarization Model  →  Nonlinear Circuit Models
```
- **Rint (Internal Resistance) model** — mô hình mạch tương đương **đơn giản nhất** của pin. Biểu diễn pin bằng: một nguồn áp lý tưởng **Voc** (Open-Circuit Voltage) + một điện trở trong **Rint (R0)** mắc nối tiếp.
  ```
  Vt = Voc − I·R0
  ```
- **Thevenin Model** — thêm **một nhánh RC** so với Rint model:
  ```
  Vt = Voc − I·R0 − V_RC
  dV_RC/dt = −(1/RC)·V_RC + I/C
  ```

**Nonlinear (e) — Shockley ideal diode equation:**
```
I = Is·(e^(V/(n·V_T)) − 1)      với V > Vz
V_T = kT/q
```

**State-Space (f):**
- State thường là: **điện áp trên tụ**, **dòng điện qua cuộn cảm**.
- Ứng dụng: **Digital Twin thời gian thực · Kalman Filter · MPC · Fault Detection**.

---

### A6. Chemical Models

Mô tả sự thay đổi **thành phần hóa học, nồng độ các chất, tốc độ phản ứng và quá trình vận chuyển vật chất**.

**Dùng để dự đoán:** thành phần hóa học · nồng độ chất phản ứng · tốc độ phản ứng · hiệu suất chuyển hóa · suy giảm vật liệu · ăn mòn (corrosion) · lão hóa pin · phát thải khí.

**Ứng dụng theo lĩnh vực:**

| Lĩnh vực | Hiện tượng mô hình hóa |
|---|---|
| Pin Lithium-ion | Phản ứng điện hóa, khuếch tán ion Li⁺, suy giảm pin |
| Nhà máy hóa chất | Phản ứng trong lò phản ứng, cân bằng vật chất, cân bằng năng lượng |
| Hệ thống xử lý nước | Phản ứng khử trùng, keo tụ, oxy hóa |
| Fuel Cell | Phản ứng hydro, trao đổi proton, sinh điện năng |
| Ăn mòn vật liệu | Oxy hóa kim loại, suy giảm kết cấu |

**6 loại mô hình (a → f):**

| | Loại | Phương trình đại diện |
|---|---|---|
| **a** | **Reaction Kinetics** (động học phản ứng) | `A → B` ; tốc độ `r = k·CA` ; bậc nhất `dCA/dt = −k·CA` ; nghiệm `CA(t) = CA0·e^(−kt)` |
| **b** | **Reaction Network** (chuỗi động học) | `A → B → C` ; `dCA/dt = −k1·CA` ; `dCB/dt = k1·CA − k2·CB` ; `dCC/dt = k2·CB` |
| **c** | **Equilibrium** (cân bằng hóa học) | `A + B ⇌ C` ; `K = [C]/([A][B])` |
| **d** | **Electrochemical** (điện hóa) | `LiC₆ ⇌ C₆ + Li⁺ + e⁻` |
| **e** | **Diffusion** (khuếch tán) | Fick 1: `J = −D·∂C/∂x` ; Fick 2: `∂C/∂t = D·∂²C/∂x²` |
| **f** | **Mass Transfer** (truyền khối) | `N = km·(Cs − C)` |

**Đọc hằng số cân bằng K (c):**
- `K ≫ 1` → phản ứng ưu tiên tạo sản phẩm C
- `K ≪ 1` → ưu tiên tồn tại dưới dạng chất phản ứng A và B
- `K ≈ 1` → cả chất phản ứng và sản phẩm cùng tồn tại đáng kể
- *Ứng dụng: nhà máy hóa chất, xử lý nước, khí hóa*

**Điện hóa (d) — quá trình xả và sạc pin Li-ion:**
```
Xả:  LiC₆ → C₆ + Li⁺ + e⁻
Sạc: C₆ + Li⁺ + e⁻ → LiC₆
```

**Truyền khối (f):** N = tốc độ truyền khối, km = hệ số truyền khối, Cs = nồng độ bề mặt, C = nồng độ môi trường. *Ví dụ: Khí → lỏng, Lỏng → rắn.*

> **State Variables — điểm phân biệt then chốt:** Khác với Electrical Models (state là điện áp/dòng điện), state của Chemical Models thường là **nồng độ chất, nhiệt độ, áp suất, độ pH, State of Charge**.

---

### A7. Electrochemical Models

Mô tả **đồng thời 5 hiện tượng**: phản ứng hóa học · dòng điện · vận chuyển ion · khuếch tán vật chất · sinh nhiệt — trong các hệ thống điện hóa.

> Đây là **loại mô hình vật lý có độ chính xác cao nhất** cho: Pin Lithium-ion · Pin thể rắn · Fuel Cell · Electrolyzer · Supercapacitor.

**Dùng để dự đoán:** điện áp pin · dòng điện · **SOC (State of Charge)** · **SOH (State of Health)** · nhiệt độ · lão hóa pin · **RUL (tuổi thọ còn lại)**.

**Ghép nối 4 lĩnh vực vật lý:**
```
├── Electrochemistry
├── Mass Transport
├── Electrical Dynamics
└── Thermal Dynamics
```

**Các trạng thái chính:** nồng độ Lithium trong điện cực · nồng độ Lithium trong điện phân · điện thế pha rắn · điện thế chất điện phân · mật độ dòng phản ứng · nhiệt độ · SOC · SOH.

**4 phương trình cơ bản:**
```
① Bảo toàn điện tích trong cực:          ∇·i_s = −aFj
② Vận chuyển ion trong chất điện phân:   ∂ce/∂t = De∇²ce + S
③ Khuếch tán trong hạt điện cực:         ∂cs/∂t = Ds∇²cs
④ Tốc độ phản ứng điện hóa (Butler-Volmer):
   j = j₀·( e^(αa·F·η/RT) − e^(−αc·F·η/RT) )
```

**Phân loại — thang độ chính xác/chi phí tăng dần:**

| | Mô hình | Đặc điểm |
|---|---|---|
| **a** | **ECM** (Equivalent Circuit Model) | Đơn giản, nhanh nhất |
| **b** | **SPM** (Single Particle Model) | Mỗi điện cực giả sử là **một hạt hình cầu duy nhất**: Cathode Particle + Anode Particle |
| **c** | **SPMe** (SPM with Electrolyte) | SPM + **thêm nồng độ ion trong điện phân** |
| **d** | **P2D** (Pseudo-Two-Dimensional) | **Mô hình điện hóa tiêu chuẩn của pin Li-ion** — độ chính xác rất cao, tính toán lớn |

---

### A8. Multiphysics Models (Mô hình đa vật lý)

Mô tả **nhiều hiện tượng vật lý tương tác với nhau đồng thời** trong một hệ thống.
*Trong thực tế, các hiện tượng vật lý hiếm khi tồn tại độc lập.*

**Ví dụ chuỗi ghép nối trong Pin Lithium-ion:**
```
Current → Heat Generation → Temperature Increase → Reaction Rate Change → Voltage Change
```

**Các loại phổ biến:** Electro-Thermal · Electrochemical-Thermal · Fluid-Structure Interaction · Thermo-Mechanical · Electromagnetic-Thermal.

---

## 5.3. TRỤC B — 6 LOẠI THEO PHƯƠNG PHÁP XÂY DỰNG MÔ HÌNH

### B.1 Lumped Parameter Models (LPM) — Mô hình tham số tập trung

**Giả định cốt lõi:** Các đại lượng vật lý (nhiệt độ, áp suất, điện áp, lực…) là **đồng nhất trong từng thành phần** của hệ thống → mỗi thành phần biểu diễn bằng **một giá trị duy nhất** thay vì phân bố theo không gian.
→ **LPM bỏ qua sự phân bố theo không gian, chỉ quan tâm sự thay đổi theo thời gian.**

*Hai hình ảnh minh họa trong slide: cả căn phòng có chung nhiệt độ t = 25°C; cả bình nước có chung một áp suất lên thành.*

| Ưu điểm | Nhược điểm |
|---|---|
| Mô hình đơn giản, dễ xây dựng | Độ chính xác giảm khi đại lượng thay đổi đáng kể theo không gian |
| Tính toán nhanh → phù hợp DT thời gian thực | Không mô tả được đại lượng phân bố bên trong vật thể |
| Cần ít dữ liệu và tài nguyên tính toán | Không phù hợp với hình học phức tạp hoặc tương tác vật lý chi tiết |
| Dễ kết hợp bộ điều khiển & bộ quan sát trạng thái (**bộ lọc Kalman**) | |

**LPM phù hợp khi:** hệ thống nhỏ hoặc biến thiên không gian không đáng kể · cần mô phỏng nhanh/thời gian thực · mục tiêu là giám sát, điều khiển hoặc dự đoán trạng thái tổng thể.

---

### B.2 ODE-Based Models (Ordinary Differential Equation)

Mô tả sự thay đổi của hệ thống **theo thời gian** bằng phương trình vi phân thường.

**Dạng tổng quát:**
```
dx(t)/dt = f( x(t), u(t), t )
   x(t) : trạng thái của hệ thống (State)
   u(t) : tín hiệu điều khiển hoặc đầu vào (Input)
   t    : thời gian
   f(·) : hàm mô tả động lực học của hệ thống
```

**Ví dụ:** vị trí xe `dx/dt = v` · Mass-Spring-Damper `mẍ + cẋ + kx = F`

> ⚠️ **Phân biệt ODE vs LPM (rất hay bị nhầm):**
> - **Lumped Parameter Model** là **cách mô hình hóa** (giả sử các đại lượng đồng nhất trong từng phần tử).
> - **ODE** là **dạng phương trình toán học** dùng để mô tả sự thay đổi theo thời gian.
> - **Hầu hết LPM đều được biểu diễn bằng ODE, nhưng KHÔNG phải mọi ODE đều xuất phát từ LPM.**

**Ứng dụng rộng rãi:**
| Lĩnh vực | Ví dụ | | Lĩnh vực | Ví dụ |
|---|---|---|---|---|
| Robot | Động lực học robot | | Máy phát điện | Động học rotor |
| Xe điện | Động lực học xe | | Drone | Bay và điều khiển |
| Pin | SOC, SOH | | Động cơ | Tốc độ quay |
| HVAC | Nhiệt độ phòng | | | |

| Ưu điểm | Nhược điểm |
|---|---|
| Mô hình hóa động lực học theo thời gian một cách tự nhiên | Không mô tả được sự phân bố theo không gian |
| Tính toán nhanh, phù hợp mô phỏng thời gian thực | Khó áp dụng cho hệ có trường nhiệt/ứng suất/dòng chảy phân bố liên tục |
| Dễ kết hợp thuật toán điều khiển & ước lượng trạng thái | Hệ phi tuyến hoặc nhiều trạng thái → giải phức tạp |
| Nhiều bộ giải số hiệu quả (**Euler, Runge–Kutta**) | |

---

### B.3 PDE-Based Models (Partial Differential Equation)

Mô tả sự biến đổi của đại lượng vật lý **theo CẢ thời gian VÀ không gian**.

**Ví dụ đại lượng thay đổi theo cả hai:** nhiệt độ trong tấm kim loại · áp suất trong đường ống · vận tốc của chất lỏng · ứng suất trong kết cấu.

**Hai phương trình mẫu:**
```
Phương trình truyền nhiệt:  ∂T/∂t = α∇²T     →  T = T(x, y, z, t)
Phương trình sóng:          ∂²u/∂t² = c²·∂²u/∂x²   ; u(x,t) là hàm sóng theo t và x
```

**Ứng dụng:**
| Hệ thống | PDE mô tả |
|---|---|
| Pin EV | Phân bố nhiệt, nồng độ ion |
| Động cơ | Truyền nhiệt |
| Chip điện tử | Nhiệt độ trên bề mặt |
| Máy bay | Ứng suất, biến dạng |
| Đường ống | Áp suất và lưu lượng |
| Tòa nhà | Trường nhiệt |
| Mô sinh học | Khuếch tán thuốc |
| Hồ chứa | Lan truyền chất ô nhiễm |

| Ưu điểm | Nhược điểm |
|---|---|
| Độ chính xác rất cao | Thiết lập và giải mô hình phức tạp |
| Mô tả được phân bố không gian | Yêu cầu nhiều dữ liệu (hình học, vật liệu, điều kiện biên) |
| Phù hợp hiện tượng vật lý liên tục | Chi phí tính toán lớn (đặc biệt 3D hoặc nhiều trường vật lý) |
| Kết hợp được nhiều trường vật lý | Khó đáp ứng thời gian thực **nếu không dùng kỹ thuật giảm bậc mô hình** |

### ⭐ Bảng so sánh ODE vs PDE

| Tiêu chí | ODE-Based Models | PDE-Based Models |
|---|---|---|
| Biến độc lập | Chỉ theo thời gian (t) | Theo thời gian **và** không gian (x,y,z,t) |
| Mô tả | Trạng thái **tổng thể** của hệ | **Trường vật lý phân bố** |
| Phương trình | Vi phân thường | Đạo hàm riêng |
| Chi phí tính toán | Thấp | Cao |
| Độ chính xác | Trung bình đến cao | Rất cao |
| Ví dụ | Động lực học robot, mạch RLC, mô hình pin tương đương | Truyền nhiệt, dòng chảy chất lỏng, ứng suất kết cấu, khuếch tán hóa chất |

---

### B.4 FEM Models (Finite Element Models)

> ⚠️ **Điểm nhấn quan trọng nhất:** **FEM KHÔNG PHẢI là một mô hình vật lý, mà là một PHƯƠNG PHÁP TÍNH TOÁN** để tìm nghiệm gần đúng của các phương trình vật lý (PDE) trên các hình học phức tạp.

**Ý tưởng cốt lõi — 3 bước** (ví dụ tính phân bố ứng suất trong cánh máy bay):
```
Bước 1. Chia vật thể thành nhiều phần tử nhỏ (Mesh)
Bước 2. Giải phương trình trên từng phần tử
Bước 3. Ghép tất cả phần tử lại
```
Trong mỗi phần tử, nghiệm được **xấp xỉ bằng các hàm nội suy (shape functions)**:
```
T(x) = N₁(x)·T₁ + N₂(x)·T₂ + …
```
Sau đó các phần tử được **lắp ráp thành một hệ phương trình toàn cục**.

| Ưu điểm | Nhược điểm |
|---|---|
| Độ chính xác cao | Chi phí tính toán lớn (3D, lưới mịn) |
| Xử lý được hình học phức tạp | Cần tạo lưới — **chất lượng lưới ảnh hưởng trực tiếp đến kết quả** |
| Áp dụng cho nhiều hiện tượng vật lý | Yêu cầu thông tin chính xác về vật liệu, hình học, điều kiện biên |
| Phân tích chi tiết tại từng điểm trong vật thể | Khó đáp ứng thời gian thực nếu mô hình quá lớn |
| Là nền tảng của nhiều phần mềm mô phỏng kỹ thuật | |

### ⭐ Bảng so sánh FEM – ODE – PDE

| Đặc điểm | ODE | PDE | FEM |
|---|---|---|---|
| **Bản chất** | Phương trình | Phương trình | **Phương pháp số** |
| Mô tả theo không gian | ✗ | ✓ | ✓ (thông qua việc giải PDE) |
| Mô tả theo thời gian | ✓ | ✓ | Tùy bài toán |
| Hình học phức tạp | Không phù hợp | Mô tả được nhưng khó giải trực tiếp | **Rất phù hợp** |
| Kết quả | Một số hữu hạn trạng thái | Trường vật lý liên tục | Nghiệm gần đúng trên các phần tử |

---

### B.5 CFD Models (Computational Fluid Dynamics)

Phương pháp **mô phỏng và phân tích chuyển động của chất lưu (fluid)** bằng cách giải các phương trình chi phối dòng chảy trên máy tính.

> ⚠️ Giống FEM: **CFD KHÔNG phải là một mô hình vật lý, mà là một phương pháp số** dùng để giải các PDE của cơ học chất lưu (**chủ yếu là phương trình Navier-Stokes**).

**Quy trình cơ bản của CFD — 6 bước:**
```
1. Physical System (Observation)        — quan sát hiện tượng thực
2. Create 3D Geometry (Modeling)        — dựng mô hình CAD 3D, xác định biên
3. Generate Mesh (Discretization)       — chia miền thành hàng triệu ô nhỏ
4. Apply Boundary Conditions (Setup)    — điều kiện biên, mô hình nhiễu loạn, chất lưu
5. Solve Navier-Stokes Equations        — giải lặp cho tới khi hội tụ
6. Visualization & Analysis             — hậu xử lý: trường vận tốc, áp suất…
```

**Ứng dụng:**
| Hệ thống | CFD dùng để mô phỏng | | Hệ thống | CFD dùng để mô phỏng |
|---|---|---|---|---|
| Xe điện | Làm mát pin | | Tua-bin gió | Dòng gió |
| Máy bay | Khí động học | | Nhà máy hóa chất | Dòng chất lỏng |
| Drone | Luồng không khí quanh cánh quạt | | Động cơ | Buồng đốt |
| HVAC | Điều hòa không khí | | Tàu thủy | Dòng nước |

| Ưu điểm | Nhược điểm |
|---|---|
| Mô phỏng chính xác dòng chảy chất lỏng và chất khí | Chi phí tính toán rất cao (dòng chảy rối — turbulent flow, mô hình 3D lớn) |
| Phân tích được áp suất, vận tốc, nhiệt độ, nhiễu loạn | Cần hình học, lưới và điều kiện biên chính xác |
| Hỗ trợ tối ưu hóa thiết kế **trước khi chế tạo** | Kết quả phụ thuộc nhiều vào chất lượng lưới và mô hình nhiễu loạn được chọn |
| Kết hợp được với truyền nhiệt, phản ứng hóa học, đa vật lý | |

---

### B.6 Multiphysics Models — Mô hình đa vật lý

Mô phỏng **đồng thời nhiều hiện tượng vật lý có tương tác (coupling)** trong cùng một hệ thống.

**Các cặp ghép nối điển hình:**
- Nhiệt ↔ Cơ học (Thermo-Mechanical)
- Điện ↔ Nhiệt (Electro-Thermal)
- Chất lưu ↔ Kết cấu (Fluid-Structure Interaction)
- Điện ↔ Hóa học ↔ Nhiệt (Electrochemical-Thermal)

**Các hệ thống DT thực tế:**
| Hệ thống | Các hiện tượng được kết hợp |
|---|---|
| Pin EV | Điện + Hóa học + Nhiệt + Cơ |
| Động cơ điện | Điện + Từ + Nhiệt + Cơ |
| Máy bay | Khí động học + Kết cấu + Nhiệt |
| Tua-bin gió | Gió + Kết cấu + Dao động |
| Chip điện tử | Điện + Nhiệt + Cơ |

| Ưu điểm | Nhược điểm |
|---|---|
| Mô phỏng gần với thực tế | Thiết lập mô hình phức tạp |
| Độ chính xác cao | Chi phí tính toán rất lớn |
| Mô tả được tương tác giữa các hiện tượng vật lý | Yêu cầu nhiều dữ liệu vật liệu và điều kiện biên |
| Hữu ích cho thiết kế, tối ưu hóa, dự đoán hư hỏng | Khó thời gian thực nếu không áp dụng kỹ thuật **giảm bậc mô hình** |

---

## 5.4. BA PHƯƠNG PHÁP SỐ: FDM – FEM – FVM

Do hệ thống rất phức tạp, các bài toán Multiphysics/PDE thường dùng 3 giải pháp:

| Phương pháp | Ý tưởng | Công thức đặc trưng |
|---|---|---|
| **FEM** — Finite Element Method | Chia miền thành nhiều **phần tử nhỏ** | Xấp xỉ bằng shape functions |
| **FVM** — Finite Volume Method | Chia miền thành các **thể tích điều khiển (control volumes)**, áp dụng định luật bảo toàn lên từng thể tích | `Tích lũy = Vào − Ra + Nguồn` |
| **FDM** — Finite Difference Method | **Thay đạo hàm bằng sai phân** | `∂T/∂x ≈ (T_{i+1} − T_i)/Δx`<br>`∂²T/∂x² ≈ (T_{i+1} − 2T_i + T_{i−1})/Δx²` |

### ⭐⭐ Bảng so sánh đầy đủ (bảng dễ ra thi nhất)

| Tiêu chí | **FDM** | **FEM** | **FVM** |
|---|---|---|---|
| Tên đầy đủ | Finite Difference Method | Finite Element Method | Finite Volume Method |
| Ý tưởng chính | Xấp xỉ đạo hàm bằng sai phân | Chia miền thành các phần tử hữu hạn | Áp dụng định luật bảo toàn trên từng thể tích điều khiển |
| Miền tính toán | Lưới **đều đơn giản** | Lưới linh hoạt (tam giác, tứ diện…) | Lưới linh hoạt |
| Hình học phức tạp | Kém | **Rất tốt** | Tốt |
| Độ chính xác | Trung bình | Cao | Cao |
| **Bảo toàn khối lượng/năng lượng** | Không tự nhiên | Không tự nhiên | **Rất tốt** |
| Độ khó cài đặt | Dễ | Khó | Trung bình |
| Chi phí tính toán | Thấp | Cao | Trung bình |
| PDE phù hợp | PDE đơn giản | PDE phức tạp | PDE bảo toàn |

### Trong Digital Twin nên dùng cái nào?

**Nguyên tắc thực tế (nhớ 3 dòng này):**
- **FEM** → tốt nhất cho **cơ học, điện từ, đa vật lý**
- **FVM** → tốt nhất cho **dòng chảy và truyền khối**
- **FDM** → tốt nhất cho **bài toán đơn giản và nghiên cứu học thuật**

**Bảng ánh xạ Mô hình vật lý → Phương pháp số:**

| Loại Physics Model | Phương pháp phổ biến |
|---|---|
| Structural Models | FEM |
| Mechanical Models | FEM |
| Electromagnetic Models | FEM |
| Thermal Models | FEM hoặc FVM |
| Fluid Models | FVM |
| Chemical Reactor Models | FVM |
| Battery Electrochemical Models | FEM hoặc FVM |
| Multiphysics Models | **FEM (phổ biến nhất)** |

---

# PHẦN 2 — DATA-DRIVEN MODEL

## 6.1. Bản chất

> **Data-Driven Models** là các mô hình xây dựng **dựa trên dữ liệu**, thay vì dựa trên các định luật vật lý. Khác với Physics-Based Models (mô tả hệ thống bằng ODE, PDE…), Data-Driven Models **học trực tiếp mối quan hệ giữa đầu vào (input) và đầu ra (output)** từ dữ liệu thu thập được.

**Ý tưởng cốt lõi:**
> *"Nếu có đủ dữ liệu, mô hình có thể học cách dự đoán hành vi của hệ thống mà **không cần biết chi tiết các quy luật vật lý bên trong**."*

**Workflow chuẩn:**
```
HISTORICAL DATA (X: Input Features, Y: Target Labels)
        ↓
MACHINE LEARNING (Data Preprocessing → Training Algorithm → Model Training)
        ↓
PREDICTION MODEL (New Input Data → Trained Model → Prediction Output)
```

**Ví dụ minh họa — dự đoán nhiệt độ pin:**

| Dòng điện | Nhiệt độ môi trường | Tốc độ xe | → Nhiệt độ pin |
|---|---|---|---|
| 40 A | 28 °C | 40 km/h | 35 °C |
| 80 A | 30 °C | 80 km/h | 48 °C |

Sau khi huấn luyện: `(50A, 29°C, 60km/h) → Mô hình AI → Nhiệt độ pin ≈ 40°C`

## 6.2. Các ứng dụng

| Chức năng | Ví dụ |
|---|---|
| Dự báo | Nhiệt độ, công suất, chất lượng |
| Bảo trì dự đoán | Phát hiện hỏng hóc |
| Phát hiện bất thường | Rò rỉ, rung, quá nhiệt |
| Tối ưu hóa | Điều khiển năng lượng, lập lịch sản xuất |
| **Mô hình thay thế (Surrogate Model)** | **Thay thế mô phỏng FEM/CFD để tăng tốc tính toán** |

> 💡 **Surrogate Model là cầu nối quan trọng nhất giữa hai thế giới:** dùng AI học kết quả của FEM/CFD (vốn rất chậm) để có được tốc độ thời gian thực.

## 6.3. Ưu điểm (đối chiếu với Physics-Based)

| Tiêu chí | Data-Driven | Physics-Based |
|---|---|---|
| Xây dựng mô hình | Không cần hiểu chi tiết quy luật vật lý | Cần kiến thức vật lý & toán học chuyên sâu |
| Thời gian phát triển | Nhanh nếu có đủ dữ liệu | Mất nhiều thời gian xây dựng và hiệu chỉnh |
| Mô hình hóa hệ phi tuyến | Rất tốt với AI/Deep Learning | Có thể rất phức tạp hoặc khó xây dựng |
| Yêu cầu thông số vật lý | Không cần hoặc cần rất ít | Cần đầy đủ vật liệu, hình học, điều kiện biên |
| Tốc độ suy luận (Inference) | Rất nhanh sau khi huấn luyện | Có thể chậm do phải giải ODE/PDE |
| Khả năng cập nhật | Dễ cập nhật bằng dữ liệu mới | Phải hiệu chỉnh lại mô hình vật lý |
| Độ phức tạp triển khai | Thấp hơn nếu dữ liệu sẵn có | Cao hơn |

**Bổ sung:** khai thác hiệu quả **dữ liệu IoT** (cảm biến, hình ảnh, âm thanh, log vận hành) · phù hợp với hệ thống quá phức tạp không thể xây mô hình vật lý.

## 6.4. Nhược điểm

| Nhược điểm | Giải thích |
|---|---|
| **Phụ thuộc dữ liệu** | Cần dữ liệu lớn, đa dạng và chất lượng cao |
| **Khó giải thích** | Đặc biệt với Deep Learning — mô hình **"hộp đen"** |
| **Ngoại suy kém** | Dễ sai khi gặp điều kiện chưa từng xuất hiện trong dữ liệu |
| **Không đảm bảo định luật vật lý** | Có thể đưa ra kết quả không phù hợp với thực tế vật lý |
| Chi phí dữ liệu cao | Thu thập, lưu trữ, làm sạch, gán nhãn đều tốn kém |
| Nhạy với dữ liệu nhiễu | Chất lượng dữ liệu ảnh hưởng trực tiếp chất lượng mô hình |
| Cần huấn luyện lại | Khi hệ thống hoặc dữ liệu thay đổi đáng kể |
| **Khó dự đoán các sự kiện hiếm** | Thiếu dữ liệu cho tình huống bất thường hoặc nguy hiểm |

> ⚠️ **4 nhược điểm in đậm ở trên chính là 4 lý do khiến Digital Twin công nghiệp không thể chỉ dùng AI thuần.**

## 6.5. Phân loại Data-driven models (5 nhóm)

| Nhóm | Thuật toán được nêu |
|---|---|
| **Statistical Models** | Linear Regression, Logistic Regression, ARIMA |
| **Traditional Machine Learning** | Decision Tree, Random Forest, SVM, XGBoost, LightGBM |
| **Deep Learning** | CNN, ANN, RNN, LSTM, GNN |
| **Probabilistic Models** | Bayesian Networks, Gaussian Processes |
| **Reinforcement Learning** | Q-Learning |

## 6.6. Nền tảng thuật toán được ôn lại trong bài

### (a) Linear Regression + L2 Loss + Gradient Descent

**Giả định:** X và Y có quan hệ tuyến tính → tồn tại `Y = f(X) = w·X + b`
**Mục tiêu:** tìm cặp tham số **(w, b)**
**Giải pháp:** xây dựng **hàm mất mát (loss function)** và **tối thiểu hóa** nó.

**Ví dụ trực quan:** X = 3, y = 8
| Model | w, b | f(X) | Loss = (f(X) − y)² |
|---|---|---|---|
| Model 1 | w=2, b=3 | 9 | **1** ← tốt hơn |
| Model 2 | w=1, b=7 | 10 | 4 |

**L2 Loss trên tập D = {Xi, Yi} gồm n mẫu:**
```
L = (1/n)·Σ |f(Xi) − Yi|²  =  (1/n)·Σ |w·Xi + b − Yi|²
```
> **Ghi chú then chốt:** Loss function **chỉ phụ thuộc vào tham số w và b** → mặt lỗi trong không gian (w, b) là một **mặt lồi (convex)** có điểm cực tiểu duy nhất **(w\*, b\*)**.

**Gradient Descent — "đang ở trên sườn dốc, làm sao xuống đáy?"**
> *Tìm gradient rồi bước một bước nhỏ theo hướng ngược lại.*

```
1. Khởi tạo w, b với giá trị ngẫu nhiên
2. Lặp nhiều lần:
     - Tính gradient của Loss theo w và b
     - Cập nhật ngược hướng gradient:
           w := w − α·∂Loss/∂w
           b := b − α·∂Loss/∂b
```
(α = learning rate — độ lớn của bước đi)

### (b) Classification & SVM

- **Bài toán 2 lớp:** có vô số đường phân tách — **làm sao để tối ưu đường biên giới?**
- **SVM (Support Vector Machine):** **tối ưu cái LỀ (margin) của đường biên giới** — tìm hyperplane phân tách tốt nhất giữa các lớp.
- Khái niệm liên quan: **Support Vectors** (các điểm nằm sát lề, quyết định vị trí biên), **Outlier**.
- **SVM cho nhiều lớp:** nhiều hyperplane chia không gian.
- **SVM cho bài toán phi tuyến:** dùng **Kernel method** — ánh xạ dữ liệu lên không gian cao chiều hơn, nơi tồn tại một **decision surface** phân tách được.

### (c) Từ Linear Regression → Neural Network

```
Linear regression :  X → [ f(X) = W·X + b ]     → Y
Neural Network    :  X → [ f(X) = a(W·X + b) ]  → Y      ← MỘT NEURON
```
Khác biệt duy nhất: thêm **hàm kích hoạt a()**. Bài dùng **ReLU = max(0, x)**.

**Bài toán supervised learning tổng quát:**
```
Functions F : f: X → Y
Training data : {(xi, yi)}
LEARNING   : tìm f̂ ∈ F sao cho  yi ≈ f̂(xi)
PREDICTION : y = f̂(x)
```
→ Với deep learning, **f phức tạp hơn nhiều với hàng triệu biến.**

**Mạng Fully Connected:** `input layer → hidden layer 1 → hidden layer 2 → output layer`

### (d) CNN — Convolutional Neural Network

**Phép tích chập:** kernel **K** trượt trên đầu vào **f** → đầu ra **g = K \* f**

**Trong xử lý ảnh, kernel quyết định hiệu ứng:**
| Kernel | Hiệu ứng |
|---|---|
| `(1/9)·[1 1 1; 1 1 1; 1 1 1]` | Làm mờ (trung bình) |
| `[0 0 0; 0 1 0; 0 0 0]` | Không đổi (identity) |
| `[-1 0 1; -2 0 2; -1 0 1]` | Phát hiện biên (dọc) |
| `[-1 -2 -1; 0 0 0; 1 2 1]` | Phát hiện biên (ngang) |

**Đặc điểm lớp tích chập (khác nơ-ron kết nối đầy đủ):**
1. Mỗi nơ-ron tích chập (**filter**) chỉ **kết nối cục bộ** với dữ liệu đầu vào.
2. Filter **trượt từ trái sang phải, từ trên xuống dưới**, sinh ra **bản đồ kích hoạt (activation map)**.
3. **Chiều sâu của filter = chiều sâu của khối dữ liệu đầu vào.**

**⭐ Công thức kích thước đầu ra (bắt buộc thuộc):**
```
Output size = (N − F) / stride + 1
```
Ví dụ N = 7, F = 3:
- stride 1 → (7−3)/1 + 1 = **5**
- stride 2 → (7−3)/2 + 1 = **3**
- stride 3 → (7−3)/3 + 1 = 2.33 → **không hợp lệ**

**Zero padding:** để **bảo toàn kích thước**, thêm viền các số 0.
> Ví dụ: đầu vào 7×7, filter 3×3, stride 1, padding 1 → đầu ra **7×7**.

**Ví dụ chuẩn:** ảnh **32×32×3**, filter **5×5×3** → activation map **28×28×1** (vì (32−5)/1 + 1 = 28).
- Thêm filter khác → thêm activation map (trọng số các filter **khác nhau**).
- 6 filter → 6 activation map ghép lại thành **"ảnh mới" 28×28×6**.

**Kiến trúc CNN:** một dãy các lớp tích chập nối liên tiếp, **xen kẽ bởi các hàm kích hoạt (ReLU)**:
```
32×32×3  ──CONV+ReLU (6 filter 5×5×3)──►  28×28×6
         ──CONV+ReLU (10 filter 5×5×6)──►  24×24×10  ──►  …
```

## 6.7. ⭐ Bảng ứng dụng tổng kết (slide cuối — rất dễ ra thi)

| Application | Input Data | Data-Driven Model | Output |
|---|---|---|---|
| **Battery Digital Twin** | Voltage, current, temperature | **LSTM, ANN** | SOH, RUL |
| **Predictive Maintenance** | Vibration, temperature | **Random Forest, SVM** | Failure probability |
| **Smart Building** | Weather, occupancy | **LSTM** | Energy consumption |
| **Wind Turbine** | Wind speed, rotor speed | **XGBoost** | Power output |
| **Manufacturing** | Process parameters | **Decision Tree** | Product quality |
| **Traffic Digital Twin** | Images, GPS | **CNN, Transformer** | Traffic prediction |
| **Aircraft Engine** | Engine sensors | **Autoencoder** | Fault detection |
| **Face Anti-Spoofing** | Images | **CNN, ViT** | Live/Fake classification |

> **Quy luật ngầm rất đáng nhớ:** dữ liệu **chuỗi thời gian** → LSTM/ANN · dữ liệu **bảng (tabular)** → Random Forest/XGBoost/Decision Tree · dữ liệu **ảnh** → CNN/ViT/Transformer · **phát hiện bất thường không nhãn** → Autoencoder.

---

# PHẦN 3 — HYBRID MODEL (Kết luận của cả bài)

Mặc dù không có section riêng, đây là **thông điệp kết luận** được nhắc lại 2 lần:

> **Xu hướng hiện nay trong Digital Twin là Hybrid Models (Physics-Informed / Physics-Guided Models)**, kết hợp:
> - **Physics-Based Models** → đảm bảo **tính nhất quán với các định luật vật lý** và **khả năng ngoại suy**.
> - **Data-Driven Models** → **học các quan hệ phức tạp**, **tăng tốc tính toán** và **thích nghi với dữ liệu thực tế**.
>
> Cách tiếp cận này **tận dụng điểm mạnh của cả hai, đồng thời giảm thiểu nhược điểm của từng phương pháp riêng lẻ**.

**"Xóa bỏ ranh giới":** Mô hình hỗn hợp kế thừa **khả năng diễn giải của Vật lý** và **tốc độ của Dữ liệu**. Có thể **tích hợp trực tiếp các định luật vật lý vào hàm mất mát của Machine Learning**, hoặc kết hợp song song.

**Ánh xạ 3 kiểu tích hợp ↔ mức độ "sâu" của sự kết hợp:**
```
Series (nông)  →  Parallel (trung bình)  →  Combined (sâu nhất, vật lý nằm trong thuật toán)
```

---

# 7. BẢNG CÔNG THỨC CẦN THUỘC

| # | Lĩnh vực | Công thức | Tên gọi |
|---|---|---|---|
| 1 | Dynamic | `ẋ = f(x, u, t)` | State-Space tổng quát |
| 2 | Dynamic | `mẍ + cẋ + kx = F` | Mass-Spring-Damper |
| 3 | Dynamic | `J·dω/dt = Tm − TL` | Động lực học rotor |
| 4 | Structural | `σ = E·ε` | Định luật Hooke |
| 5 | Thermal | `C·dT/dt = Q_in − Q_out` | Lumped Thermal (0D) |
| 6 | Thermal | `q = −k∇T` | Dẫn nhiệt (Fourier) |
| 7 | Thermal | `T(t) = Ts + (T0 − Ts)·e^(−kt)` | Đối lưu theo thời gian |
| 8 | Thermal | `Q = εσA(T⁴ − T_env⁴)` | Stefan-Boltzmann (bức xạ) |
| 9 | Fluid | `∂ρ/∂t + ∇·(ρv) = 0` | Phương trình liên tục |
| 10 | Fluid | `ρ(∂v/∂t + v·∇v) = −∇p + μ∇²v + ρg` | Navier-Stokes |
| 11 | Electrical | `V = IR` · `P = VI` · `ΣI_in = ΣI_out` · `ΣV = 0` | Ohm, Công suất, KCL, KVL |
| 12 | Electrical | `L·d²i/dt² + R·di/dt + (1/C)·i = V` | Mạch RLC |
| 13 | Electrical | `Vt = Voc − I·R0` | Rint model (pin) |
| 14 | Electrical | `Vt = Voc − I·R0 − V_RC` | Thevenin model (pin) |
| 15 | Electrical | `ẋ = Ax + Bu` ; `y = Cx + Du` | State-Space mạch điện |
| 16 | Chemical | `CA(t) = CA0·e^(−kt)` | Phản ứng bậc nhất |
| 17 | Chemical | `K = [C]/([A][B])` | Hằng số cân bằng |
| 18 | Chemical | `J = −D·∂C/∂x` ; `∂C/∂t = D·∂²C/∂x²` | Định luật Fick 1 & 2 |
| 19 | Chemical | `N = km·(Cs − C)` | Truyền khối |
| 20 | Electrochem | `j = j₀(e^(αaFη/RT) − e^(−αcFη/RT))` | Butler-Volmer |
| 21 | PDE | `∂T/∂t = α∇²T` | Truyền nhiệt |
| 22 | PDE | `∂²u/∂t² = c²·∂²u/∂x²` | Phương trình sóng |
| 23 | Số | `∂T/∂x ≈ (T_{i+1} − T_i)/Δx` | Sai phân bậc nhất (FDM) |
| 24 | Số | `Tích lũy = Vào − Ra + Nguồn` | Bảo toàn trong FVM |
| 25 | ML | `L = (1/n)Σ\|w·Xi + b − Yi\|²` | L2 Loss |
| 26 | ML | `w := w − α·∂L/∂w` | Cập nhật Gradient Descent |
| 27 | DL | `Output = (N − F)/stride + 1` | Kích thước đầu ra lớp tích chập |

---

# 8. NHỮNG ĐIỂM DỄ NHẦM (BẪY THI)

| # | Điểm dễ nhầm | Câu trả lời đúng theo slide |
|---|---|---|
| 1 | FEM / CFD có phải là mô hình vật lý không? | **KHÔNG.** Cả hai là **phương pháp số** để tìm nghiệm gần đúng của PDE. Slide nhấn mạnh điều này 2 lần. |
| 2 | LPM và ODE có phải là một? | **KHÔNG.** LPM = *cách mô hình hóa*; ODE = *dạng phương trình*. Hầu hết LPM biểu diễn bằng ODE, nhưng không phải mọi ODE đều từ LPM. |
| 3 | Digital Shadow vs Digital Twin | Digital Shadow = **chỉ giám sát**. Digital Twin thông minh = có **Prediction Module** → dự báo, tối ưu hóa, ra quyết định chủ động. |
| 4 | Update Module vs Prediction Module | Update = **đồng bộ DT với dữ liệu thực (hiện tại)**. Prediction = **dự đoán trạng thái tương lai**. |
| 5 | ODE vs PDE khác nhau ở đâu? | Biến độc lập: ODE chỉ theo **thời gian**; PDE theo **thời gian + không gian**. |
| 6 | Multiphysics xuất hiện ở cả 2 trục phân loại | Đúng — nó vừa là một **hiện tượng** (nhiều trường tương tác) vừa là một **phương pháp xây dựng mô hình**. |
| 7 | Trong DT pin, loại ECM nào quan trọng nhất? | **ECM là loại quan trọng nhất trong Digital Twin pin**; trong đó Rint là đơn giản nhất, P2D là chuẩn mực chính xác nhất. |
| 8 | Phương pháp nào bảo toàn khối lượng/năng lượng tốt nhất? | **FVM** ("Rất tốt"). FDM và FEM đều "Không tự nhiên". |
| 9 | Physics-Based có ưu điểm gì mà AI không có? | **Giải thích được (cao), ngoại suy tốt, chính xác khi ít dữ liệu, đảm bảo tuân thủ định luật vật lý.** |
| 10 | Data-Driven yếu nhất ở đâu? | **Ngoại suy kém** + **không đảm bảo định luật vật lý** + **khó dự đoán sự kiện hiếm**. |
| 11 | Surrogate Model dùng để làm gì? | Thay thế mô phỏng **FEM/CFD** để **tăng tốc tính toán**. |
| 12 | Loss function của Linear Regression phụ thuộc vào gì? | **Chỉ phụ thuộc vào tham số w và b** (không phụ thuộc dữ liệu sau khi dữ liệu đã cố định). |

---

# 9. KHUNG CÂU HỎI TỰ KIỂM TRA

**Mức nhớ:**
1. Vẽ lại kiến trúc 5 tầng của Digital Twin và chỉ ra Prediction nằm ở tầng nào.
2. Liệt kê 4 chức năng của Analytics Layer.
3. Vẽ vòng lặp Dự đoán – Cập nhật và nêu vai trò 2 mô-đun.
4. Liệt kê 8 loại Physics-Based Model theo hiện tượng vật lý và 6 loại theo phương pháp xây dựng.
5. Viết phương trình Navier-Stokes và gọi tên 4 số hạng.

**Mức hiểu:**
6. Vì sao nói Prediction Module biến Digital Shadow thành Digital Twin?
7. So sánh Physics-Based và Data-Driven theo 6 tiêu chí.
8. Vì sao FEM không được coi là mô hình vật lý?
9. Giải thích vì sao FVM bảo toàn khối lượng/năng lượng tốt hơn FDM và FEM.
10. Vì sao ECM và State-Space là hai loại Electrical Model được dùng nhiều nhất trong DT công nghiệp?

**Mức vận dụng:**
11. Động cơ có J = 0.5 kg·m², Tm = 60 N·m, TL = 40 N·m, ω hiện tại = 100 rad/s. Tính ω sau 1 giây.
12. Ảnh đầu vào 32×32×3, dùng 10 filter kích thước 5×5, stride 1, không padding. Kích thước đầu ra là bao nhiêu?
13. Cho X = 3, y = 8, model có w = 2, b = 3. Tính L2 loss. Nếu α = 0.1, viết công thức cập nhật w.
14. Một DT giám sát cầu đường bộ: hãy chọn loại Physics Model + phương pháp số + loại Data-Driven Model phù hợp và giải thích.
15. Một DT pin xe điện cần chạy thời gian thực trên bộ điều khiển nhúng: nên chọn ECM hay P2D? Vì sao?

**Mức tổng hợp:**
16. Thiết kế kiến trúc Hybrid cho Digital Twin của một tua-bin gió: chỉ rõ phần nào dùng Physics, phần nào dùng AI, và dùng kiểu tích hợp Series / Parallel / Combined nào.
17. Với hệ thống nào thì Data-Driven thuần túy là lựa chọn nguy hiểm? Lập luận dựa trên bảng nhược điểm.

---

# 10. SƠ ĐỒ NHỚ TOÀN BÀI (1 trang)

```
                        PREDICTION MODULE
                    (trái tim của Digital Twin)
                              │
        ┌─────────────────────┼─────────────────────┐
        │                     │                     │
  PHYSICS-BASED         DATA-DRIVEN             HYBRID
  "định luật vật lý"    "học từ dữ liệu"    "kết hợp cả hai"
        │                     │                     │
   ┌────┴────┐          ┌─────┴─────┐         ┌─────┴─────┐
   │         │          │           │       Series  Parallel  Combined
TRỤC A    TRỤC B     Statistical  ML/DL
(hiện     (phương    Traditional ML
tượng)    pháp)      Deep Learning
   │         │       Probabilistic
   │         │       Reinforcement
   │         └── LPM → ODE → PDE → FEM → CFD → Multiphysics
   │                              (đơn giản/nhanh → chính xác/chậm)
   │
   └── Dynamic · Structural · Thermal · Fluid · Electrical
       · Chemical · Electrochemical · Multiphysics

  PHƯƠNG PHÁP SỐ:  FEM (cơ học, điện từ, đa vật lý)
                   FVM (dòng chảy, truyền khối)
                   FDM (bài toán đơn giản, học thuật)

  ĐÁNH ĐỔI CỐT LÕI:
     Physics  → giải thích ✓  ngoại suy ✓  ít dữ liệu ✓  |  chậm ✗
     AI       → nhanh ✓  phi tuyến ✓  dễ cập nhật ✓      |  hộp đen ✗ ngoại suy ✗
     Hybrid   → lấy ưu, bỏ nhược  ← ĐÍCH ĐẾN CỦA BÀI HỌC
```

---

*Tài liệu này được tổng hợp hoàn toàn từ nội dung file `DT04_Prediction_Update_V2.pdf`, không bổ sung dữ liệu bên ngoài.*
