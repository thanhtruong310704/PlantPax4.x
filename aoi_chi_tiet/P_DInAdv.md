# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_DInAdv`
**Thuộc chương:** Chương 1: Xử lý tín hiệu Vào/Ra (I/O Processing)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `IO Processing 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_DInAdv`:** **62** Biến đầu vào (`Input Parameters`) | **45** Biến đầu ra (`Output Parameters`) | **40** Biến cấu hình cục bộ (`Local Configuration Tags`)

> **⚡ Truy cập nhanh:** [⬅ Quay lại `README.md` (Thư mục gốc)](../README.md) | [⬅ Danh mục Tra cứu Nhanh (`00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md`)](./00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md)

## 0. Giới thiệu chung & Các nhu cầu cần dùng khối `P_DInAdv`
- **Tệp định nghĩa khối lệnh (`.L5X`):** `P_DInAdv_4.10.00_AOI.L5X` *(Trang 53–57 trong tài liệu gốc PROCES-RM013I-EN-P)*
- **Giới thiệu chung chức năng:** Khối đầu vào số nâng cao. Tích hợp logic **Công tắc tốc độ quay (Zero-Speed / Underspeed Switch)** nhận phản hồi chạy từ động cơ (`Inp_MotorRun`) với thời gian trễ khởi động, và hỗ trợ **2 cảnh báo trạng thái bất thường độc lập (`Alm1`, `Alm2`)**.
- **Các nhu cầu thực tế CẦN dùng khối `P_DInAdv`:** Khi giám sát **cảm biến báo quay ở đuôi băng tải, gầu tải, quạt gió, máy khuấy**: nếu Contactor động cơ đã đóng (`Inp_MotorRun = 1`) mà sau thời gian khởi động trục bị động không quay (do đứt dây curoa hoặc trượt khớp nối), khối lập tức báo lỗi dừng máy.
- **Trường hợp KHÔNG dùng & Khối thay thế:** Cho các công tắc hành trình / báo mức thông thường chỉ cần 1 cảnh báo ([`P_DIn.md`](./P_DIn.md)).

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_DInAdv`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_Gate`, `Inp_IOFault`, `Inp_PV`, `Inp_PVNotify`, `Inp_Reset`, `Inp_RunFdbk`, `Inp_Sim`, `Inp_SpeedFdbk`, `Inp_Target`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_HasStdySSWR0`, `Cfg_Combine_Sts_RunDec`, `Cfg_Debounce`, `Cfg_DecPlcs`, `Cfg_GateDly`, `Cfg_HasSSW`, `Cfg_HasPulseSSW`, `Cfg_HasPulseSpdConv`, `Cfg_HasIOFaultAlm`, `Cfg_HasStdySSWR1`, `Cfg_HasTgtDisagreeWAlm`, `Cfg_HasVSDSpFbk`, `Cfg_HasTgtDisagreeAlm`, `Cfg_HasTgtDisWF`, `Cfg_HasMoreObj`, `Cfg_IOFaultResetReqd`, `Cfg_IOFaultAckReqd`, `Cfg_IOFaultSeverity`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_0StText`, `Cfg_1StText`, `Cfg_AccTime`, `Cfg_AcStText`, `Cfg_Area`, `Cfg_DcStText`, `Cfg_DecTime`, `Cfg_Desc`, `Cfg_Label`, `Cfg_PulseMin`, `Cfg_RStText`, `Cfg_SpeedFdbkEU`, `Cfg_SymbolVis`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_DInAdv` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PCmd_IOFaultAck`, `PCmd_Reset`, `PCmd_IOFaultSuppress`, `PCmd_IOFaultUnshelve`, `PCmd_IOFaultUnsuppress`, `PCmd_TgtDisagreeAck`, `PCmd_TgtDisagreeSuppress`, `PCmd_TgtDisagreeUnshelve`, `PCmd_TgtDisagreeUnsuppress`, `PCmd_TgtDisagreeWAck`, `PCmd_TgtDisagreeWSuppress`, `PCmd_TgtDisagreeWUnsuppress`, `PCmd_TgtDisagreeWUnshelve`
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Alm_IOFault`, `Alm_TgtDisagree`, `Alm_TgtDisagreeW`, `Sts`, `Sts_Acc`, `Sts_AlmInh`, `Sts_Dec`, `Sts_Err`, `Sts_InpPV`, `Sts_MaintByp`, `Sts_IOFault`, `Sts_IOFaultDisabled`, `Sts_IOFaultShelved`, `Sts_IOFaultSuppressed`, `Sts_PV`, `Sts_PVBad`, `Sts_RdyAck`, `Sts_RdyReset`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 62 Biến Đầu Vào (`Input Parameters`) của `P_DInAdv`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Cfg_HasStdySSWR0`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Switch Steady 0 = Run |
| **`Cfg_Combine_Sts_RunDec`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Combine Status Run and Deccelerate as Status Run |
| **`Cfg_Debounce`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time Input Sts must maintain state (sec) |
| **`Cfg_DecPlcs`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Number of decimal places for speed ref/fdbk display (0..6) |
| **`Cfg_GateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Gate Delay (s): time after Gate Input true to enable On/Off Delay |
| **`Cfg_HasSSW`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Used as Speed Switch |
| **`Cfg_HasPulseSSW`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Switch Pulse |
| **`Cfg_HasPulseSpdConv`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Pulse Speed Conversion |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=I/O Fault Alarm exists and will be checked |
| **`Cfg_HasStdySSWR1`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Switch Steady 1 = Run |
| **`Cfg_HasTgtDisagreeWAlm`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Target Disagree Warning Alarm exists and will be checked |
| **`Cfg_HasVSDSpFbk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Switch used with VSD |
| **`Cfg_HasTgtDisagreeAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Target Disagree Alarm exists and will be checked |
| **`Cfg_HasTgtDisWF`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] DIn has Target Disagree Warning & Failure Indication |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=I/O Fault Alarm is latched in, requires Reset Command to clear |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=I/O Fault Alarm must be acknowledged |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] I/O Fault Alarm Severity: 1..250=Low, 251..500=Medium, 501..750=High, 751..1000=Urgent |
| **`Cfg_Maintain_StsRun`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] SpeedSW maintain Sts_Run during Deccelerate |
| **`Cfg_NormTextVis`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Display state text in "normal" state (= target), 0=hide (some HMI objects) |
| **`Cfg_NoSubstPV`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Disallow selection of Substitute PV |
| **`Cfg_SSW_Type`** | `SINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = SpeedSw 0 Run, 2 = SppedSw 1 Run, 4 = SpeedSW Pulse |
| **`Cfg_TgtDisagreeAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Target Disagree Alarm must be acknowledged |
| **`Cfg_TgtDisagreeOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time for Input to disagree with Target to raise Status (sec) |
| **`Cfg_TgtDisagreeOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time for Input to agree with Target to clear Status (sec) |
| **`Cfg_TgtDisagreeResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Target Disagree Alarm is latched in, requires Reset Command to clear |
| **`Cfg_TgtDisagreeSSWTgt`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] SpeedSwitch TargetDisagree Set Target |
| **`Cfg_TgtDisagreeWOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time for Input to disagree with Target to raise warning Status (sec) |
| **`Cfg_TgtDisagreeWOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time for Input to agree with Target to clear warning Status (sec) |
| **`Cfg_TgtDisagreeWAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Target Disagree Warning Alarm must be acknowledged |
| **`Cfg_TgtDisagreeWResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Target Disagree Warning Alarm is latched in, requires Reset Command to clear |
| **`Cfg_TgtDisagreeSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Target Disagree Alarm Severity: 1..250=Low, 251..500=Medium, 501..750=High, 751..1000=Urgent |
| **`Cfg_TgtDisagreeWSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Target Disagree Warning Alarm Severity: 1..250=Low, 251..500=Medium, 501..750=High, 751..1000=Urgent |
| **`Cfg_UseRunFdbkSpdSW`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] SpeedSW status updates only with Motor Run Feedback |
| **`Cfg_VSD_FailTime`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] VSD Failure indication delay time |
| **`Cfg_VSD_LoLIM`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] VSD Speed Lo Limit for Warn Fail factor adjust |
| **`Cfg_VSD_WarnTime`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] VSD Warning indication delay time |
| **`Inp_Gate`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Gate Condition (=1 to enable Disagree Status) |
| **`Inp_IOFault`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Input Comm Status or Quality 1=Bad, 0=OK |
| **`Inp_PV`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu đo lường đầu vào (Biến quá trình PV) nối từ cảm biến / thẻ Analog Input |
| **`Inp_PVNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã trạng thái mức độ ưu tiên và xác nhận cảnh báo của đối tượng PV liên kết. |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Reset Alarm requiring reset |
| **`Inp_RunFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Motor Run feedback for SppedSW |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân kích hoạt chế độ Mô phỏng: 1 = Dùng giá trị giả lập (Set_SimPV); 0 = Dùng tín hiệu thực tế (Inp_PV). |
| **`Inp_SpeedFdbk`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] VSD Speed FeedBack 0 - 100% |
| **`Inp_Target`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Target State of Input (Disagree Status if not in Target State) |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) the I/O Fault Alarm |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Reset all latched Alarms |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) the I/O Fault Alarm |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) the I/O Fault Alarm |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) the I/O Fault Alarm |
| **`PCmd_TgtDisagreeAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) the Target Disagree Alarm |
| **`PCmd_TgtDisagreeSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) the Target Disagree Alarm |
| **`PCmd_TgtDisagreeUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) the Target Disagree Alarm |
| **`PCmd_TgtDisagreeUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) the Target Disagree Alarm |
| **`PCmd_TgtDisagreeWAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) the Target Disagree Warning Alarm |
| **`PCmd_TgtDisagreeWSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) the Target Disagree Warning Alarm |
| **`PCmd_TgtDisagreeWUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) the Target Disagree Warning Alarm |
| **`PCmd_TgtDisagreeWUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) the Target Disagree Warning Alarm |
| **`Set_SimPV`** | `BOOL` | `0` | Ẩn (Tag) | PV used in Simulation (Inp_Sim=1) |
| **`Set_SymbolVis`** | `BOOL` | `0` | Ẩn (Tag) | 0=Hidden except if in error. 1= Display Symbol (set from Area or Unit) |

