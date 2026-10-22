# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_Gate`
**Thuộc chương:** Chương 5: Khối chức năng dùng chung, Liên động & Cảnh báo (Cross Functional)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Cross Functional 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_Gate`:** **6** Biến đầu vào (`Input Parameters`) | **6** Biến đầu ra (`Output Parameters`) | **5** Biến cấu hình cục bộ (`Local Configuration Tags`)

> **⚡ Truy cập nhanh:** [⬅ Quay lại Danh mục Tra cứu Nhanh 55 Khối AOI (`00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md`)](./00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md)

## 0. Giới thiệu chung & Các nhu cầu cần dùng khối `P_Gate`
- **Tệp định nghĩa khối lệnh (`.L5X`):** `P_Gate_4.10.00_AOI.L5X` *(Trang 336–339 trong tài liệu gốc PROCES-RM013I-EN-P)*
- **Giới thiệu chung chức năng:** Khối **Cổng điều kiện kèm Bộ định thời Trễ Bật / Trễ Tắt (`Condition Gate Delay`)**. Chỉ cho phép tín hiệu đầu vào `Inp` đi qua bộ lọc `On-Delay` và `Off-Delay` sau khi chân cổng `Inp_Gate` đã lên mức 1 đủ thời gian `Cfg_GateDelayT`.
- **Các nhu cầu thực tế CẦN dùng khối `P_Gate`:** Khối này được nhúng sẵn bên trong các khối `P_AIn*`, `P_DIn*`, `P_PIDE`. Bạn dùng `P_Gate` rời khi muốn **lọc chống rung kèm khóa cổng ức chế (Gating)** cho một tín hiệu logic tự viết trong chương trình.
- **Trường hợp KHÔNG dùng & Khối thay thế:** Khi đã dùng sẵn các chân `Inp_*Gate` và `*OnDly`/`*OffDly` bên trong khối `P_AIn` hoặc `P_DIn`.

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_Gate`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_Gate`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_GateDly`, `Cfg_OnDly`, `Cfg_OffDly`
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_GateCond`, `Cfg_InpCond`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_Gate` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Sts_Gate`, `Sts_Err`
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 6 Biến Đầu Vào (`Input Parameters`) của `P_Gate`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Pin/Param | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp`** | `BOOL` | `0` | Pin/Param | Input Condition. This is the result of threshold comparison usually performed by the parent object. |
| **`Inp_Gate`** | `BOOL` | `1` | Pin/Param | [Đầu vào nối dây I/O hoặc Logic] Gate input used for status detection. When set to 1, input monitoring is enabled. When enabled, on delay and off delay times are applied after the gate delay time. When set to 0, input detection is disabled and the status output is forced off. |
| **`Cfg_GateDly`** | `REAL` | `0` | Pin/Param | [Biến cấu hình thông số] The amount of time (in seconds) the gate input must be turned on for status outputs to be enabled. On delays and off delays are applied to the input after the gate delay is completed. |
| **`Cfg_OnDly`** | `REAL` | `0` | Pin/Param | [Biến cấu hình thông số] Determines the minimum time (in seconds) the input must remain on for the status to be set. On-delay times are typically used to avoid unnecessary status indication or alarm when a value only briefly overshoots its threshold. |
| **`Cfg_OffDly`** | `REAL` | `0` | Pin/Param | [Biến cấu hình thông số] Determines the amount of time (in seconds) the input must be cleared in order to clear the status. Off delay times are typically used to reduce chattering status indicators or alarms. |

## 3. Bảng chi tiết 6 Biến Đầu Ra (`Output Parameters`) của `P_Gate`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Pin/Param | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Out`** | `BOOL` | `0` | Pin/Param | Output after On Delay, Off Delay, enabled by Gate. |
| **`Sts_Gate`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1 = Gate Delay done, Output is enabled. |
| **`Sts_Err`** | `BOOL` | `0` | Pin/Param | [Cờ báo trạng thái logic (Status)] 1 = Error in Config (see detail Err_ bits for reason), display icon. |
| **`Err_Timer`** | `BOOL` | `0` | Pin/Param | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: On Delay, Off Delay, Gate Delay Time Invalid (use 0 …2,147,483 seconds). |
| **`P_Gate`** | `BOOL` | `0` | Pin/Param | Unique parameter name for auto-discovery. |

## 4. Bảng chi tiết 5 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_Gate`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_GateCond`** | `STRING_20` | `'Gating Condition'` | [Biến cấu hình thông số] Displayed text of P_Gate's Gating (Inp_Gate) Condition |
| **`Cfg_InpCond`** | `STRING_20` | `'Input Condition'` | [Biến cấu hình thông số] Displayed text of P_Gate's Input (Inp) Condition |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_Gate'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI và phần mềm thông tin |
