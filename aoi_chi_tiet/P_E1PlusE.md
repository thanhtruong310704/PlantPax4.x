# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_E1PlusE`
**Thuộc chương:** [Chương 3: Động cơ, Biến tần & Rơ-le Quá tải (Motors & Drives)](../03_CHUONG_3_MOTORS_VA_BIEN_TAN.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Motors 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 252–256)  
**Tệp định nghĩa khối lệnh:** `P_E1PlusE_4.10.00_RUNG.L5X`  
**Thống kê số lượng biến của `P_E1PlusE`:** **38** Biến đầu vào (`Input Parameters`) | **49** Biến đầu ra (`Output Parameters`) | **20** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_E1PlusE` trong Studio 5000
- **Chức năng cốt lõi:** Khối giám sát **Rơ-le nhiệt điện tử E1 Plus** có gắn module truyền thông EtherNet/IP `193-ETN`. Đọc % dòng tải (% FLA), trạng thái Trip/Warning và gửi lệnh xóa lỗi từ xa (`Remote Reset`).
- **Khi nào CẦN sử dụng:** Khi tủ MCC dùng rơ-le quá tải **E1 Plus kèm module `193-ETN`** đi cùng với khối động cơ `P_Motor` / `P_MotorRev` / `P_Motor2Spd`.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi dùng rơ-le quá tải thế hệ mới E300 ([`P_E300Ovld.md`](./P_E300Ovld.md)).

### Quy trình 4 bước đấu nối và cấu hình `P_E1PlusE`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp`, `Inp_TripReset`, `Inp_IOFault`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_AllowOperReset`, `Cfg_AllowMaintReset`, `Cfg_HasWarnAlm`, `Cfg_HasTripAlm`, `Cfg_HasMoreObj`, `Cfg_HasIOFaultAlm`, `Cfg_WarnResetReqd`, `Cfg_TripResetReqd`, `Cfg_IOFaultResetReqd`, `Cfg_WarnAckReqd`, `Cfg_TripAckReqd`, `Cfg_IOFaultAckReqd`, `Cfg_WarnSeverity`, `Cfg_TripSeverity`, `Cfg_IOFaultSeverity`, `Cfg_PulseT`, `Cfg_LocalResetReqdT`, `Cfg_LocalResetReqdNum`
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_Desc`, `Cfg_Inp1Txt`, `Cfg_Inp2Txt`, `Cfg_Label`, `Cfg_OutATxt`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_E1PlusE` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_E1PlusE` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PCmd_TripReset`, `PCmd_Reset`, `PCmd_WarnAck`, `PCmd_WarnSuppress`, `PCmd_WarnUnsuppress`, `PCmd_WarnUnshelve`, `PCmd_TripAck`, `PCmd_TripSuppress`, `PCmd_TripUnsuppress`, `PCmd_TripUnshelve`, `PCmd_IOFaultAck`, `PCmd_IOFaultSuppress`, `PCmd_IOFaultUnsuppress`, `PCmd_IOFaultUnshelve`
   - **Cách sử dụng trong code PLC:** Gán giá trị đặt vào `PSet_*` (nếu có) hoặc kích xung 1 chu kỳ vào các bit lệnh `PCmd_* = 1` để xóa lỗi (`PCmd_Reset`), xác nhận cảnh báo (`PCmd_*Ack`) hoặc ức chế cảnh báo (`PCmd_*Suppress`). Khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi.
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Out_TripReset`, `Val_AvgPctFLA`, `Val_PctTherm`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Sts_WarnBits`, `Sts_TripBits`, `Sts_Inp1`, `Sts_Inp2`, `Sts_OutA`, `Sts_MotorCurr`, `Sts_LocalResetReqd`, `Sts_TripLog0`, `Sts_TripLog1`, `Sts_TripLog2`, `Sts_TripLog3`, `Sts_TripLog4`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 38 Biến Đầu Vào (`Input Parameters`) của `P_E1PlusE`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp`** | `P_E1PlusE_Inp` | `-` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu điều kiện đầu vào (1 = Kích hoạt điều kiện / Báo động, 0 = Bình thường) |
| **`Inp_TripReset`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu yêu cầu gửi lệnh xóa lỗi quá tải (Trip Reset) tới rơ-le quá tải (1 = Gửi lệnh Reset) |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi truyền thông I/O của thiết bị (0 = Tốt/OK, 1 = Lỗi truyền thông I/O Fault) |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Cfg_AllowOperReset`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép Người vận hành (Operator) nhấn nút xóa lỗi quá tải (Trip Reset) từ màn hình HMI |
| **`Cfg_AllowMaintReset`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép Nhân viên bảo trì (Maintenance) nhấn nút xóa lỗi quá tải (Trip Reset) từ màn hình HMI |
| **`Cfg_HasWarnAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_WarnResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_TripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_WarnAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_TripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_WarnSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_TripSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_PulseT`** | `REAL` | `5` | Ẩn (Tag) | [Biến cấu hình thông số] Độ rộng thời gian phát xung Reset hoặc Remote Trip gửi xuống rơ-le quá tải (giây) |
| **`Cfg_LocalResetReqdT`** | `REAL` | `3600` | Ẩn (Tag) | [Biến cấu hình thông số] Khung thời gian giám sát số lần Reset quá tải từ xa liên tiếp (giây) |
| **`Cfg_LocalResetReqdNum`** | `DINT` | `3` | Ẩn (Tag) | [Biến cấu hình thông số] Số lần nhấn Reset quá tải từ xa tối đa cho phép trong khoảng thời gian Cfg_LocalResetReqdT trước khi khóa bắt buộc phải xuống Reset tại chỗ ở tủ MCC |
| **`PCmd_TripReset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gửi xung xóa lỗi quá tải (Trip Reset) tới rơ-le nhiệt/biến tần |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`PCmd_WarnAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_WarnSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_WarnUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_WarnUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_TripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_TripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_TripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_TripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) ngay lập tức (biến xung tự động xóa về 0) |
| **`Sts_TripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |

