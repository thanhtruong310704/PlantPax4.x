# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_ResInh`
**Thuộc chương:** Chương 3: Động cơ, Biến tần & Rơ-le Quá tải (Motors & Drives)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Motors 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_ResInh`:** **13** Biến đầu vào (`Input Parameters`) | **8** Biến đầu ra (`Output Parameters`) | **7** Biến cấu hình cục bộ (`Local Configuration Tags`)

> **⚡ Truy cập nhanh:** [⬅ Quay lại `README.md` (Thư mục gốc)](../README.md) | [⬅ Danh mục Tra cứu Nhanh (`00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md`)](./00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md)

## 0. Giới thiệu chung & Các nhu cầu cần dùng khối `P_ResInh`
- **Tệp định nghĩa khối lệnh (`.L5X`):** `P_ResInh_4.10.00_AOI.L5X` *(Trang 273–278 trong tài liệu gốc PROCES-RM013I-EN-P)*
- **Giới thiệu chung chức năng:** Khối **Khóa cấm khởi động lại (Restart Inhibit)** bảo vệ nhiệt cho động cơ công suất lớn. Tính toán mô hình tích lũy nhiệt mỗi lần khởi động: giới hạn số lần khởi động liên tiếp khi nguội/nóng, ép thời gian nghỉ tối thiểu giữa 2 lần chạy và xuất đồng hồ đếm ngược thời gian chờ cho phép chạy lại (`Out_OKToStart`).
- **Các nhu cầu thực tế CẦN dùng khối `P_ResInh`:** Gắn kèm với **các động cơ trung thế hoặc hạ thế công suất lớn (> 75 kW)** có quán tính tải nặng (quạt lớn, máy nghiền, bơm lớn) để ngăn người vận hành nhấn Start liên tục làm cháy cuộn dây stator.
- **Trường hợp KHÔNG dùng & Khối thay thế:** Không cần dùng cho các động cơ công suất nhỏ khởi động nhẹ nhàng.

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_ResInh`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_Stopped`, `Inp_Starting`, `Inp_Running`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_ThreeColdStarts`, `Cfg_FirstFailCold`, `Cfg_SubseqFailCold`, `Cfg_FirstFailHot`, `Cfg_SubseqFailHot`, `Cfg_HasMoreObj`, `Cfg_HotRestartOK`, `Cfg_RestartHot`, `Cfg_HotToCold`
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_Label`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_ResInh` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Val_MinToReady`, `Val_SecToReady`, `Val_Fdbk`, `Val_State`, `Sts_Ready`, `Sts_Err`
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 13 Biến Đầu Vào (`Input Parameters`) của `P_ResInh`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_Stopped`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Equipment is confirmed Stopped |
| **`Inp_Starting`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Equipment is Starting, indicating a start attempt |
| **`Inp_Running`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Equipment is confirmed Running |
| **`Cfg_ThreeColdStarts`** | `REAL` | `0.5` | Ẩn (Tag) | [Biến cấu hình thông số] Time within which three starts are allowed if cold (hr) |
| **`Cfg_FirstFailCold`** | `REAL` | `0.5` | Ẩn (Tag) | [Biến cấu hình thông số] Time for cold motor to wait after 1st start failure before ready to start (hr) |
| **`Cfg_SubseqFailCold`** | `REAL` | `4` | Ẩn (Tag) | [Biến cấu hình thông số] Time for cold motor to wait after 2nd and subsequent start failure before ready (hr) |
| **`Cfg_FirstFailHot`** | `REAL` | `0.5` | Ẩn (Tag) | [Biến cấu hình thông số] Time for hot motor to wait after 1st start failure before ready to start (hr) |
| **`Cfg_SubseqFailHot`** | `REAL` | `4` | Ẩn (Tag) | [Biến cấu hình thông số] Time for hot motor to wait after 2nd and subsequent start failure before ready (hr) |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_HotRestartOK`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Time for hot motor to run so it can immediately restart after stop (hr) |
| **`Cfg_RestartHot`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Time for hot motor to wait after stop if stopped before Hot Restart OK time (hr) |
| **`Cfg_HotToCold`** | `REAL` | `16` | Ẩn (Tag) | [Biến cấu hình thông số] Time for a stopped hot motor to become cold (hr) |

## 3. Bảng chi tiết 8 Biến Đầu Ra (`Output Parameters`) của `P_ResInh`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Val_MinToReady`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Minutes yet inhibited before ready to start (mmm:ss) |
| **`Val_SecToReady`** | `SINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Seconds yet inhibited before ready to start (mmm:ss) |
| **`Val_Fdbk`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device feedback: 0 = None/Multiple 1 = Stopped 2 = Starting 3 = Running |
| **`Val_State`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] State Number (see State Diagram in docs) for HMI |
| **`Sts_Ready`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Permissive for unit to start 1 = ready, 0 = not ready |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Error in Config: Invalid Time (use 0.0 to 2147483) |
| **`P_ResInh`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 7 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_ResInh`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_8` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'Restart Inhibit'` | [Biến cấu hình thông số] Description for display on HMI. The string shows in the title bar of the faceplate. |
| **`Cfg_Label`** | `STRING_20` | `'Restart Inhibit'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI. This string appears on the graphic symbol. |
| **`Cfg_Tag`** | `STRING_20` | `'P_ResInh'` | [Biến cấu hình thông số] Tagname for display on HMI. This string is shown in the title bar of the faceplate. |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_ResInh'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI và phần mềm thông tin |
