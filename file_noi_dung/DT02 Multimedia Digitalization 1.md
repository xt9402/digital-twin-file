# TỔNG HỢP KIẾN THỨC — Bài 2: SỐ HÓA ĐA PHƯƠNG TIỆN
### Môn: Nhập môn Công nghệ Song sinh Thực – Số (Digital Twin) và Ứng dụng

> Tài liệu này hệ thống lại toàn bộ nội dung bài giảng (153 slide), tổ chức theo 5 phần chính, giữ nguyên các công thức và số liệu ví dụ có trong slide.

---

## BẢN ĐỒ NỘI DUNG

| Phần | Chủ đề | Trọng tâm cần nắm |
|------|--------|-------------------|
| 1 | Dữ liệu Multimedia | Phân loại media, đặc điểm & thách thức của dữ liệu đa phương tiện |
| 2 | Xử lý dữ liệu **văn bản** | Sinh ký tự, nén không mất dữ liệu, biểu diễn (tf.idf), đánh chỉ mục, tìm kiếm |
| 3 | Xử lý dữ liệu **âm thanh** | PCM/DPCM/ADPCM, định lý Shannon, miền tần số, chuẩn nén (MP3, FLAC) |
| 4 | Xử lý dữ liệu **ảnh** | Số hóa ảnh, hệ màu, nén JPEG (DCT), các định dạng ảnh |
| 5 | Xử lý dữ liệu **video** | Dư thừa không–thời gian, MPEG (I/P/B frame, GOP), H.264, HEVC |

**Ý tưởng xuyên suốt cả bài:** mọi loại media đều đi qua chuỗi
`Số hóa → Mã hóa (nén) → Truyền/Lưu trữ → Giải mã → Giải số hóa → Đầu ra`,
và bộ công cụ nén được **dùng lại** ở nhiều loại dữ liệu (xem phần "Nhìn xuyên suốt" ở cuối).

---

## PHẦN 1 — DỮ LIỆU MULTIMEDIA

### 1.1. Kiểu dữ liệu media
Là các kiểu thông tin và cách biểu diễn thông tin (văn bản, hình ảnh, đồ họa, video…) có thể **biểu diễn – lưu trữ – truyền – xử lý dưới dạng số**. Sáu thành phần: Text, Still image, Audio, Animation, Video footage, Interactivity.

**Phân loại media:**
- **Theo định dạng vật lý.**
- **Theo mối liên hệ với thời gian:**
  - *Media tĩnh (static media):* nội dung/ý nghĩa **không** phụ thuộc thời gian → văn bản, ảnh tĩnh, đồ họa.
  - *Media động (dynamic/continuous/isochronous media):* dữ liệu **có chiều thời gian** → hoạt hình, âm thanh, video.

### 1.2. Multimedia (đa phương tiện)
Tập hợp nhiều kiểu media dùng cùng nhau, trong đó có **ít nhất một kiểu không phải văn bản** (hoạt hình, âm thanh, video). Coi như tổ hợp của: văn bản + âm thanh + ảnh tĩnh + ảnh động + video + các hình thức tương tác nội dung.

### 1.3. Các loại dữ liệu thành phần
| Loại | Đặc điểm chính | Đầu vào (Input) | Kích thước lưu trữ |
|------|----------------|-----------------|--------------------|
| **Văn bản (text)** | Chứa thông tin chủ đạo. ASCII (text thường) hoặc có định dạng (HTML, XML, RTF, Word, mã nguồn) | Bàn phím, nhận dạng âm thanh/ký tự, phụ đề… | Không đáng kể |
| **Đồ họa** | Xây từ đối tượng cơ bản (primitive): đường thẳng, cong, đa giác, tròn → tạo 2D/3D. **Dễ sửa** (khác ảnh). Chuẩn: OpenGL, PHIGS, GKS | Illustrator, AutoCAD, PostScript | Không quá lớn (lưu tập primitive) |
| **Ảnh** | Chuỗi điểm ảnh biểu diễn vùng hiển thị. Định dạng: jpg, png, bmp, tiff… | Camera, scan, phần mềm sinh/xử lý ảnh | Tùy kích thước, độ phân giải, kỹ thuật nén |
| **Âm thanh** | Tín hiệu **tương tự, liên tục** | Microphone → số hóa | Lớn (thường phải nén) |
| **Video** | Chuỗi khung hình (25/30/50 fps) | Video camera → số hóa | **Tốn nhất** (bắt buộc nén) |

**Số liệu độ sâu bit của ảnh:** 1 bit/pixel (nhị phân) · 8 bit/pixel (đa mức xám) · 24 bit/pixel (ảnh màu).