## 3. Bảng chi tiết 49 Biến Đầu Ra (`Output Parameters`) của `P_E1PlusE`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Inp`** | `P_E1PlusE_Inp` | `-` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu điều kiện đầu vào (1 = Kích hoạt điều kiện / Báo động, 0 = Bình thường) |
| **`Out_TripReset`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra phát xung xóa lỗi quá tải cho rơ-le nhiệt (1 = Reset Overload Trip) |
| **`Val_AvgPctFLA`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Dòng điện trung bình 3 pha tính theo phần trăm dòng định mức (% Full Load Amps) |
| **`Val_PctTherm`** | `INT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Phần trăm dung lượng nhiệt đã sử dụng của động cơ (% Thermal Usage — đạt 100% sẽ cắt bảo vệ quá tải) |
| **`SrcQ_IO`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng dữ liệu tổng hợp cuối cùng (0 = Tín hiệu thực Tốt đã xác nhận, 1 = Tín hiệu thực Tốt giả định, 2 = Không cấu hình phản hồi/Tốt giả định, 8 = Đang mô phỏng, 9 = Mô phỏng vòng lặp, 10 = Giá trị nhập tay, 16 = Ngoài đặc tính kỹ thuật, 17 = Thay thế tại thiết bị/bus, 18 = Thay thế bởi Bảo trì, 19 = Ngắt bảo vệ giữ giá trị cũ, 20 = Ngắt bảo vệ dùng giá trị thay thế, 32 = Lỗi tín hiệu vượt dải/NaN, 33 = Lỗi kênh I/O, 34 = Lỗi module I/O, 35 = Lỗi cấu hình) |
| **`Val_Fault`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái lỗi thiết bị (Device Fault Status — kiểu liệt kê enum: 0 = Không lỗi, 16 = Lỗi khởi động/mở, 17 = Lỗi dừng/đóng, 20 = Lo, 21 = Hi, 24 = LoLo, 25 = HiHi, 32 = Fail/I/O Fault, 34 = Lỗi cấu hình CfgErr) |
| **`Val_NotifyAll`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận tổng hợp của khối này cộng với các đối tượng kênh/quá tải liên kết |
| **`Val_UnackAlmC`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms Count) |
| **`Sts_WarnBits`** | `INT` | `2#0000_0000_0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit chi tiết báo nguyên nhân gây cảnh báo sớm (Warning Reason Bits) đọc từ rơ-le quá tải |
| **`Sts_TripBits`** | `INT` | `2#0000_0000_0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit chi tiết báo nguyên nhân gây cắt mạch (Trip Reason Bits) đọc từ rơ-le quá tải |
| **`Sts_Inp1`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái ngõ vào rời rạc số #1 trên rơ-le quá tải |
| **`Sts_Inp2`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái ngõ vào rời rạc số #2 trên rơ-le quá tải |
| **`Sts_OutA`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái tiếp điểm ngõ ra rời rạc A của rơ-le quá tải |
| **`Sts_MotorCurr`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang phát hiện có dòng điện chạy qua động cơ (Motor Current Present) |
| **`Sts_LocalResetReqd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đã vượt quá số lần Reset từ xa cho phép: bắt buộc kỹ thuật viên phải xuống tận tủ MCC kiểm tra và nhấn nút Reset tại chỗ |
| **`Sts_TripLog0`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Mã nguyên nhân cắt bảo vệ trong nhật ký lỗi thứ 0 (Trip Log 0 — 0 là lần cắt gần nhất) |
| **`Sts_TripLog1`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Mã nguyên nhân cắt bảo vệ trong nhật ký lỗi thứ 1 (Trip Log 1 — 0 là lần cắt gần nhất) |
| **`Sts_TripLog2`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Mã nguyên nhân cắt bảo vệ trong nhật ký lỗi thứ 2 (Trip Log 2 — 0 là lần cắt gần nhất) |
| **`Sts_TripLog3`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Mã nguyên nhân cắt bảo vệ trong nhật ký lỗi thứ 3 (Trip Log 3 — 0 là lần cắt gần nhất) |
| **`Sts_TripLog4`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Mã nguyên nhân cắt bảo vệ trong nhật ký lỗi thứ 4 (Trip Log 4 — 0 là lần cắt gần nhất) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị chưa sẵn sàng hoạt động (kiểm tra các bit Nrdy_* chi tiết để biết nguyên nhân cụ thể) |
| **`Nrdy_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng: Thiết bị đang bị cắt bảo vệ quá tải (Tripped tại thiết bị hoặc do lệnh Trip) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi truyền thông thẻ I/O (yêu cầu nhấn Reset) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có ít nhất một cảnh báo bị Tạm hoãn (Shelved), Vô hiệu hóa (Disabled) hoặc Ức chế (Suppressed) — hiển thị biểu tượng chữ "I" trên HMI |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị đặt bộ định thời gian (Timer Preset) nằm ngoài phạm vi hợp lệ 0..2.147.483 giây |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình cảnh báo: Thời gian duy trì tối thiểu (Min On Time), thời gian tạm hoãn (Shelf Time) hoặc mức độ nghiêm trọng (Severity 1..1000) không hợp lệ |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc chốt ngắt bảo vệ (Shed) đã hết lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_Warn`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) |
| **`Alm_Warn`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) |
| **`Ack_Warn`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_WarnDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_WarnShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_WarnSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_Trip`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện rơ-le quá tải tác động cắt mạch (Overload Trip) |
| **`Alm_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho rơ-le quá tải tác động cắt mạch (Overload Trip) |
| **`Ack_Trip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động rơ-le quá tải tác động cắt mạch (Overload Trip) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_TripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_TripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_IOFault`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi truyền thông thẻ I/O (I/O Fault) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi truyền thông thẻ I/O (I/O Fault) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`P_E1PlusE`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_E1PlusE) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 20 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_E1PlusE`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'Motor Overload Relay'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_Inp1Txt`** | `STRING_20` | `'Run Feedback'` | [Biến cấu hình thông số] Nhãn mô tả cho ngõ vào rời rạc số #1 hiển thị trên HMI |
| **`Cfg_Inp2Txt`** | `STRING_20` | `''` | [Biến cấu hình thông số] Nhãn mô tả cho ngõ vào rời rạc số #2 hiển thị trên HMI |
| **`Cfg_Label`** | `STRING_20` | `'Overload Relay'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_OutATxt`** | `STRING_20` | `'Starter Energized'` | [Biến cấu hình thông số] Nhãn mô tả cho ngõ ra Out_A hiển thị trên HMI Faceplate |
| **`Cfg_Tag`** | `STRING_20` | `'P_E1PlusE'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_E1PlusE'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`IOFault`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo |
| **`OCmd_TripReset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Gửi xung xóa lỗi quá tải (Trip Reset) tới rơ-le nhiệt/biến tần |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_TripReset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_TripReset (Gửi xung xóa lỗi quá tải (Trip Reset) tới rơ-le nhiệt/biến tần), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`Trip`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho rơ-le quá tải tác động cắt mạch (Overload Trip) |
| **`Val_Notify`** | `SINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo hiện hành và trạng thái xác nhận của khối này (0 = Không có cảnh báo, 1 = Đã hết lỗi nhưng chờ Reset/Ack, 2..3 = Thấp, 4..5 = Trung bình, 6..7 = Cao, 8..9 = Khẩn cấp) |
| **`Warn`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) |
