# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_IntlkAdv`
**Thuộc chương:** [Chương 5: Khối chức năng dùng chung, Liên động & Cảnh báo (Cross Functional)](../05_CHUONG_5_CROSS_FUNCTIONAL.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Cross Functional 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 346–352)  
**Tệp định nghĩa khối lệnh:** `P_IntlkAdv_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_IntlkAdv`:** **15** Biến đầu vào (`Input Parameters`) | **12** Biến đầu ra (`Output Parameters`) | **15** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_IntlkAdv` trong Studio 5000
- **Chức năng cốt lõi:** Khối **Khóa Liên động Nâng cao (Advanced Interlocks)**. Mở rộng từ `P_Intlk` với khả năng phân loại điều kiện liên động theo các nhóm cấp độ ưu tiên dừng (**Stop Priority / Shed Levels**) và điều hướng trạng thái an toàn linh hoạt hơn.
- **Khi nào CẦN sử dụng:** Khi thiết bị chấp hành có **nhiều kiểu dừng an toàn khác nhau** tùy theo mức độ sự cố (ví dụ: liên động cấp 1 thì giảm tốc dừng êm *Coast/Ramp Stop*, còn liên động khẩn cấp cấp 0 thì cắt điện phanh gấp ngay lập tức).
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi mọi điều kiện liên động đều tác động dừng máy giống nhau → Dùng [`P_Intlk.md`](./P_Intlk.md) cho gọn nhẹ.

### Quy trình 4 bước đấu nối và cấu hình `P_IntlkAdv`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_Intlk00…Inp_Intlk15`, `Inp_IOFault`, `Inp_Avail`, `Inp_BypActive`, `Inp_LatchDefeat`, `Inp_Reset`
   - **Các chân tham chiếu dữ liệu (`Ref_*` / `InOut`):** `Ref_Inputs` — bắt buộc gán thẻ mảng/cấu trúc tương ứng trong Controller Tags.
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_HasMoreObj`, `Cfg_OKState`, `Cfg_Latched`, `Cfg_Bypassable`, `Cfg_HasNav`, `Cfg_Type0…Cfg_Type15`, `Cfg_HasType`
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_CondTxt`, `Cfg_Desc`, `Cfg_Label`, `Cfg_NavTag`, `Cfg_Tag`, `Cfg_TypeDesc`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_IntlkAdv` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_IntlkAdv` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PCmd_Reset`
   - **Cách sử dụng trong code PLC:** Gán giá trị đặt vào `PSet_*` (nếu có) hoặc kích xung 1 chu kỳ vào các bit lệnh `PCmd_* = 1` để xóa lỗi (`PCmd_Reset`), xác nhận cảnh báo (`PCmd_*Ack`) hoặc ức chế cảnh báo (`PCmd_*Suppress`). Khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi.
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Sts_IntlkOK`, `Sts_NBIntlkOK`, `Sts_BypActive`, `Sts_RdyReset`, `Sts_LatchMask`, `Sts_BypassMask`, `Sts_Intlk`, `Sts_Avail`, `Sts_FirstOut`
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 15 Biến Đầu Vào (`Input Parameters`) của `P_IntlkAdv`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp_Intlk00…Inp_Intlk15`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 16 chân đầu vào điều kiện Khóa liên động an toàn (Inp_Intlk00..15): Nếu bất kỳ chân nào lệch khỏi trạng thái an toàn (Cfg_OKState) và không được Bypass, ngõ ra Sts_IntlkOK lập tức về 0 để dừng thiết bị về trạng thái an toàn |
| **`Inp_IOFault`** | `INT` | `2#0000_0000_0000_0000` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi truyền thông I/O của thiết bị (0 = Tốt/OK, 1 = Lỗi truyền thông I/O Fault) |
| **`Inp_Avail`** | `BOOL` | `2#1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu đầu vào kiểm tra tính sẵn sàng của thiết bị đối với các điều kiện liên động |
| **`Inp_BypActive`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu báo thiết bị chính đang bật chế độ Bypass bảo trì (1 = Đang kích hoạt Bypass các điều kiện cho phép bypass; nối từ Sts_BypActive của khối thiết bị) |
| **`Inp_LatchDefeat`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tạm ngắt chức năng tự chốt giữ lỗi liên động khi thiết bị đang dừng (1 = Không chốt giữ lỗi để tìm lỗi dễ dàng khi máy dừng; nối từ Sts_Stopped/Sts_Closed) |
| **`Inp_Reset`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_OKState`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ cấu hình 16-bit quy định mức logic an toàn (0 hoặc 1) cho từng chân trong 16 điều kiện (Inp_00..15): Bit = 1 nghĩa là chân đó phải bằng 1 mới OK (kết nối thường đóng fail-safe); Bit = 0 nghĩa là chân đó phải bằng 0 mới OK |
| **`Cfg_Latched`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ cấu hình 16-bit chọn các điều kiện tự chốt giữ (Latch): Bit = 1 thì khi điều kiện đó vi phạm sẽ tự giữ chặt trạng thái lỗi cho đến khi nhấn Reset |
| **`Cfg_Bypassable`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ cấu hình 16-bit phân quyền Bypass bảo trì: Bit = 1 cho phép điều kiện tương ứng được bỏ qua khi bật Bypass; Bit = 0 là điều kiện an toàn bất khả kháng (Non-Bypassable) |
| **`Cfg_HasNav`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ bit (16-bit) bật nút điều hướng nhảy tới Faceplate nguyên nhân cho từng điều kiện trong 16 kênh (Bit = 1 là bật) |
| **`Cfg_Type0…Cfg_Type15`** | `SINT` | `6` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình kiểu đối tượng mở rộng cho 16 kênh điều kiện liên động (00..15) để điều hướng trên HMI |
| **`Cfg_HasType`** | `SINT` | `2#1111_1111` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng thông tin mở rộng đi kèm cho từng điều kiện |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |

