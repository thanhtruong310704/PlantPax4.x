# CHƯƠNG 5: CÁC KHỐI CHỨC NĂNG DÙNG CHUNG, LIÊN ĐỘNG & CẢNH BÁO (CROSS FUNCTIONAL — TRANG 331–396)

## 1. Bảng phân loại toàn bộ 10 khối lệnh trong Chương 5 (Purpose & Table 19 - Trang 331–335)

| STT | Khối lệnh AOI | Tệp Controller (`.L5X`) | Vai trò kỹ thuật & Phạm vi ứng dụng |
| :--- | :--- | :--- | :--- |
| 1 | **`P_Gate`** | `P_Gate_4.10.00_AOI.L5X` | **Condition Gate Delay:** Khối cổng điều kiện kèm bộ định thời trễ bật (On-Delay) và trễ tắt (Off-Delay) cho tín hiệu số, nhúng bên trong các khối đầu vào và PID hoặc dùng độc lập. |
| 2 | **`P_Intlk`** | `P_Intlk_4.10.00_AOI.L5X` | **Interlocks with First Out and Bypass:** Gom tối đa **16 điều kiện liên động an toàn** (hoặc nối tầng nhiều khối) để dừng thiết bị đang chạy và cấm khởi động, có bắt lỗi đầu tiên (First Out) và Bypass bảo trì. |
| 3 | **`P_IntlkAdv`** | `P_IntlkAdv_4.10.00_AOI.L5X` | **Interlocks Advanced:** Mở rộng từ `P_Intlk` với phân nhóm cấp độ ưu tiên dừng (Stop Priority / Shed levels), cho phép điều hướng liên động riêng và điều khiển trạng thái an toàn linh hoạt hơn. |
| 4 | **`P_Perm`** | `P_Perm_4.10.00_AOI.L5X` | **Permissives with Bypass:** Gom tối đa **16 điều kiện cho phép khởi động** (chỉ kiểm tra trước khi thiết bị chạy; khi thiết bị đã chạy thì bỏ qua), có hỗ trợ Bypass bảo trì. |
| 5 | **`P_D4SD`** | `P_D4SD_4.10.00_AOI.L5X` | **Discrete 2-, 3-, or 4-state Device:** Điều khiển và giám sát thiết bị có 2, 3 hoặc 4 trạng thái hoạt động rời rạc với tối đa 4 ngõ ra và 4 ngõ vào phản hồi cấu hình theo bảng chân trị. |
| 6 | **`P_Reset`** | `P_Reset_4.10.00_AOI.L5X` | **Central Reset:** Điểm xóa lỗi tập trung cho cả một cụm thiết bị (Unit / Equipment Module), gom tín hiệu `Sts_RdyToReset` từ các thiết bị con và phát xung Reset đủ độ rộng thời gian tối thiểu. |
| 7 | **`P_Alarm`** | `P_Alarm_4.10.00_AOI.L5X` | **Common Alarm Block:** Khối quản lý vòng đời cảnh báo chuẩn ANSI/ISA-18.2 (Acknowledge, Reset, Shelve, Disable, Suppress), dùng độc lập hoặc nhúng trong mọi khối PlantPAx. |
| 8 | **`P_CmdSrc`** | `P_CmdSrc_4.10.00_AOI.L5X` | **Command Source (Common Mode Block):** Quản lý phân quyền điều khiển giữa 7 nguồn lệnh (Operator, Program, External, Override, Maintenance, Hand, Out of Service). |
| 9 | **`P_Prompt`** | `P_Prompt_4.10.00_AOI.L5X` | **Operator Prompt:** Hiển thị thông báo tương tác trên HMI yêu cầu người vận hành xác nhận, chọn phương án hoặc nhập số liệu/văn bản trả về cho trình tự điều khiển (`P_Seq`). |
| 10 | **`P_Logic`** | `P_Logic_4.10.00_AOI.L5X` | **Boolean Logic with Snapshot:** Khối mạch tổ hợp gồm **8 cổng logic khả trình** (AND, OR, XOR, Set/Reset, Select, Majority) cấu hình trực tiếp từ HMI kèm tính năng chụp băng hình trạng thái (Snapshot) lúc tác động. |

---

## 2. Chi tiết từng khối lệnh AOI trong Chương 5

### 2.1. Condition Gate Delay (`P_Gate` — Trang 336–339)
* **Mô tả chức năng:**
  * Nhận tín hiệu đầu vào điều kiện `Inp` và tín hiệu cổng cho phép `Inp_Gate` (kèm thời gian trễ mở cổng `Cfg_GateDelayT`).
  * Khi cổng `Gate` mở (True): Tín hiệu `Inp` đi qua bộ lọc trễ bật (`Cfg_OnDelayT`) và trễ tắt (`Cfg_OffDelayT`) trước khi xuất ra `Out`.
  * Khi cổng `Gate` đóng (False): Ngõ ra `Out` lập tức bị giữ ở mức 0 (Off), dùng để khóa không cho báo động giả trong giai đoạn khởi động máy (Startup suppression).

### 2.2. Interlocks with First Out and Bypass (`P_Intlk` — Trang 340–345) & Advanced (`P_IntlkAdv` — Trang 346–352)
* **Nguyên lý vận hành cốt lõi (Trang 341–342):**
  1. **Kiểm tra trạng thái an toàn (`Interlock Input OK Check`):** So sánh từng đầu vào `Inp_Intlk00` ... `Inp_Intlk15` với trạng thái an toàn cấu hình `Cfg_OKState.x` (có thể cấu hình mức 1 là an toàn hoặc mức 0 là an toàn — De-energize to trip).
  2. **Chốt giữ trạng thái lỗi (`Interlock Condition Latching`):** Nếu `Cfg_Latched.x = 1`, một khi điều kiện liên động vi phạm, nó sẽ tự khóa chốt (Latch) kể cả khi tín hiệu vật lý đã trở lại bình thường, bắt buộc phải nhấn Reset mới xóa được (trừ khi chân `Inp_LatchDefeat = 1`).
  3. **Cơ chế Bypass bảo trì 3 lớp an toàn (`Interlock Bypass`):** Một đầu vào liên động chỉ được bỏ qua khi thỏa mãn đồng thời cả 3 điều kiện:
     * Kỹ sư thiết kế cho phép phép bypass (`Cfg_Bypassable.x = 1`).
     * Nhân viên bảo trì kích hoạt bypass cho kênh đó (`MSet_Bypass.x = 1`).
     * Khối thiết bị chấp hành phía sau (ví dụ `P_Motor`) đang bật cho phép nhận quyền bypass (`Sts_BypActive = 1` nối ngược về chân `Inp_BypActive` của `P_Intlk`).
  4. **Bắt lỗi đầu tiên (`First Out`):** Khi thiết bị đang chạy bình thường mà bị ngắt liên động, khối tự động đánh dấu điều kiện vi phạm **đầu tiên** gây ra cú ngắt dây chuyền để hiển thị nổi bật trên HMI, giúp chẩn đoán sự cố tức thì.
  5. **Hai ngõ ra tổng hợp quan trọng:**
     * `Sts_IntlkOK`: Bằng 1 khi tất cả các điều kiện liên động đều tốt (bất kể có đang bật Bypass hay không).
     * `Sts_NBIntlkOK`: Bằng 1 khi mọi liên động không thể bypass đều tốt và các liên động cho phép bypass đã tốt hoặc đã được bypass.
* **Quy tắc vàng từ Rockwell Automation (Trang 343–344):**
  * **CẢNH BÁO:** Luôn sử dụng các bit **Trạng thái (`Sts_*`)** từ khối `P_AIn`/`P_DIn` làm đầu vào cho `P_Intlk`, **KHÔNG** dùng bit Cảnh báo (`Alm_*`) trừ khi bạn cố tình muốn liên động bị vô hiệu hóa khi người vận hành nhấn Shelve/Disable cảnh báo!
  * **Quy ước đặt tên tự động liên kết HMI:** Đặt tên thẻ của khối `P_Intlk` theo công thức `[Tên_Thẻ_Thiết_Bị]_Intlk` (ví dụ động cơ là `M101` thì khối liên động đặt là `M101_Intlk`) để nút nhấn Interlock trên Faceplate động cơ tự động mở đúng cửa sổ liên động.
  * Khi nối dây phản hồi từ chân `Sts_BypActive` của `P_Motor` quay ngược lại chân `Inp_BypActive` của `P_Intlk` trên sơ đồ FBD, bắt buộc đánh dấu đường dây đó là **`Assume Data Available`** (Giả định dữ liệu sẵn sàng) để trình biên dịch không báo lỗi vòng lặp đệ quy.

### 2.3. Permissives with Bypass (`P_Perm` — Trang 353–358)
* **Mô tả chức năng:** Gom tối đa **16 điều kiện cho phép khởi động** (`Inp_Perm00` ... `Inp_Perm15`).
  * Khác với `P_Intlk`, khối `P_Perm` **chỉ kiểm tra trước khi thiết bị khởi động**. Khi thiết bị đã chạy ổn định, việc mất điều kiện Permissive không làm dừng thiết bị.
  * Quy ước đặt tên thẻ chuẩn để tự động điều hướng HMI: `[Tên_Thẻ_Thiết_Bị]_Perm`.

### 2.4. Discrete 2-, 3-, or 4-state Device (`P_D4SD` — Trang 359–366)
* **Mô tả chức năng:** Điều khiển thiết bị đa trạng thái phức tạp (tối đa 4 trạng thái chọn từ HMI/Program, điều khiển 4 ngõ ra số `Out_0..3` và kiểm tra 4 ngõ vào phản hồi `Inp_Fdbk0..3`).
* Mỗi ngõ ra ở từng trạng thái có thể cấu hình là: Tắt (0), Bật duy trì (1), Xung bật (Pulse On) hoặc Xung tắt (Pulse Off).

### 2.5. Central Reset (`P_Reset` — Trang 367–370)
* **Mô tả chức năng:** Tạo nút nhấn Reset tập trung cho toàn bộ một cụm công nghệ (Unit / Area).
  * Gom các chân `Sts_RdyToReset` của nhiều thiết bị về chân `Inp_ResetReqd` để làm sáng đèn báo có thiết bị đang chờ xóa lỗi.
  * Khi nhận lệnh Reset (từ HMI `OCmd_Reset`, từ chương trình `PCmd_Reset` hoặc nút nhấn vật lý `Inp_Reset`), khối phát ra xung `Out_Reset` được giữ ở mức 1 trong khoảng thời gian tối thiểu cấu hình (`Cfg_MinResetT`, mặc định vài giây) để đảm bảo mọi thiết bị mạng chậm (như biến tần, rơ-le quá tải) đều kịp nhận lệnh xóa lỗi.

### 2.6. Common Alarm Block (`P_Alarm` — Trang 371–377)
* **Mô tả chức năng:** Khối chuẩn hóa quản lý cảnh báo theo tiêu chuẩn quốc tế **ANSI/ISA-18.2**:
  * Quản lý đầy đủ sơ đồ trạng thái: **Normal** -> **Unacknowledged Alarm** -> **Acknowledged Alarm** -> **RTN (Return to Normal) Unacknowledged** -> **Reset / Normal**.
  * Hỗ trợ phân mức độ nghiêm trọng (**Severity**: Low, Medium, High, Urgent) để điều khiển màu sắc và âm thanh còi báo trên FactoryTalk View.
  * Hỗ trợ **Alarm Shelving** (Tạm hoãn cảnh báo có thời hạn), **Out-of-Service Disable** (Vô hiệu hóa cảnh báo bảo trì) và **Program Suppression** (Ức chế cảnh báo bằng logic chương trình).

