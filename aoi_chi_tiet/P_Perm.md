# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_Perm`
**Thuộc chương:** [Chương 5: Khối chức năng dùng chung, Liên động & Cảnh báo (Cross Functional)](../05_CHUONG_5_CROSS_FUNCTIONAL.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Cross Functional 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 353–358)  
**Tệp định nghĩa khối lệnh:** `P_Perm_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_Perm`:** **7** Biến đầu vào (`Input Parameters`) | **6** Biến đầu ra (`Output Parameters`) | **10** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_Perm` trong Studio 5000
- **Chức năng cốt lõi:** Khối **Điều kiện Cho phép Khởi động 16 đầu vào (`Permissives with Bypass`)**. Kiểm tra tối đa 16 điều kiện `Inp_Perm00..15` trước khi cho phép thiết bị khởi động (hoặc mở van), có hỗ trợ Bypass bảo trì và điều hướng nhanh tới thẻ nguyên nhân trên HMI.
- **Khi nào CẦN sử dụng:** Khi cần kiểm tra các **điều kiện chuẩn bị trước lúc bấm Start** (ví dụ: van đầu hút đã mở, van xả đáy đã đóng, áp suất nước làm mát phớt đã đủ) nhưng khi máy đã chạy rồi thì không dùng các tín hiệu này để cắt máy. *(Đặt tên thẻ: `[Tên_Thiết_Bị]_Perm`)*.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi điều kiện bắt buộc phải duy trì suốt quá trình chạy và nếu mất phải cắt máy ngay → Dùng [`P_Intlk.md`](./P_Intlk.md).

### Quy trình 4 bước đấu nối và cấu hình `P_Perm`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_Perm00…Inp_Perm15`, `Inp_BypActive`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_OKState`, `Cfg_Bypassable`, `Cfg_HasMoreObj`, `Cfg_HasNav`
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_CondTxt`, `Cfg_Desc`, `Cfg_Label`, `Cfg_NavTag`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_Perm` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_Perm` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Khối `P_Perm` thực hiện tính toán/xử lý trực tiếp theo các chân đầu vào `Inp_*` và thông số cấu hình `Cfg_*` trong mỗi chu kỳ quét mà không yêu cầu kích lệnh điều khiển `PCmd_*` từ chương trình.
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Sts_PermOK`, `Sts_NBPermOK`, `Sts_BypActive`, `Sts_Perm`
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 7 Biến Đầu Vào (`Input Parameters`) của `P_Perm`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp_Perm00…Inp_Perm15`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 16 chân đầu vào điều kiện Cho phép khởi động (Inp_Perm00..15): Nếu tất cả các chân đều ở trạng thái chuẩn (Cfg_OKState) hoặc được Bypass thì Sts_PermOK = 1 cho phép thiết bị khởi động |
| **`Inp_BypActive`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu báo thiết bị chính đang bật chế độ Bypass bảo trì (1 = Đang kích hoạt Bypass các điều kiện cho phép bypass; nối từ Sts_BypActive của khối thiết bị) |
| **`Cfg_OKState`** | `INT` | `2#1111_1111_1111_1111` | Ẩn (Tag) | [Biến cấu hình thông số] Từ cấu hình 16-bit quy định mức logic an toàn (0 hoặc 1) cho từng chân trong 16 điều kiện (Inp_00..15): Bit = 1 nghĩa là chân đó phải bằng 1 mới OK (kết nối thường đóng fail-safe); Bit = 0 nghĩa là chân đó phải bằng 0 mới OK |
| **`Cfg_Bypassable`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ cấu hình 16-bit phân quyền Bypass bảo trì: Bit = 1 cho phép điều kiện tương ứng được bỏ qua khi bật Bypass; Bit = 0 là điều kiện an toàn bất khả kháng (Non-Bypassable) |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_HasNav`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ bit (16-bit) bật nút điều hướng nhảy tới Faceplate nguyên nhân cho từng điều kiện trong 16 kênh (Bit = 1 là bật) |

## 3. Bảng chi tiết 6 Biến Đầu Ra (`Output Parameters`) của `P_Perm`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Sts_PermOK`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái tổng hợp của toàn bộ điều kiện Cho phép khởi động (bao gồm các kênh được Bypass): 1 = Tất cả đều OK (cho phép khởi động) |
| **`Sts_NBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái tổng hợp của các điều kiện Cho phép khởi động KHÔNG thể Bypass: 1 = Tất cả đều OK (cho phép khởi động) |
| **`Sts_BypActive`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Chế độ Bypass bảo trì đang kích hoạt (gửi sang khối P_Intlk / P_Perm để cho phép bỏ qua các điều kiện có quyền Bypass) |
| **`Sts_Perm`** | `INT` | `2#1111_1111_1111_1111` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit (16-bit) báo trạng thái của từng điều kiện Cho phép khởi động riêng lẻ (Bit = 1 là OK; Bit = 0 là chưa thỏa mãn) |
| **`P_Perm`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_Perm) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 10 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_Perm`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_8` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_CondTxt`** | `STRING_20[16]` | `{...}` | [Biến cấu hình thông số] Mảng 16 chuỗi văn bản mô tả tên của 16 điều kiện Khóa liên động / Cho phép hiển thị trên HMI Faceplate |
| **`Cfg_Desc`** | `STRING_40` | `'Permissives'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_Label`** | `STRING_20` | `'Permissives'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_NavTag`** | `STRING_NavTag[16]` | `{...}` | [Biến cấu hình thông số] Mảng 16 tên thẻ (Tagname) đích tương ứng với 16 nút điều hướng điều kiện liên động/cho phép trên HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_Perm'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_Perm'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`MSet_Bypass`** | `INT` | `2#0000_0000_0000_0000` | [Giá trị đặt từ Bảo trì HMI] Giá trị đặt từ Nhân viên bảo trì (Maintenance HMI): Từ bit (Bitmask) bật/tắt chế độ Bypass bảo trì cho từng điều kiện riêng lẻ (Bit = 1 là Bypass) |
