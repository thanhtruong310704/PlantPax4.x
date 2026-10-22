# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_MotorHO`
**Thuộc chương:** Chương 3: Động cơ, Biến tần & Rơ-le Quá tải (Motors & Drives)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Motors 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_MotorHO`:** **50** Biến đầu vào (`Input Parameters`) | **52** Biến đầu ra (`Output Parameters`) | **27** Biến cấu hình cục bộ (`Local Configuration Tags`)

> **⚡ Truy cập nhanh:** [⬅ Quay lại `README.md` (Thư mục gốc)](../README.md) | [⬅ Danh mục Tra cứu Nhanh (`00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md`)](./00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md)

## 0. Giới thiệu chung & Các nhu cầu cần dùng khối `P_MotorHO`
- **Tệp định nghĩa khối lệnh (`.L5X`):** `P_MotorHO_4.10.00_AOI.L5X` *(Trang 140–142 trong tài liệu gốc PROCES-RM013I-EN-P)*
- **Giới thiệu chung chức năng:** Khối giám sát **Động cơ vận hành bằng tay tại chỗ (Hand-Operated Motor)**. PLC không xuất lệnh khởi động mà chỉ đọc tiếp điểm phản hồi chạy (1 tốc độ, 2 tốc độ hoặc thuận/nghịch) để hiển thị lên SCADA và tùy chọn xuất 1 xung `Out_Trip` để ngắt khẩn cấp khi vi phạm liên động `Inp_IntlkOK`.
- **Các nhu cầu thực tế CẦN dùng khối `P_MotorHO`:** Khi có các **động cơ phụ trợ (bơm dầu bôi trơn tại chỗ, quạt thông gió nhà xưởng, máy gia công cục bộ)** khởi động bằng nút nhấn cơ khí tại tủ hiện trường nhưng cần theo dõi trạng thái và cắt liên động an toàn trên DCS.
- **Trường hợp KHÔNG dùng & Khối thay thế:** Khi cần khởi động động cơ từ màn hình HMI hoặc từ chương trình PLC.

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_MotorHO`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_RunFastFwdFdbk`, `Inp_SlowRevFdbk`, `Inp_IntlkOK`, `Inp_NBIntlkOK`, `Inp_OvldNotify`, `Inp_IOFault`, `Inp_Sim`, `Inp_SimRunFastFwd`, `Inp_SimSlowRev`, `Inp_SimStopped`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_HasTrip`, `Cfg_2Spd`, `Cfg_Rev`, `Cfg_HasIntlkObj`, `Cfg_HasRunTimeObj`, `Cfg_HasMoreObj`, `Cfg_HasOvldObj`, `Cfg_OCmdResets`, `Cfg_ShedOnFailToTrip`, `Cfg_ShedOnIOFault`, `Cfg_HasTripFailAlm`, `Cfg_HasIntlkTripAlm`, `Cfg_HasIOFaultAlm`, `Cfg_TripFailResetReqd`, `Cfg_IntlkTripResetReqd`, `Cfg_IOFaultResetReqd`, `Cfg_TripFailAckReqd`, `Cfg_IntlkTripAckReqd`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_FastFwdText`, `Cfg_Label`, `Cfg_SlowRevText`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_MotorHO` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PCmd_Trip`, `PCmd_Reset`, `PCmd_TripFailAck`, `PCmd_TripFailSuppress`, `PCmd_TripFailUnsuppress`, `PCmd_TripFailUnshelve`, `PCmd_IntlkTripAck`, `PCmd_IntlkTripSuppress`, `PCmd_IntlkTripUnsuppress`, `PCmd_IntlkTripUnshelve`, `PCmd_IOFaultAck`, `PCmd_IOFaultSuppress`, `PCmd_IOFaultUnsuppress`, `PCmd_IOFaultUnshelve`
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Out_Trip`, `Val_Cmd`, `Val_Fdbk`, `Val_Sts`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Sts_RunningFastFwd`, `Sts_RunningSlowRev`, `Sts_Stopped`, `Sts_Tripping`, `Sts_Bypass`, `Sts_BypActive`, `Sts_OoS`, `Sts_BadFdbk`, `Sts_NotRdy`, `Sts_MaintByp`, `Sts_AlmInh`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 50 Biến Đầu Vào (`Input Parameters`) của `P_MotorHO`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_RunFastFwdFdbk`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Motor Running (single speed), fast (2-speed) or forward (reversing) |
| **`Inp_SlowRevFdbk`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Motor running slow (2-speed) or reverse (reversing) |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Interlocks OK, interlock not tripping motor |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Non-Bypassable Interlocks OK, interlock not tripping motor |
| **`Inp_OvldNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã trạng thái mức độ ưu tiên và xác nhận cảnh báo của rơ-le quá tải liên kết. |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Input Communication Status 0 = OK, 1 = fail |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Simulation input. When set to 1, the instruction uses simulation inputs (for example, Inp_SimRunFastFwd). When set to 0, the instruction uses input parameters (for example, Inp_RunFastFwdFdbk). |
| **`Inp_SimRunFastFwd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Sets simulated motor to running (fast or forward), 0 = no change |
| **`Inp_SimSlowRev`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Sets simulated motor to running slow or reverse, 0 = no change |
| **`Inp_SimStopped`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Sets simulated motor to stopped, 0 = no change |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Reset all fault conditions and latched Alarms |
| **`Cfg_HasTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Trip Output is connected to motor, 0 = Monitor only |
| **`Cfg_2Spd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Motor is 2 speed (fast, slow or stopped) |
| **`Cfg_Rev`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Motor is reversing (forward, reverse or stopped) |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tells HMI a P_Intlk is connected to Inp_Intlk. IMPORTANT: The name of the Interlock object in the controller must be this object's name with the suffix ‘_Intlk’. For example, if your P_MotorHO object has the name’MotorHO123’, then its Interlock object must be named ‘MotorHO123_Intlk’. |
| **`Cfg_HasRunTimeObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tells HMI a P_RunTime is connected. IMPORTANT: The name of the runtime object in the controller must be this object's name with the suffix ‘_RunTime’. For example, if your P_MotorHO object has the name ‘MotorHO123’, then its runtime object must be named ‘MotorHO123_RunTime’. |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_HasOvldObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Enables navigation to connected overload object’s faceplate. IMPORTANT: The name of the Overload object in the controller must be this object's name with the suffix ‘_Ovld’. For example, if your P_MotorHO object has the name ‘MotorHO123’, then its Overload object must be named ‘MotorHO123_Ovld’. |
| **`Cfg_OCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = OCmd_Trip. resets fault, 0 = reset req'd to reset fault |
| **`Cfg_ShedOnFailToTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Continue Trip and Alarm on Fail to Trip; 0 = Alarm only on Fail to Trip |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Trip Motor and Alarm on I/O Fault; 0 = Alarm only on I/O Fault |
| **`Cfg_HasTripFailAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Fail to Trip Alarm exists and will be checked |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Interlock Trip Alarm exists and will be checked |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = I/O Fault Alarm exists and will be checked |
| **`Cfg_TripFailResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Reset requied to clear Fail to Trip Alarm |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Reset required to clear Interlock Trip Alarm |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Reset required to clear I/O Fault Alarm |
| **`Cfg_TripFailAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Xác nhận (Acknowledge) required for Fail to Trip Alarm |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Xác nhận (Acknowledge) required for Interlock Trip Alarm |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Xác nhận (Acknowledge) required for I/O Fault Alarm |
| **`Cfg_TripFailSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_SimFdbkT`** | `REAL` | `3` | Ẩn (Tag) | [Biến cấu hình thông số] Delay to echo back of Open/Closed status when in Simulation (seconds) |
| **`Cfg_TripFailT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] After tripped, time to reach trip position before Alarm (seconds) |
| **`PCmd_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Trip Motor |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để ResetShed Latches and Cleared Alarms |
| **`PCmd_TripFailAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Fail to Trip Alarm |
| **`PCmd_TripFailSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Fail to Trip Alarm |
| **`PCmd_TripFailUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Fail to Trip Alarm |
| **`PCmd_TripFailUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Unsehlve Fail to Trip Alarm |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Interlock Trip Alarm |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Interlock Trip Alarm |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Interlock Trip Alarm |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Interlock Trip Alarm |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) I/O Fault Alarm |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) I/O Fault Alarm |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) I/O Fault Alarm |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) I/O Fault Alarm |

