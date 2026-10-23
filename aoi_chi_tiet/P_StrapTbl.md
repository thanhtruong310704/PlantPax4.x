# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_StrapTbl`
**Thuộc chương:** [Chương 1: Xử lý tín hiệu Vào/Ra (I/O Processing)](../01_CHUONG_1_IO_PROCESSING.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `IO Processing 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 72–75)  
**Tệp định nghĩa khối lệnh:** `P_StrapTbl_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_StrapTbl`:** **17** Biến đầu vào (`Input Parameters`) | **10** Biến đầu ra (`Output Parameters`) | **9** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_StrapTbl` trong Studio 5000
- **Chức năng cốt lõi:** Khối tính toán **Thể tích sản phẩm thực tế trong bồn chứa trụ đứng** bằng phương pháp nội suy tuyến tính trên Bảng hiệu chuẩn dung tích bồn (**Tank Strapping Table**), có bù trừ thể tích lớp nước lắng đáy bồn, bù giãn nở nhiệt vỏ thép bồn và bù phần chìm của mái phao (Floating Roof).
- **Khi nào CẦN sử dụng:** Khi quản lý kho bồn xăng dầu, hóa chất, dầu thực vật, sữa tươi có **đáy bồn hình côn/chỏm cầu hoặc thành bồn không đều** đã được cơ quan đo lường lập bảng Bare-Tank Strapping Table.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi bồn hình trụ đều đơn giản chỉ cần quy đổi tuyến tính `0..100%` trong [`P_AIn.md`](./P_AIn.md).

### Quy trình 4 bước đấu nối và cấu hình `P_StrapTbl`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_Level`, `Inp_FWLevel`, `Inp_ObsAPI`, `Inp_AvgProdTemp`, `Inp_AmbTemp`, `Cfg_CalTbl`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_CalTbl`, `Cfg_MinorPerMajor`, `Cfg_HasCTSh`, `Cfg_HasFRA`, `Cfg_CalTemp`, `Cfg_ShellCoefOfExp`, `Cfg_K`, `Cfg_HasMoreObj`, `Cfg_FloatRoofLevel`, `Cfg_FloatRoofCalAPI`, `Cfg_FloatRoofVolPerAPI`
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Desc`, `Cfg_Label`, `Cfg_LevelEU`, `Cfg_Tag`, `Cfg_TempEU`, `Cfg_VolumeEU`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_StrapTbl` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_StrapTbl` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Khối `P_StrapTbl` thực hiện tính toán/xử lý trực tiếp theo các chân đầu vào `Inp_*` và thông số cấu hình `Cfg_*` trong mỗi chu kỳ quét mà không yêu cầu kích lệnh điều khiển `PCmd_*` từ chương trình.
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Val_TOV`, `Val_FW`, `Val_TSh`, `Val_CTSh`, `Val_FRA`, `Val_GOV`, `Sts_UnderMin`, `Sts_OverMax`
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 17 Biến Đầu Vào (`Input Parameters`) của `P_StrapTbl`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp_Level`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Mức chiều cao chất lỏng tổng cộng đo được trong bồn chứa (Tank Innage Level, đơn vị m hoặc feet) |
| **`Inp_FWLevel`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Mức chiều cao lớp nước tự do dưới đáy bồn chứa (Free Water Interface Level, đơn vị m hoặc feet) |
| **`Inp_ObsAPI`** | `REAL` | `30.5` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tỷ trọng quan sát được (Observed Density, độ API) tại nhiệt độ sản phẩm, dùng để tính bù phần chìm mái phao |
| **`Inp_AvgProdTemp`** | `REAL` | `60` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Nhiệt độ trung bình của sản phẩm trong bồn chứa (°C hoặc °F) |
| **`Inp_AmbTemp`** | `REAL` | `60` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Nhiệt độ môi trường xung quanh bên ngoài bồn chứa (°C hoặc °F) |
| **`Cfg_CalTbl`** | `P_StrapTblRow[2]` | `-` | Hiện (Pin) | [Biến cấu hình thông số] Mảng bảng hiệu chuẩn dung tích bồn chứa (Tank Calibration / Strapping Table) quy đổi từ mức chiều cao sang thể tích |
| **`Cfg_MinorPerMajor`** | `REAL` | `12` | Ẩn (Tag) | [Biến cấu hình thông số] Số đơn vị chiều cao nhỏ (mm, cm, inch) trên mỗi đơn vị chiều cao lớn (m, ft) trong bảng tra bồn (đặt = 0.0 nếu không dùng đơn vị phụ) |
| **`Cfg_HasCTSh`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Bật tính năng bù giãn nở nhiệt của thành vỏ bồn thép (Tank Shell Thermal Expansion Correction); 0 = Không bù nhiệt vỏ bồn |
| **`Cfg_HasFRA`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Bật tính năng bù trừ thể tích chiếm chỗ của mái phao (Floating Roof Adjustment); 0 = Bồn cố định không có mái phao |
| **`Cfg_CalTemp`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Nhiệt độ chuẩn khi hiệu chuẩn bảng dung tích bồn chứa (thường là 15°C hoặc 60°F) |
| **`Cfg_ShellCoefOfExp`** | `REAL` | `6.1999999999999999E-6` | Ẩn (Tag) | [Biến cấu hình thông số] Hệ số giãn nở nhiệt dài của vật liệu chế tạo vỏ bồn (1/°C hoặc 1/°F) |
| **`Cfg_K`** | `REAL` | `7` | Ẩn (Tag) | [Biến cấu hình thông số] Hệ số trọng số nhiệt độ môi trường khi tính nhiệt độ vỏ bồn (nhập 0.0 đối với bồn có bọc bảo ôn cách nhiệt hoàn toàn theo API MPMS 2.2A) |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_FloatRoofLevel`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Mức chiều cao chất lỏng tối thiểu mà tại đó mái phao bắt đầu nổi hoàn toàn và áp dụng bù mái phao |
| **`Cfg_FloatRoofCalAPI`** | `REAL` | `30.5` | Ẩn (Tag) | [Biến cấu hình thông số] Tỷ trọng chuẩn (độ API) đã được dùng để lập dữ liệu mái phao trong bảng hiệu chuẩn bồn |
| **`Cfg_FloatRoofVolPerAPI`** | `REAL` | `-2.5` | Ẩn (Tag) | [Biến cấu hình thông số] Hệ số hiệu chỉnh thể tích mái phao trên mỗi độ lệch tỷ trọng API so với Cfg_FloatRoofCalAPI |

## 3. Bảng chi tiết 10 Biến Đầu Ra (`Output Parameters`) của `P_StrapTbl`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Val_TOV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng thể tích quan sát được (Total Observed Volume - TOV) nội suy trực tiếp từ bảng dung tích bồn (đơn vị thùng/gallon/lít) |
| **`Val_FW`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Thể tích lớp nước tự do dưới đáy bồn (Free Water Volume, đơn vị thùng/gallon/lít) |
| **`Val_TSh`** | `REAL` | `60` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Nhiệt độ tính toán của thành vỏ bồn thép (Calculated Tank Shell Temperature, °C hoặc °F) |
| **`Val_CTSh`** | `REAL` | `1` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Hệ số hiệu chỉnh giãn nở nhiệt của thành vỏ bồn thép (Tank Shell Temperature Correction Multiplier) |
| **`Val_FRA`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Thể tích hiệu chỉnh bù phần chìm của mái phao (Floating Roof Adjustment Volume, đơn vị thùng/gallon/lít) |
| **`Val_GOV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng thể tích thực quan sát được của sản phẩm (Gross Observed Volume - GOV theo chuẩn API MPMS 12.1.1) |
| **`Sts_UnderMin`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Mức đo đầu vào (Inp_Level) thấp hơn mức chiều cao nhỏ nhất trong bảng dung tích bồn (Strapping Table) |
| **`Sts_OverMax`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Mức đo đầu vào (Inp_Level) vượt quá mức chiều cao lớn nhất trong bảng dung tích bồn (Strapping Table) |
| **`P_StrapTbl`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_StrapTbl) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 9 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_StrapTbl`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Desc`** | `STRING_40` | `'Tank Strapping Table'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_Label`** | `STRING_20` | `'Strapping Table'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_LevelEU`** | `STRING_8` | `'Feet'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của mức chiều cao bồn (ví dụ: "m", "ft") hiển thị trên HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_StrapTbl'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`Cfg_TempEU`** | `STRING_8` | `'Deg F'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của nhiệt độ (ví dụ: "°C", "°F") hiển thị trên HMI |
| **`Cfg_VolumeEU`** | `STRING_8` | `'Gallons'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của thể tích bồn (ví dụ: "m3", "L", "bbl") hiển thị trên HMI |
| **`Inf_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`Inf_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`Inf_Type`** | `STRING_16` | `'P_StrapTbl'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
