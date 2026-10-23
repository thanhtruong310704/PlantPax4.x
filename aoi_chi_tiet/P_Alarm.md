# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_Alarm`
**Thuộc chương:** [Chương 5: Khối chức năng dùng chung, Liên động & Cảnh báo (Cross Functional)](../05_CHUONG_5_CROSS_FUNCTIONAL.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Cross Functional 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 371–377)  
**Tệp định nghĩa khối lệnh:** `P_Alarm_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_Alarm`:** **16** Biến đầu vào (`Input Parameters`) | **16** Biến đầu ra (`Output Parameters`) | **20** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_Alarm` trong Studio 5000
- **Chức năng cốt lõi:** Khối **Quản lý Cảnh báo Chuẩn (`Common Alarm Block`)** tuân thủ tiêu chuẩn ISA-18.2. Quản lý trạng thái Ack, Reset, Shelve, Suppress, Disable và mức độ nghiêm trọng (`Severity`) để hiển thị đồng bộ trên hệ thống cảnh báo HMI.
- **Khi nào CẦN sử dụng:** Khối này đã được nhúng sẵn trong hầu hết các khối PlantPAx. Bạn lấy khối `P_Alarm` rời ra dùng khi tự viết một **thuật toán hoặc điều kiện cảnh báo tùy chỉnh riêng** trong PLC và muốn cảnh báo đó hiển thị trên thanh Alarm Banner HMI chuẩn của PlantPAx.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi cảnh báo đã có sẵn trong `P_AIn`, `P_DIn`, `P_Motor`, `P_Valve*`.

### Quy trình 4 bước đấu nối và cấu hình `P_Alarm`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_Exists`, `Cfg_ResetReqd`, `Cfg_AckReqd`, `Cfg_AllowShelve`, `Cfg_AllowDisable`, `Cfg_Severity`, `Cfg_MaxShelfT`, `Cfg_AlmMinOnT`
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Cond`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_Alarm` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_Alarm` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PCmd_Reset`, `PCmd_Ack`, `PCmd_Suppress`, `PCmd_Unshelve`
   - **Cách sử dụng trong code PLC:** Gán giá trị đặt vào `PSet_*` (nếu có) hoặc kích xung 1 chu kỳ vào các bit lệnh `PCmd_* = 1` để xóa lỗi (`PCmd_Reset`), xác nhận cảnh báo (`PCmd_*Ack`) hoặc ức chế cảnh báo (`PCmd_*Suppress`). Khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi.
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Val_Notify`, `Val_SecToUnshelve`, `Val_MinToUnshelve`, `Sts_AlmInh`, `Sts_RdyReset`, `Sts_RdyAck`, `Sts_Err`
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 16 Biến Đầu Vào (`Input Parameters`) của `P_Alarm`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu điều kiện đầu vào (1 = Kích hoạt điều kiện / Báo động, 0 = Bình thường) |
| **`Inp_Reset`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Cfg_Exists`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cảnh báo được cấu hình tồn tại và hoạt động; 0 = Cảnh báo bị tắt không quét kiểm tra |
| **`Cfg_ResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự xóa khi về bình thường |
| **`Cfg_AckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Bắt buộc phải nhấn Xác nhận (Acknowledge) khi cảnh báo xuất hiện; 0 = Cảnh báo tự động được xác nhận |
| **`Cfg_AllowShelve`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép người vận hành Tạm hoãn (Shelve) cảnh báo này trên HMI; 0 = Cấm Shelve |
| **`Cfg_AllowDisable`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép nhân viên bảo trì Vô hiệu hóa (Disable) cảnh báo này trên HMI; 0 = Cấm Disable |
| **`Cfg_Severity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_MaxShelfT`** | `DINT` | `480` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian cho phép Tạm hoãn (Shelve) cảnh báo tối đa trước khi tự động gỡ bỏ tạm hoãn (phút) |
| **`Cfg_AlmMinOnT`** | `DINT` | `5` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian duy trì mức BẬT tối thiểu của cảnh báo sau khi kích hoạt để đảm bảo HMI/Historian kịp ghi nhận (giây) |
| **`PCfg_AllowExist`** | `BOOL` | `1` | Ẩn (Tag) | 1 = Cấu hình cho phép cảnh báo này tồn tại trên HMI; 0 = Các thông số cấu hình khác làm cảnh báo này không cần thiết và bị ẩn đi |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`PCmd_Ack`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo hiện tại |
| **`PCmd_Suppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo từ chương trình |
| **`PCmd_Unshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo ngay lập tức |

