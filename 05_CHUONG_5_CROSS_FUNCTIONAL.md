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
**Thuộc chương:** Chương 5: Khối chức năng dùng chung, Liên động & Cảnh báo (Cross Functional)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Cross Functional 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_Alarm`:** **17** Biến đầu vào (`Input Parameters`) | **16** Biến đầu ra (`Output Parameters`) | **36** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_Alarm`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_Exists`, `Cfg_ResetReqd`, `Cfg_AckReqd`, `Cfg_AllowShelve`, `Cfg_AllowDisable`, `Cfg_Severity`, `Cfg_MaxShelfT`, `Cfg_AlmMinOnT`
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Cond`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_Alarm` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PCmd_Reset`, `PCmd_Ack`, `PCmd_Suppress`, `PCmd_Unsuppress`, `PCmd_Unshelve`
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Val_Notify`, `Val_SecToUnshelve`, `Val_MinToUnshelve`, `Sts_AlmInh`, `Sts_RdyReset`, `Sts_RdyAck`, `Sts_Err`
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 17 Biến Đầu Vào (`Input Parameters`) của `P_Alarm`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Pin/Param | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp`** | `BOOL` | `1` | Pin/Param | Alarm condition input 1 = alarm. |
| **`Inp_Reset`** | `BOOL` | `0` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] Chân đầu vào dùng để xóa (Reset) cảnh báo từ chương trình PLC (1 = Xóa cảnh báo). |
| **`Cfg_Exists`** | `BOOL` | `1` | Pin/Param | [Biến cấu hình thông số] 1 = Alarm configured to exist. 0 = Does not exist, don't scan. |
| **`Cfg_ResetReqd`** | `BOOL` | `0` | Pin/Param | [Biến cấu hình thông số] 1 = Alarm is latched when it occurs, and a reset command is required to clear the alarm after the abnormal condition clears. 0 = Not latched, no reset required, alarm clears when abnormal condition clears. |
| **`Cfg_AckReqd`** | `BOOL` | `1` | Pin/Param | [Biến cấu hình thông số] 1 = Xác nhận (Acknowledge) required for alarm. |
| **`Cfg_AllowShelve`** | `BOOL` | `1` | Pin/Param | [Biến cấu hình thông số] 1 = Allow operator to shelve alarm. |
| **`Cfg_AllowDisable`** | `BOOL` | `1` | Pin/Param | [Biến cấu hình thông số] 1 = Allow maintenance to disable alarm. |
| **`Cfg_Severity`** | `DINT` | `750` | Pin/Param | [Biến cấu hình thông số] These parameters determine the severity of the alarm. This drives the color and symbol that are used to indicate alarm status on the faceplate and global object. The following are valid values: 1…250 = Low 251…500 = Medium 501…750 = High 751…1000 = Urgent IMPORTANT: For FactoryTalk View software, version 7.0, this severity parameter drives only the indication on the global object and faceplate. The Alarm and Event definition severity drives the color and symbol that is used on the alarm banner and alarm summary as well as the value returned by FactoryTalk Alarm and Event display commands. |
| **`Cfg_MaxShelfT`** | `DINT` | `480` | Pin/Param | [Biến cấu hình thông số] Auto unshelve after being shelved this long (minutes) (type zero for maximum). |
| **`Cfg_AlmMinOnT`** | `DINT` | `5` | Pin/Param | [Biến cấu hình thông số] Minimum time alarm output stays on (seconds). |
| **`PCfg_AllowExist`** | `BOOL` | `1` | Pin/Param | 1 = Alarm can be configured to exist. 0 = Other configurations make alarm meaningless. EXAMPLE: For a motor configured to have no run feedback, this bit is cleared to 0 on the Fail to Start and Fail to Stop alarms. These conditions cannot exist, so do not scan these alarms. |
| **`PCmd_Reset`** | `BOOL` | `0` | Pin/Param | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_Reset = 1 để xóa tất cả các cảnh báo chốt đang chờ Reset (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_Ack`** | `BOOL` | `0` | Pin/Param | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_Suppress`** | `BOOL` | `0` | Pin/Param | [Lệnh từ Chương trình PLC (Xung 1-shot)] • Set PCmd_Ức chế (Suppress) to 1 to suppress alarm • Set PCmd_Gỡ bỏ ức chế (Unsuppress) to 1 to unsuppress alarm • These parameters reset automatically |
| **`PCmd_Unsuppress`** | `BOOL` | `0` | Pin/Param | [Lệnh từ Chương trình PLC (Xung 1-shot)] • Set PCmd_Ức chế (Suppress) to 1 to suppress alarm • Set PCmd_Gỡ bỏ ức chế (Unsuppress) to 1 to unsuppress alarm • These parameters reset automatically |
| **`PCmd_Unshelve`** | `BOOL` | `0` | Pin/Param | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |

## 3. Bảng chi tiết 16 Biến Đầu Ra (`Output Parameters`) của `P_Alarm`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Pin/Param | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Val_Notify`** | `SINT` | `0` | Pin/Param | [Giá trị thực tế đầu ra (Logic/HMI)] Current alarm level and acknowledgement (enumeration): 0 = No alarm 1 = Alarm cleared: a reset or acknowledge is required 2 = Low (acknowledged) 3 = Low (unacknowledged) 4 = Medium (acknowledged) 5 = Medium (unacknowledged) 6 = High (acknowledged) 7 = High (unacknowledged) 8 = Urgent (acknowledged) 9 = Urgent (unacknowledged) |
| **`Val_SecToUnshelve`** | `SINT` | `0` | Pin/Param | [Giá trị thực tế đầu ra (Logic/HMI)] Number of seconds until this alarm unshelves itself (mmm:ss). |
| **`Val_MinToUnshelve`** | `INT` | `0` | Pin/Param | [Giá trị thực tế đầu ra (Logic/HMI)] Number of minutes until this alarm unshelves itself (mmm:ss). |
| **`Alm`** | `BOOL` | `0` | Pin/Param | Alarm status: 1 = In alarm. |
| **`Ack`** | `BOOL` | `1` | Pin/Param | Alarm acknowledged status: 1 = Ack received. |
| **`Shelved`** | `BOOL` | `0` | Pin/Param | 1 = Alarm has been shelved by operator. |
| **`Suppressed`** | `BOOL` | `0` | Pin/Param | 1 = Alarm has been suppressed by program. |
| **`Disabled`** | `BOOL` | `0` | Pin/Param | 1 = Alarm has been disabled by maintenance. |
| **`Sts_AlmInh`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1=Alarm has been shelved or disabled, display "I" icon |
| **`Sts_RdyReset`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1=Alarm has been shelved or disabled, display "I" icon |
| **`Sts_RdyAck`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1=Alarm has been shelved or disabled, display "I" icon |
| **`Sts_Err`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1 = Error in configuration: see detail Err_ bits for reason. |
| **`Err_Timer`** | `BOOL` | `0` | Pin/Param | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in configuration: Invalid timer preset (use 0…2,147,483). |
| **`Err_Severity`** | `BOOL` | `0` | Pin/Param | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in configuration: Invalid Severity (use 1…1000). |
| **`P_Alarm`** | `BOOL` | `0` | Pin/Param | Unique Parameter name for auto-discovery. |

## 4. Bảng chi tiết 36 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_Alarm`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Cond`** | `STRING_20` | `'Alarm Condition'` | [Biến cấu hình thông số] Alarm Condition Text (e.g., "High-High PV") |
| **`Cfg_Tag`** | `STRING_20` | `'P_Alarm'` | [Biến cấu hình thông số] Tagname for display on HMI. This string shows in the title bar of the faceplate. |
| **`Com_AE`** | `INT` | `2#0000_0000_0000_0010` | Communication with HMI / Alarm/Event Server (see bit descriptions) |
| **`Com_AE.0`** | `BOOL` | `0` | Alarm to AE |
| **`Com_AE.1`** | `BOOL` | `1` | Acked from AE Status, Ack to AE Control |
| **`Com_AE.2`** | `BOOL` | `0` | (not used) |
| **`Com_AE.3`** | `BOOL` | `0` | Shelved from AE Status |
| **`Com_AE.4`** | `BOOL` | `0` | Shelve to AE Control |
| **`Com_AE.5`** | `BOOL` | `0` | Gỡ bỏ tạm hoãn (Unshelve) to AE Control |
| **`Com_AE.6`** | `BOOL` | `0` | Suppressed from AE Status |
| **`Com_AE.7`** | `BOOL` | `0` | Ức chế (Suppress) to AE Control |
| **`Com_AE.8`** | `BOOL` | `0` | Gỡ bỏ ức chế (Unsuppress) to AE Control |
| **`Com_AE.9`** | `BOOL` | `0` | Disabled from AE Status |
| **`Com_AE.10`** | `BOOL` | `0` | Disable to AE Control |
| **`Com_AE.11`** | `BOOL` | `0` | Enable to AE Control |
| **`Com_AE.12`** | `BOOL` | `0` | (not used) |
| **`Com_AE.13`** | `BOOL` | `0` | (not used) |
| **`Com_AE.14`** | `BOOL` | `0` | (not used) |
| **`Com_AE.15`** | `BOOL` | `0` | (not used) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_Alarm'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI và phần mềm thông tin |
| **`MCmd_Disable`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Disable alarm (inhibits new alarm) |
| **`MCmd_Enable`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Enable alarm (allows new alarm) |
| **`MCmd_Test`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Test alarm (issued for minimum alarm time) |
| **`MRdy_Disable`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Disable (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_Enable`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Enable (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_Test`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Test (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`OCmd_Ack`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xác nhận (Acknowledge) alarm |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset latched alarm |
| **`OCmd_Shelve`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Shelve Alarm (inhibits new alarm) |
| **`OCmd_Unshelve`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Gỡ bỏ tạm hoãn (Unshelve) alarm (allows new alarm) |
| **`ORdy_Ack`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Ack (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Shelve`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Shelve (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Unshelve`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Gỡ bỏ tạm hoãn (Unshelve) (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_Gate`
**Thuộc chương:** Chương 5: Khối chức năng dùng chung, Liên động & Cảnh báo (Cross Functional)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Cross Functional 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_Gate`:** **6** Biến đầu vào (`Input Parameters`) | **6** Biến đầu ra (`Output Parameters`) | **5** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_Gate`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_Gate`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_GateDly`, `Cfg_OnDly`, `Cfg_OffDly`
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_GateCond`, `Cfg_InpCond`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_Gate` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Sts_Gate`, `Sts_Err`
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 6 Biến Đầu Vào (`Input Parameters`) của `P_Gate`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Pin/Param | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp`** | `BOOL` | `0` | Pin/Param | Input Condition. This is the result of threshold comparison usually performed by the parent object. |
| **`Inp_Gate`** | `BOOL` | `1` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] Gate input used for status detection. When set to 1, input monitoring is enabled. When enabled, on delay and off delay times are applied after the gate delay time. When set to 0, input detection is disabled and the status output is forced off. |
| **`Cfg_GateDly`** | `REAL` | `0` | Pin/Param | [Biến cấu hình thông số] The amount of time (in seconds) the gate input must be turned on for status outputs to be enabled. On delays and off delays are applied to the input after the gate delay is completed. |
| **`Cfg_OnDly`** | `REAL` | `0` | Pin/Param | [Biến cấu hình thông số] Determines the minimum time (in seconds) the input must remain on for the status to be set. On-delay times are typically used to avoid unnecessary status indication or alarm when a value only briefly overshoots its threshold. |
| **`Cfg_OffDly`** | `REAL` | `0` | Pin/Param | [Biến cấu hình thông số] Determines the amount of time (in seconds) the input must be cleared in order to clear the status. Off delay times are typically used to reduce chattering status indicators or alarms. |

## 3. Bảng chi tiết 6 Biến Đầu Ra (`Output Parameters`) của `P_Gate`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Pin/Param | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Out`** | `BOOL` | `0` | Pin/Param | Output after On Delay, Off Delay, enabled by Gate. |
| **`Sts_Gate`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1 = Gate Delay done, Output is enabled. |
| **`Sts_Err`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1 = Error in Config (see detail Err_ bits for reason), display icon. |
| **`Err_Timer`** | `BOOL` | `0` | Pin/Param | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: On Delay, Off Delay, Gate Delay Time Invalid (use 0 …2,147,483 seconds). |
| **`P_Gate`** | `BOOL` | `0` | Pin/Param | Unique parameter name for auto-discovery. |

## 4. Bảng chi tiết 5 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_Gate`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_GateCond`** | `STRING_20` | `'Gating Condition'` | [Biến cấu hình thông số] Displayed text of P_Gate's Gating (Inp_Gate) Condition |
| **`Cfg_InpCond`** | `STRING_20` | `'Input Condition'` | [Biến cấu hình thông số] Displayed text of P_Gate's Input (Inp) Condition |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_Gate'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI và phần mềm thông tin |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_Intlk`
**Thuộc chương:** Chương 5: Khối chức năng dùng chung, Liên động & Cảnh báo (Cross Functional)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Cross Functional 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_Intlk`:** **11** Biến đầu vào (`Input Parameters`) | **8** Biến đầu ra (`Output Parameters`) | **13** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_Intlk`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_Intlk00…Inp_Intlk15`, `Inp_BypActive`, `Inp_LatchDefeat`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_HasMoreObj`, `Cfg_OKState`, `Cfg_Latched`, `Cfg_Bypassable`, `Cfg_HasNav`
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_CondTxt`, `Cfg_Desc`, `Cfg_Label`, `Cfg_NavTag`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_Intlk` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PCmd_Reset`
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Sts_IntlkOK`, `Sts_NBIntlkOK`, `Sts_BypActive`, `Sts_RdyReset`, `Sts_Intlk`, `Sts_FirstOut`
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 11 Biến Đầu Vào (`Input Parameters`) của `P_Intlk`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Pin/Param | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_Intlk00…Inp_Intlk15`** | `BOOL` | `0` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] Interlock condition inputs - If Input is not in the OK state that is defined by Cfg_OKState and not bypassed (if able to be bypassed per Cfg_Bypassable), then the appropriate interlock status (Sts_IntlkOK and Sts_NBIntlkOK) are set to 0 to stop the equipment. |
| **`Inp_BypActive`** | `BOOL` | `0` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] 1 = Interlock Bypassing is active. |
| **`Inp_LatchDefeat`** | `BOOL` | `0` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] Latch Defeat 1 = Do not latch inputs even if configured for latching. |
| **`Inp_Reset`** | `BOOL` | `0` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] Input parameter that is used to programmatically reset latched interlocks. When set to 1, all latched interlocks that require a reset are reset. |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Pin/Param | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_OKState`** | `INT` | `2#0000_0000_0000_0000` | Pin/Param | [Biến cấu hình thông số] Bits determine the state (0 or 1) of each input that is OK to run. |
| **`Cfg_Latched`** | `INT` | `2#0000_0000_0000_0000` | Pin/Param | [Biến cấu hình thông số] Set bits determine the conditions that are latched (sealed in). |
| **`Cfg_Bypassable`** | `INT` | `2#0000_0000_0000_0000` | Pin/Param | [Biến cấu hình thông số] Set bits determine the conditions that can be bypassed. |
| **`Cfg_HasNav`** | `INT` | `2#0000_0000_0000_0000` | Pin/Param | [Biến cấu hình thông số] Set bits determine the Navigation buttons that are enabled. |
| **`PCmd_Reset`** | `BOOL` | `0` | Pin/Param | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_Reset = 1 để reset latched interlocks (Biến xung tự động xóa về 0 sau khi thực thi). |

## 3. Bảng chi tiết 8 Biến Đầu Ra (`Output Parameters`) của `P_Intlk`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Pin/Param | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Sts_IntlkOK`** | `BOOL` | `1` | Pin/Param | [Cờ báo trạng thái logic (Status)] Overall Interlock Status: 1 = OK to run 0 = Stop |
| **`Sts_NBIntlkOK`** | `BOOL` | `1` | Pin/Param | [Cờ báo trạng thái logic (Status)] Interlock (cannot be bypassed) Status (1 = All NB interlocks OK to run). |
| **`Sts_BypActive`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1 = Interlock Bypassing Active (interlocks that can be bypassed are ignored). |
| **`Sts_RdyReset`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1=A latched interlock (returned to OK) is ready to be reset |
| **`Sts_Intlk`** | `INT` | `2#0000_0000_0000_0000` | Pin/Param | [Cờ báo trạng thái logic (Status)] Individual Interlock Status (1=Stop, 0=OK) |
| **`Sts_FirstOut`** | `INT` | `2#0000_0000_0000_0000` | Pin/Param | [Cờ báo trạng thái logic (Status)] Interlock First Out Status (bit 1 is First Not-OK condition) |
| **`P_Intlk`** | `BOOL` | `0` | Pin/Param | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 13 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_Intlk`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_8` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_CondTxt`** | `STRING_20[16]` | `{...}` | [Biến cấu hình thông số] Short HMI descriptions of 16 interlock conditions. |
| **`Cfg_Desc`** | `STRING_40` | `'Interlocks with First Out'` | [Biến cấu hình thông số] Description for display on HMI. This string is shown in the title bar of the faceplate. |
| **`Cfg_Label`** | `STRING_20` | `'Interlocks'` | [Biến cấu hình thông số] Label for graphic symbol that is displayed on HMI. This string appears on the graphic symbol. |
| **`Cfg_NavTag`** | `STRING_NavTag[16]` | `{...}` | [Biến cấu hình thông số] Tag names for destinations of 16 navigation buttons. IMPORTANT: This function does not apply to FactoryTalk View ME Software. |
| **`Cfg_Tag`** | `STRING_20` | `'P_Intlk'` | [Biến cấu hình thông số] Tag name for display on the HMI. This string is shown in the title bar of the faceplate. |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_Intlk'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate |
| **`MSet_Bypass`** | `INT` | `2#0000_0000_0000_0000` | [Giá trị đặt từ tab Bảo trì (Maintenance)] Individual condition Maintenance Bypass toggles |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset Latched Interlocks |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`Val_FirstOutTxt`** | `STRING_Cond` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Text Description of First-Out Condition |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_IntlkAdv`
**Thuộc chương:** Chương 5: Khối chức năng dùng chung, Liên động & Cảnh báo (Cross Functional)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Cross Functional 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_IntlkAdv`:** **15** Biến đầu vào (`Input Parameters`) | **12** Biến đầu ra (`Output Parameters`) | **15** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_IntlkAdv`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_Intlk00…Inp_Intlk15`, `Inp_IOFault`, `Inp_Avail`, `Inp_BypActive`, `Inp_LatchDefeat`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_HasMoreObj`, `Cfg_OKState`, `Cfg_Latched`, `Cfg_Bypassable`, `Cfg_HasNav`, `Cfg_Type0…Cfg_Type15`, `Cfg_HasType`
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_CondTxt`, `Cfg_Desc`, `Cfg_Label`, `Cfg_NavTag`, `Cfg_Tag`, `Cfg_TypeDesc`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_IntlkAdv` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PCmd_Reset`
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Sts_IntlkOK`, `Sts_NBIntlkOK`, `Sts_BypActive`, `Sts_RdyReset`, `Sts_LatchMask`, `Sts_BypassMask`, `Sts_Intlk`, `Sts_Avail`, `Sts_FirstOut`
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 15 Biến Đầu Vào (`Input Parameters`) của `P_IntlkAdv`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Pin/Param | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_Intlk00…Inp_Intlk15`** | `BOOL` | `0` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] Interlock condition inputs - If Input is not in the OK state that is defined by Cfg_OKState and not bypassed (if able to be bypassed per Cfg_Bypassable), then the appropriate interlock status (Sts_IntlkOK and Sts_NBIntlkOK) are set to 0 to stop the equipment. |
| **`Inp_IOFault`** | `INT` | `2#0000_0000_0000_0000` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] Interlock condition inputs - If Input is not in the OK state that is defined by Cfg_OKState and not bypassed (if able to be bypassed per Cfg_Bypassable), then the appropriate interlock status (Sts_IntlkOK and Sts_NBIntlkOK) are set to 0 to stop the equipment. |
| **`Inp_Avail`** | `BOOL` | `2#1` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] Interlock condition inputs - If Input is not in the OK state that is defined by Cfg_OKState and not bypassed (if able to be bypassed per Cfg_Bypassable), then the appropriate interlock status (Sts_IntlkOK and Sts_NBIntlkOK) are set to 0 to stop the equipment. |
| **`Inp_BypActive`** | `BOOL` | `0` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] Interlock condition inputs - If Input is not in the OK state that is defined by Cfg_OKState and not bypassed (if able to be bypassed per Cfg_Bypassable), then the appropriate interlock status (Sts_IntlkOK and Sts_NBIntlkOK) are set to 0 to stop the equipment. |
| **`Inp_LatchDefeat`** | `BOOL` | `0` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] Interlock condition inputs - If Input is not in the OK state that is defined by Cfg_OKState and not bypassed (if able to be bypassed per Cfg_Bypassable), then the appropriate interlock status (Sts_IntlkOK and Sts_NBIntlkOK) are set to 0 to stop the equipment. |
| **`Inp_Reset`** | `BOOL` | `0` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] Interlock condition inputs - If Input is not in the OK state that is defined by Cfg_OKState and not bypassed (if able to be bypassed per Cfg_Bypassable), then the appropriate interlock status (Sts_IntlkOK and Sts_NBIntlkOK) are set to 0 to stop the equipment. |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Pin/Param | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_OKState`** | `INT` | `2#0000_0000_0000_0000` | Pin/Param | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_Latched`** | `INT` | `2#0000_0000_0000_0000` | Pin/Param | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_Bypassable`** | `INT` | `2#0000_0000_0000_0000` | Pin/Param | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_HasNav`** | `INT` | `2#0000_0000_0000_0000` | Pin/Param | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_Type0…Cfg_Type15`** | `SINT` | `6` | Pin/Param | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_HasType`** | `SINT` | `2#1111_1111` | Pin/Param | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`PCmd_Reset`** | `BOOL` | `0` | Pin/Param | [Lệnh từ Chương trình PLC (Xung 1-shot)] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |

