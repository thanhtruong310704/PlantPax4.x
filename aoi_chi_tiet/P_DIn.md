# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_DIn`
**Thuộc chương:** Chương 1: Xử lý tín hiệu Vào/Ra (I/O Processing)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `IO Processing 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_DIn`:** **33** Biến đầu vào (`Input Parameters`) | **34** Biến đầu ra (`Output Parameters`) | **22** Biến cấu hình cục bộ (`Local Configuration Tags`)

> **⚡ Truy cập nhanh:** [⬅ Quay lại `README.md` (Thư mục gốc)](../README.md) | [⬅ Danh mục Tra cứu Nhanh (`00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md`)](./00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md)

## 0. Giới thiệu chung & Các nhu cầu cần dùng khối `P_DIn`
- **Tệp định nghĩa khối lệnh (`.L5X`):** `P_DIn_4.10.00_AOI.L5X` *(Trang 46–52 trong tài liệu gốc PROCES-RM013I-EN-P)*
- **Giới thiệu chung chức năng:** Khối xử lý 1 tín hiệu đầu vào số (`BOOL`). Hỗ trợ đảo mức logic (`Cfg_Inv`), lọc chống rung tiếp điểm (**Debounce On-Delay / Off-Delay**), đặt tên hiển thị trạng thái 0/1 trên HMI, so sánh với trạng thái mục tiêu để tạo cảnh báo bất thường và hỗ trợ Substitute/Sim.
- **Các nhu cầu thực tế CẦN dùng khối `P_DIn`:** Khi đưa bất kỳ **công tắc áp suất (PSH/PSL), công tắc mức (LSH/LSL), công tắc dòng chảy (FSL), nút dừng khẩn cấp, báo cháy** lên màn hình SCADA và cần lọc chống báo giả do sóng sánh chất lỏng (Debounce).
- **Trường hợp KHÔNG dùng & Khối thay thế:** Khi cần giám sát công tắc tốc độ quay gắn với trạng thái chạy mô-tơ hoặc cần 2 cảnh báo riêng → Dùng [`P_DInAdv.md`](./P_DInAdv.md).

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_DIn`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_PV`, `Inp_IOFault`, `Inp_PVNotify`, `Inp_Target`, `Inp_Gate`, `Inp_Sim`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_NoSubstPV`, `Cfg_NormTextVis`, `Cfg_HasMoreObj`, `Cfg_HasTgtDisagreeAlm`, `Cfg_TgtDisagreeResetReqd`, `Cfg_TgtDisagreeAckReqd`, `Cfg_HasIOFaultAlm`, `Cfg_IOFaultResetReqd`, `Cfg_IOFaultAckReqd`, `Cfg_TgtDisagreeSeverity`, `Cfg_IOFaultSeverity`, `Cfg_Debounce`, `Cfg_GateDly`, `Cfg_TgtDisagreeOnDly`, `Cfg_TgtDisagreeOffDly`
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_0StText`, `Cfg_1StText`, `Cfg_Area`, `Cfg_Desc`, `Cfg_Label`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_DIn` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PCmd_Reset`, `PCmd_TgtDisagreeAck`, `PCmd_TgtDisagreeSuppress`, `PCmd_TgtDisagreeUnsuppress`, `PCmd_TgtDisagreeUnshelve`, `PCmd_IOFaultAck`, `PCmd_IOFaultSuppress`, `PCmd_IOFaultUnsuppress`, `PCmd_IOFaultUnshelve`
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Val_Sts`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Sts`, `Sts_PV`, `Sts_SubstPV`, `Sts_InpPV`, `Sts_PVBad`, `Sts_MaintByp`, `Sts_AlmInh`, `Sts_Err`, `Sts_RdyReset`, `Sts_RdyAck`, `Sts_TgtDisagreeCmp`, `Sts_TgtDisagreeGate`, `Sts_TgtDisagree`, `Alm_TgtDisagree`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 33 Biến Đầu Vào (`Input Parameters`) của `P_DIn`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_PV`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu đo lường đầu vào (Biến quá trình PV) nối từ cảm biến / thẻ Analog Input |
| **`Inp_IOFault`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Input Comm Status or Quality 1=Bad, 0=OK |
| **`Inp_PVNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã trạng thái mức độ ưu tiên và xác nhận cảnh báo của đối tượng PV liên kết. |
| **`Inp_Target`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Target State of Input (Disagree Status if not in Target State) |
| **`Inp_Gate`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] This parameter is the gate input used for status detection. When set to 1, the target disagree monitoring is enabled. The on-delay and off-delay timers are applied after the gate delay timer. When set to 0, detection is disabled and the target disagree status output is forced off. If the status is used as an alarm, this input acts as a suppression-by-design condition. |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Simulation input. When set to 1, the instruction uses simulation parameters (for example, Set_SimPV) to calculate output. When set to 0, the instruction uses input parameters (for example, Inp_PV) to calculate output. |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân đầu vào dùng để xóa (Reset) tất cả các cảnh báo đang chờ Reset từ chương trình PLC (1 = Xóa cảnh báo). |
| **`Cfg_NoSubstPV`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Khóa tính năng nhập giá trị thay thế (Substitute PV) của Bảo trì: Khi = 0, cho phép nhân viên bảo trì nhập giá trị PV thay thế; khi = 1, cấm sử dụng tính năng Substitute PV. When Cfg_NoSubstPV is 0, the commands MCmd_SubstPV and MCmd_InpPV are used to select the input PV or the substitute PV. Sts_SubstPV is 1 when the substitute PV đang được chọn làm Nguồn lệnh điều khiển hiện hành. |
| **`Cfg_NormTextVis`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Display state text in Normal state (= target). 0 = Hide state text in Normal state (some HMI objects). |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_HasTgtDisagreeAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Target Disagree Alarm exists and will be checked |
| **`Cfg_TgtDisagreeResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_TgtDisagreeAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=I/O Fault Alarm exists and will be checked |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=I/O Fault Alarm is latched in, requires Reset Command to clear |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=I/O Fault Alarm must be acknowledged |
| **`Cfg_TgtDisagreeSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] I/O Fault Alarm Severity: 1..250=Low, 251..500=Medium, 501..750=High, 751..1000=Urgent |
| **`Cfg_Debounce`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time Input Sts must maintain state (sec) |
| **`Cfg_GateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Time Inp_Gate must be true before Disagree Status is checked (sec) |
| **`Cfg_TgtDisagreeOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the minimum time (in seconds) the PV must disagree with the target for the status to be set. On-delay times are used to avoid unnecessary alarms when an output (Val) overshoots its threshold (for example, Val_HiHiLim). |
| **`Cfg_TgtDisagreeOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the amount of time (in seconds) the PV must agree with the target to clear the status. Off-delay times are used to reduce chattering alarms. EXAMPLE: If Cfg_OffDly is 5 seconds, the PV must agree with the target for 5 seconds before the status is returned to normal. |
| **`Set_SimPV`** | `BOOL` | `0` | Ẩn (Tag) | PV used in Simulation (Inp_Sim=1) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_Reset = 1 để xóa tất cả các cảnh báo chốt đang chờ Reset (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_TgtDisagreeAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_TgtDisagreeSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) the Target Disagree Alarm |
| **`PCmd_TgtDisagreeUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) the Target Disagree Alarm |
| **`PCmd_TgtDisagreeUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) the I/O Fault Alarm |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) the I/O Fault Alarm |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) the I/O Fault Alarm |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) the I/O Fault Alarm |

## 3. Bảng chi tiết 34 Biến Đầu Ra (`Output Parameters`) của `P_DIn`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Enable Out: The EnableOut signal is not manipulated by this instruction. Its output always reflects EnableIn Input state. |
| **`SrcQ_IO`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final output source and quality: GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`Val_Sts`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device confirmed status: 0 = PV Good 6 = PV Bad 7 = Substitute PV |
| **`Val_Fault`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device fault status: 0 = None 16 = Target Disagree 34 = Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Val_NotifyAll`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |
| **`Val_UnackAlmC`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Sts`** | `BOOL` | `0` | Hiện (Pin) | Discrete Input Status (incl. Debounce, Manual Override, if used) |
| **`Sts_PV`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Discrete Input Status (actual, not subject to Override) |
| **`Sts_SubstPV`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Using Substitute PV (Input being overridden) |
| **`Sts_InpPV`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Using Input PV (normal) |
| **`Sts_PVBad`** | `BOOL` | `??` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Input Comm Status/ Quality (0=OK, 1=Bad) |
| **`Sts_MaintByp`** | `BOOL` | `??` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device has a Maintenance Bypass function active |
| **`Sts_AlmInh`** | `BOOL` | `??` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang bị Tạm hoãn (Shelved) hoặc Vô hiệu hóa (Disabled), hiển thị biểu tượng trên HMI |
| **`Sts_Err`** | `BOOL` | `??` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Error in Config: see detail bits for reason |
| **`Err_Timer`** | `BOOL` | `??` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Invalid Timer Preset (use 0 to 2147483) |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thông số cảnh báo (Thời gian duy trì tối thiểu, thời gian Shelve, mức Severity) không hợp lệ |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc lỗi ngắt tải (Shed) đã hết điều kiện lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_TgtDisagreeCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Input vs. Target comparicon result 1=Disagree |
| **`Sts_TgtDisagreeGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Target Disagree trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_TgtDisagree`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Input is not in Target state |
| **`Alm_TgtDisagree`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Alarm: Input is not in Target state |
| **`Ack_TgtDisagree`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Target Disagree Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_TgtDisagreeDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Target Disagree Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_TgtDisagreeShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Target Disagree Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_TgtDisagreeSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Target Disagree Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Input communication fault (Inp_IOFault = 1) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Alarm: Input communication fault |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=I/O Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`P_DIn`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 22 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_DIn`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_0StText`** | `STRING_8` | `'Off'` | [Biến cấu hình thông số] Text to display in PV=0 State |
| **`Cfg_1StText`** | `STRING_8` | `'On'` | [Biến cấu hình thông số] Text to display in PV=1 State |
| **`Cfg_Area`** | `STRING_8` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'Discrete Input'` | [Biến cấu hình thông số] Description for display on HMI |
| **`Cfg_Label`** | `STRING_20` | `'Discrete Input'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_DIn'` | [Biến cấu hình thông số] Tagname for display on HMI |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_DIn'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI và phần mềm thông tin |
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
| **`TgtDisagree`** | `P_Alarm` | `{...}` | Target Disagree Alarm |
| **`TgtDisagreeGate`** | `P_Gate` | `{...}` | Target Disagree Condition Gate/Delay |
| **`Val_Notify`** | `SINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận của riêng khối này (enum) |
