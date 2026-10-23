# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_AIChan`
**Thuộc chương:** [Chương 1: Xử lý tín hiệu Vào/Ra (I/O Processing)](../01_CHUONG_1_IO_PROCESSING.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `IO Processing 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 26–29)  
**Tệp định nghĩa khối lệnh:** `P_AIChan_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_AIChan`:** **52** Biến đầu vào (`Input Parameters`) | **45** Biến đầu ra (`Output Parameters`) | **15** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_AIChan` trong Studio 5000
- **Chức năng cốt lõi:** Khối chẩn đoán chuyên sâu cấp độ kênh phần cứng Analog Input. Kiểm tra 9 tình huống bất thường của thẻ I/O và cảm biến (lỗi module, lỗi kênh, vượt dải, cảm biến bị đơ/treo không đổi giá trị *Stuck Input*, lỗi NaN, yêu cầu bảo trì) để phân loại chất lượng dữ liệu thành `Good`, `Uncertain` hoặc `Bad`.
- **Khi nào CẦN sử dụng:** Khi dùng thẻ Analog Input có hỗ trợ thông tin chẩn đoán mở rộng hoặc hệ thống yêu cầu **phát hiện cảm biến bị kẹt/đóng băng giá trị (`Stuck PV`)** trước khi cấp tín hiệu vào khối `P_AIn`, `P_AInAdv` hoặc `P_AInDual`.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Không dùng độc lập để hiển thị giá trị quá trình hay cảnh báo ngưỡng công nghệ HH/H/L/LL (luôn phải nối sau nó một khối `P_AIn*`).

### Quy trình 4 bước đấu nối và cấu hình `P_AIChan`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_Raw`, `Inp_ModFault`, `Inp_ChanFault`, `Inp_OutOfSpec`, `Inp_FuncCheck`, `Inp_MaintReqd`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_ClampSB`, `Cfg_FailOnUncertain`, `Cfg_PVDecPlcs`, `Cfg_HasFailAlm`, `Cfg_HasMoreObj`, `Cfg_FailResetReqd`, `Cfg_FailAckReqd`, `Cfg_FailSeverity`, `Cfg_InpRawMin`, `Cfg_InpRawMax`, `Cfg_PVEUMin`, `Cfg_PVEUMax`, `Cfg_PVHiLim`, `Cfg_PVLoLim`, `Cfg_InpOORHiLim`, `Cfg_InpOORLoLim`, `Cfg_InpOORDB`, `Cfg_InpOOROnDly`...
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_Desc`, `Cfg_EU`, `Cfg_Label`, `Cfg_RU`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_AIChan` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_AIChan` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PCmd_FailAck`, `PCmd_FailSuppress`, `PCmd_FailUnsuppress`, `PCmd_FailUnshelve`, `PCmd_Reset`
   - **Cách sử dụng trong code PLC:** Gán giá trị đặt vào `PSet_*` (nếu có) hoặc kích xung 1 chu kỳ vào các bit lệnh `PCmd_* = 1` để xóa lỗi (`PCmd_Reset`), xác nhận cảnh báo (`PCmd_*Ack`) hoặc ức chế cảnh báo (`PCmd_*Suppress`). Khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi.
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Val_InpRaw`, `Val`, `Val_PVEUMin`, `Val_PVEUMax`, `Val_Sts`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Sts_PVGood`, `Sts_PVUncertain`, `Sts_PVBad`, `Sts_UseInp`, `Sts_HoldLast`, `Sts_Clamped`, `Sts_Replaced`, `Sts_InpOORHiCmp`, `Sts_InpOORLoCmp`, `Sts_InpOutOfRange`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 52 Biến Đầu Vào (`Input Parameters`) của `P_AIChan`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp_Raw`** | `REAL` | `4` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu đầu vào thô chưa quy đổi (Raw Units - RU) đọc trực tiếp từ cảm biến / bộ truyền (Transmitter) |
| **`Inp_ModFault`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi module I/O hoặc mất kết nối truyền thông với module I/O (1 = Lỗi module, 0 = Tốt/OK) |
| **`Inp_ChanFault`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi phần cứng kênh I/O vật lý (1 = Lỗi kênh Channel Fault, 0 = Tốt/OK) |
| **`Inp_OutOfSpec`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Cờ báo từ thiết bị đo thông minh: Đang hoạt động ngoài đặc tính kỹ thuật (1 = Out of Specification / Uncertain, 0 = OK) |
| **`Inp_FuncCheck`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Cờ báo từ thiết bị đo thông minh: Đang ở chế độ kiểm tra chức năng / giả lập tại hiện trường (1 = Function Check, 0 = OK) |
| **`Inp_MaintReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Cờ báo từ thiết bị đo thông minh: Thiết bị yêu cầu bảo trì (1 = Maintenance Required, 0 = OK) |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Cfg_ClampSB`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Dải bắt dính (Snap-To Band, đơn vị EU) để kẹp chặt PV về đúng mức giới hạn khi tín hiệu tiến sát ngưỡng biên |
| **`Cfg_FailOnUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Kích hoạt cờ lỗi Sts_Fail và báo động ngay cả khi chất lượng tín hiệu là Không chắc chắn (Uncertain) hoặc Xấu (Bad); 0 = Chỉ báo lỗi khi chất lượng Xấu (Bad) |
| **`Cfg_PVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho giá trị PV trên màn hình HMI (từ 0 đến 6 chữ số) |
| **`Cfg_HasFailAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_FailResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_FailAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_FailSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_InpRawMin`** | `REAL` | `4` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đầu vào thô tối thiểu (Raw Min, ví dụ 4.0 mA hoặc 0) dùng để quy đổi tỷ lệ tuyến tính sang đơn vị kỹ thuật |
| **`Cfg_InpRawMax`** | `REAL` | `20` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đầu vào thô tối đa (Raw Max, ví dụ 20.0 mA hoặc 100) dùng để quy đổi tỷ lệ tuyến tính sang đơn vị kỹ thuật |
| **`Cfg_PVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tương ứng với Raw Min (PV EU Min — hỗ trợ cả quy đổi ngược nếu đặt EUMin > EUMax) |
| **`Cfg_PVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tương ứng với Raw Max (PV EU Max — hỗ trợ cả quy đổi ngược nếu đặt EUMax < EUMin) |
| **`Cfg_PVHiLim`** | `REAL` | `1.5000000000000001E+38` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng kẹp giới hạn trên của giá trị PV (High Clamping Threshold, đơn vị kỹ thuật EU) |
| **`Cfg_PVLoLim`** | `REAL` | `-1.5000000000000001E+38` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng kẹp giới hạn dưới của giá trị PV (Low Clamping Threshold, đơn vị kỹ thuật EU) |
| **`Cfg_InpOORHiLim`** | `REAL` | `20.733333999999999` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng giới hạn trên báo lỗi tín hiệu thô vượt dải (Out-of-Range High Limit, đơn vị thô Raw) |
| **`Cfg_InpOORLoLim`** | `REAL` | `3.6666666999999999` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng giới hạn dưới báo lỗi tín hiệu thô vượt dải / đứt dây (Out-of-Range Low Limit, đơn vị thô Raw) |
| **`Cfg_InpOORDB`** | `REAL` | `6.6666669999999997E-2` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband, đơn vị thô Raw) cho ngưỡng báo lỗi vượt dải trên/dưới (Out-of-Range) |
| **`Cfg_InpOOROnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tối thiểu tín hiệu nằm ngoài dải trước khi kích hoạt cờ lỗi Out-of-Range (giây) |
| **`Cfg_InpOOROffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tối thiểu tín hiệu trở lại trong dải hợp lệ trước khi xóa cờ lỗi Out-of-Range (giây) |
| **`Cfg_InpOORAction`** | `BOOL` | `-` | Ẩn (Tag) | [Biến cấu hình thông số] Hành động xử lý ngõ ra PV khi tín hiệu đầu vào vượt dải (Out-of-Range): 1 = Giữ nguyên tín hiệu đi qua, 2 = Giữ giá trị tốt cuối cùng (Hold Last Good), 3 = Thay bằng giá trị Cfg_PVReplaceVal |
| **`Cfg_InpStuckAction`** | `BOOL` | `-` | Ẩn (Tag) | [Biến cấu hình thông số] Hành động xử lý ngõ ra PV khi tín hiệu cảm biến bị treo/đóng băng (Stuck Input): 1 = Cho đi qua, 2 = Giữ giá trị tốt cuối cùng, 3 = Thay bằng Cfg_PVReplaceVal |
| **`Cfg_InpNaNAction`** | `BOOL` | `-` | Ẩn (Tag) | [Biến cấu hình thông số] Hành động xử lý ngõ ra PV khi giá trị đầu vào bị lỗi không phải là số (NaN): 1 = Cho đi qua, 2 = Giữ giá trị tốt cuối cùng, 3 = Thay bằng Cfg_PVReplaceVal |
| **`Cfg_ModFaultAction`** | `BOOL` | `-` | Ẩn (Tag) | [Biến cấu hình thông số] Hành động xử lý ngõ ra PV khi lỗi module I/O (I/O Module Fault): 1 = Cho đi qua, 2 = Giữ giá trị tốt cuối cùng, 3 = Thay bằng Cfg_PVReplaceVal |
| **`Cfg_ChanFaultAction`** | `BOOL` | `-` | Ẩn (Tag) | [Biến cấu hình thông số] Hành động xử lý ngõ ra PV khi lỗi kênh vật lý (I/O Channel Fault): 1 = Cho đi qua, 2 = Giữ giá trị tốt cuối cùng, 3 = Thay bằng Cfg_PVReplaceVal |
| **`Cfg_OutOfSpecAction`** | `BOOL` | `-` | Ẩn (Tag) | [Biến cấu hình thông số] Hành động xử lý ngõ ra PV khi thiết bị đo báo Ngoài đặc tính (Out of Spec): 1 = Cho đi qua, 2 = Giữ giá trị tốt cuối cùng, 3 = Thay bằng Cfg_PVReplaceVal |
| **`Cfg_FuncCheckAction`** | `BOOL` | `-` | Ẩn (Tag) | [Biến cấu hình thông số] Hành động xử lý ngõ ra PV khi thiết bị đo báo Kiểm tra chức năng (Function Check): 1 = Cho đi qua, 2 = Giữ giá trị tốt cuối cùng, 3 = Thay bằng Cfg_PVReplaceVal |
| **`Cfg_MaintReqdAction`** | `BOOL` | `-` | Ẩn (Tag) | [Biến cấu hình thông số] Hành động xử lý ngõ ra PV khi thiết bị đo báo Yêu cầu bảo trì (Maintenance Required): 1 = Cho đi qua, 2 = Giữ giá trị tốt cuối cùng, 3 = Thay bằng Cfg_PVReplaceVal |
| **`Cfg_CfgErrAction`** | `BOOL` | `-` | Ẩn (Tag) | [Biến cấu hình thông số] Hành động xử lý ngõ ra PV khi khối AOI bị lỗi cài đặt cấu hình (Config Error): 1 = Cho đi qua, 2 = Giữ giá trị tốt cuối cùng, 3 = Thay bằng Cfg_PVReplaceVal |
| **`Cfg_InpOORQual`** | `BOOL` | `-` | Ẩn (Tag) | [Biến cấu hình thông số] Mức chất lượng PV báo cáo khi tín hiệu đầu vào vượt dải (Out-of-Range): 1 = Tốt (Good), 2 = Không chắc chắn (Uncertain), 3 = Xấu (Bad) |
| **`Cfg_InpStuckQual`** | `BOOL` | `-` | Ẩn (Tag) | [Biến cấu hình thông số] Mức chất lượng PV báo cáo khi tín hiệu cảm biến bị treo (Stuck Input): 1 = Tốt (Good), 2 = Không chắc chắn (Uncertain), 3 = Xấu (Bad) |
| **`Cfg_InpNaNQual`** | `BOOL` | `-` | Ẩn (Tag) | [Biến cấu hình thông số] Mức chất lượng PV báo cáo khi giá trị đầu vào không phải là số (NaN): 1 = Tốt (Good), 2 = Không chắc chắn (Uncertain), 3 = Xấu (Bad) |
| **`Cfg_ModFaultQual`** | `BOOL` | `-` | Ẩn (Tag) | [Biến cấu hình thông số] Mức chất lượng PV báo cáo khi lỗi module I/O (Module Fault): 1 = Tốt (Good), 2 = Không chắc chắn (Uncertain), 3 = Xấu (Bad) |
| **`Cfg_ChanFaultQual`** | `BOOL` | `-` | Ẩn (Tag) | [Biến cấu hình thông số] Mức chất lượng PV báo cáo khi lỗi kênh vật lý (Channel Fault): 1 = Tốt (Good), 2 = Không chắc chắn (Uncertain), 3 = Xấu (Bad) |
| **`Cfg_OutOfSpecQual`** | `BOOL` | `-` | Ẩn (Tag) | [Biến cấu hình thông số] Mức chất lượng PV báo cáo khi thiết bị đo báo Ngoài đặc tính (Out of Spec): 1 = Tốt (Good), 2 = Không chắc chắn (Uncertain), 3 = Xấu (Bad) |
| **`Cfg_FuncCheckQual`** | `BOOL` | `-` | Ẩn (Tag) | [Biến cấu hình thông số] Mức chất lượng PV báo cáo khi thiết bị đo báo Kiểm tra chức năng (Function Check): 1 = Tốt (Good), 2 = Không chắc chắn (Uncertain), 3 = Xấu (Bad) |
| **`Cfg_MaintReqdQual`** | `BOOL` | `-` | Ẩn (Tag) | [Biến cấu hình thông số] Mức chất lượng PV báo cáo khi thiết bị đo báo Yêu cầu bảo trì (Maintenance Required): 1 = Tốt (Good), 2 = Không chắc chắn (Uncertain), 3 = Xấu (Bad) |
| **`Cfg_CfgErrQual`** | `BOOL` | `-` | Ẩn (Tag) | [Biến cấu hình thông số] Mức chất lượng PV báo cáo khi khối AOI bị lỗi cấu hình (Config Error): 1 = Tốt (Good), 2 = Không chắc chắn (Uncertain), 3 = Xấu (Bad) |
| **`Cfg_StuckT`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian tối đa tín hiệu đầu vào đứng im không thay đổi giá trị trước khi báo lỗi treo cảm biến (Stuck Input, tính bằng giây) |
| **`Cfg_PVReplaceVal`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị PV dự phòng (đơn vị kỹ thuật EU) dùng để thay thế cho ngõ ra khi hành động xử lý lỗi kênh được chọn là Thay thế (= 3) |
| **`PCmd_FailAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_FailSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |

## 3. Bảng chi tiết 45 Biến Đầu Ra (`Output Parameters`) của `P_AIChan`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Val_InpRaw`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu vào thô (Raw Units - RU) trước khi quy đổi tỷ lệ hoặc kiểm tra giới hạn |
| **`Val`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu ra chính của biến quá trình PV (đã qua quy đổi đơn vị kỹ thuật EU, lọc nhiễu và chọn giá trị thay thế Substitute PV nếu có) |
| **`Val_PVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận dưới của dải đo đơn vị kỹ thuật PV = MIN(Cfg_PVEUMin, Cfg_PVEUMax) |
| **`Val_PVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận trên của dải đo đơn vị kỹ thuật PV = MAX(Cfg_PVEUMin, Cfg_PVEUMax) |
| **`SrcQ_IO`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng dữ liệu tổng hợp cuối cùng (0 = Tín hiệu thực Tốt đã xác nhận, 1 = Tín hiệu thực Tốt giả định, 2 = Không cấu hình phản hồi/Tốt giả định, 8 = Đang mô phỏng, 9 = Mô phỏng vòng lặp, 10 = Giá trị nhập tay, 16 = Ngoài đặc tính kỹ thuật, 17 = Thay thế tại thiết bị/bus, 18 = Thay thế bởi Bảo trì, 19 = Ngắt bảo vệ giữ giá trị cũ, 20 = Ngắt bảo vệ dùng giá trị thay thế, 32 = Lỗi tín hiệu vượt dải/NaN, 33 = Lỗi kênh I/O, 34 = Lỗi module I/O, 35 = Lỗi cấu hình) |
| **`Val_Sts`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái hoạt động xác nhận của thiết bị (Device Confirmed Status enum dùng để điều khiển màu sắc và biểu tượng động trên HMI) |
| **`Val_Fault`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái lỗi thiết bị (Device Fault Status — kiểu liệt kê enum: 0 = Không lỗi, 16 = Lỗi khởi động/mở, 17 = Lỗi dừng/đóng, 20 = Lo, 21 = Hi, 24 = LoLo, 25 = HiHi, 32 = Fail/I/O Fault, 34 = Lỗi cấu hình CfgErr) |
| **`Val_NotifyAll`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận tổng hợp của khối này cộng với các đối tượng kênh/quá tải liên kết |
| **`Val_UnackAlmC`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms Count) |
| **`Sts_PVGood`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Chất lượng tín hiệu PV Tốt (Good — không bị gắn cờ Bad hay Uncertain) |
| **`Sts_PVUncertain`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Chất lượng tín hiệu PV Không chắc chắn (Uncertain Quality) |
| **`Sts_PVBad`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Chất lượng tín hiệu PV Xấu (Bad Quality) hoặc tín hiệu vượt ngoài dải lỗi (Out of Range) |
| **`Sts_UseInp`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang sử dụng tín hiệu thực từ cảm biến để tính PV (không bị giữ giá trị cũ Hold hoặc thay thế Replace) |
| **`Sts_HoldLast`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị PV đang được giữ ở giá trị tốt cuối cùng (Hold Last Good Value) do phát hiện lỗi kênh đo |
| **`Sts_Clamped`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị PV tương tự đang bị kẹp ở ngưỡng giới hạn dưới (Cfg_PVLoLim) hoặc giới hạn trên (Cfg_PVHiLim) |
| **`Sts_Replaced`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị PV đang được thay thế bằng giá trị cấu hình định trước (Cfg_PVReplaceVal) do phát hiện lỗi kênh đo |
| **`Sts_InpOORHiCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị đầu vào thô hiện đang vượt quá ngưỡng lỗi vượt dải trên (Cfg_InpOORHiLim) |
| **`Sts_InpOORLoCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị đầu vào thô hiện đang thấp hơn ngưỡng lỗi vượt dải dưới (Cfg_InpOORLoLim) |
| **`Sts_InpOutOfRange`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào vượt ngoài dải đo cho phép (Out of Range — đã tính trễ On-Delay, Deadband, Off-Delay) |
| **`Sts_InpStuck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào bị treo/đóng băng không thay đổi giá trị (Stuck Input) quá thời gian Cfg_StuckT |
| **`Sts_InpNaN`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị đầu vào không phải là số hợp lệ (Not-a-Number / lỗi số thực dấu phẩy động) |
| **`Sts_ModFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có lỗi phần cứng hoặc mất kết nối với module I/O (I/O Module Fault) |
| **`Sts_ChanFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có lỗi kênh vật lý trên thẻ I/O (I/O Channel Fault) |
| **`Sts_OutOfSpec`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị đo hiện trường báo đang làm việc ngoài đặc tính kỹ thuật (Out of Specification) |
| **`Sts_FuncCheck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị đo hiện trường báo đang ở chế độ kiểm tra chức năng / giả lập tại chỗ (Function Check) |
| **`Sts_MaintReqd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị đo hiện trường báo yêu cầu bảo trì (Maintenance Required) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có ít nhất một cảnh báo bị Tạm hoãn (Shelved), Vô hiệu hóa (Disabled) hoặc Ức chế (Suppressed) — hiển thị biểu tượng chữ "I" trên HMI |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_Raw`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải giá trị thô đầu vào (Cfg_InpRawMin = Cfg_InpRawMax) bị trùng nhau |
| **`Err_EU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải quy đổi đơn vị kỹ thuật (Engineering Units) có giá trị Tối thiểu bằng Tối đa (Min = Max) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị đặt bộ định thời gian (Timer Preset) nằm ngoài phạm vi hợp lệ 0..2.147.483 giây |
| **`Err_DB`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Độ rộng vùng chết (Deadband) của một ngưỡng cảnh báo bị đặt số âm (< 0.0) |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình cảnh báo: Thời gian duy trì tối thiểu (Min On Time), thời gian tạm hoãn (Shelf Time) hoặc mức độ nghiêm trọng (Severity 1..1000) không hợp lệ |
| **`Err_Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Ngưỡng kẹp giới hạn trên của PV nhỏ hơn ngưỡng kẹp giới hạn dưới (Cfg_PVHiLim < Cfg_PVLoLim) |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc chốt ngắt bảo vệ (Shed) đã hết lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi tín hiệu / hỏng kênh đo (Signal Failure) |
| **`Alm_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi tín hiệu / hỏng kênh đo (Signal Failure) |
| **`Ack_Fail`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi tín hiệu / hỏng kênh đo (Signal Failure) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_FailDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_FailSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_FailShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`P_AIChan`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_AIChan) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 15 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_AIChan`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_8` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'Analog Channel Quality'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật (Engineering Units, ví dụ: "°C", "bar", "m3/h", "%") hiển thị trên màn hình HMI |
| **`Cfg_Label`** | `STRING_20` | `'Analog In Channel'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_RU`** | `STRING_8` | `'mA DC'` | [Biến cấu hình thông số] Chuỗi đơn vị thô (Raw Units, ví dụ: "mA", "V", "Counts") hiển thị trên màn hình HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_AIChan'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`Fail`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi tín hiệu / hỏng kênh đo (Signal Failure) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_AIChan'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`Val_Notify`** | `SINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo hiện hành và trạng thái xác nhận của khối này (0 = Không có cảnh báo, 1 = Đã hết lỗi nhưng chờ Reset/Ack, 2..3 = Thấp, 4..5 = Trung bình, 6..7 = Cao, 8..9 = Khẩn cấp) |
