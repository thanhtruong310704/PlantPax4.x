# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_DInAdv`
**Thuộc chương:** [Chương 1: Xử lý tín hiệu Vào/Ra (I/O Processing)](../01_CHUONG_1_IO_PROCESSING.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `IO Processing 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 53–57)  
**Tệp định nghĩa khối lệnh:** `P_DInAdv_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_DInAdv`:** **62** Biến đầu vào (`Input Parameters`) | **45** Biến đầu ra (`Output Parameters`) | **40** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_DInAdv` trong Studio 5000
- **Chức năng cốt lõi:** Khối đầu vào số nâng cao. Tích hợp logic **Công tắc tốc độ quay (Zero-Speed / Underspeed Switch)** nhận phản hồi chạy từ động cơ (`Inp_MotorRun`) với thời gian trễ khởi động, và hỗ trợ **2 cảnh báo trạng thái bất thường độc lập (`Alm1`, `Alm2`)**.
- **Khi nào CẦN sử dụng:** Khi giám sát **cảm biến báo quay ở đuôi băng tải, gầu tải, quạt gió, máy khuấy**: nếu Contactor động cơ đã đóng (`Inp_MotorRun = 1`) mà sau thời gian khởi động trục bị động không quay (do đứt dây curoa hoặc trượt khớp nối), khối lập tức báo lỗi dừng máy.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Cho các công tắc hành trình / báo mức thông thường chỉ cần 1 cảnh báo ([`P_DIn.md`](./P_DIn.md)).

### Quy trình 4 bước đấu nối và cấu hình `P_DInAdv`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_Gate`, `Inp_IOFault`, `Inp_PV`, `Inp_PVNotify`, `Inp_Reset`, `Inp_RunFdbk`, `Inp_Sim`, `Inp_SpeedFdbk`, `Inp_Target`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_HasStdySSWR0`, `Cfg_Combine_Sts_RunDec`, `Cfg_Debounce`, `Cfg_DecPlcs`, `Cfg_GateDly`, `Cfg_HasSSW`, `Cfg_HasPulseSSW`, `Cfg_HasPulseSpdConv`, `Cfg_HasIOFaultAlm`, `Cfg_HasStdySSWR1`, `Cfg_HasTgtDisagreeWAlm`, `Cfg_HasVSDSpFbk`, `Cfg_HasTgtDisagreeAlm`, `Cfg_HasTgtDisWF`, `Cfg_HasMoreObj`, `Cfg_IOFaultResetReqd`, `Cfg_IOFaultAckReqd`, `Cfg_IOFaultSeverity`...
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_0StText`, `Cfg_1StText`, `Cfg_AccTime`, `Cfg_AcStText`, `Cfg_Area`, `Cfg_DcStText`, `Cfg_DecTime`, `Cfg_Desc`, `Cfg_Label`, `Cfg_PulseMin`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_DInAdv` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_DInAdv` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PCmd_IOFaultAck`, `PCmd_Reset`, `PCmd_IOFaultSuppress`, `PCmd_IOFaultUnshelve`, `PCmd_IOFaultUnsuppress`, `PCmd_TgtDisagreeAck`, `PCmd_TgtDisagreeSuppress`, `PCmd_TgtDisagreeUnshelve`, `PCmd_TgtDisagreeUnsuppress`, `PCmd_TgtDisagreeWAck`, `PCmd_TgtDisagreeWSuppress`, `PCmd_TgtDisagreeWUnsuppress`, `PCmd_TgtDisagreeWUnshelve`
   - **Cách sử dụng trong code PLC:** Gán giá trị đặt vào `PSet_*` (nếu có) hoặc kích xung 1 chu kỳ vào các bit lệnh `PCmd_* = 1` để xóa lỗi (`PCmd_Reset`), xác nhận cảnh báo (`PCmd_*Ack`) hoặc ức chế cảnh báo (`PCmd_*Suppress`). Khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi.
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Alm_IOFault`, `Alm_TgtDisagree`, `Alm_TgtDisagreeW`, `Sts_Acc`, `Sts_AlmInh`, `Sts_Dec`, `Sts_Err`, `Sts_InpPV`, `Sts_MaintByp`, `Sts_IOFault`, `Sts_IOFaultDisabled`, `Sts_IOFaultShelved`, `Sts_IOFaultSuppressed`, `Sts_PV`, `Sts_PVBad`, `Sts_RdyAck`, `Sts_RdyReset`, `Sts_Run`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 62 Biến Đầu Vào (`Input Parameters`) của `P_DInAdv`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Cfg_HasStdySSWR0`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cấu hình công tắc tốc độ kiểu mức 0 duy trì tương ứng trạng thái đang chạy |
| **`Cfg_Combine_Sts_RunDec`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Gộp cả trạng thái đang Chạy (Run) và đang Giảm tốc (Decelerate) thành trạng thái báo Chạy (Sts_Run = 1) |
| **`Cfg_Debounce`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian lọc chống rung tiếp điểm đầu vào (Debounce Time, tính bằng giây) |
| **`Cfg_DecPlcs`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho tốc độ/giá trị trên màn hình HMI (từ 0 đến 6 chữ số) |
| **`Cfg_GateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng điều kiện (Gate Delay, tính bằng giây): Khoảng thời gian chân Inp_Gate phải duy trì ở mức 1 trước khi cho phép bật giám sát đầu vào |
| **`Cfg_HasSSW`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Sử dụng khối P_DInAdv làm chức năng giám sát Công tắc tốc độ quay (Speed Switch) |
| **`Cfg_HasPulseSSW`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cấu hình công tắc tốc độ kiểu đếm chuỗi xung (Pulse Speed Switch) |
| **`Cfg_HasPulseSpdConv`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Bật chức năng quy đổi tần số xung công tắc tốc độ sang phần trăm tốc độ quay |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasStdySSWR1`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cấu hình công tắc tốc độ kiểu mức 1 duy trì tương ứng trạng thái đang chạy |
| **`Cfg_HasTgtDisagreeWAlm`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo cảnh báo sớm lệch trạng thái mục tiêu (Target Disagree Warning): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasVSDSpFbk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Công tắc tốc độ được kết hợp sử dụng cùng với tín hiệu tốc độ biến tần (VSD) |
| **`Cfg_HasTgtDisagreeAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lệch trạng thái mục tiêu (Target Disagree): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasTgtDisWF`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Bật đầy đủ cả hai cấp Cảnh báo sớm (Warning) và Báo lỗi (Failure) khi tín hiệu lệch khỏi trạng thái mục tiêu |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_Maintain_StsRun`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Duy trì cờ báo chạy Sts_Run trong suốt thời gian động cơ đang giảm tốc |
| **`Cfg_NormTextVis`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Luôn hiển thị chuỗi trạng thái trên biểu tượng đồ họa ngay cả khi ở trạng thái bình thường; 0 = Ẩn chữ khi bình thường |
| **`Cfg_NoSubstPV`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Khóa chức năng nhập giá trị thay thế (Substitute PV): 0 = Cho phép nhân viên bảo trì nhập giá trị PV thay thế; 1 = Cấm sử dụng tính năng Substitute PV |
| **`Cfg_SSW_Type`** | `SINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Kiểu giám sát Công tắc tốc độ (Speed Switch Type): 1 = Mức 0 duy trì là Chạy, 2 = Mức 1 duy trì là Chạy, 4 = Đếm xung quay (Pulse) |
| **`Cfg_TgtDisagreeAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lệch trạng thái mục tiêu (Target Disagree): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_TgtDisagreeOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho lệch trạng thái mục tiêu (Target Disagree): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_TgtDisagreeOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho lệch trạng thái mục tiêu (Target Disagree): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_TgtDisagreeResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lệch trạng thái mục tiêu (Target Disagree): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_TgtDisagreeSSWTgt`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Trạng thái mục tiêu đặt cho bộ giám sát lệch trạng thái của Công tắc tốc độ |
| **`Cfg_TgtDisagreeWOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho cảnh báo sớm lệch trạng thái mục tiêu (Target Disagree Warning): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_TgtDisagreeWOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho cảnh báo sớm lệch trạng thái mục tiêu (Target Disagree Warning): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_TgtDisagreeWAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo cảnh báo sớm lệch trạng thái mục tiêu (Target Disagree Warning): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_TgtDisagreeWResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo cảnh báo sớm lệch trạng thái mục tiêu (Target Disagree Warning): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_TgtDisagreeSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lệch trạng thái mục tiêu (Target Disagree) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_TgtDisagreeWSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo cảnh báo sớm lệch trạng thái mục tiêu (Target Disagree Warning) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_UseRunFdbkSpdSW`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Chỉ cập nhật trạng thái công tắc tốc độ khi có tín hiệu phản hồi động cơ đang chạy (Inp_RunFdbk = 1) |
| **`Cfg_VSD_FailTime`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ phát báo động lỗi (Failure Delay Time) khi kết hợp với biến tần (giây) |
| **`Cfg_VSD_LoLIM`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng tốc độ thấp của biến tần (VSD Speed Low Limit) để hiệu chỉnh hệ số cảnh báo/lỗi công tắc tốc độ |
| **`Cfg_VSD_WarnTime`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ phát cảnh báo sớm (Warning Delay Time) khi kết hợp với biến tần (giây) |
| **`Inp_Gate`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input): Khi = 1, cho phép giám sát tín hiệu đầu vào Inp (áp dụng On-Delay/Off-Delay sau thời gian Cfg_GateDly); khi = 0, khóa cổng và ép ngõ ra về 0 |
| **`Inp_IOFault`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi truyền thông I/O của thiết bị (0 = Tốt/OK, 1 = Lỗi truyền thông I/O Fault) |
| **`Inp_PV`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu biến quá trình đầu vào (Process Variable - PV) đọc từ cảm biến hoặc thẻ Analog/Discrete Input |
| **`Inp_PVNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã mức độ ưu tiên cảnh báo và trạng thái xác nhận (enum) của đối tượng PV liên kết |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Inp_RunFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tiếp điểm phụ phản hồi trạng thái Chạy từ động cơ/khởi động từ (1 = Động cơ đang chạy, 0 = Động cơ đã dừng) |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân kích hoạt chế độ Mô phỏng (Simulation): Khi = 1, khóa các ngõ ra vật lý ở mức an toàn và dùng giá trị/phản hồi giả lập nội bộ; khi = 0, vận hành thực tế bình thường |
| **`Inp_SpeedFdbk`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu phản hồi tốc độ thực tế từ biến tần (Speed Feedback, đơn vị thô hoặc 0..100%) |
| **`Inp_Target`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái mục tiêu mong muốn của tín hiệu đầu vào số (0 hoặc 1): Nếu trạng thái thực tế khác Inp_Target sẽ báo lệch mục tiêu (Target Disagree) |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_TgtDisagreeAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lệch trạng thái mục tiêu (Target Disagree) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_TgtDisagreeSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lệch trạng thái mục tiêu (Target Disagree) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_TgtDisagreeUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lệch trạng thái mục tiêu (Target Disagree) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_TgtDisagreeUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lệch trạng thái mục tiêu (Target Disagree) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_TgtDisagreeWAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo cảnh báo sớm lệch trạng thái mục tiêu (Target Disagree Warning) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_TgtDisagreeWSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo cảnh báo sớm lệch trạng thái mục tiêu (Target Disagree Warning) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_TgtDisagreeWUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo cảnh báo sớm lệch trạng thái mục tiêu (Target Disagree Warning) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_TgtDisagreeWUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo cảnh báo sớm lệch trạng thái mục tiêu (Target Disagree Warning) ngay lập tức (biến xung tự động xóa về 0) |
| **`Set_SimPV`** | `BOOL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Giá trị PV giả lập (đơn vị kỹ thuật EU) được sử dụng khi bật chế độ Mô phỏng (Inp_Sim = 1) |
| **`Set_SymbolVis`** | `BOOL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Cờ điều khiển hiển thị biểu tượng đồ họa trên HMI: 0 = Ẩn biểu tượng trừ khi có lỗi; 1 = Luôn hiển thị biểu tượng |

## 3. Bảng chi tiết 45 Biến Đầu Ra (`Output Parameters`) của `P_DInAdv`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi truyền thông thẻ I/O (I/O Fault) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Ack_TgtDisagree`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lệch trạng thái mục tiêu (Target Disagree) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Ack_TgtDisagreeW`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động cảnh báo sớm lệch trạng thái mục tiêu (Target Disagree Warning) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`Alm_TgtDisagree`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lệch trạng thái mục tiêu (Target Disagree) |
| **`Alm_TgtDisagreeW`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho cảnh báo sớm lệch trạng thái mục tiêu (Target Disagree Warning) |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình cảnh báo: Thời gian duy trì tối thiểu (Min On Time), thời gian tạm hoãn (Shelf Time) hoặc mức độ nghiêm trọng (Severity 1..1000) không hợp lệ |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị đặt bộ định thời gian (Timer Preset) nằm ngoài phạm vi hợp lệ 0..2.147.483 giây |
| **`P_DIn`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_DIn) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng dữ liệu tổng hợp cuối cùng (0 = Tín hiệu thực Tốt đã xác nhận, 1 = Tín hiệu thực Tốt giả định, 2 = Không cấu hình phản hồi/Tốt giả định, 8 = Đang mô phỏng, 9 = Mô phỏng vòng lặp, 10 = Giá trị nhập tay, 16 = Ngoài đặc tính kỹ thuật, 17 = Thay thế tại thiết bị/bus, 18 = Thay thế bởi Bảo trì, 19 = Ngắt bảo vệ giữ giá trị cũ, 20 = Ngắt bảo vệ dùng giá trị thay thế, 32 = Lỗi tín hiệu vượt dải/NaN, 33 = Lỗi kênh I/O, 34 = Lỗi module I/O, 35 = Lỗi cấu hình) |
| **`Sts`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái đầu vào số sau khi đã lọc chống rung (Debounce) và chọn giá trị thay thế (Substitute PV) nếu dùng |
| **`Sts_Acc`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang trong quá trình Tăng tốc (Accelerating) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có ít nhất một cảnh báo bị Tạm hoãn (Shelved), Vô hiệu hóa (Disabled) hoặc Ức chế (Suppressed) — hiển thị biểu tượng chữ "I" trên HMI |
| **`Sts_Dec`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang trong quá trình Giảm tốc (Decelerating) |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Sts_InpPV`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang sử dụng tín hiệu PV thực tế từ cảm biến đầu vào (chế độ bình thường) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoặc cưỡng bức bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi truyền thông thẻ I/O (I/O Fault) |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_PV`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái đầu vào số thực tế (đã lọc chống rung nhưng trước khi qua bộ chọn Substitute PV) |
| **`Sts_PVBad`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Chất lượng tín hiệu PV Xấu (Bad Quality) hoặc tín hiệu vượt ngoài dải lỗi (Out of Range) |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc chốt ngắt bảo vệ (Shed) đã hết lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_Run`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở trạng thái Chạy (Running) |
| **`Sts_SubstPV`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang sử dụng giá trị PV thay thế (Substitute PV do nhân viên bảo trì nhập) thay cho tín hiệu cảm biến thật |
| **`Sts_TgtDisagree`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lệch trạng thái mục tiêu (Target Disagree) |
| **`Sts_TgtDisagreeCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của lệch trạng thái mục tiêu (Target Disagree) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_TgtDisagreeWCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của cảnh báo sớm lệch trạng thái mục tiêu (Target Disagree Warning) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_TgtDisagreeDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lệch trạng thái mục tiêu (Target Disagree) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_TgtDisagreeGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của lệch trạng thái mục tiêu (Target Disagree) đã đếm xong, đang cho phép giám sát |
| **`Sts_TgtDisagreeWDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo cảnh báo sớm lệch trạng thái mục tiêu (Target Disagree Warning) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_TgtDisagreeWGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của cảnh báo sớm lệch trạng thái mục tiêu (Target Disagree Warning) đã đếm xong, đang cho phép giám sát |
| **`Sts_TgtDisagreeShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lệch trạng thái mục tiêu (Target Disagree) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_TgtDisagreeSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lệch trạng thái mục tiêu (Target Disagree) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_TgtDisagreeWSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo cảnh báo sớm lệch trạng thái mục tiêu (Target Disagree Warning) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_TgtDisagreeW`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện cảnh báo sớm lệch trạng thái mục tiêu (Target Disagree Warning) |
| **`Sts_TgtDisagreeWShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo cảnh báo sớm lệch trạng thái mục tiêu (Target Disagree Warning) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Val_Fault`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái lỗi thiết bị (Device Fault Status — kiểu liệt kê enum: 0 = Không lỗi, 16 = Lỗi khởi động/mở, 17 = Lỗi dừng/đóng, 20 = Lo, 21 = Hi, 24 = LoLo, 25 = HiHi, 32 = Fail/I/O Fault, 34 = Lỗi cấu hình CfgErr) |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận tổng hợp của khối này cộng với các đối tượng kênh/quá tải liên kết |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái hoạt động xác nhận của thiết bị (Device Confirmed Status enum dùng để điều khiển màu sắc và biểu tượng động trên HMI) |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms Count) |