**Số liệu âm thanh (CD Quality):** 16-bit sampling @ 44.1 kHz → 1 phút Mono CD chưa nén ≈ **5 MB**, Stereo ≈ **10 MB**. Nén bằng mp3, aac, FLAC, Ogg Vorbis…

**Số liệu video chưa nén (ví dụ trong slide):**
- Video 512×512 đơn sắc: 25 × 0.25 = **6.25 Mb/giây**
- PAL (720×576/khung, màu): 1.2 × 25 = **30 Mb/giây**
- HD DVD (1440×1080 = 1.5 Megapixel/frame): 4.5 × 25 = **112.5 Mb/giây**

### 1.4. Đặc điểm của dữ liệu đa phương tiện
- Kích thước **lớn** → cần cấu trúc đặc biệt để lưu trữ và đánh chỉ mục.
- Có **chiều thời gian** (audio, video).
- Biểu diễn qua **chuỗi giá trị riêng lẻ**, thiếu cấu trúc ngữ nghĩa rõ ràng để máy "hiểu".
- Nhiều ứng dụng cần biểu diễn **nhiều kiểu dữ liệu đồng thời** với thông số thời gian & không gian.
- **Ngữ nghĩa mờ và chủ quan:** cùng một bức ảnh, hai người hiểu khác nhau.
- **Giàu thông tin:** cần nhiều tham số để biểu diễn nội dung.

### 1.5. Thách thức
Biểu diễn nội dung tin cậy & có ngữ nghĩa; biểu diễn câu hỏi/câu trả lời phức hợp; vấn đề lưu trữ – truy nhập – truyền qua mạng khi dữ liệu lớn; **thời gian truy vấn** (đặc biệt audio/video); trích chọn thuộc tính tự động và đánh chỉ mục.

---

## PHẦN 2 — XỬ LÝ DỮ LIỆU VĂN BẢN

### 2.1. Sơ đồ khối chung
`Dữ liệu đầu vào (bàn phím, máy quét, cảm biến, thiết bị IoT…) → Số hóa → Mã hóa → Truyền/Lưu trữ → Giải mã → Giải số hóa → Dữ liệu đầu ra (màn hình, máy in…)`

### 2.2. Sinh ký tự
- **Ký tự bitmap:** mỗi ký tự (với một font cho trước) là một **bitmap chữ nhật nhỏ**. Font/typeface = tập hình dạng ký tự.
  - *fontcache:* các ký tự lưu liên tiếp trong bộ nhớ.
  - *Dạng cơ bản:* Thường (N), Nghiêng (I), Đậm (B), Nghiêng-đậm (B+I).
  - *Thuộc tính:* màu, kích thước, khoảng cách, hướng.
  - Cấu trúc dữ liệu: `Char location{leftx, width}` và `fontcache{CacheId, Height, CharSpace, Charlocation Table[128]}`.
- **Ký tự vector:** định nghĩa ký tự bằng **đường cong mềm bao ngoài** → **tốn kém tính toán nhất** nhưng **chất lượng cao**.

### 2.3. Nén văn bản (luôn là nén KHÔNG mất dữ liệu)
Vì mất dù chỉ một ký tự cũng có thể đổi nghĩa cả chuỗi. Bốn thuật toán:
**Run-Length Encoding · Huffman · Lempel–Ziv–Welch (LZW) · Mã hóa số học.**

**Tỷ lệ nén — 2 định nghĩa** (gọi N = kích thước gốc, N₁ = kích thước sau nén):
- **Định nghĩa 1:** `R = N / N₁` → ví dụ 8KB → 2KB ⇒ **R = 4:1**.
- **Định nghĩa 2:** `R = 1 − N₁/N` → ví dụ 8KB → 2KB ⇒ **R = 75%**.

### 2.4. Các định dạng văn bản
| Định dạng | Đặc trưng |
|-----------|-----------|
| **.txt** | Đơn giản nhất; đổi được font/cỡ chữ nhưng **không** màu, **không** chèn ảnh, không header/footer/ghi chú. Dùng ASCII, nay dùng Unicode **UTF-8** (tương thích ngược ASCII). Nén bằng Huffman & LZW |
| **.rtf** | Mở rộng của txt: hỗ trợ nhiều font, màu, kiểu chữ; chèn ảnh (một số định dạng) & biểu đồ; căn lề |
| **.doc/.docx** | Siêu văn bản (Microsoft Office): tùy biến chữ/màu, chèn ảnh–biểu đồ–hình vẽ, tổ chức mục/chương/phần, header/footer/background, ghi chú, mục lục, liên kết, **macro** |