## 3. Bảng chi tiết 12 Biến Đầu Ra (`Output Parameters`) của `P_IntlkAdv`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Pin/Param | [Chân nối tầng Rung] Biến `EnableOut` (kiểu `BOOL`) của khối lệnh. |
| **`Ref_Inputs`** | `INT` | `0` | Pin/Param | [Tham chiếu InOut nối tới cấu trúc ngoài] Biến `Ref_Inputs` (kiểu `INT`) của khối lệnh. |
| **`Sts_IntlkOK`** | `BOOL` | `1` | Pin/Param | [Cờ báo trạng thái logic (Status)] Biến `Sts_IntlkOK` (kiểu `BOOL`) của khối lệnh. |
| **`Sts_NBIntlkOK`** | `BOOL` | `1` | Pin/Param | [Cờ báo trạng thái logic (Status)] Biến `Sts_NBIntlkOK` (kiểu `BOOL`) của khối lệnh. |
| **`Sts_BypActive`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] Biến `Sts_BypActive` (kiểu `BOOL`) của khối lệnh. |
| **`Sts_RdyReset`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] Biến `Sts_RdyReset` (kiểu `BOOL`) của khối lệnh. |
| **`Sts_LatchMask`** | `INT` | `2#0000_0000_0000_0000` | Pin/Param | [Cờ báo trạng thái logic (Status)] Biến `Sts_LatchMask` (kiểu `INT`) của khối lệnh. |
| **`Sts_BypassMask`** | `INT` | `2#0000_0000_0000_0000` | Pin/Param | [Cờ báo trạng thái logic (Status)] Biến `Sts_BypassMask` (kiểu `INT`) của khối lệnh. |
| **`Sts_Intlk`** | `INT` | `2#0000_0000_0000_0000` | Pin/Param | [Cờ báo trạng thái logic (Status)] Biến `Sts_Intlk` (kiểu `INT`) của khối lệnh. |
| **`Sts_Avail`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] Biến `Sts_Avail` (kiểu `BOOL`) của khối lệnh. |
| **`Sts_FirstOut`** | `INT` | `2#0000_0000_0000_0000` | Pin/Param | [Cờ báo trạng thái logic (Status)] Biến `Sts_FirstOut` (kiểu `INT`) của khối lệnh. |
| **`P_Intlk`** | `BOOL` | `0` | Pin/Param | Biến `P_Intlk` (kiểu `BOOL`) của khối lệnh. |

