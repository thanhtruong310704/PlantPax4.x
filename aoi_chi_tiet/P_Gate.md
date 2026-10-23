# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_Gate`
**Thuộc chương:** [Chương 5: Khối chức năng dùng chung, Liên động & Cảnh báo (Cross Functional)](../05_CHUONG_5_CROSS_FUNCTIONAL.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Cross Functional 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 336–339)  
**Tệp định nghĩa khối lệnh:** `P_Gate_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_Gate`:** **6** Biến đầu vào (`Input Parameters`) | **6** Biến đầu ra (`Output Parameters`) | **5** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_Gate` trong Studio 5000
- **Chức năng cốt lõi:** Khối **Cổng điều kiện kèm Bộ định thời Trễ Bật / Trễ Tắt (`Condition Gate Delay`)**. Chỉ cho phép tín hiệu đầu vào `Inp` đi qua bộ lọc `On-Delay` và `Off-Delay` sau khi chân cổng `Inp_Gate` đã lên mức 1 đủ thời gian `Cfg_GateDelayT`.
- **Khi nào CẦN sử dụng:** Khối này được nhúng sẵn bên trong các khối `P_AIn*`, `P_DIn*`, `P_PIDE`. Bạn dùng `P_Gate` rời khi muốn **lọc chống rung kèm khóa cổng ức chế (Gating)** cho một tín hiệu logic tự viết trong chương trình.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi đã dùng sẵn các chân `Inp_*Gate` và `*OnDly`/`*OffDly` bên trong khối `P_AIn` hoặc `P_DIn`.

### Quy trình 4 bước đấu nối và cấu hình `P_Gate`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_Gate`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_GateDly`, `Cfg_OnDly`, `Cfg_OffDly`
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_GateCond`, `Cfg_InpCond`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_Gate` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_Gate` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Khối `P_Gate` thực hiện tính toán/xử lý trực tiếp theo các chân đầu vào `Inp_*` và thông số cấu hình `Cfg_*` trong mỗi chu kỳ quét mà không yêu cầu kích lệnh điều khiển `PCmd_*` từ chương trình.
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Sts_Gate`, `Sts_Err`
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 6 Biến Đầu Vào (`Input Parameters`) của `P_Gate`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu điều kiện đầu vào (1 = Kích hoạt điều kiện / Báo động, 0 = Bình thường) |
| **`Inp_Gate`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input): Khi = 1, cho phép giám sát tín hiệu đầu vào Inp (áp dụng On-Delay/Off-Delay sau thời gian Cfg_GateDly); khi = 0, khóa cổng và ép ngõ ra về 0 |
| **`Cfg_GateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng điều kiện (Gate Delay, tính bằng giây): Khoảng thời gian chân Inp_Gate phải duy trì ở mức 1 trước khi cho phép bật giám sát đầu vào |
| **`Cfg_OnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây): Tín hiệu đầu vào phải duy trì ở mức 1 liên tục đủ thời gian này mới bật ngõ ra |
| **`Cfg_OffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây): Tín hiệu đầu vào phải về mức 0 liên tục đủ thời gian này mới tắt ngõ ra |

## 3. Bảng chi tiết 6 Biến Đầu Ra (`Output Parameters`) của `P_Gate`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Out`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra điều khiển thiết bị (hoặc kết quả cổng điều kiện sau thời gian trễ On-Delay / Off-Delay) |
| **`Sts_Gate`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) đã hoàn tất, cho phép tín hiệu đi qua cổng giám sát |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị đặt bộ định thời gian (Timer Preset) nằm ngoài phạm vi hợp lệ 0..2.147.483 giây |
| **`P_Gate`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_Gate) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 5 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_Gate`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_GateCond`** | `STRING_20` | `'Gating Condition'` | [Biến cấu hình thông số] Chuỗi văn bản mô tả điều kiện mở cổng (Inp_Gate) hiển thị trên HMI của khối P_Gate |
| **`Cfg_InpCond`** | `STRING_20` | `'Input Condition'` | [Biến cấu hình thông số] Chuỗi văn bản mô tả điều kiện đầu vào (Inp) hiển thị trên HMI của khối P_Gate |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_Gate'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
