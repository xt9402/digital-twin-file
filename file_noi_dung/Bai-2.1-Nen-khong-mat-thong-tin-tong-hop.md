# Bài 2.1 – Số hóa đa phương tiện
## Các thuật toán mã hóa nén KHÔNG MẤT THÔNG TIN với dữ liệu văn bản
*(Tổng hợp từ slide "Nhập môn Công nghệ Song sinh Thực – Số và Ứng dụng", SOICT – HUST. Toàn bộ số liệu, ví dụ, pseudocode dưới đây đều lấy nguyên từ file PDF; phần "kiểm chứng" chỉ là tính lại trên chính các con số của slide.)*

---

## 0. Bản đồ nội dung

Slide chia thành **4 thuật toán chính** + **2 phép biến đổi hỗ trợ**:

| Nhóm | Tên | Bản chất |
|---|---|---|
| Chính | Runlength Encoding (RLE) | Nén theo **độ dài chuỗi lặp liên tiếp** |
| Chính | Huffman | Nén theo **tần suất ký tự** (mã phi tiền tố, cây nhị phân) |
| Chính | Lempel – Ziv Welch (LZW) | Nén theo **từ điển xâu ↔ token** |
| Chính | Mã hóa nén số học (Arithmetic Coding) | Nén cả chuỗi thành **một số thực trong [0,1)** |
| Hỗ trợ | Move to Front (MTF) | Biến đổi tiền xử lý, tăng hiệu quả entropy coding |
| Hỗ trợ | Burrows-Wheeler Transform (BWT) | Sắp xếp lại thứ tự ký tự để tạo ra các "run" |

> Mạch tư duy xuyên suốt bài: **RLE → Huffman → LZW → Arithmetic** là chuỗi khắc phục nhược điểm của nhau.
> RLE chỉ khai thác được lặp *liên tiếp* → Huffman khai thác *tần suất* nhưng phải gửi bảng mã → LZW *không cần gửi bảng mã* → Arithmetic *không bị giới hạn "mã ngắn nhất là 1 bit"*.

---

## 1. Run Length Encoding (RLE)

### 1.1 Kỹ thuật nén
- Biểu diễn dữ liệu dựa trên **giá trị xuất hiện** và **số lần xuất hiện liên tiếp** của giá trị đó trong văn bản gốc.
- Số lần xuất hiện liên tiếp gọi là **'run length'**.

**Ví dụ nhị phân trên slide:**

```
1110011111  --RLE-->  1 3 0 2 1 5
                      ^ ^ ^ ^ ^ ^
             Values:  1   0   1
        Run Lengths:    3   2   5
```
→ 10 bit gốc được mô tả bằng 3 cặp (giá trị, độ dài).

### 1.2 Ba ví dụ và ba bài học

| Ví dụ | Đầu vào | Đầu ra | Nhận xét của slide |
|---|---|---|---|
| a | `DDDDDCCCCOOODDE` (15 kí tự) | `D5C4O3D2E1` (10 kí tự) | **Nén thành công** |
| b | `11111111111122` | `11222` → phải viết `1 12 2 2` | **Nhập nhằng** |
| c | `123456` (6 kí tự) | `1 1 2 1 3 1 4 1 5 1 6 1` (18 kí tự*) | **Trường hợp tồi** |

**Bài học rút ra:**
1. **Ví dụ a** – Chú ý chữ `D` xuất hiện **2 lần tách rời** (`D5` ở đầu và `D2` ở cuối). RLE mã hóa **chuỗi liên tiếp**, không phải tổng số lần xuất hiện trong toàn văn bản.
2. **Ví dụ b** – `11222` có thể hiểu là "chữ số 1 xuất hiện 1222 lần" hoặc "chữ số 2 xuất hiện 22 lần" ⇒ **bắt buộc phải có dấu phân cách**, và dấu phân cách chính là chi phí phụ trội của RLE.
3. **Ví dụ c** – Khi dữ liệu **không có ký tự lặp liên tiếp**, văn bản mã hóa **lớn hơn cả văn bản gốc**. Đây là điểm yếu chí mạng của RLE.

> *Lưu ý nhỏ để không bị hớ khi thi:* slide ghi kết quả ví dụ c là **18 kí tự (tính cả dấu cách trắng)**. Nếu đếm sát chuỗi `1 1 2 1 3 1 4 1 5 1 6 1` thì có 12 chữ số + 11 dấu cách = 23 kí tự. Con số cụ thể phụ thuộc quy ước dấu phân cách, nhưng **kết luận thì không đổi**: đầu ra > đầu vào.

### 1.3 Ứng dụng / ưu điểm (theo slide)
- Hữu ích khi nén dữ liệu văn bản vì giá trị lặp lại xuất hiện nhiều — **giá trị càng lặp liên tiếp nhiều thì thuật toán càng hiệu quả**.
- **Cài đặt rất đơn giản** so với các phương pháp nén khác.
- Do là nén không mất thông tin nên **dễ dàng giải nén**.