### 2.7. Command Source (`P_CmdSrc` — Trang 378–388)
* **Mô tả chức năng:** Trái tim phân quyền điều khiển của mọi khối đối tượng PlantPAx.
  * Quản lý thứ tự ưu tiên nghiêm ngặt từ cao xuống thấp:
    1. **Out of Service (OoS — Đưa ra khỏi vận hành)** — Ưu tiên cao nhất.
    2. **Hand (Điều khiển cứng tại tủ điện hiện trường)**.
    3. **Maintenance (Bảo trì trên HMI — có quyền vượt qua Bypassable Interlocks)**.
    4. **Override (Cưỡng bức an toàn bằng logic PLC)**.
    5. **External (Điều khiển từ hệ thống ngoài)**.
    6. **Program (Điều khiển tự động bởi chương trình PLC)**.
    7. **Operator (Điều khiển bởi người vận hành trên màn hình SCADA)** — Ưu tiên cơ sở.
  * Hiển thị biểu tượng hình tam giác nhỏ ở góc trên bên trái trên HMI để báo đâu là nguồn lệnh "Bình thường" (`Normal Command Source` — thông thường là Program hoặc Operator).

### 2.8. Operator Prompt (`P_Prompt` — Trang 389–390)
* **Mô tả chức năng:** Cơ chế tương tác hai chiều với người vận hành trong các quy trình tự động hoặc bán tự động:
  * Hiển thị thông điệp nhắc nhở, bảng thông số cần kiểm tra xác nhận, danh sách lựa chọn (Selection list), hoặc ô nhập giá trị số/chuỗi ký tự (lưu vào thẻ `RespData`).
  * **Lưu ý chuẩn ISA-18.2 (Trang 389):** Không bao giờ dùng `P_Prompt` để thay thế cho Cảnh báo (`P_Alarm`). Cảnh báo dùng cho tình huống bất thường; `P_Prompt` dùng cho bước thao tác vận hành bình thường trong quy trình.

### 2.9. Boolean Logic with Snapshot (`P_Logic` — Trang 391–396)
* **Mô tả chức năng:** Cho phép kỹ sư cấu hình hoặc thay đổi mạch logic tổ hợp ngay trên màn hình HMI (Engineering Tab) mà không cần sửa code PLC hay Download lại dự án!
  * Bao gồm **8 đầu vào Boolean** (`Inp_0` ... `Inp_7`) và **8 cổng logic nối tầng** (`Gate 0` ... `Gate 7`).
  * Mỗi cổng có thể chọn 1 trong 6 kiểu hàm: **`AND`**, **`OR`**, **`XOR` (Exclusive-OR)**, **`Set/Reset` (Flip-Flop ưu tiên Set hoặc Reset)**, **`Select` (Multiplexer 2-to-1)**, hoặc **`Majority` (Biểu quyết đa số 2-trên-3 / 3-trên-4)**.
  * Từng chân đầu vào của mỗi cổng đều có bit đảo trạng thái (`Invert` — do đó không cần cổng NOT riêng).
  * **Tính năng Chụp băng hình trạng thái (`Snapshot`):** Tự động ghi lại toàn bộ trạng thái tức thời của 8 đầu vào và 8 cổng logic kèm nhãn thời gian (`Timestamp`) ngay tại khoảnh khắc ngõ ra đổi trạng thái, giúp phân tích sự cố chập chờn diễn ra trong vài mili-giây mà mắt thường trên HMI không kịp nhìn thấy!

---
# PHẦN TRA CỨU CHI TIẾT TOÀN BỘ BIẾN (INPUT, OUTPUT, LOCAL TAGS), CÁCH SỬ DỤNG & CẤU HÌNH TỪNG KHỐI AOI

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_Alarm`
**Thuộc chương:** [Chương 5: Khối chức năng dùng chung, Liên động & Cảnh báo (Cross Functional)](../05_CHUONG_5_CROSS_FUNCTIONAL.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Cross Functional 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 371–377)  
**Tệp định nghĩa khối lệnh:** `P_Alarm_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_Alarm`:** **16** Biến đầu vào (`Input Parameters`) | **16** Biến đầu ra (`Output Parameters`) | **20** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_Alarm` trong Studio 5000
- **Chức năng cốt lõi:** Khối **Quản lý Cảnh báo Chuẩn (`Common Alarm Block`)** tuân thủ tiêu chuẩn ISA-18.2. Quản lý trạng thái Ack, Reset, Shelve, Suppress, Disable và mức độ nghiêm trọng (`Severity`) để hiển thị đồng bộ trên hệ thống cảnh báo HMI.
- **Khi nào CẦN sử dụng:** Khối này đã được nhúng sẵn trong hầu hết các khối PlantPAx. Bạn lấy khối `P_Alarm` rời ra dùng khi tự viết một **thuật toán hoặc điều kiện cảnh báo tùy chỉnh riêng** trong PLC và muốn cảnh báo đó hiển thị trên thanh Alarm Banner HMI chuẩn của PlantPAx.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi cảnh báo đã có sẵn trong `P_AIn`, `P_DIn`, `P_Motor`, `P_Valve*`.

### Quy trình 4 bước đấu nối và cấu hình `P_Alarm`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_Exists`, `Cfg_ResetReqd`, `Cfg_AckReqd`, `Cfg_AllowShelve`, `Cfg_AllowDisable`, `Cfg_Severity`, `Cfg_MaxShelfT`, `Cfg_AlmMinOnT`
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Cond`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_Alarm` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_Alarm` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PCmd_Reset`, `PCmd_Ack`, `PCmd_Suppress`, `PCmd_Unshelve`
   - **Cách sử dụng trong code PLC:** Gán giá trị đặt vào `PSet_*` (nếu có) hoặc kích xung 1 chu kỳ vào các bit lệnh `PCmd_* = 1` để xóa lỗi (`PCmd_Reset`), xác nhận cảnh báo (`PCmd_*Ack`) hoặc ức chế cảnh báo (`PCmd_*Suppress`). Khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi.
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Val_Notify`, `Val_SecToUnshelve`, `Val_MinToUnshelve`, `Sts_AlmInh`, `Sts_RdyReset`, `Sts_RdyAck`, `Sts_Err`
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 16 Biến Đầu Vào (`Input Parameters`) của `P_Alarm`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu điều kiện đầu vào (1 = Kích hoạt điều kiện / Báo động, 0 = Bình thường) |
| **`Inp_Reset`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Cfg_Exists`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cảnh báo được cấu hình tồn tại và hoạt động; 0 = Cảnh báo bị tắt không quét kiểm tra |
| **`Cfg_ResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự xóa khi về bình thường |
| **`Cfg_AckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Bắt buộc phải nhấn Xác nhận (Acknowledge) khi cảnh báo xuất hiện; 0 = Cảnh báo tự động được xác nhận |
| **`Cfg_AllowShelve`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép người vận hành Tạm hoãn (Shelve) cảnh báo này trên HMI; 0 = Cấm Shelve |
| **`Cfg_AllowDisable`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép nhân viên bảo trì Vô hiệu hóa (Disable) cảnh báo này trên HMI; 0 = Cấm Disable |
| **`Cfg_Severity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_MaxShelfT`** | `DINT` | `480` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian cho phép Tạm hoãn (Shelve) cảnh báo tối đa trước khi tự động gỡ bỏ tạm hoãn (phút) |
| **`Cfg_AlmMinOnT`** | `DINT` | `5` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian duy trì mức BẬT tối thiểu của cảnh báo sau khi kích hoạt để đảm bảo HMI/Historian kịp ghi nhận (giây) |
| **`PCfg_AllowExist`** | `BOOL` | `1` | Ẩn (Tag) | 1 = Cấu hình cho phép cảnh báo này tồn tại trên HMI; 0 = Các thông số cấu hình khác làm cảnh báo này không cần thiết và bị ẩn đi |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`PCmd_Ack`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo hiện tại |
| **`PCmd_Suppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo từ chương trình |
| **`PCmd_Unshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo ngay lập tức |

## 3. Bảng chi tiết 16 Biến Đầu Ra (`Output Parameters`) của `P_Alarm`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Val_Notify`** | `SINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo hiện hành và trạng thái xác nhận của khối này (0 = Không có cảnh báo, 1 = Đã hết lỗi nhưng chờ Reset/Ack, 2..3 = Thấp, 4..5 = Trung bình, 6..7 = Cao, 8..9 = Khẩn cấp) |
| **`Val_SecToUnshelve`** | `SINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Số giây lẻ còn lại cho đến khi cảnh báo tự động gỡ bỏ trạng thái tạm hoãn (Auto-Unshelve, định dạng mmm:ss) |
| **`Val_MinToUnshelve`** | `INT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Số phút còn lại cho đến khi cảnh báo tự động gỡ bỏ trạng thái tạm hoãn (Auto-Unshelve, định dạng mmm:ss) |
| **`Alm`** | `BOOL` | `0` | Ẩn (Tag) | Cờ báo trạng thái báo động hiện hành: 1 = Đang có báo động (In Alarm); 0 = Bình thường |
| **`Ack`** | `BOOL` | `1` | Ẩn (Tag) | Cờ báo trạng thái xác nhận báo động: 1 = Báo động đã được Xác nhận (Acknowledged); 0 = Báo động mới chưa được xác nhận |
| **`Shelved`** | `BOOL` | `0` | Ẩn (Tag) | 1 = Cảnh báo đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Suppressed`** | `BOOL` | `0` | Ẩn (Tag) | 1 = Cảnh báo đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Disabled`** | `BOOL` | `0` | Ẩn (Tag) | 1 = Cảnh báo đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_AlmInh`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang có ít nhất một cảnh báo bị Tạm hoãn (Shelved), Vô hiệu hóa (Disabled) hoặc Ức chế (Suppressed) — hiển thị biểu tượng chữ "I" trên HMI |
| **`Sts_RdyReset`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc chốt ngắt bảo vệ (Shed) đã hết lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị đặt bộ định thời gian (Timer Preset) nằm ngoài phạm vi hợp lệ 0..2.147.483 giây |
| **`Err_Severity`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Mức độ nghiêm trọng của cảnh báo (Cfg_Severity) nằm ngoài phạm vi 1..1000 |
| **`P_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_Alarm) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 20 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_Alarm`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Cond`** | `STRING_20` | `'Alarm Condition'` | [Biến cấu hình thông số] Chuỗi văn bản mô tả nội dung điều kiện cảnh báo hiển thị trên HMI (ví dụ: "High-High PV") |
| **`Cfg_Tag`** | `STRING_20` | `'P_Alarm'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`Com_AE`** | `INT` | `2#0000_0000_0000_0010` | Từ giao tiếp nội bộ với máy chủ Cảnh báo và Sự kiện (FactoryTalk Alarms and Events Server) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_Alarm'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`MCmd_Disable`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Vô hiệu hóa (Disable) cảnh báo không cho phát sinh báo động mới |
| **`MCmd_Enable`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Kích hoạt (Enable) cho phép cảnh báo hoạt động trở lại |
| **`MCmd_Test`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Kích thử nghiệm (Test) cảnh báo trong thời gian duy trì tối thiểu |
| **`MRdy_Disable`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Disable (Vô hiệu hóa (Disable) cảnh báo không cho phát sinh báo động mới), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_Enable`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Enable (Kích hoạt (Enable) cho phép cảnh báo hoạt động trở lại), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_Test`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Test (Kích thử nghiệm (Test) cảnh báo trong thời gian duy trì tối thiểu), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`OCmd_Ack`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xác nhận (Acknowledge) cảnh báo hiện tại |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`OCmd_Shelve`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Tạm hoãn (Shelve) cảnh báo trong thời gian cấu hình |
| **`OCmd_Unshelve`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo ngay lập tức |
| **`ORdy_Ack`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Ack (Xác nhận (Acknowledge) cảnh báo hiện tại), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Shelve`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Shelve (Tạm hoãn (Shelve) cảnh báo trong thời gian cấu hình), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Unshelve`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unshelve (Gỡ bỏ tạm hoãn (Unshelve) cảnh báo ngay lập tức), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_Gate`
**Thuộc chương:** [Chương 5: Khối chức năng dùng chung, Liên động & Cảnh báo (Cross Functional)](../05_CHUONG_5_CROSS_FUNCTIONAL.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Cross Functional 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 336–339)  
**Tệp định nghĩa khối lệnh:** `P_Gate_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_Gate`:** **6** Biến đầu vào (`Input Parameters`) | **6** Biến đầu ra (`Output Parameters`) | **5** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_Gate` trong Studio 5000
- **Chức năng cốt lõi:** Khối **Cổng điều kiện kèm Bộ định thời Trễ Bật / Trễ Tắt (`Condition Gate Delay`)**. Chỉ cho phép tín hiệu đầu vào `Inp` đi qua bộ lọc `On-Delay` và `Off-Delay` sau khi chân cổng `Inp_Gate` đã lên mức 1 đủ thời gian `Cfg_GateDelayT`.
- **Khi nào CẦN sử dụng:** Khối này được nhúng sẵn bên trong các khối `P_AIn*`, `P_DIn*`, `P_PIDE`. Bạn dùng `P_Gate` rời khi muốn **lọc chống rung kèm khóa cổng ức chế (Gating)** cho một tín hiệu logic tự viết trong chương trình.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi đã dùng sẵn các chân `Inp_*Gate` và `*OnDly`/`*OffDly` bên trong khối `P_AIn` hoặc `P_DIn`.

