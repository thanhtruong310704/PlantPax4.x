# PHẦN 0: TỔNG QUAN, MỤC LỤC CHI TIẾT & HƯỚNG DẪN SỬ DỤNG TÀI LIỆU
**Tài liệu gốc:** Rockwell Automation Publication `PROCES-RM013I-EN-P` (Tháng 04/2023) — Thay thế bản `PROCES-RM013H-EN-P` (Tháng 12/2021)  
**Tên đầy đủ:** *Rockwell Automation Library of Process Objects: Logic Instructions* (436 trang)  
**Tài liệu HMI đi kèm:** Publication `PROCES-RM014` (*Display Elements, Global Objects, and HMI Information*)

## 1. Tóm tắt thay đổi phiên bản (Summary of Changes - Trang 12)
* **Cập nhật khối lệnh AOI:** Nâng cấp hai khối lệnh cốt lõi là **`P_Intlk`** (Khóa liên động có First Out và Bypass) và **`P_Perm`** (Điều kiện cho phép khởi động có Bypass).
* **Cập nhật bảng tham số đính kèm:** Toàn bộ 5 tệp bảng tính Microsoft Excel đính kèm trong PDF đã được cập nhật để phản ánh các thay đổi về tham số (Parameters) và thẻ cấu hình cục bộ (Local Configuration Tags) của phiên bản thư viện **4.10.00 (4.1)**.

## 2. Hướng dẫn truy cập 5 tệp Excel đính kèm (Access the Attachments - Trang 12–13)
Bên trong tệp PDF `proces-rm013_-en-p.pdf` có đính kèm 5 tệp bảng tính Excel chứa danh sách đầy đủ toàn bộ tham số đầu vào, đầu ra và biến cục bộ của từng họ lệnh AOI (đã được trích xuất sẵn ra thư mục `/home/user/proces-rm013_vi_chi_tiet/attachments/` trên máy ảo):

| Tên tệp Excel đính kèm | Dung lượng | Nội dung chi tiết |
| :--- | :--- | :--- |
| `IO Processing 4.1.xlsx` | 168 KB | Toàn bộ tham số của 13 khối xử lý tín hiệu Vào/Ra (Chương 1) |
| `Regulatory_Procedural Control 4.1.xlsx` | 129 KB | Toàn bộ tham số của 7 khối điều khiển PID & Quy trình (Chương 2) |
| `Motors 4.1.xlsx` | 233 KB | Toàn bộ tham số của 17 khối Động cơ, Biến tần & Rơ-le quá tải (Chương 3) |
| `Valves 4.1.xlsx` | 129 KB | Toàn bộ tham số của 8 khối Van điều khiển & Thiết bị đa vị trí (Chương 4) |
| `Cross Functional 4.1.xlsx` | 45 KB | Toàn bộ tham số của 10 khối Liên động, Cảnh báo & Dùng chung (Chương 5) |