---

## 2. Mã hóa Huffman

### 2.1 Xuất phát điểm: mã hóa độ dài cố định
- Để mã hoá 26 chữ cái tiếng Anh bằng mã nhị phân độ dài cố định: độ dài tối thiểu **[log 26] = 5 bit**.
- Hệ quả: các xâu từ **11011 đến 11111 không được sử dụng** → **lãng phí**.
- **Cây mã hóa độ dài cố định là cây nhị phân đầy đủ**: mọi lá nằm cùng một mức, các nút `*` (dư) không dùng đến.
- Ví dụ giải mã trên cây: `0011101000` → **HI** (tách 5 bit: `00111` = H, `01000` = I).

> *Điểm không nhất quán nội bộ trong slide (nên biết trước khi đi thi):* bảng tra ở slide trước đánh `A = 00001`, còn cây mã hóa lại đánh lá đầu tiên `A = 00000`. Ví dụ giải mã `0011101000 → HI` chỉ đúng theo **cách đánh của cây** (A = 00000). Cứ bám theo cây khi làm bài.

### 2.2 Các khái niệm nền tảng
- **Bài toán:** cho văn bản trên bảng chữ cái `C`, mỗi chữ cái `c ∈ C` có tần suất `f(c)`. Tìm cách mã hoá **dùng ít bộ nhớ nhất**.
- **Tư tưởng Huffman:** tạo cây mã hóa **có thể không phải cây nhị phân đầy đủ**.
- **Mã phi tiền tố (prefix free code):** mã của một ký tự bất kỳ **không là đoạn đầu** của mã của bất kỳ ký tự nào còn lại.
  → Đây là điều kiện để giải mã được **không cần dấu phân cách** (khắc phục đúng nhược điểm của RLE).
- Mỗi mã phi tiền tố ↔ một **cây nhị phân T**: mỗi lá ứng với một chữ cái, mỗi cạnh gán 0 hoặc 1. Mã của chữ cái `c` = dãy bit trên đường đi **từ gốc đến lá** `c`.
- **Ý tưởng tham lam (greedy):** chữ cái **tần suất nhỏ hơn** → gán cho lá **xa gốc hơn**; chữ cái **tần suất lớn hơn** → gán cho nút **gần gốc hơn**.

### 2.3 Thuật toán mã hóa (pseudocode nguyên văn slide)

```
procedure Huffman(C, f);
begin
    n ← |C|;
    Q ← C;
    for i := 1 to n-1 do
    begin
        x, y ← 2 chữ cái có tần suất nhỏ nhất trong Q;   (* Thao tác 1 *)
        Tạo nút p với hai con x, y;
        f(p) := f(x) + f(y);
        Q ← Q \ {x, y} ∪ {p}                              (* Thao tác 2 *)
    end;
end;
```
- **Độ phức tạp: O(n log n)** khi cài đặt bằng **priority queue**.
- Vòng lặp chạy đúng **n-1** lần (mỗi lần gộp 2 nút thành 1, từ n nút còn 1 nút gốc).

### 2.4 Ví dụ đầy đủ trên slide (13 ký tự)

**Bảng tần suất:**

| Char | E | T | A | O | I | N | S | R | H | L | D | C | U |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Freq | 125 | 93 | 80 | 76 | 73 | 71 | 65 | 61 | 55 | 41 | 40 | 31 | 27 |

> *Lưu ý:* slide bảng tần suất đầu tiên ghi **I = 72**, nhưng toàn bộ các slide vẽ cây và bảng mã cuối đều dùng **I = 73**. Cộng lại với I = 73 mới ra đúng **tổng = 838** như slide. ⇒ Dùng **I = 73**.

**Trình tự 12 bước ghép nút (đúng như thứ tự các slide vẽ cây):**

| Bước | Ghép | Kết quả |
|---|---|---|
| 1 | C(31) + U(27) | **58** |
| 2 | D(40) + L(41) | **81** |
| 3 | 58 + H(55) | **113** |
| 4 | R(61) + S(65) | **126** |
| 5 | N(71) + I(73) | **144** |
| 6 | A(80) + O(76) | **156** |
| 7 | 81 + T(93) | **174** |
| 8 | E(125) + 113 | **238** |
| 9 | 126 + 144 | **270** |
| 10 | 156 + 174 | **330** |
| 11 | 238 + 270 | **508** |
| 12 | 330 + 508 | **838 (gốc)** |

**Cấu trúc cây kết quả:**

```
                         838
              0 /                 \ 1
             330                  508
        0 /      \ 1          0 /      \ 1
      156        174        270        238
     0/  \1     0/  \1     0/  \1     0/   \1
     A    O    81    T    126  144    E    113
    (80) (76) 0/ \1 (93) 0/\1 0/\1  (125) 0/  \1
              D   L       R S  N I        58    H
            (40)(41)                    0/ \1  (55)
                                        C   U
                                      (31)(27)
```