## 3. Bảng chi tiết 45 Biến Đầu Ra (`Output Parameters`) của `P_DInAdv`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Enable Out: The EnableOut signal is not manipulated by this instruction. Its output always reflects EnableIn Input state. |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=I/O Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Ack_TgtDisagree`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Target Disagree Alarm đã được Xác nhận (Acknowledged) |
| **`Ack_TgtDisagreeW`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Target Disagree Warning Alarm đã được Xác nhận (Acknowledged) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Alarm: Input communication fault |
| **`Alm_TgtDisagree`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Alarm: Input is not in Target state |
| **`Alm_TgtDisagreeW`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Alarm: Input is not in Target state: warning |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Invalid Timer Preset (use 0 to 2147483) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Invalid Timer Preset (use 0 to 2147483) |
| **`P_DIn`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final output source and quality: GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`Sts`** | `BOOL` | `0` | Hiện (Pin) | Discrete Input Status (incl. Debounce, Manual Override, if used) |
| **`Sts_Acc`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Accelerating |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang bị Tạm hoãn (Shelved) hoặc Vô hiệu hóa (Disabled), hiển thị biểu tượng trên HMI |
| **`Sts_Dec`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Decelerating |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Error in Config: see detail bits for reason |
| **`Sts_InpPV`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Using Input PV (normal) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device has a Maintenance Bypass function active |
| **`Sts_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Input communication fault (Inp_IOFault = 1) |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_PV`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Discrete Input Status (actual, not subject to Override) |
| **`Sts_PVBad`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Input Comm Status/ Quality (0=OK, 1=Bad) |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc lỗi ngắt tải (Shed) đã hết điều kiện lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_Run`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Running |
| **`Sts_SubstPV`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Using Substitute PV (Input being overridden) |
| **`Sts_TgtDisagree`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Input is not in Target state |
| **`Sts_TgtDisagreeCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Target Disagree comparicon result 1=Disagree |
| **`Sts_TgtDisagreeWCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Target Disagree Warning comparicon result 1=Disagree |
| **`Sts_TgtDisagreeDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Target Disagree Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_TgtDisagreeGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Target Disagree trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_TgtDisagreeWDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Target Disagree Warning Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_TgtDisagreeWGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Target Disagree Warning trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_TgtDisagreeShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Target Disagree Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_TgtDisagreeSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Target Disagree Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_TgtDisagreeWSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Target Disagree Warning Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_TgtDisagreeW`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Input is not in Target state: warning |
| **`Sts_TgtDisagreeWShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Target Disagree Warning Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Val_Fault`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Fault Status 0=none, 16=Tgt Disagree, 32=I/O comms., 34=CfgErr |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Confirmed Sts 0=PV Good, 6=PV Bad, 7=Substitute PV |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |

## 4. Bảng chi tiết 40 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_DInAdv`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_0StText`** | `STRING_8` | `'Off'` | [Biến cấu hình thông số] Text to display in PV=0 State |
| **`Cfg_1StText`** | `STRING_8` | `On'` | [Biến cấu hình thông số] Text to display in PV=1 State |
| **`Cfg_AccTime`** | `DINT` | `5` | [Biến cấu hình thông số] Accelerate Time in Sec |
| **`Cfg_AcStText`** | `STRING_8` | `'Acc'` | [Biến cấu hình thông số] Text to display in Accelerate State |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_DcStText`** | `STRING_8` | `'Decc'` | [Biến cấu hình thông số] Text to display in Decelerate State |
| **`Cfg_DecTime`** | `DINT` | `10` | [Biến cấu hình thông số] Deccelerate Time |
| **`Cfg_Desc`** | `STRING_40` | `'Discrete Input'` | [Biến cấu hình thông số] Description for display on HMI |
| **`Cfg_Label`** | `STRING_20` | `'Discrete Input'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI |
| **`Cfg_PulseMin`** | `DINT` | `0` | [Biến cấu hình thông số] Pulses per Min for Speed Calc |
| **`Cfg_RStText`** | `STRING_8` | `'Run'` | [Biến cấu hình thông số] Text to display in Run State |
| **`Cfg_SpeedFdbkEU`** | `STRING_8` | `'Hz'` | [Biến cấu hình thông số] Text to display Units |
| **`Cfg_SymbolVis`** | `BOOL` | `0` | [Biến cấu hình thông số] Always display Symbol else hidden if no error condition |
| **`Cfg_Tag`** | `STRING_20` | `'P_DInAdv'` | [Biến cấu hình thông số] Tagname for display on HMI |
| **`Err_EngTb1`** | `BOOL` | `0` | [Cờ báo lỗi cài đặt thông số Cfg_*] Error on Engineering Tab 1 |
| **`Err_EngTb2`** | `BOOL` | `0` | [Cờ báo lỗi cài đặt thông số Cfg_*] Error on Engineering Tab 2 |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_DInAdv'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI và phần mềm thông tin |
| **`IOFault`** | `P_Alarm` | `{...}` | I/O Fault Alarm |
| **`MCmd_InpPV`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Bảo trì chọn sử dụng tín hiệu PV thực tế (chế độ bình thường) |
| **`MCmd_SubstPV`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Bảo trì chọn sử dụng giá trị PV thay thế (ghi đè đầu vào) |
| **`MRdy_InpPV`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_InpPV (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_SubstPV`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_SubstPV (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MSet_SubstPV`** | `BOOL` | `0` | [Giá trị đặt từ tab Bảo trì (Maintenance)] Maintenance-Entered Substitute PV |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset all latched Alarms |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset and Xác nhận (Acknowledge) all Alarms |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`PulseSpd_ONS1`** | `BOOL` | `0` | One-Shot store bit |
| **`SSW_StaticGate`** | `P_Gate` | `{...}` | Condition Gate/Delay |
| **`Sts_Equip_Start`** | `BOOL` | `0` | [Cờ báo trạng thái logic (Status)] Equipment Starting |
| **`TgtDisagree`** | `P_Alarm` | `{...}` | Target Disagree Alarm |
| **`TgtDisagreeGate`** | `P_Gate` | `{...}` | Target Disagree Condition Gate/Delay |
| **`TgtDisagreeW`** | `P_Alarm` | `{...}` | Target Disagree Warning Alarm |
| **`TgtDisagreeWarnGate`** | `P_Gate` | `{...}` | Target Disagree Warning Condition Gate/Delay |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận của riêng khối này (enum) |
| **`Val_PlsSpdPerc`** | `REAL` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Pulse Speed as % |
| **`VSD_Sim`** | `DINT` | `0` | VSD Sim Speed |
| **`VSD_Spd_Cmd`** | `DINT` | `0` | VSD Speed Command from HMI |