Mỗi tệp Excel bao gồm **3 trang tính (Tabs)** chuẩn hóa:
1. **Input Parameters:** Danh sách các tham số đầu vào (`Inp_*`, `Cfg_*`, `PCmd_*`, `OCmd_*`, `PSet_*`, `OSet_*`, `MSet_*`).
2. **Output Parameters:** Danh sách các tham số đầu ra (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`, `SrcQ_*`).
3. **Local Configuration Tags:** Các biến cấu hình nội bộ (`Cfg_*`, chuỗi ký tự `STRING` hiển thị HMI như `Cfg_Desc`, `Cfg_Label`, `Cfg_Tag`, `Cfg_EU`).

## 3. Hệ sinh thái tài liệu tham khảo PlantPAx (Additional Resources - Trang 14)

| Mã tài liệu | Tên tài liệu | Mô tả nội dung kỹ thuật |
| :--- | :--- | :--- |
| `PROCES-SG001` | *PlantPAx DCS Selection Guide* | Định nghĩa các thành phần hệ thống và quy tắc tính toán quy mô (Sizing) khi lựa chọn cấu hình hệ thống DCS PlantPAx. |
| `PROCES-UM001` | *PlantPAx DCS Infrastructure Configuration User Manual* | Quy trình cấu hình hạ tầng mạng, máy chủ, bộ điều khiển tạo nên hệ thống DCS hiện đại. |
| `PROCES-UM003` | *PlantPAx DCS Application Configuration User Manual* | Quy trình phát triển ứng dụng điều khiển và nhập các chiến lược mẫu (Process Strategies). |
| `PROCES-RM001` | *PlantPAx DCS Reference Manual* | Các khuyến nghị chuẩn hóa khi triển khai kiến trúc hệ thống PlantPAx. |
| `PROCES-RM013` & `PROCES-RM014` | *Library of Process Objects Reference Manuals* | Bộ đôi sổ tay tra cứu thuật toán logic AOI (`RM013`) và giao diện HMI/Faceplates (`RM014`). |
| `PROCES-RM003` | *Library of Logix Diagnostic Objects Reference Manual* | Các khối AOI chẩn đoán bộ điều khiển Logix (dung lượng bộ nhớ, thời gian quét CPU, lỗi truyền thông). |
| `PROCES-RM004` | *Library of Steam Table Instructions* | Các khối AOI tính toán bảng nhiệt động lực học hơi nước bão hòa và quá nhiệt (Steam Tables). |
| `1715-UM001` | *Redundant I/O System User Manual* | Hướng dẫn lắp đặt và cấu hình hệ thống I/O dự phòng nóng 1715 Redundant I/O. |

## 4. Cấu trúc bộ tài liệu dịch chi tiết trong thư mục `/home/user/proces-rm013_vi_chi_tiet/`
1. **`00_MUC_LUC_VA_TONG_QUAN.md`**: Tổng quan, lịch sử thay đổi, cấu trúc 5 file Excel đính kèm và tài liệu liên quan (Trang 1–14).
2. **`01_CHUONG_1_IO_PROCESSING.md`**: Dịch chi tiết 13 khối xử lý I/O (Trang 15–76).
3. **`02_CHUONG_2_REGULATORY_PROCEDURAL_CONTROL.md`**: Dịch chi tiết 7 khối điều khiển PID & Quy trình (Trang 77–118).
4. **`03_CHUONG_3_MOTORS_VA_BIEN_TAN.md`**: Dịch chi tiết 17 khối Động cơ, Biến tần PowerFlex, Khởi động mềm SMC & Rơ-le quá tải E300 (Trang 119–278).
5. **`04_CHUONG_4_VALVES.md`**: Dịch chi tiết 8 khối Van điều khiển, Van chống nhiễm chéo Mix-proof & Thiết bị đa vị trí (Trang 279–330).
6. **`05_CHUONG_5_CROSS_FUNCTIONAL.md`**: Dịch chi tiết 10 khối Liên động, Cho phép khởi động, Cảnh báo, Nguồn lệnh & Logic khả trình (Trang 331–396).
7. **`06_PHU_LUC_A_B_C.md`**: Dịch chi tiết Phụ lục A (Command Source & Simulation), Phụ lục B (41 khối LINT 64-bit & Date/Time) và Phụ lục C (>65 Process Strategies `.L5X`) (Trang 397–436).

# CHƯƠNG 1: XỬ LÝ TÍN HIỆU VÀO/RA (I/O PROCESSING — TRANG 15–76)

## 1. Mục đích và Bảng lựa chọn đối tượng I/O (Purpose & Selection Table - Trang 15–20)
Nhóm đối tượng xử lý I/O thực hiện tiếp nhận, chuẩn hóa, lọc nhiễu, chuyển đổi đơn vị kỹ thuật và giám sát chất lượng cho cả tín hiệu tương tự (Analog) lẫn tín hiệu rời rạc (Discrete), đồng thời cung cấp các khối tính toán lưu lượng bù áp suất/nhiệt độ và nội suy thể tích bồn trụ đứng.

| Khối lệnh AOI | Tệp Controller (`.L5X`) | Khi nào nên sử dụng (When to Use) | Khi nào KHÔNG sử dụng (When Not to Use) |
| :--- | :--- | :--- | :--- |
| **`P_AIn`** | `P_AIn_4.10.00_AOI.L5X` | Giám sát 1 kênh analog tiêu chuẩn, quy đổi tuyến tính (Linear Scaling) và cảnh báo vượt ngưỡng HH/H/L/LL. | Khi cần khai căn bậc hai, cảnh báo tốc độ biến thiên (Rate of Change) hoặc chọn từ nhiều cảm biến (dùng `P_AInAdv`, `P_AInDual`, `P_AInMulti`). |
| **`P_AIChan`** | `P_AIChan_4.10.00_AOI.L5X` | Giám sát chẩn đoán chuyên sâu cho 1 kênh thẻ Analog Input (đi kèm với `P_AIn`, `P_AInAdv`, `P_AInDual`). | Không dùng độc lập để hiển thị giá trị PV quá trình chính (chỉ dùng cho chẩn đoán kênh phần cứng). |
| **`P_AInAdv`** | `P_AInAdv_4.10.00_AOI.L5X` | Khi cần tính căn bậc hai (Square Root cho lưu lượng chênh áp), cảnh báo tốc độ thay đổi (RoC), cảnh báo độ lệch (Deviation) hoặc ức chế cảnh báo theo điều kiện. | Khi chỉ cần đọc và cảnh báo ngưỡng cơ bản và muốn tiết kiệm bộ nhớ bộ điều khiển (dùng `P_AIn`). |
| **`P_AInDual`** | `P_AInDual_4.10.00_AOI.L5X` | Khi có 2 cảm biến đo cùng 1 đại lượng quá trình (PV), cần tính trung bình/Min/Max và cảnh báo sai lệch giữa 2 cảm biến. | Khi chỉ có 1 cảm biến (`P_AIn`) hoặc có từ 3 cảm biến trở lên (`P_AInMulti`). |
| **`P_AInMulti`** | `P_AInMulti_4.10.00_AOI.L5X` | Khi có từ 2 đến 8 cảm biến đo cùng 1 thông số (ví dụ nhiệt độ đa điểm), cần tự động loại bỏ cảm biến hỏng và tính Average/Median/Min/Max. | Khi chỉ có 1 hoặc 2 cảm biến đơn giản. |
| **`P_DIn`** | `P_DIn_4.10.00_AOI.L5X` | Tiếp nhận và xử lý 1 tín hiệu rời rạc `BOOL` (công tắc hành trình, báo mức, nút nhấn), lọc rung (Debounce) và tạo cảnh báo bất thường. | Khi cần giám sát tốc độ quay (Speed Switch) hoặc 2 trạng thái bất thường độc lập (dùng `P_DInAdv`). |
| **`P_DInAdv`** | `P_DInAdv_4.10.00_AOI.L5X` | Khi cần tính năng công tắc tốc độ (Speed Switch) kết hợp tín hiệu chạy động cơ để phát hiện đứt dây đai/khớp nối, hoặc cần 2 cảnh báo trạng thái bất thường. | Cho các tín hiệu số thông thường chỉ cần 1 cảnh báo (`P_DIn`). |
| **`P_DOut`** | `P_DOut_4.10.00_AOI.L5X` | Điều khiển 1 đầu ra rời rạc (rơ-le, cuộn hút, đèn/còi) ở chế độ giữ nguyên (Steady), xung đơn (Single Pulse) hoặc xung liên tục (Continually Pulsed). | Khi điều khiển động cơ hoặc van có khối chuyên dụng riêng (`P_Motor`, `P_ValveSO`). |
| **`P_AOut`** | `P_AOut_4.10.00_AOI.L5X` | Điều khiển trực tiếp 1 đầu ra tương tự (trạm nạp bằng tay Manual Loading Station, van tiết lưu, bộ điều tốc) có giới hạn biên độ và tốc độ biến thiên. | Khi điều khiển van điều khiển có phản hồi vị trí và công tắc hành trình (dùng `P_ValveC`). |
| **`P_PTComp`** | `P_PTComp_4.10.00_AOI.L5X` | Tính lưu lượng khí ở điều kiện tiêu chuẩn (Standard Temperature & Pressure - STP) từ lưu lượng thể tích hoặc chênh áp kết hợp áp suất và nhiệt độ thực. | Không dùng cho hơi nước bão hòa/quá nhiệt cần bảng tra Steam Table (`PROCES-RM004`). |
| **`P_StrapTbl`** | `P_StrapTbl_4.10.00_AOI.L5X` | Tính thể tích sản phẩm trong bồn trụ đứng dựa trên bảng hiệu chuẩn (Tank Strapping Table), bù nước lắng đáy, giãn nở nhiệt vỏ bồn và mái phao. | Khi bồn có hình học tuyến tính đơn giản chỉ cần quy đổi bậc nhất trong `P_AIn`. |
| **`P_AInHART` / `P_AOutHART`** | Xem `PROCES-RM010` | Đọc/xuất tín hiệu 4-20mA kết hợp dữ liệu kỹ thuật số HART (PV, SV, TV, QV và trạng thái chẩn đoán thiết bị). | Khi thẻ I/O và thiết bị hiện trường không hỗ trợ giao thức HART. |

---

## 2. Chi tiết từng khối lệnh AOI trong Chương 1

### 2.1. Basic Analog Input (`P_AIn` — Trang 21–25)
* **Mô tả chức năng (Functional Description):**
  * Quy đổi tuyến tính (Linear Scaling) giá trị đầu vào từ đơn vị thô của thẻ I/O (`Cfg_InpRawMin` ... `Cfg_InpRawMax`) sang đơn vị kỹ thuật hiển thị (`Cfg_EUMin` ... `Cfg_EUMax`).
  * Tích hợp bộ lọc số bậc 1 (First-order lag filter) làm mịn tín hiệu.
  * Cung cấp 4 ngưỡng trạng thái và cảnh báo: **High-High (`HiHi`)**, **High (`Hi`)**, **Low (`Lo`)**, **Low-Low (`LoLo`)** với vùng chết (Deadband) và thời gian trễ (Delay) cấu hình riêng biệt.
  * Cho phép đặt ngưỡng cảnh báo từ người vận hành (Operator) hoặc từ chương trình (Program) thông qua mô hình nguồn lệnh chuẩn `P_CmdSrc`.
  * Kiểm tra lỗi đầu vào vượt dải cao (Out-of-range High), vượt dải thấp (Out-of-range Low), số thực không hợp lệ (NaN / Infinite) và xuất cờ chất lượng `Sts_PVBad` cùng cảnh báo `Fail`.
  * Hỗ trợ chức năng **Substitute PV (Giá trị thay thế)** cho nhân viên bảo trì (Maintenance) ghi đè giá trị PV thủ công khi cảm biến đang bảo dưỡng.
* **Tệp bắt buộc:** `P_AIn_4.10.00_AOI.L5X`
* **Bảng Cảnh báo (`Table 2 - P_AIn Alarms` — Trang 23):**
  * **`Fail`** (`P_Alarm`: `Fail`, `P_Gate`: `FailGate`): Kích hoạt khi chất lượng PV xấu, `Inp_PVBad = 1`, PV vượt ngoài giới hạn lỗi cấu hình, lỗi số thực dấu phẩy động (NaN/Inf), hoặc cấu hình dải Raw/EU không hợp lệ.
  * **`High PV`** (`Hi` / `HiGate`): Khi `PV > Ngưỡng High` (có áp dụng Deadband, Gating và Delay).
  * **`High-High PV`** (`HiHi` / `HiHiGate`): Khi `PV > Ngưỡng High-High`.
  * **`Low PV`** (`Lo` / `LoGate`): Khi `PV < Ngưỡng Low`.
  * **`Low-Low PV`** (`LoLo` / `LoLoGate`): Khi `PV < Ngưỡng Low-Low`.
* **Mô phỏng (Simulation — Trang 24):** Đặt tham số `Inp_Sim = 1` trong Controller để vô hiệu hóa đầu vào thực `Inp_PV` và cho phép nhập giá trị PV giả lập trực tiếp trên hộp thoại Faceplate HMI (hiển thị biểu tượng Simulation ở góc trên bên trái).
* **Điều kiện thực thi (Execution — Trang 23):**
  * **`EnableIn False` (Rung sai):** Hiển thị trạng thái chất lượng xấu (`Sts_PVBad = 1`) trên HMI, xóa toàn bộ cảnh báo. Giá trị `Val_InpPV` vẫn được tính toán để người vận hành thấy giá trị thực tế, nhưng đầu ra chính `Val` giữ nguyên giá trị cuối cùng (Hold last value).
  * **`Powerup` (Prescan / First Scan):** Mọi lệnh gửi trước chu kỳ quét đầu tiên đều bị hủy bỏ; các khối `P_Alarm` nội bộ khởi tạo theo quy trình chuẩn.
* **Ví dụ lập trình (Programming Example — Trang 24–25):** Đọc cảm biến nhiệt độ lò nung (Oven Temperature `TI910`):
  * Nối tín hiệu từ transmitter vào `Inp_PV`, nối bit báo lỗi kênh I/O vào `Inp_PVBad`.
  * Nối đầu ra `Val` và `Sts_PVBad` sang chân `PV` và `PVFault` của khối điều khiển `PIDE`.
  * Cấu hình: `Cfg_InpRawMin = 0`, `Cfg_EUMin = 0`, `Cfg_InpRawMax = 300`, `Cfg_EUMax = 300`; thẻ chuỗi hiển thị: `Cfg_Desc = "Oven Temperature"`, `Cfg_EU = "Deg C"`, `Cfg_Label = "Oven Temp"`, `Cfg_Tag = "TI910"`.

### 2.2. Analog Input Channel (`P_AIChan` — Trang 26–29)
* **Mô tả chức năng:** Giám sát chuyên sâu 1 kênh đầu vào tương tự từ module I/O cho 9 tình huống bất thường:
  1. Cấu hình không hợp lệ (Invalid configuration).
  2. Lỗi module I/O (I/O module fault).
  3. Đầu vào vượt dải (Input out of range).
  4. Thiết bị đo báo: Ngoài đặc tính kỹ thuật (*Out of specification - uncertain*).
  5. Thiết bị đo báo: Đang kiểm tra chức năng (*Function check* — nhập PV thay thế thủ công).
  6. Thiết bị đo báo: Yêu cầu bảo trì (*Maintenance required*).
  7. Lỗi kênh vật lý (Channel fault).
  8. Giá trị không phải là số (*Not-a-number / floating point exception*).
  9. Tín hiệu bị treo/đóng băng (*Input stuck / unchanging* trong khoảng thời gian cấu hình).
* **Phân loại chất lượng PV (PV Quality):** Với mỗi tình huống trên, kỹ sư có thể cấu hình mức chất lượng báo cáo là **Good (Tốt)**, **Uncertain (Không chắc chắn)** hoặc **Bad (Xấu — kích hoạt cảnh báo `Fail`)**.
* **Tệp bắt buộc:** `P_AIChan_4.10.00_AOI.L5X`
* **Cảnh báo:** Duy nhất cảnh báo **`Fail`** (khi chất lượng kênh rơi vào trạng thái Bad).

### 2.3. Advanced Analog Input (`P_AInAdv` — Trang 30–34)
* **Mô tả chức năng mở rộng so với `P_AIn`:**
  * Chuyển đổi tuyến tính hoặc **khai căn bậc hai (Square Root Characterization)** chuyên dùng cho phép đo lưu lượng qua tấm chắn Orifice / ống Venturi (lưu lượng tỷ lệ với căn bậc hai của độ chênh áp $\Delta P$).
  * Bổ sung ngưỡng cắt lưu lượng nhỏ (**Low Flow Cutoff**): Khi tín hiệu gần bằng 0, ép đầu ra về 0 để tránh tích lũy lưu lượng ảo do trôi điểm 0 của cảm biến chênh áp.
  * Bổ sung 2 cảnh báo tốc độ biến thiên: **High Rate-of-Change (`HiRoC`)** và **Low (Negative) Rate-of-Change (`LoRoC`)**.
  * Bổ sung 2 cảnh báo độ lệch so với giá trị tham chiếu (`Inp_DevRef`): **High Deviation (`HiDev`)** và **Low Deviation (`LoDev`)**.
  * Hỗ trợ cổng điều kiện (Gate input) riêng biệt để ức chế từng nhóm cảnh báo khi thiết bị công nghệ đang dừng (ví dụ: không báo áp suất xả thấp khi bơm đang tắt).
* **Tệp bắt buộc:** `P_AInAdv_4.10.00_AOI.L5X`

### 2.4. Dual Sensor Analog Input (`P_AInDual` — Trang 35–38)
* **Mô tả chức năng:** Giám sát 1 biến quá trình (PV) thông qua 2 cảm biến độc lập (`Inp_PVA` và `Inp_PVB`).
  * Quy đổi tuyến tính và lọc nhiễu riêng cho từng kênh A và B.
  * Cho phép Operator hoặc Program chọn giá trị đại diện làm PV đầu ra: **Chỉ kênh A**, **Chỉ kênh B**, **Trung bình cộng (Average)**, **Giá trị lớn hơn (High Select)** hoặc **Giá trị nhỏ hơn (Low Select)**.
  * Tự động chuyển mạch thông minh: Nếu cảm biến đang dùng bị lỗi (`Bad Quality`) và chế độ chọn là Average/Min/Max, khối tự động chuyển sang dùng cảm biến còn lại đang tốt mà không làm gián đoạn vòng điều khiển.
  * Cảnh báo chênh lệch cảm biến (**Sensor Difference Alarm - `Diff`**): Kích hoạt khi $|PV_A - PV_B|$ vượt quá ngưỡng cho phép `Cfg_DiffLim`, cảnh báo sớm hiện tượng trôi cảm biến.
* **Tệp bắt buộc:** `P_AInDual_4.10.00_AOI.L5X`

### 2.5. Multiple Analog Input (`P_AInMulti` — Trang 39–45)
* **Mô tả chức năng:** Giám sát 1 biến quá trình sử dụng từ **2 đến 8 cảm biến đầu vào** (`Inp_PV0` ... `Inp_PV7`).
  * Mỗi đầu vào có bộ quy đổi tuyến tính (Scaling) riêng và cờ cho phép đưa vào phép tính (Include/Exclude).
  * Các chế độ tính toán PV tổng hợp: **Trung bình cộng (Average)**, **Trung vị (Median — loại bỏ nhiễu xung đột biến cực tốt)**, **Giá trị nhỏ nhất (Minimum)**, **Giá trị lớn nhất (Maximum)**, hoặc chọn đích danh 1 cảm biến từ 1 đến 8.
  * Cấu hình số lượng cảm biến tốt tối thiểu (`Cfg_MinGood`): Nếu số cảm biến hoạt động tốt rơi xuống dưới ngưỡng này, đầu ra PV được đánh dấu là `Bad` và kích hoạt cảnh báo `Fail`.
  * Tự động loại bỏ cảm biến hỏng hoặc cảm biến có độ lệch vượt ngưỡng (`Outlier Rejection`) ra khỏi phép tính trung bình.
* **Tệp bắt buộc:** `P_AInMulti_4.10.00_AOI.L5X`

### 2.6. Discrete Input Object (`P_DIn` — Trang 46–52)
* **Mô tả chức năng:** Xử lý 1 tín hiệu logic rời rạc `BOOL` (`Inp_PV`).
  * Cho phép đặt tên hiển thị trạng thái 0 và trạng thái 1 tùy ý (ví dụ: `"Closed"` / `"Open"`, `"Normal"` / `"Tripped"`).
  * Hỗ trợ đảo tín hiệu đầu vào (`Cfg_Inv`), lọc chống rung tiếp điểm (**Debounce On-Delay** và **Off-Delay**).
  * Kiểm tra trạng thái chuẩn (Target Agree / Disagree): So sánh đầu vào với trạng thái mong muốn để phát hiện điều kiện bất thường và kích hoạt cảnh báo `Alm` (qua khối `P_Gate` và `P_Alarm` nhúng bên trong).
  * Hỗ trợ nhập trạng thái thay thế (Substitute PV) ở chế độ Maintenance và mô phỏng ở chế độ Simulation (`Inp_Sim = 1`).
* **Tệp bắt buộc:** `P_DIn_4.10.00_AOI.L5X`

### 2.7. Discrete Input Object Advanced (`P_DInAdv` — Trang 53–57)
* **Mô tả chức năng nâng cao so với `P_DIn`:**
  * **Chức năng Công tắc tốc độ (Speed Switch):** Kết nối thêm chân phản hồi trạng thái chạy của động cơ (`Inp_MotorRun`). Khi động cơ bắt đầu chạy, sau thời gian trễ khởi động, nếu tín hiệu xung/tiếp điểm từ công tắc tốc độ gắn ở trục bị động không báo đạt tốc độ (ví dụ do đứt dây đai hoặc gãy trục khớp nối trong khi contactor vẫn hút), khối sẽ báo trạng thái bất thường và kích hoạt cảnh báo ngay lập tức.
  * **Cảnh báo kép (Dual Abnormal Situation Indication):** Cho phép cấu hình 2 điều kiện cảnh báo độc lập (`Alm1` và `Alm2`) trên cùng một điểm đo rời rạc.
* **Tệp bắt buộc:** `P_DInAdv_4.10.00_AOI.L5X`

### 2.8. Discrete Output (`P_DOut` — Trang 58–62)
* **Mô tả chức năng:** Điều khiển 1 thiết bị đầu ra số (`Out`) và tùy chọn giám sát tín hiệu phản hồi (`Inp_Fdbk`) để phát hiện lỗi cơ cấu chấp hành.
  * Các chế độ xuất xung đa dạng: **Giữ liên tục (Steady On / Steady Off)**, **Xung đơn bật/tắt (Single Pulse On / Single Pulse Off)** với độ rộng xung cấu hình được, hoặc **Chuỗi xung liên tục (Continually Pulsed / PWM chậm)** với chu kỳ On/Off cấu hình riêng.
  * Hỗ trợ đầy đủ các nguồn lệnh: Operator, Program, External, Override, Maintenance, Hand, Out of Service.
  * Kết nối trực tiếp với khối liên động `P_Intlk` để cưỡng bức đưa đầu ra về trạng thái an toàn khi có sự cố.
* **Tệp bắt buộc:** `P_DOut_4.10.00_AOI.L5X`

### 2.9. Analog Output (`P_AOut` — Trang 63–67)
* **Mô tả chức năng:** Điều khiển trực tiếp 1 đầu ra tương tự (`Out_CV`), đóng vai trò như một trạm điều khiển bằng tay (**Manual Loading Station**) hoặc bộ đệm đầu ra cho thiết bị hiện trường.
  * Hỗ trợ giới hạn biên độ đầu ra (`Cfg_MinCV` ... `Cfg_MaxCV`) và giới hạn tốc độ tăng/giảm (**Rate-of-Change Limiting** / Ramping) giúp cơ cấu chấp hành thay đổi êm dịu.
  * Quy đổi tuyến tính từ đơn vị kỹ thuật (ví dụ `0...100%`) sang giá trị thô của thẻ Analog Output (`4...20mA` hoặc `0...32767`).
  * Hỗ trợ liên động an toàn (`Inp_IntlkOK`): Khi mất liên động, ép đầu ra CV về giá trị an toàn định trước (`Cfg_IntlkCV`) hoặc giữ nguyên giá trị cuối cùng.
  * Hỗ trợ phản hồi vị trí/đầu ra thực tế (`Inp_Fdbk`) để hiển thị so sánh trên HMI.
* **Tệp bắt buộc:** `P_AOut_4.10.00_AOI.L5X`

### 2.10. Pressure/Temp. Compensated Flow (`P_PTComp` — Trang 68–71)
* **Mô tả chức năng:** Khối thuần túy tính toán toán học (không có HMI Faceplate riêng) dùng để quy đổi lưu lượng khí từ điều kiện làm việc thực tế sang lưu lượng ở **Điều kiện tiêu chuẩn (STP — Standard Temperature and Pressure)**, tương đương với lưu lượng khối lượng.
  * Hỗ trợ 2 dạng đầu vào lưu lượng:
    1. Lưu lượng kế đo thể tích (Volumetric Flowmeter — như Turbine, Vortex): $Q_{std} = Q_{act} 	imes rac{P_{act} + P_{atm}}{P_{std}} 	imes rac{T_{std}}{T_{act} + T_{offset}}$.
    2. Lưu lượng kế chênh áp (Differential Pressure — Orifice): $Q_{std} = K 	imes \sqrt{\Delta P 	imes rac{P_{act} + P_{atm}}{T_{act} + T_{offset}}}$.
  * Đầu ra của `P_PTComp` thường được nối tiếp vào chân `Inp_PV` của khối `P_AIn` để hiển thị lên HMI và tạo cảnh báo.
* **Tệp bắt buộc:** `P_PTComp_4.10.00_AOI.L5X`

### 2.11. Tank Strapping Table (`P_StrapTbl` — Trang 72–75)
* **Mô tả chức năng:** Khối tính toán thể tích chất lỏng thực tế trong bồn trụ đứng dựa trên mức đo chiều cao (`Inp_Level`) và bảng dung tích hiệu chuẩn bồn (**Strapping Table**).
  * Nội suy tuyến tính từng đoạn qua bảng điểm hiệu chuẩn của bồn.
  * **Bù lớp nước tự do dưới đáy bồn (Free Water Compensation):** Nếu có cảm biến đo mức phân giới dầu/nước (`Inp_WaterLevel`), khối tự động trừ đi thể tích nước lắng đáy để ra thể tích sản phẩm tinh.
  * **Bù giãn nở nhiệt thành bồn (Thermal Expansion of Tank Shell):** Tính toán sự thay đổi tiết diện ngang của bồn thép theo hệ số giãn nở nhiệt vật liệu, nhiệt độ môi trường và nhiệt độ chất lỏng.
  * **Bù trọng lượng mái phao (Floating Roof Compensation):** Trừ đi thể tích chất lỏng bị chiếm chỗ bởi phần chìm của mái phao dựa trên tỷ trọng sản phẩm (`Inp_Density`).
* **Tệp bắt buộc:** `P_StrapTbl_4.10.00_AOI.L5X`

### 2.12. HART Analog Input & Output (`P_AInHART` & `P_AOutHART` — Trang 76)
* Tài liệu chỉ dẫn toàn bộ thông tin chi tiết của hai khối này đã được tách riêng sang ấn phẩm chuyên ngành: **Publication `PROCES-RM010` (*Rockwell Automation Library of Process Objects: HART Modules for PlantPAx DCS*)**.

# CHƯƠNG 2: ĐIỀU KHIỂN ĐIỀU TIẾT & QUY TRÌNH (REGULATORY & PROCEDURAL CONTROL — TRANG 77–118)

## 1. Phân loại nhóm điều khiển nâng cao (Purpose - Trang 77–78)
Chương 2 chia các khối điều khiển quá trình thành 2 mảng chiến lược:
1. **Điều khiển điều tiết (Regulatory Control):** Tập trung ổn định các biến quá trình liên tục (mức, lưu lượng, nhiệt độ, áp suất) bằng các kỹ thuật điều khiển tỷ lệ (Ratio), tiến cử (Feedforward), tầng (Cascade), chia dải (Split-range) và chọn ngưỡng (Override Selector). Bao gồm: **`P_PIDE`**, **`P_Fanout`**, **`P_HiLoSel`**, **`P_DBC`**.
2. **Điều khiển quy trình/thủ tục (Procedural Control):** Tập trung vào sản phẩm đầu ra thông qua chuỗi các bước rời rạc (Sequencing), định lượng mẻ (Batch Dosing) và điều phối cụm thiết bị động lực. Bao gồm: **`P_Seq`**, **`P_Dose`**, **`P_LLS`**.

---

## 2. Chi tiết từng khối lệnh AOI trong Chương 2

### 2.1. Proportional + Integral + Derivative Enhanced (`P_PIDE` — Trang 79–83)
* **Mô tả chức năng:** Bao bọc khối hàm `PIDE` tích hợp sẵn của Studio 5000 Logix Designer trong giao diện chuẩn hóa của thư viện PlantPAx.
  * Cung cấp đầy đủ thuật toán PID nâng cao (Velocity form, Independent/Dependent gains, Adaptive gains, Feedforward, Ratio control, Cascade control, Zero-crossing deadband).
  * Tích hợp đối tượng cảnh báo thư viện cho 4 ngưỡng sai lệch (**Deviation Alarms**: `HiHiDev`, `HiDev`, `LoDev`, `LoLoDev`), cảnh báo lỗi thuật toán (`Fail`) và cảnh báo ngắt liên động (`IntlkTrip`).
  * Quản lý quyền điều khiển qua khối `P_CmdSrc` (Operator, Program, Cascade/External, Override, Maintenance, Hand, Out of Service).
  * Hỗ trợ **Setpoint Ramping** (thay đổi giá trị đặt SP theo đường dốc thời gian xác định để tránh vọt lố).
  * Cấu hình trạng thái khởi động khi có điện lại (Power-up Setpoint, Output CV và Loop Mode).
* **Hướng dẫn kích hoạt Autotune (Tự động dò thông số PID — Trang 80):**
  1. Cần có giấy phép (License) PIDE Autotune trong Studio 5000.
  2. Mở routine logic của khối con `P_PIDE_only`.
  3. Chỉnh sửa sơ đồ khối hàm (FBD), gán thẻ Autotune vào tham chiếu `Ref_Autotune`.
  4. Lưu và nạp (Download) xuống bộ điều khiển; khối `P_PIDE` bên ngoài sẽ tự động nhận diện khi khởi động và bật sáng nút **Autotune** trên HMI Faceplate.
* **Tệp bắt buộc:** `P_PIDE_4.10.00_AOI.L5X`
* **Bảng Cảnh báo (`Table 8 - P_PIDE Alarms` — Trang 81):**
  * **`Fail`**: Kích hoạt khi khối PIDE nội bộ báo lỗi (Setpoint hoặc PV có chất lượng xấu/lỗi truyền thông Cascade, cấu hình dải EU không hợp lệ, lỗi tín hiệu CV, Feedforward hoặc Hand Feedback).
  * **`Interlock Trip` (`IntlkTrip`)**: Khi điều kiện liên động `Not OK` buộc đầu ra CV chuyển về giá trị an toàn `Cfg_IntlkCV` hoặc giữ nguyên giá trị cuối.
  * **`High Deviation` (`HiDev`)** & **`High-High Deviation` (`HiHiDev`)**: Khi độ lệch dương $(PV - SP)$ vượt ngưỡng cao.
  * **`Low Deviation` (`LoDev`)** & **`Low-Low Deviation` (`LoLoDev`)**: Khi độ lệch âm $(PV - SP)$ tụt xuống dưới ngưỡng thấp.
* **Điều kiện thực thi (Execution — Trang 82):** Khi `EnableIn False`, nguồn lệnh hiển thị là *Program Out of Service*, vòng lặp bị vô hiệu hóa và đầu ra `CV` được gán về giá trị `Cfg_IntlkCV`.
* **Ví dụ lập trình `P_PIDE` kết hợp biến tần `P_VSD` (Trang 82–83):**
  * Chân `Out_CV` của `P_PIDE` nối vào `PSet_SpeedRef` của `P_VSD`.
  * Chân `Sts_Available` của `P_VSD` (đảo bit) nối về `Inp_UseCVInitVal` của `P_PIDE`, còn `Val_SpeedRef` của `P_VSD` nối về `Inp_CVInitVal`. Nhờ đó, khi người vận hành chuyển biến tần sang chế độ tay (Operator), bộ PID tự động bám theo (Track) tốc độ thực của biến tần, đảm bảo chuyển mạch không giật (Bumpless Transfer) khi trả biến tần về chế độ tự động (Program).

### 2.2. Analog Fanout (`P_Fanout` — Trang 84–88)
* **Mô tả chức năng:** Phân nhánh 1 tín hiệu điều khiển sơ cấp (`Inp_CV`) ra tối đa **8 thiết bị thứ cấp** (`Out_CV1` ... `Out_CV8`).
  * Áp dụng giới hạn tốc độ biến thiên (Rate-of-change limit) cho tín hiệu đầu vào chung.
  * Mỗi nhánh thứ cấp $i$ ($1 \dots 8$) có hệ số khuếch đại tỷ lệ (**Ratio / Slope $m_i$**), độ dịch điểm không (**Offset / Intercept $b_i$**) và giới hạn kẹp biên độ (**Clamp Limits Min/Max**) riêng biệt theo công thức: $CV_i = m_i 	imes CV_{primary} + b_i$.
  * Cho phép từng nhánh thứ cấp chuyển sang chế độ điều khiển riêng lẻ (Individual Override/Manual) mà không ảnh hưởng đến các nhánh còn lại.
  * Tính toán giá trị khởi tạo ngược (`Out_CVInitVal`) gửi trả về bộ PID sơ cấp khi tất cả các nhánh thứ cấp đều không ở chế độ nhận lệnh tự động.
* **Tệp bắt buộc:** `P_Fanout_4.10.00_AOI.L5X`

### 2.3. High or Low Selector (`P_HiLoSel` — Trang 89–91)
* **Mô tả chức năng:** Nhận tối đa **6 tín hiệu đầu vào CV** (`Inp_CV1` ... `Inp_CV6`) từ tối đa 6 bộ điều khiển PID khác nhau và chọn ra giá trị **Thấp nhất (Low Select)** hoặc **Cao nhất (High Select)** để xuất ra cơ cấu chấp hành duy nhất (`Out_CV`).
  * **Ứng dụng điển hình (Override Control — Trang 89):** Điều khiển tốc độ bơm bằng 3 bộ PID: (1) PID áp suất đầu đẩy, (2) PID giới hạn dòng điện động cơ, (3) PID bảo vệ áp suất đầu hút tối thiểu. Khối `P_HiLoSel` chọn giá trị nhỏ nhất (Low Select) trong 3 đầu ra PID để bình thường chạy theo áp suất đẩy, nhưng khi dòng động cơ quá cao hoặc áp suất hút quá thấp (nguy cơ xâm thực Cavitation), bộ PID bảo vệ tương ứng sẽ tự động chiếm quyền giảm tốc độ bơm.
  * **Chống bão hòa tích phân cho các PID không được chọn:** Xuất các cờ báo (`Out_CVxSel`) và giá trị bám (`Out_CVxTrack`) có cộng bù độ lệch để các bộ PID đang chờ không bị tích phân vọt lố (Windup).
* **Tệp bắt buộc:** `P_HiLoSel_4.10.00_AOI.L5X`

### 2.4. Deadband Controller (`P_DBC` — Trang 92–95)
* **Mô tả chức năng:** Bộ điều khiển đóng/ngắt có vùng trễ (còn gọi là *Bang-Bang Controller* hoặc *On-Off Controller*) sử dụng 1 hoặc 2 đầu ra rời rạc:
  * **`Out_Raise` (Tăng):** Kích hoạt khi $PV < 	ext{Ngưỡng Raise}$.
  * **`Out_Lower` (Giảm):** Kích hoạt khi $PV > 	ext{Ngưỡng Lower}$.
  * **`Out_Q` và `Out_QNot` (Điều khiển bơm nạp/xả kiểu Flip-Flop):** `Out_Q` bật khi $PV$ tụt xuống dưới ngưỡng Raise và giữ nguyên trạng thái bật cho đến khi $PV$ vượt lên trên ngưỡng Lower mới ngắt (ví dụ: điều khiển bơm làm đầy bồn hoặc xả cạn hố thu nước thải).
  * Tích hợp cảnh báo vượt ngưỡng `HiHi`, `Hi`, `Lo`, `LoLo`, `Fail` và `IntlkTrip`.
* **Tệp bắt buộc:** `P_DBC_4.10.00_AOI.L5X`

### 2.5. Sequencer Object (`P_Seq` — Trang 96–98)
* **Mô tả chức năng:** Giải pháp điều khiển tuần tự theo bước (Step Sequencing) đặt trực tiếp trong bộ điều khiển mà không cần viết lại code khi thay đổi quy trình công nghệ.
  * Quản lý chuỗi các bước thực thi dựa trên kiểu dữ liệu cấu trúc người dùng **`P_SeqStep`** (UDT).
  * Điều khiển tối đa **32 đầu ra Boolean** (phát lệnh đóng/mở van, bật/tắt bơm) và **32 đầu ra số thực REAL** (gửi giá trị đặt Setpoint cho các vòng PID, lưu lượng, nhiệt độ) ở mỗi bước.
  * Giám sát tối đa **32 điều kiện chuyển bước đầu vào Boolean** (Step Transition inputs) với mặt nạ cấu hình cho từng bước, kết hợp bộ định thời gian tối thiểu/tối đa cho mỗi bước (Step Timer & Max Step Time Alarm).
  * Hỗ trợ rẽ nhánh (Branching / Jump to step), tạm dừng (Hold), tiếp tục (Resume), bước đơn (Single Step) và tích hợp trực tiếp với khối **`P_Prompt`** để hiển thị câu hỏi xác nhận cho người vận hành tại các bước cần thao tác thủ công.
* **Tệp bắt buộc:** `P_Seq_4.10.00_AOI.L5X`

### 2.6. Dosing (`P_Dose` — Trang 99–106)
* **Mô tả chức năng:** Điều khiển định lượng chiết rót nguyên liệu chính xác sử dụng **Đồng hồ lưu lượng (Flowmeter — `P_DoseFM`)** hoặc **Cân trọng lượng (Weigh Scale — `P_DoseWS`)**.
  * Hỗ trợ định lượng 2 cấp tốc độ: **Nạp nhanh (Fast Feed / Bulk)** ở giai đoạn đầu và chuyển sang **Nạp tinh/nhỏ giọt (Slow Feed / Dribble)** khi gần đạt khối lượng đặt.
  * **Bù lượng rơi tự do (Pre-Act / Slop / In-Flight Compensation):** Đóng van nạp sớm trước khi đạt khối lượng mục tiêu một lượng bằng lượng vật liệu còn đang lơ lửng trên đường ống, đồng thời có thuật toán tự động hiệu chỉnh hệ số bù sau mỗi mẻ dựa trên sai số thực tế của mẻ trước.
  * Hỗ trợ kiểm tra độ kín van (**Bump / Leak Check**) và cảnh báo quá lượng nạp (`Overrun`), thiếu lượng nạp (`Underrun`), lưu lượng quá thấp hoặc mất liên động.
* **Tệp bắt buộc:** `P_DoseFM_4.10.00_AOI.L5X` và `P_DoseWS_4.10.00_AOI.L5X`

### 2.7. Lead/Lag/Standby Motor Group (`P_LLS` — Trang 107–118)
* **Mô tả chức năng:** Điều khiển và phân phối tải cho một cụm từ **2 đến 30 động cơ/bơm mắc song song** cùng cấp vào một đường ống góp chung (Header).
  * Cho phép Operator hoặc Program nhập **Số lượng động cơ yêu cầu chạy (`Demand`)** từ `0` đến `Cfg_MaxDemand`.
  * Thiết lập thời gian trễ giữa các lần khởi động liên tiếp (`Cfg_StartDelayT`) và trễ giữa các lần dừng (`Cfg_StopDelayT`) để tránh sụt áp lưới điện và sốc áp thủy lực.
  * Nếu một bơm đang chạy bị lỗi (Trip / Không khả dụng), khối lập tức gọi bơm dự phòng (**Standby**) tiếp theo vào thay thế tự động.
  * Hỗ trợ điều khiển tốc độ chung cho các biến tần trong nhóm và tự động tính toán tăng/giảm tốc độ khi thêm hoặc bớt bơm đang chạy.
* **Thuật toán sắp xếp thứ tự ưu tiên động cơ (`Motor Sort Algorithm` — Trang 117):**
  * Xếp hạng động cơ nào sẽ được gọi chạy trước (First to Start) và động cơ nào được gọi dừng trước (First to Stop) dựa trên:
    1. Chế độ **First-On / First-Off (FIFO)** hoặc **First-On / Last-Off (FILO)**.
    2. **Thứ tự ưu tiên cấu hình (`Priority`)**: Nhân viên vận hành gán số thứ tự ưu tiên 1, 2, 3...
    3. **Cân bằng thời gian chạy (`Run Time` / `Wear Leveling`)**: Kết nối với khối `P_RunTime` của từng động cơ để ưu tiên gọi động cơ có số giờ tích lũy ít nhất chạy trước và dừng động cơ có số giờ chạy nhiều nhất trước.
* **Tệp bắt buộc:** `P_LLS_4.10.00_AOI.L5X`

# CHƯƠNG 3: ĐỘNG CƠ, BIẾN TẦN, KHỞI ĐỘNG MỀM & RƠ-LE QUÁ TẢI (MOTORS — TRANG 119–278)

## 1. Bảng phân loại toàn bộ 17 khối lệnh trong Chương 3 (Purpose & Table 10 - Trang 119–126)

| STT | Khối lệnh AOI | Tệp Controller (`.L5X`) | Thiết bị điều khiển & Phạm vi ứng dụng |
| :--- | :--- | :--- | :--- |
| 1 | **`P_Motor`** | `P_Motor_4.10.00_AOI.L5X` | Động cơ 1 tốc độ, không đảo chiều (khởi động trực tiếp FVNR, khởi động mềm hoặc biến tần điều khiển qua tiếp điểm số). |
| 2 | **`P_Motor2Spd`** | `P_Motor2Spd_4.10.00_AOI.L5X` | Động cơ 2 cấp tốc độ (Fast / Slow / Stopped), không đảo chiều. |
| 3 | **`P_MotorRev`** | `P_MotorRev_4.10.00_AOI.L5X` | Động cơ đảo chiều quay (Forward / Reverse / Stopped), 1 cấp tốc độ (FVR). |
| 4 | **`P_MotorHO`** | `P_MotorHO_4.10.00_AOI.L5X` | Động cơ điều khiển tại chỗ bằng tay (Hand-operated), PLC chỉ giám sát trạng thái chạy và tùy chọn gửi lệnh ngắt (Trip). |
| 5 | **`P_PF52x`** | `P_PF52x_4.10.00_RUNG.L5X` | Biến tần hạ thế **PowerFlex 523 / PowerFlex 525** giao tiếp qua mạng EtherNet/IP. |
| 6 | **`P_PF753`** | `P_PF753_4.10.00_RUNG.L5X` | Biến tần hạ thế **PowerFlex 753** giao tiếp qua mạng EtherNet/IP hoặc ControlNet. |
| 7 | **`P_PF755`** | `P_PF755_4.10.00_RUNG.L5X` | Biến tần hạ thế **PowerFlex 755, 755TL, 755TR** (hỗ trợ chỉnh lưu tích cực / trả năng lượng về lưới). |
| 8 | **`P_PF6000`** | `P_PF6000_4.10.00_RUNG.L5X` | Biến tần trung thế **PowerFlex 6000** (Medium Voltage AC Drive). |
| 9 | **`P_PF7000`** | `P_PF7000_4.10.00_RUNG.L5X` | Biến tần trung thế **PowerFlex 7000** (Medium Voltage AC Drive). |
| 10 | **`P_SMC50`** | `P_SMC50_4.10.00_RUNG.L5X` | Bộ khởi động mềm thông minh **SMC-50 Smart Motor Controller**. |
| 11 | **`P_SMCFlex`** | `P_SMCFlex_4.10.00_RUNG.L5X` | Bộ khởi động mềm thông minh **SMC Flex Smart Motor Controller**. |
| 12 | **`P_VSD`** | `P_VSD_4.10.00_AOI.L5X` | Biến tần đa năng tổng quát (điều khiển mọi biến tần AC/DC qua dây cứng I/O hoặc biến tần đời cũ Bulletin 1336, PowerFlex 4/40/70/700). |
| 13 | **`P_E1PlusE`** | `P_E1PlusE_4.10.00_RUNG.L5X` | Rơ-le nhiệt điện tử **E1 Plus** có gắn module truyền thông EtherNet/IP `193-ETN`. |
| 14 | **`P_E3Ovld`** | `P_E3Ovld_4.10.00_RUNG.L5X` | Rơ-le bảo vệ quá tải điện tử **E3 / E3 Plus** (`193/592-EC1, -EC2, -EC3, -EC5`) qua DeviceNet hoặc `2100-ENET`. |
| 15 | **`P_E300Ovld`** | `P_E300Ovld_4.10.00_RUNG.L5X` | Rơ-le bảo vệ quá tải điện tử thế hệ mới **E300** (`193-ECM-ETR`) trên mạng EtherNet/IP. |
| 16 | **`P_RunTime`** | `P_RunTime_4.10.00_AOI.L5X` | Bộ đếm tổng thời gian vận hành (Runtime Hours) và tổng số lần khởi động (Start Counter) phục vụ bảo trì. |
| 17 | **`P_ResInh`** | `P_ResInh_4.10.00_AOI.L5X` | Khối khóa cấm khởi động lại (Restart Inhibit) bảo vệ nhiệt cho động cơ công suất lớn tránh khởi động lặp lại liên tục. |

> **LƯU Ý AN TOÀN QUAN TRỌNG CỦA ROCKWELL AUTOMATION (Trang 127):** Chức năng đưa thiết bị ra khỏi vận hành (*Out of Service*) hoặc khóa liên động trên phần mềm **KHÔNG** thay thế cho quy trình khóa cô lập nguồn năng lượng cơ-điện (**Hard Lockout/Tagout - LOTO**) khi bảo dưỡng.

---

## 2. Nhóm Động cơ điều khiển tiếp điểm (Discrete Motors — Trang 127–142)

### 2.1. Single-speed Motor (`P_Motor` — Trang 127–131)
* **Mô tả chức năng:**
  * Xuất lệnh chạy/dừng động cơ hỗ trợ cả 2 kiểu mạch khởi động từ: **Mạch duy trì (Held output — `Out_Run`)** và **Mạch tự giữ xung (Latching starter circuit — xung `Out_Start` và xung `Out_Stop`)** cộng với tính năng nhấp thử (**Jog**).
  * Giám sát tiếp điểm phụ phản hồi chạy (`Inp_RunFdbk` khi bật `Cfg_HasRunFdbk = 1` và `Cfg_UseRunFdbk = 1`).
  * Kiểm tra điều kiện cho phép khởi động (`Inp_PermOK`) và điều kiện khóa liên động (`Inp_IntlkOK`).
  * Hỗ trợ đầy đủ 7 chế độ nguồn lệnh: Operator, Program, External, Override, Maintenance, Hand, Out of Service.
* **Bảng Cảnh báo (`P_Motor` — Trang 129):**
  * **`Fail to Start` (`FailToStart`)**: Đã xuất lệnh chạy nhưng sau thời gian trễ `Cfg_FailToStartT` vẫn không nhận được phản hồi `Inp_RunFdbk = 1`. Nếu cấu hình là lỗi ngắt tải (Shed fault), động cơ bị cắt lệnh chạy và yêu cầu Reset mới cho chạy lại.
  * **`Fail to Stop` (`FailToStop`)**: Đã ngắt lệnh chạy nhưng sau thời gian trễ `Cfg_FailToStopT` tín hiệu `Inp_RunFdbk` vẫn báo đang chạy (dính tiếp điểm contactor).
  * **`Interlock Trip` (`IntlkTrip`)**: Động cơ đang chạy bị ngắt do mất điều kiện liên động.
  * **`I/O Fault` (`IOFault`)**: Khi `Inp_IOFault = 1` báo lỗi truyền thông module I/O.
* **Mô phỏng (`Simulation` — Trang 130):** Khi `Inp_Sim = 1`, ngõ ra vật lý bị giữ ở 0 (không cấp điện ra hiện trường), khối tự phản hồi trạng thái `"Starting"` -> `"Running"` hoặc `"Stopping"` -> `"Stopped"` sau thời gian trễ giả lập `Cfg_SimFdbkT` (giây).

### 2.2. Two-speed Motor (`P_Motor2Spd` — Trang 132–135)
* **Mô tả chức năng:** Điều khiển động cơ 2 cấp tốc độ (**Fast / Slow / Stopped**) với 2 ngõ ra cuộn dây `Out_Fast` và `Out_Slow`, cùng 2 ngõ vào phản hồi `Inp_FastFdbk` và `Inp_SlowFdbk`.
* **Chuyển tốc độ an toàn:** Có tham số cấu hình cho phép chuyển trực tiếp từ Nhanh sang Chậm hoặc bắt buộc phải dừng và chờ hết thời gian trễ giảm tốc (`Coast time`) trước khi đóng cuộn dây tốc độ thấp để tránh sốc cơ khí và dòng điện ngược.
* **Điều kiện cho phép riêng biệt:** Có chân `Inp_FastPermOK` và `Inp_SlowPermOK` riêng cho từng cấp tốc độ.

### 2.3. Reversing Motor (`P_MotorRev` — Trang 136–139)
* **Mô tả chức năng:** Điều khiển động cơ quay Thuận / Nghịch (**Forward / Reverse / Stopped**) với 2 ngõ ra `Out_Fwd` và `Out_Rev`, cùng 2 ngõ vào phản hồi `Inp_FwdFdbk` và `Inp_RevFdbk`.
* **Bảo vệ đảo chiều:** Cấu hình thời gian trễ dừng hãm bắt buộc khi đảo chiều từ Thuận sang Nghịch (hoặc ngược lại) để bảo vệ hộp số và tránh ngắn mạch 2 contactor. Hỗ trợ `Inp_FwdPermOK`, `Inp_RevPermOK` và liên động riêng theo từng chiều quay.

### 2.4. Hand-operated Motor (`P_MotorHO` — Trang 140–142)
* **Mô tả chức năng:** Giám sát động cơ được điều khiển đóng/cắt bằng công tắc cơ khí tại tủ điện hiện trường (Local MCC).
  * Nhận phản hồi chạy 1 tốc độ, 2 tốc độ hoặc thuận/nghịch để hiển thị trạng thái lên SCADA.
  * Tùy chọn cung cấp 1 ngõ ra cắt bảo vệ (`Out_Trip`): Cho phép chương trình tự động (khi vi phạm liên động `Inp_IntlkOK`) hoặc người vận hành trên HMI gửi xung cắt cưỡng bức động cơ tại chỗ khi khẩn cấp.

---

## 3. Nhóm Biến tần PowerFlex & Khởi động mềm SMC (Drives & Smart Starters — Trang 143–251)

### 3.1. PowerFlex 523/525 Drives (`P_PF52x` — Trang 143–157)
* **Tệp bắt buộc:** `P_PF52x_4.10.00_RUNG.L5X` (Nhập trực tiếp Rung mẫu đã gắn sẵn cấu trúc thẻ).
* **Cấu hình Datalinks bắt buộc trên biến tần PowerFlex 523/525 (Trang 144):**
  * **Input Assembly:** Drive Status (chuẩn), Output Frequency (chuẩn) + **Datalink 1:** `Fault 1 Code (Par 007)`, **Datalink 2:** `Output Current (Par 003)`, **Datalink 3:** `Output Power (Par 017)`, **Datalink 4:** Tùy chọn người dùng.
  * **Output Assembly:** Drive Logic Command (chuẩn), Frequency Command / Speed Reference (chuẩn) + 4 Output Datalinks tùy chọn.
* **Cấu trúc tham số InOut (`InOut Structure` — Trang 145):**
  * `Inp` (kiểu `P_PF52x_Inp`): Ánh xạ phần chung của Input Assembly biến tần.
  * `Out` (kiểu `P_PF52x_Out`): Ánh xạ phần chung của Output Assembly biến tần.
  * `Ref_FaultCodeList` (kiểu `P_DescList[1]`, mặc định nối với thẻ `PF525_FaultCodeList`): Mảng tra cứu dịch mã lỗi số nguyên (`DINT`) từ biến tần sang chuỗi văn bản mô tả lỗi (`STRING`) hiển thị trực tiếp trên HMI Faceplate.

### 3.2. PowerFlex 753 (`P_PF753` — Trang 158–173) & PowerFlex 755 (`P_PF755` — Trang 174–191)
* **Tệp bắt buộc:** `P_PF753_4.10.00_RUNG.L5X` và `P_PF755_4.10.00_RUNG.L5X`.
* **Khả năng chuyên sâu:**
  * Điều khiển chạy Thuận/Nghịch, Jog, đặt tốc độ tham chiếu (`SpeedRef`), giám sát trạng thái đang tăng tốc (Accelerating), đang giảm tốc (Decelerating), đang chạy đúng tốc độ (At Speed).
  * Giám sát trực tiếp dòng điện đầu ra (Output Current), công suất (Output Power), điện áp DC Bus, nhiệt độ biến tần và dịch toàn bộ mã Fault/Warning qua bảng `PF753_FaultCodeList` / `PF755_FaultCodeList`.
  * Riêng **`P_PF755`** hỗ trợ thêm các biến tần cấu hình **755TL (Low Harmonic)** và **755TR (Regenerative — trả năng lượng hãm tái sinh về lưới)**.
  * Mô phỏng (`Simulation`): Khi `Inp_Sim = 1`, khối mô phỏng quá trình tăng/giảm tốc tuyến tính theo thời gian dốc `Cfg_SimRampT`.

### 3.3. Biến tần trung thế PowerFlex 6000 (`P_PF6000` — Trang 192–203) & PowerFlex 7000 (`P_PF7000` — Trang 204–223)
* **Tệp bắt buộc:** `P_PF6000_4.10.00_RUNG.L5X` và `P_PF7000_4.10.00_RUNG.L5X`.
* **Đặc điểm trung thế (Medium Voltage 2.3kV – 11kV):** Giám sát chuyên sâu trạng thái cầu chỉnh lưu/nghịch lưu, máy cắt đầu vào trung thế (Medium Voltage Input Contactor/Breaker), cảnh báo quá tải nhiệt động cơ trung thế và giải mã bảng lỗi trung thế chuyên biệt.

### 3.4. Khởi động mềm thông minh SMC-50 (`P_SMC50` — Trang 224–229) & SMC Flex (`P_SMCFlex` — Trang 230–235)
* **Tệp bắt buộc:** `P_SMC50_4.10.00_RUNG.L5X` và `P_SMCFlex_4.10.00_RUNG.L5X`.
* **Chức năng:** Giao tiếp qua mạng với bộ khởi động mềm Allen-Bradley SMC-50 / SMC Flex để điều khiển Start, Stop, Jog (Slow speed), đọc dòng điện 3 pha, công suất, phần trăm dung lượng nhiệt tích lũy (% Thermal Capacity Used) và giải mã lỗi qua `SMC50_FaultCodeList` / `SMCFlex_FaultCodeList`.

### 3.5. Variable-Speed Drive tổng quát (`P_VSD` — Trang 236–251)
* **Tệp bắt buộc:** `P_VSD_4.10.00_AOI.L5X` (hoặc `P_VSD_4.10.00_RUNG.L5X`).
* **Phạm vi ứng dụng:** Dùng cho mọi loại biến tần bất kỳ (biến tần đấu dây cứng Hardwired qua thẻ DI/DO/AI/AO, biến tần hãng thứ 3, hoặc biến tần Allen-Bradley các dòng Bulletin 1336, PowerFlex 4/40/70/700/700S/DC).
* **Kết nối dây cứng (Hardwired Connections — Trang 238):** Cho phép nối các tín hiệu rời rạc (`Out_Start`, `Out_Stop`, `Out_ClearFault`, `Out_Fwd`, `Out_Rev`, `Inp_Running`, `Inp_Faulted`) và tín hiệu tương tự (`Out_SpeedRef`, `Inp_SpeedFdbk`).

---

## 4. Nhóm Rơ-le quá tải điện tử & Khối bảo vệ động cơ (Trang 252–278)

### 4.1. Rơ-le quá tải E1 Plus (`P_E1PlusE` — Trang 252–256), E3/E3Plus (`P_E3Ovld` — Trang 257–261) & E300 (`P_E300Ovld` — Trang 262–268)
* **Tệp bắt buộc:** `P_E1PlusE_4.10.00_RUNG.L5X`, `P_E3Ovld_4.10.00_RUNG.L5X`, `P_E300Ovld_4.10.00_RUNG.L5X`.
* **Các chức năng giám sát và bảo vệ (`P_E300Ovld` trên mạng EtherNet/IP `193-ECM-ETR`):**
  * Cảnh báo sớm nguy cơ sắp quá tải (**Warning of Impending Overloads**) và xác định nguyên nhân ngắt Trip (**Overload Trip Identification**).
  * Hiển thị phần trăm dòng điện tải (% Full Load Amperes - FLA), dòng điện từng pha ($I_{L1}, I_{L2}, I_{L3}$), phần trăm dung lượng nhiệt đã dùng (% Thermal Capacity Used), dòng rò chạm đất (Ground Fault Current) và độ mất cân bằng pha.
  * **Đồng hồ đếm ngược thời gian chờ phục hồi nhiệt (`Time until Overload Trip Can Be Reset`):** Cho người vận hành biết chính xác còn bao nhiêu giây nữa rơ-le mới nguội để cho phép nhấn Reset.
  * Hỗ trợ lệnh xóa lỗi từ xa (**Remote Reset**) và lệnh thử nghiệm ngắt từ xa (**Remote Test Trip**).

### 4.2. Runtime and Start Counter (`P_RunTime` — Trang 269–272)
* **Mô tả chức năng:** Đồng hồ đo giờ chạy cơ-điện tử bằng phần mềm gắn cho động cơ hoặc thiết bị công nghệ.
  * Tích lũy tổng số giờ vận hành (`Val_RunHrs`), ghi nhận thời gian của lần chạy gần nhất và đếm tổng số lần khởi động (`Val_Starts`).
  * Cung cấp ngưỡng cảnh báo nhắc lịch bảo trì định kỳ khi số giờ chạy vượt mốc cấu hình (`Max Run Hours`).
  * **Mẹo triển khai tiết kiệm chân (Implementation Using `EnableIn False` Feature — Trang 271):** Có thể mắc trực tiếp khối `P_RunTime` trên nhánh Rung Ladder nối tiếp ngay sau ngõ ra `Sts_Running` của động cơ; khi động cơ dừng (`EnableIn = False`), khối tự động ngừng cộng giờ chạy mà không cần nối dây vào chân `Inp_Run`.

### 4.3. Restart Inhibit for Large Motor (`P_ResInh` — Trang 273–278)
* **Mô tả nguyên lý bảo vệ nhiệt động cơ lớn:** Động cơ công suất lớn có dòng khởi động rất cao làm nóng cuộn dây nhanh chóng trong khi khối lượng nhiệt (Thermal mass) tương đối nhỏ. Nếu khởi động liên tiếp nhiều lần trong thời gian ngắn sẽ làm cháy cách điện cuộn dây.
* **Cơ chế hoạt động của `P_ResInh` (Trang 273–275):**
  * Lưu trữ mô hình bộ nhớ nhiệt khởi động của động cơ: Cho phép một số lần khởi động nguội ban đầu (ví dụ 2 lần khởi động liên tiếp khi động cơ đang nguội), nhưng bắt buộc có thời gian nghỉ tối thiểu giữa 2 lần chạy (`Min Stop Time`) và thời gian chờ tản nhiệt sau khi đã dùng hết số lần khởi động cho phép.
  * Xuất chân **`Out_OKToStart`** nối vào khối `P_Perm` của động cơ và hiển thị đồng hồ đếm ngược số phút/giây còn phải chờ trước khi được phép khởi động lại.

# CHƯƠNG 4: VAN ĐIỀU KHIỂN & CƠ CẤU CHẤP HÀNH (VALVES — TRANG 279–330)

## 1. Bảng phân loại toàn bộ 8 khối lệnh Van trong Chương 4 (Purpose & Table 17 - Trang 279–282)

| STT | Khối lệnh AOI | Tệp Controller (`.L5X`) | Loại van & Khi nào sử dụng |
| :--- | :--- | :--- | :--- |
| 1 | **`P_ValveC`** | `P_ValveC_4.10.00_AOI.L5X` | **Analog/Pulsed Control Valve:** Van điều khiển tuyến tính (nhận tín hiệu 4-20mA) hoặc van điều khiển từng nấc bằng xung đóng/mở (Ratcheting/Pulsed Open/Close), có giám sát phản hồi vị trí analog (`0...100%`) và công tắc hành trình. |
| 2 | **`P_ValveHO`** | `P_ValveHO_4.10.00_AOI.L5X` | **Hand-operated Valve:** Van thao tác bằng tay tại hiện trường (vô lăng, cần gạt), PLC giám sát công tắc hành trình Mở/Đóng và tùy chọn xuất 1 tín hiệu Solenoid cắt khẩn cấp (Trip). |
| 3 | **`P_ValveMO`** | `P_ValveMO_4.10.00_AOI.L5X` | **Motor-operated Valve (MOV):** Van đóng/mở bằng động cơ điện 2 chiều (như Rotork, Auma, Limitorque), có công tắc giới hạn mô-men xoắn (Torque Switch), công tắc hành trình và tùy chọn dừng giữa chừng (Stop mid-stroke). |
| 4 | **`P_ValveMP`** | `P_ValveMP_4.10.00_AOI.L5X` | **Mix-proof Valve:** Van chống lẫn/chống nhiễm chéo 2 đế (Double-seat Mix-proof) dùng trong ngành Sữa, Bia, Thực phẩm & Dược phẩm, hỗ trợ nâng đế trên/dưới (Seat Lift) và rửa CIP/SIP khoang rò rỉ. |
| 5 | **`P_ValveMPAdv`** | `P_ValveMPAdv_4.10.00_AOI.L5X` | **Advanced Mix-Proof Valve:** Phiên bản nâng cao của van Mix-proof với ma trận 6 đầu ra / 6 đầu vào cấu hình tự do, khóa liên động riêng cho từng trạng thái nâng đế, còi báo trước khi mở và đèn định vị van (Locator Beacon). |
| 6 | **`P_ValveSO`** | `P_ValveSO_4.10.00_AOI.L5X` | **Solenoid-operated Valve:** Van điện từ khí nén/thủy lực 2 trạng thái (ON/OFF), hỗ trợ van tác động đơn (lò xo phản hồi Fail-Close / Fail-Open) hoặc tác động kép (2 cuộn coil) và chức năng nhấp rửa van (Pulse cleaning). |
| 7 | **`P_ValveStats`** | `P_ValveStats_4.10.00_AOI.L5X` | **2-state Valve Statistics:** Khối thống kê chuyên sâu cho van 2 trạng thái: đo thời gian hành trình Mở/Đóng thực tế, tính trung bình động, độ lệch chuẩn và đếm số chu kỳ đóng mở để chẩn đoán kẹt cơ khí/mòn phớt. |
| 8 | **`P_nPos`** | `P_nPos_4.10.00_AOI.L5X` | **n-Position Device:** Điều khiển thiết bị phân phối xoay tròn (Circular) hoặc tịnh tiến (Linear) có từ **2 đến 8 vị trí rời rạc** (van nhiều ngả, bộ chia mẫu, mâm xoay). |

---

## 2. Chi tiết từng khối lệnh AOI trong Chương 4

### 2.1. Analog/Pulsed Control Valve (`P_ValveC` — Trang 283–290)
* **Mô tả chức năng:**
  * Điều khiển độ mở van (`0...100%`) thông qua ngõ ra tương tự (`Out_CV`) **hoặc** thông qua cặp ngõ ra xung rời rạc (`Out_Open` và `Out_Close` — kiểu điều khiển *Ratcheting / Raise-Lower* dựa trên độ lệch giữa độ mở đặt CV và phản hồi vị trí thực `Inp_PosFdbk`).
  * Giám sát phản hồi vị trí liên tục (`Inp_PosFdbk`) và cặp công tắc hành trình cuối (`Inp_OpenLS`, `Inp_ClosedLS`).
  * Hỗ trợ giới hạn độ mở Min/Max (`Cfg_MinCV`, `Cfg_MaxCV`) và giới hạn tốc độ mở/đóng van (`Rate of Change`).
* **Bảng Cảnh báo (`P_ValveC` — Trang 286):**
  * **`Actuator Fault` (`ActuatorFault`)**: Khi `Inp_ActuatorFault = 1` (báo lỗi bộ truyền động van như mất khí nén hoặc quá tải mô-tơ định vị).
  * **`Fail to Open` / `Fail to Close` / `Position Discrepancy`**: Khi vị trí phản hồi không đạt độ mở yêu cầu trong thời gian quy định.
  * **`Interlock Trip` (`IntlkTrip`)**: Khi mất liên động buộc van đóng/mở về vị trí an toàn `Cfg_IntlkCV`.
  * **`I/O Fault` (`IOFault`)**: Lỗi truyền thông thẻ I/O.
* **2 Ví dụ lập trình thực tế trong tài liệu (Trang 287–290):**
  1. **Ví dụ 1 — Trạm điều khiển bằng tay (Manual Loading Station cho van khí `PV110812`):** Nối `Inp_PosFdbk`, `Inp_OpenLS`, `Inp_ClosedLS` và xuất 4-20mA qua `Out_CV` (`Cfg_CVEUMin = 0`, `Cfg_CVEUMax = 100`, `Cfg_CVRawMin = 4`, `Cfg_CVRawMax = 20`, giới hạn mở tối đa `Cfg_MaxCV = 80%`).
  2. **Ví dụ 2 — Van điều khiển xung đóng/mở (Ratcheting Control Valve `FV110813`):** Không dùng ngõ ra analog mà dùng `Out_Open` và `Out_Close` băm xung theo phản hồi vị trí `Inp_PosFdbk` (4-20mA), nhận lệnh độ mở tự động từ chương trình qua chân `PSet_CV`.

### 2.2. Hand-operated Valve (`P_ValveHO` — Trang 291–294)
* **Mô tả chức năng:** Giám sát van đóng/mở bằng tay tại hiện trường qua 2 công tắc hành trình `Inp_OpenLS` và `Inp_ClosedLS`.
  * Hiển thị 4 trạng thái thực tế của van: **Opened (Đã mở)**, **Closed (Đã đóng)**, **In Transit (Đang dịch chuyển giữa hành trình)**, hoặc **Position Fault (Lỗi tiếp điểm — cả 2 công tắc cùng tác động một lúc)**.
  * Cung cấp 1 ngõ ra tùy chọn (`Out_Trip`) để cắt xả khí nén đóng van khẩn cấp từ xa nếu có chốt hãm điện từ trên van tay.

### 2.3. Motor-operated Valve (`P_ValveMO` — Trang 295–299)
* **Mô tả chức năng:** Điều khiển van truyền động bằng động cơ điện xoay chiều/một chiều (`Out_Open`, `Out_Close`, `Out_Stop`).
  * Hỗ trợ dừng van tại vị trí bất kỳ giữa hành trình (**Stop Mid-Stroke**) nếu người vận hành hoặc chương trình ra lệnh Stop khi van đang chạy.
  * Giám sát công tắc quá mô-men xoắn (**Torque Switch — `Inp_TorqueSw`**): Nếu van bị kẹt dị vật làm nhảy công tắc mô-men xoắn trước khi chạm công tắc hành trình cuối, khối lập tức ngắt động cơ và kích hoạt cảnh báo `ActuatorFault` / `FailToOpen` / `FailToClose`.
  * Hỗ trợ cấu hình kiểu đóng kín bằng hành trình (**Limit-seated**) hoặc đóng kín bằng lực ép mô-men (**Torque-seated**).

### 2.4. Mix-proof Valve (`P_ValveMP` — Trang 300–305) & Advanced Mix-Proof Valve (`P_ValveMPAdv` — Trang 306–313)
* **Nguyên lý van chống nhiễm chéo (Double-Seat Mix-Proof Valve):** Van có 2 đĩa/đế van độc lập (Upper Seat và Lower Seat) ngăn cách 2 đường ống chứa 2 chất lỏng khác nhau (ví dụ: đường sữa tươi và đường hóa chất tẩy rửa CIP), ở giữa 2 đế là khoang thoát rò rỉ thông với khí quyển (Leakage Cavity).
* **7 Trạng thái vận hành của `P_ValveMP` & `P_ValveMPAdv`:**
  1. **`Closed` (Đóng hoàn toàn):** Cả 2 đế cùng đóng, khoang giữa thông ra đường thu hồi.
  2. **`Opened` (Mở hoàn toàn):** Khoang thu hồi đóng lại, cả 2 đế cùng mở cho chất lỏng đi từ đường trên xuống đường dưới.
  3. **`Lift Upper Seat` (Nâng đế trên)***: Rửa bề mặt đế trên qua khoang rò rỉ trong khi đường dưới vẫn giữ kín.
  4. **`Lift Lower Seat` (Nâng đế dưới)***: Rửa bề mặt đế dưới qua khoang rò rỉ trong khi đường trên vẫn giữ kín.
  5. **`CIP/SIP Leakage Cavity` (Rửa/Tiệt trùng khoang rò rỉ)**.
  6. **`CIP/SIP Upper Seat` (Rửa đế trên qua van cấp CIP riêng)***
  7. **`CIP/SIP Lower Seat` (Rửa đế dưới qua van cấp CIP riêng)***
  *(Các trạng thái có dấu `*` hỗ trợ chế độ nhấp xung đóng/mở **Seat Pulsing** để tăng vận tốc dòng chảy xoáy qua khe van giúp đánh bật cặn bẩn).*
* **Điểm vượt trội của `P_ValveMPAdv` (Trang 306–313):**
  * Cung cấp **bảng ma trận trạng thái nhị phân 6 đầu ra × 6 đầu vào** (`Cfg_OutStateTbl[0..7]`, `Cfg_FdbkReqdTbl[0..7]`, `Cfg_FdbkStateTbl[0..7]`) cho phép cấu hình ngay trên HMI Faceplate cho mọi chủng loại van Mix-proof của Alfa Laval, GEA Tuchenhagen, Definox, Pentair Südmo.
  * Tách biệt **4 đầu vào khóa liên động độc lập** cho: Trạng thái Mở (`Open`), Nâng đế dưới (`Lower Lift`), Nâng đế trên (`Upper Lift`) và Rửa khoang (`Cavity/Cleaning`). Nhờ đó có thể khóa cấm mở van chính khi đang chạy CIP nhưng vẫn cho phép nhấc đế rửa van!
  * Tích hợp ngõ ra đèn nháy định vị van (**Valve Locator Beacon**) và còi báo cảnh báo trước khi van chuyển động (**Audible Horn Output**).

### 2.5. Solenoid-operated Valve (`P_ValveSO` — Trang 314–318)
* **Mô tả chức năng:** Điều khiển van điện từ ON/OFF 1 cuộn dây (Single Solenoid) hoặc 2 cuộn dây (Dual Solenoid — `Out_1`, `Out_2`).
  * Cho phép cấu hình trạng thái an toàn khi mất điện là **Fail Close (Thường đóng)**, **Fail Open (Thường mở)** hoặc **Fail Last / Hold (Giữ nguyên vị trí)**.
  * Hỗ trợ tính năng **nhấp rửa van (Pulse / Flutter)** để thông nghẹt đường ống.
  * Giám sát công tắc hành trình `Inp_OpenLS` / `Inp_ClosedLS` và báo lỗi `FailToOpen` / `FailToClose` khi quá thời gian hành trình `Cfg_TransitT`.

### 2.6. 2-state Valve Statistics (`P_ValveStats` — Trang 319–322)
* **Mô tả chức năng:** Kết nối song song với khối van 2 trạng thái (`P_ValveSO`, `P_ValveMO`, `P_ValveHO`) để ghi nhận thống kê vận hành và chẩn đoán hao mòn:
  * Đo **Thời gian hành trình mở cuối cùng (`Val_LastOpenT`)** và **Thời gian hành trình đóng cuối cùng (`Val_LastCloseT`)**.
  * Tính **Thời gian mở/đóng trung bình (`Val_AvgOpenT`, `Val_AvgCloseT`)** và **Độ lệch chuẩn (`StdDev`)** qua $N$ lần thao tác.
  * Đếm tổng số lần đóng mở (`Val_StrokeCount`) và phát cảnh báo khi số chu kỳ vượt ngưỡng bảo dưỡng hoặc khi thời gian đóng/mở chậm bất thường so với mức trung bình (dấu hiệu khô dầu, kẹt trục hoặc rò màng khí nén).

### 2.7. n-Position Device (`P_nPos` — Trang 323–330)
* **Mô tả chức năng:** Điều khiển thiết bị cơ khí có từ **2 đến 8 vị trí dừng rời rạc** (ví dụ: van đa ngả phân phối nguyên liệu vào 6 bồn, bộ lấy mẫu tự động, bàn xoay phôi).
  * Hỗ trợ cả thiết bị **Xoay tròn hai chiều (Circular — đi theo chiều kim đồng hồ CW hoặc ngược chiều kim đồng hồ CCW theo đường ngắn nhất)** và thiết bị **Tịnh tiến tuyến tính (Linear — tiến/lùi hoặc bắt buộc quay về vị trí Home số 1 trước mỗi lần chọn vị trí mới để khử độ rơ cơ khí Backlash)**.
  * Xuất cả 8 chân chọn vị trí trực tiếp (`Out_Pos1` ... `Out_Pos8`) lẫn cặp chân điều khiển chiều chạy (`Out_Inc` / `Out_Dec`) và nhận 8 chân công tắc phản hồi vị trí (`Inp_Pos1Fdbk` ... `Inp_Pos8Fdbk`).

# CHƯƠNG 5: CÁC KHỐI CHỨC NĂNG DÙNG CHUNG, LIÊN ĐỘNG & CẢNH BÁO (CROSS FUNCTIONAL — TRANG 331–396)

## 1. Bảng phân loại toàn bộ 10 khối lệnh trong Chương 5 (Purpose & Table 19 - Trang 331–335)

| STT | Khối lệnh AOI | Tệp Controller (`.L5X`) | Vai trò kỹ thuật & Phạm vi ứng dụng |
| :--- | :--- | :--- | :--- |
| 1 | **`P_Gate`** | `P_Gate_4.10.00_AOI.L5X` | **Condition Gate Delay:** Khối cổng điều kiện kèm bộ định thời trễ bật (On-Delay) và trễ tắt (Off-Delay) cho tín hiệu số, nhúng bên trong các khối đầu vào và PID hoặc dùng độc lập. |
| 2 | **`P_Intlk`** | `P_Intlk_4.10.00_AOI.L5X` | **Interlocks with First Out and Bypass:** Gom tối đa **16 điều kiện liên động an toàn** (hoặc nối tầng nhiều khối) để dừng thiết bị đang chạy và cấm khởi động, có bắt lỗi đầu tiên (First Out) và Bypass bảo trì. |
| 3 | **`P_IntlkAdv`** | `P_IntlkAdv_4.10.00_AOI.L5X` | **Interlocks Advanced:** Mở rộng từ `P_Intlk` với phân nhóm cấp độ ưu tiên dừng (Stop Priority / Shed levels), cho phép điều hướng liên động riêng và điều khiển trạng thái an toàn linh hoạt hơn. |
| 4 | **`P_Perm`** | `P_Perm_4.10.00_AOI.L5X` | **Permissives with Bypass:** Gom tối đa **16 điều kiện cho phép khởi động** (chỉ kiểm tra trước khi thiết bị chạy; khi thiết bị đã chạy thì bỏ qua), có hỗ trợ Bypass bảo trì. |
| 5 | **`P_D4SD`** | `P_D4SD_4.10.00_AOI.L5X` | **Discrete 2-, 3-, or 4-state Device:** Điều khiển và giám sát thiết bị có 2, 3 hoặc 4 trạng thái hoạt động rời rạc với tối đa 4 ngõ ra và 4 ngõ vào phản hồi cấu hình theo bảng chân trị. |
| 6 | **`P_Reset`** | `P_Reset_4.10.00_AOI.L5X` | **Central Reset:** Điểm xóa lỗi tập trung cho cả một cụm thiết bị (Unit / Equipment Module), gom tín hiệu `Sts_RdyToReset` từ các thiết bị con và phát xung Reset đủ độ rộng thời gian tối thiểu. |
| 7 | **`P_Alarm`** | `P_Alarm_4.10.00_AOI.L5X` | **Common Alarm Block:** Khối quản lý vòng đời cảnh báo chuẩn ANSI/ISA-18.2 (Acknowledge, Reset, Shelve, Disable, Suppress), dùng độc lập hoặc nhúng trong mọi khối PlantPAx. |
| 8 | **`P_CmdSrc`** | `P_CmdSrc_4.10.00_AOI.L5X` | **Command Source (Common Mode Block):** Quản lý phân quyền điều khiển giữa 7 nguồn lệnh (Operator, Program, External, Override, Maintenance, Hand, Out of Service). |
| 9 | **`P_Prompt`** | `P_Prompt_4.10.00_AOI.L5X` | **Operator Prompt:** Hiển thị thông báo tương tác trên HMI yêu cầu người vận hành xác nhận, chọn phương án hoặc nhập số liệu/văn bản trả về cho trình tự điều khiển (`P_Seq`). |
| 10 | **`P_Logic`** | `P_Logic_4.10.00_AOI.L5X` | **Boolean Logic with Snapshot:** Khối mạch tổ hợp gồm **8 cổng logic khả trình** (AND, OR, XOR, Set/Reset, Select, Majority) cấu hình trực tiếp từ HMI kèm tính năng chụp băng hình trạng thái (Snapshot) lúc tác động. |

---

## 2. Chi tiết từng khối lệnh AOI trong Chương 5

### 2.1. Condition Gate Delay (`P_Gate` — Trang 336–339)
* **Mô tả chức năng:**
  * Nhận tín hiệu đầu vào điều kiện `Inp` và tín hiệu cổng cho phép `Inp_Gate` (kèm thời gian trễ mở cổng `Cfg_GateDelayT`).
  * Khi cổng `Gate` mở (True): Tín hiệu `Inp` đi qua bộ lọc trễ bật (`Cfg_OnDelayT`) và trễ tắt (`Cfg_OffDelayT`) trước khi xuất ra `Out`.
  * Khi cổng `Gate` đóng (False): Ngõ ra `Out` lập tức bị giữ ở mức 0 (Off), dùng để khóa không cho báo động giả trong giai đoạn khởi động máy (Startup suppression).

### 2.2. Interlocks with First Out and Bypass (`P_Intlk` — Trang 340–345) & Advanced (`P_IntlkAdv` — Trang 346–352)
* **Nguyên lý vận hành cốt lõi (Trang 341–342):**
  1. **Kiểm tra trạng thái an toàn (`Interlock Input OK Check`):** So sánh từng đầu vào `Inp_Intlk00` ... `Inp_Intlk15` với trạng thái an toàn cấu hình `Cfg_OKState.x` (có thể cấu hình mức 1 là an toàn hoặc mức 0 là an toàn — De-energize to trip).
  2. **Chốt giữ trạng thái lỗi (`Interlock Condition Latching`):** Nếu `Cfg_Latched.x = 1`, một khi điều kiện liên động vi phạm, nó sẽ tự khóa chốt (Latch) kể cả khi tín hiệu vật lý đã trở lại bình thường, bắt buộc phải nhấn Reset mới xóa được (trừ khi chân `Inp_LatchDefeat = 1`).
  3. **Cơ chế Bypass bảo trì 3 lớp an toàn (`Interlock Bypass`):** Một đầu vào liên động chỉ được bỏ qua khi thỏa mãn đồng thời cả 3 điều kiện:
     * Kỹ sư thiết kế cho phép phép bypass (`Cfg_Bypassable.x = 1`).
     * Nhân viên bảo trì kích hoạt bypass cho kênh đó (`MSet_Bypass.x = 1`).
     * Khối thiết bị chấp hành phía sau (ví dụ `P_Motor`) đang bật cho phép nhận quyền bypass (`Sts_BypActive = 1` nối ngược về chân `Inp_BypActive` của `P_Intlk`).
  4. **Bắt lỗi đầu tiên (`First Out`):** Khi thiết bị đang chạy bình thường mà bị ngắt liên động, khối tự động đánh dấu điều kiện vi phạm **đầu tiên** gây ra cú ngắt dây chuyền để hiển thị nổi bật trên HMI, giúp chẩn đoán sự cố tức thì.
  5. **Hai ngõ ra tổng hợp quan trọng:**
     * `Sts_IntlkOK`: Bằng 1 khi tất cả các điều kiện liên động đều tốt (bất kể có đang bật Bypass hay không).
     * `Sts_NBIntlkOK`: Bằng 1 khi mọi liên động không thể bypass đều tốt và các liên động cho phép bypass đã tốt hoặc đã được bypass.
* **Quy tắc vàng từ Rockwell Automation (Trang 343–344):**
  * **CẢNH BÁO:** Luôn sử dụng các bit **Trạng thái (`Sts_*`)** từ khối `P_AIn`/`P_DIn` làm đầu vào cho `P_Intlk`, **KHÔNG** dùng bit Cảnh báo (`Alm_*`) trừ khi bạn cố tình muốn liên động bị vô hiệu hóa khi người vận hành nhấn Shelve/Disable cảnh báo!
  * **Quy ước đặt tên tự động liên kết HMI:** Đặt tên thẻ của khối `P_Intlk` theo công thức `[Tên_Thẻ_Thiết_Bị]_Intlk` (ví dụ động cơ là `M101` thì khối liên động đặt là `M101_Intlk`) để nút nhấn Interlock trên Faceplate động cơ tự động mở đúng cửa sổ liên động.
  * Khi nối dây phản hồi từ chân `Sts_BypActive` của `P_Motor` quay ngược lại chân `Inp_BypActive` của `P_Intlk` trên sơ đồ FBD, bắt buộc đánh dấu đường dây đó là **`Assume Data Available`** (Giả định dữ liệu sẵn sàng) để trình biên dịch không báo lỗi vòng lặp đệ quy.

### 2.3. Permissives with Bypass (`P_Perm` — Trang 353–358)
* **Mô tả chức năng:** Gom tối đa **16 điều kiện cho phép khởi động** (`Inp_Perm00` ... `Inp_Perm15`).
  * Khác với `P_Intlk`, khối `P_Perm` **chỉ kiểm tra trước khi thiết bị khởi động**. Khi thiết bị đã chạy ổn định, việc mất điều kiện Permissive không làm dừng thiết bị.
  * Quy ước đặt tên thẻ chuẩn để tự động điều hướng HMI: `[Tên_Thẻ_Thiết_Bị]_Perm`.

### 2.4. Discrete 2-, 3-, or 4-state Device (`P_D4SD` — Trang 359–366)
* **Mô tả chức năng:** Điều khiển thiết bị đa trạng thái phức tạp (tối đa 4 trạng thái chọn từ HMI/Program, điều khiển 4 ngõ ra số `Out_0..3` và kiểm tra 4 ngõ vào phản hồi `Inp_Fdbk0..3`).
* Mỗi ngõ ra ở từng trạng thái có thể cấu hình là: Tắt (0), Bật duy trì (1), Xung bật (Pulse On) hoặc Xung tắt (Pulse Off).

### 2.5. Central Reset (`P_Reset` — Trang 367–370)
* **Mô tả chức năng:** Tạo nút nhấn Reset tập trung cho toàn bộ một cụm công nghệ (Unit / Area).
  * Gom các chân `Sts_RdyToReset` của nhiều thiết bị về chân `Inp_ResetReqd` để làm sáng đèn báo có thiết bị đang chờ xóa lỗi.
  * Khi nhận lệnh Reset (từ HMI `OCmd_Reset`, từ chương trình `PCmd_Reset` hoặc nút nhấn vật lý `Inp_Reset`), khối phát ra xung `Out_Reset` được giữ ở mức 1 trong khoảng thời gian tối thiểu cấu hình (`Cfg_MinResetT`, mặc định vài giây) để đảm bảo mọi thiết bị mạng chậm (như biến tần, rơ-le quá tải) đều kịp nhận lệnh xóa lỗi.

### 2.6. Common Alarm Block (`P_Alarm` — Trang 371–377)
* **Mô tả chức năng:** Khối chuẩn hóa quản lý cảnh báo theo tiêu chuẩn quốc tế **ANSI/ISA-18.2**:
  * Quản lý đầy đủ sơ đồ trạng thái: **Normal** -> **Unacknowledged Alarm** -> **Acknowledged Alarm** -> **RTN (Return to Normal) Unacknowledged** -> **Reset / Normal**.
  * Hỗ trợ phân mức độ nghiêm trọng (**Severity**: Low, Medium, High, Urgent) để điều khiển màu sắc và âm thanh còi báo trên FactoryTalk View.
  * Hỗ trợ **Alarm Shelving** (Tạm hoãn cảnh báo có thời hạn), **Out-of-Service Disable** (Vô hiệu hóa cảnh báo bảo trì) và **Program Suppression** (Ức chế cảnh báo bằng logic chương trình).

### 2.7. Command Source (`P_CmdSrc` — Trang 378–388)
* **Mô tả chức năng:** Trái tim phân quyền điều khiển của mọi khối đối tượng PlantPAx.
  * Quản lý thứ tự ưu tiên nghiêm ngặt từ cao xuống thấp:
    1. **Out of Service (OoS — Đưa ra khỏi vận hành)** — Ưu tiên cao nhất.
    2. **Hand (Điều khiển cứng tại tủ điện hiện trường)**.
    3. **Maintenance (Bảo trì trên HMI — có quyền vượt qua Bypassable Interlocks)**.
    4. **Override (Cưỡng bức an toàn bằng logic PLC)**.
    5. **External (Điều khiển từ hệ thống ngoài)**.
    6. **Program (Điều khiển tự động bởi chương trình PLC)**.
    7. **Operator (Điều khiển bởi người vận hành trên màn hình SCADA)** — Ưu tiên cơ sở.
  * Hiển thị biểu tượng hình tam giác nhỏ ở góc trên bên trái trên HMI để báo đâu là nguồn lệnh "Bình thường" (`Normal Command Source` — thông thường là Program hoặc Operator).

### 2.8. Operator Prompt (`P_Prompt` — Trang 389–390)
* **Mô tả chức năng:** Cơ chế tương tác hai chiều với người vận hành trong các quy trình tự động hoặc bán tự động:
  * Hiển thị thông điệp nhắc nhở, bảng thông số cần kiểm tra xác nhận, danh sách lựa chọn (Selection list), hoặc ô nhập giá trị số/chuỗi ký tự (lưu vào thẻ `RespData`).
  * **Lưu ý chuẩn ISA-18.2 (Trang 389):** Không bao giờ dùng `P_Prompt` để thay thế cho Cảnh báo (`P_Alarm`). Cảnh báo dùng cho tình huống bất thường; `P_Prompt` dùng cho bước thao tác vận hành bình thường trong quy trình.

### 2.9. Boolean Logic with Snapshot (`P_Logic` — Trang 391–396)
* **Mô tả chức năng:** Cho phép kỹ sư cấu hình hoặc thay đổi mạch logic tổ hợp ngay trên màn hình HMI (Engineering Tab) mà không cần sửa code PLC hay Download lại dự án!
  * Bao gồm **8 đầu vào Boolean** (`Inp_0` ... `Inp_7`) và **8 cổng logic nối tầng** (`Gate 0` ... `Gate 7`).
  * Mỗi cổng có thể chọn 1 trong 6 kiểu hàm: **`AND`**, **`OR`**, **`XOR` (Exclusive-OR)**, **`Set/Reset` (Flip-Flop ưu tiên Set hoặc Reset)**, **`Select` (Multiplexer 2-to-1)**, hoặc **`Majority` (Biểu quyết đa số 2-trên-3 / 3-trên-4)**.
  * Từng chân đầu vào của mỗi cổng đều có bit đảo trạng thái (`Invert` — do đó không cần cổng NOT riêng).
  * **Tính năng Chụp băng hình trạng thái (`Snapshot`):** Tự động ghi lại toàn bộ trạng thái tức thời của 8 đầu vào và 8 cổng logic kèm nhãn thời gian (`Timestamp`) ngay tại khoảnh khắc ngõ ra đổi trạng thái, giúp phân tích sự cố chập chờn diễn ra trong vài mili-giây mà mắt thường trên HMI không kịp nhìn thấy!

# PHỤ LỤC A, B & C: NGUỒN LỆNH, MÔ PHỎNG, LỆNH MỞ RỘNG 64-BIT/THỜI GIAN & CHIẾN LƯỢC MẪU (TRANG 397–436)

## 1. Phụ lục A: Nguồn lệnh & Các kiểu Mô phỏng (Command Source & Simulation Types — Trang 397–400)

### 1.1. Bảng mô tả 7 Nguồn lệnh (`Table 21 - Command Source Descriptions` — Trang 398)
* **Operator:** Người vận hành điều khiển thiết bị từ màn hình HMI (`OCmd_*`, `OSet_*`).
* **Program:** Logic chương trình tự động trong PLC điều khiển thiết bị (`PCmd_*`, `PSet_*`), có thể khóa (`PCmd_Lock`) không cho Operator giành quyền khi đang chạy mẻ tự động.
* **External:** Một phần mềm hoặc trạm điều khiển bên ngoài hệ thống Logix chính nắm quyền điều khiển (`XCmd_*`, `XSet_*`).
* **Override:** Logic ưu tiên cưỡng bức thiết bị về trạng thái định sẵn khi có sự kiện công nghệ đặc biệt (vượt quyền Operator, Program và External).
* **Maintenance:** Nhân viên bảo trì nắm quyền điều khiển từ tab Maintenance trên HMI (`MCmd_*`, `MSet_*`), vượt qua các liên động cho phép bypass (`Bypassable Interlocks`) để kiểm tra máy.
* **Hand:** Công tắc chuyển mạch cứng tại tủ điện hiện trường (Local H-O-A Switch ở vị trí Hand) nắm quyền điều khiển trực tiếp; PLC chỉ theo dõi trạng thái.
* **Out of Service:** Thiết bị được đánh dấu ngưng phục vụ để sửa chữa; mọi cảnh báo bị ức chế và đầu ra điều khiển bị đưa về trạng thái nghỉ an toàn.

### 1.2. Ma trận hỗ trợ Nguồn lệnh theo từng khối (`Table 22` — Trang 399)
* Hầu hết các khối chấp hành (`P_AOut`, `P_D4SD`, `P_DOut`, `P_PIDE`, `P_LLS`, `P_Motor*`, `P_nPos`, `P_PF*`, `P_SMC*`, `P_Seq`, `P_Valve*`, `P_VSD`) đều hỗ trợ **đầy đủ cả 7 nguồn lệnh**.
* Ngoại lệ:
  * **`P_AOutHART` và `P_Dose`:** Hỗ trợ Operator, Program, External, Maintenance, Hand (không có Override và Out of Service).
  * **`P_DBC` (Deadband Controller):** Hỗ trợ Operator, Program, External, Override, Maintenance, Out of Service (không có Hand).

### 1.3. Hai kiểu Mô phỏng cơ bản (`Simulation` — Trang 399)
1. **Nhóm khối Đầu vào (Inputs — Analog & Discrete):** Khi `Inp_Sim = 1`, ngắt kết nối với kênh I/O thực tế và cho phép nhập giá trị giả lập (ví dụ `50 PSI` hoặc `1/0`) trực tiếp từ HMI Faceplate.
2. **Nhóm khối Đầu ra / Thiết bị chấp hành (Motors, Valves, Drives):** Khi `Inp_Sim = 1`, toàn bộ ngõ ra vật lý gửi tới thiết bị thực đều được **giữ ở mức 0 (De-energized)** để đảm bảo an toàn tuyệt đối, trong khi khối tự tính toán phản hồi nội bộ (vị trí van, tốc độ biến tần, tiếp điểm chạy động cơ) giống hệt như một thiết bị thật đang hoạt động hoàn hảo.

---

## 2. Phụ lục B: Các khối lệnh bổ sung Số nguyên 64-bit (`L_*`) & Thời gian/Ngày tháng (`T_*`) (Trang 401–416)

Do firmware Logix tiêu chuẩn không hỗ trợ sẵn các phép toán số học và bit trên kiểu số nguyên có dấu 64-bit (**`LINT`** — dùng cho nhãn thời gian chuẩn micro-giây), thư viện cung cấp bộ **24 khối lệnh `L_*`** và **17 khối lệnh Ngày/Giờ `T_*`**.

### 2.1. Bảng 24 khối lệnh Số nguyên 64-bit (`Table 23 - Long Integer Instructions` — Trang 401–404)

| Tên khối AOI | Tệp `.L5X` | Chức năng chi tiết |
| :--- | :--- | :--- |
| **`L_ABS`** | `L_ABS_1_0-00_AOI.L5X` | Tính giá trị tuyệt đối (`|Inp|`) của số nguyên 64-bit `LINT`. |
| **`L_ADD`** | `L_ADD_1_0-00_AOI.L5X` | Cộng hai số 64-bit `LINT`, xuất tổng 64-bit kèm các cờ trạng thái toán học Carry, Negative, Overflow, Zero. |
| **`L_SUB`** | `L_SUB_1_0-00_AOI.L5X` | Trừ hai số 64-bit `LINT`, xuất hiệu 64-bit kèm các cờ Borrow/Carry, Negative, Overflow, Zero. |
| **`L_MUL`** | `L_MUL_1_0-01_AOI.L5X` | Nhân một số 64-bit (`LINT`) với một số 32-bit (`DINT`), trả về tích số 64-bit (`LINT`). |
| **`L_DIV`** | `L_DIV_1_0-00_AOI.L5X` | Chia một số bị chia 64-bit (`LINT`) cho số chia 32-bit (`DINT`), trả về thương số 64-bit (`LINT`) và số dư 32-bit (`DINT`). |
| **`L_INC` / `L_DEC`** | `L_INC_1_0-00` / `L_DEC_1_0-00` | Tăng thêm 1 (`+1`) hoặc giảm đi 1 (`-1`) giá trị của biến 64-bit `LINT`. |
| **`L_NEG`** | `L_NEG_1_0-00_AOI.L5X` | Đảo dấu (bù 2 — 2's complement) một số 64-bit `LINT`. |
| **`L_AND`, `L_OR`, `L_XOR`, `L_NOT`** | `L_AND_1_0-00` ... | Các phép toán logic thao tác từng bit (Bitwise AND, OR, XOR, NOT/1's complement) trên 64-bit `LINT`. |
| **`L_EQU`, `L_NEQ`, `L_GRT`, `L_GEQ`, `L_LES`, `L_LEQ`** | `L_EQU_1_0-01` ... | 6 lệnh so sánh hai biến 64-bit `LINT` ($=, 
eq, >, \ge, <, \le$). |
| **`L_LIM`** | `L_LIM_1_0-01_AOI.L5X` | Kiểm tra giới hạn 64-bit (hỗ trợ cả trường hợp bình thường `Low <= High` và trường hợp vòng tròn `Low > High`). |
| **`L_MEQ` & `L_MVM`** | `L_MEQ_1_0-01` / `L_MVM_1_0-00` | So sánh qua mặt nạ 64-bit (`Masked Equal`) và Di chuyển dữ liệu qua mặt nạ 64-bit (`Move with Mask`). |
| **`L_XIC`, `L_XIO`, `L_OTE`, `L_OTL`, `L_OTU`** | `L_XIC_1_0-00` ... | Kiểm tra bit đóng/mở và Xuất/Chốt/Nhả một bit bất kỳ (chỉ số bit từ `0...63`) trong biến 64-bit `LINT`. *(Lưu ý: nếu truyền số thứ tự bit ngoài đoạn 0..63 sẽ gây Major Fault CPU)*. |
| **`L_SEL`** | `L_SEL_1_0-00_AOI.L5X` | Bộ chọn 1 trong 2 giá trị 64-bit `LINT` (`Inp_A` hoặc `Inp_B`) theo bit chọn. |
| **`L_FtoH` & `L_HtoF`** | `L_FtoH_1_0-00` / `L_HtoF_1_0-00` | Chuyển đổi qua lại giữa số thực dấu phẩy động 32-bit (`REAL` — Single Precision) và số thực 16-bit (`Half-Precision` lưu trong `INT`). |

### 2.2. Bảng 17 khối lệnh Thời gian và Ngày tháng (`Table 24 - Time and Date Instructions` — Trang 405–416)
Sử dụng kiểu dữ liệu cấu trúc **`DateTime`** gồm 7 trường `DINT`: `Yr` (Năm), `Mo` (Tháng 1..12), `Da` (Ngày 1..31), `Hr` (Giờ 0..23), `Min` (Phút 0..59), `Sec` (Giây 0..59), `uSec` (Micro-giây 0..999,999).

| Tên khối AOI | Tệp `.L5X` | Chức năng chi tiết |
| :--- | :--- | :--- |
| **`T_Clock`** | `T_Clock_1_0-01_AOI.L5X` | Quản lý đồng hồ thời gian thực của bộ điều khiển (`WallClockTime`), xuất ngày/giờ hiện tại, tính thứ trong tuần và xác định ca sản xuất hiện tại (hỗ trợ tối đa 3 ca làm việc/ngày). |
| **`T_Now`** | `T_Now_1_0-00_AOI.L5X` | Đọc nhanh ngày giờ địa phương hiện tại từ đồng hồ CPU ra biến cấu trúc `DateTime`. |
| **`T_Scan`** | `T_Scan_1_0-01_AOI.L5X` | Trả về khoảng thời gian thực tế (tính bằng giây, kiểu `REAL`) giữa lần quét trước và lần quét hiện tại của khối lệnh. |
| **`T_ADD` & `T_SUB`** | `T_ADD_1_0-01` / `T_SUB_1_0-01` | Cộng hoặc trừ một khoảng thời gian (`Time`) vào một mốc `DateTime` và tự động chuẩn hóa kết quả theo lịch Gregory (tính cả năm nhuận). |
| **`T_DIFF`** | `T_DIFF_1_0-00_AOI.L5X` | Tính khoảng cách thời gian giữa hai mốc `DateTime` (trả về số ngày, giờ, phút, giây và micro-giây). |
| **`T_EQU`, `T_NEQ`, `T_GRT`, `T_GEQ`, `T_LES`, `T_LEQ`, `T_LIM`, `T_SEL`** | `T_EQU_1_0-01` ... | Bộ 8 lệnh so sánh, kiểm tra giới hạn và lựa chọn giữa các mốc thời gian `DateTime`. |
| **`T_DoW`** | `T_DoW_1_0-00_AOI.L5X` | Tính thứ trong tuần cho một ngày bất kỳ (`0 = Chủ Nhật`, `1 = Thứ Hai` ... `6 = Thứ Bảy`). |
| **`T_DST`** | `T_DST_1_0-01_AOI.L5X` | Tự động điều chỉnh giờ mùa hè (**Daylight Saving Time — Spring Forward / Fall Back**) theo quy tắc quốc gia/khu vực cấu hình được, có cờ báo giờ trùng lặp (Overlap) hoặc giờ nhảy cóc (Gap). |
| **`T_Sun`** | `T_Sun_1_0-00_AOI.L5X` | Tính toán thời điểm **Mặt trời mọc (Sunrise)**, **Mặt trời lặn (Sunset)**, **Góc phương vị (Solar Azimuth)** và **Góc ngẩng (Solar Elevation)** dựa trên vĩ độ (`Latitude`), kinh độ (`Longitude`) và múi giờ UTC (chính xác tới ~2 phút, ứng dụng điều khiển chiếu sáng công nghiệp hoặc hệ thống pin mặt trời). |
| **`T_Sync`** | `T_Sync_1_0-01_AOI.L5X` | Đồng bộ hóa đồng hồ bộ điều khiển PLC với máy chủ thời gian mạng **NTP Time Server** qua giao diện EtherNet/IP Socket (ví dụ qua module `1756-EN2T` hoặc cổng Ethernet tích hợp của CompactLogix `Cfg_ENSlotNumber = 1`), tính toán độ trôi đồng hồ (Clock Drift). |
| **`T_LtoT` & `T_TtoL`** | `T_LtoT_1_0-01` / `T_TtoL_1_0-00` | Chuyển đổi qua lại giữa nhãn thời gian 64-bit `LTIME` (số micro-giây tính từ `1970-01-01 00:00:00 UTC`) và cấu trúc `DateTime`. |
| **`T_TtoISO_WkDate`** | `T_TtoISO_WkDate_1_0-00_AOI.L5X` | Chuyển đổi ngày thông thường sang định dạng tuần chuẩn quốc tế **ISO-8601 Week Date** (ví dụ `2014-W04-2`). |
| **`T_TtoS`** | `T_TtoS_1_0-00_AOI.L5X` | Định dạng biến `DateTime` thành chuỗi ký tự dễ đọc (`STRING`, ví dụ `"Wednesday, December 31, 2008 11:59:59.999999 p.m."` hoặc chuẩn ISO `YYYY-MM-DD`). |
| **`T_Valid`** | `T_Valid_1_0-00_AOI.L5X` | Kiểm tra tính hợp lệ của một biến `DateTime` theo quy tắc năm nhuận lịch Gregory (4 năm, 100 năm, 400 năm). |

---

## 3. Phụ lục C: Danh mục Chiến lược Điều khiển Mẫu (`Table 25 - Process Strategies` — Trang 417–428)

Các **Process Strategies** là các tệp mẫu chương trình con Function Block (`*_ROUTINE.L5X`) hoặc nhánh thang Ladder (`*_RUNG.L5X`) đã được Rockwell kết nối dây sẵn giữa khối chức năng chính với các khối phụ trợ (`P_AIChan`, `P_Intlk`, `P_IntlkAdv`, `P_Perm`, `P_RunTime`, `P_ResInh`, `P_ValveStats`, `P_E300Ovld`...), giúp kỹ sư nhập vào Studio 5000 trong vài giây:

### 3.1. Nhóm I/O Processing Strategies (Trang 418)
* `(RA-LIB)PS_DIn_4_10-00_ROUTINE.L5X` & `PS_DInAdv_4_10-00_ROUTINE.L5X`: Đầu vào số cơ bản & nâng cao.
* `(RA-LIB)PS_DOut_4_10-00_ROUTINE.L5X` (kèm `P_Intlk`), `PS_DOut_IntlkAdv_4_10-00_ROUTINE.L5X` (kèm `P_IntlkAdv`), `PS_DOut_noIntlk_4_10-00_ROUTINE.L5X` (không liên động).
* `(RA-LIB)PS_AIn_4_10-00_ROUTINE.L5X` & `PS_AIn_Chan_4_10-00_ROUTINE.L5X` (kèm `P_AIChan`).
* `(RA-LIB)PS_AInAdv_4_10-00_ROUTINE.L5X` & `PS_AInAdv_Chan_4_10-00_ROUTINE.L5X` (kèm `P_AIChan`).
* `(RA-LIB)PS_AInDual_4_10-00_ROUTINE.L5X` & `PS_AInDual_Chan_4_10-00_ROUTINE.L5X` (kèm 2 khối `P_AIChan`).
* `(RA-LIB)PS_AInMulti_4_10-00_ROUTINE.L5X`: Đa cảm biến tương tự.
* `(RA-LIB)PS_AOut_4_10-00_ROUTINE.L5X`, `PS_AOut_IntlkAdv_4_10-00_ROUTINE.L5X`, `PS_AOut_noInltk_4_10-00_ROUTINE.L5X`.

### 3.2. Nhóm Regulatory, Model-Based & Procedural Control Strategies (Trang 419–420)
* **Vòng đơn PID (`P_PIDE`):** `PS_PID_IntlkAdv`, `PS_PID_AIn_IntlkAdv`, `PS_PID_AIn_Chan_IntlkAdv`, `PS_PID_AInAdv_IntlkAdv`, `PS_PID_AInAdv_Chan_IntlkAdv`.
* **Vòng điều khiển tầng Cascade PID (Primary Outer Loop & Secondary Inner Loop):**
  * `PS_PID_Casc_IntlkAdv_AIn_Pri` & `PS_PID_Casc_IntlkAdv_AIn_Sec`
  * `PS_PID_Casc_IntlkAdv_AInAdv_Pri` & `PS_PID_Casc_IntlkAdv_AInAdv_Sec`
  * `PS_PID_PF755_IntlkAdv_AIn_Pri` & `PS_PID_PF755_IntlkAdv_AIn_Sec` (Vòng trong điều khiển trực tiếp biến tần PowerFlex 755 kèm `P_IntlkAdv`, `P_Perm`, `P_RunTime`).
* **Điều khiển nâng cao dựa trên mô hình (Model-Based Control):**
  * `(RA-LIB)PS_IMC_4_10-00_ROUTINE.L5X`: Internal Model Control (Điều khiển mô hình nội).
  * `(RA-LIB)PS_CC_4_10-00_ROUTINE.L5X`: Coordinated Control (Điều khiển phối hợp).
  * `(RA-LIB)PS_MMC_4_10-00_ROUTINE.L5X`: Modular Multivariable Control (Điều khiển đa biến mô-đun).
  * `(RA-LIB)PS_RMPS_4_10-00_ROUTINE.L5X`: Ramp/Soak (Gia nhiệt theo biểu đồ nhiệt độ - thời gian).
* **Quy trình & Định lượng:** `PS_DoseFM` (với `P_AIn` + `P_AIChan`), `PS_DoseWS` (Cân), `PS_Seq` / `PS_Seq_IntlkAdv` (có hoặc không có `P_Prompt`), `PS_Tot` (Bộ cộng dồn lưu lượng Totalizer).

### 3.3. Nhóm Valves, Motors, Drives & Low Voltage MCC Strategies (Trang 420–427)
* **Van:** Đầy đủ các mẫu cho `P_ValveC`, `P_ValveHO`, `P_ValveMO`, `P_ValveMP`, `P_ValveSO` kết hợp sẵn `P_Intlk` (hoặc `P_IntlkAdv`), `P_Perm` và `P_ValveStats`.
* **Động cơ đơn tốc độ, 2 tốc độ, Đảo chiều, Tại chỗ (`P_Motor`, `P_Motor2Spd`, `P_MotorRev`, `P_MotorHO`):** Đấu sẵn với `P_Intlk`/`P_IntlkAdv`, `P_Perm`, `P_RunTime`, `P_ResInh` và tùy chọn tích hợp sẵn rơ-le quá tải `P_E300Ovld`, `P_E3Ovld` hoặc `P_E1PlusE`.
* **Biến tần & Khởi động mềm:** Đầy đủ mẫu cho `P_PF52x`, `P_PF753`, `P_PF755`, `P_PF6000`, `P_PF7000`, `P_VSD`, `P_SMC50`, `P_SMCFlex`.
* **Tủ động lực hạ thế thông minh (Low Voltage Motor Control Centers — IntelliCENTER LVMCC):** Các mẫu chuyên biệt cho khởi động từ FVNR/FVR dùng E300 Overload ở `Operating Mode 2` (có công tắc H-O-A cứng), `Operating Mode 3` (không dây phản hồi chạy), `Operating Mode 5`, `Operating Mode 11`, `Operating Mode 13` và biến tần PowerFlex 525/753/755 có công tắc H-O-A cứng tại tủ.