### 2.5. Biểu diễn văn bản (nền tảng hệ thống IR – Information Retrieval)
- **Bag of words:** mỗi tài liệu = tập các từ. VD *"Lord of the rings"* → {the, Lord, rings, of}. Mỗi từ là **một chiều** trong không gian từ điển → số chiều = kích thước từ điển.
- **Kỹ thuật xử lý:**
  - **Stop list:** loại từ không giúp phân biệt tài liệu (the, a, of, at…). *Phụ thuộc ngữ cảnh CSDL:* từ "computer" là stop-word trong tài liệu Khoa học máy tính nhưng **không** trong tài liệu nông–lâm nghiệp.
  - **Stemming:** gộp biến thể về từ gốc: retrieved/retrieval/retrieving → "retriev"; drug/drugs/drugged → "drug".
  - **Thesaurus:** gộp từ gần nghĩa: learning/school work/reading/study → "study".
  - **Frequency table (M×N):** `tf(i,j)` = số lần từ tᵢ xuất hiện trong tài liệu dⱼ. Mỗi văn bản = vector `(tf₁ⱼ,…,tf_Mⱼ)`, thường **chuẩn hóa về [0,1]** để khử ảnh hưởng độ dài văn bản.

**Trọng số tf·idf:**
$$idf_i = \log\!\left(\frac{N}{df_i}\right),\qquad w_{i,j} = tf(i,j)\times idf(i)$$
- N: tổng số văn bản; dfᵢ: số văn bản chứa từ tᵢ.
- Mỗi văn bản dⱼ → vector tf·idf: `(w₁ⱼ,…,w_Mⱼ)`.

### 2.6. Đánh chỉ mục (Indexing)
- **Flat-files:** không hiệu quả.
- **Inverted files (file đảo):** hiệu quả, dễ cài đặt, **thông dụng nhất**.
- **Signature files** (PAT trees, graphs).

**Cấu trúc Inverted file:** bảng **Term → Postings_list**, trong đó Term lưu từ/khái niệm/từ khóa, Postings_list chỉ ra tài liệu (và có thể cả vị trí) mà term xuất hiện. Bản ghi có thể chứa vị trí chi tiết `(Doc id, Paragraph, Sentence, Word n°)` và **tần suất** term trong tài liệu.

### 2.7. Tìm kiếm (Retrieval)
Câu truy vấn Q được biểu diễn **giống tài liệu**, rồi tính **khoảng cách** giữa Q và các dⱼ. Ba nhóm mô hình: **Set-theoretic** (Boolean, Fuzzy, Extended Boolean), **Algebraic** (Vector, LSI, Neural), **Probabilistic** (Inference/Belief Networks).

**Mô hình Boolean:**
- Truy vấn = từ khóa + toán tử logic AND/OR/NOT (VD: "information" AND "retrieval").
- Thực hiện dễ trên Inverted file (hợp/giao/trừ). `distance(Q, dⱼ) ∈ {0, 1}`.
- **Ưu:** rõ ràng, đơn giản. **Nhược:** exact matching → kết quả quá nhiều/quá ít; khó biểu diễn truy vấn phức tạp.

**Mô hình Vector:**
- Văn bản và truy vấn = vector M chiều có trọng số (thường là tf·idf):
  `Dⱼ = [w_{j1},…,w_{jM}]`, `Qᵢ = [w_{i1},…,w_{iM}]`.
- **Khoảng cách khái niệm (Euclid):** $d(Q_i,D_j)=\sqrt{\sum_{k=1}^{M}(w_{ik}-w_{jk})^2}$
- **Độ tương tự cosine:** $S(Q_i,D_j)=\dfrac{Q_i\cdot D_j}{\lVert Q_i\rVert\,\lVert D_j\rVert}=\dfrac{\sum_k w_{ik}w_{jk}}{\sqrt{\sum_k w_{ik}^2}\sqrt{\sum_k w_{jk}^2}}$ → **khoảng cách cosine = 1 − S**.
- **Ranking:** sắp xếp giảm dần theo độ tương tự.
  - *Ví dụ trong slide:* Q = [0.5, 0.5, 0, 0]; kết quả 1−S: D1=0.69, D2=0.07, D3=0.34, D4=0.93 → **thứ tự D2, D3, D1, D4** (giá trị càng nhỏ càng giống; 0 = giống hệt, 2 = khác hoàn toàn).
- **Ưu:** tìm gần đúng (partial matching), đo được độ giống, đơn giản, hợp văn bản ngắn.
- **Nhược:** coi các term độc lập; chưa xét quan hệ không gian giữa từ; độ phức tạp **O(M×N)** rất lớn khi M, N lớn (tiếng Anh > 10 triệu từ).

