# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_LLS`
**Thuộc chương:** Chương 2: Điều khiển Điều tiết & Quy trình (Regulatory & Procedural Control)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Regulatory_Procedural Control 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_LLS`:** **75** Biến đầu vào (`Input Parameters`) | **71** Biến đầu ra (`Output Parameters`) | **34** Biến cấu hình cục bộ (`Local Configuration Tags`)

> **⚡ Truy cập nhanh:** [⬅ Quay lại `README.md` (Thư mục gốc)](../README.md) | [⬅ Danh mục Tra cứu Nhanh (`00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md`)](./00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md)

## 0. Giới thiệu chung & Các nhu cầu cần dùng khối `P_LLS`
- **Tệp định nghĩa khối lệnh (`.L5X`):** `P_LLS_4.10.00_AOI.L5X` *(Trang 107–118 trong tài liệu gốc PROCES-RM013I-EN-P)*
- **Giới thiệu chung chức năng:** Khối **Quản lý Nhóm Động cơ Chạy Chính / Phụ / Dự phòng (Lead / Lag / Standby Motor Group)** cho cụm từ **2 đến 30 bơm/quạt/máy nén mắc song song**. Tự động gọi chạy/dừng số lượng máy theo nhu cầu (`Demand`), tự động gọi máy dự phòng (`Standby`) vào thay thế ngay khi máy đang chạy bị Trip, và **xoay vòng thứ tự ưu tiên theo số giờ chạy tích lũy (`Wear Leveling`)**.
- **Các nhu cầu thực tế CẦN dùng khối `P_LLS`:** Cho **mọi trạm bơm cấp nước, trạm bơm tăng áp, dàn quạt tháp giải nhiệt (Cooling Tower), trạm máy nén khí** có từ 2 thiết bị trở lên chạy luân phiên hoặc hỗ trợ nhau theo tải.
- **Trường hợp KHÔNG dùng & Khối thay thế:** Khi chỉ có 1 động cơ đơn lẻ.

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_LLS`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_PermOK`, `Inp_NBPermOK`, `Inp_IntlkOK`, `Inp_NBIntlkOK`, `Inp_Hand`, `Inp_Ovrd`, `Inp_OvrdDemand`, `Inp_OvrdCmd`, `Inp_ExtInh`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_NumMotors`, `Cfg_MaxDemand`, `Cfg_MinDemand`, `Cfg_FirstOnFirstOff`, `Cfg_AllowRotate`, `Cfg_RotateOnStop`, `Cfg_HasPermObj`, `Cfg_HasIntlkObj`, `Cfg_HasMoreObj`, `Cfg_HasNav`, `Cfg_SetTrack`, `Cfg_SetTrackOvrdHand`, `Cfg_OperStopPrio`, `Cfg_ExtStopPrio`, `Cfg_OCmdResets`, `Cfg_XCmdResets`, `Cfg_OvrdPermIntlk`, `Cfg_HasCantStartAlm`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_Label`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_LLS` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PSet_Demand`, `PSet_Owner`, `PCmd_Start`, `PCmd_Stop`, `PCmd_Rotate`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_CantStartAck`, `PCmd_CantStopAck`, `PCmd_IntlkTripAck`, `PCmd_CantStartSuppress`, `PCmd_CantStopSuppress`, `PCmd_IntlkTripSuppress`, `PCmd_CantStartUnsuppress`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Val_Demand`, `Val_RotateRank`, `Val_RotateID`, `Val_Cmd`, `Val_Fdbk`, `Val_Sts`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Val_Owner`, `Sts_Stopped`, `Sts_Running`, `Sts_Stopping`, `Sts_Increasing`, `Sts_Decreasing`, `Sts_Available`, `Sts_Bypass`, `Sts_BypActive`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 75 Biến Đầu Vào (`Input Parameters`) của `P_LLS`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Enable Input - System Defined Parameter Read Only 0 |
| **`Ref_Motors`** | `P_LLS_Motor[2]` | `1` | Ẩn (Tag) | [Tham chiếu InOut nối tới cấu trúc ngoài] Motor Interface Array (link to 2 to 30 motors) 0 |
| **`Inp_PermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Permissives OK, Group can start Read/Write 0 |
| **`Inp_NBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Non-Bypassable Permissives OK, Group can start Read/Write 0 |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Interlocks OK, Group can start/run Read/Write 0 |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Non-Bypassable Interlocks OK, Group can start/run Read/Write 0 |
| **`Inp_Hand`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Acquire Hand (typ. hardwired local), 0=Release Hand Read/Write 0 |
| **`Inp_Ovrd`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Acquire Override (higher priority program logic), 0=Release Override Read/Write 0 |
| **`Inp_OvrdDemand`** | `DINT` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Override Mode setting for number of motors to run (MinDemand..MaxDemand) Read/Write 0 |
| **`Inp_OvrdCmd`** | `DINT` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Override Mode Command: 0=None, 1=Stop Group, 2=Start Group, 3=Rotate Assignments Read/Write 0 |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Inhibit External Acquisition, 0=Allow External Acquisition Read/Write 0 |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Reset all fault conditions and latched Alarms Read/Write 0 |
| **`Cfg_NumMotors`** | `DINT` | `3` | Ẩn (Tag) | [Biến cấu hình thông số] Number of motors in this Lead / Lag / Standby Group (2…30) IMPORTANT: If the Ref_Motors interface array has fewer elements than the entered value, this value will be reduced to the size of the array. |
| **`Cfg_MaxDemand`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Maximum number of motors to run (1..NumMotors) Read/Write 0 |
| **`Cfg_MinDemand`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum number of motors to run (0..MaxDemand) Read/Write 0 |
| **`Cfg_FirstOnFirstOff`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=First started is first stopped, 0=First started is last stopped Read/Write 0 |
| **`Cfg_AllowRotate`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Allow Rotate ("cycle lead") command to rotate motor assignments Read/Write 0 |
| **`Cfg_RotateOnStop`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Rotate (cycle lead to end of list) upon stopping all motors Read/Write 0 |
| **`Cfg_HasPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng permissive liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng interlock liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_HasNav`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Set bits indicate which Motor Navigation buttons are enabled Read/Write 0 |
| **`Cfg_SetTrack`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] This parameter is used to configure bumpless behavior of setting parameters when switching modes. When this parameter is 1: • In Program command source, the operator settings track the program settings. • In Operator command source, the program settings track the operator settings. • The simulation inputs match the output values (transitions are bumpless). When this parameter is 0, the instruction does not modify operator settings and program settings. In this case, when the mode is changed, the effective value of the setting can change depending on the program-set and operator-set values. |
| **`Cfg_SetTrackOvrdHand`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Prog/Oper Settings track Override/Hand speed reference Read/Write 0 |
| **`Cfg_OperStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=OCmd_Stop any time, 0=OCmd_Stop only when Oper selected Read/Write 0 |
| **`Cfg_ExtStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=XCmd_Stop any time, 0=XCmd_Stop only when Ext selected Read/Write 0 |
| **`Cfg_OCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=New group OCmd resets shed latches and cleared alarms, 0=OCmdReset req'd. Read/Write 0 |
| **`Cfg_XCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=New group XCmd resets shed latches and cleared alarms, 0=OCmdReset req'd. Read/Write 0 |
| **`Cfg_OvrdPermIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Override ignores Bypassable Perm/ Intlk; 0=always use Perm/Intlk Read/Write 0 |
| **`Cfg_HasCantStartAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasCantStopAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_CantStartResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_CantStopResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_CantStartAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_CantStopAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_CantStartSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_CantStopSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_StartDelay`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Time (sec) after start or stop until next start is allowed (0..2M sec) Read/Write 0 |
| **`Cfg_StopDelay`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Time (sec) after start or stop until next stop is allowed (0..2M sec) Read/Write 0 |
| **`PSet_Demand`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Program Setting for number of motors to run (MinDemand.. MaxDemand) Read/Write 0 |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Mã định danh chủ sở hữu chương trình (Program Owner ID: khác 0 để đăng ký chiếm quyền, bằng 0 để nhả quyền). Read/Write 0 |
| **`XSet_Demand`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài (External)] External Setting for number of motors to run (MinDemand.. MaxDemand) Read/Write 0 |
| **`PCmd_Start`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khởi động (Start) động cơ Group Read/Write 0 |
| **`PCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Dừng (Stop) động cơ Group Read/Write 0 |
| **`PCmd_Rotate`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Rotate Assignments (cycle lead to end of list) Read/Write 0 |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Program (Oper to Prog) Read/Write 0 |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Operator (Prog to Oper) Read/Write 0 |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa cứng chế độ Program (không cho phép Operator giành quyền) Read/Write 0 |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Unlock Program (allow Oper to acquire) Read/Write 0 |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Normal (Oper or Prog) Read/Write 0 |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_Reset = 1 để reset all alarms that require reset (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_CantStartAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_CantStopAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_CantStartSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Not Available to Start Alarm Read/Write 0 |
| **`PCmd_CantStopSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Not Available to Stop Alarm Read/Write 0 |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Interlock Trip Alarm Read/Write 0 |
| **`PCmd_CantStartUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Not Available to Start Alarm Read/Write 0 |
| **`PCmd_CantStopUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Not Available to Stop Alarm Read/Write 0 |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Interlock Trip Alarm Read/Write 0 |
| **`PCmd_CantStartUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_CantStopUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Release (Maint to Oper/Prog/Ovrd) Read/Write 0 |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Unlock Operator (allow Prog to acquire) Read/Write 0 |
| **`XCmd_Start`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Khởi động (Start) động cơ Group Read/Write 0 |
| **`XCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Dừng (Stop) động cơ Group Read/Write 0 |
| **`XCmd_Rotate`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Rotate Assignments (cycle lead to end of list) Read/Write 0 |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Acquire (Oper/Prog to Ext) |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Release (Ext to Oper/Prog) |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Reset all Alarms requiring Reset Read/Write 0 |

## 3. Bảng chi tiết 71 Biến Đầu Ra (`Output Parameters`) của `P_LLS`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Enable Output - System Defined Parameter |
| **`Val_Demand`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Number of motors requested to run |
| **`Val_RotateRank`** | `DINT` | `-1` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Motor rank (0=Lead, etc.) which will be demoted on Rotate |
| **`Val_RotateID`** | `DINT` | `-1` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Motor number which will be demoted on Rotate |
| **`Val_Cmd`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Group command: 0 = None 1 = Stop 2 = Start |
| **`Val_Fdbk`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Group Feedback 0..31=Number of motors actually running |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Group Confirmed Status: 0 = Powerup/Unknown 1 = Group Stopped 2 = Group Running 7 = Group Stopping (commanded to stop, waiting for motors to stop) 14 = Group Running and Demand Decreasing (waiting for a motor to stop) 15 = Group Running and Demand Increasing (waiting for a motor to start) 33 = Group Disabled |
| **`Val_Fault`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Group Fault Status: 0 = None 12 = Can't Start enough motors to meet demand 13 = Can't Stop enough motors to meet demand 34 = Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Highest Alarm prio and ack status this object + motors (enum) |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Current Object Owner ID (0=not owned) |
| **`Sts_Stopped`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Motor Group requested to stop and all motors confirmed stopped |
| **`Sts_Running`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Motor Group requested to run |
| **`Sts_Stopping`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Motor Group requested to stop and not all motors confirmed stopped |
| **`Sts_Increasing`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Group is starting motors in sequence to get up to demand |
| **`Sts_Decreasing`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Group is stopping motors in sequence to get down to demand |
| **`Sts_Available`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Group available for control by automation (Prog) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Bypassable Interlocks and Permissives are Bypassed |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Bypassing Active (Bypassed or Maintenance) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Group Not Ready, see detail bits for reason |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Group Not Ready: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Group Not Ready: Điều kiện Khóa liên động không thỏa mãn (Interlock Not OK) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Group Not Ready: Group is Out of Service |
| **`Nrdy_PrioStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Group Not Ready: Oper/Ext Priority Stop Requires Reset |
| **`Nrdy_Perm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Group Not Ready: Điều kiện Cho phép khởi động không thỏa mãn (Permissive Not OK) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=An Alarm is Disabled or Shelved, display icon |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Error in Config: see detail bits for reason |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Start/Stop Check timer preset (use 0 to 2147483) |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Alarm Throttle Time or Severity |
| **`Sts_MotorAvail`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Set bits indicate which motor are avialble for Program control |
| **`Sts_MotorStopped`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Set bits indicate which motors are confirmed Stopped |
| **`Sts_MotorStarting`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Set bits indicate which motors are Starting |
| **`Sts_MotorRunning`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Set bits indicate which motors are confirmed Running |
| **`Sts_MotorStopping`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Set bits indicate which motors are confirmed Stopped |
| **`Sts_Hand`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Hand đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn OoS, Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_OoS`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Out of Service đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_Maint`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Maintenance đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ovrd, Ext, Prog, Oper) |
| **`Sts_Ovrd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Override đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ext, Prog, Oper) |
| **`Sts_Ext`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=External đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Prog, Oper) |
| **`Sts_Prog`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Program đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_ProgLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Program đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Oper`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_OperLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Selection equals the Normal (Prog or Oper) |
| **`Sts_ExtReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=External request inhibited, cannot Get to External from current state |
| **`Sts_ProgReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Program request inhibited, cannot Get to Program from current state |
| **`Sts_MAcqRcvd`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Maintenance Acquire command received this scan |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc lỗi ngắt tải (Shed) đã hết điều kiện lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_CantStart`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Motor Failed to Start (one-shot) Read/Write 0 |
| **`Alm_CantStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Motor Fail to Start Alarm Read/Write 0 |
| **`Ack_CantStart`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Fail to Start Alarm đã được Xác nhận (Acknowledged) Read/Write 0 |
| **`Sts_CantStartDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Fail to Start Alarm has been disabled (will not be sent) Read/Write 0 |
| **`Sts_CantStartShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Fail to Start Alarm has been inhibited by logic Read/Write 0 |
| **`Sts_CantStartSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Fail to Start Alarm has been suppressed (logged only) Read/Write 0 |
| **`Sts_CantStop`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Motor Failed to Stop Read/Write 0 |
| **`Alm_CantStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Motor Fail to Stop Alarm Read/Write 0 |
| **`Ack_CantStop`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Fail to Stop Alarm đã được Xác nhận (Acknowledged) Read/Write 0 |
| **`Sts_CantStopDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Fail to Stop Alarm has been disabled (will not be sent) Read/Write 0 |
| **`Sts_CantStopShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Fail to Stop Alarm has been inhibited by logic Read/Write 0 |
| **`Sts_CantStopSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Fail to Stop Alarm has been suppressed (logged only) Read/Write 0 |
| **`Sts_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Motor stopped by an Interlock NOT OK (one-shot) Read/Write 0 |
| **`Alm_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Alarm: Motor stopped by an Interlock NOT OK Read/Write 0 |
| **`Ack_IntlkTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Interlock Trip Alarm đã được Xác nhận (Acknowledged) Read/Write 0 |
| **`Sts_IntlkTripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Interlock Trip Alarm is Disabled (not saved or sent) Read/Write 0 |
| **`Sts_IntlkTripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Interlock Trip Alarm has been inhibited by logic Read/Write 0 |
| **`Sts_IntlkTripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Interlock Trip Alarm has been suppressed (logged only) Read/Write 0 |
| **`MRdy_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`P_LLS`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI Read/Write 0 |

## 4. Bảng chi tiết 34 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_LLS`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`CantStart`** | `P_Alarm` | `{...}` | Alarm: cannot start enough motors to meet demand Read/Write |
| **`CantStop`** | `P_Alarm` | `{...}` | Alarm: cannot stop enough motors to meet demand Read/Write |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Process Area for security Read/Write |
| **`Cfg_Desc`** | `STRING_40` | `'Lead` | [Biến cấu hình thông số] / Lag / Standby Motor Group' Description for display on HMI Read/Write |
| **`Cfg_Label`** | `STRING_20` | `'Motor` | [Biến cấu hình thông số] Group Control' Label for graphic symbol displayed on HMI Read/Write |
| **`Cfg_Tag`** | `STRING_20` | `'P_LLS'` | [Biến cấu hình thông số] Tagname for display on HMI Read/Write |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc quản lý lựa chọn nguồn lệnh điều khiển Read/Write |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate Read Only |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) Read/Write |
| **`HMI_Type`** | `STRING_16` | `'P_LLS'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate Read Only |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | Interlock Trip Alarm Read/Write |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bypass all Bypassable Interlocks and Permissives Read/Write |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Check (not bypass) all Interlocks and Permissives Read/Write |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) Read Only |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) Read Only |
| **`MSet_MotorOoS`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | [Giá trị đặt từ tab Bảo trì (Maintenance)] Set bits indicate which motors have been taken Out of Service by Maintenance Read/Write |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset all Alarms requiring Reset Read/Write |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset all Alarms and latched Shed conditions Read/Write |
| **`OCmd_Rotate`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Rotate Assignments (cycle lead to end of list) Read/Write |
| **`OCmd_Start`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Khởi động (Start) động cơ Group Read/Write |
| **`OCmd_Stop`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Dừng (Stop) động cơ Group Read/Write |
| **`ORdy_Demand`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OSet_Demand (enables numeric entry) Read Only |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) Read Only |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) Read Only |
| **`ORdy_Rotate`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Rotate (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) Read Only |
| **`ORdy_Start`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Start (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) Read Only |
| **`ORdy_Stop`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Stop (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) Read Only |
| **`OSet_Demand`** | `DINT` | `0` | [Giá trị đặt từ màn hình HMI Operator] Operator Setting for number of motors to run (MinDemand.. MaxDemand) Read/Write |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) Read Only |
| **`Val_PrefList`** | `DINT[32]` | `{...}` | [Giá trị thực tế đầu ra (Logic/HMI)] Rank list of motor preferences: [0]=lead, [1]=lag, etc. Read Only |
| **`Val_PrioList`** | `DINT[32]` | `{...}` | [Giá trị thực tế đầu ra (Logic/HMI)] Rank list of motor priorities: [0]=lead, [1]=lag, etc. Read Only |
| **`Val_RankList`** | `DINT[32]` | `{...}` | [Giá trị thực tế đầu ra (Logic/HMI)] Rank list of motor numbers: [0]=lead, [1]=lag, etc.) Read Only |
| **`Val_StsList`** | `DINT[32]` | `{...}` | [Giá trị thực tế đầu ra (Logic/HMI)] Rank list of motor status: [0]=lead, [1]=lag, etc.; .0=Avaiable, .1=Stopped, .2=Starting, .3=Running, .4=Stopping, .5=Out of Service (Maint) Read Only |
| **`Val_UsrList`** | `INT[32]` | `{...}` | [Giá trị thực tế đầu ra (Logic/HMI)] Rank list of user sort criteria: [0]=lead, [1]=lag, etc. Read Only |
