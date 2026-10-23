# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_Logic`
**Thuộc chương:** [Chương 5: Khối chức năng dùng chung, Liên động & Cảnh báo (Cross Functional)](../05_CHUONG_5_CROSS_FUNCTIONAL.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Cross Functional 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 391–396)  
**Tệp định nghĩa khối lệnh:** `P_Logic_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_Logic`:** **16** Biến đầu vào (`Input Parameters`) | **28** Biến đầu ra (`Output Parameters`) | **35** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_Logic` trong Studio 5000
- **Chức năng cốt lõi:** Khối **Mạch Logic Tổ hợp Khả trình kèm Chụp băng hình trạng thái (`Boolean Logic with Snapshot`)**. Nhận 8 đầu vào Boolean đi qua 8 cổng logic nối tầng (`AND`, `OR`, `XOR`, `Set/Reset Flip-Flop`, `Select 2:1`, `Majority 2oo3`) cấu hình trực tiếp trên HMI mà không cần sửa code PLC, đồng thời **chụp lại toàn bộ trạng thái 8 đầu vào kèm nhãn thời gian (`Snapshot`)** ngay tại mili-giây ngõ ra tác động.
- **Khi nào CẦN sử dụng:** 1. Khi cần tạo **mạch điều kiện liên động/khởi động phức tạp (như biểu quyết 2-trên-3 cảm biến, mạch chốt RS)** và muốn cho phép kỹ sư nhà máy chỉnh sửa logic trên HMI. 2. Khi cần **bắt lỗi chập chờn (Glitch / Intermittent Trip)** diễn ra trong vài mili-giây nhờ tính năng chụp băng hình `Snapshot`.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi chỉ cần gom các điều kiện liên động AND/OR phẳng thông thường → Dùng [`P_Intlk.md`](./P_Intlk.md) hoặc [`P_Perm.md`](./P_Perm.md).

### Quy trình 4 bước đấu nối và cấu hình `P_Logic`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_0…Inp_7`, `Inp_Hold`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_UseInpHold`, `Cfg_UsePCmd`, `Cfg_UseOCmd`, `Cfg_UseOut01`, `Cfg_UseOut10`, `Cfg_TSonSnap`, `Cfg_SnapOver`, `Cfg_HasMoreObj`, `Cfg_OnDelay`, `Cfg_OffDelay`
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_0StText`, `Cfg_1StText`, `Cfg_Area`, `Cfg_Desc`, `Cfg_GateFunc`, `Cfg_GateSrc1Inv`, `Cfg_GateSrc1Mask`, `Cfg_GateSrc1Ptr`, `Cfg_GateSrc2Inv`, `Cfg_GateSrc2Mask`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_Logic` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_Logic` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PCmd_Snap`, `PCmd_Reset`
   - **Cách sử dụng trong code PLC:** Gán giá trị đặt vào `PSet_*` (nếu có) hoặc kích xung 1 chu kỳ vào các bit lệnh `PCmd_* = 1` để xóa lỗi (`PCmd_Reset`), xác nhận cảnh báo (`PCmd_*Ack`) hoặc ức chế cảnh báo (`PCmd_*Suppress`). Khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi.
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Out_Live`, `Out_Snap`, `Val_DelayPctLive`, `Val_DelayPctSnap`, `Val_SnapInit`, `Sts_Snapped`, `Sts_InpLive`, `Sts_GateLive`, `Sts_InpSnap`, `Sts_GateSnap`, `Sts_GateSrc1Live`, `Sts_GateSrc2Live`, `Sts_GateSrc3Live`, `Sts_GateSrc4Live`, `Sts_GateSrc1Snap`, `Sts_GateSrc2Snap`, `Sts_GateSrc3Snap`, `Sts_GateSrc4Snap`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 16 Biến Đầu Vào (`Input Parameters`) của `P_Logic`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp_0…Inp_7`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 8 tín hiệu logic đầu vào (Inp_0 đến Inp_7) đưa vào các cổng logic tổ hợp của khối P_Logic |
| **`Inp_Hold`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Chân bắt sườn lên (0 -> 1) để chụp nhanh (Snapshot) toàn bộ trạng thái đầu vào và cổng logic của chu kỳ quét trước đó |
| **`Inp_Reset`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Cfg_UseInpHold`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép chụp băng hình trạng thái (Snapshot) khi chân Inp_Hold chuyển từ 0 lên 1 |
| **`Cfg_UsePCmd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép chụp băng hình trạng thái (Snapshot) khi Chương trình PLC kích xung PCmd_Snap |
| **`Cfg_UseOCmd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép chụp băng hình trạng thái (Snapshot) khi Người vận hành nhấn nút OCmd_Snap trên HMI |
| **`Cfg_UseOut01`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tự động chụp băng hình trạng thái (Snapshot) ngay tại khoảnh khắc ngõ ra Out_Live chuyển từ 0 lên 1 (sườn lên) |
| **`Cfg_UseOut10`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tự động chụp băng hình trạng thái (Snapshot) ngay tại khoảnh khắc ngõ ra Out_Live chuyển từ 1 về 0 (sườn xuống) |
| **`Cfg_TSonSnap`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tự động ghi lại nhãn thời gian thực (Timestamp) tại khoảnh khắc xảy ra chụp băng hình Snapshot |
| **`Cfg_SnapOver`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Lần chụp băng hình (Snapshot) mới sẽ ghi đè tự động lên lần chụp cũ; 0 = Giữ cố định băng hình đầu tiên cho đến khi nhấn Reset |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_OnDelay`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động Bật ngõ ra (Output ON Delay Time, tính bằng giây) |
| **`Cfg_OffDelay`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả Tắt ngõ ra (Output OFF Delay Time, tính bằng giây) |
| **`PCmd_Snap`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chụp nhanh (Snapshot) toàn bộ trạng thái đầu vào và cổng logic tại thời điểm hiện tại |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |

## 3. Bảng chi tiết 28 Biến Đầu Ra (`Output Parameters`) của `P_Logic`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Out_Live`** | `BOOL` | `??` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Kết quả đầu ra trực tiếp (Live Output) của mạch logic tổ hợp P_Logic sau bộ định thời trễ |
| **`Out_Snap`** | `BOOL` | `??` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Trạng thái đầu ra của mạch logic tổ hợp P_Logic tại thời điểm chụp băng hình (Snapshot) |
| **`Val_DelayPctLive`** | `DINT` | `??` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Phần trăm hoàn thành bộ định thời trễ Bật/Tắt (On-Delay / Off-Delay) trực tiếp hiện tại (0..100%) |
| **`Val_DelayPctSnap`** | `DINT` | `??` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Phần trăm hoàn thành bộ định thời trễ Bật/Tắt tại thời điểm chụp băng hình (Snapshot) |
| **`Val_SnapInit`** | `DINT` | `??` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã nguyên nhân kích hoạt chụp băng hình Snapshot: 1 = Lệnh OCmd từ HMI, 2 = Lệnh PCmd từ PLC, 3 = Ngõ ra chuyển 0->1, 4 = Ngõ ra chuyển 1->0, 5 = Tín hiệu Inp_Hold |
| **`Sts_Snapped`** | `BOOL` | `??` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đã kích hoạt chụp băng hình (Snapshot) lưu lại trạng thái; 0 = Đang hiển thị trạng thái trực tiếp (Live) |
| **`Sts_InpLive`** | `SINT` | `2#0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit (.0 đến .7) biểu thị trạng thái trực tiếp (Live) của 8 đầu vào logic Inp_0..Inp_7 |
| **`Sts_GateLive`** | `SINT` | `2#0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit (.0 đến .7) biểu thị kết quả trực tiếp (Live) của 8 cổng logic Gate 0..Gate 7 |
| **`Sts_InpSnap`** | `SINT` | `2#0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit (.0 đến .7) lưu trạng thái chụp băng hình (Snapshot) của 8 đầu vào logic Inp_0..Inp_7 |
| **`Sts_GateSnap`** | `SINT` | `2#0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit (.0 đến .7) lưu kết quả chụp băng hình (Snapshot) của 8 cổng logic Gate 0..Gate 7 |
| **`Sts_GateSrc1Live`** | `SINT` | `2#0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit biểu thị trạng thái dây nối trực tiếp (Live) cho Ngõ vào #1 của từng cổng logic (bit thứ m tương ứng cổng m) |
| **`Sts_GateSrc2Live`** | `SINT` | `2#0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit biểu thị trạng thái dây nối trực tiếp (Live) cho Ngõ vào #2 của từng cổng logic (bit thứ m tương ứng cổng m) |
| **`Sts_GateSrc3Live`** | `SINT` | `2#0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit biểu thị trạng thái dây nối trực tiếp (Live) cho Ngõ vào #3 của từng cổng logic (bit thứ m tương ứng cổng m) |
| **`Sts_GateSrc4Live`** | `SINT` | `2#0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit biểu thị trạng thái dây nối trực tiếp (Live) cho Ngõ vào #4 của từng cổng logic (bit thứ m tương ứng cổng m) |
| **`Sts_GateSrc1Snap`** | `SINT` | `2#0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit lưu trạng thái chụp băng hình (Snapshot) cho Ngõ vào #1 của từng cổng logic (bit thứ m tương ứng cổng m) |
| **`Sts_GateSrc2Snap`** | `SINT` | `2#0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit lưu trạng thái chụp băng hình (Snapshot) cho Ngõ vào #2 của từng cổng logic (bit thứ m tương ứng cổng m) |
| **`Sts_GateSrc3Snap`** | `SINT` | `2#0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit lưu trạng thái chụp băng hình (Snapshot) cho Ngõ vào #3 của từng cổng logic (bit thứ m tương ứng cổng m) |
| **`Sts_GateSrc4Snap`** | `SINT` | `2#0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit lưu trạng thái chụp băng hình (Snapshot) cho Ngõ vào #4 của từng cổng logic (bit thứ m tương ứng cổng m) |
| **`Sts_OutInvLive`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái trực tiếp (Live) của ngõ ra sau cổng đảo nhưng trước bộ định thời TON/TOF |
| **`Sts_OutInvSnap`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái chụp băng hình (Snapshot) của ngõ ra sau cổng đảo nhưng trước bộ định thời TON/TOF |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_GateFunc`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Mã hàm logic của cổng (Cfg_GateFunc) nằm ngoài phạm vi hợp lệ (chỉ dùng 0..6) |
| **`Err_GateSrcPtr`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Con trỏ chọn nguồn đầu vào cổng logic (Cfg_GateSrcPtr) nằm ngoài phạm vi 0..15 |
| **`Err_GateSrcMask`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Mặt nạ chọn nguồn đầu vào cổng logic (Cfg_GateSrcMask) không hợp lệ |
| **`Err_OutSrcPtr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Con trỏ chọn nguồn ngõ ra (Cfg_OutSrcPtr) nằm ngoài phạm vi 0..15 |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị đặt bộ định thời gian (Timer Preset) nằm ngoài phạm vi hợp lệ 0..2.147.483 giây |
| **`P_Logic`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_Logic) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 35 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_Logic`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_0StText`** | `STRING_8` | `'OK'` | [Biến cấu hình thông số] Chuỗi văn bản hiển thị trên HMI khi trạng thái bằng 0 (ví dụ: "Closed", "Stopped", "Normal") |
| **`Cfg_1StText`** | `STRING_8` | `'TRIPPED'` | [Biến cấu hình thông số] Chuỗi văn bản hiển thị trên HMI khi trạng thái bằng 1 (ví dụ: "Open", "Running", "Tripped") |
| **`Cfg_Area`** | `STRING_8` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'Configurable Boolean Logic Block'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_GateFunc`** | `DINT[8]` | `{...}` | [Biến cấu hình thông số] Từ cấu hình mã hàm logic cho 8 cổng (Gate 0..7): 0 = Tắt, 1 = AND, 2 = OR, 3 = XOR, 4 = Biểu quyết đa số (Majority 2oo3), 5 = Chốt Set-Reset Flip-Flop, 6 = Bộ chọn 2:1 (Multiplexer) |
| **`Cfg_GateSrc1Inv`** | `SINT` | `2#0000_0000` | [Biến cấu hình thông số] Từ bit (8-bit cho 8 cổng) cấu hình đảo mức logic cho Ngõ vào #1 của từng cổng (1 = Đảo tín hiệu NOT) |
| **`Cfg_GateSrc1Mask`** | `SINT` | `2#0000_0000` | [Biến cấu hình thông số] Từ bit (8-bit cho 8 cổng) xác định Ngõ vào #1 có được sử dụng trong phép tính của cổng logic hay không (1 = Có dùng) |
| **`Cfg_GateSrc1Ptr`** | `DINT[8]` | `{...}` | [Biến cấu hình thông số] Con trỏ chọn tín hiệu đưa vào Ngõ vào #1 của từng cổng logic (0..7 = Đầu vào Inp_0..7, 8..15 = Ngõ ra cổng Gate 0..7) |
| **`Cfg_GateSrc2Inv`** | `SINT` | `2#0000_0000` | [Biến cấu hình thông số] Từ bit (8-bit cho 8 cổng) cấu hình đảo mức logic cho Ngõ vào #2 của từng cổng (1 = Đảo tín hiệu NOT) |
| **`Cfg_GateSrc2Mask`** | `SINT` | `2#0000_0000` | [Biến cấu hình thông số] Từ bit (8-bit cho 8 cổng) xác định Ngõ vào #2 có được sử dụng trong phép tính của cổng logic hay không (1 = Có dùng) |
| **`Cfg_GateSrc2Ptr`** | `DINT[8]` | `{...}` | [Biến cấu hình thông số] Con trỏ chọn tín hiệu đưa vào Ngõ vào #2 của từng cổng logic (0..7 = Đầu vào Inp_0..7, 8..15 = Ngõ ra cổng Gate 0..7) |
| **`Cfg_GateSrc3Inv`** | `SINT` | `2#0000_0000` | [Biến cấu hình thông số] Từ bit (8-bit cho 8 cổng) cấu hình đảo mức logic cho Ngõ vào #3 của từng cổng (1 = Đảo tín hiệu NOT) |
| **`Cfg_GateSrc3Mask`** | `SINT` | `2#0000_0000` | [Biến cấu hình thông số] Từ bit (8-bit cho 8 cổng) xác định Ngõ vào #3 có được sử dụng trong phép tính của cổng logic hay không (1 = Có dùng) |
| **`Cfg_GateSrc3Ptr`** | `DINT[8]` | `{...}` | [Biến cấu hình thông số] Con trỏ chọn tín hiệu đưa vào Ngõ vào #3 của từng cổng logic (0..7 = Đầu vào Inp_0..7, 8..15 = Ngõ ra cổng Gate 0..7) |
| **`Cfg_GateSrc4Inv`** | `SINT` | `2#0000_0000` | [Biến cấu hình thông số] Từ bit (8-bit cho 8 cổng) cấu hình đảo mức logic cho Ngõ vào #4 của từng cổng (1 = Đảo tín hiệu NOT) |
| **`Cfg_GateSrc4Mask`** | `SINT` | `2#0000_0000` | [Biến cấu hình thông số] Từ bit (8-bit cho 8 cổng) xác định Ngõ vào #4 có được sử dụng trong phép tính của cổng logic hay không (1 = Có dùng) |
| **`Cfg_GateSrc4Ptr`** | `DINT[8]` | `{...}` | [Biến cấu hình thông số] Con trỏ chọn tín hiệu đưa vào Ngõ vào #4 của từng cổng logic (0..7 = Đầu vào Inp_0..7, 8..15 = Ngõ ra cổng Gate 0..7) |
| **`Cfg_HasNav`** | `SINT` | `2#0000_0000` | [Biến cấu hình thông số] Từ bit (16-bit) bật nút điều hướng nhảy tới Faceplate nguyên nhân cho từng điều kiện trong 16 kênh (Bit = 1 là bật) |
| **`Cfg_InpTxt`** | `STRING_20[8]` | `{...}` | [Biến cấu hình thông số] Mảng 8 chuỗi văn bản mô tả tên của 8 tín hiệu đầu vào (Inp_0..7) trên màn hình HMI của khối P_Logic |
| **`Cfg_Label`** | `STRING_20` | `'Configurable Logic'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_NavTag`** | `STRING_NavTag[8]` | `{...}` | [Biến cấu hình thông số] Mảng 16 tên thẻ (Tagname) đích tương ứng với 16 nút điều hướng điều kiện liên động/cho phép trên HMI |
| **`Cfg_OutSrcInv`** | `BOOL` | `0` | [Biến cấu hình thông số] 1 = Đảo ngược mức logic của tín hiệu ngõ ra khối P_Logic (trước bộ định thời trễ) |
| **`Cfg_OutSrcPtr`** | `DINT` | `0` | [Biến cấu hình thông số] Con trỏ chọn tín hiệu đưa ra ngõ ra cuối cùng của khối P_Logic (0..7 = Lấy từ đầu vào Inp_0..7; 8..15 = Lấy từ ngõ ra cổng Gate 0..7) |
| **`Cfg_Tag`** | `STRING_20` | `'P_Logic'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`HMI_Const`** | `SINT[9]` | `{...}` | Cấu trúc hằng số nội bộ phục vụ truy xuất gián tiếp các tham số trên màn hình HMI |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_Logic'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`OCmd_Snap`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Chụp nhanh (Snapshot) toàn bộ trạng thái đầu vào và cổng logic tại thời điểm hiện tại |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Snap`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Snap (Chụp nhanh (Snapshot) toàn bộ trạng thái đầu vào và cổng logic tại thời điểm hiện tại), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`Val_LastGate`** | `DINT[8]` | `{...}` | [Giá trị thực tế đầu ra (Logic/HMI)] Chỉ số cổng logic cuối cùng có sử dụng kết quả của cổng này (0 = Không dùng, 1..8 = Cổng, 33 = Ngõ ra — dùng để vẽ đường dây trên HMI) |
| **`Val_LastInp`** | `DINT[8]` | `{...}` | [Giá trị thực tế đầu ra (Logic/HMI)] Chỉ số cổng logic cuối cùng có sử dụng chân đầu vào này (0 = Không dùng, 1..8 = Cổng 1..8, 33 = Ngõ ra — dùng để vẽ đường dây trên HMI) |
| **`Val_SnapTS`** | `DINT[7]` | `{...}` | [Giá trị thực tế đầu ra (Logic/HMI)] Nhãn thời gian (Timestamp) lúc chụp băng hình Snapshot: [0]=Năm, [1]=Tháng, [2]=Ngày, [3]=Giờ, [4]=Phút, [5]=Giây, [6]=Micro-giây |