### Quy trình 4 bước đấu nối và cấu hình `P_Gate`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_Gate`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_GateDly`, `Cfg_OnDly`, `Cfg_OffDly`
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_GateCond`, `Cfg_InpCond`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_Gate` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_Gate` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Khối `P_Gate` thực hiện tính toán/xử lý trực tiếp theo các chân đầu vào `Inp_*` và thông số cấu hình `Cfg_*` trong mỗi chu kỳ quét mà không yêu cầu kích lệnh điều khiển `PCmd_*` từ chương trình.
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Sts_Gate`, `Sts_Err`
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 6 Biến Đầu Vào (`Input Parameters`) của `P_Gate`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu điều kiện đầu vào (1 = Kích hoạt điều kiện / Báo động, 0 = Bình thường) |
| **`Inp_Gate`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input): Khi = 1, cho phép giám sát tín hiệu đầu vào Inp (áp dụng On-Delay/Off-Delay sau thời gian Cfg_GateDly); khi = 0, khóa cổng và ép ngõ ra về 0 |
| **`Cfg_GateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng điều kiện (Gate Delay, tính bằng giây): Khoảng thời gian chân Inp_Gate phải duy trì ở mức 1 trước khi cho phép bật giám sát đầu vào |
| **`Cfg_OnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây): Tín hiệu đầu vào phải duy trì ở mức 1 liên tục đủ thời gian này mới bật ngõ ra |
| **`Cfg_OffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây): Tín hiệu đầu vào phải về mức 0 liên tục đủ thời gian này mới tắt ngõ ra |

## 3. Bảng chi tiết 6 Biến Đầu Ra (`Output Parameters`) của `P_Gate`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Out`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra điều khiển thiết bị (hoặc kết quả cổng điều kiện sau thời gian trễ On-Delay / Off-Delay) |
| **`Sts_Gate`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) đã hoàn tất, cho phép tín hiệu đi qua cổng giám sát |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị đặt bộ định thời gian (Timer Preset) nằm ngoài phạm vi hợp lệ 0..2.147.483 giây |
| **`P_Gate`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_Gate) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 5 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_Gate`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_GateCond`** | `STRING_20` | `'Gating Condition'` | [Biến cấu hình thông số] Chuỗi văn bản mô tả điều kiện mở cổng (Inp_Gate) hiển thị trên HMI của khối P_Gate |
| **`Cfg_InpCond`** | `STRING_20` | `'Input Condition'` | [Biến cấu hình thông số] Chuỗi văn bản mô tả điều kiện đầu vào (Inp) hiển thị trên HMI của khối P_Gate |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_Gate'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_Intlk`
**Thuộc chương:** [Chương 5: Khối chức năng dùng chung, Liên động & Cảnh báo (Cross Functional)](../05_CHUONG_5_CROSS_FUNCTIONAL.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Cross Functional 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 340–345)  
**Tệp định nghĩa khối lệnh:** `P_Intlk_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_Intlk`:** **11** Biến đầu vào (`Input Parameters`) | **8** Biến đầu ra (`Output Parameters`) | **13** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_Intlk` trong Studio 5000
- **Chức năng cốt lõi:** Khối **Khóa Liên động An toàn 16 đầu vào có Bắt lỗi đầu tiên (First Out) và Bypass bảo trì**. Giám sát 16 điều kiện `Inp_Intlk00..15` (cấu hình được mức 0 hoặc 1 là OK, cấu hình tự giữ Latch và quyền phép Bypass cho từng kênh). Khi vi phạm sẽ cắt thiết bị đang chạy ngay lập tức và chỉ rõ nguyên nhân đầu tiên gây dừng máy (`Val_FirstOut`).
- **Khi nào CẦN sử dụng:** Gắn kèm với **mọi Động cơ, Biến tần, Van, Bộ PID** để bảo vệ thiết bị và công nghệ (ví dụ: dừng bơm ngay khi mức bồn hút quá thấp `LoLo`, áp suất đẩy quá cao `HiHi`, hoặc nhiệt độ ổ bi quá cao). *(Đặt tên thẻ: `[Tên_Thiết_Bị]_Intlk`)*.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi điều kiện chỉ cần kiểm tra lúc bắt đầu khởi động mà không cần dừng máy khi đang chạy → Dùng [`P_Perm.md`](./P_Perm.md).

### Quy trình 4 bước đấu nối và cấu hình `P_Intlk`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_Intlk00…Inp_Intlk15`, `Inp_BypActive`, `Inp_LatchDefeat`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_HasMoreObj`, `Cfg_OKState`, `Cfg_Latched`, `Cfg_Bypassable`, `Cfg_HasNav`
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_CondTxt`, `Cfg_Desc`, `Cfg_Label`, `Cfg_NavTag`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_Intlk` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_Intlk` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PCmd_Reset`
   - **Cách sử dụng trong code PLC:** Gán giá trị đặt vào `PSet_*` (nếu có) hoặc kích xung 1 chu kỳ vào các bit lệnh `PCmd_* = 1` để xóa lỗi (`PCmd_Reset`), xác nhận cảnh báo (`PCmd_*Ack`) hoặc ức chế cảnh báo (`PCmd_*Suppress`). Khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi.
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Sts_IntlkOK`, `Sts_NBIntlkOK`, `Sts_BypActive`, `Sts_RdyReset`, `Sts_Intlk`, `Sts_FirstOut`
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 11 Biến Đầu Vào (`Input Parameters`) của `P_Intlk`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp_Intlk00…Inp_Intlk15`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 16 chân đầu vào điều kiện Khóa liên động an toàn (Inp_Intlk00..15): Nếu bất kỳ chân nào lệch khỏi trạng thái an toàn (Cfg_OKState) và không được Bypass, ngõ ra Sts_IntlkOK lập tức về 0 để dừng thiết bị về trạng thái an toàn |
| **`Inp_BypActive`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu báo thiết bị chính đang bật chế độ Bypass bảo trì (1 = Đang kích hoạt Bypass các điều kiện cho phép bypass; nối từ Sts_BypActive của khối thiết bị) |
| **`Inp_LatchDefeat`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tạm ngắt chức năng tự chốt giữ lỗi liên động khi thiết bị đang dừng (1 = Không chốt giữ lỗi để tìm lỗi dễ dàng khi máy dừng; nối từ Sts_Stopped/Sts_Closed) |
| **`Inp_Reset`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_OKState`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ cấu hình 16-bit quy định mức logic an toàn (0 hoặc 1) cho từng chân trong 16 điều kiện (Inp_00..15): Bit = 1 nghĩa là chân đó phải bằng 1 mới OK (kết nối thường đóng fail-safe); Bit = 0 nghĩa là chân đó phải bằng 0 mới OK |
| **`Cfg_Latched`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ cấu hình 16-bit chọn các điều kiện tự chốt giữ (Latch): Bit = 1 thì khi điều kiện đó vi phạm sẽ tự giữ chặt trạng thái lỗi cho đến khi nhấn Reset |
| **`Cfg_Bypassable`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ cấu hình 16-bit phân quyền Bypass bảo trì: Bit = 1 cho phép điều kiện tương ứng được bỏ qua khi bật Bypass; Bit = 0 là điều kiện an toàn bất khả kháng (Non-Bypassable) |
| **`Cfg_HasNav`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ bit (16-bit) bật nút điều hướng nhảy tới Faceplate nguyên nhân cho từng điều kiện trong 16 kênh (Bit = 1 là bật) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |

