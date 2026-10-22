# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_StrapTbl`
**Thuộc chương:** Chương 1: Xử lý tín hiệu Vào/Ra (I/O Processing)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `IO Processing 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_StrapTbl`:** **17** Biến đầu vào (`Input Parameters`) | **10** Biến đầu ra (`Output Parameters`) | **9** Biến cấu hình cục bộ (`Local Configuration Tags`)

> **⚡ Truy cập nhanh:** [⬅ Quay lại Danh mục Tra cứu Nhanh 55 Khối AOI (`00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md`)](./00_GIOI_THIEU_VA_TRA_CUU_NHANH_AOI.md)

## 0. Giới thiệu chung & Các nhu cầu cần dùng khối `P_StrapTbl`
- **Tệp định nghĩa khối lệnh (`.L5X`):** `P_StrapTbl_4.10.00_AOI.L5X` *(Trang 72–75 trong tài liệu gốc PROCES-RM013I-EN-P)*
- **Giới thiệu chung chức năng:** Khối tính toán **Thể tích sản phẩm thực tế trong bồn chứa trụ đứng** bằng phương pháp nội suy tuyến tính trên Bảng hiệu chuẩn dung tích bồn (**Tank Strapping Table**), có bù trừ thể tích lớp nước lắng đáy bồn, bù giãn nở nhiệt vỏ thép bồn và bù phần chìm của mái phao (Floating Roof).
- **Các nhu cầu thực tế CẦN dùng khối `P_StrapTbl`:** Khi quản lý kho bồn xăng dầu, hóa chất, dầu thực vật, sữa tươi có **đáy bồn hình côn/chỏm cầu hoặc thành bồn không đều** đã được cơ quan đo lường lập bảng Bare-Tank Strapping Table.
- **Trường hợp KHÔNG dùng & Khối thay thế:** Khi bồn hình trụ đều đơn giản chỉ cần quy đổi tuyến tính `0..100%` trong [`P_AIn.md`](./P_AIn.md).

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_StrapTbl`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_Level`, `Inp_FWLevel`, `Inp_ObsAPI`, `Inp_AvgProdTemp`, `Inp_AmbTemp`, `Cfg_CalTbl`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_CalTbl`, `Cfg_MinorPerMajor`, `Cfg_HasCTSh`, `Cfg_HasFRA`, `Cfg_CalTemp`, `Cfg_ShellCoefOfExp`, `Cfg_K`, `Cfg_HasMoreObj`, `Cfg_FloatRoofLevel`, `Cfg_FloatRoofCalAPI`, `Cfg_FloatRoofVolPerAPI`
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Desc`, `Cfg_Label`, `Cfg_LevelEU`, `Cfg_Tag`, `Cfg_TempEU`, `Cfg_VolumeEU`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_StrapTbl` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Val_TOV`, `Val_FW`, `Val_TSh`, `Val_CTSh`, `Val_FRA`, `Val_GOV`, `Sts_UnderMin`, `Sts_OverMax`
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 17 Biến Đầu Vào (`Input Parameters`) của `P_StrapTbl`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_Level`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Input: Tank innage level (feet or meters). |
| **`Inp_FWLevel`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Input: Tank innage free water (interface) level (feet or meters). |
| **`Inp_ObsAPI`** | `REAL` | `30.5` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Input: Observed density (degrees API) at product temperature. This is used for floating roof compensation to calculate displacement based on weight of roof. |
| **`Inp_AvgProdTemp`** | `REAL` | `60` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Input: Average Product Temperature (Deg F or C) |
| **`Inp_AmbTemp`** | `REAL` | `60` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Input: Ambient Temperature (Deg F or C) |
| **`Cfg_CalTbl`** | `P_StrapTblRow[2]` | - | Hiện (Pin) | [Biến cấu hình thông số] Tank Calibration Table (level to volume) |
| **`Cfg_MinorPerMajor`** | `REAL` | `12` | Ẩn (Tag) | [Biến cấu hình thông số] Table Minor Units (in, cm, mm) per Major Unit (ft, m) (enter 0.0 if minor units not used) |
| **`Cfg_HasCTSh`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 0 = No correction for temperature of tank shell. 1 = Include correction for temperature of tank shell. |
| **`Cfg_HasFRA`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 0 = Do not use floating roof adjustment. 1 = Include floating roof adjustment to account for displacement of fluid level. |
| **`Cfg_CalTemp`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Temperature of Tank Calibration (typ. 60 Deg F or 15 Deg C) |
| **`Cfg_ShellCoefOfExp`** | `REAL` | `6.1999999999999999E-6` | Ẩn (Tag) | [Biến cấu hình thông số] Tank Shell Linear Coefficient of Thermal Expansion (1/Deg F or 1/Deg C) |
| **`Cfg_K`** | `REAL` | `7` | Ẩn (Tag) | [Biến cấu hình thông số] Temp. weighting (enter 0.0 for insulated tank) (see API MPMS 2.2A app. D) |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_FloatRoofLevel`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Lowest level at which to add/subtract floating roof compensation (feet). |
| **`Cfg_FloatRoofCalAPI`** | `REAL` | `30.5` | Ẩn (Tag) | [Biến cấu hình thông số] Degrees API for which table includes floating roof data |
| **`Cfg_FloatRoofVolPerAPI`** | `REAL` | `-2.5` | Ẩn (Tag) | [Biến cấu hình thông số] Adjustment to table values for API <> CalAPI (volume/degrees API, typically a negative number). |

## 3. Bảng chi tiết 10 Biến Đầu Ra (`Output Parameters`) của `P_StrapTbl`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Val_TOV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Raw total observed volume from Cal. Table (barrels, gallons, liters). |
| **`Val_FW`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Free Water Volume (barrels, gallons, liters). |
| **`Val_TSh`** | `REAL` | `60` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Calculated Tank Shell Temperature (Deg F, Deg C) |
| **`Val_CTSh`** | `REAL` | `1` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Correction for Temperature of Tank Shell (multiplier) |
| **`Val_FRA`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Floating roof adjustment volume (barrels, gallons, liters). |
| **`Val_GOV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Primary value: Gross observed volume (see API MPMS 12.1.1). |
| **`Sts_UnderMin`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Inp_Level is below lowest level in strapping table |
| **`Sts_OverMax`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Inp_Level is above highest level in strapping table |
| **`P_StrapTbl`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 9 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_StrapTbl`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Desc`** | `STRING_40` | `'Tank Strapping Table'` | [Biến cấu hình thông số] Description for display on HMI. This string is shown in the title bar of the faceplate. |
| **`Cfg_Label`** | `STRING_20` | `'Strapping Table'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI. This string appears on the graphic symbol. |
| **`Cfg_LevelEU`** | `STRING_8` | `'Feet'` | [Biến cấu hình thông số] Level Engineering Units for display on HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_StrapTbl'` | [Biến cấu hình thông số] Tagname for display on HMI |
| **`Cfg_TempEU`** | `STRING_8` | `'Deg F'` | [Biến cấu hình thông số] Temperature Engineering Units for display on HMI |
| **`Cfg_VolumeEU`** | `STRING_8` | `'Gallons'` | [Biến cấu hình thông số] Volume Engineering Units for display on HMI |
| **`Inf_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`Inf_Tab`** | `SINT` | `0` | Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`Inf_Type`** | `STRING_16` | `'P_StrapTbl'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI và phần mềm thông tin |