---

## PHẦN 3 — XỬ LÝ DỮ LIỆU ÂM THANH

### 3.1. Bản chất & đặc trưng vật lý
Âm thanh là **sóng âm** sinh từ dao động vật thể, truyền trong môi trường. Ba đặc trưng:
- **Độ cao (pitch):** do **tần số** dao động; đơn vị **Hz**. Tần số càng lớn → âm càng cao.
- **Độ mạnh/cường độ:** do **biên độ** dao động; đơn vị **Ben**, thực tế dùng **decibel (dB)** = 1/10 Ben.
- **Độ dài:** giá trị thời gian phát dao động của nguồn âm.

### 3.2. Chuyển đổi Analog → Digital
**Yêu cầu:** chất lượng vẫn tốt sau chuyển đổi · kích thước nhỏ dễ lưu trữ · kỹ thuật đơn giản.
**Ba kỹ thuật:** PCM · DPCM · ADPCM.

**PCM (Điều chế xung mã)** — 3 bước: **Lấy mẫu → Lượng tử hóa → Mã hóa.**
- **Định lý Shannon:** tín hiệu khôi phục được nếu lấy mẫu với tần số ≥ 2 lần tần số lớn nhất, tức chu kỳ lấy mẫu:
  $$T_s \le \frac{1}{2 f_{max}}$$
  *Ví dụ (bài tập trong slide):* tín hiệu thoại 0,3 ÷ 3,4 kHz → tần số lấy mẫu cần ≥ 2 × 3,4 kHz.
- **Khôi phục tín hiệu:** dùng phép **nội suy**.
- **Lượng tử hóa đều:** chia biên độ thành các khoảng **bằng nhau**.
- **Lượng tử hóa không đều:** biên độ xung càng lớn thì **bước lượng tử càng lớn**.
- **Ưu:** đơn giản, chất lượng tốt. **Nhược:** biên độ lớn → tốn nhiều bit → ảnh hưởng lưu trữ & truyền.

**DPCM (sai phân):** mã hóa **sự sai khác giữa các mẫu liên tiếp**; nếu tín hiệu thay đổi quá lớn giữa các mẫu thì bị cắt.

**ADPCM (sai phân thích ứng):** mức lượng tử hóa thay đổi theo dạng tín hiệu đầu vào = **mã hóa sai phân + lượng tử hóa không đều**.

### 3.3. Miền thời gian ↔ Miền tần số
Để xử lý âm thanh: chuyển **miền thời gian → miền tần số**, xử lý trong miền tần số, rồi chuyển ngược lại.
- Miền tần số là công cụ **toán học thuần túy** (do con người "tưởng tượng" để phân tích); **chỉ miền thời gian là có thực**.
- Chuyển đổi bằng **biến đổi Fourier:** CFT (liên tục), DFT (rời rạc), FFT (nhanh).

### 3.4. Nén âm thanh
- **Không tổn thất (lossless):** Huffman (hoặc Huffman sửa đổi), LZW, mã hóa số học.
- **Có tổn thất (lossy):** **Mã hóa dự đoán tuyến tính (LPC).**