## 3. Bảng chi tiết 8 Biến Đầu Ra (`Output Parameters`) của `P_Intlk`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Sts_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái tổng hợp của toàn bộ điều kiện Khóa liên động (bao gồm các kênh được Bypass): 1 = Tất cả đều OK (cho phép chạy); 0 = Cắt dừng thiết bị ngay lập tức |
| **`Sts_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái tổng hợp của các điều kiện Khóa liên động KHÔNG thể Bypass: 1 = Tất cả đều OK; 0 = Cắt dừng thiết bị ngay lập tức |
| **`Sts_BypActive`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Chế độ Bypass bảo trì đang kích hoạt (gửi sang khối P_Intlk / P_Perm để cho phép bỏ qua các điều kiện có quyền Bypass) |
| **`Sts_RdyReset`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc chốt ngắt bảo vệ (Shed) đã hết lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_Intlk`** | `INT` | `2#0000_0000_0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit (16-bit) báo trạng thái vi phạm của từng kênh liên động riêng lẻ (Bit = 1 là kênh đó đang bị lỗi gây cắt máy; Bit = 0 là kênh đó đang OK) |
| **`Sts_FirstOut`** | `INT` | `2#0000_0000_0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit (16-bit) chỉ báo thủ phạm đầu tiên gây cắt liên động (First-Out Status: chỉ duy nhất bit của điều kiện bị mất đầu tiên lên 1) |
| **`P_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_Intlk) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 13 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_Intlk`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_8` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_CondTxt`** | `STRING_20[16]` | `{...}` | [Biến cấu hình thông số] Mảng 16 chuỗi văn bản mô tả tên của 16 điều kiện Khóa liên động / Cho phép hiển thị trên HMI Faceplate |
| **`Cfg_Desc`** | `STRING_40` | `'Interlocks with First Out'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_Label`** | `STRING_20` | `'Interlocks'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_NavTag`** | `STRING_NavTag[16]` | `{...}` | [Biến cấu hình thông số] Mảng 16 tên thẻ (Tagname) đích tương ứng với 16 nút điều hướng điều kiện liên động/cho phép trên HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_Intlk'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_Intlk'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`MSet_Bypass`** | `INT` | `2#0000_0000_0000_0000` | [Giá trị đặt từ Bảo trì HMI] Giá trị đặt từ Nhân viên bảo trì (Maintenance HMI): Từ bit (Bitmask) bật/tắt chế độ Bypass bảo trì cho từng điều kiện riêng lẻ (Bit = 1 là Bypass) |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`Val_FirstOutTxt`** | `STRING_Cond` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi văn bản mô tả điều kiện Khóa liên động đầu tiên gây cắt máy (First-Out Condition Text) |
---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_IntlkAdv`
**Thuộc chương:** [Chương 5: Khối chức năng dùng chung, Liên động & Cảnh báo (Cross Functional)](../05_CHUONG_5_CROSS_FUNCTIONAL.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Cross Functional 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 346–352)  
**Tệp định nghĩa khối lệnh:** `P_IntlkAdv_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_IntlkAdv`:** **15** Biến đầu vào (`Input Parameters`) | **12** Biến đầu ra (`Output Parameters`) | **15** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_IntlkAdv` trong Studio 5000
- **Chức năng cốt lõi:** Khối **Khóa Liên động Nâng cao (Advanced Interlocks)**. Mở rộng từ `P_Intlk` với khả năng phân loại điều kiện liên động theo các nhóm cấp độ ưu tiên dừng (**Stop Priority / Shed Levels**) và điều hướng trạng thái an toàn linh hoạt hơn.
- **Khi nào CẦN sử dụng:** Khi thiết bị chấp hành có **nhiều kiểu dừng an toàn khác nhau** tùy theo mức độ sự cố (ví dụ: liên động cấp 1 thì giảm tốc dừng êm *Coast/Ramp Stop*, còn liên động khẩn cấp cấp 0 thì cắt điện phanh gấp ngay lập tức).
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi mọi điều kiện liên động đều tác động dừng máy giống nhau → Dùng [`P_Intlk.md`](./P_Intlk.md) cho gọn nhẹ.

### Quy trình 4 bước đấu nối và cấu hình `P_IntlkAdv`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_Intlk00…Inp_Intlk15`, `Inp_IOFault`, `Inp_Avail`, `Inp_BypActive`, `Inp_LatchDefeat`, `Inp_Reset`
   - **Các chân tham chiếu dữ liệu (`Ref_*` / `InOut`):** `Ref_Inputs` — bắt buộc gán thẻ mảng/cấu trúc tương ứng trong Controller Tags.
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_HasMoreObj`, `Cfg_OKState`, `Cfg_Latched`, `Cfg_Bypassable`, `Cfg_HasNav`, `Cfg_Type0…Cfg_Type15`, `Cfg_HasType`
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_CondTxt`, `Cfg_Desc`, `Cfg_Label`, `Cfg_NavTag`, `Cfg_Tag`, `Cfg_TypeDesc`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_IntlkAdv` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_IntlkAdv` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PCmd_Reset`
   - **Cách sử dụng trong code PLC:** Gán giá trị đặt vào `PSet_*` (nếu có) hoặc kích xung 1 chu kỳ vào các bit lệnh `PCmd_* = 1` để xóa lỗi (`PCmd_Reset`), xác nhận cảnh báo (`PCmd_*Ack`) hoặc ức chế cảnh báo (`PCmd_*Suppress`). Khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi.
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Sts_IntlkOK`, `Sts_NBIntlkOK`, `Sts_BypActive`, `Sts_RdyReset`, `Sts_LatchMask`, `Sts_BypassMask`, `Sts_Intlk`, `Sts_Avail`, `Sts_FirstOut`
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 15 Biến Đầu Vào (`Input Parameters`) của `P_IntlkAdv`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp_Intlk00…Inp_Intlk15`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 16 chân đầu vào điều kiện Khóa liên động an toàn (Inp_Intlk00..15): Nếu bất kỳ chân nào lệch khỏi trạng thái an toàn (Cfg_OKState) và không được Bypass, ngõ ra Sts_IntlkOK lập tức về 0 để dừng thiết bị về trạng thái an toàn |
| **`Inp_IOFault`** | `INT` | `2#0000_0000_0000_0000` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi truyền thông I/O của thiết bị (0 = Tốt/OK, 1 = Lỗi truyền thông I/O Fault) |
| **`Inp_Avail`** | `BOOL` | `2#1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu đầu vào kiểm tra tính sẵn sàng của thiết bị đối với các điều kiện liên động |
| **`Inp_BypActive`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu báo thiết bị chính đang bật chế độ Bypass bảo trì (1 = Đang kích hoạt Bypass các điều kiện cho phép bypass; nối từ Sts_BypActive của khối thiết bị) |
| **`Inp_LatchDefeat`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tạm ngắt chức năng tự chốt giữ lỗi liên động khi thiết bị đang dừng (1 = Không chốt giữ lỗi để tìm lỗi dễ dàng khi máy dừng; nối từ Sts_Stopped/Sts_Closed) |
| **`Inp_Reset`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_OKState`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ cấu hình 16-bit quy định mức logic an toàn (0 hoặc 1) cho từng chân trong 16 điều kiện (Inp_00..15): Bit = 1 nghĩa là chân đó phải bằng 1 mới OK (kết nối thường đóng fail-safe); Bit = 0 nghĩa là chân đó phải bằng 0 mới OK |
| **`Cfg_Latched`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ cấu hình 16-bit chọn các điều kiện tự chốt giữ (Latch): Bit = 1 thì khi điều kiện đó vi phạm sẽ tự giữ chặt trạng thái lỗi cho đến khi nhấn Reset |
| **`Cfg_Bypassable`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ cấu hình 16-bit phân quyền Bypass bảo trì: Bit = 1 cho phép điều kiện tương ứng được bỏ qua khi bật Bypass; Bit = 0 là điều kiện an toàn bất khả kháng (Non-Bypassable) |
| **`Cfg_HasNav`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ bit (16-bit) bật nút điều hướng nhảy tới Faceplate nguyên nhân cho từng điều kiện trong 16 kênh (Bit = 1 là bật) |
| **`Cfg_Type0…Cfg_Type15`** | `SINT` | `6` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình kiểu đối tượng mở rộng cho 16 kênh điều kiện liên động (00..15) để điều hướng trên HMI |
| **`Cfg_HasType`** | `SINT` | `2#1111_1111` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng thông tin mở rộng đi kèm cho từng điều kiện |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |

## 3. Bảng chi tiết 12 Biến Đầu Ra (`Output Parameters`) của `P_IntlkAdv`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Ref_Inputs`** | `INT` | `0` | Ẩn (Tag) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới mảng cấu hình các điều kiện đầu vào của khối liên động nâng cao |
| **`Sts_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái tổng hợp của toàn bộ điều kiện Khóa liên động (bao gồm các kênh được Bypass): 1 = Tất cả đều OK (cho phép chạy); 0 = Cắt dừng thiết bị ngay lập tức |
| **`Sts_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái tổng hợp của các điều kiện Khóa liên động KHÔNG thể Bypass: 1 = Tất cả đều OK; 0 = Cắt dừng thiết bị ngay lập tức |
| **`Sts_BypActive`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Chế độ Bypass bảo trì đang kích hoạt (gửi sang khối P_Intlk / P_Perm để cho phép bỏ qua các điều kiện có quyền Bypass) |
| **`Sts_RdyReset`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc chốt ngắt bảo vệ (Shed) đã hết lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_LatchMask`** | `INT` | `2#0000_0000_0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit (Bitmask) phản ánh các điều kiện được cấu hình tự chốt giữ (Latched) |
| **`Sts_BypassMask`** | `INT` | `2#0000_0000_0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit (Bitmask) phản ánh các điều kiện hiện đang được bật Bypass |
| **`Sts_Intlk`** | `INT` | `2#0000_0000_0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit (16-bit) báo trạng thái vi phạm của từng kênh liên động riêng lẻ (Bit = 1 là kênh đó đang bị lỗi gây cắt máy; Bit = 0 là kênh đó đang OK) |
| **`Sts_Avail`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Tham số định danh trạng thái sẵn sàng dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |
| **`Sts_FirstOut`** | `INT` | `2#0000_0000_0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit (16-bit) chỉ báo thủ phạm đầu tiên gây cắt liên động (First-Out Status: chỉ duy nhất bit của điều kiện bị mất đầu tiên lên 1) |
| **`P_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_Intlk) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 15 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_IntlkAdv`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_CondTxt`** | `STRING_Cond[16]` | `{...}` | [Biến cấu hình thông số] Mảng 16 chuỗi văn bản mô tả tên của 16 điều kiện Khóa liên động / Cho phép hiển thị trên HMI Faceplate |
| **`Cfg_Desc`** | `STRING_40` | `'Advanced Interlocks with First Out'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_Label`** | `STRING_20` | `'Advanced Interlocks'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_NavTag`** | `STRING_NavTag[16]` | `{...}` | [Biến cấu hình thông số] Mảng 16 tên thẻ (Tagname) đích tương ứng với 16 nút điều hướng điều kiện liên động/cho phép trên HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_IntlkAdv'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`Cfg_TypeDesc`** | `STRING_16[8]` | `{...}` | [Biến cấu hình thông số] Chuỗi mô tả kiểu đối tượng điều hướng hiển thị trên HMI Faceplate |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_IntlkAdv'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`MSet_Bypass`** | `INT` | `2#0000_0000_0000_0000` | [Giá trị đặt từ Bảo trì HMI] Giá trị đặt từ Nhân viên bảo trì (Maintenance HMI): Từ bit (Bitmask) bật/tắt chế độ Bypass bảo trì cho từng điều kiện riêng lẻ (Bit = 1 là Bypass) |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`Val_FirstOutTxt`** | `STRING_Cond` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi văn bản mô tả điều kiện Khóa liên động đầu tiên gây cắt máy (First-Out Condition Text) |
| **`Val_FirstOutType`** | `STRING_16` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi định danh kiểu đối tượng hoặc thẻ điều hướng của điều kiện First-Out đầu tiên |
---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_CmdSrc`
**Thuộc chương:** [Chương 5: Khối chức năng dùng chung, Liên động & Cảnh báo (Cross Functional)](../05_CHUONG_5_CROSS_FUNCTIONAL.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Cross Functional 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 378–388)  
**Tệp định nghĩa khối lệnh:** `P_CmdSrc_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_CmdSrc`:** **37** Biến đầu vào (`Input Parameters`) | **28** Biến đầu ra (`Output Parameters`) | **2** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_CmdSrc` trong Studio 5000
- **Chức năng cốt lõi:** Khối **Quản lý Phân quyền Nguồn lệnh (`Command Source`)** giữa 7 chế độ: Operator, Program, External, Override, Maintenance, Hand, Out of Service.
- **Khi nào CẦN sử dụng:** Đã được nhúng sẵn bên trong mọi khối chấp hành của thư viện. Bạn chỉ dùng `P_CmdSrc` độc lập khi **tự xây dựng một khối AOI thiết bị mới của riêng mình** và muốn nó có cơ chế chuyển mạch `Oper / Prog / Maint / Hand / OoS` chuẩn PlantPAx.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Không cần gọi riêng khi dùng các khối thiết bị có sẵn của thư viện.