**Bảng mã cuối cùng:**

| Char | Freq | Fixed (4 bit) | Huffman |
|---|---|---|---|
| E | 125 | 0000 | **110** |
| T | 93 | 0001 | **011** |
| A | 80 | 0010 | **000** |
| O | 76 | 0011 | **001** |
| I | 73 | 0100 | **1011** |
| N | 71 | 0101 | **1010** |
| S | 65 | 0110 | **1001** |
| R | 61 | 0111 | **1000** |
| H | 55 | 1000 | **1111** |
| L | 41 | 1001 | **0101** |
| D | 40 | 1010 | **0100** |
| C | 31 | 1011 | **11100** |
| U | 27 | 1100 | **11101** |
| **Tổng** | **838** | **3352** | **3036** |

**Kiểm chứng lại bằng chính số liệu của slide:**
- Mã cố định: 13 ký tự cần 4 bit → `838 × 4 = 3352 bit` ✔
- Mã Huffman:
  - Nhóm 3 bit (E, T, A, O): `(125+93+80+76) × 3 = 374 × 3 = 1122`
  - Nhóm 4 bit (I, N, S, R, H, L, D): `(73+71+65+61+55+41+40) × 4 = 406 × 4 = 1624`
  - Nhóm 5 bit (C, U): `(31+27) × 5 = 58 × 5 = 290`
  - **Tổng = 1122 + 1624 + 290 = 3036 bit** ✔
- **Tiết kiệm: 3352 − 3036 = 316 bit ≈ 9,4%.**

> Nhận xét quan trọng: **ký tự tần suất cao nhất chưa chắc có mã ngắn nhất**. Ở đây E (125) có mã 3 bit `110`, còn A (80) cũng 3 bit `000`. Độ dài mã phụ thuộc vào **vị trí lá trên cây**, tức phụ thuộc lịch sử gộp nút, chứ không phải xếp hạng tần suất tuyệt đối.

### 2.5 Thuật toán giải mã (pseudocode nguyên văn slide)

```
procedure Huffman_Decode(B);
(* B là xâu mã hóa văn bản theo mã hóa Huffman *)
begin
    <Khai báo P là gốc của cây mã hóa Huffman>
    While <chưa là bít kết thúc của B> do
    begin
        x ← bit tiếp theo trong xâu B;
        If x = 0 then  P ← Con trái P
        Else           P ← Con phải của P
        If (P là nút lá) then
        begin
            <Hiển thị ký hiệu tương ứng với nút lá>
            <Đặt lại P là gốc của cây mã hóa Huffman>
        end;
    end
end;
```
→ Cơ chế: **duyệt cây từ gốc, gặp lá thì in ra và quay lại gốc**. Đây chính là lý do mã phi tiền tố cho phép giải mã không nhập nhằng.

### 2.6 Ưu / nhược điểm

**Ưu điểm**
- Dựa trên **tần suất xuất hiện** của các ký tự.
- **Giải quyết được sự dư thừa phân bố ký tự**.

**Nhược điểm**
- Đòi hỏi phải **xây dựng cây nhị phân**.
- **Giải nén phức tạp** do không biết trước chiều dài mã cho đến khi ký tự đầu tiên được in ra.
- **Phải gửi cả bảng mã** cho phía nhận mới giải nén được ⇒ hiệu suất nén không cao, **chỉ cao khi độ lớn tập tin lớn**.

---

## 3. Mã hóa Lempel – Ziv Welch (LZW)

### 3.1 Ý tưởng cốt lõi
- Phương pháp nén được phát minh bởi **Lempel - Ziv và Welch**.
- Hoạt động dựa trên ý tưởng rất đơn giản: **người mã hoá và người giải mã cùng xây dựng bảng mã**.
- **Bảng mã KHÔNG cần lưu kèm với dữ liệu nén** — khi giải nén, người giải nén sẽ **tự xây dựng lại** nó.
  → Đây chính là điểm khắc phục nhược điểm lớn nhất của Huffman.

### 3.2 Nguyên tắc hoạt động
- Một **xâu kí tự** là một tập hợp **từ hai kí tự trở lên**.
- Nhớ tất cả các xâu đã gặp và gán cho nó một dấu hiệu riêng, gọi là **token**.
- Lần sau gặp lại xâu đó → **thay thế bằng token** đã gán.
- Cần một **"từ điển"** rất lớn để lưu các xâu đã gặp.
- Khi duyệt dữ liệu, các ký tự được giữ trong **bộ đệm tạm thời (Accumulator)** và so sánh với các xâu trong "từ điển":
  - Xâu trong bộ đệm **chưa có** trong từ điển → **bổ sung vào từ điển + cấp một token**.
  - Xâu trong bộ đệm **đã có** trong từ điển → **token đã gán sẽ được hiển thị ra ngoài**.

### 3.3 Bốn quy tắc (rất hay hỏi thi)

