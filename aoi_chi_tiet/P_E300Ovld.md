# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_E300Ovld`
**Thuộc chương:** Chương 3: Động cơ, Biến tần & Rơ-le Quá tải (Motors & Drives)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Motors 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_E300Ovld`:** **43** Biến đầu vào (`Input Parameters`) | **98** Biến đầu ra (`Output Parameters`) | **31** Biến cấu hình cục bộ (`Local Configuration Tags`)

> **⚡ Truy cập nhanh:** [⬅ Quay lại `README.md` (Thư mục gốc)](../README.md) | [⬅ Danh mục Tra cứu Nhanh (`00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md`)](./00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md)

## 0. Giới thiệu chung & Các nhu cầu cần dùng khối `P_E300Ovld`
- **Tệp định nghĩa khối lệnh (`.L5X`):** `P_E300Ovld_4.10.00_RUNG.L5X` *(Trang 262–268 trong tài liệu gốc PROCES-RM013I-EN-P)*
- **Giới thiệu chung chức năng:** Khối giám sát & điều khiển **Rơ-le bảo vệ động cơ điện tử thế hệ mới E300 (`193-ECM-ETR`)** trên mạng EtherNet/IP. Đọc đầy đủ dòng 3 pha, áp, công suất, % nhiệt tích lũy, đếm ngược thời gian chờ phục hồi nhiệt, cảnh báo sớm sắp quá tải và hỗ trợ `Remote Reset` / `Remote Test Trip`.
- **Các nhu cầu thực tế CẦN dùng khối `P_E300Ovld`:** Khi thiết kế tủ động lực thông minh (IntelliCENTER MCC) sử dụng rơ-le **E300 EtherNet/IP** kết hợp với `P_Motor*`.
- **Trường hợp KHÔNG dùng & Khối thay thế:** Khi rơ-le nhiệt chỉ là loại cơ khí thông thường đấu 1 tiếp điểm NC báo lỗi vào `P_DIn` / `P_Intlk`.

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_E300Ovld`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp`, `Ref_TripCodeList`, `Ref_WarningCodeList`, `Inp_Trip`, `Inp_TripReset`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_AllowOperTrip`, `Cfg_AllowOperReset`, `Cfg_AllowMaintTrip`, `Cfg_HasMoreObj`, `Cfg_AllowMaintReset`, `Cfg_HasWarnAlm`, `Cfg_HasTripAlm`, `Cfg_HasIOFaultAlm`, `Cfg_WarnResetReqd`, `Cfg_TripResetReqd`, `Cfg_IOFaultResetReqd`, `Cfg_WarnAckReqd`, `Cfg_TripAckReqd`, `Cfg_IOFaultAckReqd`, `Cfg_WarnSeverity`, `Cfg_TripSeverity`, `Cfg_IOFaultSeverity`, `Cfg_PulseT`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_InpPt00Txt`, `Cfg_InpPt01Txt`, `Cfg_InpPt02Txt`, `Cfg_InpPt03Txt`, `Cfg_InpPt04Txt`, `Cfg_InpPt05Txt`, `Cfg_Label`, `Cfg_OutPt00Txt`, `Cfg_OutPt01Txt`, `Cfg_OutPt02Txt`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_E300Ovld` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PCmd_Trip`, `PCmd_TripReset`, `PCmd_Reset`, `PCmd_WarnAck`, `PCmd_WarnSuppress`, `PCmd_WarnUnsuppress`, `PCmd_WarnUnshelve`, `PCmd_TripAck`, `PCmd_TripSuppress`, `PCmd_TripUnsuppress`, `PCmd_TripUnshelve`, `PCmd_IOFaultAck`, `PCmd_IOFaultSuppress`, `PCmd_IOFaultUnsuppress`, `PCmd_IOFaultUnshelve`
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Inp`, `Ref_TripCodeList`, `Ref_WarningCodeList`, `Out_TripReset`, `Out_RemoteTrip`, `Val_PctTherm`, `Val_CurrImbal`, `Val_AvgPctFLA`, `Val_AvgCurr`, `Val_L1Curr`, `Val_L2Curr`, `Val_L3Curr`, `Val_GndFaultCurr`, `Val_AvgVolts`, `Val_L1L2Volts`, `Val_L2L3Volts`, `Val_L3L1Volts`, `Val_TotRealPwr`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 43 Biến Đầu Vào (`Input Parameters`) của `P_E300Ovld`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp`** | `P_E300Ovld_Inp` | - | Hiện (Pin) | E300 Overload (193-ECM-ETR) Input Structure |
| **`Ref_TripCodeList`** | `P_DescList[1]` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] Tag containing List of Trip History Codes and Descriptions |
| **`Ref_WarningCodeList`** | `P_DescList[1]` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] Tag containing List of Warning History Codes and Descriptions |
| **`Inp_Trip`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Send Remote Trip request to Overload Relay |
| **`Inp_TripReset`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Send Trip Reset request to Overload Relay |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Reset All Latched Alarms |
| **`Cfg_AllowOperTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Remote Trip function is available to Operator |
| **`Cfg_AllowOperReset`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Trip Reset function is available to operator |
| **`Cfg_AllowMaintTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Remote Trip function is available to Maintenance |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_AllowMaintReset`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Trip Reset function is available to Maintenance |
| **`Cfg_HasWarnAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_WarnResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_TripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_WarnAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_TripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_WarnSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_TripSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_PulseT`** | `REAL` | `5` | Ẩn (Tag) | [Biến cấu hình thông số] Time (seconds) to pulse Trip Reset or Remote Trip output to E3 |
| **`Cfg_LocalResetReqdT`** | `REAL` | `3600` | Ẩn (Tag) | [Biến cấu hình thông số] No more than (1..3) remote Trip Resets are allowed in this amount of time (seconds) |
| **`Cfg_LocalResetReqdNum`** | `DINT` | `3` | Ẩn (Tag) | [Biến cấu hình thông số] No more than this many remote Trip Resets are allowed in (cfg'd) time (1..3) |
| **`PCmd_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để send Remote Trip request to Overload Relay |
| **`PCmd_TripReset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để send Trip Reset request to Overload Relay |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Reset all Alarms requiring Reset |
| **`PCmd_WarnAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Impending Trip Warning alarm |
| **`PCmd_WarnSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Impending Trip Warning alarm |
| **`PCmd_WarnUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Impending Trip Warning alarm |
| **`PCmd_WarnUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Impending Trip Warning alarm |
| **`PCmd_TripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Overload Tripped alarm |
| **`PCmd_TripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Overload Tripped alarm |
| **`PCmd_TripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Overload Tripped alarm |
| **`PCmd_TripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Overload Tripped alarm |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) I/O Fault alarm |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) I/O Fault alarm |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) I/O Fault alarm |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Program Comnmand to Gỡ bỏ tạm hoãn (Unshelve) I/O Fault alarm |
| **`Sts_TripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Overload Trip Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |

## 3. Bảng chi tiết 98 Biến Đầu Ra (`Output Parameters`) của `P_E300Ovld`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Inp`** | `P_E300Ovld_Inp` | - | Hiện (Pin) | E300 Overload (193-ECM-ETR) Input Structure |
| **`Ref_TripCodeList`** | `P_DescList[1]` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] Tag containing List of Trip History Codes and Descriptions |
| **`Ref_WarningCodeList`** | `P_DescList[1]` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] Tag containing List of Warning History Codes and Descriptions |
| **`Out_TripReset`** | `BOOL` | `??` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1 = Reset Overload Trip |
| **`Out_RemoteTrip`** | `BOOL` | `??` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1 = Initiate Remote Trip |
| **`Val_PctTherm`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Motor Thermal Usage (%) (Trip / Fault at 100%) |
| **`Val_CurrImbal`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Current Imbalance (%) |
| **`Val_AvgPctFLA`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Average Current as Percent Full Load Amps (%) |
| **`Val_AvgCurr`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Average Current (Amps) |
| **`Val_L1Curr`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] L1 Phase Current (Amps) |
| **`Val_L2Curr`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] L2 Phase Current (Amps) |
| **`Val_L3Curr`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] L3 Phase Current (Amps) |
| **`Val_GndFaultCurr`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Ground Fault Current (Amps) |
| **`Val_AvgVolts`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Average Line-to-Line Voltage (Volts) |
| **`Val_L1L2Volts`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] L1 Phase to L2 Phase Voltage (Volts) |
| **`Val_L2L3Volts`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] L2 Phase to L3 Phase Voltage (Volts) |
| **`Val_L3L1Volts`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] L3 Phase to L1 Phase Voltage (Volts) |
| **`Val_TotRealPwr`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Total Real Power (kW) |
| **`Val_TotReactPwr`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Total Reactive Power (kVAR) |
| **`Val_TotAppPwr`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Total Apparent Power (kVA) |
| **`Val_PwrFact`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Power Factor (%) |
| **`Val_MinToReset`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Time until overload can be reset (min in mmm:ss) |
| **`Val_SecToReset`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Time unitl overload can be reset (sec in mmm:ss) |
| **`Val_MinToStart`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Time until motor can be started (min in mmm:ss) |
| **`Val_SecToStart`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Time until motor can be started (sec in mmm:ss) |
| **`Val_TripCode`** | `INT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Overload Trip History 0 (most recent trip) Code (enum) |
| **`Val_WarningCode`** | `INT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Overload Warning History 0 (most recent warning) Code (enum) |
| **`Val_InvCfgParam`** | `INT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] ID number of misconfigured parameter in E300 (Par #) |
| **`SrcQ_IO`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Source and Quality of primary I/O (enum) |
| **`SrcQ`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final device status source and quality: GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`Val_Fault`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Fault Status 0 = None, 15=Warning, 30 = Tripped, 31 = Local Reset Reqd, 32=I/O Fault, 34=Config Error |
| **`Val_NotifyAll`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận của khối này cộng với khối kênh (enum) |
| **`Val_UnackAlmC`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Sts_MotorCurr`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor Current is Present |
| **`Sts_GFCurr`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Ground Fault Current is Present |
| **`Sts_MotorVolts`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor Voltage Preset |
| **`Sts_EStartEna`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Emergency Start capability is enabled |
| **`Sts_DevLgxEna`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = DeviceLogix capability is enabled |
| **`Sts_FdbkTmoEna`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Feedback Timeout Enabled |
| **`Sts_OpStaPres`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = E300 has Operator Station present |
| **`Sts_VoltSense`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Voltage Sensing capability is present (e.g., VIG) |
| **`Sts_IntGndFaultSense`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Internal Ground Fault Sensing is Present (IG or VIG) |
| **`Sts_ExtGndFaultSense`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = External Ground Fault Sensing is present (IG or VIG) |
| **`Sts_PTCSense`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = PTC Temperature Sensing is present |
| **`Sts_InpPt00`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Status of discrete input Pt00 |
| **`Sts_InpPt01`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Status of discrete input Pt01 |
| **`Sts_InpPt02`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Status of discrete input Pt02 |
| **`Sts_InpPt03`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Status of discrete input Pt03 |
| **`Sts_InpPt04`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Status of discrete input Pt04 |
| **`Sts_InpPt05`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Status of discrete input Pt05 |
| **`Sts_OutPt00`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Status of discrete output Pt00 |
| **`Sts_OutPt01`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Status of discrete output Pt01 |
| **`Sts_OutPt02`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Status of discrete output Pt02 |
| **`Sts_DeviceSts1`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Device Status 1 (Par 21) (Control, Sensing, Expansion Module Presence) |
| **`Sts_InpSts1`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Input Status 1 (Par 17) (Discrete Expansion Module Inputs) |
| **`Sts_OutSts`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Output Status (Par 18) (Discrete Expansion Module Outputs) |
| **`Sts_OpSta`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Local Operator Station Stauts (Par 19) |
| **`Sts_CurrentTrip`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Current Trip Status Bits (Par 4) |
| **`Sts_CurrentWarning`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Current Warning Status Bits (Par 10) |
| **`Sts_VoltageTrip`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Voltage Trip Status Bits (Par 5) |
| **`Sts_VoltageWarning`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Voltage Warning Status Bits (Par 11) |
| **`Sts_PowerTrip`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Power Trip Status Bits (Par 6) |
| **`Sts_PowerWarning`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Power Warning Status Bits (Par 12) |
| **`Sts_ControlTrip`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Control Trip Status Bits (Par 7) |
| **`Sts_ControlWarning`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Control Warning Status Bits (Par 13) |
| **`Sts_AnalogTrip`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Analog Trip Status Bits (Par 8) |
| **`Sts_AnalogWarning`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Analog Warning Status Bits (Par 14) |
| **`Sts_DevLgxOutputs`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] DeviceLogix Output Bits (Par 348) |
| **`Sts_LocalResetReqd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Too many remote resets: go to starter, find cause, and reset there |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Device Not Ready, see detail bits for reason |
| **`Nrdy_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Tripped (at device or by command) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi truyền thông I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OvldNR`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Overload powerup not complete |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=An Alarm is Disabled or Shelved, display icon |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình (kiểm tra các bit Err_* chi tiết để biết nguyên nhân) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Reset pulse timer preset (use 0 to 2147483) |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc lỗi ngắt tải (Shed) đã hết điều kiện lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Alarm Min On Time or Severity |
| **`Sts_Warn`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Warning of impending trip (See Sts_WarnBits for reason) |
| **`Alm_Warn`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Alarm: warning of impending trip |
| **`Ack_Warn`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Trip Warning Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_WarnDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Trip Warning Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_WarnShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Trip Warning Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_WarnSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Trip Warning Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_Trip`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Overload Tripped (See Sts_TripBits for reason) |
| **`Alm_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Alarm: Overload Tripped |
| **`Ack_Trip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Overload Trip Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_TripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Overload Trip Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_TripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Overload Trip Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IOFault`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] I/O Comm Fault Status (0 = OK, 1 = Bad) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = I/O Fault Alarm |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = I/O Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`P_E300Ovld`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 31 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_E300Ovld`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_8` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'Motor Overload Relay'` | [Biến cấu hình thông số] Description for display on HMI. This string is shown in the title bar of the faceplate. |
| **`Cfg_InpPt00Txt`** | `STRING_20` | `'Starter Aux Contact'` | [Biến cấu hình thông số] Text for Discrete Input Pt00 label |
| **`Cfg_InpPt01Txt`** | `STRING_20` | `''` | [Biến cấu hình thông số] Text for Discrete Input Pt01 label |
| **`Cfg_InpPt02Txt`** | `STRING_20` | `''` | [Biến cấu hình thông số] Text for Discrete Input Pt02 label |
| **`Cfg_InpPt03Txt`** | `STRING_20` | `''` | [Biến cấu hình thông số] Text for Discrete Input Pt03 label |
| **`Cfg_InpPt04Txt`** | `STRING_20` | `''` | [Biến cấu hình thông số] Text for Discrete Input Pt04 label |
| **`Cfg_InpPt05Txt`** | `STRING_20` | `''` | [Biến cấu hình thông số] Text for Discrete Input Pt05 label |
| **`Cfg_Label`** | `STRING_20` | `'Overload Relay'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI. This string appears on the graphic symbol. |
| **`Cfg_OutPt00Txt`** | `STRING_20` | `'Starter Energized'` | [Biến cấu hình thông số] Text for Discrete Output Pt00 label |
| **`Cfg_OutPt01Txt`** | `STRING_20` | `''` | [Biến cấu hình thông số] Text for Discrete Output Pt01 label |
| **`Cfg_OutPt02Txt`** | `STRING_20` | `''` | [Biến cấu hình thông số] Text for Discrete Output Pt02 label |
| **`Cfg_Tag`** | `STRING_20` | `'P_E300Ovld'` | [Biến cấu hình thông số] Tagname for display on HMI. This string is shown in the title bar of the faceplate. |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_E300Ovld'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate |
| **`IOFault`** | `P_Alarm` | `{...}` | Alarm: I/O Fault |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset all Alarms requiring Reset |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset all Alarms and latched Shed conditions |
| **`OCmd_Trip`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để send Remote Trip request to Overload Relay |
| **`OCmd_TripReset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để send Trip Reset request to Overload Relay |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = At least one Alarm requires Reset |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = At least one Alarm requires Reset or Ack |
| **`ORdy_Trip`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Ready to receive OCmd_Trip (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_TripReset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Ready to receive OCmd_TripReset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`Trip`** | `P_Alarm` | `{...}` | Alarm: Overload has tripped |
| **`Val_AnalogIn`** | `INT[12]` | `{...}` | [Giá trị thực tế đầu ra (Logic/HMI)] Analog Input Values 0 to 65535 raw UINT (unsigned) |
| **`Val_Notify`** | `SINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |
| **`Val_TripDesc`** | `STRING_40` | `'` | [Giá trị thực tế đầu ra (Logic/HMI)] Description of Trip Reason (given TripHistory0 code) |
| **`Val_WarningDesc`** | `STRING_40` | `‘Logic not yet scanned.’` | [Giá trị thực tế đầu ra (Logic/HMI)] Description of Warning Reason (given WarnHistory0 code) |
| **`Warn`** | `P_Alarm` | `{...}` | Alarm: Warning of impending trip |