### Quy trình 4 bước đấu nối và cấu hình `P_CmdSrc`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_Hand`, `Inp_Ovrd`, `Inp_ExtInh`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_HasOper`, `Cfg_HasOperLocked`, `Cfg_HasProg`, `Cfg_HasProgLocked`, `Cfg_HasExt`, `Cfg_HasMaint`, `Cfg_HasMOoS`, `Cfg_OvrdOverLock`, `Cfg_ExtOverLock`, `Cfg_ProgPwrUp`, `Cfg_ProgNormal`, `Cfg_PCmdPriority`, `Cfg_PCmdProgAsLevel`, `Cfg_PCmdLockAsLevel`, `Cfg_ExtAcqAsLevel`
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** Không có biến `Cfg_*` cục bộ
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_CmdSrc` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_CmdSrc` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PCmd_Oper`, `PCmd_Prog`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh điều khiển tự động, kiểm tra khối đã sẵn sàng ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển quyền về Program, sau đó gán giá trị vào `PSet_*` và kích xung `PCmd_* = 1` (khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Sts_eSrc`, `Sts_bSrc`, `Sts_Hand`, `Sts_OoS`, `Sts_Maint`, `Sts_Ovrd`, `Sts_Ext`, `Sts_Prog`, `Sts_ProgLocked`, `Sts_Oper`, `Sts_OperLocked`, `Sts_ProgOperSel`, `Sts_ProgOperLock`, `Sts_Normal`, `Sts_ExtReqInh`, `Sts_ProgReqInh`, `Sts_MAcqRcvd`
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 37 Biến Đầu Vào (`Input Parameters`) của `P_CmdSrc`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp_Hand`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu chọn chế độ Điều khiển bằng tay tại chỗ (Hand/Local): 1 = Chiếm quyền sang chế độ Hand (thường nối từ công tắc cứng tại tủ MCC); 0 = Nhả chế độ Hand |
| **`Inp_Ovrd`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu kích hoạt chế độ Cưỡng bức ưu tiên (Override): 1 = Chiếm quyền sang chế độ Override; 0 = Nhả chế độ Override |
| **`Inp_ExtInh`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Chân khóa cấm nguồn lệnh ngoài (External Inhibit): 1 = Cấm chế độ External chiếm quyền điều khiển; 0 = Cho phép External chiếm quyền |
| **`Cfg_HasOper`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép sử dụng chế độ Người vận hành (Operator — không khóa) |
| **`Cfg_HasOperLocked`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép sử dụng chế độ Khóa Người vận hành (Operator Locked) |
| **`Cfg_HasProg`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép sử dụng chế độ Chương trình tự động (Program — không khóa) |
| **`Cfg_HasProgLocked`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép sử dụng chế độ Khóa Chương trình tự động (Program Locked) |
| **`Cfg_HasExt`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép sử dụng chế độ Điều khiển ngoài (External) |
| **`Cfg_HasMaint`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép sử dụng chế độ Bảo trì (Maintenance) |
| **`Cfg_HasMOoS`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép nhân viên bảo trì chuyển thiết bị sang chế độ Ngưng phục vụ (Out of Service) |
| **`Cfg_OvrdOverLock`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép chế độ Override ghi đè chiếm quyền ngay cả khi đang bị khóa ở Program/Operator; 0 = Tôn trọng khóa Lock |
| **`Cfg_ExtOverLock`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép chế độ External ghi đè chiếm quyền ngay cả khi đang bị khóa ở Program/Operator; 0 = Tôn trọng khóa Lock |
| **`Cfg_ProgPwrUp`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Nguồn lệnh mặc định khi vừa bật nguồn PLC (Powerup): 1 = Khởi động vào chế độ Program; 0 = Khởi động vào chế độ Operator |
| **`Cfg_ProgNormal`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Nguồn lệnh chuẩn mặc định khi nhận lệnh Normal: 1 = Chế độ Program là Normal; 0 = Chế độ Operator là Normal |
| **`Cfg_PCmdPriority`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Quyền ưu tiên khi có lệnh đồng thời: 1 = Lệnh từ Chương trình (PCmd) thắng; 0 = Lệnh từ Người vận hành (OCmd) thắng |
| **`Cfg_PCmdProgAsLevel`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Chân PCmd_Prog hoạt động theo mức duy trì (Level: 1 = Program, 0 = Operator); 0 = Hoạt động theo sườn xung (Edge) |
| **`Cfg_PCmdLockAsLevel`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Chân PCmd_Lock hoạt động theo mức duy trì (Level: 1 = Khóa, 0 = Mở khóa); 0 = Hoạt động theo sườn xung (Edge) |
| **`Cfg_ExtAcqAsLevel`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Chân XCmd_Acq hoạt động theo mức duy trì (1 = Chiếm quyền External, 0 = Nhả quyền); 0 = Hoạt động theo sườn xung |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Người vận hành (từ Program sang Operator) |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Chương trình tự động (từ Operator sang Program) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa giữ nguồn lệnh hiện tại (không cho phép nguồn lệnh kia giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Đưa nguồn lệnh về chế độ Bình thường mặc định (Normal: Program hoặc Operator theo Cfg_ProgNormal) |
| **`MCmd_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Đưa thiết bị vào trạng thái Ngưng phục vụ (Out of Service) |
| **`MCmd_IS`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Đưa thiết bị trở lại trạng thái Phục vụ bình thường (In Service) |
| **`MCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Chiếm quyền điều khiển (Acquire) về chế độ tương ứng |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |
| **`OCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Chuyển nguồn lệnh sang chế độ Người vận hành (từ Program sang Operator) |
| **`OCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Chuyển nguồn lệnh sang chế độ Chương trình tự động (từ Operator sang Program) |
| **`OCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Khóa giữ nguồn lệnh hiện tại (không cho phép nguồn lệnh kia giành quyền) |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`OCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Đưa nguồn lệnh về chế độ Bình thường mặc định (Normal: Program hoặc Operator theo Cfg_ProgNormal) |
| **`XRdy_Acq`** | `BOOL` | `0` | Ẩn (Tag) | 1 = Sẵn sàng nhận lệnh Chiếm quyền điều khiển từ hệ thống ngoài (XCmd_Acq) |
| **`XRdy_Rel`** | `BOOL` | `0` | Ẩn (Tag) | 1 = Sẵn sàng nhận lệnh Nhả quyền điều khiển từ hệ thống ngoài (XCmd_Rel) |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Chiếm quyền điều khiển (Acquire) về chế độ tương ứng |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |

## 3. Bảng chi tiết 28 Biến Đầu Ra (`Output Parameters`) của `P_CmdSrc`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Sts_eSrc`** | `INT` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Mã liệt kê nguồn lệnh hiện hành (.8=Ext, .7=OoS, .6=Oper, .5=Prog, .4=Ovrd, .3=Maint, .2=Hand, .1=Norm, .0=Lock) |
| **`Sts_bSrc`** | `INT` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit (Bitmap) biểu thị nguồn lệnh đang hoạt động (dùng để hiển thị cột trạng thái nguồn lệnh trên HMI) |
| **`Sts_Hand`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Điều khiển bằng tay tại chỗ (Hand — ưu tiên cao hơn OoS, Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_OoS`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Ngưng phục vụ (Out of Service — ưu tiên cao hơn Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_Maint`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Bảo trì (Maintenance — ưu tiên cao hơn Ovrd, Ext, Prog, Oper) |
| **`Sts_Ovrd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Cưỡng bức ưu tiên (Override — ưu tiên cao hơn Ext, Prog, Oper) |
| **`Sts_Ext`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Điều khiển ngoài (External — ưu tiên cao hơn Prog, Oper) |
| **`Sts_Prog`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Chương trình tự động (Program) |
| **`Sts_ProgLocked`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Chương trình tự động và đã Khóa (Program Locked — không cho Operator lấy quyền) |
| **`Sts_Oper`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Người vận hành trên HMI (Operator) |
| **`Sts_OperLocked`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Người vận hành và đã Khóa (Operator Locked — không cho Program lấy quyền) |
| **`Sts_ProgOperSel`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái chốt chọn giữa Program và Operator: 1 = Program, 0 = Operator |
| **`Sts_ProgOperLock`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái chốt khóa nguồn lệnh: 1 = Đang khóa (Locked), 0 = Không khóa (Unlocked) |
| **`Sts_Normal`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Nguồn lệnh hiện hành đang khớp với nguồn lệnh Bình thường mặc định (Program hoặc Operator theo Cfg_ProgNormal) |
| **`Sts_ExtReqInh`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Yêu cầu chuyển sang chế độ External đang bị khóa cấm (không thể chuyển sang External từ trạng thái hiện tại) |
| **`Sts_ProgReqInh`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Yêu cầu chuyển sang chế độ Program đang bị khóa cấm (không thể chuyển sang Program từ trạng thái hiện tại) |
| **`Sts_MAcqRcvd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đã nhận được lệnh Chiếm quyền bảo trì (MCmd_Acq) trong chu kỳ quét hiện tại |
| **`MRdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_OoS (Đưa thiết bị vào trạng thái Ngưng phục vụ (Out of Service)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_IS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_IS (Đưa thiết bị trở lại trạng thái Phục vụ bình thường (In Service)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Acq (Chiếm quyền điều khiển (Acquire) về chế độ tương ứng), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Oper (Chuyển nguồn lệnh sang chế độ Người vận hành (từ Program sang Operator)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Prog (Chuyển nguồn lệnh sang chế độ Chương trình tự động (từ Operator sang Program)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Lock (Khóa giữ nguồn lệnh hiện tại (không cho phép nguồn lệnh kia giành quyền)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Normal (Đưa nguồn lệnh về chế độ Bình thường mặc định (Normal: Program hoặc Operator theo Cfg_ProgNormal)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`P_CmdSrc`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_CmdSrc) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 2 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_CmdSrc`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`HMI_Lib`** | `STRING_8` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Type`** | `STRING_12` | `P_CmdSrc'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_Perm`
**Thuộc chương:** [Chương 5: Khối chức năng dùng chung, Liên động & Cảnh báo (Cross Functional)](../05_CHUONG_5_CROSS_FUNCTIONAL.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Cross Functional 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 353–358)  
**Tệp định nghĩa khối lệnh:** `P_Perm_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_Perm`:** **7** Biến đầu vào (`Input Parameters`) | **6** Biến đầu ra (`Output Parameters`) | **10** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_Perm` trong Studio 5000
- **Chức năng cốt lõi:** Khối **Điều kiện Cho phép Khởi động 16 đầu vào (`Permissives with Bypass`)**. Kiểm tra tối đa 16 điều kiện `Inp_Perm00..15` trước khi cho phép thiết bị khởi động (hoặc mở van), có hỗ trợ Bypass bảo trì và điều hướng nhanh tới thẻ nguyên nhân trên HMI.
- **Khi nào CẦN sử dụng:** Khi cần kiểm tra các **điều kiện chuẩn bị trước lúc bấm Start** (ví dụ: van đầu hút đã mở, van xả đáy đã đóng, áp suất nước làm mát phớt đã đủ) nhưng khi máy đã chạy rồi thì không dùng các tín hiệu này để cắt máy. *(Đặt tên thẻ: `[Tên_Thiết_Bị]_Perm`)*.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi điều kiện bắt buộc phải duy trì suốt quá trình chạy và nếu mất phải cắt máy ngay → Dùng [`P_Intlk.md`](./P_Intlk.md).

### Quy trình 4 bước đấu nối và cấu hình `P_Perm`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_Perm00…Inp_Perm15`, `Inp_BypActive`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_OKState`, `Cfg_Bypassable`, `Cfg_HasMoreObj`, `Cfg_HasNav`
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_CondTxt`, `Cfg_Desc`, `Cfg_Label`, `Cfg_NavTag`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_Perm` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_Perm` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Khối `P_Perm` thực hiện tính toán/xử lý trực tiếp theo các chân đầu vào `Inp_*` và thông số cấu hình `Cfg_*` trong mỗi chu kỳ quét mà không yêu cầu kích lệnh điều khiển `PCmd_*` từ chương trình.
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Sts_PermOK`, `Sts_NBPermOK`, `Sts_BypActive`, `Sts_Perm`
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 7 Biến Đầu Vào (`Input Parameters`) của `P_Perm`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp_Perm00…Inp_Perm15`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 16 chân đầu vào điều kiện Cho phép khởi động (Inp_Perm00..15): Nếu tất cả các chân đều ở trạng thái chuẩn (Cfg_OKState) hoặc được Bypass thì Sts_PermOK = 1 cho phép thiết bị khởi động |
| **`Inp_BypActive`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu báo thiết bị chính đang bật chế độ Bypass bảo trì (1 = Đang kích hoạt Bypass các điều kiện cho phép bypass; nối từ Sts_BypActive của khối thiết bị) |
| **`Cfg_OKState`** | `INT` | `2#1111_1111_1111_1111` | Ẩn (Tag) | [Biến cấu hình thông số] Từ cấu hình 16-bit quy định mức logic an toàn (0 hoặc 1) cho từng chân trong 16 điều kiện (Inp_00..15): Bit = 1 nghĩa là chân đó phải bằng 1 mới OK (kết nối thường đóng fail-safe); Bit = 0 nghĩa là chân đó phải bằng 0 mới OK |
| **`Cfg_Bypassable`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ cấu hình 16-bit phân quyền Bypass bảo trì: Bit = 1 cho phép điều kiện tương ứng được bỏ qua khi bật Bypass; Bit = 0 là điều kiện an toàn bất khả kháng (Non-Bypassable) |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_HasNav`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ bit (16-bit) bật nút điều hướng nhảy tới Faceplate nguyên nhân cho từng điều kiện trong 16 kênh (Bit = 1 là bật) |

## 3. Bảng chi tiết 6 Biến Đầu Ra (`Output Parameters`) của `P_Perm`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Sts_PermOK`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái tổng hợp của toàn bộ điều kiện Cho phép khởi động (bao gồm các kênh được Bypass): 1 = Tất cả đều OK (cho phép khởi động) |
| **`Sts_NBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái tổng hợp của các điều kiện Cho phép khởi động KHÔNG thể Bypass: 1 = Tất cả đều OK (cho phép khởi động) |
| **`Sts_BypActive`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Chế độ Bypass bảo trì đang kích hoạt (gửi sang khối P_Intlk / P_Perm để cho phép bỏ qua các điều kiện có quyền Bypass) |
| **`Sts_Perm`** | `INT` | `2#1111_1111_1111_1111` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit (16-bit) báo trạng thái của từng điều kiện Cho phép khởi động riêng lẻ (Bit = 1 là OK; Bit = 0 là chưa thỏa mãn) |
| **`P_Perm`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_Perm) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 10 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_Perm`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_8` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_CondTxt`** | `STRING_20[16]` | `{...}` | [Biến cấu hình thông số] Mảng 16 chuỗi văn bản mô tả tên của 16 điều kiện Khóa liên động / Cho phép hiển thị trên HMI Faceplate |
| **`Cfg_Desc`** | `STRING_40` | `'Permissives'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_Label`** | `STRING_20` | `'Permissives'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_NavTag`** | `STRING_NavTag[16]` | `{...}` | [Biến cấu hình thông số] Mảng 16 tên thẻ (Tagname) đích tương ứng với 16 nút điều hướng điều kiện liên động/cho phép trên HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_Perm'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_Perm'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`MSet_Bypass`** | `INT` | `2#0000_0000_0000_0000` | [Giá trị đặt từ Bảo trì HMI] Giá trị đặt từ Nhân viên bảo trì (Maintenance HMI): Từ bit (Bitmask) bật/tắt chế độ Bypass bảo trì cho từng điều kiện riêng lẻ (Bit = 1 là Bypass) |
---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_Reset`
**Thuộc chương:** [Chương 5: Khối chức năng dùng chung, Liên động & Cảnh báo (Cross Functional)](../05_CHUONG_5_CROSS_FUNCTIONAL.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Cross Functional 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 367–370)  
**Tệp định nghĩa khối lệnh:** `P_Reset_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_Reset`:** **6** Biến đầu vào (`Input Parameters`) | **5** Biến đầu ra (`Output Parameters`) | **8** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_Reset` trong Studio 5000
- **Chức năng cốt lõi:** Khối **Xóa lỗi Tập trung (`Central Reset`)** cho cả một cụm thiết bị (Unit / Equipment Module). Gom các cờ `Sts_RdyReset` của các thiết bị con về để báo đèn chờ Reset và phát xung `Out_Reset` có độ rộng tối thiểu (`Cfg_MinResetT`) đảm bảo các thiết bị truyền thông mạng đều nhận được lệnh xóa lỗi.
- **Khi nào CẦN sử dụng:** Khi muốn tạo **1 nút nhấn "Reset Toàn Cụm" trên màn hình HMI hoặc trên mặt tủ điều khiển** để xóa lỗi đồng thời cho toàn bộ bơm, van, biến tần và liên động trong khu vực đó.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi chỉ xóa lỗi riêng từng thiết bị đơn lẻ ngay trên Faceplate của thiết bị đó.