**LPC:** mẫu hiện tại được xấp xỉ bằng **tổ hợp tuyến tính M mẫu trước**:
$$S(n)\approx a_1 S(n-1)+a_2 S(n-2)+\dots+a_M S(n-M)$$
- Lỗi dự đoán: `e(n) = s(n) − s'(n)` (s' là giá trị ước đoán).
- Tìm hệ số aᵢ bằng cách **cực tiểu hóa sai số bình phương trung bình** (đặt đạo hàm riêng của J = 0).

### 3.5. Định dạng & chuẩn nén âm thanh
| Định dạng | Đặc điểm |
|-----------|----------|
| **WAV** | Chuẩn IBM/Microsoft, **chưa nén**, mã hóa **PCM tuyến tính**. Phổ biến 44,1 kHz / 16 bit / 2 kênh. Giới hạn **< 4 GB** (header dùng 32-bit không dấu); **không** có trường metadata (tên bài, ca sĩ…) |
| **MP3** (MPEG-1 Audio Layer-3) | Ra đời 1980 (Fraunhofer, Đức). Nén **mất dữ liệu**. Hỗ trợ 32/44,1/48 kHz; bitrate 32–448 kbps (thường 128/160/192 kbps). Cấu trúc = nhiều khung (header + data); chứa **ID3 metadata** |
| **RealAudio** | Của RealNetworks (1995), chủ yếu phát nhạc trực tuyến |
| **WMA** | Của Microsoft (1999); lý thuyết nén 96 kbps ~ chất lượng MP3 128 kbps |
| **FLAC** (Free Lossless Audio Codec) | Nén **không mất dữ liệu**, **định dạng mở**, hỗ trợ metadata/bìa album. Dùng Huffman + Run-Length + **LPC**. Hỗ trợ 4–32 bit, tần số tới **655.350 Hz**, 2–8 kênh; tham số chất lượng 0–8; dùng **CRC checksum**; giảm ~**50%** so với WAV. Cấu trúc: "flac" (4 byte) + khối STREAMINFO + khối khác + các khung âm thanh |

---

## PHẦN 4 — XỬ LÝ DỮ LIỆU ẢNH

### 4.1. Khái niệm
Xử lý ảnh số = biến đổi ảnh số trên máy tính. Ảnh số tạo bởi số **hữu hạn** điểm ảnh; mỗi điểm có vị trí & giá trị nhất định → gọi là **pixel**.

### 4.2. Các giai đoạn chính
1. **Thu nhận ảnh:** camera (tương tự/số), vệ tinh (sensor), máy quét.
2. **Số hóa ảnh:** biến ảnh tương tự → rời rạc, qua **lấy mẫu** (rời rạc không gian) + **lượng tử hóa** (rời rạc biên độ).
3. **Xử lý số:** tăng cường (enhancement), khôi phục (restoration), phát hiện biên (edge detection), phân vùng (segmentation), trích chọn đặc tính (feature extraction)…
4. **Hệ quyết định:** hiển thị, nhận dạng, phân lớp, truyền thông…

**Ứng dụng:** y học; địa chất (bản đồ địa hình 3-D từ ảnh vệ tinh); khí tượng (dự báo thời tiết); kiểm soát chất lượng dây chuyền tự động; hình sự & bảo mật/kiểm soát truy cập; nhận dạng mục tiêu quân sự; máy nhìn công nghiệp; nén để lưu/truyền.

### 4.3. Số hóa ảnh
Phương pháp chung: **lấy mẫu theo hàng và mã hóa từng hàng.**
Sơ đồ: `f(x,y) [ảnh tương tự] → Lấy mẫu → fᵢ(m,n) → Lượng tử hóa → u(m,n) → Máy tính`.
- **Lấy mẫu** trên lưới chữ nhật (bước Δx, Δy) thỏa Shannon 2D: $\Delta x \le \frac{1}{2 f_{x,max}},\ \Delta y \le \frac{1}{2 f_{y,max}}$. Thực tế có nhiễu nên dùng thêm lưới không vuông, lưới bát giác.
- **Lượng tử hóa:** ánh xạ biến liên tục u (độ sáng) → biến rời rạc u* trong tập hữu hạn {r₁,…,r_L}. Chia dải sáng Lₘᵢₙ→Lₘₐₓ thành các mức; thường Lₘᵢₙ=0, Lₘₐₓ = 2^B (thường **B = 8** → mỗi điểm ảnh 8 bit).

### 4.4. Biểu diễn & phân loại ảnh
- **Biểu diễn:** ma trận `f(x,y)` kích thước M×N; pixel tại (x,y) có độ xám/màu nhất định.
- **Theo nguồn gốc:** *ảnh tự nhiên* (camera, kính hiển vi, tomography, hồng ngoại, vệ tinh) vs *ảnh nhân tạo* (đồ họa máy tính, thực tại ảo).
- **Theo mức xám:**
  - Ảnh mức xám: I(x,y) ∈ [0..255]
  - Ảnh nhị phân: I(x,y) ∈ {0, 1}
  - Ảnh màu: 3 kênh I_R, I_G, I_B

### 4.5. Hệ tọa độ màu
- **RGB:** một màu = tổ hợp R (đỏ) + G (lục) + B (lơ) theo tỷ lệ.
- Ngoài ra: **CMYK, HSV, YIQ** — có thể chuyển đổi qua lại.

### 4.6. Độ phân giải
- **Lấy mẫu** → độ phân giải **không gian**.
- **Lượng tử hóa** → độ phân giải **mức xám**.

### 4.7. Nén ảnh
**Ý tưởng:** giảm thông tin dư thừa (mã hóa chuỗi cùng độ chói bằng (độ chói, chiều dài); thay chuỗi đặc biệt bằng token; dùng mẫu thay thế; chuyển sang miền tần số bằng Fourier/Cosine; dựa trên sai phân; lượng tử hóa vector).

**Thước đo chất lượng:** **PSNR (Peak Signal-to-Noise Ratio).**

**Kỹ thuật nén:**
- *Không mất thông tin:* Pixel Packing, RLE, LZW, mã hóa số học, Huffman.
- *Mất thông tin:* **DCT (JPEG)**, Wavelet, Fractal.

**Sơ đồ khối nén ảnh tiêu biểu:**
`Tín hiệu gốc → Bộ chuyển đổi (DCT: dồn năng lượng vào ít hệ số) → Bộ lượng tử hóa (vô hướng/vector; KHÔNG thuận nghịch — tạo phần lớn tỷ lệ nén) → Bộ mã hóa (gán từ mã nhị phân) → Tín hiệu nén`.

**Chuẩn nén ảnh/video liên quan:**
- **JPEG** — nén ảnh tĩnh (ITU-TS + ISO).
- **MPEG-1/2/4/7** — nén kết hợp video + audio (ISO IEC/JTC1/SC29/WG11).
- **H.261** — video nx 64 Kbps; **H.263** — điện thoại thấy hình < 64 Kbps.

### 4.8. Chuẩn JPEG (chi tiết)
- Tổ chức JPEG lập 1982; chuẩn thiết lập 1986 (ISO/IEC + ITU). Dùng cho ảnh tĩnh đơn sắc & màu; chất lượng khôi phục tốt, ít tính toán hơn MPEG.
- **Ý tưởng:** loại các thành phần **tần số cao** (mắt người ít cảm nhận) → chuyển ảnh sang miền tần số bằng **DCT**, áp dụng trên **khối 8×8 pixel**.
- **Các bước nén:** `Phân khối 8×8 → DCT → Lượng tử hóa (theo bảng lượng tử) → Mã hóa (Huffman, theo bảng mã) → ảnh nén`.
- **Mã hóa Huffman (entropy):** ký hiệu xuất hiện nhiều → mã ngắn hơn.
  *Ví dụ:* A(50%)→0, B(25%)→10, C(15%)→110, D(10%)→111.
- Hệ số DC: $X(0,0)=\frac{1}{8}\sum_{m=0}^{7}\sum_{n=0}^{7} x(m,n)$ (x là khối 8×8, X là hệ số tần số).
- **Vì sao dùng DCT thay Fourier (DFT)?**
  - DCT **chỉ có phần thực**, không có phần ảo phức tạp như DFT.
  - DCT **dồn năng lượng tốt hơn** → nén hiệu quả hơn.
  - DCT dùng dữ liệu đối xứng → **biên mượt**; DFT dùng tín hiệu tuần hoàn → tạo nhảy xung ở biên.
- **Khôi phục:** `ảnh nén → Giải mã → Lượng tử hóa ngược → IDCT (DCT ngược) → ảnh`. Sai số: `e(j,k) = f(j,k) − f*(j,k)`.

### 4.9. Các định dạng ảnh
| Định dạng | Nén | Đặc điểm nổi bật |
|-----------|-----|------------------|
| **BMP** (.bmp/.dib) | Thường **không nén** | Windows bitmap, rất phổ biến |
| **GIF** | Lossless | ≤ 256 màu, hỗ trợ hoạt hình; tốt cho đường truyền nhỏ; CompuServe 1987 |
| **PNG** | Lossless | Cải thiện & thay GIF; **không cần giấy phép sáng chế**; thư viện libpng |
| **TIFF** | Lossless hoặc không nén | Giữ nguyên chi tiết/màu; **kích thước rất lớn**; dùng trong Photoshop, Lightroom |
| **JPEG/JPG** | **Lossy** | Bỏ bớt dữ liệu gốc → nhẹ; hợp chia sẻ mạng/email; **không** hỗ trợ ảnh động |
| **WebP** (Google) | Lossy & lossless | Thay JPEG/PNG/GIF; **nhỏ hơn JPEG ~25–35%** ở cùng chất lượng |
| **HEIF/HEIC** | Lossy & lossless | HEIC dùng nén video **HEVC (H.265)**; chất lượng cao hơn JPEG, **nhỏ hơn ~40–50%** |

### 4.10. Một số kỹ thuật xử lý ảnh
- **Histogram (lược đồ ảnh):** phân bố các giá trị mức xám. `H(k)` = tổng số pixel có giá trị k.
- **Luminance (độ sáng):** giá trị **trung bình** của tất cả mức xám trong ảnh.
- **Contrast (độ tương phản):** định nghĩa bằng **độ lệch chuẩn** mức xám hoặc **dao động min–max** mức xám.
- **Các phép cơ bản:** tăng cường độ tương phản.
- **Bộ lọc:** làm mờ (blurring), làm rõ (sharpening), giảm nhiễu (Wiener), lọc trung bình, **lọc trung vị (median)**.
- **Phát hiện biên (edge detection).**

---

## PHẦN 5 — XỬ LÝ DỮ LIỆU VIDEO

### 5.1. Khái niệm
Video = chuỗi tín hiệu điện tử tạo nguồn hình ảnh tĩnh liên tiếp, **mô phỏng chuyển động**. Một giây thường **24 hoặc 30 khung hình**.
- Mỗi pixel biểu diễn bằng **3 thuộc tính 8-bit** theo **YCbCr**: **Y** (độ chói – độ sáng), **Cb & Cr** (sắc độ – màu sắc).
- **Thuật ngữ:** *Frame* (một ảnh) → *Shot* (chuỗi frame trong một thao tác camera) → *Scene* (tập shot tạo cảnh có nghĩa). Video: 20/30 fps.

### 5.2. Vì sao cần nén video?
- Video **chưa nén** 720×480, 30 fps, 90 phút, đủ màu → **≈ 167,96 GB**.
- **HDTV** 1920×1080, 30 fps, 8 bit/màu (RGB) → **≈ 1,5 Gb/giây**.

### 5.3. Hai loại dư thừa
Nén video dựa trên **2 yếu tố** + đặc điểm mắt người (phân giải chi tiết độ **sáng** tốt hơn chi tiết **màu** → có thể xóa bớt dữ liệu mà chất lượng gần như không giảm):
- **Dư thừa không gian (spatial):** trong một khung, các pixel lân cận thường tương quan (VD nền cỏ xanh).
- **Dư thừa thời gian (temporal):** giữa các khung liên tiếp thường tương quan (VD nền xanh giữ nguyên qua nhiều khung).

**Giảm dư thừa không gian** (mã hóa trong khung – *Intra-Frame*):
`RGB → YUV (con người ít nhạy sắc độ) → Khối macro (16×16) → DCT (dồn tần số) → Lượng tử hóa (giảm dữ liệu, kiểm soát "chất lượng") → Zig-Zag Scan + Run-Length → Mã hóa entropy`.

**Giảm dư thừa thời gian:** tận dụng tương đồng giữa các khung liên tiếp qua **vector chuyển động (motion vector)**.

### 5.4. MPEG (Motion Picture Experts Group)
Lập 1988 trong ISO/IEC, định chuẩn nén số cho tín hiệu audio & video.
| Chuẩn | Năm/ISO | Đặc điểm & bitrate |
|-------|---------|--------------------|
| **MPEG-1** | 1993 / ISO-IEC 11172 | Nén video quét liên tục; dùng CD-i, Video CD; **1,5 Mbit/s** |
| **MPEG-2** | 1995 / ISO-IEC 13818 | Quét liên tục & xen kẽ; phát sóng SDTV/HDTV; **3–15 Mbit/s (SD)**, **15–20 Mbit/s (HD)** |
| **MPEG-4** | 1998 / ISO-IEC 14496 | Mã hóa **hướng đối tượng**; 3D, đồ họa hoạt hình, tương tác; streaming web/DVB; **vài Kbit/s → hàng chục Mbit/s** |
| **MPEG-7** | (đang phát triển) | Chuẩn **biểu diễn nội dung để tìm kiếm** đa phương tiện trên web; hoạt động cả thời gian thực & không thời gian thực |

### 5.5. Ba loại khung hình MPEG
- **I-frame (Intra-coded):** khung **độc lập**, mã hóa **không tham chiếu** khung khác.
- **P-frame (Predictive):** **không** độc lập; dự đoán từ **một** khung I/P **quá khứ** (mã hóa dự đoán chuyển tiếp).
- **B-frame (Bi-directional):** tham chiếu **cả khung quá khứ và tương lai** (I/P) — xử lý chuyển động bất ngờ khi khối macro không khớp tốt ở khung trước.

**Group of Pictures (GOP):** chuỗi khung I/P/B sắp thành nhóm; bắt đầu bằng **I-frame**, kết thúc ngay trước I-frame kế tiếp. "Open" kết bằng B-frame, "Closed" kết tại P-frame. VD: **IBBPBBPBBI**, IBBPBBPBBPBBI.
**Khối macro:** đơn vị nén **16×16 pixel**.

**Số liệu nén theo loại khung (Variable Bit Rate):**
| Loại | Kích thước | Tỷ lệ nén |
|------|-----------|-----------|
| I | 18 KB | 7:1 |
| P | 6 KB | 20:1 |
| B | 2.5 KB | 50:1 |
| **Trung bình** | **4.8 KB** | **27:1** |

### 5.6. H.264 (Advanced Video Coding)
Ba bước: **Dự đoán → Chuyển đổi + Lượng tử hóa → Mã hóa.** Điểm mới: mỗi macroblock chia theo **1 trong 7 cách** (tới 16 khối nhỏ, nhiều MV); bù chuyển động độ chính xác **1/4 pixel**; **nhiều khung tham chiếu**. Ứng dụng: Web video, Mobile video…

### 5.7. HEVC / H.265
Bối cảnh: lưu lượng Internet do video chi phối (**~80%** là video), cần giảm băng thông; hướng tới **4K–8K**.
- **Ưu:** tỷ lệ nén cao hơn (giảm ~**50% bitrate**), chất lượng cao, ổn định, xử lý được 3D/4K/8K.
- **Nhược:** thời gian mã hóa x265:x264 = 7:1 (3,5:1); CPU khi phát lại cao hơn (x265 ~15% lõi i7 chỉ-CPU; x264 ~6% có hỗ trợ GPU); ảnh hưởng thời lượng pin thiết bị di động.

### 5.8. Ba cách tải video/âm thanh từ web
| Cách | Cơ chế | Lưu trên máy người dùng? |
|------|--------|--------------------------|
| **Download** (Progressive) | Phải tải **toàn bộ** clip **trước khi phát** | **Có** (lưu vĩnh viễn) |
| **Progressive Download** | Tải xong **một phần** là bắt đầu phát | **Có** (lưu vĩnh viễn) |
| **Streaming** | Gói được gửi xuống và phát **ngay lập tức** | **Không** lưu trên hệ thống người dùng cuối |

---

## NHÌN XUYÊN SUỐT — CÁC Ý TƯỞNG LẶP LẠI TRONG CẢ BÀI

Đây là góc nhìn tổng hợp giúp nhớ nhanh (đều rút từ nội dung bài giảng):

**1. Cùng một bộ công cụ nén dùng cho nhiều loại media:**

| Kỹ thuật | Văn bản | Âm thanh | Ảnh | Video |
|----------|:-------:|:--------:|:---:|:-----:|
| Huffman (entropy) | ✔ | ✔ | ✔ (JPEG) | ✔ (entropy) |
| LZW | ✔ | ✔ | ✔ | |
| Run-Length (RLE) | ✔ | ✔ (FLAC) | ✔ | ✔ (zig-zag) |
| Mã hóa số học | ✔ | ✔ | ✔ | |
| DCT (biến đổi Cosine) | | | ✔ (JPEG) | ✔ (MPEG/H.26x) |
| LPC (dự đoán tuyến tính) | | ✔ | | |

**2. Phân đôi Lossless vs Lossy:** văn bản **luôn lossless** (mất 1 ký tự là đổi nghĩa); audio/ảnh/video chấp nhận **lossy** dựa trên giới hạn cảm nhận của con người (tai ít nhạy dải tần nhất định, mắt ít nhạy tần số cao & sắc độ).

**3. Số hóa = Lấy mẫu + Lượng tử hóa:** lặp lại y hệt ở audio (PCM) và ảnh; đều bị chi phối bởi **định lý Shannon** (fₛ ≥ 2·f_max).

**4. Miền tần số là chìa khóa xử lý & nén:** Fourier (audio) và DCT (ảnh/video) đều chuyển tín hiệu sang miền tần số để "dồn năng lượng" và loại thành phần ít quan trọng.

**5. Chuỗi xử lý chuẩn** `Số hóa → Mã hóa → Truyền/Lưu → Giải mã → Giải số hóa → Đầu ra` áp dụng cho mọi loại dữ liệu.

---

## GỢI Ý CÂU HỎI TỰ KIỂM TRA
1. Phân biệt media tĩnh và media động, cho ví dụ.
2. Viết 2 công thức tỷ lệ nén và tính cho trường hợp 8KB → 2KB.
3. Giải thích ý nghĩa tf, idf và công thức trọng số tf·idf.
4. So sánh mô hình Boolean và mô hình Vector trong tìm kiếm văn bản (ưu/nhược).
5. Nêu 3 bước của PCM; phát biểu định lý Shannon và áp dụng cho tín hiệu thoại 0,3–3,4 kHz.
6. Vì sao JPEG dùng DCT thay vì DFT? Nêu 3 lý do.
7. Phân biệt I-frame, P-frame, B-frame; giải thích cấu trúc GOP "IBBPBBPBBI".
8. Vì sao chuyển RGB → YUV lại giúp nén video? Vai trò của khối macro 16×16.
9. So sánh 3 cách tải media (Download / Progressive / Streaming) về lưu trữ.