## 4. Bảng chi tiết 40 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_DInAdv`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_0StText`** | `STRING_8` | `'Off'` | [Biến cấu hình thông số] Chuỗi văn bản hiển thị trên HMI khi trạng thái bằng 0 (ví dụ: "Closed", "Stopped", "Normal") |
| **`Cfg_1StText`** | `STRING_8` | `On'` | [Biến cấu hình thông số] Chuỗi văn bản hiển thị trên HMI khi trạng thái bằng 1 (ví dụ: "Open", "Running", "Tripped") |
| **`Cfg_AccTime`** | `DINT` | `5` | [Biến cấu hình thông số] Thời gian tăng tốc khởi động của động cơ trước khi bắt đầu kiểm tra công tắc tốc độ (giây) |
| **`Cfg_AcStText`** | `STRING_8` | `'Acc'` | [Biến cấu hình thông số] Chuỗi văn bản hiển thị trên HMI khi ở trạng thái Tăng tốc (Accelerate State) |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_DcStText`** | `STRING_8` | `'Decc'` | [Biến cấu hình thông số] Chuỗi văn bản hiển thị trên HMI khi ở trạng thái Giảm tốc (Decelerate State) |
| **`Cfg_DecTime`** | `DINT` | `10` | [Biến cấu hình thông số] Thời gian giảm tốc dừng máy của động cơ (giây) |
| **`Cfg_Desc`** | `STRING_40` | `'Discrete Input'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_Label`** | `STRING_20` | `'Discrete Input'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_PulseMin`** | `DINT` | `0` | [Biến cấu hình thông số] Số xung định mức trên mỗi phút (Pulses per Minute) tương ứng với 100% tốc độ quay |
| **`Cfg_RStText`** | `STRING_8` | `'Run'` | [Biến cấu hình thông số] Chuỗi văn bản hiển thị trên HMI khi ở trạng thái Chạy (Run State) |
| **`Cfg_SpeedFdbkEU`** | `STRING_8` | `'Hz'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của tốc độ phản hồi biến tần (ví dụ: "Hz", "RPM", "%") hiển thị trên HMI |
| **`Cfg_SymbolVis`** | `BOOL` | `0` | [Biến cấu hình thông số] Cấu hình hiển thị biểu tượng trên HMI: 1 = Luôn hiển thị biểu tượng; 0 = Chỉ hiển thị khi có lỗi/bất thường |
| **`Cfg_Tag`** | `STRING_20` | `'P_DInAdv'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`Err_EngTb1`** | `BOOL` | `0` | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Có lỗi nhập thông số trên Tab Kỹ thuật số 1 (Engineering Tab 1) của HMI Faceplate |
| **`Err_EngTb2`** | `BOOL` | `0` | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Có lỗi nhập thông số trên Tab Kỹ thuật số 2 (Engineering Tab 2) của HMI Faceplate |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_DInAdv'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`IOFault`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`MCmd_InpPV`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Sử dụng lại tín hiệu PV thực tế từ cảm biến đầu vào (ngưng dùng giá trị thay thế Substitute PV) |
| **`MCmd_SubstPV`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Chuyển sang sử dụng giá trị PV thay thế (Substitute PV) do nhân viên bảo trì nhập |
| **`MRdy_InpPV`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_InpPV (Sử dụng lại tín hiệu PV thực tế từ cảm biến đầu vào (ngưng dùng giá trị thay thế Substitute PV)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_SubstPV`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_SubstPV (Chuyển sang sử dụng giá trị PV thay thế (Substitute PV) do nhân viên bảo trì nhập), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MSet_SubstPV`** | `BOOL` | `0` | [Giá trị đặt từ Bảo trì HMI] Giá trị đặt từ Nhân viên bảo trì (Maintenance HMI): Giá trị PV thay thế (Substitute PV, đơn vị EU) do nhân viên bảo trì nhập thủ công để ghi đè tín hiệu cảm biến |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`PulseSpd_ONS1`** | `BOOL` | `0` | Bit nhớ tạo xung 1 chu kỳ (One-Shot) phục vụ tính toán tốc độ xung trong khối P_DInAdv |
| **`SSW_StaticGate`** | `P_Gate` | `{...}` | Khối con P_Gate nội bộ xử lý trễ cổng điều kiện tĩnh cho chức năng Công tắc tốc độ (Speed Switch) |
| **`Sts_Equip_Start`** | `BOOL` | `0` | [Cờ báo trạng thái logic (Status)] Trạng thái báo thiết bị truyền động đang bắt đầu chu trình khởi động |
| **`TgtDisagree`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lệch trạng thái mục tiêu (Target Disagree) |
| **`TgtDisagreeGate`** | `P_Gate` | `{...}` | Khối con P_Gate nội bộ xử lý trễ cổng điều kiện cho cảnh báo Lệch trạng thái mục tiêu (Target Disagree) |
| **`TgtDisagreeW`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho cảnh báo sớm lệch trạng thái mục tiêu (Target Disagree Warning) |
| **`TgtDisagreeWarnGate`** | `P_Gate` | `{...}` | Khối con P_Gate nội bộ xử lý trễ cổng điều kiện cho cảnh báo sớm Lệch trạng thái mục tiêu (Target Disagree Warning) |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo hiện hành và trạng thái xác nhận của khối này (0 = Không có cảnh báo, 1 = Đã hết lỗi nhưng chờ Reset/Ack, 2..3 = Thấp, 4..5 = Trung bình, 6..7 = Cao, 8..9 = Khẩn cấp) |
| **`Val_PlsSpdPerc`** | `REAL` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Tốc độ quay tính toán từ tần số xung công tắc tốc độ theo phần trăm (%) |
| **`VSD_Sim`** | `DINT` | `0` | Biến nội bộ lưu giá trị tốc độ biến tần giả lập trong khối P_DInAdv |
| **`VSD_Spd_Cmd`** | `DINT` | `0` | Biến nội bộ lưu giá trị tốc độ đặt biến tần từ HMI trong khối P_DInAdv |
