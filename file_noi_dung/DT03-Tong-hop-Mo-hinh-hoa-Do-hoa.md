# BÀI 3 — MÔ HÌNH HÓA ĐỒ HỌA
### Nhập môn Công nghệ Song sinh Thực – Số và Ứng dụng (Digital Twin)
*Tổng hợp kiến thức từ bộ slide DT03 (194 slide) — Trường CNTT&TT*

---

## PHẦN 0. THỐNG KÊ & BẢN ĐỒ KIẾN THỨC

### 0.1. Thống kê cấu trúc tài liệu

| Chương | Tên | Slide | Số slide | Tỉ trọng |
|---|---|---|---|---|
| **1** | Tổng quan | 2–14 | 13 | ~7% |
| **2** | **Mô hình hóa** | 15–128 | 114 | **~59%** |
| **3** | Tạo bóng và ánh sáng | 129–195 | 67 | ~34% |

### 0.2. Chi tiết Chương 2 (phần nặng nhất)

| Chủ đề con | Slide | Số slide |
|---|---|---|
| Primitives + Điểm + Đoạn thẳng (DDA, Bresenham, Trung điểm) | 16–44 | 29 |
| Đường tròn & Ê-líp | 45–48 | 4 |
| **Đường cong** (Hermite, Bézier, B-Spline, NURBS) | 49–99 | **51** |
| Đa giác (scan-line) | 100–105 | 6 |
| Ký tự (font) | 106–109 | 4 |
| Khối đặc (B-rep, CSG, Voxel) | 110–127 | 18 |

> **Nhận xét của giảng viên:** Trọng tâm rơi vào **đường cong tham số** (51 slide) và **shading** (23 slide). Đây là hai chỗ ra đề thi nhiều nhất. Phần đường thẳng nhiều slide nhưng thực chất chỉ xoay quanh 3 thuật toán.

### 0.3. Bản đồ kiến thức tổng thể

```
                    TIẾN TRÌNH XỬ LÝ ĐỒ HỌA (slide 10)
                              │
     ┌────────────────────────┼────────────────────────┐
     ▼                        ▼                        ▼
  MODELING                RENDERING               DISPLAYING
 (mô tả cái gì)        (mô tả thành ảnh)        (hiện lên màn hình)
     │                        │                        │
Geometric Engine        Rendering Engine        Raster & Display Engine
 - modeling              - viewing/projection    - hardware
 - modeling transf.      - drawing & clipping    - rasterization
 - color models          - local illumination
 - material property       & shading
 - lighting property     - texture mapping
                         - global rendering
        ▲                        ▲
     CHƯƠNG 2                 CHƯƠNG 3
```

**Ý tưởng xuyên suốt bài học:** Muốn có một Digital Twin nhìn thấy được, phải đi qua đủ 3 khâu: **mô tả hình học → tính ánh sáng → rời rạc hóa thành pixel**.

---

## PHẦN 1. TỔNG QUAN (slide 2–14)

### 1.1. Vai trò của đồ họa máy tính trong Digital Twin ⭐ *(slide 3 — trọng tâm thi)*

Đây là slide duy nhất nối trực tiếp môn học với Digital Twin. Bốn vai trò:

| # | Vai trò | Nội dung |
|---|---|---|
| 1 | **Trực quan hóa dữ liệu thời gian thực** (Real-time Visualization) | Chuyển dòng dữ liệu thô (số, text) từ cảm biến IoT thành mô hình 3D sống động → người vận hành quan sát trạng thái dây chuyền sản xuất trực quan |
| 2 | **Mô phỏng tương tác vật lý** (Physical & Kinematic Simulation) | Dùng Collision Detection, Rigid Body Dynamics để mô phỏng chuyển động, va chạm, tác động vật lý của máy móc **trước khi** vận hành thực tế |
| 3 | **Giao diện tương tác người–máy** (HCI & UI/UX) | Cho phép điều khiển ngược lại hệ thống vật lý; tích hợp VR/AR để kỹ sư "ở bên trong" dây chuyền sửa chữa từ xa |
| 4 | **Tối ưu hóa hiệu năng render** (Rendering Pipeline) | Tối ưu GPU, kỹ thuật LoD (Level of Detail), dùng Game Engine (Unity, Unreal) để render mượt mô hình 3D quy mô lớn |

### 1.2. Định nghĩa nền tảng (slide 4)

- **Định nghĩa ISO:** Kỹ thuật đồ họa là *các phương pháp và công nghệ để chuyển đổi dữ liệu đến hoặc từ các thiết bị đồ họa sử dụng máy tính*.
- **Computer Graphics:** lĩnh vực của CNTT nghiên cứu, xây dựng và tập hợp các công cụ (mô hình lý thuyết + phần mềm) để **kiến tạo, lưu trữ, xử lý** các *model* và *image* của đối tượng.
- **Interactive Computer Graphics:** người dùng điều khiển nội dung, cấu trúc, hình ảnh của đối tượng thông qua **phản hồi hình ảnh tức thì**.

### 1.3. Hai trường phái đồ họa ⭐ *(slide 5–9)*

| Tiêu chí | **Đồ họa điểm (Raster / Sample-based)** | **Đồ họa vector** |
|---|---|---|
| Bản chất | Hiển thị qua từng pixel (mẫu rời rạc) | Mô hình hình học + quá trình tô trát |
| Công thức | Lưới (grid) các pixel rời rạc | **Vector = geometrical model + rendering** |
| Thao tác | Thay đổi/xóa thuộc tính **từng pixel**; copy pixel giữa các ảnh | **Không** thay đổi thuộc tính từng điểm trực tiếp; xử lý theo **thành phần hình học cơ sở** rồi tô trát lại |
| Ưu thế | Thay đổi từng phần, từng vùng ảnh dễ dàng | **Quan sát ở nhiều góc độ** bằng cách đổi điểm nhìn và góc nhìn |
| Ví dụ | Bitmap | Đường, đa giác, spline |

Pipeline vector: `Graphical Model + Rendering Parameters → Rendering → Output Device`

### 1.4. Ba khâu trong tiến trình xử lý (slide 10–13)

| Khâu | Câu hỏi trả lời | Engine phụ trách |
|---|---|---|
| **Modeling** | *To describe* — mô tả cho máy tính biết cái bàn, cái xe là gì | Geometric Engine |
| **Rendering** | *To capture* — tạo ảnh 2D từ mô hình 2D/3D | Rendering Engine |
| **Displaying** | *To show* — sinh ảnh trên màn hình | Raster & Display Engine |

**Rasterizer (slide 13):** chuyển đối tượng hình học (**vertex**) → biểu diễn ảnh (**fragment**).
- `Fragment = image fragment = Pixel + associated data (color, depth, stencil...)`
- Chấp nhận **nội suy** để tạo các điểm ảnh.

### 1.5. Hệ thống đồ họa (slide 14)

- **Phần cứng đồ họa:** tập hợp thiết bị điện tử (CPU, bộ nhớ, màn hình) để chạy phần mềm đồ họa.
- **Phần mềm đồ họa hệ thống:** tập lệnh đồ họa (graphics output commands), quyết định hiển thị **cái gì** (what object) và **như thế nào** (how). Đặc điểm quan trọng: **phụ thuộc vào phần cứng**.

---

## PHẦN 2. MÔ HÌNH HÓA (slide 15–128)

## 2.1. Graphics Output Primitives (slide 16)

Mọi đối tượng đồ họa đều được tạo từ **nguyên thủy** (primitives) — các hàm hình học đơn giản, kết hợp lại thành cấu trúc phức tạp.

- **Nguyên thủy đơn giản nhất:** Điểm (Point/pixel), Đoạn đường (Line segment), Cung tròn (Circle).
- Các gói đồ họa khác nhau cung cấp thêm: hình chữ nhật, phần hình nón, đường cong spline, bề mặt.

## 2.2. Điểm (slide 17–19)

- Vị trí điểm xác định bởi **tham số tọa độ**; giá trị có thể **chính xác hoặc gần đúng**.
- **Scan Converting a Point:** mỗi pixel trên màn hình **không** đại diện cho một điểm toán học, mà là một **khu vực** về lý thuyết chứa vô số điểm. Quét-chuyển đổi một điểm = chiếu sáng pixel chứa điểm đó.
- API cơ bản:
  - `setpixel(x, y, color)` — ghi vào frame buffer tại cột x, dòng quét y
  - `color = getpixel(x, y)` — đọc giá trị hiện tại
- OpenGL: `glBegin(GL_POINTS); glVertex2f(px,py); glEnd();`
- Camera trực giao: `glMatrixMode(GL_PROJECTION); glLoadIdentity(); gluOrtho2D(xmin,xmax,ymin,ymax);`

## 2.3. Đoạn thẳng — Ba dạng biểu diễn ⭐ *(slide 20–27)*

`Line_Seg = {(x1,y1), (x2,y2)}`

**(a) Tường minh (Explicit):**
```
y = m·x + b
m = (y2-y1)/(x2-x1) = Δy/Δx        (hệ số góc)
b = y1 - m·x1                       (giao trục y)
Δx = Δy/m ;  Δy = m·Δx
```