| # | Quy tắc |
|---|---|
| 1 | **256 token đầu tiên từ 0 (000h) đến 0ffh (255) được dành cho các kí tự đơn** |
| 2 | So sánh với "từ điển" **khi trong bộ đệm tạm thời đã có nhiều hơn một kí tự** |
| 3 | Các kí tự ở đầu vào được **bổ sung vào bộ đệm cho đến khi** chuỗi trong bộ đệm **không có** trong "từ điển" |
| 4 | Khi bộ đệm có xâu mà từ điển không có → **cho xâu đó vào từ điển**; **kí tự cuối cùng của xâu được giữ lại trong bộ đệm** để tiếp tục tạo xâu mới |

### 3.4 Ví dụ chuẩn của slide: `!BAN!BA!BAA!BAR!BAT`

Diễn giải từng bước đầu:
- **Bước 1:** `!` vào bộ đệm tạm thời.
- **Bước 2:** thêm `B` → `!B` chưa có trong từ điển → thêm vào từ điển, gán **100h** (vì 000h–0ffh dành riêng cho kí tự đơn — Quy tắc 1). Đưa ra `!`, giữ lại `B`.
- **Bước 3:** thêm `A` → `BA` chưa có → gán **101h**. Đưa ra `B`, giữ `A`.
- **Bước 4:** thêm `N` → `AN` chưa có → gán **102h**. Đưa ra `A`, giữ `N`.
- **Bước 5:** thêm `!` → `N!` chưa có → gán **103h**. Đưa ra `N`, giữ `!`.
- **Bước 6:** thêm `B` → `!B` **đã có** trong từ điển → **không đưa ra kí tự nào**, `!B` tiếp tục ở lại bộ đệm.
- **Bước 7:** thêm `A` → `!BA` chưa có → gán **104h**, đồng thời **token 100h được gửi ra thay cho `!B`** (Quy tắc 4). `A` ở lại bộ đệm.

**Bảng chi tiết 20 bước (đúng như slide):**

| STT | Bộ đệm chứa | Dữ liệu vào (8 bits) | Dữ liệu ra (12 Bits) | Từ điển |
|---|---|---|---|---|
| 1 | - | ! | - | - |
| 2 | ! | B | ! | 100h = !B |
| 3 | B | A | B | 101h = BA |
| 4 | A | N | A | 102h = AN |
| 5 | N | ! | N | 103h = N! |
| 6 | ! | B | - | - |
| 7 | !B | A | **&lt;100h&gt;** | 104h = !BA |
| 8 | A | ! | A | 105h = A! |
| 9 | ! | B | - | - |
| 10 | !B | A | - | - |
| 11 | !BA | A | **&lt;104h&gt;** | 106h = !BAA |
| 12 | A | ! | - | - |
| 13 | A! | B | **&lt;105h&gt;** | 107h = A!B |
| 14 | B | A | - | - |
| 15 | BA | R | **&lt;101h&gt;** | 108h = BAR |
| 16 | R | ! | R | 109h = R! |
| 17 | ! | B | - | - |
| 18 | !B | A | - | - |
| 19 | !BA | T | **&lt;104h&gt;** | 10Ah = !BAT |
| 20 | - | - | T | - |

**Kết luận quan trọng của slide:**
> Việc **giảm kích thước chỉ thực sự bắt đầu tại bước 7**, khi một token (độ lớn khoảng **12 bits**) là `<100h>` được đưa ra thay cho xâu kí tự `!B` (độ lớn khoảng **2 byte = 16 bits**).

### 3.5 Đặc tính vận hành
- **Phần lớn thời gian khi bắt đầu nén chủ yếu mất vào việc tạo "từ điển"** (giai đoạn đầu gần như chưa nén được gì).
- Khi từ điển **đủ lớn**, xác suất gặp lại xâu tăng lên → **càng nén được nhiều hơn**.
- Token biểu diễn bằng số **12 bits** ⇒ từ điển có **4096 token**.
- Tăng số bit biểu diễn token → **hiệu quả nén tốt hơn nhưng tăng dung lượng lưu trữ** (đánh đổi).

### 3.6 Pseudocode

**Nén:**
```
read a character k
w = k;
while (read a character k)
{
    if wk exists in the dictionary
        w = wk;
    else
        add wk to the dictionary;
        if w contains a character
            output character
        else
            output the code for w;
        w = k;
}
```

**Giải nén:**
```
read a character k;
output k;
w = k;
while (read k)          /* k could be a character or a code */
{
    if k is a character or code k exists in the dictionary
        entry = character k or string in dictionary for code k;
        output entry;
        add w + firstCharacterOf(entry) to dictionary;
        w = entry;
    else
        output entry = w + firstCharacterOf(w);
        add entry to dictionary;
        w = entry;
}
```
> Nhánh `else` ở giải nén chính là xử lý trường hợp đặc biệt: bộ giải mã nhận được một code **chưa kịp có** trong từ điển của nó (do bên nén luôn "đi trước" một bước).