### Quy trình 4 bước đấu nối và cấu hình `P_Reset`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_Reset`, `Inp_ResetReqd`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_MinHoldTime`, `Cfg_HasMoreObj`
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_Desc`, `Cfg_Label`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_Reset` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_Reset` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PCmd_Reset`
   - **Cách sử dụng trong code PLC:** Gán giá trị đặt vào `PSet_*` (nếu có) hoặc kích xung 1 chu kỳ vào các bit lệnh `PCmd_* = 1` để xóa lỗi (`PCmd_Reset`), xác nhận cảnh báo (`PCmd_*Ack`) hoặc ức chế cảnh báo (`PCmd_*Suppress`). Khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi.
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Out_Reset`, `Sts_RdyReset`, `Sts_Err`
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 6 Biến Đầu Vào (`Input Parameters`) của `P_Reset`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp_Reset`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Inp_ResetReqd`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu báo có thiết bị con trong cụm đang chờ được xóa lỗi (1 = Yêu cầu Reset, cho phép sáng nút nhấn Reset trên HMI) |
| **`Cfg_MinHoldTime`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian tối thiểu giữ xung ngõ ra Out_Reset ở mức 1 để đảm bảo mọi thiết bị nhận được lệnh xóa lỗi (giây) |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |

## 3. Bảng chi tiết 5 Biến Đầu Ra (`Output Parameters`) của `P_Reset`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Out_Reset`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra phát xung xóa lỗi tập trung cho các thiết bị thuộc phạm vi khối P_Reset (1 = Đang phát xung Reset) |
| **`Sts_RdyReset`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc chốt ngắt bảo vệ (Shed) đã hết lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`P_Reset`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_Reset) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 8 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_Reset`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_8` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'Multiple-Device Reset'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_Label`** | `STRING_20` | `'Reset'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_Tag`** | `STRING_20` | `'P_Reset'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Type`** | `STRING_16` | `'P_Reset'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_Prompt`
**Thuộc chương:** [Chương 5: Khối chức năng dùng chung, Liên động & Cảnh báo (Cross Functional)](../05_CHUONG_5_CROSS_FUNCTIONAL.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Cross Functional 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 389–390)  
**Tệp định nghĩa khối lệnh:** `P_Prompt_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_Prompt`:** **12** Biến đầu vào (`Input Parameters`) | **18** Biến đầu ra (`Output Parameters`) | **0** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_Prompt` trong Studio 5000
- **Chức năng cốt lõi:** Khối **Hộp thoại Nhắc lệnh & Tương tác Người vận hành (`Operator Prompt`)**. Hiển thị thông báo trên HMI yêu cầu người vận hành xác nhận thao tác thủ công, chọn phương án từ danh sách hoặc nhập thông số thực tế trả về cho bộ điều khiển tuần tự `P_Seq`.
- **Khi nào CẦN sử dụng:** Khi kết hợp với khối **`P_Seq`**: ví dụ đến bước 5 của quy trình pha chế, hệ thống tạm dừng và bật hộp thoại `P_Prompt` nhắc công nhân *"Hãy đổ 5 kg phụ gia X vào phễu và nhấn Xác nhận (Confirm) để tiếp tục chạy bước 6"*.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Không dùng `P_Prompt` để báo động sự cố (sự cố bất thường phải dùng `P_Alarm`).