## 3. Bảng chi tiết 16 Biến Đầu Ra (`Output Parameters`) của `P_Alarm`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Val_Notify`** | `SINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo hiện hành và trạng thái xác nhận của khối này (0 = Không có cảnh báo, 1 = Đã hết lỗi nhưng chờ Reset/Ack, 2..3 = Thấp, 4..5 = Trung bình, 6..7 = Cao, 8..9 = Khẩn cấp) |
| **`Val_SecToUnshelve`** | `SINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Số giây lẻ còn lại cho đến khi cảnh báo tự động gỡ bỏ trạng thái tạm hoãn (Auto-Unshelve, định dạng mmm:ss) |
| **`Val_MinToUnshelve`** | `INT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Số phút còn lại cho đến khi cảnh báo tự động gỡ bỏ trạng thái tạm hoãn (Auto-Unshelve, định dạng mmm:ss) |
| **`Alm`** | `BOOL` | `0` | Ẩn (Tag) | Cờ báo trạng thái báo động hiện hành: 1 = Đang có báo động (In Alarm); 0 = Bình thường |
| **`Ack`** | `BOOL` | `1` | Ẩn (Tag) | Cờ báo trạng thái xác nhận báo động: 1 = Báo động đã được Xác nhận (Acknowledged); 0 = Báo động mới chưa được xác nhận |
| **`Shelved`** | `BOOL` | `0` | Ẩn (Tag) | 1 = Cảnh báo đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Suppressed`** | `BOOL` | `0` | Ẩn (Tag) | 1 = Cảnh báo đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Disabled`** | `BOOL` | `0` | Ẩn (Tag) | 1 = Cảnh báo đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_AlmInh`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang có ít nhất một cảnh báo bị Tạm hoãn (Shelved), Vô hiệu hóa (Disabled) hoặc Ức chế (Suppressed) — hiển thị biểu tượng chữ "I" trên HMI |
| **`Sts_RdyReset`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc chốt ngắt bảo vệ (Shed) đã hết lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị đặt bộ định thời gian (Timer Preset) nằm ngoài phạm vi hợp lệ 0..2.147.483 giây |
| **`Err_Severity`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Mức độ nghiêm trọng của cảnh báo (Cfg_Severity) nằm ngoài phạm vi 1..1000 |
| **`P_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_Alarm) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 20 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_Alarm`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Cond`** | `STRING_20` | `'Alarm Condition'` | [Biến cấu hình thông số] Chuỗi văn bản mô tả nội dung điều kiện cảnh báo hiển thị trên HMI (ví dụ: "High-High PV") |
| **`Cfg_Tag`** | `STRING_20` | `'P_Alarm'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`Com_AE`** | `INT` | `2#0000_0000_0000_0010` | Từ giao tiếp nội bộ với máy chủ Cảnh báo và Sự kiện (FactoryTalk Alarms and Events Server) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_Alarm'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`MCmd_Disable`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Vô hiệu hóa (Disable) cảnh báo không cho phát sinh báo động mới |
| **`MCmd_Enable`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Kích hoạt (Enable) cho phép cảnh báo hoạt động trở lại |
| **`MCmd_Test`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Kích thử nghiệm (Test) cảnh báo trong thời gian duy trì tối thiểu |
| **`MRdy_Disable`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Disable (Vô hiệu hóa (Disable) cảnh báo không cho phát sinh báo động mới), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_Enable`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Enable (Kích hoạt (Enable) cho phép cảnh báo hoạt động trở lại), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_Test`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Test (Kích thử nghiệm (Test) cảnh báo trong thời gian duy trì tối thiểu), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`OCmd_Ack`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xác nhận (Acknowledge) cảnh báo hiện tại |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`OCmd_Shelve`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Tạm hoãn (Shelve) cảnh báo trong thời gian cấu hình |
| **`OCmd_Unshelve`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo ngay lập tức |
| **`ORdy_Ack`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Ack (Xác nhận (Acknowledge) cảnh báo hiện tại), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Shelve`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Shelve (Tạm hoãn (Shelve) cảnh báo trong thời gian cấu hình), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Unshelve`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unshelve (Gỡ bỏ tạm hoãn (Unshelve) cảnh báo ngay lập tức), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