### 3.7 Ví dụ thứ hai (dạng bảng w–k)

| w | k | Dictionary | Output |
|---|---|---|---|
| - | b | - | - |
| b | o | bo (256) | 98 (b) |
| o | o | oo (257) | 111 (o) |
| o | o | - | - |
| oo | p | oop (258) | 257 (oo) |
| p | - | - | 112 (p) |

→ Đầu vào `booop`; đầu ra `98, 111, 257, 112`. Bốn ký hiệu đầu ra cho 5 ký tự đầu vào, và token `257` đã thay thế cho 2 ký tự `oo`.

### 3.8 Ưu / nhược điểm

**Ưu điểm**
- **Tỷ lệ nén tương đối cao**, trong tập tin nén **không cần phải chứa bảng mã**.
- Bên nhận **tự xây dựng được bảng mã** mà không cần bên gửi gửi kèm.
- Khắc phục **sự lãng phí bộ nhớ** mà các thuật toán trước không tận dụng hết; khắc phục **sự cứng nhắc**, làm thuật toán nén **linh hoạt hơn**.

**Nhược điểm**
- **Tốn nhiều bộ nhớ**, **khó thực hiện trên các mảng bé hơn 64KB**.

---

## 4. Mã hóa Số học (Arithmetic Coding)

### 4.1 Vấn đề của Huffman (slide "Huffman Coding")
1. Replacing an input symbol with a **codeword** (thay 1 ký hiệu bằng 1 từ mã).
2. **Need a probability distribution**.
3. **Hard to adapt to changing statistics** (khó thích nghi khi thống kê thay đổi).
4. **Need to store the codeword table**.
5. **Minimum codeword length is 1 bit** ← giới hạn cứng: dù ký tự có xác suất 0,99 thì vẫn tốn tối thiểu 1 bit.

### 4.2 Đặc điểm của Arithmetic Coding
- **Replace the entire input with a single floating-point number** (thay toàn bộ đầu vào bằng **một số thực duy nhất**).
- **Does not need the probability distribution.**
- **Adaptive coding is very easy.**
- **No need to keep and send codeword table.**

### 4.3 Xuất phát từ table look-up decoding của Huffman
- N: alphabet size; L: max code word length.
- Chia `[0, 2^L]` thành **N khoảng**, **mỗi khoảng ứng với một ký hiệu**.
- Kích thước khoảng **roughly proportional** (gần đúng tỉ lệ) với xác suất ký hiệu.
- **Arithmetic coding áp dụng ý tưởng này một cách ĐỆ QUY:**
  - Chuẩn hóa dải `[0, 2^L]` về `[0, 1]`.
  - Ánh xạ **cả một chuỗi đầu vào (nhiều ký hiệu)** thành **một tag duy nhất** trong `[0, 1)`.

### 4.4 Phân hoạch khoảng
Với bộ ký hiệu và xác suất: **a (0.8), b (0.02), c (0.18)**
- Phân hoạch **rời nhau (disjoint)** và **đầy đủ (complete)** của `[0, 1)`:
  `[0, 0.8)`, `[0.8, 0.82)`, `[0.82, 1)`
- Mỗi khoảng ứng với một ký hiệu; **kích thước khoảng tỉ lệ với xác suất ký hiệu**.
- **Ký hiệu đầu tiên hạn chế vị trí của tag** vào một trong các khoảng.
- Khoảng thu hẹp lại được **phân hoạch đệ quy** khi xử lý thêm ký hiệu.
- **Observation:** một khi tag đã rơi vào một khoảng, **nó không bao giờ ra khỏi khoảng đó** ⇒ đây là cơ sở để giải mã được.
- **Thứ tự ký hiệu trên trục không quan trọng** (order does not matter), miễn là **bộ giải mã dùng đúng thứ tự đó**.

### 4.5 Ví dụ đầy đủ: mã hóa chuỗi `"1321"`

Bảng ký hiệu: `1 (0.8)`, `2 (0.02)`, `3 (0.18)` → phân hoạch `1: [0, 0.8)`, `2: [0.8, 0.82)`, `3: [0.82, 1)`

**Pseudocode mã hóa:**
```
LOW = 0.0, HIGH = 1.0;
while (not EOF) {
    n = ReadSymbol();
    RANGE = HIGH - LOW;
    HIGH  = LOW + RANGE * CDF(n);
    LOW   = LOW + RANGE * CDF(n-1);
}
output LOW;
```
(với `CDF(0)=0`, `CDF(1)=0.8`, `CDF(2)=0.82`, `CDF(3)=1.0`)

**Bảng truy vết (nguyên văn slide):**