**(b) Không tường minh (Implicit):**
```
(y2-y1)x - (x2-x1)y + x2y1 - x1y2 = 0
⟺ r·x + s·y + t = 0
với  r = (y2-y1),  s = -(x2-x1),  t = x2y1 - x1y2
```

**(c) Tham số (Parametric):**
```
P(u) = P1 + u(P2 - P1),   u ∈ [0,1]
X = x1 + u(x2-x1)
Y = y1 + u(y2-y1)
⟺ P(u) = (1-u)P1 + u·P2
```

> **Bài tập 1 (slide 27):** P1 = (2,2), P2 = (6,6).
> - u = 0 → P = (2,2)
> - u = 1 → P = (6,6)
> - u = 1/2 → P = (4,4)

### Vấn đề của cách tiếp cận ngây thơ (slide 21–22)

Cách làm đơn giản: tăng x → tính y → ép kiểu int → vẽ (x, (int)y).

**Hạn chế:**
- Tốt với đường **độ dốc ≤ 1**; **không hiệu quả** khi độ dốc > 1
- Đường trở nên **không liên tục** (đứt quãng)
- Cần thêm **hơn 1 pixel mỗi cột** để đoạn thẳng mượt

### Scan conversion & Ràng buộc (slide 28–29)

- **Rasterization:** xác định pixel nào xấp xỉ tốt nhất cho đường mong muốn.
- **Quét chuyển đổi:** rasterization + tạo ảnh **theo thứ tự dòng quét**.

**4 ràng buộc bắt buộc của thuật toán vẽ đoạn thẳng:**
1. Đoạn thẳng phải **xuất hiện thẳng**
2. Điểm đầu/cuối **chính xác tại điểm ảnh**
3. **Độ sáng nhất quán** dọc chiều dài đoạn thẳng
4. **Thời gian xử lý nhanh**

## 2.4. BA THUẬT TOÁN VẼ ĐOẠN THẲNG ⭐⭐ *(trọng tâm số 1)*

### (1) DDA — Digital Differential Analyzer (slide 31–33)

**Nguyên lý:** nội suy dựa trên `m = Δy/Δx`
```
xi = x(i-1) + 1
yi = y(i-1) + m
```
Với đường **độ dốc > 1** → **đảo vai trò x và y**, tính các giá trị x liên tiếp.

**Pseudo-code:**
```c
DDA(int x1, int y1, int x2, int y2) {
   numsteps = x2 - x1;
   xinc = 1;
   yinc = (y2 - y1) / numsteps;    // = m
   x = x1;  y = y1;
   ColorPixel(x, Round(y));
   for (int i = 0; i < numsteps; i++) {
      x += xinc;
      y += yinc;
      SetPixel(x, Round(y));
   }
}
```

**Chi phí mỗi bước:** 4 phép toán dấu phẩy động + 2 phép toán số nguyên.

**Mở rộng:** DDA dùng được cho tam giác, đa giác, và cả hàm phi tuyến (perspective-correct texture mapping, quadratic curves, traversing voxels). Rất phù hợp triển khai **phần cứng**.

---

### (2) Bresenham (slide 34–38)

**Lịch sử:** 1960 phát triển tại IBM → 1962 đưa vào sử dụng → 1963 trình bày tại hội nghị ACM (Denver, Colorado).

**Ưu điểm cốt lõi:** chỉ dùng **phép cộng, trừ số nguyên và dịch bit** — loại bỏ hoàn toàn phép chia và làm tròn của DDA.

**Xây dựng (xét 0 < k < 1):**
```
d2 = y - yi     = k(xi+1) + b - yi
d1 = yi+1 - y   = yi + 1 - k(xi+1) - b

if d1 ≤ d2  →  yi+1 = yi + 1
else        →  yi+1 = yi

D  = d1 - d2 = -2k(xi+1) + 2yi - 2b + 1
Pi = Δx · D = Δx(d1 - d2)
```

**Công thức tăng dần (incremental):**
```
Pi = -2Δy·xi + 2Δx·yi + c
P1 = -2Δy + Δx          (giá trị khởi tạo)

Nếu Pi ≤ 0 → yi+1 = yi + 1,  Pi+1 = Pi - 2Δy + 2Δx
Nếu Pi > 0 → yi+1 = yi,      Pi+1 = Pi - 2Δy
```

**Mở rộng:** đã được phát triển cho đường tròn, ê-líp, đường cong Bézier bậc hai/bậc ba, và các phiên bản **khử răng cưa (anti-aliasing)**.

---

### (3) Giải thuật Trung điểm — Midpoint (slide 39–44)

**Xuất xứ:** Jack Bresenham 1965 / Pitteway 1967; VanAken áp dụng cho đường thẳng và đường tròn năm 1985. Công thức **đơn giản hơn**, cho kết quả **tương tự Bresenham**.

**Nguyên lý:** dùng biểu diễn **không tường minh** `F(x,y) = a·x + b·y + c`
```
F(xi, yi) = 0  →  điểm nằm TRÊN đường
F(xi, yi) < 0  →  điểm nằm PHÍA TRÊN đường
F(xi, yi) > 0  →  điểm nằm PHÍA DƯỚI đường
```
Xét **trung điểm M** của đoạn AB (A ở trên, B ở dưới), `d = F(xi+1, yi+1/2)`:
- `d > 0` → chọn **B** → `yi+1 = yi`
- `d < 0` → chọn **A** → `yi+1 = yi + 1`
- `d = 0` → chọn A hoặc B tùy ý

**Cập nhật biến quyết định:**
- Chọn B: M chỉ tăng theo x một đơn vị → `d(i+1) = di + a`
- Chọn A: M tăng cả x và y → `d(i+1) = di + a + b`, với `a + b = dy - dx`

**Pseudo-code:**
```
dx = x_end - x_start
dy = y_end - y_start
d  = 2*dy - dx            // khởi tạo
x  = x_start ;  y = y_start
while x < x_end
    if d <= 0 then
        d = d + (2*dy)        // chọn B
        x = x + 1
    else
        d = d + 2*(dy - dx)   // chọn A
        x = x + 1
        y = y + 1
    endif
    SetPixel(x, y)
endwhile
```

### 📊 So sánh 3 thuật toán

| | DDA | Bresenham | Trung điểm |
|---|---|---|---|
| Phép toán | Dấu phẩy động (4 FP + 2 int/bước) | **Chỉ số nguyên** (+, −, dịch bit) | Chỉ số nguyên |
| Có chia / làm tròn? | **Có** | Không | Không |
| Biểu diễn dùng | Tường minh `y = mx+b` | Tường minh | **Không tường minh** |
| Điểm mạnh | Đơn giản, dễ mở rộng phi tuyến, hợp phần cứng | Nhanh, kinh điển | Công thức đơn giản nhất, mở rộng tốt cho tròn/ê-líp |

## 2.5. Đường tròn và Ê-líp (slide 45–48)

**Đường tròn — Midpoint (slide 46–47):**
- Dùng biểu diễn **không tường minh**: `(x - xc)² + (y - yc)² - r² = 0`
- Chỉ chạy thuật toán trên **1/8 đường tròn**, còn lại **lấy đối xứng**.

```
d = 1 - r ;  x = 0 ;  y = r        // khởi tạo
while y < x                        // dừng ở đường chéo = hết octant
    if d < 0 then
        d = d + 2*x + 3            // chọn A
        x = x + 1
    else
        d = d + 2*(x - y) + 5      // chọn B
        x = x + 1
        y = y - 1
    endif
    SetPixel(cx + x, cy + y)       // tịnh tiến về tâm đường tròn
endwhile
```

**Ê-líp (slide 48):** tương tự đường tròn nhưng áp dụng cho **1/4 đường ê-líp**, sau đó lấy đối xứng.
```
b²x² + a²y² - a²b² = 0      (2a: đường kính ngang, 2b: đường kính dọc)
```

---

## 2.6. MÔ HÌNH HÓA ĐƯỜNG CONG ⭐⭐ (slide 49–99 — trọng tâm số 2)

### 2.6.1. Khái niệm nền (slide 51–53)

- **Control Points (điểm kiểm soát):** tập điểm ảnh hưởng đến hình dạng đường cong.
- **Control Polygon:** đa giác nối các điểm kiểm soát; chỉ ảnh hưởng đến hình dạng.

**Hai kiểu mô hình:**

| | **Interpolation (nội suy)** | **Approximation (xấp xỉ)** |
|---|---|---|
| Đặc điểm | Tất cả điểm **nằm trên** hình đã tạo | Các điểm **không cần** nằm trên hình |
| Quan hệ | Đường cong **đi qua** các điểm kiểm soát | Đường cong không đi qua điểm kiểm soát |

**Tại sao không dùng polyline/đa giác?** (slide 53)
- Polyline/đa giác: cần **lượng dữ liệu lớn** để đạt độ chính xác tốt; thao tác tương tác **tẻ nhạt**.
- Đường cong bậc cao: **nhỏ gọn hơn** (ít dung lượng), **dễ thao tác tương tác**.

### 2.6.2. Đường cong tham số (slide 54–56)

