# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_Reset`
**Thuộc chương:** Chương 5: Khối chức năng dùng chung, Liên động & Cảnh báo (Cross Functional)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Cross Functional 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_Reset`:** **6** Biến đầu vào (`Input Parameters`) | **5** Biến đầu ra (`Output Parameters`) | **8** Biến cấu hình cục bộ (`Local Configuration Tags`)

> **⚡ Truy cập nhanh:** [⬅ Quay lại `README.md` (Thư mục gốc)](../README.md) | [⬅ Danh mục Tra cứu Nhanh (`00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md`)](./00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md)

## 0. Giới thiệu chung & Các nhu cầu cần dùng khối `P_Reset`
- **Tệp định nghĩa khối lệnh (`.L5X`):** `P_Reset_4.10.00_AOI.L5X` *(Trang 367–370 trong tài liệu gốc PROCES-RM013I-EN-P)*
- **Giới thiệu chung chức năng:** Khối **Xóa lỗi Tập trung (`Central Reset`)** cho cả một cụm thiết bị (Unit / Equipment Module). Gom các cờ `Sts_RdyReset` của các thiết bị con về để báo đèn chờ Reset và phát xung `Out_Reset` có độ rộng tối thiểu (`Cfg_MinResetT`) đảm bảo các thiết bị truyền thông mạng đều nhận được lệnh xóa lỗi.
- **Các nhu cầu thực tế CẦN dùng khối `P_Reset`:** Khi muốn tạo **1 nút nhấn "Reset Toàn Cụm" trên màn hình HMI hoặc trên mặt tủ điều khiển** để xóa lỗi đồng thời cho toàn bộ bơm, van, biến tần và liên động trong khu vực đó.
- **Trường hợp KHÔNG dùng & Khối thay thế:** Khi chỉ xóa lỗi riêng từng thiết bị đơn lẻ ngay trên Faceplate của thiết bị đó.

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_Reset`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_Reset`, `Inp_ResetReqd`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_MinHoldTime`, `Cfg_HasMoreObj`
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_Label`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_Reset` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PCmd_Reset`
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Out_Reset`, `Sts_RdyReset`, `Sts_Err`
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 6 Biến Đầu Vào (`Input Parameters`) của `P_Reset`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Pin/Param | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_Reset`** | `BOOL` | `0` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] Reset Input, 1=Reset, from pushbutton or another object |
| **`Inp_ResetReqd`** | `BOOL` | `1` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] 1=Reset Required, enable Reset button |
| **`Cfg_MinHoldTime`** | `REAL` | `1` | Pin/Param | [Biến cấu hình thông số] Minimum Time to hold Reset Output true (sec) |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Pin/Param | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`PCmd_Reset`** | `BOOL` | `0` | Pin/Param | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Reset, from other object |

## 3. Bảng chi tiết 5 Biến Đầu Ra (`Output Parameters`) của `P_Reset`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Pin/Param | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Out_Reset`** | `BOOL` | `0` | Pin/Param | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1=Reset received for this P_Reset's scope |
| **`Sts_RdyReset`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1=A latched condition is ready to be reset |
| **`Sts_Err`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1=Error in Config: Invalid Hold Time (use 0 to 2147483) |
| **`P_Reset`** | `BOOL` | `0` | Pin/Param | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 8 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_Reset`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_8` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'Multiple-Device Reset'` | [Biến cấu hình thông số] Description for display on HMI. The string shows in the title bar of the faceplate. |
| **`Cfg_Label`** | `STRING_20` | `'Reset'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI. This string appears on the graphic symbol. |
| **`Cfg_Tag`** | `STRING_20` | `'P_Reset'` | [Biến cấu hình thông số] Tagname for display on HMI. This string shows in the bottom right of the home tab of the faceplate. |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Type`** | `STRING_16` | `'P_Reset'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset, from HMI |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