| Input | LOW | HIGH | RANGE |
|---|---|---|---|
| Initial | 0.0 | 1.0 | 1.0 |
| **1** | 0.0 + 1.0×0 = **0.0** | 0.0 + 1.0×0.8 = 0.8 | 0.8 |
| **3** | 0.0 + 0.8×0.82 = **0.656** | 0.0 + 0.8×1 = 0.8 | 0.144 |
| **2** | 0.656 + 0.144×0.8 = **0.7712** | 0.656 + 0.144×0.82 = 0.77408 | 0.00288 |
| **1** | 0.7712 + 0.00288×0 = **0.7712** | 0.7712 + 0.00288×0.8 = 0.773504 | 0.002304 |

→ **Đầu ra: 0.7712** (Termination: encode the lower end or midpoint to signal the end).

**Hai khó khăn (Difficulties) mà slide nêu rõ:**
1. Khoảng co lại liên tục ⇒ đòi hỏi **độ chính xác rất cao (high precision)** với chuỗi dài.
2. **Không sinh ra output nào cho tới khi toàn bộ chuỗi được xử lý xong** (độ trễ / latency).

### 4.6 Giải mã

**Cách 1 – conceptual (nhược điểm):** giải mã trực tiếp trên trục gốc.
> Drawback: **need to recalculate all thresholds each time** (phải tính lại toàn bộ các ngưỡng mỗi lần).

**Cách 2 – chuẩn hóa RANGE về [0,1) mỗi bước** (không cần tính lại ngưỡng):

$$x \leftarrow \frac{x - low}{range}$$

Truy vết với `x = 0.7712`:

| Bước | Phép tính | x | Kết quả |
|---|---|---|---|
| 1 | nhận 0.7712, rơi vào [0, 0.8) | 0.7712 | **Decode 1** |
| 2 | x = (0.7712 − 0) / 0.8 | 0.964 → rơi vào [0.82, 1) | **Decode 3** |
| 3 | x = (0.964 − 0.82) / 0.18 | 0.8 → rơi vào [0.8, 0.82) | **Decode 2** |
| 4 | x = (0.8 − 0.8) / 0.02 | 0 → rơi vào [0, 0.8) | **Decode 1. Stop.** |

→ Khôi phục đúng chuỗi `1321`.

**Pseudocode giải mã:**
```
Low = 0; high = 1;
x = GetEncodedNumber();
While (x ≠ low) {
    n = DecodeOneSymbol(x);
    output symbol n;
    x = (x - CDF(n-1)) / (CDF(n) - CDF(n-1));
};
```
> Slide vẫn cảnh báo: **phương pháp này vẫn cần phép toán dấu phẩy động độ chính xác cao và vẫn phải đọc toàn bộ đầu vào ngay từ đầu quá trình giải mã.**

### 4.7 Ưu / nhược điểm
- **Ưu điểm:** **Tỷ lệ nén tốt hơn** so với nhiều thuật toán mã hóa nén **entropy** khác.
- **Nhược điểm:** **Khó cài đặt.**

---

## 5. Các thuật toán hỗ trợ

Slide nêu rõ: đây là các thuật toán **dùng KÈM** với 4 thuật toán trên **để tăng tỷ lệ nén**, chứ bản thân chúng **không nén** (BWT không làm đổi giá trị ký tự, chỉ đổi thứ tự).

> **Ví dụ tích hợp:** thuật toán nén **Bzip2** hoạt động bằng **hai bước chính: biến đổi Burrows-Wheeler Transform + mã hóa Huffman**.

### 5.1 Move to Front (MTF) Transform

**Định nghĩa:** MTF là một phép mã hóa dữ liệu (thường là **dòng byte**) được thiết kế để **cải thiện hiệu năng của entropy encoding** — trong đó entropy encoding là sơ đồ mã hóa gán mã cho ký hiệu sao cho **độ dài mã khớp với xác suất của ký hiệu**. Khi cài đặt đúng, nó **đủ nhanh để lợi ích của nó biện minh cho việc thêm một bước phụ** vào thuật toán nén.

**Cơ chế:**
1. Mỗi giá trị byte được mã hóa bằng **chỉ số (index) của nó trong một danh sách** — danh sách này **thay đổi trong quá trình chạy**.
2. Danh sách khởi tạo theo thứ tự giá trị byte `(0, 1, 2, 3, ..., 255)` ⇒ **byte đầu tiên luôn được mã hóa bằng chính giá trị của nó**.
3. Sau khi mã hóa một byte, **giá trị đó được chuyển lên đầu danh sách** trước khi xử lý byte tiếp theo.

**Ví dụ `bananaaa` (slide):**

| Iteration | Sequence | List |
|---|---|---|
| **b**ananaaa | 1 | (abcdefghijklmnopqrstuvwxyz) |
| b**a**nanaaa | 1,1 | (bacdefghijklmnopqrstuvwxyz) |
| ba**n**anaaa | 1,1,13 | (abcdefghijklmnopqrstuvwxyz) |
| ban**a**naaa | 1,1,13,1 | (nabcdefghijklmopqrstuvwxyz) |
| bana**n**aaa | 1,1,13,1,1 | (anbcdefghijklmopqrstuvwxyz) |
| banan**a**aa | 1,1,13,1,1,1 | (nabcdefghijklmopqrstuvwxyz) |
| banana**a**a | 1,1,13,1,1,1,0 | (anbcdefghijklmopqrstuvwxyz) |
| bananaa**a** | 1,1,13,1,1,1,0,0 | (anbcdefghijklmopqrstuvwxyz) |
| **Final** | **1,1,13,1,1,1,0,0** | (anbcdefghijklmopqrstuvwxyz) |