## 3. Bảng chi tiết 52 Biến Đầu Ra (`Output Parameters`) của `P_MotorHO`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Out_Trip`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1 = Trip (stop) Motor |
| **`SrcQ_IO`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final motor status source and quality: GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`Val_Cmd`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Command 0 = None, 7=Trip |
| **`Val_Fdbk`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Feedback 0 = Stop, 1 = Run, 2=Run Fast, 3=Run Slow, 4=Run Fwd, 5=Run Rev, 6=Invalid |
| **`Val_Sts`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] This is the Primary Status used to show the operator the confirmed device status: 0 = Powerup or Unknown 1 = Stop 2 = Run 3 = Fast 4 = Slow 5 = Forward 6 = Reverse 13 = Tripping 33 = Disabled |
| **`Val_Fault`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] This is the Device Fault Status used to show the operator the most severe device fault: 0 = None 18 = Fail to Trip 32 = I/O Fault 34 = Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Val_NotifyAll`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |
| **`Val_UnackAlmC`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Sts_RunningFastFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor is Running (single-speed), Fwd (reversing) or Fast (2-speed) |
| **`Sts_RunningSlowRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor is Running Rev (reversing) or Slow (2-speed) |
| **`Sts_Stopped`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor is confirmed Stopped |
| **`Sts_Tripping`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor requested to Trip and is not yet stopped |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bypassable Interlocks are Bypassed |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bypassing Active (Bypassed or Maintenance) |
| **`Sts_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Motor is Out of Service |
| **`Sts_BadFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Motor reports both fast and slow (fwd and rev) feedback |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Device Not Ready, see detail bits for reason |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi thiết bị ngắt tải (yêu cầu nhấn Reset) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Khóa liên động không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi truyền thông I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Thiết bị đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Nrdy_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Tripped (at device or by command) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=An Alarm is Disabled or Shelved, display icon |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình (kiểm tra các bit Err_* chi tiết để biết nguyên nhân) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Trip Check timer preset (use 0 to 2147483) |
| **`Err_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thời gian phản hồi mô phỏng (Cfg_SimFdbkT/SimRampT) ngoài phạm vi 0..2147483 giây |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thông số cảnh báo (Thời gian duy trì tối thiểu, thời gian Shelve, mức Severity) không hợp lệ |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc lỗi ngắt tải (Shed) đã hết điều kiện lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_TripFail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Motor Failed to Trip(did not stop) |
| **`Alm_TripFail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Motor Failed to Trip(did not stop) Alarm |
| **`Ack_TripFail`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Fail to Trip Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_TripFailDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Trip Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_TripFailShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Trip Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_TripFailSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Trip Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Motor Tripped by an Interlock NOT OK |
| **`Alm_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Alarm: Motor Tripped by an Interlock NOT OK |
| **`Ack_IntlkTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Interlock Trip Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IntlkTripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock Trip Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IntlkTripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock Trip Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IntlkTripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock Trip Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] I/O Comm Fault Status (0 = OK, 1 = Bad) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = I/O Fault Alarm |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = I/O Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`P_MotorHO`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 27 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_MotorHO`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'Hand Operated Motor'` | [Biến cấu hình thông số] Description for display on HMI. This string is shown in the title bar of the faceplate. |
| **`Cfg_FastFwdText`** | `STRING_16` | `'Running'` | [Biến cấu hình thông số] Text for Running, Fast or Forward for display on HMI |
| **`Cfg_Label`** | `STRING_20` | `'Motor Monitor'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI. This string appears on the graphic symbol. |
| **`Cfg_SlowRevText`** | `STRING_16` | `'Slow'` | [Biến cấu hình thông số] Text for Slow or Reverse for display on HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_MotorHO'` | [Biến cấu hình thông số] Tagname for display on HMI. This string is shown in the title bar of the faceplate. |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_MotorHO'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | Interlock Trip Alarm |
| **`IOFault`** | `P_Alarm` | `{...}` | I/O Fault Alarm |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bypass all Bypassable Interlocks and Permissives |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Check (not bypass) all Interlocks and Permissives |
| **`MCmd_IS`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để place motor In Service |
| **`MCmd_OoS`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để place motor Out of Service |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_IS`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_IS (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_OoS`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_OoS (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi, plus Xác nhận (Acknowledge) alarms |
| **`OCmd_Trip`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Trip Motor |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Trip`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Trip (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`TripFail`** | `P_Alarm` | `{...}` | Fail to Trip (motor did not stop) Alarm |
| **`Val_Notify`** | `SINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |
