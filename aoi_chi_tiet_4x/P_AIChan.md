# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_AIChan`
**Thuộc chương:** Chương 1: Xử lý tín hiệu Vào/Ra (I/O Processing)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `IO Processing 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_AIChan`:** **52** Biến đầu vào (`Input Parameters`) | **45** Biến đầu ra (`Output Parameters`) | **15** Biến cấu hình cục bộ (`Local Configuration Tags`)

> **⚡ Truy cập nhanh:** [⬅ Quay lại Danh mục Tra cứu Nhanh 55 Khối AOI (`00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md`)](./00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md)

## 0. Giới thiệu chung & Các nhu cầu cần dùng khối `P_AIChan`
- **Tệp định nghĩa khối lệnh (`.L5X`):** `P_AIChan_4.10.00_AOI.L5X` *(Trang 26–29 trong tài liệu gốc PROCES-RM013I-EN-P)*
- **Giới thiệu chung chức năng:** Khối chẩn đoán chuyên sâu cấp độ kênh phần cứng Analog Input. Kiểm tra 9 tình huống bất thường của thẻ I/O và cảm biến (lỗi module, lỗi kênh, vượt dải, cảm biến bị đơ/treo không đổi giá trị *Stuck Input*, lỗi NaN, yêu cầu bảo trì) để phân loại chất lượng dữ liệu thành `Good`, `Uncertain` hoặc `Bad`.
- **Các nhu cầu thực tế CẦN dùng khối `P_AIChan`:** Khi dùng thẻ Analog Input có hỗ trợ thông tin chẩn đoán mở rộng hoặc hệ thống yêu cầu **phát hiện cảm biến bị kẹt/đóng băng giá trị (`Stuck PV`)** trước khi cấp tín hiệu vào khối `P_AIn`, `P_AInAdv` hoặc `P_AInDual`.
- **Trường hợp KHÔNG dùng & Khối thay thế:** Không dùng độc lập để hiển thị giá trị quá trình hay cảnh báo ngưỡng công nghệ HH/H/L/LL (luôn phải nối sau nó một khối `P_AIn*`).

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_AIChan`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_Raw`, `Inp_ModFault`, `Inp_ChanFault`, `Inp_OutOfSpec`, `Inp_FuncCheck`, `Inp_MaintReqd`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_ClampSB`, `Cfg_FailOnUncertain`, `Cfg_PVDecPlcs`, `Cfg_HasFailAlm`, `Cfg_HasMoreObj`, `Cfg_FailResetReqd`, `Cfg_FailAckReqd`, `Cfg_FailSeverity`, `Cfg_InpRawMin`, `Cfg_InpRawMax`, `Cfg_PVEUMin`, `Cfg_PVEUMax`, `Cfg_PVHiLim`, `Cfg_PVLoLim`, `Cfg_InpOORHiLim`, `Cfg_InpOORLoLim`, `Cfg_InpOORDB`, `Cfg_InpOOROnDly`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_EU`, `Cfg_Label`, `Cfg_RU`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_AIChan` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PCmd_FailAck`, `PCmd_FailSuppress`, `PCmd_FailUnsuppress`, `PCmd_FailUnshelve`, `PCmd_Reset`
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Val_InpRaw`, `Val`, `Val_PVEUMin`, `Val_PVEUMax`, `Val_Sts`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Sts_PVGood`, `Sts_PVUncertain`, `Sts_PVBad`, `Sts_UseInp`, `Sts_HoldLast`, `Sts_Clamped`, `Sts_Replaced`, `Sts_InpOORHiCmp`, `Sts_InpOORLoCmp`, `Sts_InpOutOfRange`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 52 Biến Đầu Vào (`Input Parameters`) của `P_AIChan`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_Raw`** | `REAL` | `4` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Input Signal from Transmitter or Sensor (RU) |
| **`Inp_ModFault`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = I/O module failure or module communication status bad 0 = OK |
| **`Inp_ChanFault`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = I/O channel fault or failure 0 = OK |
| **`Inp_OutOfSpec`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Out of specification (PV uncertain, from instrument) 0 = OK |
| **`Inp_FuncCheck`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Function check (PV substituted, from instrument) 0 = OK |
| **`Inp_MaintReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Maintenance Required (from instrument) |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân đầu vào dùng để xóa (Reset) tất cả các cảnh báo đang chờ Reset từ chương trình PLC (1 = Xóa cảnh báo). |
| **`Cfg_ClampSB`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Clamping Snap-To Band to Clamp when PV gets near to Limit |
| **`Cfg_FailOnUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Raise Sts_Fail (and alarm) if Bad or Uncertain quality 0 = Only if Bad quality |
| **`Cfg_PVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho giá trị PV trên HMI (từ 0 đến 6 chữ số). |
| **`Cfg_HasFailAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_FailResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_FailAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_FailSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_InpRawMin`** | `REAL` | `4` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đầu vào thô tối thiểu (Raw Min) dùng để quy đổi tỷ lệ tuyến tính. (RU) |
| **`Cfg_InpRawMax`** | `REAL` | `20` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đầu vào thô tối đa (Raw Max) dùng để quy đổi tỷ lệ tuyến tính. (RU) |
| **`Cfg_PVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] PV (Output) Minimum for Scaling (EU) |
| **`Cfg_PVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tối đa (EU Max) của PV tương ứng với Raw Max. TIP The P_AIChan instruction supports reverse scaling; either the raw (input) or engineering (scaled) range can be reversed (maximum less than minimum). |
| **`Cfg_PVHiLim`** | `REAL` | `1.5000000000000001E+38` | Ẩn (Tag) | [Biến cấu hình thông số] PV High clamping threshold (engineering units). Valid = any float greater than or equal to Cfg_PVLoLim. |
| **`Cfg_PVLoLim`** | `REAL` | `-1.5000000000000001E+38` | Ẩn (Tag) | [Biến cấu hình thông số] PV Low clamping threshold (engineering units). Valid = any float less than or equal to Cfg_PVHiLim. |
| **`Cfg_InpOORHiLim`** | `REAL` | `20.733333999999999` | Ẩn (Tag) | [Biến cấu hình thông số] Out-of-Range (fail) high limit (raw units). |
| **`Cfg_InpOORLoLim`** | `REAL` | `3.6666666999999999` | Ẩn (Tag) | [Biến cấu hình thông số] Out-of-Range (fail) low limit (raw units). |
| **`Cfg_InpOORDB`** | `REAL` | `6.6666669999999997E-2` | Ẩn (Tag) | [Biến cấu hình thông số] Out-of-Range (fail) high/low deadband (raw units). |
| **`Cfg_InpOOROnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time out-of-range to raise status (seconds). |
| **`Cfg_InpOOROffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time in-range to clear out-of-range status (seconds). |
| **`Cfg_InpOORAction`** | `BOOL` | - | Ẩn (Tag) | [Biến cấu hình thông số] PV action on: Out-of-range, stuck (unchanging), PV not a number, I/O module fault, I/O channel fault, Inp_PVUncertain, function check, maintenance required, or AOI configuration error: 1 = Pass input PV through unchanged 2 = Hold last good PV value 3 = Replace PV value with Cfg_PVReplaceVal |
| **`Cfg_InpStuckAction`** | `BOOL` | - | Ẩn (Tag) | [Biến cấu hình thông số] PV action on: Out-of-range, stuck (unchanging), PV not a number, I/O module fault, I/O channel fault, Inp_PVUncertain, function check, maintenance required, or AOI configuration error: 1 = Pass input PV through unchanged 2 = Hold last good PV value 3 = Replace PV value with Cfg_PVReplaceVal |
| **`Cfg_InpNaNAction`** | `BOOL` | - | Ẩn (Tag) | [Biến cấu hình thông số] PV action on: Out-of-range, stuck (unchanging), PV not a number, I/O module fault, I/O channel fault, Inp_PVUncertain, function check, maintenance required, or AOI configuration error: 1 = Pass input PV through unchanged 2 = Hold last good PV value 3 = Replace PV value with Cfg_PVReplaceVal |
| **`Cfg_ModFaultAction`** | `BOOL` | - | Ẩn (Tag) | [Biến cấu hình thông số] PV action on: Out-of-range, stuck (unchanging), PV not a number, I/O module fault, I/O channel fault, Inp_PVUncertain, function check, maintenance required, or AOI configuration error: 1 = Pass input PV through unchanged 2 = Hold last good PV value 3 = Replace PV value with Cfg_PVReplaceVal |
| **`Cfg_ChanFaultAction`** | `BOOL` | - | Ẩn (Tag) | [Biến cấu hình thông số] PV action on: Out-of-range, stuck (unchanging), PV not a number, I/O module fault, I/O channel fault, Inp_PVUncertain, function check, maintenance required, or AOI configuration error: 1 = Pass input PV through unchanged 2 = Hold last good PV value 3 = Replace PV value with Cfg_PVReplaceVal |
| **`Cfg_OutOfSpecAction`** | `BOOL` | - | Ẩn (Tag) | [Biến cấu hình thông số] PV action on: Out-of-range, stuck (unchanging), PV not a number, I/O module fault, I/O channel fault, Inp_PVUncertain, function check, maintenance required, or AOI configuration error: 1 = Pass input PV through unchanged 2 = Hold last good PV value 3 = Replace PV value with Cfg_PVReplaceVal |
| **`Cfg_FuncCheckAction`** | `BOOL` | - | Ẩn (Tag) | [Biến cấu hình thông số] PV action on: Out-of-range, stuck (unchanging), PV not a number, I/O module fault, I/O channel fault, Inp_PVUncertain, function check, maintenance required, or AOI configuration error: 1 = Pass input PV through unchanged 2 = Hold last good PV value 3 = Replace PV value with Cfg_PVReplaceVal |
| **`Cfg_MaintReqdAction`** | `BOOL` | - | Ẩn (Tag) | [Biến cấu hình thông số] PV action on: Out-of-range, stuck (unchanging), PV not a number, I/O module fault, I/O channel fault, Inp_PVUncertain, function check, maintenance required, or AOI configuration error: 1 = Pass input PV through unchanged 2 = Hold last good PV value 3 = Replace PV value with Cfg_PVReplaceVal |
| **`Cfg_CfgErrAction`** | `BOOL` | - | Ẩn (Tag) | [Biến cấu hình thông số] PV action on: Out-of-range, stuck (unchanging), PV not a number, I/O module fault, I/O channel fault, Inp_PVUncertain, function check, maintenance required, or AOI configuration error: 1 = Pass input PV through unchanged 2 = Hold last good PV value 3 = Replace PV value with Cfg_PVReplaceVal |
| **`Cfg_InpOORQual`** | `BOOL` | - | Ẩn (Tag) | [Biến cấu hình thông số] PV quality to report for: Out-of-range, stuck (unchanging), PV not a number, I/O module fault, I/O channel fault, Inp_PVUncertain, function check, maintenance required, or AOI configuration error: 1 = Good 2 = Uncertain 3 = Bad |
| **`Cfg_InpStuckQual`** | `BOOL` | - | Ẩn (Tag) | [Biến cấu hình thông số] PV quality to report for: Out-of-range, stuck (unchanging), PV not a number, I/O module fault, I/O channel fault, Inp_PVUncertain, function check, maintenance required, or AOI configuration error: 1 = Good 2 = Uncertain 3 = Bad |
| **`Cfg_InpNaNQual`** | `BOOL` | - | Ẩn (Tag) | [Biến cấu hình thông số] PV quality to report for: Out-of-range, stuck (unchanging), PV not a number, I/O module fault, I/O channel fault, Inp_PVUncertain, function check, maintenance required, or AOI configuration error: 1 = Good 2 = Uncertain 3 = Bad |
| **`Cfg_ModFaultQual`** | `BOOL` | - | Ẩn (Tag) | [Biến cấu hình thông số] PV quality to report for: Out-of-range, stuck (unchanging), PV not a number, I/O module fault, I/O channel fault, Inp_PVUncertain, function check, maintenance required, or AOI configuration error: 1 = Good 2 = Uncertain 3 = Bad |
| **`Cfg_ChanFaultQual`** | `BOOL` | - | Ẩn (Tag) | [Biến cấu hình thông số] PV quality to report for: Out-of-range, stuck (unchanging), PV not a number, I/O module fault, I/O channel fault, Inp_PVUncertain, function check, maintenance required, or AOI configuration error: 1 = Good 2 = Uncertain 3 = Bad |
| **`Cfg_OutOfSpecQual`** | `BOOL` | - | Ẩn (Tag) | [Biến cấu hình thông số] PV quality to report for: Out-of-range, stuck (unchanging), PV not a number, I/O module fault, I/O channel fault, Inp_PVUncertain, function check, maintenance required, or AOI configuration error: 1 = Good 2 = Uncertain 3 = Bad |
| **`Cfg_FuncCheckQual`** | `BOOL` | - | Ẩn (Tag) | [Biến cấu hình thông số] PV quality to report for: Out-of-range, stuck (unchanging), PV not a number, I/O module fault, I/O channel fault, Inp_PVUncertain, function check, maintenance required, or AOI configuration error: 1 = Good 2 = Uncertain 3 = Bad |
| **`Cfg_MaintReqdQual`** | `BOOL` | - | Ẩn (Tag) | [Biến cấu hình thông số] PV quality to report for: Out-of-range, stuck (unchanging), PV not a number, I/O module fault, I/O channel fault, Inp_PVUncertain, function check, maintenance required, or AOI configuration error: 1 = Good 2 = Uncertain 3 = Bad |
| **`Cfg_CfgErrQual`** | `BOOL` | - | Ẩn (Tag) | [Biến cấu hình thông số] PV quality to report for: Out-of-range, stuck (unchanging), PV not a number, I/O module fault, I/O channel fault, Inp_PVUncertain, function check, maintenance required, or AOI configuration error: 1 = Good 2 = Uncertain 3 = Bad |
| **`Cfg_StuckT`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Time with no change in Input to raise Stuck Status (s) |
| **`Cfg_PVReplaceVal`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Value (engineering units) to use to replace PV when action = replace. |
| **`PCmd_FailAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_FailSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Analog Input Failure Alarm |
| **`PCmd_FailUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Analog Input Failure Alarm |
| **`PCmd_FailUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |

## 3. Bảng chi tiết 45 Biến Đầu Ra (`Output Parameters`) của `P_AIChan`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Val_InpRaw`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Analog Input Value (RU) (before scaling or checking) |
| **`Val`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Process Variable Value (EU) |
| **`Val_PVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cực tiểu của dải đo đơn vị kỹ thuật = MIN(Cfg_PVEUMin, Cfg_PVEUMax) |
| **`Val_PVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cực đại của dải đo đơn vị kỹ thuật = MAX(Cfg_PVEUMin, Cfg_PVEUMax) |
| **`SrcQ_IO`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final channel status source and quality. GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`Val_Sts`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device confirmed status: 0 = PV Good 5 = PV Uncertain 6 = PV Bad 7 = Substitute PV 33 = Disabled |
| **`Val_Fault`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device fault status: 0 = none 32 = Fail 34 = Configuration error |
| **`Val_NotifyAll`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |
| **`Val_UnackAlmC`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Sts_PVGood`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=PV Quality is Good (not flagged as Bad or Uncertain) |
| **`Sts_PVUncertain`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=PV Quality is flagged as Uncertain |
| **`Sts_PVBad`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=PV Quality is flagged as Bad |
| **`Sts_UseInp`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Using Input to calculate PV (not replaced or held) |
| **`Sts_HoldLast`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Analog PV being held at last good value |
| **`Sts_Clamped`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Analog PV being clamped at Low or High Limit |
| **`Sts_Replaced`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Analog PV being replaced with configured value |
| **`Sts_InpOORHiCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Input PV exceeds Out-of-Range High Limit |
| **`Sts_InpOORLoCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Input PV exceeds Out-of-Range Low limit |
| **`Sts_InpOutOfRange`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Input is Out of Range (includes On-Delay, Deadband, Off-Delay) |
| **`Sts_InpStuck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Input is "stuck" (unchanging) |
| **`Sts_InpNaN`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Input is Not a Number (floating point exception) |
| **`Sts_ModFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Module Fault condition |
| **`Sts_ChanFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Channel Fault condition |
| **`Sts_OutOfSpec`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Working outside specifications (from instrument) |
| **`Sts_FuncCheck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Function Check (PV simulated/replaced at instrument) |
| **`Sts_MaintReqd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Maintenance is Required (from instrument) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang bị Tạm hoãn (Shelved) hoặc Vô hiệu hóa (Disabled), hiển thị biểu tượng trên HMI |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình (kiểm tra các bit Err_* chi tiết để biết nguyên nhân) |
| **`Err_Raw`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị dải thô Cfg_InpRawMin bằng Cfg_InpRawMax |
| **`Err_EU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị dải kỹ thuật Cfg_PVEUMin bằng Cfg_PVEUMax |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Timer Preset (must be 0 to |
| **`Err_DB`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: a Status Deadband is < 0.0 |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thông số cảnh báo (Thời gian duy trì tối thiểu, thời gian Shelve, mức Severity) không hợp lệ |
| **`Err_Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: PV High Clamping is less than PV Low Clamping |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc lỗi ngắt tải (Shed) đã hết điều kiện lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Analog Input Failure (Bad, Uncertain) |
| **`Alm_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Cảnh báo lỗi ngõ vào Analog (Fail Alarm) (Bad, Uncertain) |
| **`Ack_Fail`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Cảnh báo lỗi ngõ vào Analog (Fail Alarm) đã được Xác nhận (Acknowledged) |
| **`Sts_FailDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi ngõ vào Analog (Fail Alarm) đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_FailSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi ngõ vào Analog (Fail Alarm) đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_FailShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi ngõ vào Analog (Fail Alarm) đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`P_AIChan`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 15 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_AIChan`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_8` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'Analog Channel Quality'` | [Biến cấu hình thông số] Description for display on HMI. This string is shown in the title bar of the faceplate. |
| **`Cfg_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Engineering Units for display on HMI |
| **`Cfg_Label`** | `STRING_20` | `'Analog In Channel'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI |
| **`Cfg_RU`** | `STRING_8` | `'mA DC'` | [Biến cấu hình thông số] Raw Units for display on HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_AIChan'` | [Biến cấu hình thông số] Tagname for display on HMI |
| **`Fail`** | `P_Alarm` | `{...}` | Khối con quản lý Cảnh báo lỗi ngõ vào Analog (chất lượng xấu hoặc vượt dải) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_AIChan'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI và phần mềm thông tin |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset all Alarms requiring Reset |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset and Xác nhận (Acknowledge) all Alarms |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1=At least one Alarm requires Reset |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1=At least one Alarm requires Reset or Acknowledgement |
| **`Val_Notify`** | `SINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận của riêng khối này (enum) |