**Ý nghĩa:** một chuỗi có nhiều ký tự lặp/gần nhau sẽ biến thành một dãy **toàn số nhỏ (0, 1, ...)** ⇒ phân bố lệch mạnh ⇒ Huffman/Arithmetic nén rất tốt. Chuỗi `aaa` cuối biến thành `1,0,0`.

### 5.2 Burrows-Wheeler Transform (BWT)

**Giới thiệu:**
- BWT (a.k.a. **block-sorting compression**) là một trong những phương pháp **phổ biến nhất** trong nén dữ liệu.
- Phát minh bởi **Michael Burrows và David Wheeler**, vào **những năm 90**.
- Khi một xâu ký tự được biến đổi bởi BWT, **không ký tự nào bị đổi giá trị** — phép biến đổi chỉ **sắp xếp lại thứ tự các ký tự** một cách khéo léo.
- Nếu xâu gốc có **nhiều xâu con xuất hiện thường xuyên**, thì xâu sau biến đổi sẽ có **nhiều vị trí mà một ký tự lặp lại nhiều lần liên tiếp**.
- Điều này **hữu ích cho việc nén**, vì xâu có các "run" ký tự lặp thì **dễ nén bằng move-to-front transform và run-length encoding**.

**Định nghĩa phép quay vòng (cyclic rotation):**
> Với `0 ≤ k ≤ n-1`, phép quay vòng thứ `k` của xâu `w = w[0..n-1]` là xâu `v = v[0..n-1]` sao cho **`v[i] = w[(i+k) mod n]`**.

**Thuật toán BWT (4 bước):**
1. Tạo **ma trận vuông M[n×n]**, trong đó **hàng thứ `k` chứa phép quay vòng thứ `k` của `w`**.
2. **Sắp xếp các hàng của M theo thứ tự từ điển (lexicographic order)**.
3. **Lưu xâu là cột cuối cùng của M**.
4. **Và chỉ số của hàng chứa vị trí xâu gốc `w`** (tức phép quay vòng thứ 0).

**Ví dụ trên slide: từ Fibonacci thứ 5, `f5 = babbabab` (n = 8)**

| # | Ma trận M (chưa sắp xếp) | | # sau sort | Ma trận M (đã sắp xếp) | Chỉ số gốc |
|---|---|---|---|---|---|
| [0] | b a b b a b a b | | [0] | a b a b b a b **b** | [4] |
| [1] | a b b a b a b b | | [1] | a b b a b a b **b** | [1] |
| [2] | b b a b a b b a | | [2] | a b b a b b a **b** | [6] |
| [3] | b a b a b b a b | | [3] | b a b a b b a **b** | [3] |
| [4] | a b a b b a b b | | [4] | b a b b a b a **b** | **[0]** ← hàng chứa xâu gốc |
| [5] | b a b b a b b a | | [5] | b a b b a b b **a** | [5] |
| [6] | a b b a b b a b | | [6] | b b a b a b b **a** | [2] |
| [7] | b b a b b a b a | | [7] | b b a b b a b **a** | [7] |

→ **Đầu ra: xâu `bbbbbaaa` và vị trí `[4]`.**

Quan sát: xâu gốc `babbabab` xen kẽ lộn xộn, sau BWT thành `bbbbbaaa` — **gom thành 2 run duy nhất**, cực kỳ thuận lợi cho RLE/MTF ở bước sau.

**Tính chất:**
- BWT **có thể tính bằng thuật toán xây dựng suffix array** ⇒ **tính được trong thời gian tuyến tính (linear time)** (không cần thực sự dựng ma trận n×n).
- BWT **khả nghịch (reversible)**; xâu gốc được khôi phục hiệu quả **thông qua việc sinh lần lượt các cột của ma trận M**.

**Hai cách nghịch đảo BWT:**
- **Cách khó (hard way):** sinh dần từng cột của ma trận M — cột 1, cột 2, ... cho tới khi có đủ n cột, rồi lấy hàng tại chỉ số đã lưu. Tốn bộ nhớ và thời gian.
- **Cách dễ (easy way), dựa trên tính chất sắp xếp ổn định (stable sorting property):**
  1. Cột BWT (cột cuối) đặt cạnh **cột đầu tiên** (chính là cột BWT đã sắp xếp).
  2. Ghép cặp tương ứng giữa hai cột → tạo thành một **hoán vị (cycle)**.
  3. **Chỉ việc đi theo chu trình (just follow the cycle)** từ vị trí đã lưu.
  - Trong ví dụ: chu trình cho ra thứ tự chỉ số `4, 1, 6, 3, 0, 5, 2, 7`, đọc ra chính là `b a b b a b a b` (vị trí 0 1 2 3 4 5 6 7) = xâu gốc.