## 4. Bảng chi tiết 15 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_IntlkAdv`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Biến `Cfg_Area` (kiểu `STRING_Area`) của khối lệnh. |
| **`Cfg_CondTxt`** | `STRING_Cond[16]` | `{...}` | [Biến cấu hình thông số] Biến `Cfg_CondTxt` (kiểu `STRING_Cond[16]`) của khối lệnh. |
| **`Cfg_Desc`** | `STRING_40` | `'Advanced Interlocks with First Out'` | [Biến cấu hình thông số] Biến `Cfg_Desc` (kiểu `STRING_40`) của khối lệnh. |
| **`Cfg_Label`** | `STRING_20` | `'Advanced Interlocks'` | [Biến cấu hình thông số] Biến `Cfg_Label` (kiểu `STRING_20`) của khối lệnh. |
| **`Cfg_NavTag`** | `STRING_NavTag[16]` | `{...}` | [Biến cấu hình thông số] Navigation Tag |
| **`Cfg_Tag`** | `STRING_20` | `'P_IntlkAdv'` | [Biến cấu hình thông số] Navigation Tag |
| **`Cfg_TypeDesc`** | `STRING_16[8]` | `{...}` | [Biến cấu hình thông số] Navigation Tag |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Navigation Tag |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Navigation Tag |
| **`HMI_Type`** | `STRING_16` | `'P_IntlkAdv'` | [Biến nội bộ giao diện HMI Faceplate] Navigation Tag |
| **`MSet_Bypass`** | `INT` | `2#0000_0000_0000_0000` | [Giá trị đặt từ tab Bảo trì (Maintenance)] Navigation Tag |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Navigation Tag |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] Navigation Tag |
| **`Val_FirstOutTxt`** | `STRING_Cond` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Navigation Tag |
| **`Val_FirstOutType`** | `STRING_16` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Navigation Tag |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_CmdSrc`
**Thuộc chương:** Chương 5: Khối chức năng dùng chung, Liên động & Cảnh báo (Cross Functional)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Cross Functional 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_CmdSrc`:** **38** Biến đầu vào (`Input Parameters`) | **28** Biến đầu ra (`Output Parameters`) | **2** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_CmdSrc`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_Hand`, `Inp_Ovrd`, `Inp_ExtInh`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_HasOper`, `Cfg_HasOperLocked`, `Cfg_HasProg`, `Cfg_HasProgLocked`, `Cfg_HasExt`, `Cfg_HasMaint`, `Cfg_HasMOoS`, `Cfg_OvrdOverLock`, `Cfg_ExtOverLock`, `Cfg_ProgPwrUp`, `Cfg_ProgNormal`, `Cfg_PCmdPriority`, `Cfg_PCmdProgAsLevel`, `Cfg_PCmdLockAsLevel`, `Cfg_ExtAcqAsLevel`, `Cfg_ExtAcqAsLevel`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_CmdSrc` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PCmd_Oper`, `PCmd_Prog`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Sts_eSrc`, `Sts_bSrc`, `Sts_Hand`, `Sts_OoS`, `Sts_Maint`, `Sts_Ovrd`, `Sts_Ext`, `Sts_Prog`, `Sts_ProgLocked`, `Sts_Oper`, `Sts_OperLocked`, `Sts_ProgOperSel`, `Sts_ProgOperLock`, `Sts_Normal`, `Sts_ExtReqInh`, `Sts_ProgReqInh`, `Sts_MAcqRcvd`
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 38 Biến Đầu Vào (`Input Parameters`) của `P_CmdSrc`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Pin/Param | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_Hand`** | `BOOL` | `0` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] 1=Acquire Hand (typ. hardwired local), 0=Release Hand |
| **`Inp_Ovrd`** | `BOOL` | `0` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] 1=Acquire Override (higher priority program logic), 0=Release Override |
| **`Inp_ExtInh`** | `BOOL` | `0` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] 1=Inhibit External Acquisition, 0=Allow External Acquisition |
| **`Cfg_HasOper`** | `BOOL` | `1` | Pin/Param | [Biến cấu hình thông số] 1=Operator (unlocked) exists, can be selected |
| **`Cfg_HasOperLocked`** | `BOOL` | `1` | Pin/Param | [Biến cấu hình thông số] 1=Operator Locked exists, can be selected |
| **`Cfg_HasProg`** | `BOOL` | `1` | Pin/Param | [Biến cấu hình thông số] 1=Program (unlocked) exists, can be selected |
| **`Cfg_HasProgLocked`** | `BOOL` | `1` | Pin/Param | [Biến cấu hình thông số] 1=Program Locked exists, can be selected |
| **`Cfg_HasExt`** | `BOOL` | `0` | Pin/Param | [Biến cấu hình thông số] 1=External exists, can be selected |
| **`Cfg_HasMaint`** | `BOOL` | `1` | Pin/Param | [Biến cấu hình thông số] 1=Maintenance exists, can be selected |
| **`Cfg_HasMOoS`** | `BOOL` | `1` | Pin/Param | [Biến cấu hình thông số] 1=Maintenance Out of Service exists, can be selected |
| **`Cfg_OvrdOverLock`** | `BOOL` | `1` | Pin/Param | [Biến cấu hình thông số] 1=Override ưu tiên cao hơn Prog/Oper Lock, 0=don't override Lock |
| **`Cfg_ExtOverLock`** | `BOOL` | `0` | Pin/Param | [Biến cấu hình thông số] 1=External ưu tiên cao hơn Prog/Oper Lock, 0=don't override Lock |
| **`Cfg_ProgPwrUp`** | `BOOL` | `0` | Pin/Param | [Biến cấu hình thông số] 1=Power Up to Prog, 0=Power Up to Oper |
| **`Cfg_ProgNormal`** | `BOOL` | `0` | Pin/Param | [Biến cấu hình thông số] Normal Source: 1=Program if no requests; 0=Operator if no requests |
| **`Cfg_PCmdPriority`** | `BOOL` | `0` | Pin/Param | [Biến cấu hình thông số] Command Priority: 1=Program Cmds Win; 0=Operator Cmds Win |
| **`Cfg_PCmdProgAsLevel`** | `BOOL` | `0` | Pin/Param | [Biến cấu hình thông số] 1=PCmd_Prog used as a Level (1=Prog, 0=Oper) |
| **`Cfg_PCmdLockAsLevel`** | `BOOL` | `0` | Pin/Param | [Biến cấu hình thông số] 1=PCmd_Lock used as a Level (1=Lock, 0=Unlock) |
| **`Cfg_ExtAcqAsLevel`** | `BOOL` | `0` | Pin/Param | [Biến cấu hình thông số] Decimal |
| **`PCmd_Oper`** | `BOOL` | `0` | Pin/Param | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Operator (Prog to Oper) |
| **`PCmd_Prog`** | `BOOL` | `0` | Pin/Param | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Program (Oper to Prog) |
| **`PCmd_Lock`** | `BOOL` | `0` | Pin/Param | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa cứng chế độ Program (không cho phép Operator giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Pin/Param | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Unlock Program (allow Oper to acquire) |
| **`PCmd_Normal`** | `BOOL` | `0` | Pin/Param | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Normal (Oper or Prog) |
| **`MCmd_OoS`** | `BOOL` | `0` | Pin/Param | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để select Out Of Service |
| **`MCmd_IS`** | `BOOL` | `0` | Pin/Param | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để select In Service |
| **`MCmd_Acq`** | `BOOL` | `0` | Pin/Param | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Acquire (Oper/Prog/Ovrd to Maint) |
| **`MCmd_Rel`** | `BOOL` | `0` | Pin/Param | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Release (Maint to Oper/Prog/Ovrd) |
| **`OCmd_Oper`** | `BOOL` | `0` | Pin/Param | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để select Operator (Prog to Oper) |
| **`OCmd_Prog`** | `BOOL` | `0` | Pin/Param | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để select Program (Oper to Prog) |
| **`OCmd_Lock`** | `BOOL` | `0` | Pin/Param | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Lock Operator (disallow Program) |
| **`OCmd_Unlock`** | `BOOL` | `0` | Pin/Param | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Unlock Operator (allow Prog to acquire) |
| **`OCmd_Normal`** | `BOOL` | `0` | Pin/Param | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để select Normal (Oper or Prog) |
| **`XRdy_Acq`** | `BOOL` | `0` | Pin/Param | Decimal |
| **`XRdy_Rel`** | `BOOL` | `0` | Pin/Param | Decimal |
| **`XCmd_Acq`** | `BOOL` | `0` | Pin/Param | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Acquire (Oper/Prog to Ext) |
| **`XCmd_Rel`** | `BOOL` | `0` | Pin/Param | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Release (Ext to Oper/Prog) |
| **`Cfg_ExtAcqAsLevel`** | `BOOL` | `0` | Pin/Param | [Biến cấu hình thông số] 1 = XCmdAcq Used as Level (1 = Acquire, 0 = Release) |

## 3. Bảng chi tiết 28 Biến Đầu Ra (`Output Parameters`) của `P_CmdSrc`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Pin/Param | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Sts_eSrc`** | `INT` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] Selection enum: .8=Ext, .7=OoS, .6=Oper, .5=Prog, .4=Ovrd, .3=Maint, .2=Hand, .1=Norm, .0=Lock |
| **`Sts_bSrc`** | `INT` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] Active Selection bitmap (for HMI totem pole) |
| **`Sts_Hand`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1=Hand đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn OoS, Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_OoS`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1=Out of Service đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_Maint`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1=Maintenance đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ovrd, Ext, Prog, Oper) |
| **`Sts_Ovrd`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1=Override đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ext, Prog, Oper) |
| **`Sts_Ext`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1=External đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Prog, Oper) |
| **`Sts_Prog`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1=Program đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_ProgLocked`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1=Program đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Oper`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1=Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_OperLocked`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1=Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_ProgOperSel`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] Prog/Oper selection (latch) state, 1=Program, 0=Operator |
| **`Sts_ProgOperLock`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] Prog/Oper lock (latch) state, 1=Locked, 0=Unlocked |
| **`Sts_Normal`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1=Selection equals the Normal (Prog or Oper) |
| **`Sts_ExtReqInh`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1=External request inhibited, cannot Get to External from current state |
| **`Sts_ProgReqInh`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1=Program request inhibited, cannot Get to Program from current state |
| **`Sts_MAcqRcvd`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1=Maintenance Acquire command received this scan |
| **`MRdy_OoS`** | `BOOL` | `0` | Pin/Param | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_OoS (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_IS`** | `BOOL` | `0` | Pin/Param | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_IS (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_Acq`** | `BOOL` | `0` | Pin/Param | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Acq (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_Rel`** | `BOOL` | `0` | Pin/Param | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Oper`** | `BOOL` | `0` | Pin/Param | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Oper (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Prog`** | `BOOL` | `0` | Pin/Param | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Prog (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Lock`** | `BOOL` | `0` | Pin/Param | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Lock (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Unlock`** | `BOOL` | `0` | Pin/Param | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Normal`** | `BOOL` | `0` | Pin/Param | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Normal (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`P_CmdSrc`** | `BOOL` | `0` | Pin/Param | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 2 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_CmdSrc`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`HMI_Lib`** | `STRING_8` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Type`** | `STRING_12` | `P_CmdSrc'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI và phần mềm thông tin |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_Perm`
**Thuộc chương:** Chương 5: Khối chức năng dùng chung, Liên động & Cảnh báo (Cross Functional)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Cross Functional 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_Perm`:** **7** Biến đầu vào (`Input Parameters`) | **6** Biến đầu ra (`Output Parameters`) | **10** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_Perm`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_Perm00…Inp_Perm15`, `Inp_BypActive`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_OKState`, `Cfg_Bypassable`, `Cfg_HasMoreObj`, `Cfg_HasNav`
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_CondTxt`, `Cfg_Desc`, `Cfg_Label`, `Cfg_NavTag`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_Perm` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Sts_PermOK`, `Sts_NBPermOK`, `Sts_BypActive`, `Sts_Perm`
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 7 Biến Đầu Vào (`Input Parameters`) của `P_Perm`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Pin/Param | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_Perm00…Inp_Perm15`** | `BOOL` | `1` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] Permissive Condition [00…15]. |
| **`Inp_BypActive`** | `BOOL` | `0` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] 1=Permissive Bypassing is currently active |
| **`Cfg_OKState`** | `INT` | `2#1111_1111_1111_1111` | Pin/Param | [Biến cấu hình thông số] Bits indicate which state (0 or 1) of each input is OK to start |
| **`Cfg_Bypassable`** | `INT` | `2#0000_0000_0000_0000` | Pin/Param | [Biến cấu hình thông số] Set bits indicate which conditions can be bypassed |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Pin/Param | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_HasNav`** | `INT` | `2#0000_0000_0000_0000` | Pin/Param | [Biến cấu hình thông số] Set bits indicate which Navigation buttons are enabled |

## 3. Bảng chi tiết 6 Biến Đầu Ra (`Output Parameters`) của `P_Perm`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Pin/Param | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Sts_PermOK`** | `BOOL` | `1` | Pin/Param | [Cờ báo trạng thái logic (Status)] Overall Permissive Status (1=OK to start) |
| **`Sts_NBPermOK`** | `BOOL` | `1` | Pin/Param | [Cờ báo trạng thái logic (Status)] Non-Bypassable Permissive Status (1 =all non- bypassable Permissives OK to start) |
| **`Sts_BypActive`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1=Permissive Bypassing is Active (ignore bypassable permissives) |
| **`Sts_Perm`** | `INT` | `2#1111_1111_1111_1111` | Pin/Param | [Cờ báo trạng thái logic (Status)] Individual Permissive Status (1=OK, 0=don't start) |
| **`P_Perm`** | `BOOL` | `0` | Pin/Param | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 10 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_Perm`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_8` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_CondTxt`** | `STRING_20[16]` | `{...}` | [Biến cấu hình thông số] Short HMI description of Permissive Conditions |
| **`Cfg_Desc`** | `STRING_40` | `'Permissives'` | [Biến cấu hình thông số] Description for display on HMI. The string shows in the title bar of the faceplate. |
| **`Cfg_Label`** | `STRING_20` | `'Permissives'` | [Biến cấu hình thông số] Label for graphic symbol that is displayed on HMI. This string appears on the graphic symbol. |
| **`Cfg_NavTag`** | `STRING_NavTag[16]` | `{...}` | [Biến cấu hình thông số] Tag names for destination of 16 navigation buttons. IMPORTANT: This function does not apply to FactoryTalk View ME Software. |
| **`Cfg_Tag`** | `STRING_20` | `'P_Perm'` | [Biến cấu hình thông số] Tagname for display on HMI. This string shows in the title bar of the home tab of the faceplate. |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_Perm'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate |
| **`MSet_Bypass`** | `INT` | `2#0000_0000_0000_0000` | [Giá trị đặt từ tab Bảo trì (Maintenance)] Individual condition Maintenance Bypass toggles |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_Reset`
**Thuộc chương:** Chương 5: Khối chức năng dùng chung, Liên động & Cảnh báo (Cross Functional)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Cross Functional 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_Reset`:** **6** Biến đầu vào (`Input Parameters`) | **5** Biến đầu ra (`Output Parameters`) | **8** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_Reset`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_Reset`, `Inp_ResetReqd`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_MinHoldTime`, `Cfg_HasMoreObj`
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_Label`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_Reset` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PCmd_Reset`
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Out_Reset`, `Sts_RdyReset`, `Sts_Err`
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 6 Biến Đầu Vào (`Input Parameters`) của `P_Reset`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Pin/Param | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_Reset`** | `BOOL` | `0` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] Reset Input, 1=Reset, from pushbutton or another object |
| **`Inp_ResetReqd`** | `BOOL` | `1` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] 1=Reset Required, enable Reset button |
| **`Cfg_MinHoldTime`** | `REAL` | `1` | Pin/Param | [Biến cấu hình thông số] Minimum Time to hold Reset Output true (sec) |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Pin/Param | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`PCmd_Reset`** | `BOOL` | `0` | Pin/Param | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Reset, from other object |

## 3. Bảng chi tiết 5 Biến Đầu Ra (`Output Parameters`) của `P_Reset`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Pin/Param | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Out_Reset`** | `BOOL` | `0` | Pin/Param | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1=Reset received for this P_Reset's scope |
| **`Sts_RdyReset`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1=A latched condition is ready to be reset |
| **`Sts_Err`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1=Error in Config: Invalid Hold Time (use 0 to 2147483) |
| **`P_Reset`** | `BOOL` | `0` | Pin/Param | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 8 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_Reset`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_8` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'Multiple-Device Reset'` | [Biến cấu hình thông số] Description for display on HMI. The string shows in the title bar of the faceplate. |
| **`Cfg_Label`** | `STRING_20` | `'Reset'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI. This string appears on the graphic symbol. |
| **`Cfg_Tag`** | `STRING_20` | `'P_Reset'` | [Biến cấu hình thông số] Tagname for display on HMI. This string shows in the bottom right of the home tab of the faceplate. |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Type`** | `STRING_16` | `'P_Reset'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset, from HMI |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_Prompt`
**Thuộc chương:** Chương 5: Khối chức năng dùng chung, Liên động & Cảnh báo (Cross Functional)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Cross Functional 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_Prompt`:** **12** Biến đầu vào (`Input Parameters`) | **18** Biến đầu ra (`Output Parameters`) | **0** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_Prompt`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_Req`, `Inp_Ref`, `Inp_ScalePct`, `Inp_Real_0`, `Inp_Real_1`, `Inp_Real_2`, `Inp_Real_3`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_HasMoreObj`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_Prompt` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Out_Cfm`, `Out_Rdy`, `Out_Select`, `Out_Real_0`, `Out_Real_1`, `Out_Real_2`, `Out_Real_3`, `Val_Ref`, `Sts_Alert`, `Sts_RefChg`, `Sts_Err`
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 12 Biến Đầu Vào (`Input Parameters`) của `P_Prompt`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Pin/Param | [Chân kích hoạt Rung] Enable Input - System Defined Parameter |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Pin/Param | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Inp_Req`** | `BOOL` | `0` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] Input to Enable an Operator Prompt |
| **`Inp_Ref`** | `DINT` | `0` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] Number of Manual Prompt configurations in the array to use |
| **`Inp_ScalePct`** | `REAL` | `100` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] Percent by which to scale numeric display values |
| **`Inp_Real_0`** | `REAL` | `0` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] Programmatic Real Value to display (0) |
| **`Inp_Real_1`** | `REAL` | `0` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] Programmatic Real Value to display (1) |
| **`Inp_Real_2`** | `REAL` | `0` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] Programmatic Real Value to display (2) |
| **`Inp_Real_3`** | `REAL` | `0` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] Programmatic Real Value to display (3) |
| **`OCmd_Cfm`** | `BOOL` | `0` | Pin/Param | [Lệnh từ màn hình HMI Operator] Operator Confirmation |
| **`Prompts`** | `P_PromptCfg[1]` | - | Pin/Param | Prompt instance configuration data |
| **`RespData`** | `P_PromptResp` | - | Pin/Param | structure to hold most recent response data in Character format |