```
P(u) = [x  y  z]     với  x = x(u), y = y(u), z = z(u)
Bề mặt 2D:  Q(u,v) = [x(u,v), y(u,v), z(u,v)]
```

**Ưu điểm (rất hay hỏi):**
- Khắc phục vấn đề của dạng tường minh và không tường minh
- **Không có độ dốc hình học** (có thể vô hạn) → thay bằng **vectơ tiếp tuyến tham số** (không bao giờ vô hạn)
- Đường cong được xấp xỉ bởi đường cong **đa thức piecewise** (từng đoạn)
- **Ít tham số hơn lưới đa giác** → tạo nhanh hơn, dễ chỉnh sửa hơn (lưới đa giác có ít nhất bằng số đỉnh tham số)
- Các trường pháp tuyến (normal) xác định đúng ở **mọi nơi**
- **Dễ tạo hoạt hình** hơn lưới đa giác
- Số lượng tham số cho biết **kích thước (chiều)** của đối tượng

### 2.6.3. Tại sao dùng đa thức, và tại sao bậc 3? (slide 58–59)

**Tại sao đa thức (polynomials)?**
1. Dạng đơn giản
2. Thuộc tính nổi tiếng, đã được hiểu rõ
3. Độ linh hoạt hình dạng vừa phải
4. Không phụ thuộc vào số liệu cơ bản
5. Tính toán dễ sử dụng

**Tại sao bậc 3 (cubic)?** ⭐
1. Bậc thấp hơn → **quá ít linh hoạt** trong kiểm soát hình dạng
2. Bậc cao hơn → **sai lệch không mong muốn** + tính toán nhiều hơn
3. Bậc **thấp nhất cho phép đặc tả điểm cuối và đạo hàm** của chúng
4. Bậc **thấp nhất không phẳng trong 3D**

**Dạng tổng quát:**
```
x(u) = axu³ + bxu² + cxu + dx
y(u) = ayu³ + byu² + cyu + dy
z(u) = azu³ + bzu² + czu + dz
```

### 2.6.4. Ba họ đường cong chính (slide 62)

| Họ | Được xác định bởi |
|---|---|
| **Hermite** | 2 điểm cuối + **2 vectơ tiếp tuyến** |
| **Bézier** | 2 điểm cuối + 2 điểm khác điều khiển vectơ tiếp tuyến điểm cuối |
| **Splines** | 4 điểm (nhiều loại: B-spline đồng nhất, B-spline không đồng đều, β-spline) |

---

### 2.6.5. Đường cong HERMITE (slide 63–69)

**Định nghĩa:** đa thức bậc ba xác định bởi **vị trí và độ dốc tại mỗi điểm cuối**.
```
P(u)  = k3u³ + k2u² + k1u + k0
P'(u) = 3k3u² + 2k2u + k1
```

**Giải hệ số** (thay u tại hai đầu mút):
```
P0  = P(0)  = k0                       P0' = P'(0) = k1
P1  = P(1)  = k3 + k2 + k1 + k0        P1' = P'(1) = 3k3 + 2k2 + k1

⟹  k0 = P0
    k1 = P0'
    k2 = -3P0 - 2P0' + 3P1 - P1'
    k3 =  2P0 +  P0' - 2P1 + P1'
```

**Dạng ma trận:** `P(u) = uᵀ · M_H · q` (q là vectơ điều khiển, M_H là ma trận Hermite)

**Hàm trộn (blending functions) — slide 66:** 4 hàm cơ sở, mỗi hàm gắn trọng số cho một phần tử của vectơ kiểm soát. **Ở mỗi đầu chỉ có vị trí khác không** → đường cong **bắt buộc chạm vào điểm cuối**.

**Cách hiển thị (slide 67):**
1. Chọn kích thước bước phù hợp
2. Cắm giá trị vào ma trận hình học
3. Đánh giá P(u)
4. Lặp cho y & z độc lập
5. Vẽ đoạn thẳng nối điểm hiện tại với điểm trước

**Nối đường cong:**
- Điểm cuối trùng nhau → liên tục **C⁰**
- Căn chỉnh vectơ tiếp tuyến → liên tục **C¹**

**❌ Hạn chế (slide 69):**
- Khó đoán hành vi giữa 2 điểm khi độ dốc điểm cuối tùy ý
- Giới hạn bậc 3 → đường cong khá **cứng**
- **Khó mô hình hóa** — phải chỉ định cả điểm *và* độ dốc; trực quan hơn là chỉ xác định điểm

---

### 2.6.6. Đường cong BÉZIER ⭐ (slide 70–85)

**Xuất xứ:** P. Bézier (1910–1999, Paris) tại hãng ô tô Renault — "Hệ thống thiết kế các bề mặt điêu khắc của thân ô tô".

**Ý tưởng:** thay 2 vectơ tiếp tuyến của Hermite bằng **2 điểm kiểm soát bổ sung** → có thể suy ra từ ma trận Hermite. Hai điểm kiểm soát giữa xác định tiếp tuyến.

**Điều kiện xây dựng (cubic — slide 74):**
```
Nội suy:    p(0) = p0 = k0
            p(1) = p3 = k0 + k1 + k2 + k3
Đạo hàm:    p'(0) = 3(p1 - p0)      (p1 ở u = 1/3)
            p'(1) = 3(p3 - p2)      (p2 ở u = 2/3)
```

**Dạng ma trận:** `P(u) = uᵀ · M_B · P` (M_B là ma trận Bézier), n = 4, i = 0,1,2,3

**Công thức tổng quát theo bậc (slide 80):**
```
Bậc 1 (Linear):     P(u) = (1-u)P0 + uP1
Bậc 2 (Quadratic):  P(u) = (1-u)²P0 + 2u(1-u)P1 + u²P2
Bậc 3 (Cubic):      P(u) = (1-u)³P0 + 3u(1-u)²P1 + 3u²(1-u)P2 + u³P3
```
> **Quy tắc vàng:** *n + 1 điểm xác định đường cong bậc n.*

**Hàm trộn Bézier (slide 76):**
- Tổng các hàm bằng **1** tại bất kỳ điểm nào dọc đường cong
- **Điểm cuối có trọng số đầy đủ**
- Trọng số mỗi hàm rõ ràng, nhãn hiển thị điều khiển

**Đa thức Bernstein (slide 82–84):**
- Đặt theo tên **Sergei Natanovich Bernstein** (1880–1968, Odessa, Ukraine)
- Hàm trộn Bézier là **trường hợp đặc biệt** của đa thức Bernstein
- Hệ số nhị thức: `"n choose i" = n! / [i!(n-i)!]`
- Cách đánh giá **ổn định về mặt số học**: **công thức de Casteljau** — giảm tính toán do giai thừa gây ra
- **Ba tính chất:** tất cả bằng 0 tại 0 và 1 · với bất kỳ bậc nào tổng bằng 1 · tất cả nằm trong [0,1] bên trong khoảng (0,1)

**❌ Nhược điểm Bézier (slide 85):**
1. **Tốn chi phí** đánh giá đường cong tại nhiều điểm
2. Không có cách dễ dàng biết **mức lấy mẫu tốt**; tốc độ lấy mẫu có thể phải khác nhau dọc đường cong
3. **Một mảnh duy nhất, không có điều khiển cục bộ** — di chuyển một điểm kiểm soát làm **thay đổi toàn bộ** đường cong
4. Hình dạng phức tạp → **bậc rất cao**, khó đối phó

> **Bài tập (Exercise 0, slide 77):** P0=(2,2,0), P1=(2,3,0), P2=(3,3,0), P3=(3,2,0).
> Áp dụng công thức Bézier bậc 3:
> ```
> x(u) = -2u³ + 3u² + 2
> y(u) = -3u² + 3u + 2
> z(u) = 0
> ```
> | u | 0 | 1/4 | 1/2 | 3/4 | 1 |
> |---|---|---|---|---|---|
> | x | 2 | 2.15625 | 2.5 | 2.84375 | 3 |
> | y | 2 | 2.5625 | 2.75 | 2.5625 | 2 |
>
> Kiểm chứng: u=0 cho đúng P0, u=1 cho đúng P3 — đường cong **đi qua 2 điểm cuối** nhưng **không đi qua P1, P2**.

---

### 2.6.7. SPLINE và B-SPLINE (slide 86–94)

**Spline là gì (slide 86):** đường cong kết nối hai hoặc nhiều điểm cụ thể. Ta biểu diễn đường cong **độ dài tùy ý** bằng cách ghép một loạt đoạn cong lại — nhưng **cần kiểm soát cách chúng khớp với nhau**.

**Tổng quát hóa từ Bézier (slide 87):**
- Kiểm soát cục bộ tốt hơn
- **Bậc đường cong kết quả ĐỘC LẬP với số lượng điểm kiểm soát** ← khác biệt then chốt so với Bézier

**B-Splines (slide 88–89):**
- Gồm các đoạn đường cong có hệ số đa thức **chỉ phụ thuộc vào một vài điểm kiểm soát** → **kiểm soát cục bộ**
- Các phân đoạn nối tại các **knots** (nút)
- Đường cong **không nhất thiết đi qua** các điểm kiểm soát
- Hình dạng bị giới hạn trong **thân lồi (convex hull)** tạo bởi các điểm kiểm soát
- B-spline bậc 3 đồng nhất có liên tục **C²** — **cao hơn** Hermite hoặc Bézier

**Định nghĩa tổng quát (slide 91):**
```
B-spline bậc k là đường cong tham số gồm tổ hợp tuyến tính các B-spline cơ sở B(i,n)

P(u) = Σ Pi · B(i,n)(u)

Pi (i = 0,…,m):  các điểm kiểm soát
Knots: tj, j = 0,…, k+m

Định nghĩa đệ quy (Cox–de Boor):
B(i,1)(u) = 1  nếu  ti ≤ u < t(i+1) ,  ngược lại = 0
B(i,k)(u) = [(u - ti)/(t(i+k-1) - ti)]·B(i,k-1)(u)
          + [(t(i+k) - u)/(t(i+k) - t(i+1))]·B(i+1,k-1)(u)
```

**Tính chất (slide 92–93):**
- Số điểm kiểm soát **độc lập với bậc đa thức**
- **Kiểm soát cục bộ:** một điểm kiểm soát chỉ ảnh hưởng đến **k phân khúc**
- Liên tục **C²** + điều khiển cục bộ
- Không đi qua điểm kiểm soát (nhưng **có thể bị ép buộc** đi qua)
- **Đồng dạng (Uniform):** knots cách đều nhau trong t
- **Không đều (Non-uniform):** cho phép thêm điểm kiểm soát bổ sung ở bất cứ đâu trong bộ

**❌ Nhược điểm Cubic Splines (slide 94):**
1. **Bậc đường cong luôn không đổi** bất kể số điểm dữ liệu → muốn tăng linh hoạt phải thêm nhiều điểm, tạo nhiều phân đoạn spline vẫn ở bậc lập phương
2. Kiểm soát đường cong chỉ qua thay đổi vị trí điểm dữ liệu hoặc độ dốc cuối
3. Các đặc điểm **kiểm soát toàn cục không trực quan**

---

### 2.6.8. NURBS ⭐ (slide 95–99)

**NURBS = Non-Uniform Rational B-Splines** — khái quát hóa của Bézier.

| Chữ | Nghĩa | Ý nghĩa kỹ thuật |
|---|---|---|
| **NU** | Non-Uniform | Điểm kiểm soát **không cần trọng số bằng nhau** |
| **R** | Rational | Spline định nghĩa bởi **đa thức hợp lý** (tọa độ đồng nhất) |
| **BS** | B-Spline | Chuỗi spline Bézier bị xích với **mức độ có thể kiểm soát được** |

**Tính không đồng nhất mang lại gì (slide 97):**
- Số lượng tham số **t** dùng để mô hình hóa **từng đoạn khác nhau**
- Cho phép liên tục **C², C¹ hoặc C⁰** tại các điểm nối giữa các đoạn
- Cho phép **thêm điểm kiểm soát vào giữa đường cong**

**Lịch sử phát triển (slide 96):**
| Năm | Tổ chức | Hệ thống |
|---|---|---|
| 1979 | Boeing | Tiger System |
| 1981 | University of Utah | Alpha-1 |
| 1993 | SDRC | Geomod |
| — | Chuẩn công nghiệp | IGES, PHIGS, PDES, Pro/E |

→ **NURBS là tiêu chuẩn công nghiệp hiện tại.** Là yếu tố hình học bản địa trong **Maya**.

**✅ Ưu điểm NURBS (slide 99):**
1. Khái quát hóa chính xác **B-spline không đều** + đường cong/bề mặt Bézier phân thức hoặc không phân thức
2. Cung cấp **một dạng toán học chung** để biểu diễn hình giải tích và đường cong/bề mặt mềm chính xác (B-spline **chỉ có thể xấp xỉ** đường cong hình nón)
3. **Linh hoạt thiết kế** nhiều hình dạng lớn qua điểm kiểm soát và **trọng số** — tăng trọng số **kéo đường cong về phía điểm kiểm soát**
4. Bộ công cụ mạnh: chèn/tinh chỉnh/loại bỏ nút, nâng bậc, tách…
5. **Bất biến** theo tỷ lệ, xoay, dịch, dự đoán
6. **Nhanh và ổn định** về mặt tính toán
7. Trực quan trong thiết kế

> ⚠️ Lưu ý: NURBS **trơn mượt tự nhiên**, nhưng **đòi hỏi nỗ lực để làm cho KHÔNG trơn**.

### 📊 Bảng so sánh 4 họ đường cong (bảng ôn thi)

| Tiêu chí | Hermite | Bézier | B-Spline | NURBS |
|---|---|---|---|---|
| Xác định bởi | 2 điểm cuối + 2 tiếp tuyến | 2 điểm cuối + 2 điểm kiểm soát | Điểm kiểm soát + knots | Điểm kiểm soát + knots + **trọng số** |
| Đi qua điểm kiểm soát? | Có (2 đầu) | Chỉ 2 đầu | Không (có thể ép) | Không |
| Bậc phụ thuộc số điểm? | Cố định bậc 3 | **Có** (n+1 điểm → bậc n) | **Không** | **Không** |
| Kiểm soát cục bộ | Không | **Không** | **Có** (k phân khúc) | **Có** |
| Liên tục | C⁰ / C¹ | — | **C²** | **C⁰/C¹/C² tùy chọn** |
| Điểm yếu chính | Khó mô hình, phải nhập độ dốc | Không kiểm soát cục bộ, bậc cao | Bậc cố định, kiểm soát toàn cục không trực quan | — |
| Ứng dụng | — | Thiết kế thân ô tô (Renault) | Mô hình hóa mặt cong | **Chuẩn công nghiệp CAD/Maya** |

---

## 2.7. Giải thuật đường quét sinh đa giác (slide 100–105)

**Bối cảnh:** tồn tại rất nhiều giải thuật; mỗi giải thuật phục vụ **một loại đa giác nhất định** — có loại chỉ dùng cho tam giác, có loại đòi hỏi đa giác **lồi, không tự cắt, không có lỗ hổng**.

**Polygon scan conversion** là giải thuật **chung kinh điển**.

**Thủ tục chung (slide 104):** ⭐
1. **Xác định giao** của đường thẳng quét với cạnh đa giác
2. **Sắp xếp các giao điểm** theo mức độ **tăng dần của x**
3. **Điền các điểm ảnh** vào giữa **cặp** các điểm x

**Điều kiện ràng buộc (slide 103):** dùng giải thuật trung điểm xác định điểm biên theo thứ tự tăng của x. Các điểm biên phải:
- **Không bị chia sẻ** bởi các đa giác lân cận
- Các đa giác chỉ toàn điểm cạnh (điểm biên)
- → Đảm bảo các đa giác **chia sẻ điểm biên mà không chia sẻ điểm ảnh bên trong**

**Các quy ước xử lý biên (slide 105):**
- Cạnh **nằm ngang (horizontal edge)** → **loại bỏ**
- **y_max không được bao gồm**
- Giá trị x nguyên nằm ở **bên phải = ngoài (exterior)**
- Làm tròn **xuống** cho A, làm tròn **lên** cho B

---

## 2.8. Mô hình hóa ký tự (slide 106–109)

**Cơ sở:** mỗi ký tự với một font cho trước được định nghĩa là một **bitmap chữ nhật nhỏ**.

**Font/typeface = tập các hình dạng ký tự:**
- **Fontcache:** các ký tự theo chuỗi liên tiếp nhau trong bộ nhớ
- **Dạng cơ bản:** thường (N), nghiêng (I), đậm (B), nghiêng đậm (B+I)
- **Thuộc tính:** màu sắc, kích thước, khoảng cách (spacing), hướng (orientation)

**Cấu trúc dữ liệu (slide 108):**
```c
typedef struct {
    int leftx;
    int width;
} CharLocation;              // Vị trí

typedef struct {
    CacheId;
    Height;                  // Độ rộng chữ
    CharSpace;               // Khoảng cách giữa các ký tự
    CharLocation Table[128];
} fontcache;
```

**Ký tự vector (slide 109):** định nghĩa ký tự bởi **đường cong mềm bao ngoài**.
- ✅ **Chất lượng cao**
- ❌ **Tốn kém nhất về mặt tính toán**

---

## 2.9. MÔ HÌNH HÓA KHỐI ĐẶC (Solid Modeling) ⭐ (slide 110–127)

**Định nghĩa (slide 111):** mô hình hóa hình đặc (mô hình hóa 3D) các thực thể vật lý là phương pháp biểu diễn dựa trên mô tả các thực thể dưới dạng **mô hình toán học, ký hiệu, sơ đồ**.

### 2 phương pháp nền tảng phân loại ⭐

| | **Boundary Representations (B-reps)** | **Space Partitioning** |
|---|---|---|
| Mô tả đối tượng như | Tập các **bề mặt phân cách** hình đặc với môi trường | Tập các **khối nhỏ đơn vị** liên tiếp **không chồng nhau** |
| Ví dụ | Polygonal Representations | Voxel, Quadtree/Octree |