---

## 6. Bảng so sánh tổng hợp 4 thuật toán chính

| Tiêu chí | RLE | Huffman | LZW | Arithmetic |
|---|---|---|---|---|
| **Khai thác dư thừa dạng nào** | Lặp **liên tiếp** | **Tần suất** ký tự | **Xâu lặp lại** (bất kỳ vị trí) | **Xác suất** cả chuỗi |
| **Đơn vị đầu ra** | Cặp (giá trị, run length) | Từ mã bit/ký tự | Token 12 bit | **1 số thực** cho cả chuỗi |
| **Phải gửi bảng mã?** | Không | **Có** (nhược điểm lớn) | **Không** (2 bên tự dựng) | **Không** |
| **Cấu trúc dữ liệu chính** | – | Cây nhị phân + priority queue | Từ điển 4096 token | Khoảng `[LOW, HIGH)` |
| **Độ phức tạp cài đặt** | Rất đơn giản | Trung bình | Trung bình | **Khó cài đặt** |
| **Nhược điểm chí mạng** | Có thể **phình to hơn gốc** | Gửi bảng mã; giải nén phức tạp | **Tốn bộ nhớ** (< 64KB khó chạy) | Cần **độ chính xác cao**; **trễ** (chỉ xuất khi xong chuỗi) |
| **Hiệu quả cao khi** | Dữ liệu nhiều run | **File lớn** | Từ điển đã đủ lớn | Hầu hết trường hợp entropy |

---

## 7. Checklist ôn tập (tự kiểm tra)

**RLE**
- [ ] Mã hóa `1110011111` và giải thích đâu là Values, đâu là Run Lengths.
- [ ] Vì sao `11111111111122` phải viết là `1 12 2 2`?
- [ ] Cho một ví dụ mà RLE làm dữ liệu **to ra**.

**Huffman**
- [ ] Vì sao mã cố định cho 26 chữ cái cần 5 bit và xâu nào bị lãng phí?
- [ ] Định nghĩa **mã phi tiền tố** và giải thích vì sao nó cho phép giải mã không nhập nhằng.
- [ ] Dựng lại cây Huffman cho bảng 13 ký tự (E 125 … U 27) theo đúng 12 bước gộp.
- [ ] Tính lại `3352` và `3036` bit, giải thích 316 bit tiết kiệm đến từ đâu.
- [ ] Độ phức tạp `O(n log n)` đến từ cấu trúc nào?

**LZW**
- [ ] Phát biểu **4 quy tắc**.
- [ ] Chạy tay `!BAN!BA!BAA!BAR!BAT` ít nhất tới bước 11.
- [ ] Giải thích vì sao **bước 7** mới bắt đầu thực sự nén.
- [ ] Vì sao token 12 bit ⇒ từ điển 4096 entry, và đánh đổi khi tăng số bit là gì?

**Arithmetic**
- [ ] Liệt kê 5 vấn đề của Huffman mà arithmetic coding nhắm tới.
- [ ] Chạy tay bảng LOW/HIGH/RANGE cho `"1321"` ra `0.7712`.
- [ ] Giải mã ngược `0.7712` bằng công thức `x ← (x − low)/range`.
- [ ] Nêu 2 difficulties.

**MTF & BWT**
- [ ] Chạy MTF cho `bananaaa` ra `1,1,13,1,1,1,0,0`.
- [ ] Viết công thức phép quay vòng `v[i] = w[(i+k) mod n]`.
- [ ] Chạy BWT cho `babbabab` ra `bbbbbaaa` + vị trí `[4]`.
- [ ] Vì sao BWT tính được trong thời gian tuyến tính? Vì sao nó khả nghịch?
- [ ] Bzip2 gồm hai bước nào?

---

## 8. Ba câu "chốt" nếu chỉ được nhớ ba điều

1. **RLE, Huffman, LZW, Arithmetic là bốn cách khác nhau để khai thác cùng một thứ: sự dư thừa (redundancy) trong dữ liệu** — lần lượt là dư thừa *lặp liên tiếp*, dư thừa *phân bố tần suất*, dư thừa *xâu lặp*, và dư thừa *xác suất trên toàn chuỗi*.
2. **Bài toán "có phải gửi bảng mã hay không" là trục tiến hóa chính:** Huffman phải gửi ⇒ chỉ hiệu quả với file lớn; LZW và Arithmetic không phải gửi ⇒ đó là bước tiến quan trọng nhất.
3. **MTF và BWT không nén — chúng "dọn dẹp" dữ liệu để thuật toán nén phía sau làm việc hiệu quả hơn.** Bzip2 = BWT + Huffman là minh chứng cho kiến trúc pipeline này.
