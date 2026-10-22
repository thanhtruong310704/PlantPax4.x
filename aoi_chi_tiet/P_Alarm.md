# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_Alarm`
**Thuộc chương:** Chương 5: Khối chức năng dùng chung, Liên động & Cảnh báo (Cross Functional)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Cross Functional 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_Alarm`:** **17** Biến đầu vào (`Input Parameters`) | **16** Biến đầu ra (`Output Parameters`) | **36** Biến cấu hình cục bộ (`Local Configuration Tags`)

> **⚡ Truy cập nhanh:** [⬅ Quay lại `README.md` (Thư mục gốc)](../README.md) | [⬅ Danh mục Tra cứu Nhanh (`00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md`)](./00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md)

## 0. Giới thiệu chung & Các nhu cầu cần dùng khối `P_Alarm`
- **Tệp định nghĩa khối lệnh (`.L5X`):** `P_Alarm_4.10.00_AOI.L5X` *(Trang 371–377 trong tài liệu gốc PROCES-RM013I-EN-P)*
- **Giới thiệu chung chức năng:** Khối **Quản lý Cảnh báo Chuẩn (`Common Alarm Block`)** tuân thủ tiêu chuẩn ISA-18.2. Quản lý trạng thái Ack, Reset, Shelve, Suppress, Disable và mức độ nghiêm trọng (`Severity`) để hiển thị đồng bộ trên hệ thống cảnh báo HMI.
- **Các nhu cầu thực tế CẦN dùng khối `P_Alarm`:** Khối này đã được nhúng sẵn trong hầu hết các khối PlantPAx. Bạn lấy khối `P_Alarm` rời ra dùng khi tự viết một **thuật toán hoặc điều kiện cảnh báo tùy chỉnh riêng** trong PLC và muốn cảnh báo đó hiển thị trên thanh Alarm Banner HMI chuẩn của PlantPAx.
- **Trường hợp KHÔNG dùng & Khối thay thế:** Khi cảnh báo đã có sẵn trong `P_AIn`, `P_DIn`, `P_Motor`, `P_Valve*`.

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