## 3. Bảng chi tiết 18 Biến Đầu Ra (`Output Parameters`) của `P_Prompt`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Pin/Param | [Chân nối tầng Rung] Enable Output - System Defined Prameter |
| **`Out_Cfm`** | `BOOL` | `0` | Pin/Param | [Ngõ ra vật lý điều khiển thiết bị/I/O] Operator Confirmation output |
| **`Out_Rdy`** | `BOOL` | `0` | Pin/Param | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1 = Prompt is ready to be displayed |
| **`Out_Select`** | `SINT` | `4` | Pin/Param | [Ngõ ra vật lý điều khiển thiết bị/I/O] Value of Operator response to selector |
| **`Out_Real_0`** | `REAL` | `0` | Pin/Param | [Ngõ ra vật lý điều khiển thiết bị/I/O] Real numeric value of Operator entry (0) |
| **`Out_Real_1`** | `REAL` | `0` | Pin/Param | [Ngõ ra vật lý điều khiển thiết bị/I/O] Real numeric value of Operator entry (1) |
| **`Out_Real_2`** | `REAL` | `0` | Pin/Param | [Ngõ ra vật lý điều khiển thiết bị/I/O] Real numeric value of Operator entry (2) |
| **`Out_Real_3`** | `REAL` | `0` | Pin/Param | [Ngõ ra vật lý điều khiển thiết bị/I/O] Real numeric value of Operator entry (3) |
| **`Val_Ref`** | `DINT` | `0` | Pin/Param | [Giá trị thực tế đầu ra (Logic/HMI)] Number of Manual Prompt configurations in the array used |
| **`Sts_Alert`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] Status for Alert breadcrumb |
| **`Sts_RefChg`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1 = The Inp_Ref has changed (Single Scan) |
| **`Sts_Err`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1= Error in Config. See detail bits for reason |
| **`Err_PromptCfgSize`** | `BOOL` | `0` | Pin/Param | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in size of an array in the P_PromptCfg UDT definition |
| **`Err_RespDataSize`** | `BOOL` | `0` | Pin/Param | [Cờ báo lỗi cài đặt thông số Cfg_*] 2 = Error in size of an array in the P_PromptResp UDT definition |
| **`Err_MsgRef`** | `BOOL` | `0` | Pin/Param | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in value on Inp_Ref (past end of prompt array) |
| **`ORdy_Cfm`** | `BOOL` | `0` | Pin/Param | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Prompt ready for Operator confirmation |
| **`Prompts`** | `P_PromptCfg[1]` | - | Pin/Param | Prompt instance configuration data |
| **`RespData`** | `P_PromptResp` | - | Pin/Param | structure to hold most recent response data in Character format |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_Logic`
**Thuộc chương:** Chương 5: Khối chức năng dùng chung, Liên động & Cảnh báo (Cross Functional)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Cross Functional 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_Logic`:** **16** Biến đầu vào (`Input Parameters`) | **28** Biến đầu ra (`Output Parameters`) | **35** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_Logic`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_0…Inp_7`, `Inp_Hold`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_UseInpHold`, `Cfg_UsePCmd`, `Cfg_UseOCmd`, `Cfg_UseOut01`, `Cfg_UseOut10`, `Cfg_TSonSnap`, `Cfg_SnapOver`, `Cfg_HasMoreObj`, `Cfg_OnDelay`, `Cfg_OffDelay`
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_0StText`, `Cfg_1StText`, `Cfg_Area`, `Cfg_Desc`, `Cfg_GateFunc`, `Cfg_GateSrc1Inv`, `Cfg_GateSrc1Mask`, `Cfg_GateSrc1Ptr`, `Cfg_GateSrc2Inv`, `Cfg_GateSrc2Mask`, `Cfg_GateSrc2Ptr`, `Cfg_GateSrc3Inv`, `Cfg_GateSrc3Mask`, `Cfg_GateSrc3Ptr`, `Cfg_GateSrc4Inv`, `Cfg_GateSrc4Mask`, `Cfg_GateSrc4Ptr`, `Cfg_HasNav`, `Cfg_InpTxt`, `Cfg_Label`, `Cfg_NavTag`, `Cfg_OutSrcInv`, `Cfg_OutSrcPtr`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_Logic` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PCmd_Snap`, `PCmd_Reset`
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Out_Live`, `Out_Snap`, `Val_DelayPctLive`, `Val_DelayPctSnap`, `Val_SnapInit`, `Sts_Snapped`, `Sts_InpLive`, `Sts_GateLive`, `Sts_InpSnap`, `Sts_GateSnap`, `Sts_GateSrc1Live`, `Sts_GateSrc2Live`, `Sts_GateSrc3Live`, `Sts_GateSrc4Live`, `Sts_GateSrc1Snap`, `Sts_GateSrc2Snap`, `Sts_GateSrc3Snap`, `Sts_GateSrc4Snap`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 16 Biến Đầu Vào (`Input Parameters`) của `P_Logic`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Pin/Param | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_0…Inp_7`** | `BOOL` | `0` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] Logic Input 0…7. |
| **`Inp_Hold`** | `BOOL` | `0` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] 0 --> 1: Capture previous scan states in Snapshot. |
| **`Inp_Reset`** | `BOOL` | `0` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] Input parameter used to programmatically reset alarms. When set to 1, all alarms requiring reset are reset. |
| **`Cfg_UseInpHold`** | `BOOL` | `0` | Pin/Param | [Biến cấu hình thông số] 1 = Use Inp_Hold to snap state; 0 = Use Cmds or Output transition to snap |
| **`Cfg_UsePCmd`** | `BOOL` | `1` | Pin/Param | [Biến cấu hình thông số] 1 = Enable Snapshot on PCmd_Snap 0 --> 1 (edge). |
| **`Cfg_UseOCmd`** | `BOOL` | `1` | Pin/Param | [Biến cấu hình thông số] 1 = Enable Snapshot on OCmd_Snap 0 --> 1 (edge). |
| **`Cfg_UseOut01`** | `BOOL` | `1` | Pin/Param | [Biến cấu hình thông số] 1 = Enable Snapshot on Output 0 --> 1 (rising edge). |
| **`Cfg_UseOut10`** | `BOOL` | `0` | Pin/Param | [Biến cấu hình thông số] 1 = Enable Snapshot on Output 1 --> 0 (falling edge). |
| **`Cfg_TSonSnap`** | `BOOL` | `0` | Pin/Param | [Biến cấu hình thông số] 1 = Generate a TimeStamp when Snapshot occurs |
| **`Cfg_SnapOver`** | `BOOL` | `0` | Pin/Param | [Biến cấu hình thông số] 1 = New Snapshot overwrites without reset. 0 = Save first Snapshot until reset. |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Pin/Param | [Biến cấu hình thông số] 1 = Tells HMI an object with more info is available |
| **`Cfg_OnDelay`** | `REAL` | `0` | Pin/Param | [Biến cấu hình thông số] Output ON delay time (seconds). |
| **`Cfg_OffDelay`** | `REAL` | `0` | Pin/Param | [Biến cấu hình thông số] Output OFF delay time (seconds). |
| **`PCmd_Snap`** | `BOOL` | `0` | Pin/Param | [Lệnh từ Chương trình PLC (Xung 1-shot)] • Set PCmd_Snap to 1 to capture input, gate states in Snapshot • This parameter resets automatically. |
| **`PCmd_Reset`** | `BOOL` | `0` | Pin/Param | [Lệnh từ Chương trình PLC (Xung 1-shot)] • Set PCmd_Reset to 1 to reset (re-arm) snapshot latch • This parameter resets automatically |

