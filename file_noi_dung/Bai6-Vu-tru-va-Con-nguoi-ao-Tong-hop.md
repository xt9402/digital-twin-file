# Bài 6 — Vũ trụ và Con người ảo

> **Môn học:** Nhập môn Công nghệ Song sinh Thực - Số và Ứng dụng (Digital Twin)
> **Đơn vị:** Trường Công nghệ Thông tin và Truyền thông — Đại học Bách Khoa Hà Nội
> **Nguồn:** Tổng hợp từ slide bài giảng DT05 (46 slide)

---

## Mục lục

- [0. Thống kê cấu trúc bài giảng](#0-thống-kê-cấu-trúc-bài-giảng)
- [Phần 1 — Vũ trụ ảo (Metaverse)](#phần-1--vũ-trụ-ảo-metaverse)
- [Phần 2 — Công nghệ thực tế ảo (VR)](#phần-2--công-nghệ-thực-tế-ảo-vr)
- [Phần 3 — Con người ảo (Virtual Human)](#phần-3--con-người-ảo-virtual-human)
- [Sợi chỉ đỏ của cả bài](#sợi-chỉ-đỏ-của-cả-bài)
- [Danh sách kiểm tra trước khi thi](#danh-sách-kiểm-tra-trước-khi-thi)

---

## 0. Thống kê cấu trúc bài giảng

Toàn bộ slide: **46**, trong đó có **41 slide nội dung** (trừ bìa, 3 slide phân mục, slide kết).

| Phần | Chủ đề | Số slide | Tỷ trọng |
|---|---|---|---|
| 1 | Vũ trụ ảo (Metaverse) | 5 (slide 3–7) | ~12% |
| 2 | Công nghệ thực tế ảo (VR) | 27 (slide 9–35) | ~66% |
| 3 | Con người ảo (Virtual Human) | 9 (slide 37–45) | ~22% |

**Nhận xét sư phạm:** trọng tâm kỹ thuật của bài nằm ở Phần 2 (VR). Phần 1 và 3 mang tính khái niệm – ứng dụng, đóng vai trò "khung" bao quanh phần lõi công nghệ. Nếu ôn thi, Phần 2 là nơi chứa các con số, phân loại và thuật ngữ dễ ra đề nhất.

---

## Phần 1 — Vũ trụ ảo (Metaverse)

### 1.1 Định nghĩa

Metaverse là **không gian ảo được chia sẻ ở mức siêu thực tế**, nơi con người nhập vai và tương tác theo cách tương tự thế giới thật.

### 1.2 Quan hệ Digital Twin ↔ Metaverse

> Đây là nội dung then chốt nhất của Phần 1.

Mối quan hệ **hai chiều, bổ trợ lẫn nhau**:

| Thành phần | Vai trò | Đóng góp cho phía kia |
|---|---|---|
| **Digital Twin** | "Trái tim" về **dữ liệu** — cấp dữ liệu chính xác, thời gian thực từ thế giới vật lý | Giúp Metaverse trở nên **"thực"**: đối tượng trong Metaverse phản ánh đúng trạng thái/hành vi vật lý |
| **Metaverse** | "Giao diện" **trực quan và tương tác** — không gian 3D nhập vai | Giúp Digital Twin trở nên **"sống động"**: thay vì đọc biểu đồ, người dùng đi bộ trong nhà máy ảo |

Một cách diễn đạt khác mà bài giảng dùng: **Metaverse được coi như "hệ điều hành" cho các Digital Twin** — cung cấp nền tảng chung để nhiều Digital Twin cùng tồn tại và vận hành.

### 1.3 Vai trò của Metaverse

**1. Trực quan hóa và tương tác nhập vai**

- Thay giao diện 2D bằng trải nghiệm 3D chân thực
- Tương tác đa chiều: đi bộ, chạm, di chuyển đối tượng → phục vụ đào tạo, thiết kế, bảo trì

**2. Hợp tác đa người dùng**

- Làm việc từ xa: chuyên gia nhiều nơi gặp nhau trong một không gian ảo
- Đào tạo hiệu quả: thực hành quy trình phức tạp trong môi trường an toàn

### 1.4 Ví dụ minh họa — Nhà máy thông minh

Nhà máy được xây dựng dưới dạng Digital Twin → đưa vào không gian Metaverse. Nhớ theo **3 vai**:

- **Giám đốc điều hành:** họp ảo, đi qua khu sản xuất ảo để kiểm tra tiến độ, hiệu suất
- **Kỹ sư bảo trì:** dùng kính VR "đi vào" nhà máy, xem dữ liệu cảm biến trực tiếp, định vị máy lỗi mà không cần có mặt
- **Kỹ sư thiết kế:** thử thay đổi bố cục nhà máy trong không gian ảo trước khi áp dụng thật → tiết kiệm thời gian, chi phí

### 1.5 Khả năng mới được mở ra

- **Thành phố thông minh:** người dân tương tác với bản sao số của thành phố để kiểm tra giao thông, môi trường, dịch vụ
- **Y học:** bác sĩ dùng Digital Twin của bệnh nhân trong Metaverse để mô phỏng ca phẫu thuật

---

## Phần 2 — Công nghệ thực tế ảo (VR)

Đây là phần dài nhất, được chia thành **6 cụm kiến thức** để dễ nhớ.

### 2.1 Cụm A — Định nghĩa, lịch sử, định vị VR

#### Định nghĩa (P. Greenbaum, 1992)

> Thực tế ảo là một **thế giới khác** được tạo bởi hình ảnh **do máy tính tạo ra**, **tương ứng** với chuyển động của con người.

Ba từ khóa được nhấn mạnh trong slide: *thế giới khác* – *do máy tính tạo* – *tương ứng với chuyển động*.

#### Mốc lịch sử

| Năm | Sự kiện |
|---|---|
| 1962 | **Sensorama** — Morton Heilig, xuất phát từ ngành điện ảnh |
| ~1970s | Trực quan hóa thế giới ảo trên màn hình |
| 1970 | **HMD đầu tiên** — Daniel Vivkers, ĐH Utah (từ ý tưởng của Ivan Sutherland/MIT) |
| 1982 | **Dataglove** |
| 1980–85 | Sản phẩm VR thương mại đầu tiên |
| 1987 | **Virtual Cockpit** (British Aerospace): head/hand tracking, eye tracking, 3D visuals, 3D audio, speech recognition, vibro-tactile feedback |
| 1990–95 | Phổ biến hóa VR qua phim ảnh, sách |
| 1998 | ARMY chi gần **1000 triệu USD** cho nghiên cứu VR |

#### VR định vị ở đâu so với các media khác?

Bài dùng hệ **2 trục**:

- **Vividness** (tái hiện môi trường chính xác)
  - *breadth* — thị giác, thính giác, xúc giác, khứu giác
  - *depth* — chất lượng, độ trung thực
- **Interactivity** (cho phép người dùng thay đổi môi trường)
  - *speed* — tốc độ cập nhật, độ trễ
  - *mapping* — text, speech, gestures, gaze, mẫu hành vi phức tạp

→ VR nằm ở góc **vừa vividness cao vừa interactivity cao** — điều mà sách, ảnh, TV, điện thoại, hội nghị truyền hình đều không đạt được đồng thời.

#### Luận điểm quan trọng: "VR KHÔNG PHẢI là trò chơi!"

VR là một **loại phương tiện truyền thông (media) mới**. Bất cứ điều gì tạo ra được bằng phương tiện khác đều có thể tạo bằng VR: trò chơi, phim, truyện, trải nghiệm âm nhạc, mạng xã hội, mô phỏng, họp trực tuyến, **giáo dục**.

### 2.2 Cụm B — Tính "hòa nhập" (Immersion)

> Khái niệm lõi của toàn bộ Phần 2.

**Định nghĩa:** Môi trường VR là *immersive* nếu nó đem lại cho người dùng cảm giác **tồn tại trong** môi trường, **chứ không phải cảm giác đang quan sát**.

**Ba điều kiện phải đảm bảo:**

1. Thế giới **lý tưởng** sinh ra bởi máy tính
2. Giống thật về **hình dáng**
3. Tương tác với người dùng **cùng kiểu** với thế giới thực

**→ Hệ quả: tạo càng nhiều cảm nhận trên các giác quan càng tốt**

| Giác quan | Trạng thái công nghệ |
|---|---|
| Nhìn & nghe (sight & sound) | Cơ bản nhất — qua thiết bị hiển thị và âm thanh |
| Xúc giác | Qua phản hồi từ tay cầm, áo xúc giác (haptic interfaces) — đang nghiên cứu |
| Vị giác & khứu giác | Tạm thời bỏ qua |
| Giác quan thứ 6? | Câu hỏi mở |

**Lưu ý biện chứng:** Một VR *thật* không tồn tại. Mục tiêu thực tế là làm con người **cảm thấy thoải mái khi có các nhầm lẫn**, chấp nhận **tính xấp xỉ với hạn độ hợp lý**, và **đưa ra chuẩn thấp hơn** so với lý tưởng.

### 2.3 Cụm C — Phần cứng và hiển thị

#### Các thiết bị phần cứng hỗ trợ

- Màn hình toàn cảnh (gần đây ít dùng — chi phí lớn, tính cơ động thấp)
- Head-mounted display (HMD)
- Controllers
- VR treadmill
- Haptic gloves, haptic suit (chưa phổ biến do hạn chế công nghệ)

#### Hai họ hiển thị

| | Màn hình toàn cảnh | Head-mounted display |
|---|---|---|
| Ứng dụng | Hệ thống mô phỏng (vd: mô phỏng bay của US Airforce) | Cá nhân, cơ động |
| Nguyên lý | — | **Stereoscopic**: mắt trái/phải nhìn 2 ảnh khác nhau → não cảm nhận 3D. Cảm biến vị trí HMD → đồ họa thay đổi theo góc nhìn |

#### 3-DOF vs 6-DOF

> Nội dung rất hay ra đề.

- **DOF = Degree of Freedom**
- **3 trục xoay (rotational):** Rolling, Pitching, Yawing
- **3 trục tịnh tiến (translational):** Surging, Strafing, Elevating
- **3-DOF = chỉ xoay; 6-DOF = xoay + tịnh tiến**
- **6-DOF là yêu cầu quan trọng** với VR để tránh bị **"say"** (motion sickness, tương tự say ô tô)

**Thiết bị 3-DOF — gần như tuyệt chủng:**

- **Google Cardboard** — thực chất là *blueprint*, không phải sản phẩm; vẫn có giá rất rẻ
- **Samsung GearVR, Google Daydream** (ngừng sản xuất) — phải dùng điện thoại cụ thể, có controller 3-DOF
- **Oculus Go** (ngừng sản xuất) — standalone kèm controller

> ⚠️ **Ghi nhớ đặc biệt:** video 360 độ **LUÔN LUÔN** là 3-DOF.

#### Bảng HMD 6-DOF

Bài liệt kê các thiết bị với **4 tiêu chí so sánh**: giá – standalone hay không – độ phân giải mỗi mắt – tần số quét.

| Thiết bị | Giá | Standalone | Độ phân giải/mắt | Tần số |
|---|---|---|---|---|
| **Oculus Quest 2** | 300–400 € | Có | 1832×1920 | 90 Hz |
| **Valve Index** | 1000–2000 € | Không | 1600×1440 | 120 Hz |
| HTC Vive Pro 2 | 800–1400 € | Không | 2440×2440 | 120 Hz |
| HTC Vive Cosmos | 670 € | Không | 1700×1440 | 90 Hz |
| HTC Vive Focus 3 | 1500 € | Có | 2448×2448 | 90 Hz |
| Sony PlayStation VR | 300–900 € | Không | 1080×960 | 120 Hz |
| **Pico 4** | 429 € | Có | 2160×2160 | 90 Hz |
| Sony PSVR2 | 600 € | Không | 2000×2040 | 120 Hz |

**Quy luật cần rút ra** (quan trọng hơn học thuộc bảng):

- Dải giá rất rộng: 300 € → 2000 €
- Tần số quét chỉ có **2 mức: 90 Hz hoặc 120 Hz**
- Máy standalone (Quest 2, Vive Focus 3, Pico 4) thường rẻ hoặc phân giải cao
- **Pico 4** là ví dụ điển hình cho tỷ lệ giá/hiệu năng tốt

#### Tracking — 2 kiểu (dùng cho thiết bị 6-DOF)

- **Inside-out:** camera gắn trên người dùng, nhìn ra môi trường
- **Outside-in:** camera cố định trong môi trường, nhìn vào người dùng (dùng IR diodes / IR LEDs)
- Khi **mất tracking** → **dự đoán từ dữ liệu cảm biến**

### 2.4 Cụm D — VR / AR / MR / XR

> Phân biệt bắt buộc nhớ.

| Thuật ngữ | Tên Việt | Đặc trưng |
|---|---|---|
| **VR** | Thực tế ảo | Thế giới hoàn toàn do máy tính sinh |
| **AR** | Thực tế ảo tăng cường | **Kết hợp** đồ họa với thế giới thực; đầu vào máy tính kết hợp với góc nhìn thực của người dùng |
| **MR** | Thực tế hỗn hợp | Đối tượng thực và ảo **tương tác real-time**; người dùng tương tác với **cả hai** thành phần. **Cần HMD có trang bị camera** |
| **XR** | Thực tế ảo mở rộng | **XR = VR + AR + MR** |

**Điểm phân biệt AR/MR dễ nhầm:** AR là *chồng lớp* thông tin ảo lên thực; MR là *tương tác hai chiều real-time* giữa thực và ảo.

### 2.5 Cụm E — 5 nhóm công nghệ & yêu cầu kỹ thuật

#### Công nghệ trong VR (5 khối)

1. **Visual displays** — hiển thị thế giới ảo tới người dùng
2. **Tracking systems** — theo dõi vị trí và hướng của người dùng
3. **Computation systems** — tính toán để sinh không gian ảo
4. **Haptic devices** — cung cấp thao tác và nhận phản hồi cảm giác
5. **Audio systems** — phản hồi âm thanh

#### Yêu cầu về mặt nguyên lý

- Cảnh phải được **vẽ phụ thuộc vào điểm nhìn** của người dùng
- Xác định vị trí mắt → thiết bị theo dõi xác định hướng và vị trí **đầu/mắt** → phần mềm **hiệu chỉnh ma trận chiếu**
- Cảnh 3-D dựa trên **2 cơ chế thị giác**:
  - **Stereo vision** — khác biệt giữa ảnh của hai mắt
  - **Tiêu cự** — thấu kính mắt hội tụ khác nhau với vật gần/xa
- Người dùng phải có khả năng **ảnh hưởng đến khung cảnh** — qua đối tượng cầm tay có gắn thiết bị theo dõi, thiết bị theo dõi gắn trên quần áo, hoặc nguồn video feed

#### Technical requirements for presence

> Bảng số liệu quan trọng nhất của cả bài.

| Tiêu chí | Ngưỡng |
|---|---|
| Chuyển động | **6 DOF** |
| Độ chính xác xoay | **< ¼ độ** |
| Độ chính xác tịnh tiến | **< 1 mm** |
| Tracking | rock-solid |
| Tốc độ khung hình | **> 90 fps** |
| Pixel persistence | **< 3 ms** |
| Độ trễ motion-to-photon | **< 20 ms** |
| Độ phân giải | **> 1k mỗi mắt** |
| Trường nhìn (FOV) | **> 110 độ** |
| Quang học | Đã hiệu chuẩn, chất lượng cao |

### 2.6 Cụm F — Tối ưu hiệu năng & Locomotion

#### "Details matter"

Vì mọi thứ trong VR có thể được nhìn ở **cự ly cực gần**, cần render chất lượng cao nhất từ asset chất lượng cao nhất. Thêm nữa, **độ phân giải góc (angular resolution) của HMD hiện nay vẫn thấp** → chất lượng **từng pixel** thực sự quan trọng.

Slide trích lời Adam Savage (*Inside Valve: Making Half-Life: Alyx for Virtual Reality*) về việc người chơi VR chú ý và muốn nghịch mọi thứ, nên phải lấp đầy mọi ngóc ngách.

#### Kỹ thuật tối ưu render

**Stencil Mesh** — các mẫu lưới 1×1, 2×2, 4×4, 2×1/1×2, 4×2/2×4

**(Fixed) Foveated Rendering** — giảm độ phân giải ở vùng ngoại vi:

- Đã dùng trong ứng dụng Quest và một số ứng dụng PC; mức giảm độ phân giải có thể thay đổi theo thời gian; tiled rendering trên nền tảng mobile
- Được **eye tracking** hỗ trợ rất nhiều nhưng **vẫn hoạt động được khi không có**
- Dự đoán foveated rendering theo dõi động sẽ được áp dụng rộng hơn cùng PSVR2 và Meta "Quest Pro"
- Thực hiện được bằng **Variable Rate Shading**
- Cũng dùng để **supersampling vùng đang nhìn**: Nvidia **VRSS**
  - Dùng trong nhiều game PCVR, có thể dùng eye tracking của Tobii
  - Là tính năng mức driver nhưng cần Nvidia phê duyệt
- Có thể dùng cho **nén tín hiệu hiển thị**

#### Locomotion — 7 phương pháp di chuyển

> Cụm rất dễ ra đề "kể tên và so sánh".

| Phương pháp | Cơ chế | Ghi chú |
|---|---|---|
| **Continuous movement** | Bấm nút / gạt thumbstick để di chuyển liên tục theo hướng (tương đối với đầu hoặc tay) | **Gây motion sickness** → giảm bằng **'vignetting'** |
| **No locomotion** | Trò chơi diễn ra quanh người chơi tại một vị trí duy nhất | Chỉ chuyển động vật lý của người chơi tạo ra locomotion |
| **Fixed locations** | Di chuyển giữa các vị trí cố định | Do người chơi điều khiển hoặc theo sự kiện |
| **Projectile moving** | Bắn một vật thể, chọn thời điểm dịch chuyển tới vị trí đó | |
| **Blink teleportation** | Dịch chuyển tức thì tới vị trí mong muốn | |
| **Simulate running** | Vung controller lên xuống mô phỏng động tác tay khi chạy | |
| **Grappling hook** | Bắn móc câu, móc kéo người chơi về phía trước | |

**Cơ chế gây motion sickness:**

```
Mắt:      "We are moving!"    ─┐
                               ├─→  Não xung đột tín hiệu  →  RESET (vomit)
Tai trong: "No we are not!"   ─┘
```

Slide cũng gạch chéo bộ phím **WASD** — hàm ý điều khiển kiểu game PC truyền thống không phù hợp với VR.

---

## Phần 3 — Con người ảo (Virtual Human)

### 3.1 Định nghĩa

- Là **đối tượng ảo** được thể hiện với mức độ biểu diễn **thực tế tương tự con người thật**, sử dụng **AI + đồ họa máy tính**
- Là đối tượng tạo bằng phần mềm, hoặc nhân vật hư cấu hay con người, được thiết kế để **xuất hiện, hành xử và tương tác như con người** nhưng tồn tại trong môi trường ảo
- Tên gọi khác: **digital human**, **meta human**

### 3.2 Ba đặc điểm

1. Không bị giới hạn về **thời gian và không gian**
2. **Thích ứng cao** với nhiều lĩnh vực khác nhau
3. **Giảm thiểu rủi ro** thường gắn với cá nhân thật (rủi ro hình ảnh, sức khỏe)

### 3.3 Tiến hóa ứng dụng

```
Ca sĩ ảo tiên phong 'Adam' và 'Lusia' — Hàn Quốc, 1998
                    ↓
Người mẫu quảng cáo: TVC, thương mại điện tử, làm đẹp & sức khỏe
                    ↓
Vai trò mới: người dẫn chương trình AI, nhân viên ngân hàng AI, giáo sư AI
                    ↓
Virtual Influencer  &  Virtual YouTuber
```

### 3.4 Kỹ thuật tạo Virtual Human — 2 nhánh × 2 kỹ thuật

**A. Tái tạo 3D**

- **Digital Double:** tạo mô hình 3D chính xác của cá nhân thật — thường dùng trong phim Hollywood
- **Engine-Based:** dựng toàn bộ cơ thể trong môi trường 3D bằng game engine (**Unreal**, **Unity**) → cho phép định hướng tự nhiên từ nhiều góc độ

**B. Tổng hợp hình ảnh (Image Synthesis)**

- **Deepfake:** dùng AI tổng hợp khuôn mặt ảo lên cơ thể người thật từ hình ảnh thực → sản xuất nhanh, chi phí thấp **sau thời gian học ban đầu**
- **Generative AI:** tạo hình ảnh hoàn toàn mới từ dữ liệu hình ảnh → cho kết quả nhanh, **không cần công việc đồ họa thủ công**

> 💡 **Điểm phân biệt để nhớ:** nhánh A dựng **mô hình hình học 3D**; nhánh B sinh trực tiếp **pixel/ảnh 2D**.

### 3.5 Human Digital Twin

> Cầu nối quay về chủ đề chính của môn học.

- Là **biểu diễn kỹ thuật số chi tiết cao về một cá nhân**
- Bao gồm đặc điểm **sinh học, hành vi và môi trường** — **không chỉ ngoại hình** (đây là điểm khác biệt cốt lõi so với Virtual Human thuần túy)
- Tích hợp: điện toán, khoa học dữ liệu, **Trí tuệ nhân tạo**, **sinh trắc học**
- Vận hành dựa trên **thu thập dữ liệu liên tục** qua thiết bị đeo và cảm biến: chỉ số sức khỏe, hoạt động thể chất, sở thích, tương tác môi trường
- Thuật toán AI phân tích để:

  ```
  Mô phỏng hành vi con người
        ↓
  Dự đoán kết quả sức khỏe tương lai
        ↓
  Thích ứng với các thay đổi
        ↓
  Đảm bảo bản sao số luôn phản ánh đúng trạng thái hiện tại
  ```

### 3.6 Ứng dụng

#### Y tế

- **Y học cá nhân hóa** — mô phỏng kịch bản sức khỏe, điều chỉnh phác đồ điều trị
- **Phát triển thuốc và vắc-xin ("in silico medicine")** — thử nghiệm trên mô hình dữ liệu thay vì bệnh nhân thật; sàng lọc sớm vắc-xin không hiệu quả; tăng tốc nghiên cứu, nâng cao an toàn
- **Giám sát và dự đoán bệnh** — theo dõi liên tục nhịp tim, huyết áp, mức oxy; cảnh báo bất thường trước khi nghiêm trọng
- **Hỗ trợ chuyên gia y tế** — đóng vai "huấn luyện viên" cho cả bác sĩ và bệnh nhân

#### Giáo dục và đào tạo

- **Trải nghiệm học tập cá nhân hóa** — "bản sao ảo" học song song với người học, hoạt động như gia sư, điều chỉnh lộ trình theo điểm yếu
- **Môi trường học tập nhập vai và không rủi ro** — thử và sai an toàn
  - *Ví dụ:* tương tác với mô hình 3D của di tích lịch sử, phân tử phức tạp, dự án kỹ thuật
- **Phát triển nghề nghiệp** — dự đoán nhu cầu đào tạo lại, đề xuất chủ đề mới dựa trên **"dấu chân kỹ thuật số"** của cá nhân

#### Ứng dụng khác

- **Đại diện/đại lý ảo** — thay mặt cá nhân dự họp, đàm phán, thực hiện nhiệm vụ
- **Tăng cường khả năng con người** — hợp nhất/trao đổi kỹ năng với đối tác kỹ thuật số để vượt hạn chế thể chất, nhận thức
- **"Bất tử" kỹ thuật số** — lưu giữ ngoại hình, suy nghĩ, kinh nghiệm, ký ức; tương tác với "bản thân kỹ thuật số" hoặc người thân đã mất
- **Trong Metaverse** — thiết kế và tích hợp nhân vật người ảo vào các không gian Metaverse

### 3.7 Ba nhóm thách thức

**1. An toàn và tính riêng tư**

- **"Song sinh kỹ thuật số độc hại" (evil digital twin)** — mô hình phần mềm ảo độc hại dùng để tăng cường tội phạm mạng: ransomware, lừa đảo, chiến tranh mạng có mục tiêu cao
- Dữ liệu khổng lồ từ thiết bị IoT

**2. Độ chính xác dữ liệu**

- Sai lệch dữ liệu → **quyết định sai lầm**

**3. Triển khai ban đầu**

- Vấn đề dữ liệu
- **Khó khăn trong tích hợp dữ liệu**

---

## Sợi chỉ đỏ của cả bài

Bài giảng đi theo một mạch logic ba tầng — đây là điều cần nắm hơn cả các chi tiết rời rạc:

```
Digital Twin (dữ liệu)  →  cần một nơi để "sống"
        ↓
Metaverse (không gian / hệ điều hành)  →  cần công nghệ để người dùng bước vào
        ↓
VR / AR / MR / XR (cửa ngõ giác quan)  →  cần chủ thể để tương tác trong đó
        ↓
Virtual Human / Human Digital Twin (con người trong không gian đó)
```

**Chú ý điểm khép vòng:** Phần 3 quay lại chính khái niệm Digital Twin, nhưng đối tượng được "song sinh hóa" lúc này **không còn là máy móc hay nhà máy mà là con người**. Đây là ý tưởng thống nhất toàn bài — và cũng là câu hỏi tổng hợp có xác suất xuất hiện cao nhất trong bài thi.

---

## Danh sách kiểm tra trước khi thi

Tự trả lời được, không nhìn tài liệu:

- [ ] Digital Twin và Metaverse hỗ trợ nhau như thế nào? (2 chiều, 2 ẩn dụ "trái tim" / "giao diện")
- [ ] Định nghĩa immersion và 3 điều kiện đảm bảo
- [ ] Phân biệt VR / AR / MR, và công thức XR
- [ ] 6 bậc tự do gồm những gì (3 xoay + 3 tịnh tiến, tên tiếng Anh)
- [ ] Tại sao 6-DOF quan trọng; cơ chế gây motion sickness
- [ ] 5 nhóm công nghệ trong VR
- [ ] Các ngưỡng kỹ thuật cho presence (90 fps, 20 ms, 110°, ¼°, 1 mm, 3 ms, 1k/mắt)
- [ ] 7 phương pháp locomotion và ưu/nhược
- [ ] 4 kỹ thuật tạo Virtual Human (2 nhánh)
- [ ] Human Digital Twin khác Virtual Human ở điểm nào
- [ ] 3 nhóm thách thức, đặc biệt khái niệm "evil digital twin"