## 3. Bảng chi tiết 12 Biến Đầu Ra (`Output Parameters`) của `P_IntlkAdv`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Ref_Inputs`** | `INT` | `0` | Ẩn (Tag) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới mảng cấu hình các điều kiện đầu vào của khối liên động nâng cao |
| **`Sts_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái tổng hợp của toàn bộ điều kiện Khóa liên động (bao gồm các kênh được Bypass): 1 = Tất cả đều OK (cho phép chạy); 0 = Cắt dừng thiết bị ngay lập tức |
| **`Sts_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái tổng hợp của các điều kiện Khóa liên động KHÔNG thể Bypass: 1 = Tất cả đều OK; 0 = Cắt dừng thiết bị ngay lập tức |
| **`Sts_BypActive`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Chế độ Bypass bảo trì đang kích hoạt (gửi sang khối P_Intlk / P_Perm để cho phép bỏ qua các điều kiện có quyền Bypass) |
| **`Sts_RdyReset`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc chốt ngắt bảo vệ (Shed) đã hết lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_LatchMask`** | `INT` | `2#0000_0000_0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit (Bitmask) phản ánh các điều kiện được cấu hình tự chốt giữ (Latched) |
| **`Sts_BypassMask`** | `INT` | `2#0000_0000_0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit (Bitmask) phản ánh các điều kiện hiện đang được bật Bypass |
| **`Sts_Intlk`** | `INT` | `2#0000_0000_0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit (16-bit) báo trạng thái vi phạm của từng kênh liên động riêng lẻ (Bit = 1 là kênh đó đang bị lỗi gây cắt máy; Bit = 0 là kênh đó đang OK) |
| **`Sts_Avail`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Tham số định danh trạng thái sẵn sàng dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |
| **`Sts_FirstOut`** | `INT` | `2#0000_0000_0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit (16-bit) chỉ báo thủ phạm đầu tiên gây cắt liên động (First-Out Status: chỉ duy nhất bit của điều kiện bị mất đầu tiên lên 1) |
| **`P_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_Intlk) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 15 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_IntlkAdv`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_CondTxt`** | `STRING_Cond[16]` | `{...}` | [Biến cấu hình thông số] Mảng 16 chuỗi văn bản mô tả tên của 16 điều kiện Khóa liên động / Cho phép hiển thị trên HMI Faceplate |
| **`Cfg_Desc`** | `STRING_40` | `'Advanced Interlocks with First Out'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_Label`** | `STRING_20` | `'Advanced Interlocks'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_NavTag`** | `STRING_NavTag[16]` | `{...}` | [Biến cấu hình thông số] Mảng 16 tên thẻ (Tagname) đích tương ứng với 16 nút điều hướng điều kiện liên động/cho phép trên HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_IntlkAdv'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`Cfg_TypeDesc`** | `STRING_16[8]` | `{...}` | [Biến cấu hình thông số] Chuỗi mô tả kiểu đối tượng điều hướng hiển thị trên HMI Faceplate |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_IntlkAdv'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`MSet_Bypass`** | `INT` | `2#0000_0000_0000_0000` | [Giá trị đặt từ Bảo trì HMI] Giá trị đặt từ Nhân viên bảo trì (Maintenance HMI): Từ bit (Bitmask) bật/tắt chế độ Bypass bảo trì cho từng điều kiện riêng lẻ (Bit = 1 là Bypass) |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`Val_FirstOutTxt`** | `STRING_Cond` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi văn bản mô tả điều kiện Khóa liên động đầu tiên gây cắt máy (First-Out Condition Text) |
| **`Val_FirstOutType`** | `STRING_16` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi định danh kiểu đối tượng hoặc thẻ điều hướng của điều kiện First-Out đầu tiên |