## 3. Bảng chi tiết 28 Biến Đầu Ra (`Output Parameters`) của `P_Logic`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Pin/Param | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Out_Live`** | `BOOL` | `??` | Pin/Param | [Ngõ ra vật lý điều khiển thiết bị/I/O] Condition Logic Output (result) after delay |
| **`Out_Snap`** | `BOOL` | `??` | Pin/Param | [Ngõ ra vật lý điều khiển thiết bị/I/O] Condition Logic Output (result) at Snapshot |
| **`Val_DelayPctLive`** | `DINT` | `??` | Pin/Param | [Giá trị thực tế đầu ra (Logic/HMI)] Output OnDelay or OffDelay percent complete: live |
| **`Val_DelayPctSnap`** | `DINT` | `??` | Pin/Param | [Giá trị thực tế đầu ra (Logic/HMI)] Output OnDelay or OffDelay percent complete: snapshot |
| **`Val_SnapInit`** | `DINT` | `??` | Pin/Param | [Giá trị thực tế đầu ra (Logic/HMI)] Snapshot Initiator: 1= OCmd 2 = PCmd 3 = Out 0-->1 4 = Out 1-->0 5 = Inp_Hold |
| **`Sts_Snapped`** | `BOOL` | `??` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1 = Snapshot has been triggered. 0 = Snapshot showing live states. |
| **`Sts_InpLive`** | `SINT` | `2#0000_0000` | Pin/Param | [Cờ báo trạng thái logic (Status)] Live Input Status bits: .0 to .7 = Inp_0 to Inp_7 |
| **`Sts_GateLive`** | `SINT` | `2#0000_0000` | Pin/Param | [Cờ báo trạng thái logic (Status)] Live Gate Result Status bits: .0 to .7 = Gate 0 to 7 |
| **`Sts_InpSnap`** | `SINT` | `2#0000_0000` | Pin/Param | [Cờ báo trạng thái logic (Status)] Snapshot of Input Status bits: .0 to .7 = Inp_0 to Inp_7 |
| **`Sts_GateSnap`** | `SINT` | `2#0000_0000` | Pin/Param | [Cờ báo trạng thái logic (Status)] Snapshot of Gate Result Status bits: .0 to .7 = Gate 0 to 7 |
| **`Sts_GateSrc1Live`** | `SINT` | `2#0000_0000` | Pin/Param | [Cờ báo trạng thái logic (Status)] Live wire state for Source 1 of each gate (bit# = gate#) |
| **`Sts_GateSrc2Live`** | `SINT` | `2#0000_0000` | Pin/Param | [Cờ báo trạng thái logic (Status)] Live wire state for Source 2 of each gate (bit# = gate#) |
| **`Sts_GateSrc3Live`** | `SINT` | `2#0000_0000` | Pin/Param | [Cờ báo trạng thái logic (Status)] Live wire state for Source 3 of each gate (bit# = gate#) |
| **`Sts_GateSrc4Live`** | `SINT` | `2#0000_0000` | Pin/Param | [Cờ báo trạng thái logic (Status)] Live wire state for Source 4 of each gate (bit# = gate#) |
| **`Sts_GateSrc1Snap`** | `SINT` | `2#0000_0000` | Pin/Param | [Cờ báo trạng thái logic (Status)] Snapshot of Wire state for Source 1 of each gate (bit# = gate#) |
| **`Sts_GateSrc2Snap`** | `SINT` | `2#0000_0000` | Pin/Param | [Cờ báo trạng thái logic (Status)] Snapshot of Wire state for Source 2 of each gate (bit# = gate#) |
| **`Sts_GateSrc3Snap`** | `SINT` | `2#0000_0000` | Pin/Param | [Cờ báo trạng thái logic (Status)] Snapshot of Wire state for Source 3 of each gate (bit# = gate#) |
| **`Sts_GateSrc4Snap`** | `SINT` | `2#0000_0000` | Pin/Param | [Cờ báo trạng thái logic (Status)] Snapshot of Wire state for Source 4 of each gate (bit# = gate#) |
| **`Sts_OutInvLive`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] Output after inverter but before TON/TOF timers |
| **`Sts_OutInvSnap`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] Snapshot of Output after inverter but before TON/TOF |
| **`Sts_Err`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1 = Error in configuration: see detail bits for reason. |
| **`Err_GateFunc`** | `SINT` | `2#0000_0000` | Pin/Param | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in each Gate's Function Code (use 0…6). |
| **`Err_GateSrcPtr`** | `SINT` | `2#0000_0000` | Pin/Param | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in each Gate's Source Pointer (use 0…15). |
| **`Err_GateSrcMask`** | `SINT` | `2#0000_0000` | Pin/Param | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in each Gate's Mask (source used) configuration (qty, choice). |
| **`Err_OutSrcPtr`** | `BOOL` | `0` | Pin/Param | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Output's Source Pointer (use 0…15). |
| **`Err_Timer`** | `BOOL` | `0` | Pin/Param | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Output's On Delay or Off Delay Preset (use 0…2,147,483). |
| **`P_Logic`** | `BOOL` | `0` | Pin/Param | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 35 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_Logic`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_0StText`** | `STRING_8` | `'OK'` | [Biến cấu hình thông số] Text to display in Out=0 State |
| **`Cfg_1StText`** | `STRING_8` | `'TRIPPED'` | [Biến cấu hình thông số] Text to display in Out=1 State |
| **`Cfg_Area`** | `STRING_8` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'Configurable Boolean Logic Block'` | [Biến cấu hình thông số] Description for display on HMI. This string is shown in the title bar of the faceplate. |
| **`Cfg_GateFunc`** | `DINT[8]` | `{...}` | [Biến cấu hình thông số] Function code for gate M (1=AND, 2=OR, 3=XOR, 4=2oo3, 5=Set-Reset) |
| **`Cfg_GateSrc1Inv`** | `SINT` | `2#0000_0000` | [Biến cấu hình thông số] Gate M Source #1 is Inverted (M by bit) (1=invert) |
| **`Cfg_GateSrc1Mask`** | `SINT` | `2#0000_0000` | [Biến cấu hình thông số] Gate M Source #1 is Used (M by bit) (1=used) |
| **`Cfg_GateSrc1Ptr`** | `DINT[8]` | `{...}` | [Biến cấu hình thông số] Pointer to Gate M Source #1 (0..7 = inputs, 8..15 = gate outputs) |
| **`Cfg_GateSrc2Inv`** | `SINT` | `2#0000_0000` | [Biến cấu hình thông số] Gate M Source #2 is Inverted (M by bit) (1=invert) |
| **`Cfg_GateSrc2Mask`** | `SINT` | `2#0000_0000` | [Biến cấu hình thông số] Gate M Source #2 is Used (M by bit) (1=used) |
| **`Cfg_GateSrc2Ptr`** | `DINT[8]` | `{...}` | [Biến cấu hình thông số] Pointer to Gate M Source #2 (0..7 = inputs, 8..15 = gate outputs) |
| **`Cfg_GateSrc3Inv`** | `SINT` | `2#0000_0000` | [Biến cấu hình thông số] Gate M Source #3 is Inverted (M by bit) (1=invert) |
| **`Cfg_GateSrc3Mask`** | `SINT` | `2#0000_0000` | [Biến cấu hình thông số] Gate M Source #3 is Used (M by bit) (1=used) |
| **`Cfg_GateSrc3Ptr`** | `DINT[8]` | `{...}` | [Biến cấu hình thông số] Pointer to Gate M Source #3 (0..7 = inputs, 8..15 = gate outputs) |
| **`Cfg_GateSrc4Inv`** | `SINT` | `2#0000_0000` | [Biến cấu hình thông số] Gate M Source #4 is Inverted (M by bit) (1=invert) |
| **`Cfg_GateSrc4Mask`** | `SINT` | `2#0000_0000` | [Biến cấu hình thông số] Gate M Source #4 is Used (M by bit) (1=used) |
| **`Cfg_GateSrc4Ptr`** | `DINT[8]` | `{...}` | [Biến cấu hình thông số] Pointer to Gate M Source #4 (0..7 = inputs, 8..15 = gate outputs) |
| **`Cfg_HasNav`** | `SINT` | `2#0000_0000` | [Biến cấu hình thông số] Set bits indicate which Navigation buttons are enabled |
| **`Cfg_InpTxt`** | `STRING_20[8]` | `{...}` | [Biến cấu hình thông số] Short HMI description of each Input |
| **`Cfg_Label`** | `STRING_20` | `'Configurable Logic'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI |
| **`Cfg_NavTag`** | `STRING_NavTag[8]` | `{...}` | [Biến cấu hình thông số] Tagnames for destinations of Navigation buttons. IMPORTANT: This tag does not work in FactoryTalk ME Software. |
| **`Cfg_OutSrcInv`** | `BOOL` | `0` | [Biến cấu hình thông số] Out Source (before Min Duration Timer) is Inverted (1=invert) |
| **`Cfg_OutSrcPtr`** | `DINT` | `0` | [Biến cấu hình thông số] Source bit for Output (0..7 = inputs, 8..15 = gates) |
| **`Cfg_Tag`** | `STRING_20` | `'P_Logic'` | [Biến cấu hình thông số] Tagname for display on HMI |
| **`HMI_Const`** | `SINT[9]` | `{...}` | [Biến nội bộ giao diện HMI Faceplate] Constants (for use in HMI indirection of parameters) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_Logic'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset (re-arm) Snapshot Latch |
| **`OCmd_Snap`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để capture Input, Gate states in Snapshot |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1=Ready to receive OCmd_Reset (enables button) |
| **`ORdy_Snap`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1=Ready to receive OCmd_Snap (enables button) |
| **`Val_LastGate`** | `DINT[8]` | `{...}` | [Giá trị thực tế đầu ra (Logic/HMI)] Last Gate pin which uses this Gate Result (0=not used 1..3= gates, 33=output) |
| **`Val_LastInp`** | `DINT[8]` | `{...}` | [Giá trị thực tế đầu ra (Logic/HMI)] Last Gate pin which uses this Input (0=not used 1..3= gates, 33=output) (for animation) |
| **`Val_SnapTS`** | `DINT[7]` | `{...}` | [Giá trị thực tế đầu ra (Logic/HMI)] Snapshot Timestamp [0]=yr, [1]=mo, [2]=da, [3]=hr, [4]=min, [5]=sec, [6]=usec |