*(Ví dụ khác được nêu: Constructive Solid Geometry — CSG)*

### 2.9.1. Sơ đồ biểu diễn & thuộc tính (slide 112–113)

**Representation Schemes:** cách thức biểu diễn phương pháp mô hình hóa đối tượng thông qua các thành phần, nhằm đảm bảo **độ chính xác rõ ràng** và **tính duy nhất** của kết quả.

*Ví dụ với đa giác:* (1) mỗi đa giác cấu tạo bởi tập đỉnh theo thứ tự tùy ý → (2) mỗi đỉnh là cặp 2 số thực tọa độ → (3) danh sách chứa tất cả các cặp: `(x1,y1); (x2,y2); … (xn,yn)`

**Các thuộc tính bắt buộc của sơ đồ biểu diễn:** ⭐

| Thuộc tính | Nội dung |
|---|---|
| **Miền (Domain)** | Mức độ phủ hình học — chỉ ra **loại đối tượng nào** biểu diễn được |
| **Tính hợp lệ (Validity)** | Biểu diễn có tương ứng với đối tượng trong miền không? Dữ liệu sai có thể **treo hệ thống** |
| **Tính trong sáng (Non-ambiguity)** | Gồm 2 vế: **Đầy đủ (Completeness)** — tạo ra đối tượng hoàn chỉnh, thực hiện được mọi phép toán; **Duy nhất (Uniqueness)** — mỗi biểu diễn hợp lệ ứng với **1 đối tượng duy nhất** |
| *Thuộc tính khác* | Xúc tích (Conciseness), Dễ xây dựng (Ease of construction), Khả hợp ứng dụng (Suitability) |

### 2.9.2. Nửa không gian & r-set (slide 114)

- **Half Space (nửa không gian):** phần không gian **vô hạn** được giới hạn bởi **1 bề mặt**: `{p : f(p) ≤ 0}` — ví dụ `ax + by + cz + d ≤ 0`
- **Semi-algebraic set:** kết quả của các **phép toán tập hợp** trên các nửa không gian
- **r-sets:** các tập bán đại số **chính quy, bao đóng**. Phải đảm bảo: **cứng (rigid), hữu hạn (finite), đặc (solid), thỏa mãn phép toán Boolean**, và **độc nhất dưới đường bao** → phương pháp mô hình hóa **đối tượng cứng đồng nhất**

### 2.9.3. B-Rep — Biểu diễn biên (slide 115–117)

**Nguyên lý:** biểu diễn đối tượng vật lý thông qua **không gian được bao bởi các mặt kín có hướng**. Mô hình biểu diễn bằng **đồ thị** mô tả cấu trúc liên kết, các **nút là mặt, cạnh, đỉnh**; **mũi tên giữa các nút là thông tin kết nối**.

**Hai loại thông tin cần phân biệt rõ:** ⭐
- **Topology (cấu trúc liên kết):** thông tin về **cách kết nối**
- **Geometry (hình học):** thông tin **đo được** (metric information) — ví dụ tọa độ đỉnh

**Điều kiện của MẶT (Face):**
1. Là tập con của biên tôpô hình đặc
2. Tập kết hợp tất cả các mặt tạo thành **biên** cho hình đặc
3. Mỗi mặt chỉ là tập con **duy nhất** của 1 cá thể bề mặt
4. Phải có **tính đồng nhất 2D**, không có cạnh hay đỉnh rời
5. Các mặt phải được **kết nối**
6. Chỉ bị **tách ra tại các cạnh hay đỉnh nút**

**Điều kiện của CẠNH (Edge):**
1. Là tập con biên của mặt
2. Tập tất cả cạnh liên quan đến mặt tạo thành **biên của mặt**
3. Mỗi cạnh là một phần của **phần giao giữa hai mặt cong**
4. Mỗi cạnh là một **1-manifold (đa tạp) kết nối**
5. Các cạnh **rời nhau**, chỉ **tiếp xúc tại các đỉnh**
6. Là **tập lớn nhất** của đường biên các mặt thỏa mãn đủ các điều kiện trên

### 2.9.4. CSG — Constructive Solid Geometry ⭐ (slide 118–125)

**Nguyên lý:** xây dựng trên **tập nhỏ các tham biến do người dùng định nghĩa** để xác định mô hình hình học, **vị trí và hướng** tạo nên các đối tượng cơ sở.

**6 thực thể cơ sở:** hình hộp (block) · hình cầu (sphere) · hình trụ (cylinder) · hình nón (cone) · hình nhẫn (torus) · hình nêm (wedge)

**3 phép toán Boolean cơ bản:** ⭐

| Phép toán | Ký hiệu | Định nghĩa | Tính chất |
|---|---|---|---|
| **Union (Hợp)** | `X = A + B` | Phần không gian hợp thành từ 2 đối tượng | **Giao hoán:** A + B = B + A |
| **Intersection (Giao)** | `X = A \| B` | Phần không gian hình thành từ phép AND giữa 2 đối tượng | **Giao hoán:** A \| B = B \| A |
| **Difference (Trừ)** | `X = A - B` | Mọi điểm của X chứa trong A mà **không chứa** trong B | **KHÔNG giao hoán:** A - B ≠ B - A |

**Cây cấu trúc CSG (slide 123–124):**
- **Cấu trúc cây nhị phân** được dùng như **ngôn ngữ** biểu diễn hình học cấu trúc đặc
- Mỗi **cây con tại các nút** biểu diễn hình đặc là **kết quả từ các phép toán Boolean và các phép biến đổi hình học lớp dưới**

**Mở rộng CSG (slide 125):** hai kỹ thuật tạo thực thể hình học rắn cơ sở làm dữ liệu đầu vào cho cây CSG:
- **Lathing** — phép quay
- **Extrusion** — phép tịnh tiến (sweep)

### 2.9.5. Voxel (slide 126–127)

- Thuộc **phương pháp liệt kê không gian (Space Partitioning)**
- Cấu trúc dữ liệu: **Quadtree** — vùng *partially occupied* (bị chiếm một phần) sẽ **yêu cầu phân rã tiếp** (further decomposition)

### 📊 So sánh CSG vs Voxel (slide 127) ⭐

| | **CSG** | **Voxel (Volumetric model)** |
|---|---|---|
| Bản chất | Đối tượng 3D định nghĩa như các **phép toán Boolean trên khối nguyên thủy** | Đối tượng 3D là **tập hợp các phần tử thể tích (voxel)** |
| Cách mô tả không gian | Qua cây thao tác | **Đánh dấu chiếm chỗ không gian trong vùng rời rạc** |

---

## PHẦN 3. TẠO BÓNG VÀ ÁNH SÁNG (slide 129–195)

## 3.1. Rendering Pipeline ⭐ (slide 128)

```
Model & Camera Parameters
        ↓
  ┌─────────────┐
  │  Transform  │
  │  Illuminate │
  │  Transform  │
  │    Clip     │
  │   Project   │
  │  Rasterize  │
  └─────────────┘
        ↓
   Framebuffer  →  Display
```

## 3.2. Rendering là gì (slide 130–133)

**Định nghĩa:** Kết xuất 3D là quá trình tự động chuyển đổi **mô hình khung dây 3D** thành **hình ảnh 2D** với hiệu ứng quang học 3D hoặc kết xuất không quang học.

`Đối tượng mô hình → Hình ảnh → Kết xuất`

**Mô hình đối tượng cần mô tả 3 thứ:**
1. Dạng **hình học** của các đối tượng
2. **Mối quan hệ của các đối tượng với nhau**
3. **Mối quan hệ của các đối tượng với máy ảnh (camera)**

**Hai hướng kết xuất:**

| | **Thời gian thực** | **Không theo thời gian thực** |
|---|---|---|
| Ứng dụng | Trò chơi, **mô phỏng**, soạn thảo, tiếp thị | Phim, cảnh tĩnh, hoạt hình, kết xuất ảnh thực tế |

> Trong cả hai trường hợp, **mục tiêu cuối cùng như nhau**: biến biểu diễn toán học của cảnh 3D thành một cái gì đó đẹp. (Digital Twin thuộc nhóm **mô phỏng thời gian thực**.)

## 3.3. Illumination & Shading — phân biệt khái niệm ⭐ (slide 134–135, 148)

| Khái niệm | Định nghĩa |
|---|---|
| **Texture (Kết cấu)** | Nhóm kỹ thuật (**texture mapping, bump mapping, reflection mapping**…) dùng để **thêm hiệu ứng vào bề mặt** đối tượng |
| **Illumination model (Mô hình chiếu sáng)** | **Xác định màu của điểm bề mặt** bằng cách mô phỏng một số thuộc tính ánh sáng |
| **Shading model (Mô hình tô bóng)** | **Áp dụng** mô hình chiếu sáng **tại một tập hợp các điểm** và tô màu **toàn bộ** hình ảnh |

