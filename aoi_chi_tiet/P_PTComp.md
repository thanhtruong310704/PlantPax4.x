# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_PTComp`
**Thuộc chương:** [Chương 1: Xử lý tín hiệu Vào/Ra (I/O Processing)](../01_CHUONG_1_IO_PROCESSING.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `IO Processing 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 68–71)  
**Tệp định nghĩa khối lệnh:** `P_PTComp_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_PTComp`:** **14** Biến đầu vào (`Input Parameters`) | **8** Biến đầu ra (`Output Parameters`) | **6** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_PTComp` trong Studio 5000
- **Chức năng cốt lõi:** Khối toán học tính **Lưu lượng chất khí ở Điều kiện Tiêu chuẩn (STP — Standard Temperature & Pressure)** từ phép đo lưu lượng thể tích (Volumetric) hoặc độ chênh áp ($\Delta P$) kết hợp với cảm biến Áp suất thực (`Inp_Press`) và Nhiệt độ thực (`Inp_Temp`).
- **Khi nào CẦN sử dụng:** Khi đo **lưu lượng khí nén, khí đốt (Natural Gas), khí Nitơ, Oxy, Biogas** mà áp suất và nhiệt độ đường ống thay đổi liên tục, cần quy đổi chính xác về lưu lượng chuẩn ($Nm^3/h$ hoặc $SCFM$) trước khi đưa vào `P_AIn` và bộ PID.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Không dùng cho chất lỏng (không chịu nén) và không dùng cho hơi nước bão hòa/quá nhiệt (dùng bộ Steam Table `PROCES-RM004`).

### Quy trình 4 bước đấu nối và cấu hình `P_PTComp`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_T_Act`, `Inp_P_Act`, `Inp_DP_Act`, `Inp_F_Act`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_HasMoreObj`, `Cfg_LoFlowCutoff`, `Cfg_T_Std`, `Cfg_P_Std`, `Cfg_T_Offset`, `Cfg_P_Offset`, `Cfg_DP_Ref`, `Cfg_F_Ref`, `Cfg_UseDP`
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Desc`, `Cfg_Label`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_PTComp` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_PTComp` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Khối `P_PTComp` thực hiện tính toán/xử lý trực tiếp theo các chân đầu vào `Inp_*` và thông số cấu hình `Cfg_*` trong mỗi chu kỳ quét mà không yêu cầu kích lệnh điều khiển `PCmd_*` từ chương trình.
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Out_Flow`, `Sts_Err`
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 14 Biến Đầu Vào (`Input Parameters`) của `P_PTComp`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp_T_Act`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Nhiệt độ thực tế đo được trên đường ống (Actual Temperature, đơn vị °C hoặc °F) |
| **`Inp_P_Act`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Áp suất thực tế đo được trên đường ống (Actual Pressure, đơn vị tương đối hoặc tuyệt đối như barG, PSIG) |
| **`Inp_DP_Act`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Độ chênh áp thực tế đo được qua thiết bị tiết lưu Orifice/Venturi (Actual Differential Pressure, ví dụ: mbar hoặc inWC) |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Inp_F_Act`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Lưu lượng thể tích thực tế đo được chưa bù (Actual Volumetric Flow, ví dụ: m³/h hoặc CFM) |
| **`Cfg_LoFlowCutoff`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng cắt lưu lượng nhỏ (Low Flow Cutoff): Nếu lưu lượng tính ra nhỏ hơn ngưỡng này thì xuất Out_Flow = 0.0 |
| **`Cfg_T_Std`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Nhiệt độ ở điều kiện tiêu chuẩn (Standard Temperature, tính theo đơn vị của Inp_T_Act, ví dụ: 0°C, 15°C hoặc 60°F) |
| **`Cfg_P_Std`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Áp suất ở điều kiện tiêu chuẩn (Standard Pressure, tính theo đơn vị của Inp_P_Act, ví dụ: 1.01325 bar hoặc 14.696 psi) |
| **`Cfg_T_Offset`** | `REAL` | `273.14999999999998` | Ẩn (Tag) | [Biến cấu hình thông số] Độ lệch cộng thêm để quy đổi đơn vị nhiệt độ đo (Inp_T_Act) về nhiệt độ tuyệt đối Kelvin hoặc Rankine (ví dụ: 273.15 nếu đo bằng °C; 459.67 nếu đo bằng °F) |
| **`Cfg_P_Offset`** | `REAL` | `14.696` | Ẩn (Tag) | [Biến cấu hình thông số] Độ lệch cộng thêm để quy đổi đơn vị áp suất đo (Inp_P_Act) về áp suất tuyệt đối (ví dụ: áp suất khí quyển 1.01325 bar nếu đo áp suất dư barG; đặt = 0 nếu cảm biến đo áp suất tuyệt đối barA) |
| **`Cfg_DP_Ref`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Độ chênh áp chuẩn tối đa (Reference Full-Scale Differential Pressure) tương ứng với lưu lượng chuẩn Cfg_F_Ref |
| **`Cfg_F_Ref`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Lưu lượng thể tích chuẩn tại điều kiện thiết kế (Reference Flow, ví dụ: Nm³/h hoặc SCFM) tương ứng với độ chênh áp chuẩn Cfg_DP_Ref |
| **`Cfg_UseDP`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Phương pháp tính lưu lượng khí bù nhiệt-áp: 1 = Tính từ độ chênh áp Inp_DP_Act (có khai căn bậc hai); 0 = Tính từ lưu lượng thể tích Inp_F_Act (tuyến tính) |

## 3. Bảng chi tiết 8 Biến Đầu Ra (`Output Parameters`) của `P_PTComp`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Out_Flow`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Lưu lượng khí/hơi đã bù Nhiệt độ & Áp suất về điều kiện tiêu chuẩn (Compensated Flow — Lưu lượng chuẩn/khối lượng) |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_T_Std`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Nhiệt độ tiêu chuẩn tuyệt đối (Cfg_T_Std + Cfg_T_Offset) phải lớn hơn 0.0 |
| **`Err_P_Std`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Áp suất tiêu chuẩn tuyệt đối (Cfg_P_Std + Cfg_P_Offset) phải lớn hơn 0.0 |
| **`Err_DP_Ref`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Áp suất chênh áp chuẩn (Cfg_DP_Ref) không hợp lệ (phải > 0.0 khi dùng phép đo chênh áp) |
| **`Err_F_Ref`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Lưu lượng chuẩn (Cfg_F_Ref tại chênh áp chuẩn) không hợp lệ (phải > 0.0 khi dùng chênh áp) |
| **`P_PTComp`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_PTComp) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 6 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_PTComp`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Desc`** | `STRING_40` | `'Pressure / Temperature Compensated Flow'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_Label`** | `STRING_20` | `'Flow at Std. Cond.'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_Tag`** | `STRING_20` | `'P_PTComp'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`Inf_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`Inf_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`Inf_Type`** | `STRING_16` | `'P_PTComp'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
