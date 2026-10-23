# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_AOutHART`
**Thuộc chương:** [Chương 1: Xử lý tín hiệu Vào/Ra (I/O Processing)](../01_CHUONG_1_IO_PROCESSING.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `IO Processing 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 76)  
**Tệp định nghĩa khối lệnh:** `P_AOutHART_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_AOutHART`:** **80** Biến đầu vào (`Input Parameters`) | **78** Biến đầu ra (`Output Parameters`) | **48** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_AOutHART` trong Studio 5000
- **Chức năng cốt lõi:** Mở rộng từ `P_AOut` để xuất tín hiệu điều khiển 4–20mA kết hợp đọc dữ liệu phản hồi kỹ thuật số **HART** từ bộ định vị van thông minh (Smart Valve Positioner).
- **Khi nào CẦN sử dụng:** Khi xuất tín hiệu ra van điều khiển thông minh có HART và muốn đọc ngược lại vị trí thực của ty van, áp suất khí nén cấp vào bầu van và trạng thái chẩn đoán HART mà không cần kéo thêm dây AI phản hồi.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi thẻ Analog Output không có HART (dùng [`P_AOut.md`](./P_AOut.md) hoặc [`P_ValveC.md`](./P_ValveC.md)).

### Quy trình 4 bước đấu nối và cấu hình `P_AOutHART`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Ref_ChanData`, `Ref_DiagTable`, `Inp_IntlkOK`, `Inp_NBIntlkOK`, `Inp_IOFault`, `Inp_Sim`, `Inp_Hand`, `Inp_Ovrd`, `Inp_OvrdCV`, `Inp_ExtInh`, `Inp_Reset`
   - **Các chân tham chiếu dữ liệu (`Ref_*` / `InOut`):** `Ref_ChanData`, `Ref_DiagTable` — bắt buộc gán thẻ mảng/cấu trúc tương ứng trong Controller Tags.
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_ShedHold`, `Cfg_SkipRoCLim`, `Cfg_SetTrack`, `Cfg_SetTrackOvrdHand`, `Cfg_HasHART`, `Cfg_HasMoreObj`, `Cfg_HasPV`, `Cfg_HasSV`, `Cfg_HasTV`, `Cfg_HasFV`, `Cfg_UseVarSts`, `Cfg_UseHARTText`, `Cfg_UseHARTScaling`, `Cfg_AutoUpdDevInfo`, `Cfg_ManUpdDevInfo`, `Cfg_HasIntlkObj`, `Cfg_HasCVNav`, `Cfg_OvrdIntlk`...
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_CVNavTag`, `Cfg_Desc`, `Cfg_EU`, `Cfg_FVEU`, `Cfg_FVLabel`, `Cfg_Label`, `Cfg_PVEU`, `Cfg_PVLabel`, `Cfg_SVEU`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_AOutHART` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_AOutHART` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PSet_CV`, `PSet_Owner`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_IntlkTripAck`, `PCmd_IntlkTripSuppress`, `PCmd_IntlkTripUnsuppress`, `PCmd_IntlkTripUnshelve`, `PCmd_IOFaultAck`, `PCmd_IOFaultSuppress`, `PCmd_IOFaultUnsuppress`, `PCmd_IOFaultUnshelve`
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh điều khiển tự động, kiểm tra khối đã sẵn sàng ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển quyền về Program, sau đó gán giá trị vào `PSet_*` và kích xung `PCmd_* = 1` (khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Out_CV`, `Val_CVSet`, `Val_CVOut`, `Val_CVFdbk`, `Val_CVEUMin`, `Val_CVEUMax`, `Val_PV`, `Val_SV`, `Val_TV`, `Val_FV`, `Val_DiagCode1`, `Val_DiagCode2`, `Val_DiagCode3`, `Val_NAMURSts`, `Val_NAMURSts1`, `Val_NAMURSts2`, `Val_NAMURSts3`, `Val_Sts`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 80 Biến Đầu Vào (`Input Parameters`) của `P_AOutHART`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Ref_ChanData`** | `HART_ChanData` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut liên kết tới cấu trúc dữ liệu kênh HART của thẻ Analog Output |
| **`Ref_DiagTable`** | `P_DiagTable[1]` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới bảng tra cứu giải mã Mã chẩn đoán HART (Diagnostic Code) sang chuỗi văn bản mô tả |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp Khóa liên động an toàn có thể Bypass (1 = Liên động OK cho phép chạy; 0 = Cắt dừng thiết bị ngay lập tức; nối từ P_Intlk.Sts_IntlkOK) |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp Khóa liên động an toàn KHÔNG thể Bypass (1 = Liên động bất khả kháng OK; 0 = Cắt dừng thiết bị ngay lập tức; nối từ P_Intlk.Sts_NBIntlkOK) |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi truyền thông I/O của thiết bị (0 = Tốt/OK, 1 = Lỗi truyền thông I/O Fault) |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân kích hoạt chế độ Mô phỏng (Simulation): Khi = 1, khóa các ngõ ra vật lý ở mức an toàn và dùng giá trị/phản hồi giả lập nội bộ; khi = 0, vận hành thực tế bình thường |
| **`Inp_Hand`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu chọn chế độ Điều khiển bằng tay tại chỗ (Hand/Local): 1 = Chiếm quyền sang chế độ Hand (thường nối từ công tắc cứng tại tủ MCC); 0 = Nhả chế độ Hand |
| **`Inp_Ovrd`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu kích hoạt chế độ Cưỡng bức ưu tiên (Override): 1 = Chiếm quyền sang chế độ Override; 0 = Nhả chế độ Override |
| **`Inp_OvrdCV`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Giá trị đặt biến điều khiển CV khi ở chế độ Cưỡng bức (Override CV Target, đơn vị kỹ thuật EU) |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân khóa cấm nguồn lệnh ngoài (External Inhibit): 1 = Cấm chế độ External chiếm quyền điều khiển; 0 = Cho phép External chiếm quyền |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Cfg_ShedHold`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Hành động xử lý ngõ ra CV khi vi phạm Khóa liên động (Interlock Trip): 1 = Giữ nguyên giá trị CV hiện tại (Hold Last State); 0 = Đưa ngõ ra CV về giá trị an toàn định trước (Cfg_IntlkCV) |
| **`Cfg_SkipRoCLim`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Bỏ qua bộ giới hạn tốc độ thay đổi CV (Rate-of-Change Limiting) khi ở chế độ Bảo trì, Cưỡng bức (Override) hoặc khi ngắt Liên động |
| **`Cfg_SetTrack`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép các giá trị đặt (PSet_CV / OSet_CV / SP) tự động bám theo ngõ ra thực tế hiện hành để chuyển mạch không giật (Bumpless Transfer) giữa Operator và Program |
| **`Cfg_SetTrackOvrdHand`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép giá trị đặt của Program và Operator tự động bám theo giá trị CV khi đang ở chế độ Override hoặc Hand |
| **`Cfg_HasHART`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Thiết bị đo có sử dụng truyền thông kỹ thuật số HART; 0 = Thiết bị chỉ dùng tín hiệu tương tự 4–20mA thuần túy |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_HasPV`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Biến sơ cấp kỹ thuật số HART (PV) được cấu hình và hiển thị trên HMI Faceplate |
| **`Cfg_HasSV`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Biến thứ cấp kỹ thuật số HART (SV) được cấu hình và hiển thị trên HMI Faceplate |
| **`Cfg_HasTV`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Biến thứ ba kỹ thuật số HART (TV) được cấu hình và hiển thị trên HMI Faceplate |
| **`Cfg_HasFV`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Biến thứ tư kỹ thuật số HART (FV) được cấu hình và hiển thị trên HMI Faceplate |
| **`Cfg_UseVarSts`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Sử dụng trạng thái chất lượng của các biến kỹ thuật số HART để đánh giá lỗi; 0 = Bỏ qua trạng thái biến HART |
| **`Cfg_UseHARTText`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tự động lấy chuỗi Tag, Mô tả, Nhãn và Đơn vị kỹ thuật đọc qua HART để hiển thị HMI; 0 = Dùng chuỗi nhập thủ công |
| **`Cfg_UseHARTScaling`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tự động lấy dải đo Raw và EU đọc từ thiết bị qua HART để quy đổi tỷ lệ; 0 = Dùng dải đo nhập thủ công trong Cfg_* |
| **`Cfg_AutoUpdDevInfo`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tự động đọc cập nhật thông tin thiết bị HART định kỳ; 0 = Không tự động cập nhật |
| **`Cfg_ManUpdDevInfo`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép yêu cầu đọc cập nhật thông tin thiết bị HART thủ công từ HMI; 0 = Không cho phép |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Khóa liên động (P_Intlk) nối vào chân Inp_IntlkOK và bật nút điều hướng mở Faceplate P_Intlk |
| **`Cfg_HasCVNav`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép hiển thị nút điều hướng nhảy tới Faceplate của khối cơ cấu chấp hành CV trên HMI |
| **`Cfg_OvrdIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Chế độ Override được quyền bỏ qua các điều kiện Khóa liên động (Interlocks) có thể bypass; 0 = Luôn kiểm tra Interlocks |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt dừng thiết bị về trạng thái an toàn (Shed) và phát báo động khi lỗi truyền thông thẻ I/O; 0 = Chỉ phát báo động |
| **`Cfg_DecPlcs`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho tốc độ/giá trị trên màn hình HMI (từ 0 đến 6 chữ số) |
| **`Cfg_PVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho giá trị PV trên màn hình HMI (từ 0 đến 6 chữ số) |
| **`Cfg_SVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho giá trị SV trên màn hình HMI (từ 0 đến 6 chữ số) |
| **`Cfg_TVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho giá trị TV trên màn hình HMI (từ 0 đến 6 chữ số) |
| **`Cfg_FVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho giá trị FV trên màn hình HMI (từ 0 đến 6 chữ số) |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_MinCV`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp dưới tối thiểu cho phép của ngõ ra điều khiển CV (Minimum CV Limit, đơn vị kỹ thuật EU) |
| **`Cfg_MaxCV`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp trên tối đa cho phép của ngõ ra điều khiển CV (Maximum CV Limit, đơn vị kỹ thuật EU) |
| **`Cfg_CVRoCLimInc`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn tốc độ tăng tối đa của ngõ ra CV (Increasing Rate-of-Change Limit, đơn vị EU/giây; đặt = 0.0 để tăng tức thời không giới hạn) |
| **`Cfg_CVRoCLimDec`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn tốc độ giảm tối đa của ngõ ra CV (Decreasing Rate-of-Change Limit, đơn vị EU/giây; đặt = 0.0 để giảm tức thời không giới hạn) |
| **`Cfg_IntlkCV`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị ngõ ra CV an toàn (đơn vị kỹ thuật EU) được xuất ra khi vi phạm Khóa liên động (nếu Cfg_ShedHold = 0) |
| **`Cfg_CVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật đầu ra CV tương ứng với Cfg_CVRawMin (thường là 0.0%) |
| **`Cfg_CVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật đầu ra CV tương ứng với Cfg_CVRawMax (thường là 100.0%) |
| **`Cfg_CVRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị thô đầu ra tối thiểu (CV Raw Min) gửi ra thẻ Analog Output tương ứng với Cfg_CVEUMin |
| **`Cfg_CVRawMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị thô đầu ra tối đa (CV Raw Max) gửi ra thẻ Analog Output tương ứng với Cfg_CVEUMax |
| **`Cfg_MaxInactiveCV`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng CV tối thiểu (đơn vị CVEU) để bật cờ báo thiết bị đang tích cực hoạt động (Sts_Active = 1 trên HMI) |
| **`PSet_CV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Giá trị biến điều khiển đầu ra (Controlled Variable - CV, tính theo đơn vị kỹ thuật EU) |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Mã định danh Chủ sở hữu (Owner ID: ghi khác 0 để đăng ký chiếm quyền điều khiển, ghi 0 để nhả quyền) |
| **`XSet_CV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài] Giá trị đặt từ Hệ thống điều khiển ngoài (External): Giá trị biến điều khiển đầu ra (Controlled Variable - CV, tính theo đơn vị kỹ thuật EU) |
| **`Set_SimHARTPV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Giá trị biến sơ cấp HART (PV) giả lập dùng trong chế độ Mô phỏng (khi Inp_Sim = 1, đơn vị EU) |
| **`Set_SimHARTSV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Giá trị biến thứ cấp HART (SV) giả lập dùng trong chế độ Mô phỏng (khi Inp_Sim = 1, đơn vị EU) |
| **`Set_SimHARTTV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Giá trị biến thứ ba HART (TV) giả lập dùng trong chế độ Mô phỏng (khi Inp_Sim = 1, đơn vị EU) |
| **`Set_SimHARTFV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Giá trị biến thứ tư HART (FV) giả lập dùng trong chế độ Mô phỏng (khi Inp_Sim = 1, đơn vị EU) |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Chương trình tự động (từ Operator sang Program) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Người vận hành (từ Program sang Operator) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa giữ nguồn lệnh hiện tại (không cho phép nguồn lệnh kia giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Đưa nguồn lệnh về chế độ Bình thường mặc định (Normal: Program hoặc Operator theo Cfg_ProgNormal) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) ngay lập tức (biến xung tự động xóa về 0) |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Chiếm quyền điều khiển (Acquire) về chế độ tương ứng |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |

## 3. Bảng chi tiết 78 Biến Đầu Ra (`Output Parameters`) của `P_AOutHART`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Out_CV`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị ngõ ra điều khiển tương tự (Controlled Variable - CV) gửi tới cơ cấu chấp hành / thẻ Analog Output (đơn vị Raw hoặc EU) |
| **`Val_CVSet`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đặt CV đang được chọn (trước khi qua bộ giới hạn tốc độ thay đổi RoC, đơn vị EU) |
| **`Val_CVOut`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị biến điều khiển CV xuất ra thực tế (sau khi qua bộ giới hạn tốc độ thay đổi RoC, đơn vị EU) |
| **`Val_CVFdbk`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị phản hồi CV đọc ngược từ kênh Analog Output hoặc cơ cấu chấp hành (đơn vị EU) |
| **`Val_CVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận dưới của dải đơn vị kỹ thuật ngõ ra CV = MIN(Cfg_CVEUMin, Cfg_CVEUMax) |
| **`Val_CVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận trên của dải đơn vị kỹ thuật ngõ ra CV = MAX(Cfg_CVEUMin, Cfg_CVEUMax) |
| **`Val_PV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị biến sơ cấp kỹ thuật số (HART Primary Variable - PV, tính theo đơn vị PVEU) |
| **`Val_SV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị biến thứ cấp kỹ thuật số (HART Secondary Variable - SV, tính theo đơn vị SVEU) |
| **`Val_TV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị biến thứ ba kỹ thuật số (HART Third Variable - TV, tính theo đơn vị TVEU) |
| **`Val_FV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị biến thứ tư kỹ thuật số (HART Fourth Variable - FV, tính theo đơn vị FVEU) |
| **`Val_DiagCode1`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã chẩn đoán thiết bị HART số #1 (-1 = Không có lỗi chẩn đoán) |
| **`Val_DiagCode2`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã chẩn đoán thiết bị HART số #2 (-1 = Không có lỗi chẩn đoán) |
| **`Val_DiagCode3`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã chẩn đoán thiết bị HART số #3 (-1 = Không có lỗi chẩn đoán) |
| **`Val_NAMURSts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái chẩn đoán chuẩn NAMUR NE107 tổng hợp (mức xấu nhất trong 3 mã chẩn đoán HART) dùng để hiển thị biểu tượng trên HMI |
| **`Val_NAMURSts1`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã phân loại trạng thái chuẩn NAMUR NE107 cho mã chẩn đoán HART #1: 0 = Tốt (OK), 1 = Thông tin (Info), 2 = Yêu cầu bảo trì (Maintenance Required), 4 = Ngoài đặc tính (Off Spec), 8 = Kiểm tra chức năng (Function Check), 16 = Lỗi hỏng (Failure) |
| **`Val_NAMURSts2`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã phân loại trạng thái chuẩn NAMUR NE107 cho mã chẩn đoán HART #2: 0 = Tốt (OK), 1 = Thông tin (Info), 2 = Yêu cầu bảo trì (Maintenance Required), 4 = Ngoài đặc tính (Off Spec), 8 = Kiểm tra chức năng (Function Check), 16 = Lỗi hỏng (Failure) |
| **`Val_NAMURSts3`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã phân loại trạng thái chuẩn NAMUR NE107 cho mã chẩn đoán HART #3: 0 = Tốt (OK), 1 = Thông tin (Info), 2 = Yêu cầu bảo trì (Maintenance Required), 4 = Ngoài đặc tính (Off Spec), 8 = Kiểm tra chức năng (Function Check), 16 = Lỗi hỏng (Failure) |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng dữ liệu tổng hợp cuối cùng (0 = Tín hiệu thực Tốt đã xác nhận, 1 = Tín hiệu thực Tốt giả định, 2 = Không cấu hình phản hồi/Tốt giả định, 8 = Đang mô phỏng, 9 = Mô phỏng vòng lặp, 10 = Giá trị nhập tay, 16 = Ngoài đặc tính kỹ thuật, 17 = Thay thế tại thiết bị/bus, 18 = Thay thế bởi Bảo trì, 19 = Ngắt bảo vệ giữ giá trị cũ, 20 = Ngắt bảo vệ dùng giá trị thay thế, 32 = Lỗi tín hiệu vượt dải/NaN, 33 = Lỗi kênh I/O, 34 = Lỗi module I/O, 35 = Lỗi cấu hình) |
| **`SrcQ_PV`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của biến sơ cấp HART PV (kiểu liệt kê enum) |
| **`SrcQ_SV`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của biến thứ cấp HART SV (kiểu liệt kê enum) |
| **`SrcQ_TV`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của biến thứ ba HART TV (kiểu liệt kê enum) |
| **`SrcQ_FV`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của biến thứ tư HART FV (kiểu liệt kê enum) |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái đầu ra tương tự HART: 0 = Đã đạt giá trị đích (At Target), 1 = Đang giảm (Ramp Down), 2 = Đang tăng (Ramp Up), 3 = Đang kẹp giới hạn dưới (Clamp Min), 4 = Đang kẹp giới hạn trên (Clamp Max), 33 = Ngưng phục vụ (Disabled) |
| **`Val_Fault`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái lỗi thiết bị (Device Fault Status — kiểu liệt kê enum: 0 = Không lỗi, 16 = Lỗi khởi động/mở, 17 = Lỗi dừng/đóng, 20 = Lo, 21 = Hi, 24 = LoLo, 25 = HiHi, 32 = Fail/I/O Fault, 34 = Lỗi cấu hình CfgErr) |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận tổng hợp của khối này cộng với các đối tượng kênh/quá tải liên kết |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms Count) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã định danh Chủ sở hữu hiện hành của đối tượng (Current Object Owner ID: 0 = Đang không bị chiếm quyền) |
| **`Sts_Ramping`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị ngõ ra CV đang trong quá trình thay đổi theo độ dốc (Ramping) tiến về giá trị đặt |
| **`Sts_Clamped`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị PV tương tự đang bị kẹp ở ngưỡng giới hạn dưới (Cfg_PVLoLim) hoặc giới hạn trên (Cfg_PVHiLim) |
| **`Sts_SkipRoCLim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang bỏ qua bộ giới hạn tốc độ thay đổi CV trong chu kỳ này (do chuyển sang Maint, Ovrd, Intlk hoặc Hand) |
| **`Sts_Active`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị ngõ ra CV lớn hơn ngưỡng kích hoạt (Cfg_MaxInactiveCV), hiển thị biểu tượng hoạt động trên HMI |
| **`Sts_Available`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị sẵn sàng cho Chương trình tự động (Program) điều khiển (đang ở chế độ Program và không có lỗi/liên động ngắt) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass hiện đang được bỏ qua (Bypassed) |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Chế độ Bypass bảo trì đang kích hoạt (gửi sang khối P_Intlk / P_Perm để cho phép bỏ qua các điều kiện có quyền Bypass) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị chưa sẵn sàng hoạt động (kiểm tra các bit Nrdy_* chi tiết để biết nguyên nhân cụ thể) |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Khóa liên động an toàn không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi truyền thông thẻ I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoặc cưỡng bức bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có ít nhất một cảnh báo bị Tạm hoãn (Shelved), Vô hiệu hóa (Disabled) hoặc Ức chế (Suppressed) — hiển thị biểu tượng chữ "I" trên HMI |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Ngưỡng kẹp giới hạn trên của PV nhỏ hơn ngưỡng kẹp giới hạn dưới (Cfg_PVHiLim < Cfg_PVLoLim) |
| **`Err_EU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải quy đổi đơn vị kỹ thuật (Engineering Units) có giá trị Tối thiểu bằng Tối đa (Min = Max) |
| **`Err_Raw`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải giá trị thô đầu vào (Cfg_InpRawMin = Cfg_InpRawMax) bị trùng nhau |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình cảnh báo: Thời gian duy trì tối thiểu (Min On Time), thời gian tạm hoãn (Shelf Time) hoặc mức độ nghiêm trọng (Severity 1..1000) không hợp lệ |
| **`Sts_Hand`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Điều khiển bằng tay tại chỗ (Hand — ưu tiên cao hơn OoS, Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_OoS`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Ngưng phục vụ (Out of Service — ưu tiên cao hơn Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_Maint`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Bảo trì (Maintenance — ưu tiên cao hơn Ovrd, Ext, Prog, Oper) |
| **`Sts_Ovrd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Cưỡng bức ưu tiên (Override — ưu tiên cao hơn Ext, Prog, Oper) |
| **`Sts_Ext`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Điều khiển ngoài (External — ưu tiên cao hơn Prog, Oper) |
| **`Sts_Prog`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Chương trình tự động (Program) |
| **`Sts_ProgLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Chương trình tự động và đã Khóa (Program Locked — không cho Operator lấy quyền) |
| **`Sts_Oper`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Người vận hành trên HMI (Operator) |
| **`Sts_OperLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Người vận hành và đã Khóa (Operator Locked — không cho Program lấy quyền) |
| **`Sts_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Nguồn lệnh hiện hành đang khớp với nguồn lệnh Bình thường mặc định (Program hoặc Operator theo Cfg_ProgNormal) |
| **`Sts_ExtReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Yêu cầu chuyển sang chế độ External đang bị khóa cấm (không thể chuyển sang External từ trạng thái hiện tại) |
| **`Sts_ProgReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Yêu cầu chuyển sang chế độ Program đang bị khóa cấm (không thể chuyển sang Program từ trạng thái hiện tại) |
| **`Sts_MAcqRcvd`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đã nhận được lệnh Chiếm quyền bảo trì (MCmd_Acq) trong chu kỳ quét hiện tại |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc chốt ngắt bảo vệ (Shed) đã hết lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`Alm_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`Ack_IntlkTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_IntlkTripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_IntlkTripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_IntlkTripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi truyền thông thẻ I/O (I/O Fault) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi truyền thông thẻ I/O (I/O Fault) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`MRdy_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`P_AOutHART`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_AOutHART) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 48 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_AOutHART`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_CVNavTag`** | `STRING_NavTag` | `''` | [Biến cấu hình thông số] Tên thẻ (Tagname) của khối cơ cấu chấp hành CV đích khi nhấn nút điều hướng CV trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'Analog Output'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật (Engineering Units, ví dụ: "°C", "bar", "m3/h", "%") hiển thị trên màn hình HMI |
| **`Cfg_FVEU`** | `STRING_8` | `''` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của biến thứ tư HART FV hiển thị trên màn hình HMI |
| **`Cfg_FVLabel`** | `STRING_16` | `''` | [Biến cấu hình thông số] Nhãn mô tả cho Biến thứ tư (Fourth Variable - FV) hiển thị trên HMI |
| **`Cfg_Label`** | `STRING_20` | `'Analog Output'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_PVEU`** | `STRING_8` | `''` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của biến sơ cấp PV hiển thị trên màn hình HMI |
| **`Cfg_PVLabel`** | `STRING_16` | `''` | [Biến cấu hình thông số] Nhãn mô tả cho Biến sơ cấp (Primary Variable - PV) hiển thị trên HMI |
| **`Cfg_SVEU`** | `STRING_8` | `''` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của biến thứ cấp HART SV hiển thị trên màn hình HMI |
| **`Cfg_SVLabel`** | `STRING_16` | `''` | [Biến cấu hình thông số] Nhãn mô tả cho Biến thứ cấp (Secondary Variable - SV) hiển thị trên HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_AOutHART'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`Cfg_TVEU`** | `STRING_8` | `''` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của biến thứ ba HART TV hiển thị trên màn hình HMI |
| **`Cfg_TVLabel`** | `STRING_16` | `''` | [Biến cấu hình thông số] Nhãn mô tả cho Biến thứ ba (Third Variable - TV) hiển thị trên HMI |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc nội bộ quản lý phân quyền và chuyển mạch giữa 7 nguồn lệnh điều khiển (OoS, Hand, Maint, Ovrd, Ext, Prog, Oper) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `??` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `??` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`IOFault`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bỏ qua (Bypass) tất cả các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Kiểm tra lại bình thường (Check / Gỡ bỏ Bypass) toàn bộ điều kiện Khóa liên động và Cho phép |
| **`MCmd_UpdDevInfo`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Yêu cầu đọc cập nhật thông tin định danh thiết bị từ mạng truyền thông |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (Bỏ qua (Bypass) tất cả các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (Kiểm tra lại bình thường (Check / Gỡ bỏ Bypass) toàn bộ điều kiện Khóa liên động và Cho phép), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_UpdDevInfo`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_UpdDevInfo (Yêu cầu đọc cập nhật thông tin định danh thiết bị từ mạng truyền thông), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo |
| **`ORdy_CV`** | `BOOL` | `0` | [Cờ cho phép nhập dữ liệu trên HMI] 1 = Sẵn sàng nhận giá trị đặt OSet_CV (Giá trị biến điều khiển đầu ra (Controlled Variable - CV, tính theo đơn vị kỹ thuật EU)), cho phép mở ô nhập số trên HMI Faceplate |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`OSet_CV`** | `REAL` | `0` | [Giá trị đặt từ Người vận hành HMI] Giá trị đặt từ Người vận hành (Operator HMI): Giá trị biến điều khiển đầu ra (Controlled Variable - CV, tính theo đơn vị kỹ thuật EU) |
| **`Val_DiagDesc1`** | `STRING_32` | `‘Logic not yet scanned.’` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi văn bản mô tả chẩn đoán thiết bị số #1 (tra cứu từ mã Val_DiagCode1) |
| **`Val_DiagDesc2`** | `STRING_32` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi văn bản mô tả chẩn đoán thiết bị số #2 (tra cứu từ mã Val_DiagCode2) |
| **`Val_DiagDesc3`** | `STRING_32` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi văn bản mô tả chẩn đoán thiết bị số #3 (tra cứu từ mã Val_DiagCode3) |
| **`Val_HART_CVEUMax`** | `REAL` | `100` | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận trên đơn vị kỹ thuật ngõ ra CV nhận được qua giao thức HART |
| **`Val_HART_CVEUMin`** | `REAL` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận dưới đơn vị kỹ thuật ngõ ra CV nhận được qua giao thức HART |
| **`Val_HART_CVRawMax`** | `REAL` | `100` | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận trên tín hiệu thô ngõ ra CV nhận được qua giao thức HART |
| **`Val_HART_CVRawMin`** | `REAL` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận dưới tín hiệu thô ngõ ra CV nhận được qua giao thức HART |
| **`Val_HART_Desc`** | `STRING_32` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi mô tả (Description Text) đọc trực tiếp từ thiết bị hiện trường qua giao thức HART |
| **`Val_HART_EU`** | `STRING_8` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi đơn vị kỹ thuật của giá trị tương tự đọc qua giao thức HART |
| **`Val_HART_FVEU`** | `STRING_8` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi đơn vị kỹ thuật của biến thứ tư FV đọc qua giao thức HART |
| **`Val_HART_Label`** | `STRING_20` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi nhãn hiển thị (Label Text) đọc trực tiếp từ thiết bị hiện trường qua giao thức HART |
| **`Val_HART_PVEU`** | `STRING_8` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi đơn vị kỹ thuật của biến sơ cấp PV đọc qua giao thức HART |
| **`Val_HART_SVEU`** | `STRING_8` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi đơn vị kỹ thuật của biến thứ cấp SV đọc qua giao thức HART |
| **`Val_HART_Tag`** | `STRING_20` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi tên thẻ (Tag Text) đọc trực tiếp từ bộ nhớ thiết bị hiện trường qua giao thức HART |
| **`Val_HART_TVEU`** | `STRING_8` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi đơn vị kỹ thuật của biến thứ ba TV đọc qua giao thức HART |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo hiện hành và trạng thái xác nhận của khối này (0 = Không có cảnh báo, 1 = Đã hết lỗi nhưng chờ Reset/Ack, 2..3 = Thấp, 4..5 = Trung bình, 6..7 = Cao, 8..9 = Khẩn cấp) |