> Câu chốt: *"Shading là việc **thực hiện** mô hình chiếu sáng tại các điểm pixel hoặc bề mặt đa giác của các đối tượng đồ họa."*
>
> Và: *"Chỉ cần gán một màu cho một bề mặt sẽ mang lại vẻ ngoài **không thực tế**. Sự tương tác của ánh sáng với vật thể là **cơ chế chính** để đạt được hiện thực."*

## 3.4. Nguồn sáng (slide 138–140)

**4 loại nguồn sáng trong mô hình 3D:**
1. Nguồn sáng **điểm** (point)
2. Nguồn sáng **xung quanh** (ambient)
3. Nguồn sáng **định hướng** (directional)
4. Nguồn sáng **chiếu** (spot)

> Chọn **đúng loại và số lượng** nguồn sáng là một phần quan trọng của đầu ra cảnh tốt.

**Sự xuất hiện của một đối tượng phụ thuộc vào (slide 140):** ⭐
1. **Loại** nguồn sáng
2. **Khoảng cách** đến nguồn sáng
3. **Hướng** của vật thể so với nguồn sáng
4. **Thuộc tính bề mặt** của đối tượng (xác định bản chất tương tác ánh sáng)

→ Phải lặp lại cho **tất cả** các nguồn sáng ⇒ **rất nhiều chế biến (tốn tính toán)**.

## 3.5. Từ ánh sáng đến bề mặt (slide 141–142)

**4 khả năng của bề mặt:** Absorb (hấp thụ) · Reflect (phản xạ) · Refract (khúc xạ) · Emit (phát sáng)

**3 thành phần phản xạ cốt lõi:** ⭐⭐

| Thành phần | Bản chất |
|---|---|
| **Diffuse (Khuếch tán)** | Phản xạ từ ánh sáng tới với **cường độ bằng nhau theo mọi hướng**. Phụ thuộc tính chất bề mặt |
| **Ambient (Ánh sáng môi trường)** | Bề mặt tiếp xúc với **ánh sáng gián tiếp phản xạ từ các vật thể gần đó** |
| **Specular (Ánh sáng gương)** | Gần **tổng số ánh sáng tới xung quanh góc phản xạ** |

## 3.6. "Standard" Lighting Model ⭐⭐ (slide 143–145)

**Ba thành phần kết hợp TUYẾN TÍNH:**
```
Khuếch tán:  I_d    = I_L · cos(θ)
             I_diff = K_d · I_L · (N · L)

Môi trường:  I_amb  = K_a · I_a

Gương:       Thành phần cụ thể cho lượng ánh sáng phản chiếu kiểu gương
             → phụ thuộc VỊ TRÍ NGƯỜI QUAN SÁT
```
Trong đó: `I_L` = cường độ ánh sáng · `N` = vectơ pháp tuyến · `L` = vectơ hướng tới nguồn sáng · `K_d`, `K_a` = hệ số phản xạ.

**Quy tắc với NHIỀU nguồn sáng (slide 145):** ⭐
1. **Lặp lại** tính toán khuếch tán và đặc trưng (specular) **cho từng nguồn sáng**
2. **Cộng** các thành phần từ tất cả nguồn sáng
3. **Thuật ngữ môi trường (ambient) chỉ đóng góp MỘT LẦN** ← rất hay bị nhầm

**Hệ số phản xạ theo vật liệu:**
- Kim loại ánh kim đơn giản: `ks` và `kd` **chia sẻ màu vật liệu**
- Nhựa đơn giản: `ks` là **màu trắng**

## 3.7. Light Interaction (slide 149)

**Tương tác ánh sáng bao gồm:** Illumination · Reflection · Shading · Shadows · Texture of surfaces · Transparency/Translucency

**Hai nhóm thành phần:**

| **Light Source Properties** | **Object Properties** |
|---|---|
| Color (bước sóng ánh sáng) | Material |
| Shape | Geometry |
| Direction | Absorption |

## 3.8. HAI HƯỚNG TIẾP CẬN RENDER ⭐⭐ (slide 150)

| | **Bắt đầu từ HÌNH HỌC → Rasterization** | **Bắt đầu từ PIXEL → Ray Casting** |
|---|---|---|
| Ý tưởng | Tính hình chiếu của **mỗi tam giác** lên không gian ảnh 2D | Với **mỗi pixel** trong ảnh cuối cùng |
| Câu hỏi trả lời | Nó có nhìn thấy? Nhìn thấy phần nào? Màu gì? | Đối tượng nào nhìn thấy ở pixel này? Nó màu gì? |

## 3.9. Bốn thuật toán kết xuất & độ phức tạp ⭐⭐

### (1) Rasterization (slide 152)
- Hình học chia thành **tam giác**; mỗi pixel trên màn hình kiểm tra xem **tâm của nó có nằm trong hình học không**
- Pixel được ánh xạ sẽ được tô màu bằng **scan conversion**
- **Độ phức tạp: O(N)** — N = số tam giác
- ✅ **Cực nhanh** so với ray casting/tracing
- ❌ Chỉ **ánh xạ pixel ra**, **không tính** cách tô màu chúng hoặc vẽ chúng trên màn hình

### (2) Ray Casting (slide 155–158)
- **Ý tưởng:** theo dõi các tia **từ mắt**, mỗi tia qua một pixel, tìm **vật thể gần nhất chặn đường đi** của tia
- Đây là **truy dấu vết ngược**: thay vì truy tia từ nguồn sáng, ta truy tia **từ người xem trở lại nguồn sáng**
- **Độ phức tạp: O(W × N)** — W = chiều rộng màn hình, N = số đối tượng

**Các bước:**
1. Gửi các tia **theo chiều ngang** từ điểm quan sát vào cảnh
2. Tính **khoảng cách** đến chướng ngại vật
3. Vẽ **đường trung tâm theo chiều dọc** trên màn hình và **thu nhỏ theo khoảng cách**
4. Đối tượng chuyển động là các **sprite phẳng**, luôn phải đối mặt với máy ảnh

**Đặc điểm:** rất nhanh vì chỉ phải tính **một phép tính cho mỗi đường thẳng đứng** của màn hình · Đơn giản thực hiện · Chỉ những tia tới mắt · Cần **ít nhất một tia cho mỗi pixel** · Dễ thực hiện ánh sáng thực tế, bóng tối, phản xạ, khúc xạ, chiếu sáng gián tiếp.

> *Bối cảnh lịch sử: khi máy tính chạy chậm hơn, không thể chạy engine 3D thực trong thời gian thực, ray casting là **giải pháp đầu tiên**.*

### (3) Ray Tracing (slide 159–160)
**Ray tracing = Ray casting + khả năng SINH RA CÁC TIA MỚI khi tia ban đầu chạm vật.**

**3 loại tia phái sinh:**

| Loại tia | Dùng cho hình học |
|---|---|
| **Refraction rays** (khúc xạ) | Glass geometry (thủy tinh) |
| **Reflection rays** (phản xạ) | Metallic geometry (kim loại) |
| **Shadow rays** (bóng) | Opaque geometry (vật đục) |

**Quy trình:** gửi tia từ mắt qua màn hình vào cảnh → khi va chạm chướng ngại vật, gửi tia bổ sung tùy loại hình học bị chạm → **lấy trung bình các tia** = màu của pixel đó.

- **Độ phức tạp: O(W × H × N)** — W rộng, H cao, N số đối tượng
- ✅ Xử lý được **hầu hết các khía cạnh kết xuất trong MỘT thuật toán** (đổ bóng/chiếu sáng…)

### (4) Radiosity (slide 161–162)
- Thuật toán kết xuất cho phép hiển thị **thực tế** của **bóng và ánh sáng khuếch tán**
- **Nguyên lý:** một tia sáng chiếu vào bề mặt được **phản xạ bởi nhiều tia khuếch tán**, chúng có thể **tự chiếu sáng các bề mặt khác**
- Làm cho **tất cả các bề mặt** phản xạ ánh sáng, và nguồn sáng tự nó sáng nơi thực hiện đổ bóng và pha trộn
- Thuộc nhóm **Chiếu sáng toàn cảnh (Global illumination)**
- **Độ phức tạp: O(N²)** — N = số **bề mặt con (subsurfaces)**

### 📊 Bảng độ phức tạp (nhớ kỹ để thi)

| Thuật toán | Độ phức tạp | Biến số |
|---|---|---|
| **Rasterization** | **O(N)** | N = số tam giác |
| **Ray Casting** | **O(W·N)** | W = rộng màn hình, N = số đối tượng |
| **Ray Tracing** | **O(W·H·N)** | W rộng, H cao, N số đối tượng |
| **Radiosity** | **O(N²)** | N = số bề mặt con |

## 3.10. Local vs Global Illumination ⭐⭐ (slide 165–168)

**Bối cảnh:** Tạo bóng **đúng** yêu cầu tính toán **toàn cục** liên quan đến **tất cả** đối tượng và nguồn sáng → **không tương thích** với mô hình đường ống làm mờ từng đa giác một cách độc lập (kết xuất cục bộ).

> Tuy nhiên trong đồ họa máy tính, **đặc biệt là đồ họa thời gian thực**, ta "rất vui nếu mọi thứ chỉ đơn giản là **trông đúng**" → nhiều kỹ thuật **gần đúng** hiệu ứng toàn cục. **← Đây chính là triết lý áp dụng cho Digital Twin thời gian thực.**