### Quy trình 4 bước đấu nối và cấu hình `P_Prompt`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_Req`, `Inp_Ref`, `Inp_ScalePct`, `Inp_Real_0`, `Inp_Real_1`, `Inp_Real_2`, `Inp_Real_3`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_HasMoreObj`
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** Không có biến `Cfg_*` cục bộ
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_Prompt` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_Prompt` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Khối `P_Prompt` thực hiện tính toán/xử lý trực tiếp theo các chân đầu vào `Inp_*` và thông số cấu hình `Cfg_*` trong mỗi chu kỳ quét mà không yêu cầu kích lệnh điều khiển `PCmd_*` từ chương trình.
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Out_Cfm`, `Out_Rdy`, `Out_Select`, `Out_Real_0`, `Out_Real_1`, `Out_Real_2`, `Out_Real_3`, `Val_Ref`, `Sts_Alert`, `Sts_RefChg`, `Sts_Err`
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 12 Biến Đầu Vào (`Input Parameters`) của `P_Prompt`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Inp_Req`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Chân kích hoạt yêu cầu bật hộp thoại nhắc lệnh Người vận hành (1 = Enable Operator Prompt) |
| **`Inp_Ref`** | `DINT` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Số thứ tự cấu hình thông báo nhắc lệnh trong mảng Prompts cần gọi hiển thị |
| **`Inp_ScalePct`** | `REAL` | `100` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Hệ số phần trăm (%) dùng để nhân tỷ lệ các giá trị thực xuất ra hoặc hiển thị trong trình tự P_Seq |
| **`Inp_Real_0`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Giá trị số thực (#0) từ chương trình PLC truyền vào để hiển thị trên hộp thoại nhắc lệnh P_Prompt |
| **`Inp_Real_1`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Giá trị số thực (#1) từ chương trình PLC truyền vào để hiển thị trên hộp thoại nhắc lệnh P_Prompt |
| **`Inp_Real_2`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Giá trị số thực (#2) từ chương trình PLC truyền vào để hiển thị trên hộp thoại nhắc lệnh P_Prompt |
| **`Inp_Real_3`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Giá trị số thực (#3) từ chương trình PLC truyền vào để hiển thị trên hộp thoại nhắc lệnh P_Prompt |
| **`OCmd_Cfm`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xác nhận thông báo nhắc lệnh của Người vận hành (Operator Prompt Confirmation) |
| **`Prompts`** | `P_PromptCfg[1]` | `-` | Ẩn (Tag) | Mảng cấu trúc dữ liệu (P_PromptCfg) lưu nội dung câu nhắc và các phương án trả lời của khối P_Prompt |
| **`RespData`** | `P_PromptResp` | `-` | Ẩn (Tag) | Cấu trúc dữ liệu (P_PromptResp) lưu câu trả lời gần nhất của Người vận hành dưới dạng chuỗi ký tự |

## 3. Bảng chi tiết 18 Biến Đầu Ra (`Output Parameters`) của `P_Prompt`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Out_Cfm`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Xung ngõ ra báo Người vận hành đã nhấn Xác nhận hộp thoại nhắc lệnh (Operator Confirmation Output) |
| **`Out_Rdy`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Cờ báo thông báo nhắc lệnh đã sẵn sàng hiển thị trên màn hình HMI (1 = Sẵn sàng) |
| **`Out_Select`** | `SINT` | `4` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị số nguyên phản hồi phương án lựa chọn của Người vận hành trên hộp thoại nhắc lệnh |
| **`Out_Real_0`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị số thực (REAL #0) do Người vận hành nhập vào từ hộp thoại nhắc lệnh P_Prompt |
| **`Out_Real_1`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị số thực (REAL #1) do Người vận hành nhập vào từ hộp thoại nhắc lệnh P_Prompt |
| **`Out_Real_2`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị số thực (REAL #2) do Người vận hành nhập vào từ hộp thoại nhắc lệnh P_Prompt |
| **`Out_Real_3`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị số thực (REAL #3) do Người vận hành nhập vào từ hộp thoại nhắc lệnh P_Prompt |
| **`Val_Ref`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Số thứ tự cấu hình thông báo nhắc lệnh (Prompt Reference) hiện đang được sử dụng |
| **`Sts_Alert`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái cảnh báo chẩn đoán dùng để hiển thị biểu tượng cảnh báo phụ trên HMI |
| **`Sts_RefChg`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Phát hiện chỉ số thông báo nhắc lệnh (Inp_Ref) vừa thay đổi trong chu kỳ quét hiện tại (xung 1 chu kỳ) |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_PromptCfgSize`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Kích thước mảng trong kiểu dữ liệu UDT P_PromptCfg không khớp tiêu chuẩn |
| **`Err_RespDataSize`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 2 = Lỗi cấu hình: Kích thước mảng dữ liệu phản hồi trong kiểu UDT P_PromptResp không hợp lệ |
| **`Err_MsgRef`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Chỉ số thông báo nhắc lệnh (Inp_Ref) vượt quá kích thước mảng cấu hình Prompt |
| **`ORdy_Cfm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Cfm (Xác nhận thông báo nhắc lệnh của Người vận hành (Operator Prompt Confirmation)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`Prompts`** | `P_PromptCfg[1]` | `-` | Ẩn (Tag) | Mảng cấu trúc dữ liệu (P_PromptCfg) lưu nội dung câu nhắc và các phương án trả lời của khối P_Prompt |
| **`RespData`** | `P_PromptResp` | `-` | Ẩn (Tag) | Cấu trúc dữ liệu (P_PromptResp) lưu câu trả lời gần nhất của Người vận hành dưới dạng chuỗi ký tự |
---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_Logic`
**Thuộc chương:** [Chương 5: Khối chức năng dùng chung, Liên động & Cảnh báo (Cross Functional)](../05_CHUONG_5_CROSS_FUNCTIONAL.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Cross Functional 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 391–396)  
**Tệp định nghĩa khối lệnh:** `P_Logic_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_Logic`:** **16** Biến đầu vào (`Input Parameters`) | **28** Biến đầu ra (`Output Parameters`) | **35** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_Logic` trong Studio 5000
- **Chức năng cốt lõi:** Khối **Mạch Logic Tổ hợp Khả trình kèm Chụp băng hình trạng thái (`Boolean Logic with Snapshot`)**. Nhận 8 đầu vào Boolean đi qua 8 cổng logic nối tầng (`AND`, `OR`, `XOR`, `Set/Reset Flip-Flop`, `Select 2:1`, `Majority 2oo3`) cấu hình trực tiếp trên HMI mà không cần sửa code PLC, đồng thời **chụp lại toàn bộ trạng thái 8 đầu vào kèm nhãn thời gian (`Snapshot`)** ngay tại mili-giây ngõ ra tác động.
- **Khi nào CẦN sử dụng:** 1. Khi cần tạo **mạch điều kiện liên động/khởi động phức tạp (như biểu quyết 2-trên-3 cảm biến, mạch chốt RS)** và muốn cho phép kỹ sư nhà máy chỉnh sửa logic trên HMI. 2. Khi cần **bắt lỗi chập chờn (Glitch / Intermittent Trip)** diễn ra trong vài mili-giây nhờ tính năng chụp băng hình `Snapshot`.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi chỉ cần gom các điều kiện liên động AND/OR phẳng thông thường → Dùng [`P_Intlk.md`](./P_Intlk.md) hoặc [`P_Perm.md`](./P_Perm.md).

### Quy trình 4 bước đấu nối và cấu hình `P_Logic`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_0…Inp_7`, `Inp_Hold`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_UseInpHold`, `Cfg_UsePCmd`, `Cfg_UseOCmd`, `Cfg_UseOut01`, `Cfg_UseOut10`, `Cfg_TSonSnap`, `Cfg_SnapOver`, `Cfg_HasMoreObj`, `Cfg_OnDelay`, `Cfg_OffDelay`
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_0StText`, `Cfg_1StText`, `Cfg_Area`, `Cfg_Desc`, `Cfg_GateFunc`, `Cfg_GateSrc1Inv`, `Cfg_GateSrc1Mask`, `Cfg_GateSrc1Ptr`, `Cfg_GateSrc2Inv`, `Cfg_GateSrc2Mask`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_Logic` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_Logic` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PCmd_Snap`, `PCmd_Reset`
   - **Cách sử dụng trong code PLC:** Gán giá trị đặt vào `PSet_*` (nếu có) hoặc kích xung 1 chu kỳ vào các bit lệnh `PCmd_* = 1` để xóa lỗi (`PCmd_Reset`), xác nhận cảnh báo (`PCmd_*Ack`) hoặc ức chế cảnh báo (`PCmd_*Suppress`). Khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi.
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Out_Live`, `Out_Snap`, `Val_DelayPctLive`, `Val_DelayPctSnap`, `Val_SnapInit`, `Sts_Snapped`, `Sts_InpLive`, `Sts_GateLive`, `Sts_InpSnap`, `Sts_GateSnap`, `Sts_GateSrc1Live`, `Sts_GateSrc2Live`, `Sts_GateSrc3Live`, `Sts_GateSrc4Live`, `Sts_GateSrc1Snap`, `Sts_GateSrc2Snap`, `Sts_GateSrc3Snap`, `Sts_GateSrc4Snap`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 16 Biến Đầu Vào (`Input Parameters`) của `P_Logic`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp_0…Inp_7`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 8 tín hiệu logic đầu vào (Inp_0 đến Inp_7) đưa vào các cổng logic tổ hợp của khối P_Logic |
| **`Inp_Hold`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Chân bắt sườn lên (0 -> 1) để chụp nhanh (Snapshot) toàn bộ trạng thái đầu vào và cổng logic của chu kỳ quét trước đó |
| **`Inp_Reset`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Cfg_UseInpHold`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép chụp băng hình trạng thái (Snapshot) khi chân Inp_Hold chuyển từ 0 lên 1 |
| **`Cfg_UsePCmd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép chụp băng hình trạng thái (Snapshot) khi Chương trình PLC kích xung PCmd_Snap |
| **`Cfg_UseOCmd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép chụp băng hình trạng thái (Snapshot) khi Người vận hành nhấn nút OCmd_Snap trên HMI |
| **`Cfg_UseOut01`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tự động chụp băng hình trạng thái (Snapshot) ngay tại khoảnh khắc ngõ ra Out_Live chuyển từ 0 lên 1 (sườn lên) |
| **`Cfg_UseOut10`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tự động chụp băng hình trạng thái (Snapshot) ngay tại khoảnh khắc ngõ ra Out_Live chuyển từ 1 về 0 (sườn xuống) |
| **`Cfg_TSonSnap`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tự động ghi lại nhãn thời gian thực (Timestamp) tại khoảnh khắc xảy ra chụp băng hình Snapshot |
| **`Cfg_SnapOver`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Lần chụp băng hình (Snapshot) mới sẽ ghi đè tự động lên lần chụp cũ; 0 = Giữ cố định băng hình đầu tiên cho đến khi nhấn Reset |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_OnDelay`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động Bật ngõ ra (Output ON Delay Time, tính bằng giây) |
| **`Cfg_OffDelay`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả Tắt ngõ ra (Output OFF Delay Time, tính bằng giây) |
| **`PCmd_Snap`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chụp nhanh (Snapshot) toàn bộ trạng thái đầu vào và cổng logic tại thời điểm hiện tại |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |

## 3. Bảng chi tiết 28 Biến Đầu Ra (`Output Parameters`) của `P_Logic`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Out_Live`** | `BOOL` | `??` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Kết quả đầu ra trực tiếp (Live Output) của mạch logic tổ hợp P_Logic sau bộ định thời trễ |
| **`Out_Snap`** | `BOOL` | `??` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Trạng thái đầu ra của mạch logic tổ hợp P_Logic tại thời điểm chụp băng hình (Snapshot) |
| **`Val_DelayPctLive`** | `DINT` | `??` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Phần trăm hoàn thành bộ định thời trễ Bật/Tắt (On-Delay / Off-Delay) trực tiếp hiện tại (0..100%) |
| **`Val_DelayPctSnap`** | `DINT` | `??` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Phần trăm hoàn thành bộ định thời trễ Bật/Tắt tại thời điểm chụp băng hình (Snapshot) |
| **`Val_SnapInit`** | `DINT` | `??` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã nguyên nhân kích hoạt chụp băng hình Snapshot: 1 = Lệnh OCmd từ HMI, 2 = Lệnh PCmd từ PLC, 3 = Ngõ ra chuyển 0->1, 4 = Ngõ ra chuyển 1->0, 5 = Tín hiệu Inp_Hold |
| **`Sts_Snapped`** | `BOOL` | `??` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đã kích hoạt chụp băng hình (Snapshot) lưu lại trạng thái; 0 = Đang hiển thị trạng thái trực tiếp (Live) |
| **`Sts_InpLive`** | `SINT` | `2#0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit (.0 đến .7) biểu thị trạng thái trực tiếp (Live) của 8 đầu vào logic Inp_0..Inp_7 |
| **`Sts_GateLive`** | `SINT` | `2#0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit (.0 đến .7) biểu thị kết quả trực tiếp (Live) của 8 cổng logic Gate 0..Gate 7 |
| **`Sts_InpSnap`** | `SINT` | `2#0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit (.0 đến .7) lưu trạng thái chụp băng hình (Snapshot) của 8 đầu vào logic Inp_0..Inp_7 |
| **`Sts_GateSnap`** | `SINT` | `2#0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit (.0 đến .7) lưu kết quả chụp băng hình (Snapshot) của 8 cổng logic Gate 0..Gate 7 |
| **`Sts_GateSrc1Live`** | `SINT` | `2#0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit biểu thị trạng thái dây nối trực tiếp (Live) cho Ngõ vào #1 của từng cổng logic (bit thứ m tương ứng cổng m) |
| **`Sts_GateSrc2Live`** | `SINT` | `2#0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit biểu thị trạng thái dây nối trực tiếp (Live) cho Ngõ vào #2 của từng cổng logic (bit thứ m tương ứng cổng m) |
| **`Sts_GateSrc3Live`** | `SINT` | `2#0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit biểu thị trạng thái dây nối trực tiếp (Live) cho Ngõ vào #3 của từng cổng logic (bit thứ m tương ứng cổng m) |
| **`Sts_GateSrc4Live`** | `SINT` | `2#0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit biểu thị trạng thái dây nối trực tiếp (Live) cho Ngõ vào #4 của từng cổng logic (bit thứ m tương ứng cổng m) |
| **`Sts_GateSrc1Snap`** | `SINT` | `2#0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit lưu trạng thái chụp băng hình (Snapshot) cho Ngõ vào #1 của từng cổng logic (bit thứ m tương ứng cổng m) |
| **`Sts_GateSrc2Snap`** | `SINT` | `2#0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit lưu trạng thái chụp băng hình (Snapshot) cho Ngõ vào #2 của từng cổng logic (bit thứ m tương ứng cổng m) |
| **`Sts_GateSrc3Snap`** | `SINT` | `2#0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit lưu trạng thái chụp băng hình (Snapshot) cho Ngõ vào #3 của từng cổng logic (bit thứ m tương ứng cổng m) |
| **`Sts_GateSrc4Snap`** | `SINT` | `2#0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit lưu trạng thái chụp băng hình (Snapshot) cho Ngõ vào #4 của từng cổng logic (bit thứ m tương ứng cổng m) |
| **`Sts_OutInvLive`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái trực tiếp (Live) của ngõ ra sau cổng đảo nhưng trước bộ định thời TON/TOF |
| **`Sts_OutInvSnap`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái chụp băng hình (Snapshot) của ngõ ra sau cổng đảo nhưng trước bộ định thời TON/TOF |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_GateFunc`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Mã hàm logic của cổng (Cfg_GateFunc) nằm ngoài phạm vi hợp lệ (chỉ dùng 0..6) |
| **`Err_GateSrcPtr`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Con trỏ chọn nguồn đầu vào cổng logic (Cfg_GateSrcPtr) nằm ngoài phạm vi 0..15 |
| **`Err_GateSrcMask`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Mặt nạ chọn nguồn đầu vào cổng logic (Cfg_GateSrcMask) không hợp lệ |
| **`Err_OutSrcPtr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Con trỏ chọn nguồn ngõ ra (Cfg_OutSrcPtr) nằm ngoài phạm vi 0..15 |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị đặt bộ định thời gian (Timer Preset) nằm ngoài phạm vi hợp lệ 0..2.147.483 giây |
| **`P_Logic`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_Logic) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 35 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_Logic`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_0StText`** | `STRING_8` | `'OK'` | [Biến cấu hình thông số] Chuỗi văn bản hiển thị trên HMI khi trạng thái bằng 0 (ví dụ: "Closed", "Stopped", "Normal") |
| **`Cfg_1StText`** | `STRING_8` | `'TRIPPED'` | [Biến cấu hình thông số] Chuỗi văn bản hiển thị trên HMI khi trạng thái bằng 1 (ví dụ: "Open", "Running", "Tripped") |
| **`Cfg_Area`** | `STRING_8` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'Configurable Boolean Logic Block'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_GateFunc`** | `DINT[8]` | `{...}` | [Biến cấu hình thông số] Từ cấu hình mã hàm logic cho 8 cổng (Gate 0..7): 0 = Tắt, 1 = AND, 2 = OR, 3 = XOR, 4 = Biểu quyết đa số (Majority 2oo3), 5 = Chốt Set-Reset Flip-Flop, 6 = Bộ chọn 2:1 (Multiplexer) |
| **`Cfg_GateSrc1Inv`** | `SINT` | `2#0000_0000` | [Biến cấu hình thông số] Từ bit (8-bit cho 8 cổng) cấu hình đảo mức logic cho Ngõ vào #1 của từng cổng (1 = Đảo tín hiệu NOT) |
| **`Cfg_GateSrc1Mask`** | `SINT` | `2#0000_0000` | [Biến cấu hình thông số] Từ bit (8-bit cho 8 cổng) xác định Ngõ vào #1 có được sử dụng trong phép tính của cổng logic hay không (1 = Có dùng) |
| **`Cfg_GateSrc1Ptr`** | `DINT[8]` | `{...}` | [Biến cấu hình thông số] Con trỏ chọn tín hiệu đưa vào Ngõ vào #1 của từng cổng logic (0..7 = Đầu vào Inp_0..7, 8..15 = Ngõ ra cổng Gate 0..7) |
| **`Cfg_GateSrc2Inv`** | `SINT` | `2#0000_0000` | [Biến cấu hình thông số] Từ bit (8-bit cho 8 cổng) cấu hình đảo mức logic cho Ngõ vào #2 của từng cổng (1 = Đảo tín hiệu NOT) |
| **`Cfg_GateSrc2Mask`** | `SINT` | `2#0000_0000` | [Biến cấu hình thông số] Từ bit (8-bit cho 8 cổng) xác định Ngõ vào #2 có được sử dụng trong phép tính của cổng logic hay không (1 = Có dùng) |
| **`Cfg_GateSrc2Ptr`** | `DINT[8]` | `{...}` | [Biến cấu hình thông số] Con trỏ chọn tín hiệu đưa vào Ngõ vào #2 của từng cổng logic (0..7 = Đầu vào Inp_0..7, 8..15 = Ngõ ra cổng Gate 0..7) |
| **`Cfg_GateSrc3Inv`** | `SINT` | `2#0000_0000` | [Biến cấu hình thông số] Từ bit (8-bit cho 8 cổng) cấu hình đảo mức logic cho Ngõ vào #3 của từng cổng (1 = Đảo tín hiệu NOT) |
| **`Cfg_GateSrc3Mask`** | `SINT` | `2#0000_0000` | [Biến cấu hình thông số] Từ bit (8-bit cho 8 cổng) xác định Ngõ vào #3 có được sử dụng trong phép tính của cổng logic hay không (1 = Có dùng) |
| **`Cfg_GateSrc3Ptr`** | `DINT[8]` | `{...}` | [Biến cấu hình thông số] Con trỏ chọn tín hiệu đưa vào Ngõ vào #3 của từng cổng logic (0..7 = Đầu vào Inp_0..7, 8..15 = Ngõ ra cổng Gate 0..7) |
| **`Cfg_GateSrc4Inv`** | `SINT` | `2#0000_0000` | [Biến cấu hình thông số] Từ bit (8-bit cho 8 cổng) cấu hình đảo mức logic cho Ngõ vào #4 của từng cổng (1 = Đảo tín hiệu NOT) |
| **`Cfg_GateSrc4Mask`** | `SINT` | `2#0000_0000` | [Biến cấu hình thông số] Từ bit (8-bit cho 8 cổng) xác định Ngõ vào #4 có được sử dụng trong phép tính của cổng logic hay không (1 = Có dùng) |
| **`Cfg_GateSrc4Ptr`** | `DINT[8]` | `{...}` | [Biến cấu hình thông số] Con trỏ chọn tín hiệu đưa vào Ngõ vào #4 của từng cổng logic (0..7 = Đầu vào Inp_0..7, 8..15 = Ngõ ra cổng Gate 0..7) |
| **`Cfg_HasNav`** | `SINT` | `2#0000_0000` | [Biến cấu hình thông số] Từ bit (16-bit) bật nút điều hướng nhảy tới Faceplate nguyên nhân cho từng điều kiện trong 16 kênh (Bit = 1 là bật) |
| **`Cfg_InpTxt`** | `STRING_20[8]` | `{...}` | [Biến cấu hình thông số] Mảng 8 chuỗi văn bản mô tả tên của 8 tín hiệu đầu vào (Inp_0..7) trên màn hình HMI của khối P_Logic |
| **`Cfg_Label`** | `STRING_20` | `'Configurable Logic'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_NavTag`** | `STRING_NavTag[8]` | `{...}` | [Biến cấu hình thông số] Mảng 16 tên thẻ (Tagname) đích tương ứng với 16 nút điều hướng điều kiện liên động/cho phép trên HMI |
| **`Cfg_OutSrcInv`** | `BOOL` | `0` | [Biến cấu hình thông số] 1 = Đảo ngược mức logic của tín hiệu ngõ ra khối P_Logic (trước bộ định thời trễ) |
| **`Cfg_OutSrcPtr`** | `DINT` | `0` | [Biến cấu hình thông số] Con trỏ chọn tín hiệu đưa ra ngõ ra cuối cùng của khối P_Logic (0..7 = Lấy từ đầu vào Inp_0..7; 8..15 = Lấy từ ngõ ra cổng Gate 0..7) |
| **`Cfg_Tag`** | `STRING_20` | `'P_Logic'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`HMI_Const`** | `SINT[9]` | `{...}` | Cấu trúc hằng số nội bộ phục vụ truy xuất gián tiếp các tham số trên màn hình HMI |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_Logic'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`OCmd_Snap`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Chụp nhanh (Snapshot) toàn bộ trạng thái đầu vào và cổng logic tại thời điểm hiện tại |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Snap`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Snap (Chụp nhanh (Snapshot) toàn bộ trạng thái đầu vào và cổng logic tại thời điểm hiện tại), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`Val_LastGate`** | `DINT[8]` | `{...}` | [Giá trị thực tế đầu ra (Logic/HMI)] Chỉ số cổng logic cuối cùng có sử dụng kết quả của cổng này (0 = Không dùng, 1..8 = Cổng, 33 = Ngõ ra — dùng để vẽ đường dây trên HMI) |
| **`Val_LastInp`** | `DINT[8]` | `{...}` | [Giá trị thực tế đầu ra (Logic/HMI)] Chỉ số cổng logic cuối cùng có sử dụng chân đầu vào này (0 = Không dùng, 1..8 = Cổng 1..8, 33 = Ngõ ra — dùng để vẽ đường dây trên HMI) |
| **`Val_SnapTS`** | `DINT[7]` | `{...}` | [Giá trị thực tế đầu ra (Logic/HMI)] Nhãn thời gian (Timestamp) lúc chụp băng hình Snapshot: [0]=Năm, [1]=Tháng, [2]=Ngày, [3]=Giờ, [4]=Phút, [5]=Giây, [6]=Micro-giây |
