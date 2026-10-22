# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_Logic`
**Thuộc chương:** Chương 5: Khối chức năng dùng chung, Liên động & Cảnh báo (Cross Functional)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Cross Functional 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_Logic`:** **16** Biến đầu vào (`Input Parameters`) | **28** Biến đầu ra (`Output Parameters`) | **35** Biến cấu hình cục bộ (`Local Configuration Tags`)

> **⚡ Truy cập nhanh:** [⬅ Quay lại `README.md` (Thư mục gốc)](../README.md) | [⬅ Danh mục Tra cứu Nhanh (`00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md`)](./00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md)

## 0. Giới thiệu chung & Các nhu cầu cần dùng khối `P_Logic`
- **Tệp định nghĩa khối lệnh (`.L5X`):** `P_Logic_4.10.00_AOI.L5X` *(Trang 391–396 trong tài liệu gốc PROCES-RM013I-EN-P)*
- **Giới thiệu chung chức năng:** Khối **Mạch Logic Tổ hợp Khả trình kèm Chụp băng hình trạng thái (`Boolean Logic with Snapshot`)**. Nhận 8 đầu vào Boolean đi qua 8 cổng logic nối tầng (`AND`, `OR`, `XOR`, `Set/Reset Flip-Flop`, `Select 2:1`, `Majority 2oo3`) cấu hình trực tiếp trên HMI mà không cần sửa code PLC, đồng thời **chụp lại toàn bộ trạng thái 8 đầu vào kèm nhãn thời gian (`Snapshot`)** ngay tại mili-giây ngõ ra tác động.
- **Các nhu cầu thực tế CẦN dùng khối `P_Logic`:** 1. Khi cần tạo **mạch điều kiện liên động/khởi động phức tạp (như biểu quyết 2-trên-3 cảm biến, mạch chốt RS)** và muốn cho phép kỹ sư nhà máy chỉnh sửa logic trên HMI. 2. Khi cần **bắt lỗi chập chờn (Glitch / Intermittent Trip)** diễn ra trong vài mili-giây nhờ tính năng chụp băng hình `Snapshot`.
- **Trường hợp KHÔNG dùng & Khối thay thế:** Khi chỉ cần gom các điều kiện liên động AND/OR phẳng thông thường → Dùng [`P_Intlk.md`](./P_Intlk.md) hoặc [`P_Perm.md`](./P_Perm.md).

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