| | **Local Illumination** | **Global Illumination** |
|---|---|---|
| Xét đến | Ánh sáng, **vị trí người quan sát**, thuộc tính vật liệu của vật thể | **Tương tác của ánh sáng từ TẤT CẢ các bề mặt** trong cảnh |
| Đặc điểm | "Everything floats in dark space" — chỉ xét **một lần phản xạ** ánh sáng | Ánh sáng phản xạ khỏi vật → chiếu lên vật khác → **ánh sáng gián tiếp (indirect light)** |
| Kỹ thuật | **Ray Casting**, **Polygon shading** | **Ray Tracing**, **Radiosity**, **Monte Carlo** |
| Chi phí | Thấp | **Rất tốn kém — chưa hoàn thiện** |

## 3.11. SHADING — Tô bóng ⭐⭐ (slide 169–191)

### 3.11.1. Tại sao cần shading? (slide 171–172)

Nếu xây một hình cầu bằng nhiều đa giác và tô bằng `glColor` → được một quả bóng **phẳng, giả**. Cái ta cần là bề mặt cong mượt.

**Nguyên nhân vật lý:** tương tác vật liệu–ánh sáng làm **mỗi điểm có màu sắc hoặc sắc thái khác nhau**. Phải xét: **nguồn sáng · tính chất vật liệu · vị trí người xem · định hướng bề mặt**.

**Một mô hình chiếu sáng thường xem xét 4 nhóm yếu tố (slide 170):**
1. Thuộc tính **ánh sáng** (cường độ, màu, vị trí, hướng, hình dạng)
2. Thuộc tính **bề mặt đối tượng** (màu, độ phản xạ, độ trong suốt…)
3. **Tương tác giữa đèn và vật thể** (hướng đối tượng)
4. **Tương tác giữa vật thể và mắt** (hướng nhìn)

### 3.11.2. Ba mô hình shading cho đa giác (slide 173)

| Mô hình | Cách tính |
|---|---|
| **Shading liên tục (bóng phẳng)** | Tính chiếu sáng tại **bất kỳ điểm nào** trên bề mặt; dùng pháp tuyến mặt hoặc từ một cặp cạnh. Tốt cho **ánh sáng ở xa và người xem ở xa**, hoặc nếu các mặt gần đúng bề mặt |
| **Shading mỗi pixel** | Tính chiếu sáng tại **mọi điểm** trên bề mặt |
| **Shading nội suy** | Tính độ rọi **ở các đỉnh** rồi **nội suy màu** |

---

### 3.11.3. FLAT SHADING (slide 175–178)

**Định nghĩa:** áp dụng **một màu pixel trên toàn bộ đa giác**.

**Thuật toán 4 bước:**
1. Tính **vectơ chuẩn (normal) của bề mặt**
2. Tính **góc** giữa vectơ chuẩn này và vectơ ánh sáng
3. Dùng **tích vô hướng** → cho ta **cosin của góc** giữa 2 vectơ
4. Dùng giá trị này để áp dụng lượng ánh sáng cho mặt, dựa trên màu hiện tại

```
final color = color × Math.Max(0, cos(angle))
```

**✅ Ưu điểm:** rất nhanh và hiệu quả; thường dùng cho **kết xuất tốc độ cao** nơi kỹ thuật cao cấp quá tốn kém.

**❌ Nhược điểm:** chủ nghĩa hiện thực bị hạn chế, đặc biệt với **số lượng đa giác thấp**; **các đa giác riêng biệt nhìn thấy rõ ràng**.

**🔴 Mach Banding (slide 178) — lỗi kinh điển:**
- Tạo bóng cho từng khía cạnh đa giác riêng lẻ **sẽ không tạo ra ảo ảnh bề mặt cong trơn tru**
- **Lý do:** đa giác sẽ có **màu khác nhau dọc theo ranh giới**; không may là **nhận thức của con người còn LÀM NỔI BẬT sự gián đoạn** đó → **hiệu ứng dải Mach**

---

### 3.11.4. GOURAUD SHADING (slide 179–186)

**Định nghĩa:** đặt theo tên **Henri Gouraud** — phương pháp **nội suy** để tạo bóng liên tục cho bề mặt biểu thị bằng lưới đa giác.

**Điều kiện tiên quyết:** cần có **pháp tuyến trên mỗi ĐỈNH**.

**Cơ chế (slide 182–183):**
1. Dùng **3 normals — 1 trên mỗi đỉnh** của tam giác
2. Tính **cường độ tại đỉnh** trước
3. **Nội suy** màu của từng pixel từ 3 cấp độ màu tại đỉnh
4. Kết quả: **độ sáng liên tục** trên các tam giác

**Công thức nội suy:**
```
Nếu biết v1 và v2, điểm x cách chúng khoảng a và b:
x = b/(a+b) · v1  +  a/(a+b) · v2
```

**✅ Ưu điểm:**
- **Nhanh**, được hỗ trợ bởi **hầu hết các bộ tăng tốc đồ họa (phần cứng)**
- Đạt ánh sáng mịn trên bề mặt **đa giác thấp (low-poly)** mà không cần tính toán nặng
- Che giấu cạnh tốt hơn Flat — quan trọng vì **mắt người đặc biệt giỏi chú ý các cạnh**, nên *"bóng phẳng là hành động chống lại sinh học của chúng ta"*
- Cung cấp **dải màu tuyến tính** trên đa giác

**❌ Hạn chế (slide 185–186):** ⭐
1. **Không xử lý đúng các điểm nổi bật đặc biệt (specular highlights)**, đặc biệt khi tham số n lớn (vùng tô sáng nhỏ)
2. **Lý do:** màu sắc được **nội suy** → cường độ **tối thiểu và tối đa LUÔN xảy ra ở các ĐỈNH**
3. Khi highlight rơi vào **gần tâm một tam giác lớn**, nó thường bị **bỏ qua hoàn toàn**
4. Không có phản xạ specular đúng; vẫn còn **hiệu ứng băng tần (banding)**
5. *Khắc phục tạm:* dùng **tam giác nhỏ hơn**

---

### 3.11.5. PHONG SHADING (slide 187–191)

**Khác biệt cốt lõi so với Gouraud:** ⭐⭐
> Gouraud **nội suy MÀU** — Phong **nội suy VECTƠ PHÁP TUYẾN**.

**Thuật toán 3 bước:**
1. **Tính trung bình pháp tuyến ở các đỉnh**
2. **Nội suy các pháp tuyến** dọc theo các cạnh và đường quét (theo từng thành phần)
3. **Tính toán chiếu sáng trên MỖI PIXEL**

**Phong Reflection Model (slide 189):** ước tính màu của các điểm ảnh; ánh sáng được mô tả là **sự kết hợp của 3 thành phần**:
```
Ambient light  +  Diffuse light  +  Specular Light
```

**✅ Ưu điểm (slide 191):**
- Các **điểm nổi bật nhất (highlights)** nổi bật **chính xác hơn nhiều, sắc nét hơn nhiều**, và **có thể xảy ra BÊN TRONG một đa giác** (điều Gouraud không làm được)
- **Mach banding giảm đáng kể**

**❌ Chi phí:** **gia tăng đáng kể thời gian xử lý** vì mô hình phản chiếu được áp dụng **cho mỗi pixel**. Chậm, **không được hỗ trợ rộng rãi bởi phần cứng**.

### 📊 So sánh 3 mô hình Shading (bảng ôn thi số 1)

| Tiêu chí | **Flat** | **Gouraud** | **Phong** |
|---|---|---|---|
| Đơn vị tính chiếu sáng | **1 màu / cả đa giác** | Tại **đỉnh**, rồi nội suy | Tại **mỗi pixel** |
| Nội suy cái gì | Không nội suy | **MÀU** | **PHÁP TUYẾN** |
| Pháp tuyến cần | Pháp tuyến **mặt** | Pháp tuyến **đỉnh** | Pháp tuyến **đỉnh** (rồi nội suy) |
| Tốc độ | **Nhanh nhất** | Nhanh | **Chậm nhất** |
| Hỗ trợ phần cứng | Có | **Hầu hết accelerator** | **Không rộng rãi** |
| Mach banding | **Rõ rệt** | Còn banding | **Giảm đáng kể** |
| Specular highlight | Không | **Sai / bị bỏ qua giữa tam giác** | **Chính xác, sắc nét** |
| Nhìn thấy cạnh đa giác | **Rõ** | Bị che | Bị che |

## 3.12. OpenGL (slide 192)

OpenGL định nghĩa **hai mô hình đổ bóng cụ thể** — kiểm soát cách màu sắc được gán cho pixel:

| Lệnh | Ý nghĩa |
|---|---|
| `glShadeModel(GL_SMOOTH)` | **Nội suy** giữa các màu tại các đỉnh (**mặc định**) |
| `glShadeModel(GL_FLAT)` | Sử dụng **màu không đổi** trên đa giác |

