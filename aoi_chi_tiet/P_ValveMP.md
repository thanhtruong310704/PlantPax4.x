# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_ValveMP`
**Thuộc chương:** [Chương 4: Van Điều khiển & Cơ cấu Chấp hành (Valves)](../04_CHUONG_4_VALVES.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Valves 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 300–305)  
**Tệp định nghĩa khối lệnh:** `P_ValveMP_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_ValveMP`:** **88** Biến đầu vào (`Input Parameters`) | **77** Biến đầu ra (`Output Parameters`) | **38** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_ValveMP` trong Studio 5000
- **Chức năng cốt lõi:** Khối điều khiển **Van Chống lẫn / Chống nhiễm chéo 2 đế tiêu chuẩn (Mix-Proof Valve)**. Quản lý 7 trạng thái vận hành: Đóng (`Closed`), Mở (`Opened`), Nâng đế trên (`Lift Upper`), Nâng đế dưới (`Lift Lower`), Rửa khoang rò rỉ (`CIP Cavity`), Rửa đế trên/dưới và hỗ trợ nhấp xung rửa đế (**Seat Pulsing**).
- **Khi nào CẦN sử dụng:** Dùng trong các nhà máy **Sữa, Bia, Nước giải khát, Thực phẩm, Dược phẩm** sử dụng van chống nhiễm chéo 2 đế chuẩn (Alfa Laval Unique, GEA Tuchenhagen Varivent, Pentair Südmo) có tới 3 cuộn Solenoid và 2–4 phản hồi.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi van Mix-Proof có tới 4–6 cuộn Solenoid hoặc cần khóa liên động tách rời cho từng chế độ nâng đế → Dùng [`P_ValveMPAdv.md`](./P_ValveMPAdv.md).

### Quy trình 4 bước đấu nối và cấu hình `P_ValveMP`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_OpenLS`, `Inp_ClosedLS`, `Inp_LowerLS`, `Inp_UpperLS`, `Inp_CavityInLS`, `Inp_CavityOutLS`, `Inp_PermOK`, `Inp_NBPermOK`, `Inp_IntlkOK`, `Inp_NBIntlkOK`, `Inp_IntlkAvail`, `Inp_IOFault`, `Inp_Sim`, `Inp_Hand`, `Inp_Ovrd`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_HasLiftLower`, `Cfg_HasLiftUpper`, `Cfg_HasSIPCavity`, `Cfg_HasSIPLower`, `Cfg_HasSIPUpper`, `Cfg_PulseLiftLower`, `Cfg_PulseLiftUpper`, `Cfg_PulseSIPLower`, `Cfg_PulseSIPUpper`, `Cfg_HasMoreObj`, `Cfg_HasPermObj`, `Cfg_HasIntlkObj`, `Cfg_HasStatsObj`, `Cfg_OCmdResets`, `Cfg_XCmdResets`, `Cfg_OvrdPermIntlk`, `Cfg_ShedOnFail`, `Cfg_ShedOnIOFault`...
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_Desc`, `Cfg_FdbkStateTbl`, `Cfg_FdbkTimeTbl`, `Cfg_Label`, `Cfg_OutStateTbl`, `Cfg_FdbkReqdTbl`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_ValveMP` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_ValveMP` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PSet_Owner`, `PCmd_Close`, `PCmd_Open`, `PCmd_LiftLower`, `PCmd_LiftUpper`, `PCmd_SIPCavity`, `PCmd_SIPLower`, `PCmd_SIPUpper`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_FailAck`, `PCmd_IntlkTripAck`, `PCmd_IOFaultAck`, `PCmd_FailSuppress`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh điều khiển tự động, kiểm tra khối đã sẵn sàng ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển quyền về Program, sau đó gán giá trị vào `PSet_*` và kích xung `PCmd_* = 1` (khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Out_Open`, `Out_Close`, `Out_LiftLower`, `Out_LiftUpper`, `Out_CavityInlet`, `Out_CavityOutlet`, `Val_Cmd`, `Val_Sts`, `Val_Fault`, `Val_State`, `Val_NotifyAll`, `Val_UnackAlmC`, `Val_Owner`, `Sts_Closed`, `Sts_Opened`, `Sts_LiftLower`, `Sts_LiftUpper`, `Sts_SIPCavity`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 88 Biến Đầu Vào (`Input Parameters`) của `P_ValveMP`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp_OpenLS`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu công tắc hành trình báo van đã Mở hoàn toàn (Open Limit Switch: 1 = Xác nhận van đang MỞ) |
| **`Inp_ClosedLS`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu công tắc hành trình báo van đã Đóng hoàn toàn (Closed Limit Switch: 1 = Xác nhận van đang ĐÓNG) |
| **`Inp_LowerLS`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu công tắc hành trình báo đế van dưới (Lower Seat) đã nâng lên |
| **`Inp_UpperLS`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu công tắc hành trình báo đế van trên (Upper Seat) đã nâng lên |
| **`Inp_CavityInLS`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu công tắc hành trình của van cấp vào khoang chống rò (Cavity Inlet Valve LS) |
| **`Inp_CavityOutLS`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu công tắc hành trình của van xả khoang chống rò (Cavity Outlet Valve LS) |
| **`Inp_PermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp điều kiện Cho phép khởi động có thể Bypass (1 = Tất cả điều kiện Perm OK, cho phép khởi động; thường nối từ P_Perm.Sts_PermOK) |
| **`Inp_NBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp điều kiện Cho phép khởi động KHÔNG thể Bypass (1 = Tất cả điều kiện Non-Bypassable Perm OK; thường nối từ P_Perm.Sts_NBPermOK) |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp Khóa liên động an toàn có thể Bypass (1 = Liên động OK cho phép chạy; 0 = Cắt dừng thiết bị ngay lập tức; nối từ P_Intlk.Sts_IntlkOK) |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp Khóa liên động an toàn KHÔNG thể Bypass (1 = Liên động bất khả kháng OK; 0 = Cắt dừng thiết bị ngay lập tức; nối từ P_Intlk.Sts_NBIntlkOK) |
| **`Inp_IntlkAvail`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu báo điều kiện Khóa liên động sẵn sàng cho phép gọi máy trong nhóm (1 = Interlock Availability OK) |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi truyền thông I/O của thiết bị (0 = Tốt/OK, 1 = Lỗi truyền thông I/O Fault) |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân kích hoạt chế độ Mô phỏng (Simulation): Khi = 1, khóa các ngõ ra vật lý ở mức an toàn và dùng giá trị/phản hồi giả lập nội bộ; khi = 0, vận hành thực tế bình thường |
| **`Inp_Hand`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu chọn chế độ Điều khiển bằng tay tại chỗ (Hand/Local): 1 = Chiếm quyền sang chế độ Hand (thường nối từ công tắc cứng tại tủ MCC); 0 = Nhả chế độ Hand |
| **`Inp_Ovrd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu kích hoạt chế độ Cưỡng bức ưu tiên (Override): 1 = Chiếm quyền sang chế độ Override; 0 = Nhả chế độ Override |
| **`Inp_OvrdCmd`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã lệnh van chống lẫn khi ở chế độ Cưỡng bức (Override): 0 = Không tác động, 1 = Đóng van (Closed), 2 = Mở van (Open), 3 = Nâng đế dưới (Lift Lower), 4 = Nâng đế trên (Lift Upper), 5 = Rửa khoang van (SIP/CIP Cavity), 6 = Rửa đế dưới (SIP/CIP Lower Seat), 7 = Rửa đế trên (SIP/CIP Upper Seat) |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân khóa cấm nguồn lệnh ngoài (External Inhibit): 1 = Cấm chế độ External chiếm quyền điều khiển; 0 = Cho phép External chiếm quyền |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Cfg_HasLiftLower`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Van chống lẫn (Mix-Proof) có hỗ trợ trạng thái Nâng đế van dưới (Lift Lower Seat) |
| **`Cfg_HasLiftUpper`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Van chống lẫn (Mix-Proof) có hỗ trợ trạng thái Nâng đế van trên (Lift Upper Seat) |
| **`Cfg_HasSIPCavity`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Van chống lẫn có hỗ trợ trạng thái Tiệt trùng hơi nước khoang van (SIP Cavity) |
| **`Cfg_HasSIPLower`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Van chống lẫn có hỗ trợ trạng thái Tiệt trùng hơi nước đế van dưới (SIP Lower Seat) |
| **`Cfg_HasSIPUpper`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Van chống lẫn có hỗ trợ trạng thái Tiệt trùng hơi nước đế van trên (SIP Upper Seat) |
| **`Cfg_PulseLiftLower`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Băm xung ngõ ra nâng đế dưới (Pulse Seat Lift) khi rửa đế dưới; 0 = Giữ mở liên tục |
| **`Cfg_PulseLiftUpper`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Băm xung ngõ ra nâng đế trên (Pulse Seat Lift) khi rửa đế trên; 0 = Giữ mở liên tục |
| **`Cfg_PulseSIPLower`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Băm xung ngõ ra nâng đế dưới khi thực hiện SIP/CIP đế dưới |
| **`Cfg_PulseSIPUpper`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Băm xung ngõ ra nâng đế trên khi thực hiện SIP/CIP đế trên |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_HasPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Điều kiện cho phép (P_Perm) nối vào chân Inp_PermOK và bật nút điều hướng mở Faceplate P_Perm |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Khóa liên động (P_Intlk) nối vào chân Inp_IntlkOK và bật nút điều hướng mở Faceplate P_Intlk |
| **`Cfg_HasStatsObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Thống kê hành trình van (P_ValveStats) đi kèm và bật nút điều hướng mở Faceplate |
| **`Cfg_OCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khi Người vận hành ra lệnh Chạy/Mở mới (OCmd_*), khối tự động xóa luôn các chốt lỗi cũ; 0 = Bắt buộc phải nhấn nút Reset trước khi ra lệnh mới |
| **`Cfg_XCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khi hệ thống ngoài gửi lệnh Chạy/Mở mới (XCmd_*), khối tự động xóa luôn các chốt lỗi cũ; 0 = Bắt buộc phải gửi xung XCmd_Reset trước |
| **`Cfg_OvrdPermIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Chế độ Cưỡng bức (Override) được quyền bỏ qua các điều kiện Cho phép và Liên động có thể bypass; 0 = Chế độ Override vẫn phải tuân thủ mọi điều kiện |
| **`Cfg_ShedOnFail`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Đưa thiết bị về trạng thái Tắt/An toàn và phát báo động khi lỗi không đạt trạng thái mong muốn; 0 = Chỉ phát báo động |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt dừng thiết bị về trạng thái an toàn (Shed) và phát báo động khi lỗi truyền thông thẻ I/O; 0 = Chỉ phát báo động |
| **`Cfg_HasFailAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_FailResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_FailAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_FailSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_PulseOpenT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian giữ mở nhấc đế van trong chu kỳ băm xung rửa đế (giây) |
| **`Cfg_PulseCloseT`** | `REAL` | `5` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian đóng hạ đế van trong chu kỳ băm xung rửa đế (giây) |
| **`Cfg_FailT`** | `REAL` | `3` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian chờ tối đa (giây) sau khi ra lệnh chuyển trạng thái để thiết bị đạt tới trạng thái phản hồi mới trước khi báo lỗi |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Mã định danh Chủ sở hữu (Owner ID: ghi khác 0 để đăng ký chiếm quyền điều khiển, ghi 0 để nhả quyền) |
| **`PCmd_Close`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Đóng van (Close Valve) |
| **`PCmd_Open`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Mở van (Open Valve) |
| **`PCmd_LiftLower`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Nâng đế van dưới để vệ sinh CIP (Lift Lower Seat) |
| **`PCmd_LiftUpper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Nâng đế van trên để vệ sinh CIP (Lift Upper Seat) |
| **`PCmd_SIPCavity`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Tiệt trùng hơi nước (SIP) khoang chống rò của van |
| **`PCmd_SIPLower`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Tiệt trùng hơi nước (SIP) qua đế van dưới |
| **`PCmd_SIPUpper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Tiệt trùng hơi nước (SIP) qua đế van trên |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Chương trình tự động (từ Operator sang Program) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Người vận hành (từ Program sang Operator) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa giữ nguồn lệnh hiện tại (không cho phép nguồn lệnh kia giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Đưa nguồn lệnh về chế độ Bình thường mặc định (Normal: Program hoặc Operator theo Cfg_ProgNormal) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`PCmd_FailAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_FailSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) ngay lập tức (biến xung tự động xóa về 0) |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`XCmd_Close`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Đóng van (Close Valve) |
| **`XCmd_Open`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Mở van (Open Valve) |
| **`XCmd_LiftLower`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nâng đế van dưới để vệ sinh CIP (Lift Lower Seat) |
| **`XCmd_LiftUpper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nâng đế van trên để vệ sinh CIP (Lift Upper Seat) |
| **`XCmd_SIPCavity`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Tiệt trùng hơi nước (SIP) khoang chống rò của van |
| **`XCmd_SIPLower`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Tiệt trùng hơi nước (SIP) qua đế van dưới |
| **`XCmd_SIPUpper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Tiệt trùng hơi nước (SIP) qua đế van trên |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Chiếm quyền điều khiển (Acquire) về chế độ tương ứng |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |

## 3. Bảng chi tiết 77 Biến Đầu Ra (`Output Parameters`) của `P_ValveMP`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Out_Open`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra điều khiển cơ cấu chấp hành Mở van (1 = Cấp lệnh Mở van, 0 = Ngắt lệnh) |
| **`Out_Close`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra điều khiển cơ cấu chấp hành Đóng van (1 = Cấp lệnh Đóng van, 0 = Ngắt lệnh) |
| **`Out_LiftLower`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra điều khiển nâng đế van dưới (Lift Lower Seat) để rửa CIP |
| **`Out_LiftUpper`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra điều khiển nâng đế van trên (Lift Upper Seat) để rửa CIP |
| **`Out_CavityInlet`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra điều khiển van cấp vào khoang chống rò (Cavity Inlet Valve — thường là van thường đóng FC) |
| **`Out_CavityOutlet`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra điều khiển van xả khoang chống rò (Cavity Outlet Valve — thường là van thường mở FO) |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng dữ liệu tổng hợp cuối cùng (0 = Tín hiệu thực Tốt đã xác nhận, 1 = Tín hiệu thực Tốt giả định, 2 = Không cấu hình phản hồi/Tốt giả định, 8 = Đang mô phỏng, 9 = Mô phỏng vòng lặp, 10 = Giá trị nhập tay, 16 = Ngoài đặc tính kỹ thuật, 17 = Thay thế tại thiết bị/bus, 18 = Thay thế bởi Bảo trì, 19 = Ngắt bảo vệ giữ giá trị cũ, 20 = Ngắt bảo vệ dùng giá trị thay thế, 32 = Lỗi tín hiệu vượt dải/NaN, 33 = Lỗi kênh I/O, 34 = Lỗi module I/O, 35 = Lỗi cấu hình) |
| **`Val_Cmd`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã lệnh điều khiển thiết bị đang xuất ra (Device Command enum: 0 = Không tác động/Tắt, 1 = Dừng/Đóng/Off, 2 = Chạy/Mở/On...) |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái hoạt động xác nhận của thiết bị (Device Confirmed Status enum dùng để điều khiển màu sắc và biểu tượng động trên HMI) |
| **`Val_Fault`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái lỗi thiết bị (Device Fault Status — kiểu liệt kê enum: 0 = Không lỗi, 16 = Lỗi khởi động/mở, 17 = Lỗi dừng/đóng, 20 = Lo, 21 = Hi, 24 = LoLo, 25 = HiHi, 32 = Fail/I/O Fault, 34 = Lỗi cấu hình CfgErr) |
| **`Val_State`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái máy trạng thái nội bộ (Internal Logic State) dùng để tạo hiệu ứng chuyển động sơ đồ trạng thái trên HMI Faceplate |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận tổng hợp của khối này cộng với các đối tượng kênh/quá tải liên kết |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms Count) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã định danh Chủ sở hữu hiện hành của đối tượng (Current Object Owner ID: 0 = Đang không bị chiếm quyền) |
| **`Sts_Closed`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Van được yêu cầu đóng và đã xác nhận Đóng hoàn toàn (Confirmed Closed) |
| **`Sts_Opened`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Van được yêu cầu mở và đã xác nhận Mở hoàn toàn (Confirmed Open) |
| **`Sts_LiftLower`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Van chống lẫn đã đạt vị trí Nâng đế van dưới (Lower Seat Lift Achieved) để rửa CIP |
| **`Sts_LiftUpper`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Van chống lẫn đã đạt vị trí Nâng đế van trên (Upper Seat Lift Achieved) để rửa CIP |
| **`Sts_SIPCavity`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Van chống lẫn đã đạt vị trí Tiệt trùng hơi nước (SIP) khoang chống rò |
| **`Sts_SIPLower`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Van chống lẫn đã đạt vị trí Tiệt trùng hơi nước (SIP) qua đế van dưới |
| **`Sts_SIPUpper`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Van chống lẫn đã đạt vị trí Tiệt trùng hơi nước (SIP) qua đế van trên |
| **`Sts_Moving`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị đang trong quá trình chuyển trạng thái (chưa nhận được phản hồi xác nhận đã tới trạng thái đích) |
| **`Sts_Pulsing`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Ngõ ra đang trong chu trình phát xung (Single Pulse hoặc Continuous Pulse) |
| **`Sts_Available`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị sẵn sàng cho Chương trình tự động (Program) điều khiển (đang ở chế độ Program và không có lỗi/liên động ngắt) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass hiện đang được bỏ qua (Bypassed) |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Chế độ Bypass bảo trì đang kích hoạt (gửi sang khối P_Intlk / P_Perm để cho phép bỏ qua các điều kiện có quyền Bypass) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị chưa sẵn sàng hoạt động (kiểm tra các bit Nrdy_* chi tiết để biết nguyên nhân cụ thể) |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang có lỗi thiết bị ngắt bảo vệ (Shed — yêu cầu nhấn Reset) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Khóa liên động an toàn không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi truyền thông thẻ I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Nrdy_Perm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Cho phép khởi động không thỏa mãn (Permissive Not OK) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoặc cưỡng bức bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có ít nhất một cảnh báo bị Tạm hoãn (Shelved), Vô hiệu hóa (Disabled) hoặc Ức chế (Suppressed) — hiển thị biểu tượng chữ "I" trên HMI |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thời gian kiểm tra lỗi phản hồi không hợp lệ (phải nằm trong dải 0..2.147.483 giây) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị đặt bộ định thời gian (Timer Preset) nằm ngoài phạm vi hợp lệ 0..2.147.483 giây |
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
| **`Sts_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi tín hiệu / hỏng kênh đo (Signal Failure) |
| **`Alm_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi tín hiệu / hỏng kênh đo (Signal Failure) |
| **`Ack_Fail`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi tín hiệu / hỏng kênh đo (Signal Failure) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_FailDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_FailShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_FailSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
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
| **`P_ValveMP`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_ValveMP) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 38 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_ValveMP`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'Mix Proof Valve'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_FdbkStateTbl`** | `SINT[11]` | `{...}` | [Biến cấu hình thông số] Mảng bảng cấu hình mức logic phản hồi chuẩn cho từng chế độ của van P_ValveMPAdv |
| **`Cfg_FdbkTimeTbl`** | `REAL[11]` | `{...}` | [Biến cấu hình thông số] Mảng bảng thời gian xác nhận phản hồi cho từng chế độ của van P_ValveMPAdv (giây) |
| **`Cfg_Label`** | `STRING_20` | `'Valve Control'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_OutStateTbl`** | `SINT[11]` | `{...}` | [Biến cấu hình thông số] Mảng bảng cấu hình trạng thái các ngõ ra tương ứng với từng chế độ vận hành của van chống lẫn P_ValveMPAdv |
| **`Cfg_FdbkReqdTbl`** | `SINT[11]` | `{...}` | [Biến cấu hình thông số] Mảng bảng cấu hình mặt nạ các ngõ vào phản hồi bắt buộc kiểm tra cho từng chế độ của van P_ValveMPAdv |
| **`Cfg_Tag`** | `STRING_20` | `'P_ValveMP'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc nội bộ quản lý phân quyền và chuyển mạch giữa 7 nguồn lệnh điều khiển (OoS, Hand, Maint, Ovrd, Ext, Prog, Oper) |
| **`Fail`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi tín hiệu / hỏng kênh đo (Signal Failure) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_ValveMP'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`IOFault`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bỏ qua (Bypass) tất cả các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Kiểm tra lại bình thường (Check / Gỡ bỏ Bypass) toàn bộ điều kiện Khóa liên động và Cho phép |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (Bỏ qua (Bypass) tất cả các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (Kiểm tra lại bình thường (Check / Gỡ bỏ Bypass) toàn bộ điều kiện Khóa liên động và Cho phép), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`OCmd_Close`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Đóng van (Close Valve) |
| **`OCmd_LiftLower`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Nâng đế van dưới để vệ sinh CIP (Lift Lower Seat) |
| **`OCmd_LiftUpper`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Nâng đế van trên để vệ sinh CIP (Lift Upper Seat) |
| **`OCmd_Open`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Mở van (Open Valve) |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo |
| **`OCmd_SIPCavity`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Tiệt trùng hơi nước (SIP) khoang chống rò của van |
| **`OCmd_SIPLower`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Tiệt trùng hơi nước (SIP) qua đế van dưới |
| **`OCmd_SIPUpper`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Tiệt trùng hơi nước (SIP) qua đế van trên |
| **`ORdy_Close`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Close (Đóng van (Close Valve)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_LiftLower`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_LiftLower (Nâng đế van dưới để vệ sinh CIP (Lift Lower Seat)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_LiftUpper`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_LiftUpper (Nâng đế van trên để vệ sinh CIP (Lift Upper Seat)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Open`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Open (Mở van (Open Valve)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_SIPCavity`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_SIPCavity (Tiệt trùng hơi nước (SIP) khoang chống rò của van), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_SIPLower`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_SIPLower (Tiệt trùng hơi nước (SIP) qua đế van dưới), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_SIPUpper`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_SIPUpper (Tiệt trùng hơi nước (SIP) qua đế van trên), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo hiện hành và trạng thái xác nhận của khối này (0 = Không có cảnh báo, 1 = Đã hết lỗi nhưng chờ Reset/Ack, 2..3 = Thấp, 4..5 = Trung bình, 6..7 = Cao, 8..9 = Khẩn cấp) |
