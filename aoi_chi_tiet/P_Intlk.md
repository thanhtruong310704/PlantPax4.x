# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_Intlk`
**Thuộc chương:** [Chương 5: Khối chức năng dùng chung, Liên động & Cảnh báo (Cross Functional)](../05_CHUONG_5_CROSS_FUNCTIONAL.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Cross Functional 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 340–345)  
**Tệp định nghĩa khối lệnh:** `P_Intlk_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_Intlk`:** **11** Biến đầu vào (`Input Parameters`) | **8** Biến đầu ra (`Output Parameters`) | **13** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_Intlk` trong Studio 5000
- **Chức năng cốt lõi:** Khối **Khóa Liên động An toàn 16 đầu vào có Bắt lỗi đầu tiên (First Out) và Bypass bảo trì**. Giám sát 16 điều kiện `Inp_Intlk00..15` (cấu hình được mức 0 hoặc 1 là OK, cấu hình tự giữ Latch và quyền phép Bypass cho từng kênh). Khi vi phạm sẽ cắt thiết bị đang chạy ngay lập tức và chỉ rõ nguyên nhân đầu tiên gây dừng máy (`Val_FirstOut`).
- **Khi nào CẦN sử dụng:** Gắn kèm với **mọi Động cơ, Biến tần, Van, Bộ PID** để bảo vệ thiết bị và công nghệ (ví dụ: dừng bơm ngay khi mức bồn hút quá thấp `LoLo`, áp suất đẩy quá cao `HiHi`, hoặc nhiệt độ ổ bi quá cao). *(Đặt tên thẻ: `[Tên_Thiết_Bị]_Intlk`)*.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi điều kiện chỉ cần kiểm tra lúc bắt đầu khởi động mà không cần dừng máy khi đang chạy → Dùng [`P_Perm.md`](./P_Perm.md).

### Quy trình 4 bước đấu nối và cấu hình `P_Intlk`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_Intlk00…Inp_Intlk15`, `Inp_BypActive`, `Inp_LatchDefeat`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_HasMoreObj`, `Cfg_OKState`, `Cfg_Latched`, `Cfg_Bypassable`, `Cfg_HasNav`
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_CondTxt`, `Cfg_Desc`, `Cfg_Label`, `Cfg_NavTag`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_Intlk` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_Intlk` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PCmd_Reset`
   - **Cách sử dụng trong code PLC:** Gán giá trị đặt vào `PSet_*` (nếu có) hoặc kích xung 1 chu kỳ vào các bit lệnh `PCmd_* = 1` để xóa lỗi (`PCmd_Reset`), xác nhận cảnh báo (`PCmd_*Ack`) hoặc ức chế cảnh báo (`PCmd_*Suppress`). Khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi.
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Sts_IntlkOK`, `Sts_NBIntlkOK`, `Sts_BypActive`, `Sts_RdyReset`, `Sts_Intlk`, `Sts_FirstOut`
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 11 Biến Đầu Vào (`Input Parameters`) của `P_Intlk`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp_Intlk00…Inp_Intlk15`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 16 chân đầu vào điều kiện Khóa liên động an toàn (Inp_Intlk00..15): Nếu bất kỳ chân nào lệch khỏi trạng thái an toàn (Cfg_OKState) và không được Bypass, ngõ ra Sts_IntlkOK lập tức về 0 để dừng thiết bị về trạng thái an toàn |
| **`Inp_BypActive`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu báo thiết bị chính đang bật chế độ Bypass bảo trì (1 = Đang kích hoạt Bypass các điều kiện cho phép bypass; nối từ Sts_BypActive của khối thiết bị) |
| **`Inp_LatchDefeat`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tạm ngắt chức năng tự chốt giữ lỗi liên động khi thiết bị đang dừng (1 = Không chốt giữ lỗi để tìm lỗi dễ dàng khi máy dừng; nối từ Sts_Stopped/Sts_Closed) |
| **`Inp_Reset`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_OKState`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ cấu hình 16-bit quy định mức logic an toàn (0 hoặc 1) cho từng chân trong 16 điều kiện (Inp_00..15): Bit = 1 nghĩa là chân đó phải bằng 1 mới OK (kết nối thường đóng fail-safe); Bit = 0 nghĩa là chân đó phải bằng 0 mới OK |
| **`Cfg_Latched`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ cấu hình 16-bit chọn các điều kiện tự chốt giữ (Latch): Bit = 1 thì khi điều kiện đó vi phạm sẽ tự giữ chặt trạng thái lỗi cho đến khi nhấn Reset |
| **`Cfg_Bypassable`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ cấu hình 16-bit phân quyền Bypass bảo trì: Bit = 1 cho phép điều kiện tương ứng được bỏ qua khi bật Bypass; Bit = 0 là điều kiện an toàn bất khả kháng (Non-Bypassable) |
| **`Cfg_HasNav`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ bit (16-bit) bật nút điều hướng nhảy tới Faceplate nguyên nhân cho từng điều kiện trong 16 kênh (Bit = 1 là bật) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |

## 3. Bảng chi tiết 8 Biến Đầu Ra (`Output Parameters`) của `P_Intlk`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Sts_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái tổng hợp của toàn bộ điều kiện Khóa liên động (bao gồm các kênh được Bypass): 1 = Tất cả đều OK (cho phép chạy); 0 = Cắt dừng thiết bị ngay lập tức |
| **`Sts_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái tổng hợp của các điều kiện Khóa liên động KHÔNG thể Bypass: 1 = Tất cả đều OK; 0 = Cắt dừng thiết bị ngay lập tức |
| **`Sts_BypActive`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Chế độ Bypass bảo trì đang kích hoạt (gửi sang khối P_Intlk / P_Perm để cho phép bỏ qua các điều kiện có quyền Bypass) |
| **`Sts_RdyReset`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc chốt ngắt bảo vệ (Shed) đã hết lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_Intlk`** | `INT` | `2#0000_0000_0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit (16-bit) báo trạng thái vi phạm của từng kênh liên động riêng lẻ (Bit = 1 là kênh đó đang bị lỗi gây cắt máy; Bit = 0 là kênh đó đang OK) |
| **`Sts_FirstOut`** | `INT` | `2#0000_0000_0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit (16-bit) chỉ báo thủ phạm đầu tiên gây cắt liên động (First-Out Status: chỉ duy nhất bit của điều kiện bị mất đầu tiên lên 1) |
| **`P_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_Intlk) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 13 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_Intlk`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_8` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_CondTxt`** | `STRING_20[16]` | `{...}` | [Biến cấu hình thông số] Mảng 16 chuỗi văn bản mô tả tên của 16 điều kiện Khóa liên động / Cho phép hiển thị trên HMI Faceplate |
| **`Cfg_Desc`** | `STRING_40` | `'Interlocks with First Out'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_Label`** | `STRING_20` | `'Interlocks'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_NavTag`** | `STRING_NavTag[16]` | `{...}` | [Biến cấu hình thông số] Mảng 16 tên thẻ (Tagname) đích tương ứng với 16 nút điều hướng điều kiện liên động/cho phép trên HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_Intlk'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_Intlk'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`MSet_Bypass`** | `INT` | `2#0000_0000_0000_0000` | [Giá trị đặt từ Bảo trì HMI] Giá trị đặt từ Nhân viên bảo trì (Maintenance HMI): Từ bit (Bitmask) bật/tắt chế độ Bypass bảo trì cho từng điều kiện riêng lẻ (Bit = 1 là Bypass) |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`Val_FirstOutTxt`** | `STRING_Cond` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi văn bản mô tả điều kiện Khóa liên động đầu tiên gây cắt máy (First-Out Condition Text) |