**Ví dụ thiết lập ánh sáng:**
```c
GLfloat white[] = {1., 1., 1., 1.};
GLfloat light0_position[] = {1., 1., 5., 0.};   /* directional light (w=0) */

glLightfv(GL_LIGHT0, GL_POSITION, light0_position);
glLightfv(GL_LIGHT0, GL_DIFFUSE,  white);
glLightfv(GL_LIGHT0, GL_SPECULAR, white);
glEnable(GL_LIGHT0);
glEnable(GL_NORMALIZE);                          /* normalize normal vectors */
glLightModeli(GL_LIGHT_MODEL_TWO_SIDE, GL_TRUE); /* two-sided lighting */
glEnable(GL_LIGHTING);
```
> Lưu ý: `w = 0` trong `light0_position` báo hiệu **nguồn sáng định hướng (directional)**.

## 3.13. Summary của bài (slide 194)

- **Đổ bóng và bóng quan trọng đối với định nghĩa 3D**
- Các mô hình khác nhau tồn tại để quản lý bóng: **Flat · Gouraud · Phong**
- **Bóng tối quan trọng đối với chủ nghĩa hiện thực**
  - Chủ yếu thực hiện bằng **shadow mapping (ánh xạ bóng)**
  - Chỉ là một **xấp xỉ** của tắc ánh sáng (occlusion)
  - **Ảnh hưởng tới các mô hình CV (Computer Vision) trong Digital Twin** ← câu kết nối lại chủ đề môn học

---

# PHỤ LỤC A. BỘ CÔNG THỨC CỐT LÕI CẦN THUỘC

```
── ĐOẠN THẲNG ──────────────────────────────────────
Tường minh:    y = mx + b,  m = Δy/Δx,  b = y1 - m·x1
Không tường minh: rx + sy + t = 0
               r = (y2-y1), s = -(x2-x1), t = x2y1 - x1y2
Tham số:       P(u) = (1-u)P1 + u·P2,   u ∈ [0,1]

── DDA ─────────────────────────────────────────────
xi = x(i-1) + 1 ;  yi = y(i-1) + m
Chi phí: 4 FP + 2 int mỗi bước

── BRESENHAM ───────────────────────────────────────
P1  = -2Δy + Δx
Pi ≤ 0 → y+1, Pi+1 = Pi - 2Δy + 2Δx
Pi > 0 → y giữ, Pi+1 = Pi - 2Δy

── TRUNG ĐIỂM (đường thẳng) ────────────────────────
d = 2Δy - Δx
d ≤ 0 → d += 2Δy         (chọn B, chỉ x+1)
d > 0 → d += 2(Δy-Δx)    (chọn A, x+1 và y+1)

── TRUNG ĐIỂM (đường tròn) ─────────────────────────
d = 1 - r ; x = 0 ; y = r ; while y < x
d < 0 → d += 2x + 3       (chọn A, x+1)
d ≥ 0 → d += 2(x-y) + 5   (chọn B, x+1, y-1)
Ê-líp: b²x² + a²y² - a²b² = 0

── HERMITE ─────────────────────────────────────────
P(u) = k3u³ + k2u² + k1u + k0
k0 = P0 ; k1 = P0'
k2 = -3P0 - 2P0' + 3P1 - P1'
k3 =  2P0 +  P0' - 2P1 + P1'
P(u) = uᵀ · M_H · q

── BÉZIER ──────────────────────────────────────────
Bậc 1: (1-u)P0 + uP1
Bậc 2: (1-u)²P0 + 2u(1-u)P1 + u²P2
Bậc 3: (1-u)³P0 + 3u(1-u)²P1 + 3u²(1-u)P2 + u³P3
p'(0) = 3(p1-p0) ; p'(1) = 3(p3-p2)
n+1 điểm → bậc n ;  Bernstein: C(n,i) = n!/[i!(n-i)!]

── B-SPLINE ────────────────────────────────────────
P(u) = Σ Pi · B(i,n)(u) ,  knots tj (j = 0..k+m)
B(i,1) = 1 nếu ti ≤ u < t(i+1), ngược lại 0
B(i,k) = (u-ti)/(t(i+k-1)-ti) · B(i,k-1)
       + (t(i+k)-u)/(t(i+k)-t(i+1)) · B(i+1,k-1)

── CHIẾU SÁNG ──────────────────────────────────────
I_d    = I_L · cos(θ)
I_diff = K_d · I_L · (N · L)
I_amb  = K_a · I_a
Tổng = Ambient (1 lần) + Σ[Diffuse + Specular] mọi nguồn

── SHADING ─────────────────────────────────────────
Flat:    final color = color × max(0, cos(angle))
Gouraud: x = b/(a+b)·v1 + a/(a+b)·v2

── ĐỘ PHỨC TẠP ─────────────────────────────────────
Rasterization O(N) < Ray Casting O(W·N)
             < Ray Tracing O(W·H·N) < Radiosity O(N²)
```

---

# PHỤ LỤC B. CHECKLIST ÔN TẬP

**Chương 1**
- [ ] Kể đủ 4 vai trò của đồ họa máy tính trong Digital Twin
- [ ] Phân biệt Raster vs Vector (bản chất, thao tác, ưu thế)
- [ ] Vẽ được sơ đồ Modeling → Rendering → Displaying + engine tương ứng
- [ ] Định nghĩa fragment = pixel + associated data

**Chương 2**
- [ ] Viết 3 dạng biểu diễn đoạn thẳng và chuyển đổi giữa chúng
- [ ] Chạy tay DDA / Bresenham / Trung điểm trên một ví dụ số cụ thể
- [ ] Giải thích tại sao Bresenham nhanh hơn DDA
- [ ] Nêu 4 ràng buộc của thuật toán vẽ đoạn thẳng
- [ ] Chạy tay Midpoint circle; giải thích tại sao chỉ cần 1/8 đường tròn
- [ ] Phân biệt Interpolation vs Approximation
- [ ] Trả lời "Tại sao dùng đa thức?" và "Tại sao bậc 3?"
- [ ] Suy được 4 hệ số Hermite từ điều kiện biên
- [ ] Viết công thức Bézier bậc 1/2/3 và tính điểm tại u cho trước
- [ ] Nêu 4 nhược điểm của Bézier → dẫn tới B-Spline
- [ ] Giải thích "kiểm soát cục bộ" và tính liên tục C²
- [ ] Giải mã 3 chữ N-U-R-BS và nêu ưu điểm NURBS
- [ ] Nêu 3 bước của polygon scan conversion + quy ước xử lý biên
- [ ] Phân biệt B-rep vs Space Partitioning; Topology vs Geometry
- [ ] Nêu 4 thuộc tính của sơ đồ biểu diễn
- [ ] 3 phép toán CSG + phép nào không giao hoán
- [ ] So sánh CSG vs Voxel

**Chương 3**
- [ ] Vẽ đủ 6 bước của Rendering Pipeline
- [ ] Phân biệt Illumination model vs Shading model vs Texture
- [ ] Kể 4 loại nguồn sáng, 4 yếu tố quyết định vẻ ngoài đối tượng
- [ ] Viết công thức 3 thành phần Diffuse / Ambient / Specular
- [ ] Nhớ quy tắc: ambient chỉ cộng MỘT LẦN với nhiều nguồn sáng
- [ ] Phân biệt Rasterization vs Ray Casting theo hướng xuất phát
- [ ] Nêu 3 loại tia phái sinh của Ray Tracing và hình học tương ứng
- [ ] Thuộc bảng độ phức tạp 4 thuật toán
- [ ] So sánh Local vs Global illumination + thuật toán thuộc mỗi nhóm
- [ ] Giải thích Mach banding và tại sao mắt người làm nó tệ hơn
- [ ] **So sánh Flat / Gouraud / Phong — nội suy cái gì, hạn chế gì**
- [ ] Nêu hạn chế specular highlight của Gouraud và cách khắc phục
- [ ] Hai lệnh `glShadeModel` của OpenGL

---

# PHỤ LỤC C. GÓC NHÌN DIGITAL TWIN

Bài học này không dạy đồ họa "để làm game", mà là **nền móng kỹ thuật cho lớp hiển thị của Digital Twin**. Ba mạch liên hệ có trong slide:

| Yêu cầu của Digital Twin | Kiến thức tương ứng trong bài |
|---|---|
| Hiển thị **thời gian thực** dữ liệu IoT | Rendering pipeline · Rasterization O(N) · LoD · Game Engine (Unity/Unreal) |
| **Mô phỏng vật lý** trước khi vận hành thật | Solid modeling (B-rep/CSG/Voxel) · Collision Detection · Rigid Body Dynamics |
| **Độ chân thực** cho VR/AR và mô hình CV | Illumination model · Shading (Flat→Gouraud→Phong) · Shadow mapping |

**Đánh đổi cốt lõi phải nắm:** chất lượng hình ảnh ⟷ chi phí tính toán. Slide 165 nói rõ triết lý: với đồ họa **thời gian thực**, ta chấp nhận các kỹ thuật **gần đúng** miễn là kết quả "**trông đúng**" — đây chính là lý do Digital Twin công nghiệp dùng rasterization + local illumination + Gouraud/Phong, chứ không dùng ray tracing/radiosity thuần túy.
