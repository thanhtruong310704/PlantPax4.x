# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_PTComp`
**Thuộc chương:** Chương 1: Xử lý tín hiệu Vào/Ra (I/O Processing)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `IO Processing 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_PTComp`:** **14** Biến đầu vào (`Input Parameters`) | **8** Biến đầu ra (`Output Parameters`) | **6** Biến cấu hình cục bộ (`Local Configuration Tags`)

> **⚡ Truy cập nhanh:** [⬅ Quay lại Danh mục Tra cứu Nhanh 55 Khối AOI (`00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md`)](./00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md)

## 0. Giới thiệu chung & Các nhu cầu cần dùng khối `P_PTComp`
- **Tệp định nghĩa khối lệnh (`.L5X`):** `P_PTComp_4.10.00_AOI.L5X` *(Trang 68–71 trong tài liệu gốc PROCES-RM013I-EN-P)*
- **Giới thiệu chung chức năng:** Khối toán học tính **Lưu lượng chất khí ở Điều kiện Tiêu chuẩn (STP — Standard Temperature & Pressure)** từ phép đo lưu lượng thể tích (Volumetric) hoặc độ chênh áp ($\Delta P$) kết hợp với cảm biến Áp suất thực (`Inp_Press`) và Nhiệt độ thực (`Inp_Temp`).
- **Các nhu cầu thực tế CẦN dùng khối `P_PTComp`:** Khi đo **lưu lượng khí nén, khí đốt (Natural Gas), khí Nitơ, Oxy, Biogas** mà áp suất và nhiệt độ đường ống thay đổi liên tục, cần quy đổi chính xác về lưu lượng chuẩn ($Nm^3/h$ hoặc $SCFM$) trước khi đưa vào `P_AIn` và bộ PID.
- **Trường hợp KHÔNG dùng & Khối thay thế:** Không dùng cho chất lỏng (không chịu nén) và không dùng cho hơi nước bão hòa/quá nhiệt (dùng bộ Steam Table `PROCES-RM004`).

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_PTComp`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_T_Act`, `Inp_P_Act`, `Inp_DP_Act`, `Inp_F_Act`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_HasMoreObj`, `Cfg_LoFlowCutoff`, `Cfg_T_Std`, `Cfg_P_Std`, `Cfg_T_Offset`, `Cfg_P_Offset`, `Cfg_DP_Ref`, `Cfg_F_Ref`, `Cfg_UseDP`
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Desc`, `Cfg_Label`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_PTComp` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Out_Flow`, `Sts_Err`
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 14 Biến Đầu Vào (`Input Parameters`) của `P_PTComp`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_T_Act`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Actual (measured) temperature, can be absolute or common units (for example, Fahrenheit or Celsius). |
| **`Inp_P_Act`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Actual (measured) pressure, can be absolute or common units (for example, PSIG). |
| **`Inp_DP_Act`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Actual (measured) differential pressure (for example, “WC). |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Inp_F_Act`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Actual (measured) uncompensated flow in volumetric units (for example, CFM). |
| **`Cfg_LoFlowCutoff`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] If Out_Flow is less than this cutoff value, it is shown as 0.0 |
| **`Cfg_T_Std`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Standard temperature in Inp_T_Act units (for example, Fahrenheit or Celsius). |
| **`Cfg_P_Std`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Standard pressure in Inp_P_Act units (for example, PSIG). |
| **`Cfg_T_Offset`** | `REAL` | `273.14999999999998` | Ẩn (Tag) | [Biến cấu hình thông số] ‘Zero’ input-units temperature in absolute units (for example, R or K). |
| **`Cfg_P_Offset`** | `REAL` | `14.696` | Ẩn (Tag) | [Biến cấu hình thông số] ‘Zero’ input-units pressure in absolute units (for example, PSIA). |
| **`Cfg_DP_Ref`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Reference (full-scale) differential pressure (for example, “WC). |
| **`Cfg_F_Ref`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Reference flow in volumetric units (for example, CFM) at Reference DP. |
| **`Cfg_UseDP`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Use Inp_DP_Act (square root curve) to calculate flow. 0 = Use Inp_F_Act (linear). |

## 3. Bảng chi tiết 8 Biến Đầu Ra (`Output Parameters`) của `P_PTComp`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Out_Flow`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Compensated Flow (at Standard Temperature and Pressure: Mass Flow) |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Invalid Configuration, see Err_ bits |
| **`Err_T_Std`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Invalid Standard Temperature (T_Std + T_Offset must be > 0.0) |
| **`Err_P_Std`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Invalid Standard Pressure (P_Std + P_Offset must be > 0.0) |
| **`Err_DP_Ref`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Invalid Reference Differential Pressure (must be > 0.0 if DP used) |
| **`Err_F_Ref`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Invalid Reference Flow (at Reference DP) (must be > 0.0 if DP used) |
| **`P_PTComp`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 6 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_PTComp`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Desc`** | `STRING_40` | `'Pressure / Temperature Compensated Flow'` | [Biến cấu hình thông số] Description for display on HMI. This string is shown in the title bar of the faceplate. |
| **`Cfg_Label`** | `STRING_20` | `'Flow at Std. Cond.'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI. This string appears on the graphic symbol. |
| **`Cfg_Tag`** | `STRING_20` | `'P_PTComp'` | [Biến cấu hình thông số] Tagname for display on HMI. This string is shown in the title bar of the faceplate. |
| **`Inf_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`Inf_Tab`** | `SINT` | `0` | Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`Inf_Type`** | `STRING_16` | `'P_PTComp'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI và phần mềm thông tin |
