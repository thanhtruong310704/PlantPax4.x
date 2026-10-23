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

---
# PHẦN TRA CỨU CHI TIẾT TOÀN BỘ BIẾN (INPUT, OUTPUT, LOCAL TAGS), CÁCH SỬ DỤNG & CẤU HÌNH TỪNG KHỐI AOI

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_AIn`
**Thuộc chương:** [Chương 1: Xử lý tín hiệu Vào/Ra (I/O Processing)](../01_CHUONG_1_IO_PROCESSING.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `IO Processing 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 21–25)  
**Tệp định nghĩa khối lệnh:** `P_AIn_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_AIn`:** **91** Biến đầu vào (`Input Parameters`) | **66** Biến đầu ra (`Output Parameters`) | **29** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_AIn` trong Studio 5000
- **Chức năng cốt lõi:** Khối đọc tín hiệu tương tự (Analog Input) tiêu chuẩn. Thực hiện quy đổi tuyến tính (Linear Scaling) từ đơn vị Raw sang đơn vị kỹ thuật (EU), lọc thông thấp bậc 1, kiểm tra đứt dây/vượt dải, cho phép nhập giá trị thay thế (Substitute PV) và giám sát 4 ngưỡng cảnh báo `HiHi`, `Hi`, `Lo`, `LoLo` kèm `Fail`.
- **Khi nào CẦN sử dụng:** Khi đọc **90% các cảm biến 4–20mA / 0–10V thông thường** trong nhà máy (nhiệt độ, áp suất, mức bồn tuyến tính, lưu lượng từ/vortex, độ dẫn điện, pH) chỉ cần quy đổi bậc nhất và cảnh báo vượt ngưỡng chuẩn.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi cần khai căn bậc hai ($\sqrt{\Delta P}$), cảnh báo tốc độ tăng/giảm nhanh (`RoC`), cảnh báo độ lệch (`Deviation`) → Dùng [`P_AInAdv.md`](./P_AInAdv.md).

### Quy trình 4 bước đấu nối và cấu hình `P_AIn`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_PV`, `Inp_PVSrcQ`, `Inp_PVNotify`, `Inp_PVBad`, `Inp_PVUncertain`, `Inp_Sim`, `Inp_HiHiGate`, `Inp_HiGate`, `Inp_LoGate`, `Inp_LoLoGate`, `Inp_FailGate`, `Inp_Reset`, `Cfg_HiHiLim`, `Cfg_HiLim`, `Cfg_LoLim`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_NoSubstPV`, `Cfg_HasChanObj`, `Cfg_UseChanSrcQ`, `Cfg_PVDecPlcs`, `Cfg_HasHiHiAlm`, `Cfg_HasHiAlm`, `Cfg_HasLoAlm`, `Cfg_HasLoLoAlm`, `Cfg_HasFailAlm`, `Cfg_HiHiResetReqd`, `Cfg_HiResetReqd`, `Cfg_LoResetReqd`, `Cfg_LoLoResetReqd`, `Cfg_FailResetReqd`, `Cfg_HasMoreObj`, `Cfg_HiHiAckReqd`, `Cfg_HiAckReqd`, `Cfg_LoAckReqd`...
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_Desc`, `Cfg_EU`, `Cfg_Label`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_AIn` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_AIn` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PCmd_ClearCapt`, `PCmd_Reset`, `PCmd_HiHiAck`, `PCmd_HiAck`, `PCmd_LoAck`, `PCmd_LoLoAck`, `PCmd_FailAck`, `PCmd_HiHiSuppress`, `PCmd_HiSuppress`, `PCmd_LoSuppress`, `PCmd_LoLoSuppress`, `PCmd_FailSuppress`, `PCmd_HiHiUnsuppress`, `PCmd_HiUnsuppress`, `PCmd_LoUnsuppress`, `PCmd_LoLoUnsuppress`, `PCmd_HiHiUnshelve`, `PCmd_HiUnshelve`...
   - **Cách sử dụng trong code PLC:** Gán giá trị đặt vào `PSet_*` (nếu có) hoặc kích xung 1 chu kỳ vào các bit lệnh `PCmd_* = 1` để xóa lỗi (`PCmd_Reset`), xác nhận cảnh báo (`PCmd_*Ack`) hoặc ức chế cảnh báo (`PCmd_*Suppress`). Khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi.
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Val`, `Val_InpPV`, `Val_PVMinCapt`, `Val_PVMaxCapt`, `Val_PVEUMin`, `Val_PVEUMax`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Sts_SubstPV`, `Sts_PVBad`, `Sts_PVUncertain`, `Sts_MaintByp`, `Sts_AlmInh`, `Sts_Err`, `Sts_RdyReset`, `Sts_RdyAck`, `Sts_HiHiCmp`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 91 Biến Đầu Vào (`Input Parameters`) của `P_AIn`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp_PV`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu biến quá trình đầu vào (Process Variable - PV) đọc từ cảm biến hoặc thẻ Analog/Discrete Input |
| **`Inp_PVSrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã nguồn gốc và chất lượng tín hiệu PV đầu vào (kiểu liệt kê enum, nối từ chân SrcQ của khối kênh P_AIChan nếu có) |
| **`Inp_PVNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã mức độ ưu tiên cảnh báo và trạng thái xác nhận (enum) của đối tượng PV liên kết |
| **`Inp_PVBad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi chất lượng tín hiệu hoặc lỗi truyền thông đầu vào PV (1 = Lỗi/Bad, 0 = Tốt/OK — thường nối từ bit Channel Fault của thẻ I/O) |
| **`Inp_PVUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái chất lượng tín hiệu đầu vào PV không chắc chắn (1 = Không chắc chắn/Uncertain, 0 = Tốt/OK) |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân kích hoạt chế độ Mô phỏng (Simulation): Khi = 1, khóa các ngõ ra vật lý ở mức an toàn và dùng giá trị/phản hồi giả lập nội bộ; khi = 0, vận hành thực tế bình thường |
| **`Inp_HiHiGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát mức Rất Cao (High-High): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_HiGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát mức Cao (High): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_LoGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát mức Thấp (Low): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_LoLoGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát mức Rất Thấp (Low-Low): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_FailGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát lỗi tín hiệu / hỏng kênh đo (Signal Failure): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Cfg_NoSubstPV`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Khóa chức năng nhập giá trị thay thế (Substitute PV): 0 = Cho phép nhân viên bảo trì nhập giá trị PV thay thế; 1 = Cấm sử dụng tính năng Substitute PV |
| **`Cfg_HasChanObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối chẩn đoán kênh (P_AIChan) liên kết với khối này và bật nút điều hướng mở Faceplate tương ứng |
| **`Cfg_UseChanSrcQ`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Nhận trực tiếp mã nguồn gốc và chất lượng (SrcQ) từ khối kênh P_AIChan nối ở đầu vào; 0 = Khối tự tính toán mã SrcQ nội bộ |
| **`Cfg_PVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho giá trị PV trên màn hình HMI (từ 0 đến 6 chữ số) |
| **`Cfg_HasHiHiAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo mức Rất Cao (High-High): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasHiAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo mức Cao (High): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasLoAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo mức Thấp (Low): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasLoLoAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo mức Rất Thấp (Low-Low): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasFailAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HiHiResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo mức Rất Cao (High-High): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_HiResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo mức Cao (High): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_LoResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo mức Thấp (Low): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_LoLoResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo mức Rất Thấp (Low-Low): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_FailResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_HiHiAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo mức Rất Cao (High-High): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_HiAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo mức Cao (High): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_LoAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo mức Thấp (Low): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_LoLoAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo mức Rất Thấp (Low-Low): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_FailAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_HiHiSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo mức Rất Cao (High-High) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_HiSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo mức Cao (High) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_LoSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo mức Thấp (Low) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_LoLoSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo mức Rất Thấp (Low-Low) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_FailSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_InpRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đầu vào thô tối thiểu (Raw Min, ví dụ 4.0 mA hoặc 0) dùng để quy đổi tỷ lệ tuyến tính sang đơn vị kỹ thuật |
| **`Cfg_InpRawMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đầu vào thô tối đa (Raw Max, ví dụ 20.0 mA hoặc 100) dùng để quy đổi tỷ lệ tuyến tính sang đơn vị kỹ thuật |
| **`Cfg_PVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tương ứng với Raw Min (PV EU Min — hỗ trợ cả quy đổi ngược nếu đặt EUMin > EUMax) |
| **`Cfg_PVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tương ứng với Raw Max (PV EU Max — hỗ trợ cả quy đổi ngược nếu đặt EUMax < EUMin) |
| **`Cfg_FiltTC`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Hằng số thời gian của bộ lọc thông thấp bậc 1 cho tín hiệu PV (tính bằng giây; đặt = 0.0 nếu không lọc) |
| **`Cfg_HiHiLim`** | `REAL` | `1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Ngưỡng giới hạn tác động (Threshold Limit) cho mức Rất Cao (High-High) (tính theo đơn vị kỹ thuật EU) |
| **`Cfg_HiHiDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng mức Rất Cao (High-High) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_HiDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng mức Cao (High) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_LoDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng mức Thấp (Low) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_LoLoDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng mức Rất Thấp (Low-Low) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_FailDB`** | `REAL` | `0.41666666000000002` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng lỗi tín hiệu / hỏng kênh đo (Signal Failure) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_HiHiGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho mức Rất Cao (High-High): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_HiGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho mức Cao (High): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_LoGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho mức Thấp (Low): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_LoLoGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho mức Rất Thấp (Low-Low): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_FailGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho lỗi tín hiệu / hỏng kênh đo (Signal Failure): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_HiHiOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho mức Rất Cao (High-High): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_HiOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho mức Cao (High): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_LoOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho mức Thấp (Low): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_LoLoOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho mức Rất Thấp (Low-Low): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_FailOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho lỗi tín hiệu / hỏng kênh đo (Signal Failure): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_HiHiOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho mức Rất Cao (High-High): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_HiOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho mức Cao (High): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_LoOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho mức Thấp (Low): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_LoLoOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho mức Rất Thấp (Low-Low): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_FailOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho lỗi tín hiệu / hỏng kênh đo (Signal Failure): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_HiLim`** | `REAL` | `1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Ngưỡng giới hạn tác động (Threshold Limit) cho mức Cao (High) (tính theo đơn vị kỹ thuật EU) |
| **`Cfg_LoLim`** | `REAL` | `1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Ngưỡng giới hạn tác động (Threshold Limit) cho mức Thấp (Low) (tính theo đơn vị kỹ thuật EU) |
| **`Cfg_LoLoLim`** | `REAL` | `1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Ngưỡng giới hạn tác động (Threshold Limit) cho mức Rất Thấp (Low-Low) (tính theo đơn vị kỹ thuật EU) |
| **`Cfg_FailHiLim`** | `REAL` | `103.958336` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng giới hạn trên báo lỗi đứt dây / vượt dải tín hiệu đầu vào (Out-of-Range High, đơn vị thô Raw hoặc EU) |
| **`Cfg_FailLoLim`** | `REAL` | `-2.0833333000000001` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng giới hạn dưới báo lỗi đứt dây / vượt dải tín hiệu đầu vào (Out-of-Range Low, đơn vị thô Raw hoặc EU) |
| **`Set_SimPV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Giá trị PV giả lập (đơn vị kỹ thuật EU) được sử dụng khi bật chế độ Mô phỏng (Inp_Sim = 1) |
| **`PCmd_ClearCapt`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa giá trị đỉnh Cực tiểu / Cực đại (Min/Max Excursion) đã lưu của PV |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`PCmd_HiHiAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo mức Rất Cao (High-High) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_HiAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo mức Cao (High) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_LoAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo mức Thấp (Low) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_LoLoAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo mức Rất Thấp (Low-Low) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_FailAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_HiHiSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo mức Rất Cao (High-High) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_HiSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo mức Cao (High) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_LoSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo mức Thấp (Low) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_LoLoSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo mức Rất Thấp (Low-Low) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_HiHiUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo mức Rất Cao (High-High) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_HiUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo mức Cao (High) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_LoUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo mức Thấp (Low) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_LoLoUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo mức Rất Thấp (Low-Low) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_HiHiUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo mức Rất Cao (High-High) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_HiUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo mức Cao (High) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_LoUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo mức Thấp (Low) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_LoLoUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo mức Rất Thấp (Low-Low) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_FailUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) ngay lập tức (biến xung tự động xóa về 0) |

## 3. Bảng chi tiết 66 Biến Đầu Ra (`Output Parameters`) của `P_AIn`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Val`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu ra chính của biến quá trình PV (đã qua quy đổi đơn vị kỹ thuật EU, lọc nhiễu và chọn giá trị thay thế Substitute PV nếu có) |
| **`Val_InpPV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị PV thực tế tính từ tín hiệu đầu vào (sau khi quy đổi đơn vị EU nhưng trước khi qua bộ chọn Substitute PV) |
| **`Val_PVMinCapt`** | `REAL` | `1.5000000000000001E+38` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cực tiểu (Minimum Peak Excursion) của PV ghi nhận được kể từ lần xóa (ClearCapt) gần nhất |
| **`Val_PVMaxCapt`** | `REAL` | `-1.5000000000000001E+38` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cực đại (Maximum Peak Excursion) của PV ghi nhận được kể từ lần xóa (ClearCapt) gần nhất |
| **`Val_PVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận dưới của dải đo đơn vị kỹ thuật PV = MIN(Cfg_PVEUMin, Cfg_PVEUMax) |
| **`Val_PVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận trên của dải đo đơn vị kỹ thuật PV = MAX(Cfg_PVEUMin, Cfg_PVEUMax) |
| **`SrcQ_IO`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng dữ liệu tổng hợp cuối cùng (0 = Tín hiệu thực Tốt đã xác nhận, 1 = Tín hiệu thực Tốt giả định, 2 = Không cấu hình phản hồi/Tốt giả định, 8 = Đang mô phỏng, 9 = Mô phỏng vòng lặp, 10 = Giá trị nhập tay, 16 = Ngoài đặc tính kỹ thuật, 17 = Thay thế tại thiết bị/bus, 18 = Thay thế bởi Bảo trì, 19 = Ngắt bảo vệ giữ giá trị cũ, 20 = Ngắt bảo vệ dùng giá trị thay thế, 32 = Lỗi tín hiệu vượt dải/NaN, 33 = Lỗi kênh I/O, 34 = Lỗi module I/O, 35 = Lỗi cấu hình) |
| **`Val_Fault`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái lỗi thiết bị (Device Fault Status — kiểu liệt kê enum: 0 = Không lỗi, 16 = Lỗi khởi động/mở, 17 = Lỗi dừng/đóng, 20 = Lo, 21 = Hi, 24 = LoLo, 25 = HiHi, 32 = Fail/I/O Fault, 34 = Lỗi cấu hình CfgErr) |
| **`Val_NotifyAll`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận tổng hợp của khối này cộng với các đối tượng kênh/quá tải liên kết |
| **`Val_UnackAlmC`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms Count) |
| **`Sts_SubstPV`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang sử dụng giá trị PV thay thế (Substitute PV do nhân viên bảo trì nhập) thay cho tín hiệu cảm biến thật |
| **`Sts_PVBad`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Chất lượng tín hiệu PV Xấu (Bad Quality) hoặc tín hiệu vượt ngoài dải lỗi (Out of Range) |
| **`Sts_PVUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Chất lượng tín hiệu PV Không chắc chắn (Uncertain Quality) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoặc cưỡng bức bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có ít nhất một cảnh báo bị Tạm hoãn (Shelved), Vô hiệu hóa (Disabled) hoặc Ức chế (Suppressed) — hiển thị biểu tượng chữ "I" trên HMI |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_Raw`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải giá trị thô đầu vào (Cfg_InpRawMin = Cfg_InpRawMax) bị trùng nhau |
| **`Err_EU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải quy đổi đơn vị kỹ thuật (Engineering Units) có giá trị Tối thiểu bằng Tối đa (Min = Max) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị đặt bộ định thời gian (Timer Preset) nằm ngoài phạm vi hợp lệ 0..2.147.483 giây |
| **`Err_Filt`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thông số bộ lọc tín hiệu PV (Cfg_FiltTC hoặc Cfg_RateTime) không hợp lệ |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình cảnh báo: Thời gian duy trì tối thiểu (Min On Time), thời gian tạm hoãn (Shelf Time) hoặc mức độ nghiêm trọng (Severity 1..1000) không hợp lệ |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc chốt ngắt bảo vệ (Shed) đã hết lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_HiHiCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của mức Rất Cao (High-High) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_HiHiGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của mức Rất Cao (High-High) đã đếm xong, đang cho phép giám sát |
| **`Sts_HiHi`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện mức Rất Cao (High-High) |
| **`Alm_HiHi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho mức Rất Cao (High-High) |
| **`Ack_HiHi`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động mức Rất Cao (High-High) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_HiHiDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Rất Cao (High-High) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_HiHiSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Rất Cao (High-High) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_HiHiShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Rất Cao (High-High) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_HiCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của mức Cao (High) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_HiGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của mức Cao (High) đã đếm xong, đang cho phép giám sát |
| **`Sts_Hi`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện mức Cao (High) |
| **`Alm_Hi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho mức Cao (High) |
| **`Ack_Hi`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động mức Cao (High) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_HiDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Cao (High) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_HiSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Cao (High) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_HiShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Cao (High) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_LoCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của mức Thấp (Low) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_LoGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của mức Thấp (Low) đã đếm xong, đang cho phép giám sát |
| **`Sts_Lo`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện mức Thấp (Low) |
| **`Alm_Lo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho mức Thấp (Low) |
| **`Ack_Lo`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động mức Thấp (Low) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_LoDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Thấp (Low) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_LoSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Thấp (Low) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_LoShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Thấp (Low) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_LoLoCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của mức Rất Thấp (Low-Low) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_LoLoGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của mức Rất Thấp (Low-Low) đã đếm xong, đang cho phép giám sát |
| **`Sts_LoLo`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện mức Rất Thấp (Low-Low) |
| **`Alm_LoLo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho mức Rất Thấp (Low-Low) |
| **`Ack_LoLo`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động mức Rất Thấp (Low-Low) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_LoLoDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Rất Thấp (Low-Low) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_LoLoSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Rất Thấp (Low-Low) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_LoLoShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Rất Thấp (Low-Low) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_FailCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của lỗi tín hiệu / hỏng kênh đo (Signal Failure) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_FailGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của lỗi tín hiệu / hỏng kênh đo (Signal Failure) đã đếm xong, đang cho phép giám sát |
| **`Sts_Fail`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi tín hiệu / hỏng kênh đo (Signal Failure) |
| **`Alm_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi tín hiệu / hỏng kênh đo (Signal Failure) |
| **`Ack_Fail`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi tín hiệu / hỏng kênh đo (Signal Failure) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_FailDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_FailSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_FailShelved`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`P_AIn`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_AIn) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 29 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_AIn`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_8` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'Analog Input'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật (Engineering Units, ví dụ: "°C", "bar", "m3/h", "%") hiển thị trên màn hình HMI |
| **`Cfg_Label`** | `STRING_20` | `'Analog Input'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_Tag`** | `STRING_20` | `'P_AIn'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`Fail`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi tín hiệu / hỏng kênh đo (Signal Failure) |
| **`FailGate`** | `P_Gate` | `{...}` | [Khối con P_Gate nội bộ] Bộ định thời trễ mở cổng (Gate Delay), trễ bật (On-Delay) và trễ tắt (Off-Delay) cho lỗi tín hiệu / hỏng kênh đo (Signal Failure) |
| **`Hi`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho mức Cao (High) |
| **`HiGate`** | `P_Gate` | `{...}` | [Khối con P_Gate nội bộ] Bộ định thời trễ mở cổng (Gate Delay), trễ bật (On-Delay) và trễ tắt (Off-Delay) cho mức Cao (High) |
| **`HiHi`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho mức Rất Cao (High-High) |
| **`HiHiGate`** | `P_Gate` | `{...}` | [Khối con P_Gate nội bộ] Bộ định thời trễ mở cổng (Gate Delay), trễ bật (On-Delay) và trễ tắt (Off-Delay) cho mức Rất Cao (High-High) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_AIn'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`Lo`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho mức Thấp (Low) |
| **`LoGate`** | `P_Gate` | `{...}` | [Khối con P_Gate nội bộ] Bộ định thời trễ mở cổng (Gate Delay), trễ bật (On-Delay) và trễ tắt (Off-Delay) cho mức Thấp (Low) |
| **`LoLo`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho mức Rất Thấp (Low-Low) |
| **`LoLoGate`** | `P_Gate` | `{...}` | [Khối con P_Gate nội bộ] Bộ định thời trễ mở cổng (Gate Delay), trễ bật (On-Delay) và trễ tắt (Off-Delay) cho mức Rất Thấp (Low-Low) |
| **`MCmd_InpPV`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Sử dụng lại tín hiệu PV thực tế từ cảm biến đầu vào (ngưng dùng giá trị thay thế Substitute PV) |
| **`MCmd_SubstPV`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Chuyển sang sử dụng giá trị PV thay thế (Substitute PV) do nhân viên bảo trì nhập |
| **`MRdy_InpPV`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_InpPV (Sử dụng lại tín hiệu PV thực tế từ cảm biến đầu vào (ngưng dùng giá trị thay thế Substitute PV)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_SubstPV`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_SubstPV (Chuyển sang sử dụng giá trị PV thay thế (Substitute PV) do nhân viên bảo trì nhập), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MSet_SubstPV`** | `REAL` | `0` | [Giá trị đặt từ Bảo trì HMI] Giá trị đặt từ Nhân viên bảo trì (Maintenance HMI): Giá trị PV thay thế (Substitute PV, đơn vị EU) do nhân viên bảo trì nhập thủ công để ghi đè tín hiệu cảm biến |
| **`OCmd_ClearCapt`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa giá trị đỉnh Cực tiểu / Cực đại (Min/Max Excursion) đã lưu của PV |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`Val_Notify`** | `SINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo hiện hành và trạng thái xác nhận của khối này (0 = Không có cảnh báo, 1 = Đã hết lỗi nhưng chờ Reset/Ack, 2..3 = Thấp, 4..5 = Trung bình, 6..7 = Cao, 8..9 = Khẩn cấp) |
---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_AIChan`
**Thuộc chương:** [Chương 1: Xử lý tín hiệu Vào/Ra (I/O Processing)](../01_CHUONG_1_IO_PROCESSING.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `IO Processing 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 26–29)  
**Tệp định nghĩa khối lệnh:** `P_AIChan_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_AIChan`:** **52** Biến đầu vào (`Input Parameters`) | **45** Biến đầu ra (`Output Parameters`) | **15** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_AIChan` trong Studio 5000
- **Chức năng cốt lõi:** Khối chẩn đoán chuyên sâu cấp độ kênh phần cứng Analog Input. Kiểm tra 9 tình huống bất thường của thẻ I/O và cảm biến (lỗi module, lỗi kênh, vượt dải, cảm biến bị đơ/treo không đổi giá trị *Stuck Input*, lỗi NaN, yêu cầu bảo trì) để phân loại chất lượng dữ liệu thành `Good`, `Uncertain` hoặc `Bad`.
- **Khi nào CẦN sử dụng:** Khi dùng thẻ Analog Input có hỗ trợ thông tin chẩn đoán mở rộng hoặc hệ thống yêu cầu **phát hiện cảm biến bị kẹt/đóng băng giá trị (`Stuck PV`)** trước khi cấp tín hiệu vào khối `P_AIn`, `P_AInAdv` hoặc `P_AInDual`.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Không dùng độc lập để hiển thị giá trị quá trình hay cảnh báo ngưỡng công nghệ HH/H/L/LL (luôn phải nối sau nó một khối `P_AIn*`).

### Quy trình 4 bước đấu nối và cấu hình `P_AIChan`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_Raw`, `Inp_ModFault`, `Inp_ChanFault`, `Inp_OutOfSpec`, `Inp_FuncCheck`, `Inp_MaintReqd`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_ClampSB`, `Cfg_FailOnUncertain`, `Cfg_PVDecPlcs`, `Cfg_HasFailAlm`, `Cfg_HasMoreObj`, `Cfg_FailResetReqd`, `Cfg_FailAckReqd`, `Cfg_FailSeverity`, `Cfg_InpRawMin`, `Cfg_InpRawMax`, `Cfg_PVEUMin`, `Cfg_PVEUMax`, `Cfg_PVHiLim`, `Cfg_PVLoLim`, `Cfg_InpOORHiLim`, `Cfg_InpOORLoLim`, `Cfg_InpOORDB`, `Cfg_InpOOROnDly`...
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_Desc`, `Cfg_EU`, `Cfg_Label`, `Cfg_RU`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_AIChan` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_AIChan` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PCmd_FailAck`, `PCmd_FailSuppress`, `PCmd_FailUnsuppress`, `PCmd_FailUnshelve`, `PCmd_Reset`
   - **Cách sử dụng trong code PLC:** Gán giá trị đặt vào `PSet_*` (nếu có) hoặc kích xung 1 chu kỳ vào các bit lệnh `PCmd_* = 1` để xóa lỗi (`PCmd_Reset`), xác nhận cảnh báo (`PCmd_*Ack`) hoặc ức chế cảnh báo (`PCmd_*Suppress`). Khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi.
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Val_InpRaw`, `Val`, `Val_PVEUMin`, `Val_PVEUMax`, `Val_Sts`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Sts_PVGood`, `Sts_PVUncertain`, `Sts_PVBad`, `Sts_UseInp`, `Sts_HoldLast`, `Sts_Clamped`, `Sts_Replaced`, `Sts_InpOORHiCmp`, `Sts_InpOORLoCmp`, `Sts_InpOutOfRange`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 52 Biến Đầu Vào (`Input Parameters`) của `P_AIChan`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp_Raw`** | `REAL` | `4` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu đầu vào thô chưa quy đổi (Raw Units - RU) đọc trực tiếp từ cảm biến / bộ truyền (Transmitter) |
| **`Inp_ModFault`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi module I/O hoặc mất kết nối truyền thông với module I/O (1 = Lỗi module, 0 = Tốt/OK) |
| **`Inp_ChanFault`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi phần cứng kênh I/O vật lý (1 = Lỗi kênh Channel Fault, 0 = Tốt/OK) |
| **`Inp_OutOfSpec`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Cờ báo từ thiết bị đo thông minh: Đang hoạt động ngoài đặc tính kỹ thuật (1 = Out of Specification / Uncertain, 0 = OK) |
| **`Inp_FuncCheck`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Cờ báo từ thiết bị đo thông minh: Đang ở chế độ kiểm tra chức năng / giả lập tại hiện trường (1 = Function Check, 0 = OK) |
| **`Inp_MaintReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Cờ báo từ thiết bị đo thông minh: Thiết bị yêu cầu bảo trì (1 = Maintenance Required, 0 = OK) |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Cfg_ClampSB`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Dải bắt dính (Snap-To Band, đơn vị EU) để kẹp chặt PV về đúng mức giới hạn khi tín hiệu tiến sát ngưỡng biên |
| **`Cfg_FailOnUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Kích hoạt cờ lỗi Sts_Fail và báo động ngay cả khi chất lượng tín hiệu là Không chắc chắn (Uncertain) hoặc Xấu (Bad); 0 = Chỉ báo lỗi khi chất lượng Xấu (Bad) |
| **`Cfg_PVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho giá trị PV trên màn hình HMI (từ 0 đến 6 chữ số) |
| **`Cfg_HasFailAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_FailResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_FailAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_FailSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_InpRawMin`** | `REAL` | `4` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đầu vào thô tối thiểu (Raw Min, ví dụ 4.0 mA hoặc 0) dùng để quy đổi tỷ lệ tuyến tính sang đơn vị kỹ thuật |
| **`Cfg_InpRawMax`** | `REAL` | `20` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đầu vào thô tối đa (Raw Max, ví dụ 20.0 mA hoặc 100) dùng để quy đổi tỷ lệ tuyến tính sang đơn vị kỹ thuật |
| **`Cfg_PVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tương ứng với Raw Min (PV EU Min — hỗ trợ cả quy đổi ngược nếu đặt EUMin > EUMax) |
| **`Cfg_PVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tương ứng với Raw Max (PV EU Max — hỗ trợ cả quy đổi ngược nếu đặt EUMax < EUMin) |
| **`Cfg_PVHiLim`** | `REAL` | `1.5000000000000001E+38` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng kẹp giới hạn trên của giá trị PV (High Clamping Threshold, đơn vị kỹ thuật EU) |
| **`Cfg_PVLoLim`** | `REAL` | `-1.5000000000000001E+38` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng kẹp giới hạn dưới của giá trị PV (Low Clamping Threshold, đơn vị kỹ thuật EU) |
| **`Cfg_InpOORHiLim`** | `REAL` | `20.733333999999999` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng giới hạn trên báo lỗi tín hiệu thô vượt dải (Out-of-Range High Limit, đơn vị thô Raw) |
| **`Cfg_InpOORLoLim`** | `REAL` | `3.6666666999999999` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng giới hạn dưới báo lỗi tín hiệu thô vượt dải / đứt dây (Out-of-Range Low Limit, đơn vị thô Raw) |
| **`Cfg_InpOORDB`** | `REAL` | `6.6666669999999997E-2` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband, đơn vị thô Raw) cho ngưỡng báo lỗi vượt dải trên/dưới (Out-of-Range) |
| **`Cfg_InpOOROnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tối thiểu tín hiệu nằm ngoài dải trước khi kích hoạt cờ lỗi Out-of-Range (giây) |
| **`Cfg_InpOOROffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tối thiểu tín hiệu trở lại trong dải hợp lệ trước khi xóa cờ lỗi Out-of-Range (giây) |
| **`Cfg_InpOORAction`** | `BOOL` | `-` | Ẩn (Tag) | [Biến cấu hình thông số] Hành động xử lý ngõ ra PV khi tín hiệu đầu vào vượt dải (Out-of-Range): 1 = Giữ nguyên tín hiệu đi qua, 2 = Giữ giá trị tốt cuối cùng (Hold Last Good), 3 = Thay bằng giá trị Cfg_PVReplaceVal |
| **`Cfg_InpStuckAction`** | `BOOL` | `-` | Ẩn (Tag) | [Biến cấu hình thông số] Hành động xử lý ngõ ra PV khi tín hiệu cảm biến bị treo/đóng băng (Stuck Input): 1 = Cho đi qua, 2 = Giữ giá trị tốt cuối cùng, 3 = Thay bằng Cfg_PVReplaceVal |
| **`Cfg_InpNaNAction`** | `BOOL` | `-` | Ẩn (Tag) | [Biến cấu hình thông số] Hành động xử lý ngõ ra PV khi giá trị đầu vào bị lỗi không phải là số (NaN): 1 = Cho đi qua, 2 = Giữ giá trị tốt cuối cùng, 3 = Thay bằng Cfg_PVReplaceVal |
| **`Cfg_ModFaultAction`** | `BOOL` | `-` | Ẩn (Tag) | [Biến cấu hình thông số] Hành động xử lý ngõ ra PV khi lỗi module I/O (I/O Module Fault): 1 = Cho đi qua, 2 = Giữ giá trị tốt cuối cùng, 3 = Thay bằng Cfg_PVReplaceVal |
| **`Cfg_ChanFaultAction`** | `BOOL` | `-` | Ẩn (Tag) | [Biến cấu hình thông số] Hành động xử lý ngõ ra PV khi lỗi kênh vật lý (I/O Channel Fault): 1 = Cho đi qua, 2 = Giữ giá trị tốt cuối cùng, 3 = Thay bằng Cfg_PVReplaceVal |
| **`Cfg_OutOfSpecAction`** | `BOOL` | `-` | Ẩn (Tag) | [Biến cấu hình thông số] Hành động xử lý ngõ ra PV khi thiết bị đo báo Ngoài đặc tính (Out of Spec): 1 = Cho đi qua, 2 = Giữ giá trị tốt cuối cùng, 3 = Thay bằng Cfg_PVReplaceVal |
| **`Cfg_FuncCheckAction`** | `BOOL` | `-` | Ẩn (Tag) | [Biến cấu hình thông số] Hành động xử lý ngõ ra PV khi thiết bị đo báo Kiểm tra chức năng (Function Check): 1 = Cho đi qua, 2 = Giữ giá trị tốt cuối cùng, 3 = Thay bằng Cfg_PVReplaceVal |
| **`Cfg_MaintReqdAction`** | `BOOL` | `-` | Ẩn (Tag) | [Biến cấu hình thông số] Hành động xử lý ngõ ra PV khi thiết bị đo báo Yêu cầu bảo trì (Maintenance Required): 1 = Cho đi qua, 2 = Giữ giá trị tốt cuối cùng, 3 = Thay bằng Cfg_PVReplaceVal |
| **`Cfg_CfgErrAction`** | `BOOL` | `-` | Ẩn (Tag) | [Biến cấu hình thông số] Hành động xử lý ngõ ra PV khi khối AOI bị lỗi cài đặt cấu hình (Config Error): 1 = Cho đi qua, 2 = Giữ giá trị tốt cuối cùng, 3 = Thay bằng Cfg_PVReplaceVal |
| **`Cfg_InpOORQual`** | `BOOL` | `-` | Ẩn (Tag) | [Biến cấu hình thông số] Mức chất lượng PV báo cáo khi tín hiệu đầu vào vượt dải (Out-of-Range): 1 = Tốt (Good), 2 = Không chắc chắn (Uncertain), 3 = Xấu (Bad) |
| **`Cfg_InpStuckQual`** | `BOOL` | `-` | Ẩn (Tag) | [Biến cấu hình thông số] Mức chất lượng PV báo cáo khi tín hiệu cảm biến bị treo (Stuck Input): 1 = Tốt (Good), 2 = Không chắc chắn (Uncertain), 3 = Xấu (Bad) |
| **`Cfg_InpNaNQual`** | `BOOL` | `-` | Ẩn (Tag) | [Biến cấu hình thông số] Mức chất lượng PV báo cáo khi giá trị đầu vào không phải là số (NaN): 1 = Tốt (Good), 2 = Không chắc chắn (Uncertain), 3 = Xấu (Bad) |
| **`Cfg_ModFaultQual`** | `BOOL` | `-` | Ẩn (Tag) | [Biến cấu hình thông số] Mức chất lượng PV báo cáo khi lỗi module I/O (Module Fault): 1 = Tốt (Good), 2 = Không chắc chắn (Uncertain), 3 = Xấu (Bad) |
| **`Cfg_ChanFaultQual`** | `BOOL` | `-` | Ẩn (Tag) | [Biến cấu hình thông số] Mức chất lượng PV báo cáo khi lỗi kênh vật lý (Channel Fault): 1 = Tốt (Good), 2 = Không chắc chắn (Uncertain), 3 = Xấu (Bad) |
| **`Cfg_OutOfSpecQual`** | `BOOL` | `-` | Ẩn (Tag) | [Biến cấu hình thông số] Mức chất lượng PV báo cáo khi thiết bị đo báo Ngoài đặc tính (Out of Spec): 1 = Tốt (Good), 2 = Không chắc chắn (Uncertain), 3 = Xấu (Bad) |
| **`Cfg_FuncCheckQual`** | `BOOL` | `-` | Ẩn (Tag) | [Biến cấu hình thông số] Mức chất lượng PV báo cáo khi thiết bị đo báo Kiểm tra chức năng (Function Check): 1 = Tốt (Good), 2 = Không chắc chắn (Uncertain), 3 = Xấu (Bad) |
| **`Cfg_MaintReqdQual`** | `BOOL` | `-` | Ẩn (Tag) | [Biến cấu hình thông số] Mức chất lượng PV báo cáo khi thiết bị đo báo Yêu cầu bảo trì (Maintenance Required): 1 = Tốt (Good), 2 = Không chắc chắn (Uncertain), 3 = Xấu (Bad) |
| **`Cfg_CfgErrQual`** | `BOOL` | `-` | Ẩn (Tag) | [Biến cấu hình thông số] Mức chất lượng PV báo cáo khi khối AOI bị lỗi cấu hình (Config Error): 1 = Tốt (Good), 2 = Không chắc chắn (Uncertain), 3 = Xấu (Bad) |
| **`Cfg_StuckT`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian tối đa tín hiệu đầu vào đứng im không thay đổi giá trị trước khi báo lỗi treo cảm biến (Stuck Input, tính bằng giây) |
| **`Cfg_PVReplaceVal`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị PV dự phòng (đơn vị kỹ thuật EU) dùng để thay thế cho ngõ ra khi hành động xử lý lỗi kênh được chọn là Thay thế (= 3) |
| **`PCmd_FailAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_FailSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |

## 3. Bảng chi tiết 45 Biến Đầu Ra (`Output Parameters`) của `P_AIChan`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Val_InpRaw`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu vào thô (Raw Units - RU) trước khi quy đổi tỷ lệ hoặc kiểm tra giới hạn |
| **`Val`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu ra chính của biến quá trình PV (đã qua quy đổi đơn vị kỹ thuật EU, lọc nhiễu và chọn giá trị thay thế Substitute PV nếu có) |
| **`Val_PVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận dưới của dải đo đơn vị kỹ thuật PV = MIN(Cfg_PVEUMin, Cfg_PVEUMax) |
| **`Val_PVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận trên của dải đo đơn vị kỹ thuật PV = MAX(Cfg_PVEUMin, Cfg_PVEUMax) |
| **`SrcQ_IO`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng dữ liệu tổng hợp cuối cùng (0 = Tín hiệu thực Tốt đã xác nhận, 1 = Tín hiệu thực Tốt giả định, 2 = Không cấu hình phản hồi/Tốt giả định, 8 = Đang mô phỏng, 9 = Mô phỏng vòng lặp, 10 = Giá trị nhập tay, 16 = Ngoài đặc tính kỹ thuật, 17 = Thay thế tại thiết bị/bus, 18 = Thay thế bởi Bảo trì, 19 = Ngắt bảo vệ giữ giá trị cũ, 20 = Ngắt bảo vệ dùng giá trị thay thế, 32 = Lỗi tín hiệu vượt dải/NaN, 33 = Lỗi kênh I/O, 34 = Lỗi module I/O, 35 = Lỗi cấu hình) |
| **`Val_Sts`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái hoạt động xác nhận của thiết bị (Device Confirmed Status enum dùng để điều khiển màu sắc và biểu tượng động trên HMI) |
| **`Val_Fault`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái lỗi thiết bị (Device Fault Status — kiểu liệt kê enum: 0 = Không lỗi, 16 = Lỗi khởi động/mở, 17 = Lỗi dừng/đóng, 20 = Lo, 21 = Hi, 24 = LoLo, 25 = HiHi, 32 = Fail/I/O Fault, 34 = Lỗi cấu hình CfgErr) |
| **`Val_NotifyAll`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận tổng hợp của khối này cộng với các đối tượng kênh/quá tải liên kết |
| **`Val_UnackAlmC`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms Count) |
| **`Sts_PVGood`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Chất lượng tín hiệu PV Tốt (Good — không bị gắn cờ Bad hay Uncertain) |
| **`Sts_PVUncertain`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Chất lượng tín hiệu PV Không chắc chắn (Uncertain Quality) |
| **`Sts_PVBad`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Chất lượng tín hiệu PV Xấu (Bad Quality) hoặc tín hiệu vượt ngoài dải lỗi (Out of Range) |
| **`Sts_UseInp`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang sử dụng tín hiệu thực từ cảm biến để tính PV (không bị giữ giá trị cũ Hold hoặc thay thế Replace) |
| **`Sts_HoldLast`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị PV đang được giữ ở giá trị tốt cuối cùng (Hold Last Good Value) do phát hiện lỗi kênh đo |
| **`Sts_Clamped`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị PV tương tự đang bị kẹp ở ngưỡng giới hạn dưới (Cfg_PVLoLim) hoặc giới hạn trên (Cfg_PVHiLim) |
| **`Sts_Replaced`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị PV đang được thay thế bằng giá trị cấu hình định trước (Cfg_PVReplaceVal) do phát hiện lỗi kênh đo |
| **`Sts_InpOORHiCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị đầu vào thô hiện đang vượt quá ngưỡng lỗi vượt dải trên (Cfg_InpOORHiLim) |
| **`Sts_InpOORLoCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị đầu vào thô hiện đang thấp hơn ngưỡng lỗi vượt dải dưới (Cfg_InpOORLoLim) |
| **`Sts_InpOutOfRange`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào vượt ngoài dải đo cho phép (Out of Range — đã tính trễ On-Delay, Deadband, Off-Delay) |
| **`Sts_InpStuck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào bị treo/đóng băng không thay đổi giá trị (Stuck Input) quá thời gian Cfg_StuckT |
| **`Sts_InpNaN`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị đầu vào không phải là số hợp lệ (Not-a-Number / lỗi số thực dấu phẩy động) |
| **`Sts_ModFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có lỗi phần cứng hoặc mất kết nối với module I/O (I/O Module Fault) |
| **`Sts_ChanFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có lỗi kênh vật lý trên thẻ I/O (I/O Channel Fault) |
| **`Sts_OutOfSpec`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị đo hiện trường báo đang làm việc ngoài đặc tính kỹ thuật (Out of Specification) |
| **`Sts_FuncCheck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị đo hiện trường báo đang ở chế độ kiểm tra chức năng / giả lập tại chỗ (Function Check) |
| **`Sts_MaintReqd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị đo hiện trường báo yêu cầu bảo trì (Maintenance Required) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có ít nhất một cảnh báo bị Tạm hoãn (Shelved), Vô hiệu hóa (Disabled) hoặc Ức chế (Suppressed) — hiển thị biểu tượng chữ "I" trên HMI |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_Raw`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải giá trị thô đầu vào (Cfg_InpRawMin = Cfg_InpRawMax) bị trùng nhau |
| **`Err_EU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải quy đổi đơn vị kỹ thuật (Engineering Units) có giá trị Tối thiểu bằng Tối đa (Min = Max) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị đặt bộ định thời gian (Timer Preset) nằm ngoài phạm vi hợp lệ 0..2.147.483 giây |
| **`Err_DB`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Độ rộng vùng chết (Deadband) của một ngưỡng cảnh báo bị đặt số âm (< 0.0) |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình cảnh báo: Thời gian duy trì tối thiểu (Min On Time), thời gian tạm hoãn (Shelf Time) hoặc mức độ nghiêm trọng (Severity 1..1000) không hợp lệ |
| **`Err_Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Ngưỡng kẹp giới hạn trên của PV nhỏ hơn ngưỡng kẹp giới hạn dưới (Cfg_PVHiLim < Cfg_PVLoLim) |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc chốt ngắt bảo vệ (Shed) đã hết lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi tín hiệu / hỏng kênh đo (Signal Failure) |
| **`Alm_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi tín hiệu / hỏng kênh đo (Signal Failure) |
| **`Ack_Fail`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi tín hiệu / hỏng kênh đo (Signal Failure) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_FailDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_FailSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_FailShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`P_AIChan`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_AIChan) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 15 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_AIChan`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_8` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'Analog Channel Quality'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật (Engineering Units, ví dụ: "°C", "bar", "m3/h", "%") hiển thị trên màn hình HMI |
| **`Cfg_Label`** | `STRING_20` | `'Analog In Channel'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_RU`** | `STRING_8` | `'mA DC'` | [Biến cấu hình thông số] Chuỗi đơn vị thô (Raw Units, ví dụ: "mA", "V", "Counts") hiển thị trên màn hình HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_AIChan'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`Fail`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi tín hiệu / hỏng kênh đo (Signal Failure) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_AIChan'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`Val_Notify`** | `SINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo hiện hành và trạng thái xác nhận của khối này (0 = Không có cảnh báo, 1 = Đã hết lỗi nhưng chờ Reset/Ack, 2..3 = Thấp, 4..5 = Trung bình, 6..7 = Cao, 8..9 = Khẩn cấp) |
---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_AInAdv`
**Thuộc chương:** [Chương 1: Xử lý tín hiệu Vào/Ra (I/O Processing)](../01_CHUONG_1_IO_PROCESSING.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `IO Processing 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 30–34)  
**Tệp định nghĩa khối lệnh:** `P_AInAdv_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_AInAdv`:** **138** Biến đầu vào (`Input Parameters`) | **97** Biến đầu ra (`Output Parameters`) | **34** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_AInAdv` trong Studio 5000
- **Chức năng cốt lõi:** Khối xử lý tín hiệu Analog nâng cao. Ngoài toàn bộ tính năng của `P_AIn`, bổ sung thêm: **Khai căn bậc hai (Square Root)**, **Ngưỡng cắt lưu lượng nhỏ (Low Flow Cutoff)**, 2 cảnh báo **Tốc độ biến thiên (`HiRoC`, `LoRoC`)** và 2 cảnh báo **Độ lệch so với tham chiếu (`HiDev`, `LoDev`)**.
- **Khi nào CẦN sử dụng:** 1. Đo lưu lượng bằng chênh áp (Orifice, Venturi, Pitot) xuất tín hiệu $\Delta P$ chưa khai căn. 2. Giám sát nhiệt độ lò phản ứng cần cảnh báo khi tốc độ tăng nhiệt quá nhanh (`HiRoC`) hoặc tụt áp đột ngột (`LoRoC`).
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi chỉ đọc cảm biến thông thường và muốn tiết kiệm bộ nhớ RAM bộ điều khiển → Dùng [`P_AIn.md`](./P_AIn.md).

### Quy trình 4 bước đấu nối và cấu hình `P_AInAdv`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_PV`, `Inp_PVSrcQ`, `Inp_PVNotify`, `Inp_PVBad`, `Inp_PVUncertain`, `Inp_Sim`, `Inp_HiHiGate`, `Inp_HiGate`, `Inp_LoGate`, `Inp_LoLoGate`, `Inp_HiRoCGate`, `Inp_HiDevGate`, `Inp_LoDevGate`, `Inp_FailGate`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_CtrlHiDB`, `Cfg_CtrlHiHiDB`, `Cfg_CtrlHiHiLim`, `Cfg_CtrlHiLim`, `Cfg_CtrlLoDB`, `Cfg_CtrlLoLim`, `Cfg_CtrlLoLoDB`, `Cfg_CtrlLoLoLim`, `Cfg_UseSqRt`, `Cfg_HasMoreObj`, `Cfg_HasRoC`, `Cfg_HasDev`, `Cfg_NoSubstPV`, `Cfg_HasChanObj`, `Cfg_UseChanSrcQ`, `Cfg_PVDecPlcs`, `Cfg_HasHiHiAlm`, `Cfg_HasHiAlm`...
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_Desc`, `Cfg_EU`, `Cfg_Label`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_AInAdv` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_AInAdv` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PCmd_ClearCapt`, `PCmd_Reset`, `PCmd_HiHiAck`, `PCmd_HiAck`, `PCmd_LoAck`, `PCmd_LoLoAck`, `PCmd_HiRoCAck`, `PCmd_HiDevAck`, `PCmd_LoDevAck`, `PCmd_FailAck`, `PCmd_HiHiSuppress`, `PCmd_HiSuppress`, `PCmd_LoSuppress`, `PCmd_LoLoSuppress`, `PCmd_FailSuppress`, `PCmd_HiHiUnsuppress`, `PCmd_HiUnsuppress`, `PCmd_LoUnsuppress`...
   - **Cách sử dụng trong code PLC:** Gán giá trị đặt vào `PSet_*` (nếu có) hoặc kích xung 1 chu kỳ vào các bit lệnh `PCmd_* = 1` để xóa lỗi (`PCmd_Reset`), xác nhận cảnh báo (`PCmd_*Ack`) hoặc ức chế cảnh báo (`PCmd_*Suppress`). Khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi.
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Val`, `Val_InpPV`, `Val_RoC`, `Val_Dev`, `Val_PVMinCapt`, `Val_PVMaxCapt`, `Val_PVEUMin`, `Val_PVEUMax`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Sts_SubstPV`, `Sts_PVBad`, `Sts_PVUncertain`, `Sts_MaintByp`, `Sts_AlmInh`, `Sts_Err`, `Sts_RdyReset`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 138 Biến Đầu Vào (`Input Parameters`) của `P_AInAdv`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp_PV`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu biến quá trình đầu vào (Process Variable - PV) đọc từ cảm biến hoặc thẻ Analog/Discrete Input |
| **`Inp_PVSrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã nguồn gốc và chất lượng tín hiệu PV đầu vào (kiểu liệt kê enum, nối từ chân SrcQ của khối kênh P_AIChan nếu có) |
| **`Inp_PVNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã mức độ ưu tiên cảnh báo và trạng thái xác nhận (enum) của đối tượng PV liên kết |
| **`Inp_PVBad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi chất lượng tín hiệu hoặc lỗi truyền thông đầu vào PV (1 = Lỗi/Bad, 0 = Tốt/OK — thường nối từ bit Channel Fault của thẻ I/O) |
| **`Inp_PVUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái chất lượng tín hiệu đầu vào PV không chắc chắn (1 = Không chắc chắn/Uncertain, 0 = Tốt/OK) |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân kích hoạt chế độ Mô phỏng (Simulation): Khi = 1, khóa các ngõ ra vật lý ở mức an toàn và dùng giá trị/phản hồi giả lập nội bộ; khi = 0, vận hành thực tế bình thường |
| **`Inp_HiHiGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát mức Rất Cao (High-High): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_HiGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát mức Cao (High): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_LoGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát mức Thấp (Low): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_LoLoGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát mức Rất Thấp (Low-Low): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_HiRoCGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát tốc độ biến thiên Cao (High Rate-of-Change): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_HiDevGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát độ lệch Cao (High Deviation): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_LoDevGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát độ lệch Thấp (Low Deviation): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_FailGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát lỗi tín hiệu / hỏng kênh đo (Signal Failure): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Cfg_CtrlHiDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết (Deadband) cho ngưỡng điều khiển mức Cao (đơn vị EU) |
| **`Cfg_CtrlHiHiDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết (Deadband) cho ngưỡng điều khiển mức Rất Cao (đơn vị EU) |
| **`Cfg_CtrlHiHiLim`** | `REAL` | `1.5000000000000001E+38` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng giới hạn điều khiển mức Rất Cao (High-High Control Threshold, đơn vị EU) |
| **`Cfg_CtrlHiLim`** | `REAL` | `1.5000000000000001E+38` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng giới hạn điều khiển mức Cao (High Control Threshold, đơn vị EU) |
| **`Cfg_CtrlLoDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết (Deadband) cho ngưỡng điều khiển mức Thấp (đơn vị EU) |
| **`Cfg_CtrlLoLim`** | `REAL` | `-1.5000000000000001E+38` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng giới hạn điều khiển mức Thấp (Low Control Threshold, đơn vị EU) |
| **`Cfg_CtrlLoLoDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết (Deadband) cho ngưỡng điều khiển mức Rất Thấp (đơn vị EU) |
| **`Cfg_CtrlLoLoLim`** | `REAL` | `-1.5000000000000001E+38` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng giới hạn điều khiển mức Rất Thấp (Low-Low Control Threshold, đơn vị EU) |
| **`Cfg_UseSqRt`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Áp dụng hàm khai căn bậc hai (Square Root Characterization) cho phép đo lưu lượng qua tấm chắn chênh áp; 0 = Quy đổi tuyến tính bậc nhất |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_HasRoC`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Bật chức năng tính tốc độ biến thiên (Rate of Change) và giám sát cảnh báo tốc độ tăng/giảm nhanh |
| **`Cfg_HasDev`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Bật chức năng nhập giá trị tham chiếu (Cfg_Ref) và giám sát cảnh báo độ lệch (Deviation Alarms) |
| **`Cfg_NoSubstPV`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Khóa chức năng nhập giá trị thay thế (Substitute PV): 0 = Cho phép nhân viên bảo trì nhập giá trị PV thay thế; 1 = Cấm sử dụng tính năng Substitute PV |
| **`Cfg_HasChanObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối chẩn đoán kênh (P_AIChan) liên kết với khối này và bật nút điều hướng mở Faceplate tương ứng |
| **`Cfg_UseChanSrcQ`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Nhận trực tiếp mã nguồn gốc và chất lượng (SrcQ) từ khối kênh P_AIChan nối ở đầu vào; 0 = Khối tự tính toán mã SrcQ nội bộ |
| **`Cfg_PVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho giá trị PV trên màn hình HMI (từ 0 đến 6 chữ số) |
| **`Cfg_HasHiHiAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo mức Rất Cao (High-High): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasHiAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo mức Cao (High): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasLoAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo mức Thấp (Low): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasLoLoAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo mức Rất Thấp (Low-Low): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasHiRoCAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo tốc độ biến thiên Cao (High Rate-of-Change): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasHiDevAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo độ lệch Cao (High Deviation): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasLoDevAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo độ lệch Thấp (Low Deviation): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasFailAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HiHiResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo mức Rất Cao (High-High): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_HiResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo mức Cao (High): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_LoResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo mức Thấp (Low): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_LoLoResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo mức Rất Thấp (Low-Low): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_HiRoCResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo tốc độ biến thiên Cao (High Rate-of-Change): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_HiDevResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo độ lệch Cao (High Deviation): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_LoDevResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo độ lệch Thấp (Low Deviation): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_FailResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_HiHiAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo mức Rất Cao (High-High): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_HiAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo mức Cao (High): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_LoAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo mức Thấp (Low): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_LoLoAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo mức Rất Thấp (Low-Low): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_HiRoCAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo tốc độ biến thiên Cao (High Rate-of-Change): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_HiDevAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo độ lệch Cao (High Deviation): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_LoDevAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo độ lệch Thấp (Low Deviation): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_FailAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_HiHiSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo mức Rất Cao (High-High) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_HiSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo mức Cao (High) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_LoSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo mức Thấp (Low) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_LoLoSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo mức Rất Thấp (Low-Low) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_HiRoCSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo tốc độ biến thiên Cao (High Rate-of-Change) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_HiDevSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo độ lệch Cao (High Deviation) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_LoDevSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo độ lệch Thấp (Low Deviation) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_FailSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_InpRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đầu vào thô tối thiểu (Raw Min, ví dụ 4.0 mA hoặc 0) dùng để quy đổi tỷ lệ tuyến tính sang đơn vị kỹ thuật |
| **`Cfg_InpRawMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đầu vào thô tối đa (Raw Max, ví dụ 20.0 mA hoặc 100) dùng để quy đổi tỷ lệ tuyến tính sang đơn vị kỹ thuật |
| **`Cfg_PVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tương ứng với Raw Min (PV EU Min — hỗ trợ cả quy đổi ngược nếu đặt EUMin > EUMax) |
| **`Cfg_PVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tương ứng với Raw Max (PV EU Max — hỗ trợ cả quy đổi ngược nếu đặt EUMax < EUMin) |
| **`Cfg_FiltTC`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Hằng số thời gian của bộ lọc thông thấp bậc 1 cho tín hiệu PV (tính bằng giây; đặt = 0.0 nếu không lọc) |
| **`Cfg_RateTime`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Đơn vị cơ sở thời gian để tính tốc độ biến thiên (Rate of Change): 1.0 = mỗi giây (/s), 60.0 = mỗi phút (/min), 3600.0 = mỗi giờ (/h) |
| **`Cfg_Ref`** | `REAL` | `0` | Hiện (Pin) | [Biến cấu hình thông số] Giá trị chuẩn tham chiếu (Reference Value, đơn vị EU) dùng để tính độ lệch PV cho các cảnh báo Deviation |
| **`Cfg_HiHiLim`** | `REAL` | `1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Ngưỡng giới hạn tác động (Threshold Limit) cho mức Rất Cao (High-High) (tính theo đơn vị kỹ thuật EU) |
| **`Cfg_HiHiDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng mức Rất Cao (High-High) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_HiHiOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho mức Rất Cao (High-High): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_HiHiOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho mức Rất Cao (High-High): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_HiHiGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho mức Rất Cao (High-High): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_HiLim`** | `REAL` | `1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Ngưỡng giới hạn tác động (Threshold Limit) cho mức Cao (High) (tính theo đơn vị kỹ thuật EU) |
| **`Cfg_HiDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng mức Cao (High) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_HiOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho mức Cao (High): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_HiOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho mức Cao (High): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_HiGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho mức Cao (High): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_LoLim`** | `REAL` | `-1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Ngưỡng giới hạn tác động (Threshold Limit) cho mức Thấp (Low) (tính theo đơn vị kỹ thuật EU) |
| **`Cfg_LoDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng mức Thấp (Low) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_LoOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho mức Thấp (Low): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_LoOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho mức Thấp (Low): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_LoGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho mức Thấp (Low): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_LoLoLim`** | `REAL` | `-1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Ngưỡng giới hạn tác động (Threshold Limit) cho mức Rất Thấp (Low-Low) (tính theo đơn vị kỹ thuật EU) |
| **`Cfg_LoLoDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng mức Rất Thấp (Low-Low) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_LoLoOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho mức Rất Thấp (Low-Low): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_LoLoOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho mức Rất Thấp (Low-Low): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_LoLoGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho mức Rất Thấp (Low-Low): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_HiRoCLim`** | `REAL` | `1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Ngưỡng giới hạn tác động (Threshold Limit) cho tốc độ biến thiên Cao (High Rate-of-Change) (tính theo đơn vị kỹ thuật EU) |
| **`Cfg_HiRoCDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng tốc độ biến thiên Cao (High Rate-of-Change) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_HiRoCOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho tốc độ biến thiên Cao (High Rate-of-Change): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_HiRoCOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho tốc độ biến thiên Cao (High Rate-of-Change): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_HiRoCGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho tốc độ biến thiên Cao (High Rate-of-Change): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_HiDevLim`** | `REAL` | `1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Ngưỡng giới hạn tác động (Threshold Limit) cho độ lệch Cao (High Deviation) (tính theo đơn vị kỹ thuật EU) |
| **`Cfg_HiDevDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng độ lệch Cao (High Deviation) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_HiDevOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho độ lệch Cao (High Deviation): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_HiDevOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho độ lệch Cao (High Deviation): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_HiDevGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho độ lệch Cao (High Deviation): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_LoDevLim`** | `REAL` | `1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Ngưỡng giới hạn tác động (Threshold Limit) cho độ lệch Thấp (Low Deviation) (tính theo đơn vị kỹ thuật EU) |
| **`Cfg_LoDevDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng độ lệch Thấp (Low Deviation) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_LoDevOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho độ lệch Thấp (Low Deviation): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_LoDevOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho độ lệch Thấp (Low Deviation): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_LoDevGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho độ lệch Thấp (Low Deviation): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_FailHiLim`** | `REAL` | `103.958336` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng giới hạn trên báo lỗi đứt dây / vượt dải tín hiệu đầu vào (Out-of-Range High, đơn vị thô Raw hoặc EU) |
| **`Cfg_FailLoLim`** | `REAL` | `-2.0833333000000001` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng giới hạn dưới báo lỗi đứt dây / vượt dải tín hiệu đầu vào (Out-of-Range Low, đơn vị thô Raw hoặc EU) |
| **`Cfg_FailDB`** | `REAL` | `0.41666666000000002` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng lỗi tín hiệu / hỏng kênh đo (Signal Failure) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_FailOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho lỗi tín hiệu / hỏng kênh đo (Signal Failure): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_FailOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho lỗi tín hiệu / hỏng kênh đo (Signal Failure): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_FailGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho lỗi tín hiệu / hỏng kênh đo (Signal Failure): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`MSet_SubstPV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Bảo trì HMI] Giá trị đặt từ Nhân viên bảo trì (Maintenance HMI): Giá trị PV thay thế (Substitute PV, đơn vị EU) do nhân viên bảo trì nhập thủ công để ghi đè tín hiệu cảm biến |
| **`Set_SimPV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Giá trị PV giả lập (đơn vị kỹ thuật EU) được sử dụng khi bật chế độ Mô phỏng (Inp_Sim = 1) |
| **`PCmd_ClearCapt`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa giá trị đỉnh Cực tiểu / Cực đại (Min/Max Excursion) đã lưu của PV |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`PCmd_HiHiAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo mức Rất Cao (High-High) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_HiAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo mức Cao (High) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_LoAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo mức Thấp (Low) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_LoLoAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo mức Rất Thấp (Low-Low) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_HiRoCAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo tốc độ biến thiên Cao (High Rate-of-Change) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_HiDevAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo độ lệch Cao (High Deviation) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_LoDevAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo độ lệch Thấp (Low Deviation) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_FailAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_HiHiSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo mức Rất Cao (High-High) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_HiSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo mức Cao (High) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_LoSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo mức Thấp (Low) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_LoLoSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo mức Rất Thấp (Low-Low) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_HiHiUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo mức Rất Cao (High-High) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_HiUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo mức Cao (High) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_LoUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo mức Thấp (Low) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_LoLoUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo mức Rất Thấp (Low-Low) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_HiHiUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo mức Rất Cao (High-High) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_HiUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo mức Cao (High) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_LoUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo mức Thấp (Low) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_LoLoUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo mức Rất Thấp (Low-Low) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_FailUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) ngay lập tức (biến xung tự động xóa về 0) |

## 3. Bảng chi tiết 97 Biến Đầu Ra (`Output Parameters`) của `P_AInAdv`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Val`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu ra chính của biến quá trình PV (đã qua quy đổi đơn vị kỹ thuật EU, lọc nhiễu và chọn giá trị thay thế Substitute PV nếu có) |
| **`Val_InpPV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị PV thực tế tính từ tín hiệu đầu vào (sau khi quy đổi đơn vị EU nhưng trước khi qua bộ chọn Substitute PV) |
| **`Val_RoC`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Tốc độ biến thiên tức thời của giá trị PV (Rate of Change, tính theo đơn vị EU trên một đơn vị thời gian Cfg_RateTime) |
| **`Val_Dev`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Độ lệch tính toán được giữa giá trị PV hiện tại và giá trị tham chiếu (Deviation, đơn vị EU) |
| **`Val_PVMinCapt`** | `REAL` | `1.5000000000000001E+38` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cực tiểu (Minimum Peak Excursion) của PV ghi nhận được kể từ lần xóa (ClearCapt) gần nhất |
| **`Val_PVMaxCapt`** | `REAL` | `-1.5000000000000001E+38` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cực đại (Maximum Peak Excursion) của PV ghi nhận được kể từ lần xóa (ClearCapt) gần nhất |
| **`Val_PVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận dưới của dải đo đơn vị kỹ thuật PV = MIN(Cfg_PVEUMin, Cfg_PVEUMax) |
| **`Val_PVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận trên của dải đo đơn vị kỹ thuật PV = MAX(Cfg_PVEUMin, Cfg_PVEUMax) |
| **`SrcQ_IO`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng dữ liệu tổng hợp cuối cùng (0 = Tín hiệu thực Tốt đã xác nhận, 1 = Tín hiệu thực Tốt giả định, 2 = Không cấu hình phản hồi/Tốt giả định, 8 = Đang mô phỏng, 9 = Mô phỏng vòng lặp, 10 = Giá trị nhập tay, 16 = Ngoài đặc tính kỹ thuật, 17 = Thay thế tại thiết bị/bus, 18 = Thay thế bởi Bảo trì, 19 = Ngắt bảo vệ giữ giá trị cũ, 20 = Ngắt bảo vệ dùng giá trị thay thế, 32 = Lỗi tín hiệu vượt dải/NaN, 33 = Lỗi kênh I/O, 34 = Lỗi module I/O, 35 = Lỗi cấu hình) |
| **`Val_Fault`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái lỗi thiết bị (Device Fault Status — kiểu liệt kê enum: 0 = Không lỗi, 16 = Lỗi khởi động/mở, 17 = Lỗi dừng/đóng, 20 = Lo, 21 = Hi, 24 = LoLo, 25 = HiHi, 32 = Fail/I/O Fault, 34 = Lỗi cấu hình CfgErr) |
| **`Val_NotifyAll`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận tổng hợp của khối này cộng với các đối tượng kênh/quá tải liên kết |
| **`Val_UnackAlmC`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms Count) |
| **`Sts_SubstPV`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang sử dụng giá trị PV thay thế (Substitute PV do nhân viên bảo trì nhập) thay cho tín hiệu cảm biến thật |
| **`Sts_PVBad`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Chất lượng tín hiệu PV Xấu (Bad Quality) hoặc tín hiệu vượt ngoài dải lỗi (Out of Range) |
| **`Sts_PVUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Chất lượng tín hiệu PV Không chắc chắn (Uncertain Quality) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoặc cưỡng bức bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có ít nhất một cảnh báo bị Tạm hoãn (Shelved), Vô hiệu hóa (Disabled) hoặc Ức chế (Suppressed) — hiển thị biểu tượng chữ "I" trên HMI |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_Raw`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải giá trị thô đầu vào (Cfg_InpRawMin = Cfg_InpRawMax) bị trùng nhau |
| **`Err_EU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải quy đổi đơn vị kỹ thuật (Engineering Units) có giá trị Tối thiểu bằng Tối đa (Min = Max) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị đặt bộ định thời gian (Timer Preset) nằm ngoài phạm vi hợp lệ 0..2.147.483 giây |
| **`Err_Rate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Cơ sở thời gian tính tốc độ biến thiên (Cfg_RateTime) không hợp lệ (dùng 1.0=giây, 60.0=phút, 3600.0=giờ, 86400.0=ngày) |
| **`Err_Filt`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thông số bộ lọc tín hiệu PV (Cfg_FiltTC hoặc Cfg_RateTime) không hợp lệ |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình cảnh báo: Thời gian duy trì tối thiểu (Min On Time), thời gian tạm hoãn (Shelf Time) hoặc mức độ nghiêm trọng (Severity 1..1000) không hợp lệ |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc chốt ngắt bảo vệ (Shed) đã hết lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_HiHiCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của mức Rất Cao (High-High) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_HiHiGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của mức Rất Cao (High-High) đã đếm xong, đang cho phép giám sát |
| **`Sts_HiHi`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện mức Rất Cao (High-High) |
| **`Alm_HiHi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho mức Rất Cao (High-High) |
| **`Ack_HiHi`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động mức Rất Cao (High-High) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_HiHiDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Rất Cao (High-High) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_HiHiShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Rất Cao (High-High) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_HiHiSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Rất Cao (High-High) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_HiCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của mức Cao (High) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_HiGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của mức Cao (High) đã đếm xong, đang cho phép giám sát |
| **`Sts_Hi`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện mức Cao (High) |
| **`Alm_Hi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho mức Cao (High) |
| **`Ack_Hi`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động mức Cao (High) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_HiDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Cao (High) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_HiShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Cao (High) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_HiSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Cao (High) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_LoCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của mức Thấp (Low) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_LoGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của mức Thấp (Low) đã đếm xong, đang cho phép giám sát |
| **`Sts_Lo`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện mức Thấp (Low) |
| **`Alm_Lo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho mức Thấp (Low) |
| **`Ack_Lo`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động mức Thấp (Low) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_LoDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Thấp (Low) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_LoShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Thấp (Low) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_LoSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Thấp (Low) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_LoLoCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của mức Rất Thấp (Low-Low) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_LoLoGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của mức Rất Thấp (Low-Low) đã đếm xong, đang cho phép giám sát |
| **`Sts_LoLo`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện mức Rất Thấp (Low-Low) |
| **`Alm_LoLo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho mức Rất Thấp (Low-Low) |
| **`Ack_LoLo`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động mức Rất Thấp (Low-Low) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_LoLoDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Rất Thấp (Low-Low) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_LoLoShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Rất Thấp (Low-Low) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_LoLoSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Rất Thấp (Low-Low) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_HiRoCCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của tốc độ biến thiên Cao (High Rate-of-Change) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_HiRoCGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của tốc độ biến thiên Cao (High Rate-of-Change) đã đếm xong, đang cho phép giám sát |
| **`Sts_HiRoC`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện tốc độ biến thiên Cao (High Rate-of-Change) |
| **`Alm_HiRoC`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho tốc độ biến thiên Cao (High Rate-of-Change) |
| **`Ack_HiRoC`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động tốc độ biến thiên Cao (High Rate-of-Change) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_HiRoCDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo tốc độ biến thiên Cao (High Rate-of-Change) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_HiRoCShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo tốc độ biến thiên Cao (High Rate-of-Change) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_HiRoCSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo tốc độ biến thiên Cao (High Rate-of-Change) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_HiDevCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của độ lệch Cao (High Deviation) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_HiDevGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của độ lệch Cao (High Deviation) đã đếm xong, đang cho phép giám sát |
| **`Sts_HiDev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện độ lệch Cao (High Deviation) |
| **`Alm_HiDev`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho độ lệch Cao (High Deviation) |
| **`Ack_HiDev`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động độ lệch Cao (High Deviation) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_HiDevDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo độ lệch Cao (High Deviation) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_HiDevShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo độ lệch Cao (High Deviation) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_HiDevSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo độ lệch Cao (High Deviation) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_LoDevCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của độ lệch Thấp (Low Deviation) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_LoDevGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của độ lệch Thấp (Low Deviation) đã đếm xong, đang cho phép giám sát |
| **`Sts_LoDev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện độ lệch Thấp (Low Deviation) |
| **`Alm_LoDev`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho độ lệch Thấp (Low Deviation) |
| **`Ack_LoDev`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động độ lệch Thấp (Low Deviation) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_LoDevDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo độ lệch Thấp (Low Deviation) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_LoDevShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo độ lệch Thấp (Low Deviation) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_LoDevSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo độ lệch Thấp (Low Deviation) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_FailCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của lỗi tín hiệu / hỏng kênh đo (Signal Failure) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_FailGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của lỗi tín hiệu / hỏng kênh đo (Signal Failure) đã đếm xong, đang cho phép giám sát |
| **`Sts_Fail`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi tín hiệu / hỏng kênh đo (Signal Failure) |
| **`Alm_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi tín hiệu / hỏng kênh đo (Signal Failure) |
| **`Ack_Fail`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi tín hiệu / hỏng kênh đo (Signal Failure) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_FailDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_FailShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_FailSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_CtrlHi`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị đầu vào tương tự vượt trên ngưỡng điều khiển Cao (Cfg_CtrlHiLim) |
| **`Sts_CtrlHiHi`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị đầu vào tương tự vượt trên ngưỡng điều khiển Rất Cao (Cfg_CtrlHiHiLim) |
| **`Sts_CtrlLo`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị đầu vào tương tự xuống dưới ngưỡng điều khiển Thấp (Cfg_CtrlLoLim) |
| **`Sts_CtrlLoLo`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị đầu vào tương tự xuống dưới ngưỡng điều khiển Rất Thấp (Cfg_CtrlLoLoLim) |
| **`P_AInAdv`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_AInAdv) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 34 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_AInAdv`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_8` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'Advanced Analog Input'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật (Engineering Units, ví dụ: "°C", "bar", "m3/h", "%") hiển thị trên màn hình HMI |
| **`Cfg_Label`** | `STRING_20` | `'Analog Input'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_Tag`** | `STRING_20` | `'P_AinAdv'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`Fail`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi tín hiệu / hỏng kênh đo (Signal Failure) |
| **`FailGate`** | `P_Gate` | `{...}` | [Khối con P_Gate nội bộ] Bộ định thời trễ mở cổng (Gate Delay), trễ bật (On-Delay) và trễ tắt (Off-Delay) cho lỗi tín hiệu / hỏng kênh đo (Signal Failure) |
| **`Hi`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho mức Cao (High) |
| **`HiDev`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho độ lệch Cao (High Deviation) |
| **`HiDevGate`** | `P_Gate` | `{...}` | [Khối con P_Gate nội bộ] Bộ định thời trễ mở cổng (Gate Delay), trễ bật (On-Delay) và trễ tắt (Off-Delay) cho độ lệch Cao (High Deviation) |
| **`HiGate`** | `P_Gate` | `{...}` | [Khối con P_Gate nội bộ] Bộ định thời trễ mở cổng (Gate Delay), trễ bật (On-Delay) và trễ tắt (Off-Delay) cho mức Cao (High) |
| **`HiHi`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho mức Rất Cao (High-High) |
| **`HiHiGate`** | `P_Gate` | `{...}` | [Khối con P_Gate nội bộ] Bộ định thời trễ mở cổng (Gate Delay), trễ bật (On-Delay) và trễ tắt (Off-Delay) cho mức Rất Cao (High-High) |
| **`HiRoC`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho tốc độ biến thiên Cao (High Rate-of-Change) |
| **`HiRoCGate`** | `P_Gate` | `{...}` | [Khối con P_Gate nội bộ] Bộ định thời trễ mở cổng (Gate Delay), trễ bật (On-Delay) và trễ tắt (Off-Delay) cho tốc độ biến thiên Cao (High Rate-of-Change) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_AInAdv'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`Lo`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho mức Thấp (Low) |
| **`LoDev`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho độ lệch Thấp (Low Deviation) |
| **`LoDevGate`** | `P_Gate` | `{...}` | [Khối con P_Gate nội bộ] Bộ định thời trễ mở cổng (Gate Delay), trễ bật (On-Delay) và trễ tắt (Off-Delay) cho độ lệch Thấp (Low Deviation) |
| **`LoGate`** | `P_Gate` | `{...}` | [Khối con P_Gate nội bộ] Bộ định thời trễ mở cổng (Gate Delay), trễ bật (On-Delay) và trễ tắt (Off-Delay) cho mức Thấp (Low) |
| **`LoLo`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho mức Rất Thấp (Low-Low) |
| **`LoLoGate`** | `P_Gate` | `{...}` | [Khối con P_Gate nội bộ] Bộ định thời trễ mở cổng (Gate Delay), trễ bật (On-Delay) và trễ tắt (Off-Delay) cho mức Rất Thấp (Low-Low) |
| **`MCmd_InpPV`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Sử dụng lại tín hiệu PV thực tế từ cảm biến đầu vào (ngưng dùng giá trị thay thế Substitute PV) |
| **`MCmd_SubstPV`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Chuyển sang sử dụng giá trị PV thay thế (Substitute PV) do nhân viên bảo trì nhập |
| **`MRdy_InpPV`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_InpPV (Sử dụng lại tín hiệu PV thực tế từ cảm biến đầu vào (ngưng dùng giá trị thay thế Substitute PV)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_SubstPV`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_SubstPV (Chuyển sang sử dụng giá trị PV thay thế (Substitute PV) do nhân viên bảo trì nhập), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`OCmd_ClearCapt`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa giá trị đỉnh Cực tiểu / Cực đại (Min/Max Excursion) đã lưu của PV |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`Val_Notify`** | `SINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo hiện hành và trạng thái xác nhận của khối này (0 = Không có cảnh báo, 1 = Đã hết lỗi nhưng chờ Reset/Ack, 2..3 = Thấp, 4..5 = Trung bình, 6..7 = Cao, 8..9 = Khẩn cấp) |
---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_AInDual`
**Thuộc chương:** [Chương 1: Xử lý tín hiệu Vào/Ra (I/O Processing)](../01_CHUONG_1_IO_PROCESSING.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `IO Processing 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 35–38)  
**Tệp định nghĩa khối lệnh:** `P_AInDual_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_AInDual`:** **139** Biến đầu vào (`Input Parameters`) | **100** Biến đầu ra (`Output Parameters`) | **0** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_AInDual` trong Studio 5000
- **Chức năng cốt lõi:** Khối xử lý **cặp cảm biến kép (Dual Sensor)** cùng đo 1 đại lượng quá trình (`Inp_PVA` và `Inp_PVB`). Quy đổi và lọc riêng từng kênh, cho phép chọn đầu ra là Kênh A, Kênh B, Trung bình (`Average`), Nhỏ nhất (`Min`) hoặc Lớn nhất (`Max`), tự động loại kênh hỏng và báo động khi 2 cảm biến lệch nhau quá ngưỡng `Cfg_DiffLim`.
- **Khi nào CẦN sử dụng:** Khi thiết kế điểm đo quan trọng có **2 cảm biến dự phòng nóng (1oo2 / Redundant Sensors)** để nếu 1 cảm biến đứt dây thì vòng điều khiển PID tự động chuyển sang dùng cảm biến còn lại mà không bị gián đoạn, đồng thời phát hiện sớm cảm biến bị trôi (Drift).
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi chỉ có 1 cảm biến ([`P_AIn.md`](./P_AIn.md)) hoặc có từ 3 đến 8 cảm biến ([`P_AInMulti.md`](./P_AInMulti.md)).

### Quy trình 4 bước đấu nối và cấu hình `P_AInDual`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_PVA`, `Inp_PVB`, `Inp_PVASrcQ`, `Inp_PVANotify`, `Inp_PVBSrcQ`, `Inp_PVBNotify`, `Inp_PVABad`, `Inp_PVBBad`, `Inp_PVAUncertain`, `Inp_PVBUncertain`, `Inp_Sim`, `Inp_HiHiGate`, `Inp_HiGate`, `Inp_LoGate`, `Inp_LoLoGate`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_NoSubstPV`, `Cfg_SetTrack`, `Cfg_HasChanObjA`, `Cfg_HasChanObjB`, `Cfg_UseChanSrcQA`, `Cfg_UseChanSrcQB`, `Cfg_PVDecPlcs`, `Cfg_HasHiHiAlm`, `Cfg_HasHiAlm`, `Cfg_HasLoAlm`, `Cfg_HasLoLoAlm`, `Cfg_HasOneGoodAlm`, `Cfg_HasNoneGoodAlm`, `Cfg_HasDiffAlm`, `Cfg_HasFailAlm`, `Cfg_HasMoreObj`, `Cfg_HiHiResetReqd`, `Cfg_HiResetReqd`...
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** Không có biến `Cfg_*` cục bộ
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_AInDual` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_AInDual` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PCmd_SelA`, `PCmd_SelB`, `PCmd_SelAvg`, `PCmd_SelMin`, `PCmd_SelMax`, `PCmd_ClearCapt`, `PCmd_Reset`, `PCmd_HiHiAck`, `PCmd_HiAck`, `PCmd_LoAck`, `PCmd_LoLoAck`, `PCmd_OneGoodAck`, `PCmd_NoneGoodAck`, `PCmd_DiffAck`, `PCmd_FailAck`, `PCmd_OneGoodSuppress`, `PCmd_NoneGoodSuppress`, `PCmd_DiffSuppress`...
   - **Cách sử dụng trong code PLC:** Gán giá trị đặt vào `PSet_*` (nếu có) hoặc kích xung 1 chu kỳ vào các bit lệnh `PCmd_* = 1` để xóa lỗi (`PCmd_Reset`), xác nhận cảnh báo (`PCmd_*Ack`) hoặc ức chế cảnh báo (`PCmd_*Suppress`). Khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi.
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Val`, `Val_PVA`, `Val_PVB`, `Val_AvgPV`, `Val_MinPV`, `Val_MaxPV`, `Val_InpPV`, `Val_Diff`, `Val_PVMinCapt`, `Val_PVMaxCapt`, `Val_PVEUMin`, `Val_PVEUMax`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Sts_PVASel`, `Sts_PVBSel`, `Sts_AvgSel`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 139 Biến Đầu Vào (`Input Parameters`) của `P_AInDual`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp_PVA`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu biến quá trình đầu vào từ cảm biến kênh A (Input PV A) |
| **`Inp_PVB`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu biến quá trình đầu vào từ cảm biến kênh B (Input PV B) |
| **`Inp_PVASrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã nguồn gốc và chất lượng tín hiệu (enum) từ khối kênh P_AIChan của cảm biến A |
| **`Inp_PVANotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã mức độ ưu tiên cảnh báo và xác nhận (enum) của đối tượng kênh cảm biến A |
| **`Inp_PVBSrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã nguồn gốc và chất lượng tín hiệu (enum) từ khối kênh P_AIChan của cảm biến B |
| **`Inp_PVBNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã mức độ ưu tiên cảnh báo và xác nhận (enum) của đối tượng kênh cảm biến B |
| **`Inp_PVABad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi chất lượng tín hiệu hoặc mất truyền thông của kênh cảm biến A (1 = Lỗi/Bad, 0 = Tốt/OK) |
| **`Inp_PVBBad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi chất lượng tín hiệu hoặc mất truyền thông của kênh cảm biến B (1 = Lỗi/Bad, 0 = Tốt/OK) |
| **`Inp_PVAUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái chất lượng tín hiệu kênh cảm biến A không chắc chắn (1 = Uncertain, 0 = OK) |
| **`Inp_PVBUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái chất lượng tín hiệu kênh cảm biến B không chắc chắn (1 = Uncertain, 0 = OK) |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân kích hoạt chế độ Mô phỏng (Simulation): Khi = 1, khóa các ngõ ra vật lý ở mức an toàn và dùng giá trị/phản hồi giả lập nội bộ; khi = 0, vận hành thực tế bình thường |
| **`Inp_HiHiGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát mức Rất Cao (High-High): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_HiGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát mức Cao (High): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_LoGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát mức Thấp (Low): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_LoLoGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát mức Rất Thấp (Low-Low): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_DiffGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát độ chênh lệch giữa 2 cảm biến vượt ngưỡng (Sensor Difference): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_FailGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát lỗi tín hiệu / hỏng kênh đo (Signal Failure): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Cfg_NoSubstPV`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Khóa chức năng nhập giá trị thay thế (Substitute PV): 0 = Cho phép nhân viên bảo trì nhập giá trị PV thay thế; 1 = Cấm sử dụng tính năng Substitute PV |
| **`Cfg_SetTrack`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép các giá trị đặt (PSet_CV / OSet_CV / SP) tự động bám theo ngõ ra thực tế hiện hành để chuyển mạch không giật (Bumpless Transfer) giữa Operator và Program |
| **`Cfg_HasChanObjA`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết kênh cảm biến A có sử dụng khối chẩn đoán kênh (P_AIChan) và bật nút điều hướng tương ứng |
| **`Cfg_HasChanObjB`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết kênh cảm biến B có sử dụng khối chẩn đoán kênh (P_AIChan) và bật nút điều hướng tương ứng |
| **`Cfg_UseChanSrcQA`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Nhận trực tiếp mã chất lượng SrcQ từ khối kênh A (P_AIChanA); 0 = Tự tính toán mã SrcQ kênh A |
| **`Cfg_UseChanSrcQB`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Nhận trực tiếp mã chất lượng SrcQ từ khối kênh B (P_AIChanB); 0 = Tự tính toán mã SrcQ kênh B |
| **`Cfg_PVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho giá trị PV trên màn hình HMI (từ 0 đến 6 chữ số) |
| **`Cfg_HasHiHiAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo mức Rất Cao (High-High): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasHiAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo mức Cao (High): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasLoAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo mức Thấp (Low): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasLoLoAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo mức Rất Thấp (Low-Low): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasOneGoodAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo chỉ còn 1 cảm biến hoạt động tốt (Only One Good Sensor): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasNoneGoodAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo không còn cảm biến nào hoạt động tốt (No Good Sensor): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasDiffAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo độ chênh lệch giữa 2 cảm biến vượt ngưỡng (Sensor Difference): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasFailAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_HiHiResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo mức Rất Cao (High-High): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_HiResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo mức Cao (High): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_LoResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo mức Thấp (Low): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_LoLoResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo mức Rất Thấp (Low-Low): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_OneGoodResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo chỉ còn 1 cảm biến hoạt động tốt (Only One Good Sensor): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_NoneGoodResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo không còn cảm biến nào hoạt động tốt (No Good Sensor): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_DiffResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo độ chênh lệch giữa 2 cảm biến vượt ngưỡng (Sensor Difference): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_FailResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_HiHiAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo mức Rất Cao (High-High): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_HiAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo mức Cao (High): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_LoAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo mức Thấp (Low): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_LoLoAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo mức Rất Thấp (Low-Low): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_OneGoodAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo chỉ còn 1 cảm biến hoạt động tốt (Only One Good Sensor): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_NoneGoodAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo không còn cảm biến nào hoạt động tốt (No Good Sensor): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_DiffAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo độ chênh lệch giữa 2 cảm biến vượt ngưỡng (Sensor Difference): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_FailAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_HiHiSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo mức Rất Cao (High-High) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_HiSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo mức Cao (High) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_LoSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo mức Thấp (Low) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_LoLoSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo mức Rất Thấp (Low-Low) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_OneGoodSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo chỉ còn 1 cảm biến hoạt động tốt (Only One Good Sensor) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_NoneGoodSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo không còn cảm biến nào hoạt động tốt (No Good Sensor) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_DiffSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo độ chênh lệch giữa 2 cảm biến vượt ngưỡng (Sensor Difference) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_FailSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_InpRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đầu vào thô tối thiểu (Raw Min, ví dụ 4.0 mA hoặc 0) dùng để quy đổi tỷ lệ tuyến tính sang đơn vị kỹ thuật |
| **`Cfg_InpRawMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đầu vào thô tối đa (Raw Max, ví dụ 20.0 mA hoặc 100) dùng để quy đổi tỷ lệ tuyến tính sang đơn vị kỹ thuật |
| **`Cfg_PVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tương ứng với Raw Min (PV EU Min — hỗ trợ cả quy đổi ngược nếu đặt EUMin > EUMax) |
| **`Cfg_PVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tương ứng với Raw Max (PV EU Max — hỗ trợ cả quy đổi ngược nếu đặt EUMax < EUMin) |
| **`Cfg_FiltTC`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Hằng số thời gian của bộ lọc thông thấp bậc 1 cho tín hiệu PV (tính bằng giây; đặt = 0.0 nếu không lọc) |
| **`Cfg_HiHiLim`** | `REAL` | `1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Ngưỡng giới hạn tác động (Threshold Limit) cho mức Rất Cao (High-High) (tính theo đơn vị kỹ thuật EU) |
| **`Cfg_HiHiDB`** | `REAL` | `1.0` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng mức Rất Cao (High-High) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_HiDB`** | `REAL` | `1.0` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng mức Cao (High) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_LoDB`** | `REAL` | `1.0` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng mức Thấp (Low) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_LoLoDB`** | `REAL` | `1.0` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng mức Rất Thấp (Low-Low) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_FailDB`** | `REAL` | `0.0` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng lỗi tín hiệu / hỏng kênh đo (Signal Failure) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_DiffDB`** | `REAL` | `1.0` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng độ chênh lệch giữa 2 cảm biến vượt ngưỡng (Sensor Difference) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_HiHiGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho mức Rất Cao (High-High): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_HiGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho mức Cao (High): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_LoGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho mức Thấp (Low): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_LoLoGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho mức Rất Thấp (Low-Low): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_DiffGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho độ chênh lệch giữa 2 cảm biến vượt ngưỡng (Sensor Difference): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_FailGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho lỗi tín hiệu / hỏng kênh đo (Signal Failure): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_HiHiOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho mức Rất Cao (High-High): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_HiOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho mức Cao (High): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_DiffOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho độ chênh lệch giữa 2 cảm biến vượt ngưỡng (Sensor Difference): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_LoOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho mức Thấp (Low): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_LoLoOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho mức Rất Thấp (Low-Low): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_FailOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho lỗi tín hiệu / hỏng kênh đo (Signal Failure): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_HiHiOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho mức Rất Cao (High-High): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_LoOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho mức Thấp (Low): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_LoLoOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho mức Rất Thấp (Low-Low): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_FailOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho lỗi tín hiệu / hỏng kênh đo (Signal Failure): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_HiOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho mức Cao (High): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_DiffOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho độ chênh lệch giữa 2 cảm biến vượt ngưỡng (Sensor Difference): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_HiLim`** | `REAL` | `1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Ngưỡng giới hạn tác động (Threshold Limit) cho mức Cao (High) (tính theo đơn vị kỹ thuật EU) |
| **`Cfg_LoLim`** | `REAL` | `-1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Ngưỡng giới hạn tác động (Threshold Limit) cho mức Thấp (Low) (tính theo đơn vị kỹ thuật EU) |
| **`Cfg_LoLoLim`** | `REAL` | `-1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Ngưỡng giới hạn tác động (Threshold Limit) cho mức Rất Thấp (Low-Low) (tính theo đơn vị kỹ thuật EU) |
| **`Cfg_DiffLim`** | `REAL` | `1.5000000000000001E+38` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng giới hạn tác động (Threshold Limit) cho độ chênh lệch giữa 2 cảm biến vượt ngưỡng (Sensor Difference) (tính theo đơn vị kỹ thuật EU) |
| **`Cfg_FailHiLim`** | `REAL` | `103.958336` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng giới hạn trên báo lỗi đứt dây / vượt dải tín hiệu đầu vào (Out-of-Range High, đơn vị thô Raw hoặc EU) |
| **`Cfg_FailLoLim`** | `REAL` | `-2.0833333000000001` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng giới hạn dưới báo lỗi đứt dây / vượt dải tín hiệu đầu vào (Out-of-Range Low, đơn vị thô Raw hoặc EU) |
| **`Cfg_OperKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ bit cấu hình cho phép Operator giữ quyền chỉnh thông số ngay cả khi khối ở chế độ Program (.0 = Giữ quyền chọn kênh PV, .1 = Giữ quyền chỉnh ngưỡng cảnh báo) |
| **`Cfg_ProgKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ bit cấu hình cho phép Program giữ quyền chỉnh thông số ngay cả khi khối ở chế độ Operator (.0 = Giữ quyền chọn kênh PV, .1 = Giữ quyền chỉnh ngưỡng cảnh báo) |
| **`MSet_SubstPV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Bảo trì HMI] Giá trị đặt từ Nhân viên bảo trì (Maintenance HMI): Giá trị PV thay thế (Substitute PV, đơn vị EU) do nhân viên bảo trì nhập thủ công để ghi đè tín hiệu cảm biến |
| **`Set_SimPVA`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Giá trị PV giả lập cho kênh cảm biến A (đơn vị kỹ thuật EU) dùng khi bật chế độ Mô phỏng (Inp_Sim = 1) |
| **`Set_SimPVB`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Giá trị PV giả lập cho kênh cảm biến B (đơn vị kỹ thuật EU) dùng khi bật chế độ Mô phỏng (Inp_Sim = 1) |
| **`PCmd_SelA`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chọn cảm biến kênh A làm giá trị PV đầu ra |
| **`PCmd_SelB`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chọn cảm biến kênh B làm giá trị PV đầu ra |
| **`PCmd_SelAvg`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chọn giá trị Trung bình cộng của kênh A và B làm giá trị PV đầu ra |
| **`PCmd_SelMin`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chọn giá trị Nhỏ nhất (Min) giữa kênh A và B làm giá trị PV đầu ra |
| **`PCmd_SelMax`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chọn giá trị Lớn nhất (Max) giữa kênh A và B làm giá trị PV đầu ra |
| **`PCmd_ClearCapt`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa giá trị đỉnh Cực tiểu / Cực đại (Min/Max Excursion) đã lưu của PV |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`PCmd_HiHiAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo mức Rất Cao (High-High) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_HiAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo mức Cao (High) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_LoAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo mức Thấp (Low) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_LoLoAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo mức Rất Thấp (Low-Low) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_OneGoodAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo chỉ còn 1 cảm biến hoạt động tốt (Only One Good Sensor) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_NoneGoodAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo không còn cảm biến nào hoạt động tốt (No Good Sensor) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_DiffAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo độ chênh lệch giữa 2 cảm biến vượt ngưỡng (Sensor Difference) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_FailAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_OneGoodSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo chỉ còn 1 cảm biến hoạt động tốt (Only One Good Sensor) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_NoneGoodSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo không còn cảm biến nào hoạt động tốt (No Good Sensor) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_DiffSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo độ chênh lệch giữa 2 cảm biến vượt ngưỡng (Sensor Difference) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_HiHiSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo mức Rất Cao (High-High) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_HiSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo mức Cao (High) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_LoSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo mức Thấp (Low) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_LoLoSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo mức Rất Thấp (Low-Low) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_HiHiUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo mức Rất Cao (High-High) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_HiUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo mức Cao (High) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_LoUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo mức Thấp (Low) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_LoLoUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo mức Rất Thấp (Low-Low) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_OneGoodUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo chỉ còn 1 cảm biến hoạt động tốt (Only One Good Sensor) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_NoneGoodUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo không còn cảm biến nào hoạt động tốt (No Good Sensor) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_DiffUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo độ chênh lệch giữa 2 cảm biến vượt ngưỡng (Sensor Difference) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_HiHiUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo mức Rất Cao (High-High) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_HiUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo mức Cao (High) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_LoUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo mức Thấp (Low) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_LoLoUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo mức Rất Thấp (Low-Low) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_OneGoodUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo chỉ còn 1 cảm biến hoạt động tốt (Only One Good Sensor) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_NoneGoodUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo không còn cảm biến nào hoạt động tốt (No Good Sensor) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_DiffUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo độ chênh lệch giữa 2 cảm biến vượt ngưỡng (Sensor Difference) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_FailUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) ngay lập tức (biến xung tự động xóa về 0) |

## 3. Bảng chi tiết 100 Biến Đầu Ra (`Output Parameters`) của `P_AInDual`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Val`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu ra chính của biến quá trình PV (đã qua quy đổi đơn vị kỹ thuật EU, lọc nhiễu và chọn giá trị thay thế Substitute PV nếu có) |
| **`Val_PVA`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị thực tế đã quy đổi đơn vị kỹ thuật (EU) của kênh cảm biến A |
| **`Val_PVB`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị thực tế đã quy đổi đơn vị kỹ thuật (EU) của kênh cảm biến B |
| **`Val_AvgPV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị trung bình cộng của hai kênh cảm biến A và B (đơn vị kỹ thuật EU) |
| **`Val_MinPV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị nhỏ nhất (Minimum) giữa hai kênh cảm biến A và B (đơn vị kỹ thuật EU) |
| **`Val_MaxPV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị lớn nhất (Maximum) giữa hai kênh cảm biến A và B (đơn vị kỹ thuật EU) |
| **`Val_InpPV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị PV thực tế tính từ tín hiệu đầu vào (sau khi quy đổi đơn vị EU nhưng trước khi qua bộ chọn Substitute PV) |
| **`Val_Diff`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Độ chênh lệch (PVA - PVB) giữa hai kênh cảm biến A và B (đơn vị kỹ thuật EU) |
| **`Val_PVMinCapt`** | `REAL` | `1.5000000000000001E+38` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cực tiểu (Minimum Peak Excursion) của PV ghi nhận được kể từ lần xóa (ClearCapt) gần nhất |
| **`Val_PVMaxCapt`** | `REAL` | `-1.5000000000000001E+38` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cực đại (Maximum Peak Excursion) của PV ghi nhận được kể từ lần xóa (ClearCapt) gần nhất |
| **`Val_PVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận dưới của dải đo đơn vị kỹ thuật PV = MIN(Cfg_PVEUMin, Cfg_PVEUMax) |
| **`Val_PVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận trên của dải đo đơn vị kỹ thuật PV = MAX(Cfg_PVEUMin, Cfg_PVEUMax) |
| **`SrcQ_IOA`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O cảm biến A (kiểu liệt kê enum) |
| **`SrcQ_IOB`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O cảm biến B (kiểu liệt kê enum) |
| **`SrcQ_IO`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng dữ liệu tổng hợp cuối cùng (0 = Tín hiệu thực Tốt đã xác nhận, 1 = Tín hiệu thực Tốt giả định, 2 = Không cấu hình phản hồi/Tốt giả định, 8 = Đang mô phỏng, 9 = Mô phỏng vòng lặp, 10 = Giá trị nhập tay, 16 = Ngoài đặc tính kỹ thuật, 17 = Thay thế tại thiết bị/bus, 18 = Thay thế bởi Bảo trì, 19 = Ngắt bảo vệ giữ giá trị cũ, 20 = Ngắt bảo vệ dùng giá trị thay thế, 32 = Lỗi tín hiệu vượt dải/NaN, 33 = Lỗi kênh I/O, 34 = Lỗi module I/O, 35 = Lỗi cấu hình) |
| **`Val_Fault`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái lỗi thiết bị (Device Fault Status — kiểu liệt kê enum: 0 = Không lỗi, 16 = Lỗi khởi động/mở, 17 = Lỗi dừng/đóng, 20 = Lo, 21 = Hi, 24 = LoLo, 25 = HiHi, 32 = Fail/I/O Fault, 34 = Lỗi cấu hình CfgErr) |
| **`Val_NotifyAll`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận tổng hợp của khối này cộng với các đối tượng kênh/quá tải liên kết |
| **`Val_UnackAlmC`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms Count) |
| **`Sts_PVASel`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang chọn kênh cảm biến A làm giá trị PV đầu ra |
| **`Sts_PVBSel`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang chọn kênh cảm biến B làm giá trị PV đầu ra |
| **`Sts_AvgSel`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang chọn giá trị Trung bình cộng của A và B làm giá trị PV đầu ra |
| **`Sts_MinSel`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang chọn giá trị Nhỏ nhất (Min) giữa A và B làm giá trị PV đầu ra |
| **`Sts_MaxSel`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang chọn giá trị Lớn nhất (Max) giữa A và B làm giá trị PV đầu ra |
| **`Sts_SubstPV`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang sử dụng giá trị PV thay thế (Substitute PV do nhân viên bảo trì nhập) thay cho tín hiệu cảm biến thật |
| **`Sts_PVBad`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Chất lượng tín hiệu PV Xấu (Bad Quality) hoặc tín hiệu vượt ngoài dải lỗi (Out of Range) |
| **`Sts_PVUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Chất lượng tín hiệu PV Không chắc chắn (Uncertain Quality) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoặc cưỡng bức bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có ít nhất một cảnh báo bị Tạm hoãn (Shelved), Vô hiệu hóa (Disabled) hoặc Ức chế (Suppressed) — hiển thị biểu tượng chữ "I" trên HMI |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_Raw`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải giá trị thô đầu vào (Cfg_InpRawMin = Cfg_InpRawMax) bị trùng nhau |
| **`Err_EU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải quy đổi đơn vị kỹ thuật (Engineering Units) có giá trị Tối thiểu bằng Tối đa (Min = Max) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị đặt bộ định thời gian (Timer Preset) nằm ngoài phạm vi hợp lệ 0..2.147.483 giây |
| **`Err_Filt`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thông số bộ lọc tín hiệu PV (Cfg_FiltTC hoặc Cfg_RateTime) không hợp lệ |
| **`Err_DB`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Độ rộng vùng chết (Deadband) của một ngưỡng cảnh báo bị đặt số âm (< 0.0) |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình cảnh báo: Thời gian duy trì tối thiểu (Min On Time), thời gian tạm hoãn (Shelf Time) hoặc mức độ nghiêm trọng (Severity 1..1000) không hợp lệ |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc chốt ngắt bảo vệ (Shed) đã hết lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_HiHiCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của mức Rất Cao (High-High) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_HiHiGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của mức Rất Cao (High-High) đã đếm xong, đang cho phép giám sát |
| **`Sts_HiHi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện mức Rất Cao (High-High) |
| **`Alm_HiHi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho mức Rất Cao (High-High) |
| **`Ack_HiHi`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động mức Rất Cao (High-High) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_HiHiDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Rất Cao (High-High) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_HiHiSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Rất Cao (High-High) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_HiHiShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Rất Cao (High-High) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_HiCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của mức Cao (High) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_HiGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của mức Cao (High) đã đếm xong, đang cho phép giám sát |
| **`Sts_Hi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện mức Cao (High) |
| **`Alm_Hi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho mức Cao (High) |
| **`Ack_Hi`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động mức Cao (High) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_HiDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Cao (High) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_HiSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Cao (High) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_HiShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Cao (High) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_LoCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của mức Thấp (Low) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_LoGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của mức Thấp (Low) đã đếm xong, đang cho phép giám sát |
| **`Sts_Lo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện mức Thấp (Low) |
| **`Alm_Lo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho mức Thấp (Low) |
| **`Ack_Lo`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động mức Thấp (Low) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_LoDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Thấp (Low) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_LoSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Thấp (Low) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_LoShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Thấp (Low) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_LoLoCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của mức Rất Thấp (Low-Low) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_LoLoGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của mức Rất Thấp (Low-Low) đã đếm xong, đang cho phép giám sát |
| **`Sts_LoLo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện mức Rất Thấp (Low-Low) |
| **`Alm_LoLo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho mức Rất Thấp (Low-Low) |
| **`Ack_LoLo`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động mức Rất Thấp (Low-Low) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_LoLoDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Rất Thấp (Low-Low) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_LoLoSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Rất Thấp (Low-Low) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_LoLoShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Rất Thấp (Low-Low) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_OneGood`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Chỉ còn duy nhất 1 cảm biến có chất lượng tốt (cảm biến kia bị lỗi Bad hoặc Uncertain) |
| **`Alm_OneGood`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho chỉ còn 1 cảm biến hoạt động tốt (Only One Good Sensor) |
| **`Ack_OneGood`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động chỉ còn 1 cảm biến hoạt động tốt (Only One Good Sensor) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_OneGoodDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo chỉ còn 1 cảm biến hoạt động tốt (Only One Good Sensor) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_OneGoodSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo chỉ còn 1 cảm biến hoạt động tốt (Only One Good Sensor) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_OneGoodShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo chỉ còn 1 cảm biến hoạt động tốt (Only One Good Sensor) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_NoneGood`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Không còn cảm biến nào đạt chất lượng tốt (cả hai đều lỗi Bad hoặc một Bad / một Uncertain) |
| **`Alm_NoneGood`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho không còn cảm biến nào hoạt động tốt (No Good Sensor) |
| **`Ack_NoneGood`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động không còn cảm biến nào hoạt động tốt (No Good Sensor) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_NoneGoodDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo không còn cảm biến nào hoạt động tốt (No Good Sensor) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_NoneGoodSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo không còn cảm biến nào hoạt động tốt (No Good Sensor) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_NoneGoodShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo không còn cảm biến nào hoạt động tốt (No Good Sensor) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_DiffCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của độ chênh lệch giữa 2 cảm biến vượt ngưỡng (Sensor Difference) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_DiffGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của độ chênh lệch giữa 2 cảm biến vượt ngưỡng (Sensor Difference) đã đếm xong, đang cho phép giám sát |
| **`Sts_Diff`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện độ chênh lệch giữa 2 cảm biến vượt ngưỡng (Sensor Difference) |
| **`Alm_Diff`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho độ chênh lệch giữa 2 cảm biến vượt ngưỡng (Sensor Difference) |
| **`Ack_Diff`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động độ chênh lệch giữa 2 cảm biến vượt ngưỡng (Sensor Difference) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_DiffDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo độ chênh lệch giữa 2 cảm biến vượt ngưỡng (Sensor Difference) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_DiffSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo độ chênh lệch giữa 2 cảm biến vượt ngưỡng (Sensor Difference) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_DiffShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo độ chênh lệch giữa 2 cảm biến vượt ngưỡng (Sensor Difference) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_FailCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của lỗi tín hiệu / hỏng kênh đo (Signal Failure) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_FailGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của lỗi tín hiệu / hỏng kênh đo (Signal Failure) đã đếm xong, đang cho phép giám sát |
| **`Sts_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi tín hiệu / hỏng kênh đo (Signal Failure) |
| **`Alm_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi tín hiệu / hỏng kênh đo (Signal Failure) |
| **`Ack_Fail`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi tín hiệu / hỏng kênh đo (Signal Failure) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_FailDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_FailSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_FailShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`P_AInDual`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_AInDual) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |
---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_AInMulti`
**Thuộc chương:** [Chương 1: Xử lý tín hiệu Vào/Ra (I/O Processing)](../01_CHUONG_1_IO_PROCESSING.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `IO Processing 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 39–45)  
**Tệp định nghĩa khối lệnh:** `P_AInMulti_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_AInMulti`:** **173** Biến đầu vào (`Input Parameters`) | **110** Biến đầu ra (`Output Parameters`) | **38** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_AInMulti` trong Studio 5000
- **Chức năng cốt lõi:** Khối xử lý đa cảm biến (**từ 2 đến 8 đầu vào Analog** `Inp_PV0..7`). Hỗ trợ tính Trung bình cộng (`Average`), **Trung vị (`Median`)**, `Min`, `Max`, tự động loại bỏ cảm biến hỏng hoặc cảm biến có giá trị vọt lố bất thường (`Outlier`), và kiểm tra số lượng cảm biến tốt tối thiểu (`Cfg_MinGood`).
- **Khi nào CẦN sử dụng:** Khi đo nhiệt độ/áp suất trung bình trong buồng đốt, tháp chưng cất, hầm sấy có **3–8 cảm biến**, hoặc hệ thống an toàn biểu quyết **2oo3 (3 cảm biến lấy giá trị Trung vị Median)** giúp loại bỏ hoàn toàn nhiễu đột biến của 1 cảm biến bất kỳ.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi chỉ có 1 hoặc 2 cảm biến đơn giản.

### Quy trình 4 bước đấu nối và cấu hình `P_AInMulti`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_PVA`, `Inp_PVB`, `Inp_PVC`, `Inp_PVD`, `Inp_PVE`, `Inp_PVF`, `Inp_PVG`, `Inp_PVH`, `Inp_PVASrcQ`, `Inp_PVANotify`, `Inp_PVBSrcQ`, `Inp_PVBNotify`, `Inp_PVCSrcQ`, `Inp_PVCNotify`, `Inp_PVDSrcQ`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_HasMoreObj`, `Cfg_HasPVA`, `Cfg_HasPVB`, `Cfg_HasPVC`, `Cfg_HasPVD`, `Cfg_HasPVE`, `Cfg_HasPVF`, `Cfg_HasPVG`, `Cfg_HasPVH`, `Cfg_UsePVA`, `Cfg_UsePVB`, `Cfg_UsePVC`, `Cfg_UsePVD`, `Cfg_UsePVE`, `Cfg_UsePVF`, `Cfg_UsePVG`, `Cfg_UsePVH`, `Cfg_RejectUncertain`...
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_Desc`, `Cfg_EU`, `Cfg_Label`, `Cfg_PVATag`, `Cfg_PVBTag`, `Cfg_PVCTag`, `Cfg_PVDTag`, `Cfg_PVETag`, `Cfg_PVFTag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_AInMulti` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_AInMulti` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PCmd_ClearCapt`, `PCmd_Reset`, `PCmd_HiHiAck`, `PCmd_HiAck`, `PCmd_LoAck`, `PCmd_LoLoAck`, `PCmd_AnyRejectAck`, `PCmd_MinGoodAck`, `PCmd_FailAck`, `PCmd_HiHiSuppress`, `PCmd_HiSuppress`, `PCmd_LoSuppress`, `PCmd_LoLoSuppress`, `PCmd_AnyRejectSuppress`, `PCmd_MinGoodSuppress`, `PCmd_FailSuppress`, `PCmd_HiHiUnsuppress`, `PCmd_HiUnsuppress`...
   - **Cách sử dụng trong code PLC:** Gán giá trị đặt vào `PSet_*` (nếu có) hoặc kích xung 1 chu kỳ vào các bit lệnh `PCmd_* = 1` để xóa lỗi (`PCmd_Reset`), xác nhận cảnh báo (`PCmd_*Ack`) hoặc ức chế cảnh báo (`PCmd_*Suppress`). Khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi.
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Val`, `Val_InpPV`, `Val_PVA`, `Val_PVB`, `Val_PVC`, `Val_PVD`, `Val_PVE`, `Val_PVF`, `Val_PVG`, `Val_PVH`, `Val_PVMinCapt`, `Val_PVMaxCapt`, `Val_PVEUMin`, `Val_PVEUMax`, `Val_NumPVs`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 173 Biến Đầu Vào (`Input Parameters`) của `P_AInMulti`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp_PVA`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu biến quá trình đầu vào từ cảm biến kênh A (Input PV A) |
| **`Inp_PVB`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu biến quá trình đầu vào từ cảm biến kênh B (Input PV B) |
| **`Inp_PVC`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu biến quá trình đầu vào từ cảm biến kênh C (Input PV C) |
| **`Inp_PVD`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu biến quá trình đầu vào từ cảm biến kênh D (Input PV D) |
| **`Inp_PVE`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu biến quá trình đầu vào từ cảm biến kênh E (Input PV E) |
| **`Inp_PVF`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu biến quá trình đầu vào từ cảm biến kênh F (Input PV F) |
| **`Inp_PVG`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu biến quá trình đầu vào từ cảm biến kênh G (Input PV G) |
| **`Inp_PVH`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu biến quá trình đầu vào từ cảm biến kênh H (Input PV H) |
| **`Inp_PVASrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã nguồn gốc và chất lượng tín hiệu (enum) từ khối kênh P_AIChan của cảm biến A |
| **`Inp_PVANotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã mức độ ưu tiên cảnh báo và xác nhận (enum) của đối tượng kênh cảm biến A |
| **`Inp_PVBSrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã nguồn gốc và chất lượng tín hiệu (enum) từ khối kênh P_AIChan của cảm biến B |
| **`Inp_PVBNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã mức độ ưu tiên cảnh báo và xác nhận (enum) của đối tượng kênh cảm biến B |
| **`Inp_PVCSrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã nguồn gốc và chất lượng tín hiệu (enum) từ khối kênh P_AIChan của cảm biến C |
| **`Inp_PVCNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã mức độ ưu tiên cảnh báo và xác nhận (enum) của đối tượng kênh cảm biến C |
| **`Inp_PVDSrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã nguồn gốc và chất lượng tín hiệu (enum) từ khối kênh P_AIChan của cảm biến D |
| **`Inp_PVDNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã mức độ ưu tiên cảnh báo và xác nhận (enum) của đối tượng kênh cảm biến D |
| **`Inp_PVESrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã nguồn gốc và chất lượng tín hiệu (enum) từ khối kênh P_AIChan của cảm biến E |
| **`Inp_PVENotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã mức độ ưu tiên cảnh báo và xác nhận (enum) của đối tượng kênh cảm biến E |
| **`Inp_PVFSrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã nguồn gốc và chất lượng tín hiệu (enum) từ khối kênh P_AIChan của cảm biến F |
| **`Inp_PVFNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã mức độ ưu tiên cảnh báo và xác nhận (enum) của đối tượng kênh cảm biến F |
| **`Inp_PVGSrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã nguồn gốc và chất lượng tín hiệu (enum) từ khối kênh P_AIChan của cảm biến G |
| **`Inp_PVGNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã mức độ ưu tiên cảnh báo và xác nhận (enum) của đối tượng kênh cảm biến G |
| **`Inp_PVHSrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã nguồn gốc và chất lượng tín hiệu (enum) từ khối kênh P_AIChan của cảm biến H |
| **`Inp_PVHNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã mức độ ưu tiên cảnh báo và xác nhận (enum) của đối tượng kênh cảm biến H |
| **`Inp_PVABad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi chất lượng tín hiệu hoặc mất truyền thông của kênh cảm biến A (1 = Lỗi/Bad, 0 = Tốt/OK) |
| **`Inp_PVBBad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi chất lượng tín hiệu hoặc mất truyền thông của kênh cảm biến B (1 = Lỗi/Bad, 0 = Tốt/OK) |
| **`Inp_PVCBad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi chất lượng tín hiệu hoặc mất truyền thông của kênh cảm biến C (1 = Lỗi/Bad, 0 = Tốt/OK) |
| **`Inp_PVDBad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi chất lượng tín hiệu hoặc mất truyền thông của kênh cảm biến D (1 = Lỗi/Bad, 0 = Tốt/OK) |
| **`Inp_PVEBad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi chất lượng tín hiệu hoặc mất truyền thông của kênh cảm biến E (1 = Lỗi/Bad, 0 = Tốt/OK) |
| **`Inp_PVFBad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi chất lượng tín hiệu hoặc mất truyền thông của kênh cảm biến F (1 = Lỗi/Bad, 0 = Tốt/OK) |
| **`Inp_PVGBad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi chất lượng tín hiệu hoặc mất truyền thông của kênh cảm biến G (1 = Lỗi/Bad, 0 = Tốt/OK) |
| **`Inp_PVHBad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi chất lượng tín hiệu hoặc mất truyền thông của kênh cảm biến H (1 = Lỗi/Bad, 0 = Tốt/OK) |
| **`Inp_PVAUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái chất lượng tín hiệu kênh cảm biến A không chắc chắn (1 = Uncertain, 0 = OK) |
| **`Inp_PVBUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái chất lượng tín hiệu kênh cảm biến B không chắc chắn (1 = Uncertain, 0 = OK) |
| **`Inp_PVCUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái chất lượng tín hiệu kênh cảm biến C không chắc chắn (1 = Uncertain, 0 = OK) |
| **`Inp_PVDUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái chất lượng tín hiệu kênh cảm biến D không chắc chắn (1 = Uncertain, 0 = OK) |
| **`Inp_PVEUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái chất lượng tín hiệu kênh cảm biến E không chắc chắn (1 = Uncertain, 0 = OK) |
| **`Inp_PVFUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái chất lượng tín hiệu kênh cảm biến F không chắc chắn (1 = Uncertain, 0 = OK) |
| **`Inp_PVGUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái chất lượng tín hiệu kênh cảm biến G không chắc chắn (1 = Uncertain, 0 = OK) |
| **`Inp_PVHUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái chất lượng tín hiệu kênh cảm biến H không chắc chắn (1 = Uncertain, 0 = OK) |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân kích hoạt chế độ Mô phỏng (Simulation): Khi = 1, khóa các ngõ ra vật lý ở mức an toàn và dùng giá trị/phản hồi giả lập nội bộ; khi = 0, vận hành thực tế bình thường |
| **`Inp_HiHiGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát mức Rất Cao (High-High): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_HiGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát mức Cao (High): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_LoGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát mức Thấp (Low): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_LoLoGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát mức Rất Thấp (Low-Low): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_FailGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát lỗi tín hiệu / hỏng kênh đo (Signal Failure): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_HasPVA`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khai báo kênh cảm biến A (Inp_PVA) có tồn tại và được đấu nối vào khối |
| **`Cfg_HasPVB`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khai báo kênh cảm biến B (Inp_PVB) có tồn tại và được đấu nối vào khối |
| **`Cfg_HasPVC`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khai báo kênh cảm biến C (Inp_PVC) có tồn tại và được đấu nối vào khối |
| **`Cfg_HasPVD`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khai báo kênh cảm biến D (Inp_PVD) có tồn tại và được đấu nối vào khối |
| **`Cfg_HasPVE`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khai báo kênh cảm biến E (Inp_PVE) có tồn tại và được đấu nối vào khối |
| **`Cfg_HasPVF`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khai báo kênh cảm biến F (Inp_PVF) có tồn tại và được đấu nối vào khối |
| **`Cfg_HasPVG`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khai báo kênh cảm biến G (Inp_PVG) có tồn tại và được đấu nối vào khối |
| **`Cfg_HasPVH`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khai báo kênh cảm biến H (Inp_PVH) có tồn tại và được đấu nối vào khối |
| **`Cfg_UsePVA`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Đưa kênh cảm biến A (Inp_PVA) vào phép tính PV tổng hợp; 0 = Tạm tách kênh A ra khỏi phép tính (Maintenance Exclude) |
| **`Cfg_UsePVB`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Đưa kênh cảm biến B (Inp_PVB) vào phép tính PV tổng hợp; 0 = Tạm tách kênh B ra khỏi phép tính (Maintenance Exclude) |
| **`Cfg_UsePVC`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Đưa kênh cảm biến C (Inp_PVC) vào phép tính PV tổng hợp; 0 = Tạm tách kênh C ra khỏi phép tính (Maintenance Exclude) |
| **`Cfg_UsePVD`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Đưa kênh cảm biến D (Inp_PVD) vào phép tính PV tổng hợp; 0 = Tạm tách kênh D ra khỏi phép tính (Maintenance Exclude) |
| **`Cfg_UsePVE`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Đưa kênh cảm biến E (Inp_PVE) vào phép tính PV tổng hợp; 0 = Tạm tách kênh E ra khỏi phép tính (Maintenance Exclude) |
| **`Cfg_UsePVF`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Đưa kênh cảm biến F (Inp_PVF) vào phép tính PV tổng hợp; 0 = Tạm tách kênh F ra khỏi phép tính (Maintenance Exclude) |
| **`Cfg_UsePVG`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Đưa kênh cảm biến G (Inp_PVG) vào phép tính PV tổng hợp; 0 = Tạm tách kênh G ra khỏi phép tính (Maintenance Exclude) |
| **`Cfg_UsePVH`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Đưa kênh cảm biến H (Inp_PVH) vào phép tính PV tổng hợp; 0 = Tạm tách kênh H ra khỏi phép tính (Maintenance Exclude) |
| **`Cfg_RejectUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tự động loại bỏ cảm biến có chất lượng Không chắc chắn (Uncertain) ra khỏi phép tính PV tổng hợp; 0 = Vẫn chấp nhận cảm biến Uncertain |
| **`Cfg_UseStdDev`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Phương pháp phát hiện cảm biến vọt lố bất thường (Outlier Rejection): 1 = So sánh với tích của Độ lệch chuẩn (Standard Deviation × Cfg_DiffLim); 0 = So sánh trực tiếp độ lệch tuyệt đối với Cfg_DiffLim |
| **`Cfg_CalcAvg`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Phương pháp tính giá trị PV tổng hợp khi có từ 3 cảm biến tốt trở lên: 1 = Tính Trung bình cộng (Average); 0 = Lấy giá trị Trung vị (Median) |
| **`Cfg_NoSubstPV`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Khóa chức năng nhập giá trị thay thế (Substitute PV): 0 = Cho phép nhân viên bảo trì nhập giá trị PV thay thế; 1 = Cấm sử dụng tính năng Substitute PV |
| **`Cfg_HasChanObjA`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết kênh cảm biến A có sử dụng khối chẩn đoán kênh (P_AIChan) và bật nút điều hướng tương ứng |
| **`Cfg_HasChanObjB`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết kênh cảm biến B có sử dụng khối chẩn đoán kênh (P_AIChan) và bật nút điều hướng tương ứng |
| **`Cfg_HasChanObjC`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết kênh cảm biến C có sử dụng khối chẩn đoán kênh (P_AIChan) và bật nút điều hướng tương ứng |
| **`Cfg_HasChanObjD`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết kênh cảm biến D có sử dụng khối chẩn đoán kênh (P_AIChan) và bật nút điều hướng tương ứng |
| **`Cfg_HasChanObjE`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết kênh cảm biến E có sử dụng khối chẩn đoán kênh (P_AIChan) và bật nút điều hướng tương ứng |
| **`Cfg_HasChanObjF`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết kênh cảm biến F có sử dụng khối chẩn đoán kênh (P_AIChan) và bật nút điều hướng tương ứng |
| **`Cfg_HasChanObjG`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết kênh cảm biến G có sử dụng khối chẩn đoán kênh (P_AIChan) và bật nút điều hướng tương ứng |
| **`Cfg_HasChanObjH`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết kênh cảm biến H có sử dụng khối chẩn đoán kênh (P_AIChan) và bật nút điều hướng tương ứng |
| **`Cfg_PVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho giá trị PV trên màn hình HMI (từ 0 đến 6 chữ số) |
| **`Cfg_HasHiHiAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo mức Rất Cao (High-High): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasHiAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo mức Cao (High): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasLoAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo mức Thấp (Low): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasLoLoAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo mức Rất Thấp (Low-Low): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasAnyRejectAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo có ít nhất 1 cảm biến đầu vào bị loại bỏ (Any Input Rejected): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasMinGoodAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo số cảm biến tốt chạm mức tối thiểu (Minimum Good Inputs): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasFailAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HiHiResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo mức Rất Cao (High-High): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_HiResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo mức Cao (High): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_LoResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo mức Thấp (Low): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_LoLoResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo mức Rất Thấp (Low-Low): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_AnyRejectResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo có ít nhất 1 cảm biến đầu vào bị loại bỏ (Any Input Rejected): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_MinGoodResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo số cảm biến tốt chạm mức tối thiểu (Minimum Good Inputs): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_FailResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_HiHiAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo mức Rất Cao (High-High): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_HiAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo mức Cao (High): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_LoAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo mức Thấp (Low): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_LoLoAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo mức Rất Thấp (Low-Low): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_AnyRejectAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo có ít nhất 1 cảm biến đầu vào bị loại bỏ (Any Input Rejected): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_MinGoodAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo số cảm biến tốt chạm mức tối thiểu (Minimum Good Inputs): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_FailAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_MinGood`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số lượng cảm biến hoạt động tốt tối thiểu bắt buộc (từ 1 đến tổng số cảm biến Cfg_HasPV*) để giá trị PV đầu ra được công nhận là Tốt (Good) |
| **`Cfg_CalcWhen2`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Phương pháp tính giá trị PV tổng hợp khi chỉ còn đúng 2 cảm biến tốt: 0 = Trung bình cộng (Average), 1 = Nhỏ nhất (Minimum), 2 = Lớn nhất (Maximum) |
| **`Cfg_HiHiSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo mức Rất Cao (High-High) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_HiSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo mức Cao (High) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_LoSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo mức Thấp (Low) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_LoLoSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo mức Rất Thấp (Low-Low) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_AnyRejectSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo có ít nhất 1 cảm biến đầu vào bị loại bỏ (Any Input Rejected) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_MinGoodSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo số cảm biến tốt chạm mức tối thiểu (Minimum Good Inputs) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_FailSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_InpRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đầu vào thô tối thiểu (Raw Min, ví dụ 4.0 mA hoặc 0) dùng để quy đổi tỷ lệ tuyến tính sang đơn vị kỹ thuật |
| **`Cfg_InpRawMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đầu vào thô tối đa (Raw Max, ví dụ 20.0 mA hoặc 100) dùng để quy đổi tỷ lệ tuyến tính sang đơn vị kỹ thuật |
| **`Cfg_PVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tương ứng với Raw Min (PV EU Min — hỗ trợ cả quy đổi ngược nếu đặt EUMin > EUMax) |
| **`Cfg_PVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tương ứng với Raw Max (PV EU Max — hỗ trợ cả quy đổi ngược nếu đặt EUMax < EUMin) |
| **`Cfg_AbsDevLim`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng độ lệch tuyệt đối tối đa cho phép (Absolute Deviation Threshold, đơn vị EU) dùng để loại bỏ cảm biến vọt lố khi Cfg_UseStdDev = 0 |
| **`Cfg_FiltTC`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Hằng số thời gian của bộ lọc thông thấp bậc 1 cho tín hiệu PV (tính bằng giây; đặt = 0.0 nếu không lọc) |
| **`Cfg_HiHiDB`** | `REAL` | `1.0` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng mức Rất Cao (High-High) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_HiDB`** | `REAL` | `1.0` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng mức Cao (High) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_LoDB`** | `REAL` | `1.0` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng mức Thấp (Low) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_LoLoDB`** | `REAL` | `1.0` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng mức Rất Thấp (Low-Low) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_FailDB`** | `REAL` | `0.41666666` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng lỗi tín hiệu / hỏng kênh đo (Signal Failure) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_HiHiGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho mức Rất Cao (High-High): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_HiGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho mức Cao (High): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_LoGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho mức Thấp (Low): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_LoLoGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho mức Rất Thấp (Low-Low): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_FailGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho lỗi tín hiệu / hỏng kênh đo (Signal Failure): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_HiHiOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho mức Rất Cao (High-High): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_HiOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho mức Cao (High): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_LoOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho mức Thấp (Low): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_LoLoOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho mức Rất Thấp (Low-Low): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_FailOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho lỗi tín hiệu / hỏng kênh đo (Signal Failure): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_HiHiOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho mức Rất Cao (High-High): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_HiOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho mức Cao (High): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_LoOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho mức Thấp (Low): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_LoLoOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho mức Rất Thấp (Low-Low): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_FailOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho lỗi tín hiệu / hỏng kênh đo (Signal Failure): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_FailHiLim`** | `REAL` | `103.958336` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng giới hạn trên báo lỗi đứt dây / vượt dải tín hiệu đầu vào (Out-of-Range High, đơn vị thô Raw hoặc EU) |
| **`Cfg_FailLoLim`** | `REAL` | `-2.0833333` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng giới hạn dưới báo lỗi đứt dây / vượt dải tín hiệu đầu vào (Out-of-Range Low, đơn vị thô Raw hoặc EU) |
| **`MSet_SubstPV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Bảo trì HMI] Giá trị đặt từ Nhân viên bảo trì (Maintenance HMI): Giá trị PV thay thế (Substitute PV, đơn vị EU) do nhân viên bảo trì nhập thủ công để ghi đè tín hiệu cảm biến |
| **`Set_SimPVA`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Giá trị PV giả lập cho kênh cảm biến A (đơn vị kỹ thuật EU) dùng khi bật chế độ Mô phỏng (Inp_Sim = 1) |
| **`Set_SimPVB`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Giá trị PV giả lập cho kênh cảm biến B (đơn vị kỹ thuật EU) dùng khi bật chế độ Mô phỏng (Inp_Sim = 1) |
| **`Set_SimPVC`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Giá trị PV giả lập cho kênh cảm biến C (đơn vị kỹ thuật EU) dùng khi bật chế độ Mô phỏng (Inp_Sim = 1) |
| **`Set_SimPVD`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Giá trị PV giả lập cho kênh cảm biến D (đơn vị kỹ thuật EU) dùng khi bật chế độ Mô phỏng (Inp_Sim = 1) |
| **`Set_SimPVE`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Giá trị PV giả lập cho kênh cảm biến E (đơn vị kỹ thuật EU) dùng khi bật chế độ Mô phỏng (Inp_Sim = 1) |
| **`Set_SimPVF`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Giá trị PV giả lập cho kênh cảm biến F (đơn vị kỹ thuật EU) dùng khi bật chế độ Mô phỏng (Inp_Sim = 1) |
| **`Set_SimPVG`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Giá trị PV giả lập cho kênh cảm biến G (đơn vị kỹ thuật EU) dùng khi bật chế độ Mô phỏng (Inp_Sim = 1) |
| **`Set_SimPVH`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Giá trị PV giả lập cho kênh cảm biến H (đơn vị kỹ thuật EU) dùng khi bật chế độ Mô phỏng (Inp_Sim = 1) |
| **`PCmd_ClearCapt`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa giá trị đỉnh Cực tiểu / Cực đại (Min/Max Excursion) đã lưu của PV |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`PCmd_HiHiAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo mức Rất Cao (High-High) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_HiAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo mức Cao (High) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_LoAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo mức Thấp (Low) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_LoLoAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo mức Rất Thấp (Low-Low) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_AnyRejectAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo có ít nhất 1 cảm biến đầu vào bị loại bỏ (Any Input Rejected) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_MinGoodAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo số cảm biến tốt chạm mức tối thiểu (Minimum Good Inputs) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_FailAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_HiHiSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo mức Rất Cao (High-High) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_HiSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo mức Cao (High) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_LoSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo mức Thấp (Low) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_LoLoSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo mức Rất Thấp (Low-Low) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_AnyRejectSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo có ít nhất 1 cảm biến đầu vào bị loại bỏ (Any Input Rejected) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_MinGoodSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo số cảm biến tốt chạm mức tối thiểu (Minimum Good Inputs) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_HiHiUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo mức Rất Cao (High-High) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_HiUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo mức Cao (High) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_LoUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo mức Thấp (Low) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_LoLoUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo mức Rất Thấp (Low-Low) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_AnyRejectUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo có ít nhất 1 cảm biến đầu vào bị loại bỏ (Any Input Rejected) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_MinGoodUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo số cảm biến tốt chạm mức tối thiểu (Minimum Good Inputs) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_HiHiUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo mức Rất Cao (High-High) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_HiUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo mức Cao (High) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_LoUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo mức Thấp (Low) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_LoLoUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo mức Rất Thấp (Low-Low) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_FailUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) ngay lập tức (biến xung tự động xóa về 0) |

## 3. Bảng chi tiết 110 Biến Đầu Ra (`Output Parameters`) của `P_AInMulti`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Cfg_HiHiLim`** | `REAL` | `1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Ngưỡng giới hạn tác động (Threshold Limit) cho mức Rất Cao (High-High) (tính theo đơn vị kỹ thuật EU) |
| **`Cfg_HiLim`** | `REAL` | `1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Ngưỡng giới hạn tác động (Threshold Limit) cho mức Cao (High) (tính theo đơn vị kỹ thuật EU) |
| **`Cfg_LoLoLim`** | `REAL` | `-1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Ngưỡng giới hạn tác động (Threshold Limit) cho mức Rất Thấp (Low-Low) (tính theo đơn vị kỹ thuật EU) |
| **`Val`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu ra chính của biến quá trình PV (đã qua quy đổi đơn vị kỹ thuật EU, lọc nhiễu và chọn giá trị thay thế Substitute PV nếu có) |
| **`Val_InpPV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị PV thực tế tính từ tín hiệu đầu vào (sau khi quy đổi đơn vị EU nhưng trước khi qua bộ chọn Substitute PV) |
| **`Val_PVA`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị thực tế đã quy đổi đơn vị kỹ thuật (EU) của kênh cảm biến A |
| **`Val_PVB`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị thực tế đã quy đổi đơn vị kỹ thuật (EU) của kênh cảm biến B |
| **`Val_PVC`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị thực tế đã quy đổi đơn vị kỹ thuật (EU) của kênh cảm biến C |
| **`Val_PVD`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị thực tế đã quy đổi đơn vị kỹ thuật (EU) của kênh cảm biến D |
| **`Val_PVE`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị thực tế đã quy đổi đơn vị kỹ thuật (EU) của kênh cảm biến E |
| **`Val_PVF`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị thực tế đã quy đổi đơn vị kỹ thuật (EU) của kênh cảm biến F |
| **`Val_PVG`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị thực tế đã quy đổi đơn vị kỹ thuật (EU) của kênh cảm biến G |
| **`Val_PVH`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị thực tế đã quy đổi đơn vị kỹ thuật (EU) của kênh cảm biến H |
| **`Val_PVMinCapt`** | `REAL` | `1.5000000000000001E+38` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cực tiểu (Minimum Peak Excursion) của PV ghi nhận được kể từ lần xóa (ClearCapt) gần nhất |
| **`Val_PVMaxCapt`** | `REAL` | `-1.5000000000000001E+38` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cực đại (Maximum Peak Excursion) của PV ghi nhận được kể từ lần xóa (ClearCapt) gần nhất |
| **`Val_PVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận dưới của dải đo đơn vị kỹ thuật PV = MIN(Cfg_PVEUMin, Cfg_PVEUMax) |
| **`Val_PVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận trên của dải đo đơn vị kỹ thuật PV = MAX(Cfg_PVEUMin, Cfg_PVEUMax) |
| **`Val_NumPVs`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Số lượng kênh cảm biến đầu vào hiện đang hợp lệ và được đưa vào phép tính PV tổng hợp |
| **`SrcQ_IOA`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O cảm biến A (kiểu liệt kê enum) |
| **`SrcQ_IOB`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O cảm biến B (kiểu liệt kê enum) |
| **`SrcQ_IOC`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O cảm biến C (kiểu liệt kê enum) |
| **`SrcQ_IOD`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O cảm biến D (kiểu liệt kê enum) |
| **`SrcQ_IOE`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O cảm biến E (kiểu liệt kê enum) |
| **`SrcQ_IOF`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O cảm biến F (kiểu liệt kê enum) |
| **`SrcQ_IOG`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O cảm biến G (kiểu liệt kê enum) |
| **`SrcQ_IOH`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O cảm biến H (kiểu liệt kê enum) |
| **`SrcQ_IO`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng dữ liệu tổng hợp cuối cùng (0 = Tín hiệu thực Tốt đã xác nhận, 1 = Tín hiệu thực Tốt giả định, 2 = Không cấu hình phản hồi/Tốt giả định, 8 = Đang mô phỏng, 9 = Mô phỏng vòng lặp, 10 = Giá trị nhập tay, 16 = Ngoài đặc tính kỹ thuật, 17 = Thay thế tại thiết bị/bus, 18 = Thay thế bởi Bảo trì, 19 = Ngắt bảo vệ giữ giá trị cũ, 20 = Ngắt bảo vệ dùng giá trị thay thế, 32 = Lỗi tín hiệu vượt dải/NaN, 33 = Lỗi kênh I/O, 34 = Lỗi module I/O, 35 = Lỗi cấu hình) |
| **`Val_Fault`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái lỗi thiết bị (Device Fault Status — kiểu liệt kê enum: 0 = Không lỗi, 16 = Lỗi khởi động/mở, 17 = Lỗi dừng/đóng, 20 = Lo, 21 = Hi, 24 = LoLo, 25 = HiHi, 32 = Fail/I/O Fault, 34 = Lỗi cấu hình CfgErr) |
| **`Val_NotifyAll`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận tổng hợp của khối này cộng với các đối tượng kênh/quá tải liên kết |
| **`Val_UnackAlmC`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms Count) |
| **`Sts_PVAReject`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Kênh cảm biến A đang bị loại bỏ (Rejected) và không được đưa vào phép tính PV tổng hợp |
| **`Sts_PVBReject`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Kênh cảm biến B đang bị loại bỏ (Rejected) và không được đưa vào phép tính PV tổng hợp |
| **`Sts_PVCReject`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Kênh cảm biến C đang bị loại bỏ (Rejected) và không được đưa vào phép tính PV tổng hợp |
| **`Sts_PVDReject`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Kênh cảm biến D đang bị loại bỏ (Rejected) và không được đưa vào phép tính PV tổng hợp |
| **`Sts_PVEReject`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Kênh cảm biến E đang bị loại bỏ (Rejected) và không được đưa vào phép tính PV tổng hợp |
| **`Sts_PVFReject`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Kênh cảm biến F đang bị loại bỏ (Rejected) và không được đưa vào phép tính PV tổng hợp |
| **`Sts_PVGReject`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Kênh cảm biến G đang bị loại bỏ (Rejected) và không được đưa vào phép tính PV tổng hợp |
| **`Sts_PVHReject`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Kênh cảm biến H đang bị loại bỏ (Rejected) và không được đưa vào phép tính PV tổng hợp |
| **`Sts_SubstPV`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang sử dụng giá trị PV thay thế (Substitute PV do nhân viên bảo trì nhập) thay cho tín hiệu cảm biến thật |
| **`Sts_PVBad`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Chất lượng tín hiệu PV Xấu (Bad Quality) hoặc tín hiệu vượt ngoài dải lỗi (Out of Range) |
| **`Sts_PVUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Chất lượng tín hiệu PV Không chắc chắn (Uncertain Quality) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoặc cưỡng bức bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có ít nhất một cảnh báo bị Tạm hoãn (Shelved), Vô hiệu hóa (Disabled) hoặc Ức chế (Suppressed) — hiển thị biểu tượng chữ "I" trên HMI |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_Raw`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải giá trị thô đầu vào (Cfg_InpRawMin = Cfg_InpRawMax) bị trùng nhau |
| **`Err_EU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải quy đổi đơn vị kỹ thuật (Engineering Units) có giá trị Tối thiểu bằng Tối đa (Min = Max) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị đặt bộ định thời gian (Timer Preset) nằm ngoài phạm vi hợp lệ 0..2.147.483 giây |
| **`Err_Filt`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thông số bộ lọc tín hiệu PV (Cfg_FiltTC hoặc Cfg_RateTime) không hợp lệ |
| **`Err_DB`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Độ rộng vùng chết (Deadband) của một ngưỡng cảnh báo bị đặt số âm (< 0.0) |
| **`Err_Has`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Bắt buộc phải bật ít nhất một kênh cảm biến đầu vào (Cfg_HasPVx = 1) |
| **`Err_Use`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Phải có ít nhất một kênh cảm biến được chọn đưa vào phép tính (Cfg_UsePVx = 1) |
| **`Err_MinGood`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Số lượng cảm biến tốt tối thiểu (Cfg_MinGood) nằm ngoài phạm vi hợp lệ [1..8] |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình cảnh báo: Thời gian duy trì tối thiểu (Min On Time), thời gian tạm hoãn (Shelf Time) hoặc mức độ nghiêm trọng (Severity 1..1000) không hợp lệ |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc chốt ngắt bảo vệ (Shed) đã hết lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_HiHiCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của mức Rất Cao (High-High) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_HiHiGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của mức Rất Cao (High-High) đã đếm xong, đang cho phép giám sát |
| **`Sts_HiHi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện mức Rất Cao (High-High) |
| **`Alm_HiHi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho mức Rất Cao (High-High) |
| **`Ack_HiHi`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động mức Rất Cao (High-High) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_HiHiDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Rất Cao (High-High) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_HiHiShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Rất Cao (High-High) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_HiHiSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Rất Cao (High-High) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_HiCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của mức Cao (High) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_HiGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của mức Cao (High) đã đếm xong, đang cho phép giám sát |
| **`Sts_Hi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện mức Cao (High) |
| **`Alm_Hi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho mức Cao (High) |
| **`Ack_Hi`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động mức Cao (High) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_HiDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Cao (High) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_HiShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Cao (High) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_HiSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Cao (High) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_LoCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của mức Thấp (Low) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_LoGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của mức Thấp (Low) đã đếm xong, đang cho phép giám sát |
| **`Sts_Lo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện mức Thấp (Low) |
| **`Alm_Lo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho mức Thấp (Low) |
| **`Ack_Lo`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động mức Thấp (Low) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_LoDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Thấp (Low) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_LoShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Thấp (Low) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_LoSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Thấp (Low) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_LoLoCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của mức Rất Thấp (Low-Low) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_LoLoGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của mức Rất Thấp (Low-Low) đã đếm xong, đang cho phép giám sát |
| **`Sts_LoLo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện mức Rất Thấp (Low-Low) |
| **`Alm_LoLo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho mức Rất Thấp (Low-Low) |
| **`Ack_LoLo`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động mức Rất Thấp (Low-Low) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_LoLoDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Rất Thấp (Low-Low) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_LoLoShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Rất Thấp (Low-Low) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_LoLoSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Rất Thấp (Low-Low) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_AnyReject`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có ít nhất một kênh cảm biến đầu vào đang bị loại bỏ khỏi phép tính tổng hợp |
| **`Alm_AnyReject`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho có ít nhất 1 cảm biến đầu vào bị loại bỏ (Any Input Rejected) |
| **`Ack_AnyReject`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động có ít nhất 1 cảm biến đầu vào bị loại bỏ (Any Input Rejected) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_AnyRejectDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo có ít nhất 1 cảm biến đầu vào bị loại bỏ (Any Input Rejected) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_AnyRejectShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo có ít nhất 1 cảm biến đầu vào bị loại bỏ (Any Input Rejected) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_AnyRejectSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo có ít nhất 1 cảm biến đầu vào bị loại bỏ (Any Input Rejected) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_MinGood`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Số lượng cảm biến tốt đã chạm mức tối thiểu bắt buộc (Cfg_MinGood); nếu mất thêm 1 cảm biến sẽ báo lỗi toàn bộ PV |
| **`Alm_MinGood`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho số cảm biến tốt chạm mức tối thiểu (Minimum Good Inputs) |
| **`Ack_MinGood`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động số cảm biến tốt chạm mức tối thiểu (Minimum Good Inputs) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_MinGoodDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo số cảm biến tốt chạm mức tối thiểu (Minimum Good Inputs) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_MinGoodShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo số cảm biến tốt chạm mức tối thiểu (Minimum Good Inputs) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_MinGoodSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo số cảm biến tốt chạm mức tối thiểu (Minimum Good Inputs) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_FailCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của lỗi tín hiệu / hỏng kênh đo (Signal Failure) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_FailGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của lỗi tín hiệu / hỏng kênh đo (Signal Failure) đã đếm xong, đang cho phép giám sát |
| **`Sts_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi tín hiệu / hỏng kênh đo (Signal Failure) |
| **`Alm_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi tín hiệu / hỏng kênh đo (Signal Failure) |
| **`Ack_Fail`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi tín hiệu / hỏng kênh đo (Signal Failure) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_FailDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_FailShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_FailSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`P_AInMulti`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_AInMulti) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 38 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_AInMulti`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`AnyReject`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho có ít nhất 1 cảm biến đầu vào bị loại bỏ (Any Input Rejected) |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'Multiple (to 8) Analog Input'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật (Engineering Units, ví dụ: "°C", "bar", "m3/h", "%") hiển thị trên màn hình HMI |
| **`Cfg_Label`** | `STRING_20` | `'Analog Input'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_PVATag`** | `STRING_20` | `'Sensor / Input A'` | [Biến cấu hình thông số] Tên thẻ (Tagname) của cảm biến kênh A hiển thị trên màn hình HMI Faceplate |
| **`Cfg_PVBTag`** | `STRING_20` | `'Sensor / Input B'` | [Biến cấu hình thông số] Tên thẻ (Tagname) của cảm biến kênh B hiển thị trên màn hình HMI Faceplate |
| **`Cfg_PVCTag`** | `STRING_20` | `'Sensor / Input C'` | [Biến cấu hình thông số] Tên thẻ (Tagname) của cảm biến kênh C hiển thị trên màn hình HMI Faceplate |
| **`Cfg_PVDTag`** | `STRING_20` | `'Sensor / Input D'` | [Biến cấu hình thông số] Tên thẻ (Tagname) của cảm biến kênh D hiển thị trên màn hình HMI Faceplate |
| **`Cfg_PVETag`** | `STRING_20` | `'Sensor / Input E'` | [Biến cấu hình thông số] Tên thẻ (Tagname) của cảm biến kênh E hiển thị trên màn hình HMI Faceplate |
| **`Cfg_PVFTag`** | `STRING_20` | `'Sensor / Input F'` | [Biến cấu hình thông số] Tên thẻ (Tagname) của cảm biến kênh F hiển thị trên màn hình HMI Faceplate |
| **`Cfg_PVGTag`** | `STRING_20` | `'Sensor / Input G'` | [Biến cấu hình thông số] Tên thẻ (Tagname) của cảm biến kênh G hiển thị trên màn hình HMI Faceplate |
| **`Cfg_PVHTag`** | `STRING_20` | `'Sensor / Input H'` | [Biến cấu hình thông số] Tên thẻ (Tagname) của cảm biến kênh H hiển thị trên màn hình HMI Faceplate |
| **`Cfg_Tag`** | `STRING_20` | `'P_AInMulti'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`Fail`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi tín hiệu / hỏng kênh đo (Signal Failure) |
| **`FailGate`** | `P_Gate` | `{...}` | [Khối con P_Gate nội bộ] Bộ định thời trễ mở cổng (Gate Delay), trễ bật (On-Delay) và trễ tắt (Off-Delay) cho lỗi tín hiệu / hỏng kênh đo (Signal Failure) |
| **`Hi`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho mức Cao (High) |
| **`HiGate`** | `P_Gate` | `{...}` | [Khối con P_Gate nội bộ] Bộ định thời trễ mở cổng (Gate Delay), trễ bật (On-Delay) và trễ tắt (Off-Delay) cho mức Cao (High) |
| **`HiHi`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho mức Rất Cao (High-High) |
| **`HiHiGate`** | `P_Gate` | `{...}` | [Khối con P_Gate nội bộ] Bộ định thời trễ mở cổng (Gate Delay), trễ bật (On-Delay) và trễ tắt (Off-Delay) cho mức Rất Cao (High-High) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_AInMulti'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`Lo`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho mức Thấp (Low) |
| **`LoGate`** | `P_Gate` | `{...}` | [Khối con P_Gate nội bộ] Bộ định thời trễ mở cổng (Gate Delay), trễ bật (On-Delay) và trễ tắt (Off-Delay) cho mức Thấp (Low) |
| **`LoLo`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho mức Rất Thấp (Low-Low) |
| **`LoLoGate`** | `P_Gate` | `{...}` | [Khối con P_Gate nội bộ] Bộ định thời trễ mở cổng (Gate Delay), trễ bật (On-Delay) và trễ tắt (Off-Delay) cho mức Rất Thấp (Low-Low) |
| **`MCmd_InpPV`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Sử dụng lại tín hiệu PV thực tế từ cảm biến đầu vào (ngưng dùng giá trị thay thế Substitute PV) |
| **`MCmd_SubstPV`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Chuyển sang sử dụng giá trị PV thay thế (Substitute PV) do nhân viên bảo trì nhập |
| **`MinGood`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho số cảm biến tốt chạm mức tối thiểu (Minimum Good Inputs) |
| **`MRdy_InpPV`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_InpPV (Sử dụng lại tín hiệu PV thực tế từ cảm biến đầu vào (ngưng dùng giá trị thay thế Substitute PV)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_SubstPV`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_SubstPV (Chuyển sang sử dụng giá trị PV thay thế (Substitute PV) do nhân viên bảo trì nhập), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`OCmd_ClearCapt`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa giá trị đỉnh Cực tiểu / Cực đại (Min/Max Excursion) đã lưu của PV |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`Val_Notify`** | `SINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo hiện hành và trạng thái xác nhận của khối này (0 = Không có cảnh báo, 1 = Đã hết lỗi nhưng chờ Reset/Ack, 2..3 = Thấp, 4..5 = Trung bình, 6..7 = Cao, 8..9 = Khẩn cấp) |
---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_DIn`
**Thuộc chương:** [Chương 1: Xử lý tín hiệu Vào/Ra (I/O Processing)](../01_CHUONG_1_IO_PROCESSING.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `IO Processing 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 46–52)  
**Tệp định nghĩa khối lệnh:** `P_DIn_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_DIn`:** **33** Biến đầu vào (`Input Parameters`) | **34** Biến đầu ra (`Output Parameters`) | **22** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_DIn` trong Studio 5000
- **Chức năng cốt lõi:** Khối xử lý 1 tín hiệu đầu vào số (`BOOL`). Hỗ trợ đảo mức logic (`Cfg_Inv`), lọc chống rung tiếp điểm (**Debounce On-Delay / Off-Delay**), đặt tên hiển thị trạng thái 0/1 trên HMI, so sánh với trạng thái mục tiêu để tạo cảnh báo bất thường và hỗ trợ Substitute/Sim.
- **Khi nào CẦN sử dụng:** Khi đưa bất kỳ **công tắc áp suất (PSH/PSL), công tắc mức (LSH/LSL), công tắc dòng chảy (FSL), nút dừng khẩn cấp, báo cháy** lên màn hình SCADA và cần lọc chống báo giả do sóng sánh chất lỏng (Debounce).
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi cần giám sát công tắc tốc độ quay gắn với trạng thái chạy mô-tơ hoặc cần 2 cảnh báo riêng → Dùng [`P_DInAdv.md`](./P_DInAdv.md).

### Quy trình 4 bước đấu nối và cấu hình `P_DIn`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_PV`, `Inp_IOFault`, `Inp_PVNotify`, `Inp_Target`, `Inp_Gate`, `Inp_Sim`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_NoSubstPV`, `Cfg_NormTextVis`, `Cfg_HasMoreObj`, `Cfg_HasTgtDisagreeAlm`, `Cfg_TgtDisagreeResetReqd`, `Cfg_TgtDisagreeAckReqd`, `Cfg_HasIOFaultAlm`, `Cfg_IOFaultResetReqd`, `Cfg_IOFaultAckReqd`, `Cfg_TgtDisagreeSeverity`, `Cfg_IOFaultSeverity`, `Cfg_Debounce`, `Cfg_GateDly`, `Cfg_TgtDisagreeOnDly`, `Cfg_TgtDisagreeOffDly`
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_0StText`, `Cfg_1StText`, `Cfg_Area`, `Cfg_Desc`, `Cfg_Label`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_DIn` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_DIn` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PCmd_Reset`, `PCmd_TgtDisagreeAck`, `PCmd_TgtDisagreeSuppress`, `PCmd_TgtDisagreeUnsuppress`, `PCmd_TgtDisagreeUnshelve`, `PCmd_IOFaultAck`, `PCmd_IOFaultSuppress`, `PCmd_IOFaultUnsuppress`, `PCmd_IOFaultUnshelve`
   - **Cách sử dụng trong code PLC:** Gán giá trị đặt vào `PSet_*` (nếu có) hoặc kích xung 1 chu kỳ vào các bit lệnh `PCmd_* = 1` để xóa lỗi (`PCmd_Reset`), xác nhận cảnh báo (`PCmd_*Ack`) hoặc ức chế cảnh báo (`PCmd_*Suppress`). Khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi.
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Val_Sts`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Sts_PV`, `Sts_SubstPV`, `Sts_InpPV`, `Sts_PVBad`, `Sts_MaintByp`, `Sts_AlmInh`, `Sts_Err`, `Sts_RdyReset`, `Sts_RdyAck`, `Sts_TgtDisagreeCmp`, `Sts_TgtDisagreeGate`, `Sts_TgtDisagree`, `Alm_TgtDisagree`, `Sts_TgtDisagreeDisabled`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 33 Biến Đầu Vào (`Input Parameters`) của `P_DIn`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp_PV`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu biến quá trình đầu vào (Process Variable - PV) đọc từ cảm biến hoặc thẻ Analog/Discrete Input |
| **`Inp_IOFault`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi truyền thông I/O của thiết bị (0 = Tốt/OK, 1 = Lỗi truyền thông I/O Fault) |
| **`Inp_PVNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã mức độ ưu tiên cảnh báo và trạng thái xác nhận (enum) của đối tượng PV liên kết |
| **`Inp_Target`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái mục tiêu mong muốn của tín hiệu đầu vào số (0 hoặc 1): Nếu trạng thái thực tế khác Inp_Target sẽ báo lệch mục tiêu (Target Disagree) |
| **`Inp_Gate`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input): Khi = 1, cho phép giám sát tín hiệu đầu vào Inp (áp dụng On-Delay/Off-Delay sau thời gian Cfg_GateDly); khi = 0, khóa cổng và ép ngõ ra về 0 |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân kích hoạt chế độ Mô phỏng (Simulation): Khi = 1, khóa các ngõ ra vật lý ở mức an toàn và dùng giá trị/phản hồi giả lập nội bộ; khi = 0, vận hành thực tế bình thường |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Cfg_NoSubstPV`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Khóa chức năng nhập giá trị thay thế (Substitute PV): 0 = Cho phép nhân viên bảo trì nhập giá trị PV thay thế; 1 = Cấm sử dụng tính năng Substitute PV |
| **`Cfg_NormTextVis`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Luôn hiển thị chuỗi trạng thái trên biểu tượng đồ họa ngay cả khi ở trạng thái bình thường; 0 = Ẩn chữ khi bình thường |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_HasTgtDisagreeAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lệch trạng thái mục tiêu (Target Disagree): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_TgtDisagreeResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lệch trạng thái mục tiêu (Target Disagree): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_TgtDisagreeAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lệch trạng thái mục tiêu (Target Disagree): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_TgtDisagreeSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lệch trạng thái mục tiêu (Target Disagree) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_Debounce`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian lọc chống rung tiếp điểm đầu vào (Debounce Time, tính bằng giây) |
| **`Cfg_GateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng điều kiện (Gate Delay, tính bằng giây): Khoảng thời gian chân Inp_Gate phải duy trì ở mức 1 trước khi cho phép bật giám sát đầu vào |
| **`Cfg_TgtDisagreeOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho lệch trạng thái mục tiêu (Target Disagree): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_TgtDisagreeOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho lệch trạng thái mục tiêu (Target Disagree): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Set_SimPV`** | `BOOL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Giá trị PV giả lập (đơn vị kỹ thuật EU) được sử dụng khi bật chế độ Mô phỏng (Inp_Sim = 1) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`PCmd_TgtDisagreeAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lệch trạng thái mục tiêu (Target Disagree) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_TgtDisagreeSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lệch trạng thái mục tiêu (Target Disagree) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_TgtDisagreeUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lệch trạng thái mục tiêu (Target Disagree) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_TgtDisagreeUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lệch trạng thái mục tiêu (Target Disagree) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) ngay lập tức (biến xung tự động xóa về 0) |

## 3. Bảng chi tiết 34 Biến Đầu Ra (`Output Parameters`) của `P_DIn`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`SrcQ_IO`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng dữ liệu tổng hợp cuối cùng (0 = Tín hiệu thực Tốt đã xác nhận, 1 = Tín hiệu thực Tốt giả định, 2 = Không cấu hình phản hồi/Tốt giả định, 8 = Đang mô phỏng, 9 = Mô phỏng vòng lặp, 10 = Giá trị nhập tay, 16 = Ngoài đặc tính kỹ thuật, 17 = Thay thế tại thiết bị/bus, 18 = Thay thế bởi Bảo trì, 19 = Ngắt bảo vệ giữ giá trị cũ, 20 = Ngắt bảo vệ dùng giá trị thay thế, 32 = Lỗi tín hiệu vượt dải/NaN, 33 = Lỗi kênh I/O, 34 = Lỗi module I/O, 35 = Lỗi cấu hình) |
| **`Val_Sts`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái hoạt động xác nhận của thiết bị (Device Confirmed Status enum dùng để điều khiển màu sắc và biểu tượng động trên HMI) |
| **`Val_Fault`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái lỗi thiết bị (Device Fault Status — kiểu liệt kê enum: 0 = Không lỗi, 16 = Lỗi khởi động/mở, 17 = Lỗi dừng/đóng, 20 = Lo, 21 = Hi, 24 = LoLo, 25 = HiHi, 32 = Fail/I/O Fault, 34 = Lỗi cấu hình CfgErr) |
| **`Val_NotifyAll`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận tổng hợp của khối này cộng với các đối tượng kênh/quá tải liên kết |
| **`Val_UnackAlmC`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms Count) |
| **`Sts`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái đầu vào số sau khi đã lọc chống rung (Debounce) và chọn giá trị thay thế (Substitute PV) nếu dùng |
| **`Sts_PV`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái đầu vào số thực tế (đã lọc chống rung nhưng trước khi qua bộ chọn Substitute PV) |
| **`Sts_SubstPV`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang sử dụng giá trị PV thay thế (Substitute PV do nhân viên bảo trì nhập) thay cho tín hiệu cảm biến thật |
| **`Sts_InpPV`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang sử dụng tín hiệu PV thực tế từ cảm biến đầu vào (chế độ bình thường) |
| **`Sts_PVBad`** | `BOOL` | `??` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Chất lượng tín hiệu PV Xấu (Bad Quality) hoặc tín hiệu vượt ngoài dải lỗi (Out of Range) |
| **`Sts_MaintByp`** | `BOOL` | `??` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoặc cưỡng bức bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `??` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có ít nhất một cảnh báo bị Tạm hoãn (Shelved), Vô hiệu hóa (Disabled) hoặc Ức chế (Suppressed) — hiển thị biểu tượng chữ "I" trên HMI |
| **`Sts_Err`** | `BOOL` | `??` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_Timer`** | `BOOL` | `??` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị đặt bộ định thời gian (Timer Preset) nằm ngoài phạm vi hợp lệ 0..2.147.483 giây |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình cảnh báo: Thời gian duy trì tối thiểu (Min On Time), thời gian tạm hoãn (Shelf Time) hoặc mức độ nghiêm trọng (Severity 1..1000) không hợp lệ |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc chốt ngắt bảo vệ (Shed) đã hết lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_TgtDisagreeCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của lệch trạng thái mục tiêu (Target Disagree) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_TgtDisagreeGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của lệch trạng thái mục tiêu (Target Disagree) đã đếm xong, đang cho phép giám sát |
| **`Sts_TgtDisagree`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lệch trạng thái mục tiêu (Target Disagree) |
| **`Alm_TgtDisagree`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lệch trạng thái mục tiêu (Target Disagree) |
| **`Ack_TgtDisagree`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lệch trạng thái mục tiêu (Target Disagree) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_TgtDisagreeDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lệch trạng thái mục tiêu (Target Disagree) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_TgtDisagreeShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lệch trạng thái mục tiêu (Target Disagree) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_TgtDisagreeSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lệch trạng thái mục tiêu (Target Disagree) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi truyền thông thẻ I/O (I/O Fault) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi truyền thông thẻ I/O (I/O Fault) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`P_DIn`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_DIn) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 22 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_DIn`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_0StText`** | `STRING_8` | `'Off'` | [Biến cấu hình thông số] Chuỗi văn bản hiển thị trên HMI khi trạng thái bằng 0 (ví dụ: "Closed", "Stopped", "Normal") |
| **`Cfg_1StText`** | `STRING_8` | `'On'` | [Biến cấu hình thông số] Chuỗi văn bản hiển thị trên HMI khi trạng thái bằng 1 (ví dụ: "Open", "Running", "Tripped") |
| **`Cfg_Area`** | `STRING_8` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'Discrete Input'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_Label`** | `STRING_20` | `'Discrete Input'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_Tag`** | `STRING_20` | `'P_DIn'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_DIn'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`IOFault`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`MCmd_InpPV`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Sử dụng lại tín hiệu PV thực tế từ cảm biến đầu vào (ngưng dùng giá trị thay thế Substitute PV) |
| **`MCmd_SubstPV`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Chuyển sang sử dụng giá trị PV thay thế (Substitute PV) do nhân viên bảo trì nhập |
| **`MRdy_InpPV`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_InpPV (Sử dụng lại tín hiệu PV thực tế từ cảm biến đầu vào (ngưng dùng giá trị thay thế Substitute PV)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_SubstPV`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_SubstPV (Chuyển sang sử dụng giá trị PV thay thế (Substitute PV) do nhân viên bảo trì nhập), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MSet_SubstPV`** | `BOOL` | `0` | [Giá trị đặt từ Bảo trì HMI] Giá trị đặt từ Nhân viên bảo trì (Maintenance HMI): Giá trị PV thay thế (Substitute PV, đơn vị EU) do nhân viên bảo trì nhập thủ công để ghi đè tín hiệu cảm biến |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`TgtDisagree`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lệch trạng thái mục tiêu (Target Disagree) |
| **`TgtDisagreeGate`** | `P_Gate` | `{...}` | Khối con P_Gate nội bộ xử lý trễ cổng điều kiện cho cảnh báo Lệch trạng thái mục tiêu (Target Disagree) |
| **`Val_Notify`** | `SINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo hiện hành và trạng thái xác nhận của khối này (0 = Không có cảnh báo, 1 = Đã hết lỗi nhưng chờ Reset/Ack, 2..3 = Thấp, 4..5 = Trung bình, 6..7 = Cao, 8..9 = Khẩn cấp) |
---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_DInAdv`
**Thuộc chương:** [Chương 1: Xử lý tín hiệu Vào/Ra (I/O Processing)](../01_CHUONG_1_IO_PROCESSING.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `IO Processing 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 53–57)  
**Tệp định nghĩa khối lệnh:** `P_DInAdv_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_DInAdv`:** **62** Biến đầu vào (`Input Parameters`) | **45** Biến đầu ra (`Output Parameters`) | **40** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_DInAdv` trong Studio 5000
- **Chức năng cốt lõi:** Khối đầu vào số nâng cao. Tích hợp logic **Công tắc tốc độ quay (Zero-Speed / Underspeed Switch)** nhận phản hồi chạy từ động cơ (`Inp_MotorRun`) với thời gian trễ khởi động, và hỗ trợ **2 cảnh báo trạng thái bất thường độc lập (`Alm1`, `Alm2`)**.
- **Khi nào CẦN sử dụng:** Khi giám sát **cảm biến báo quay ở đuôi băng tải, gầu tải, quạt gió, máy khuấy**: nếu Contactor động cơ đã đóng (`Inp_MotorRun = 1`) mà sau thời gian khởi động trục bị động không quay (do đứt dây curoa hoặc trượt khớp nối), khối lập tức báo lỗi dừng máy.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Cho các công tắc hành trình / báo mức thông thường chỉ cần 1 cảnh báo ([`P_DIn.md`](./P_DIn.md)).

### Quy trình 4 bước đấu nối và cấu hình `P_DInAdv`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_Gate`, `Inp_IOFault`, `Inp_PV`, `Inp_PVNotify`, `Inp_Reset`, `Inp_RunFdbk`, `Inp_Sim`, `Inp_SpeedFdbk`, `Inp_Target`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_HasStdySSWR0`, `Cfg_Combine_Sts_RunDec`, `Cfg_Debounce`, `Cfg_DecPlcs`, `Cfg_GateDly`, `Cfg_HasSSW`, `Cfg_HasPulseSSW`, `Cfg_HasPulseSpdConv`, `Cfg_HasIOFaultAlm`, `Cfg_HasStdySSWR1`, `Cfg_HasTgtDisagreeWAlm`, `Cfg_HasVSDSpFbk`, `Cfg_HasTgtDisagreeAlm`, `Cfg_HasTgtDisWF`, `Cfg_HasMoreObj`, `Cfg_IOFaultResetReqd`, `Cfg_IOFaultAckReqd`, `Cfg_IOFaultSeverity`...
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_0StText`, `Cfg_1StText`, `Cfg_AccTime`, `Cfg_AcStText`, `Cfg_Area`, `Cfg_DcStText`, `Cfg_DecTime`, `Cfg_Desc`, `Cfg_Label`, `Cfg_PulseMin`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_DInAdv` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_DInAdv` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PCmd_IOFaultAck`, `PCmd_Reset`, `PCmd_IOFaultSuppress`, `PCmd_IOFaultUnshelve`, `PCmd_IOFaultUnsuppress`, `PCmd_TgtDisagreeAck`, `PCmd_TgtDisagreeSuppress`, `PCmd_TgtDisagreeUnshelve`, `PCmd_TgtDisagreeUnsuppress`, `PCmd_TgtDisagreeWAck`, `PCmd_TgtDisagreeWSuppress`, `PCmd_TgtDisagreeWUnsuppress`, `PCmd_TgtDisagreeWUnshelve`
   - **Cách sử dụng trong code PLC:** Gán giá trị đặt vào `PSet_*` (nếu có) hoặc kích xung 1 chu kỳ vào các bit lệnh `PCmd_* = 1` để xóa lỗi (`PCmd_Reset`), xác nhận cảnh báo (`PCmd_*Ack`) hoặc ức chế cảnh báo (`PCmd_*Suppress`). Khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi.
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Alm_IOFault`, `Alm_TgtDisagree`, `Alm_TgtDisagreeW`, `Sts_Acc`, `Sts_AlmInh`, `Sts_Dec`, `Sts_Err`, `Sts_InpPV`, `Sts_MaintByp`, `Sts_IOFault`, `Sts_IOFaultDisabled`, `Sts_IOFaultShelved`, `Sts_IOFaultSuppressed`, `Sts_PV`, `Sts_PVBad`, `Sts_RdyAck`, `Sts_RdyReset`, `Sts_Run`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 62 Biến Đầu Vào (`Input Parameters`) của `P_DInAdv`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Cfg_HasStdySSWR0`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cấu hình công tắc tốc độ kiểu mức 0 duy trì tương ứng trạng thái đang chạy |
| **`Cfg_Combine_Sts_RunDec`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Gộp cả trạng thái đang Chạy (Run) và đang Giảm tốc (Decelerate) thành trạng thái báo Chạy (Sts_Run = 1) |
| **`Cfg_Debounce`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian lọc chống rung tiếp điểm đầu vào (Debounce Time, tính bằng giây) |
| **`Cfg_DecPlcs`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho tốc độ/giá trị trên màn hình HMI (từ 0 đến 6 chữ số) |
| **`Cfg_GateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng điều kiện (Gate Delay, tính bằng giây): Khoảng thời gian chân Inp_Gate phải duy trì ở mức 1 trước khi cho phép bật giám sát đầu vào |
| **`Cfg_HasSSW`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Sử dụng khối P_DInAdv làm chức năng giám sát Công tắc tốc độ quay (Speed Switch) |
| **`Cfg_HasPulseSSW`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cấu hình công tắc tốc độ kiểu đếm chuỗi xung (Pulse Speed Switch) |
| **`Cfg_HasPulseSpdConv`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Bật chức năng quy đổi tần số xung công tắc tốc độ sang phần trăm tốc độ quay |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasStdySSWR1`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cấu hình công tắc tốc độ kiểu mức 1 duy trì tương ứng trạng thái đang chạy |
| **`Cfg_HasTgtDisagreeWAlm`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo cảnh báo sớm lệch trạng thái mục tiêu (Target Disagree Warning): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasVSDSpFbk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Công tắc tốc độ được kết hợp sử dụng cùng với tín hiệu tốc độ biến tần (VSD) |
| **`Cfg_HasTgtDisagreeAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lệch trạng thái mục tiêu (Target Disagree): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasTgtDisWF`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Bật đầy đủ cả hai cấp Cảnh báo sớm (Warning) và Báo lỗi (Failure) khi tín hiệu lệch khỏi trạng thái mục tiêu |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_Maintain_StsRun`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Duy trì cờ báo chạy Sts_Run trong suốt thời gian động cơ đang giảm tốc |
| **`Cfg_NormTextVis`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Luôn hiển thị chuỗi trạng thái trên biểu tượng đồ họa ngay cả khi ở trạng thái bình thường; 0 = Ẩn chữ khi bình thường |
| **`Cfg_NoSubstPV`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Khóa chức năng nhập giá trị thay thế (Substitute PV): 0 = Cho phép nhân viên bảo trì nhập giá trị PV thay thế; 1 = Cấm sử dụng tính năng Substitute PV |
| **`Cfg_SSW_Type`** | `SINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Kiểu giám sát Công tắc tốc độ (Speed Switch Type): 1 = Mức 0 duy trì là Chạy, 2 = Mức 1 duy trì là Chạy, 4 = Đếm xung quay (Pulse) |
| **`Cfg_TgtDisagreeAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lệch trạng thái mục tiêu (Target Disagree): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_TgtDisagreeOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho lệch trạng thái mục tiêu (Target Disagree): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_TgtDisagreeOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho lệch trạng thái mục tiêu (Target Disagree): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_TgtDisagreeResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lệch trạng thái mục tiêu (Target Disagree): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_TgtDisagreeSSWTgt`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Trạng thái mục tiêu đặt cho bộ giám sát lệch trạng thái của Công tắc tốc độ |
| **`Cfg_TgtDisagreeWOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho cảnh báo sớm lệch trạng thái mục tiêu (Target Disagree Warning): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_TgtDisagreeWOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho cảnh báo sớm lệch trạng thái mục tiêu (Target Disagree Warning): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_TgtDisagreeWAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo cảnh báo sớm lệch trạng thái mục tiêu (Target Disagree Warning): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_TgtDisagreeWResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo cảnh báo sớm lệch trạng thái mục tiêu (Target Disagree Warning): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_TgtDisagreeSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lệch trạng thái mục tiêu (Target Disagree) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_TgtDisagreeWSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo cảnh báo sớm lệch trạng thái mục tiêu (Target Disagree Warning) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_UseRunFdbkSpdSW`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Chỉ cập nhật trạng thái công tắc tốc độ khi có tín hiệu phản hồi động cơ đang chạy (Inp_RunFdbk = 1) |
| **`Cfg_VSD_FailTime`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ phát báo động lỗi (Failure Delay Time) khi kết hợp với biến tần (giây) |
| **`Cfg_VSD_LoLIM`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng tốc độ thấp của biến tần (VSD Speed Low Limit) để hiệu chỉnh hệ số cảnh báo/lỗi công tắc tốc độ |
| **`Cfg_VSD_WarnTime`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ phát cảnh báo sớm (Warning Delay Time) khi kết hợp với biến tần (giây) |
| **`Inp_Gate`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input): Khi = 1, cho phép giám sát tín hiệu đầu vào Inp (áp dụng On-Delay/Off-Delay sau thời gian Cfg_GateDly); khi = 0, khóa cổng và ép ngõ ra về 0 |
| **`Inp_IOFault`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi truyền thông I/O của thiết bị (0 = Tốt/OK, 1 = Lỗi truyền thông I/O Fault) |
| **`Inp_PV`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu biến quá trình đầu vào (Process Variable - PV) đọc từ cảm biến hoặc thẻ Analog/Discrete Input |
| **`Inp_PVNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã mức độ ưu tiên cảnh báo và trạng thái xác nhận (enum) của đối tượng PV liên kết |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Inp_RunFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tiếp điểm phụ phản hồi trạng thái Chạy từ động cơ/khởi động từ (1 = Động cơ đang chạy, 0 = Động cơ đã dừng) |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân kích hoạt chế độ Mô phỏng (Simulation): Khi = 1, khóa các ngõ ra vật lý ở mức an toàn và dùng giá trị/phản hồi giả lập nội bộ; khi = 0, vận hành thực tế bình thường |
| **`Inp_SpeedFdbk`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu phản hồi tốc độ thực tế từ biến tần (Speed Feedback, đơn vị thô hoặc 0..100%) |
| **`Inp_Target`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái mục tiêu mong muốn của tín hiệu đầu vào số (0 hoặc 1): Nếu trạng thái thực tế khác Inp_Target sẽ báo lệch mục tiêu (Target Disagree) |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_TgtDisagreeAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lệch trạng thái mục tiêu (Target Disagree) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_TgtDisagreeSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lệch trạng thái mục tiêu (Target Disagree) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_TgtDisagreeUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lệch trạng thái mục tiêu (Target Disagree) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_TgtDisagreeUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lệch trạng thái mục tiêu (Target Disagree) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_TgtDisagreeWAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo cảnh báo sớm lệch trạng thái mục tiêu (Target Disagree Warning) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_TgtDisagreeWSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo cảnh báo sớm lệch trạng thái mục tiêu (Target Disagree Warning) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_TgtDisagreeWUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo cảnh báo sớm lệch trạng thái mục tiêu (Target Disagree Warning) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_TgtDisagreeWUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo cảnh báo sớm lệch trạng thái mục tiêu (Target Disagree Warning) ngay lập tức (biến xung tự động xóa về 0) |
| **`Set_SimPV`** | `BOOL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Giá trị PV giả lập (đơn vị kỹ thuật EU) được sử dụng khi bật chế độ Mô phỏng (Inp_Sim = 1) |
| **`Set_SymbolVis`** | `BOOL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Cờ điều khiển hiển thị biểu tượng đồ họa trên HMI: 0 = Ẩn biểu tượng trừ khi có lỗi; 1 = Luôn hiển thị biểu tượng |

## 3. Bảng chi tiết 45 Biến Đầu Ra (`Output Parameters`) của `P_DInAdv`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi truyền thông thẻ I/O (I/O Fault) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Ack_TgtDisagree`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lệch trạng thái mục tiêu (Target Disagree) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Ack_TgtDisagreeW`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động cảnh báo sớm lệch trạng thái mục tiêu (Target Disagree Warning) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`Alm_TgtDisagree`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lệch trạng thái mục tiêu (Target Disagree) |
| **`Alm_TgtDisagreeW`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho cảnh báo sớm lệch trạng thái mục tiêu (Target Disagree Warning) |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình cảnh báo: Thời gian duy trì tối thiểu (Min On Time), thời gian tạm hoãn (Shelf Time) hoặc mức độ nghiêm trọng (Severity 1..1000) không hợp lệ |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị đặt bộ định thời gian (Timer Preset) nằm ngoài phạm vi hợp lệ 0..2.147.483 giây |
| **`P_DIn`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_DIn) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng dữ liệu tổng hợp cuối cùng (0 = Tín hiệu thực Tốt đã xác nhận, 1 = Tín hiệu thực Tốt giả định, 2 = Không cấu hình phản hồi/Tốt giả định, 8 = Đang mô phỏng, 9 = Mô phỏng vòng lặp, 10 = Giá trị nhập tay, 16 = Ngoài đặc tính kỹ thuật, 17 = Thay thế tại thiết bị/bus, 18 = Thay thế bởi Bảo trì, 19 = Ngắt bảo vệ giữ giá trị cũ, 20 = Ngắt bảo vệ dùng giá trị thay thế, 32 = Lỗi tín hiệu vượt dải/NaN, 33 = Lỗi kênh I/O, 34 = Lỗi module I/O, 35 = Lỗi cấu hình) |
| **`Sts`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái đầu vào số sau khi đã lọc chống rung (Debounce) và chọn giá trị thay thế (Substitute PV) nếu dùng |
| **`Sts_Acc`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang trong quá trình Tăng tốc (Accelerating) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có ít nhất một cảnh báo bị Tạm hoãn (Shelved), Vô hiệu hóa (Disabled) hoặc Ức chế (Suppressed) — hiển thị biểu tượng chữ "I" trên HMI |
| **`Sts_Dec`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang trong quá trình Giảm tốc (Decelerating) |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Sts_InpPV`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang sử dụng tín hiệu PV thực tế từ cảm biến đầu vào (chế độ bình thường) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoặc cưỡng bức bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi truyền thông thẻ I/O (I/O Fault) |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_PV`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái đầu vào số thực tế (đã lọc chống rung nhưng trước khi qua bộ chọn Substitute PV) |
| **`Sts_PVBad`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Chất lượng tín hiệu PV Xấu (Bad Quality) hoặc tín hiệu vượt ngoài dải lỗi (Out of Range) |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc chốt ngắt bảo vệ (Shed) đã hết lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_Run`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở trạng thái Chạy (Running) |
| **`Sts_SubstPV`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang sử dụng giá trị PV thay thế (Substitute PV do nhân viên bảo trì nhập) thay cho tín hiệu cảm biến thật |
| **`Sts_TgtDisagree`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lệch trạng thái mục tiêu (Target Disagree) |
| **`Sts_TgtDisagreeCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của lệch trạng thái mục tiêu (Target Disagree) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_TgtDisagreeWCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của cảnh báo sớm lệch trạng thái mục tiêu (Target Disagree Warning) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_TgtDisagreeDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lệch trạng thái mục tiêu (Target Disagree) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_TgtDisagreeGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của lệch trạng thái mục tiêu (Target Disagree) đã đếm xong, đang cho phép giám sát |
| **`Sts_TgtDisagreeWDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo cảnh báo sớm lệch trạng thái mục tiêu (Target Disagree Warning) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_TgtDisagreeWGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của cảnh báo sớm lệch trạng thái mục tiêu (Target Disagree Warning) đã đếm xong, đang cho phép giám sát |
| **`Sts_TgtDisagreeShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lệch trạng thái mục tiêu (Target Disagree) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_TgtDisagreeSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lệch trạng thái mục tiêu (Target Disagree) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_TgtDisagreeWSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo cảnh báo sớm lệch trạng thái mục tiêu (Target Disagree Warning) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_TgtDisagreeW`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện cảnh báo sớm lệch trạng thái mục tiêu (Target Disagree Warning) |
| **`Sts_TgtDisagreeWShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo cảnh báo sớm lệch trạng thái mục tiêu (Target Disagree Warning) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Val_Fault`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái lỗi thiết bị (Device Fault Status — kiểu liệt kê enum: 0 = Không lỗi, 16 = Lỗi khởi động/mở, 17 = Lỗi dừng/đóng, 20 = Lo, 21 = Hi, 24 = LoLo, 25 = HiHi, 32 = Fail/I/O Fault, 34 = Lỗi cấu hình CfgErr) |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận tổng hợp của khối này cộng với các đối tượng kênh/quá tải liên kết |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái hoạt động xác nhận của thiết bị (Device Confirmed Status enum dùng để điều khiển màu sắc và biểu tượng động trên HMI) |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms Count) |

## 4. Bảng chi tiết 40 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_DInAdv`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_0StText`** | `STRING_8` | `'Off'` | [Biến cấu hình thông số] Chuỗi văn bản hiển thị trên HMI khi trạng thái bằng 0 (ví dụ: "Closed", "Stopped", "Normal") |
| **`Cfg_1StText`** | `STRING_8` | `On'` | [Biến cấu hình thông số] Chuỗi văn bản hiển thị trên HMI khi trạng thái bằng 1 (ví dụ: "Open", "Running", "Tripped") |
| **`Cfg_AccTime`** | `DINT` | `5` | [Biến cấu hình thông số] Thời gian tăng tốc khởi động của động cơ trước khi bắt đầu kiểm tra công tắc tốc độ (giây) |
| **`Cfg_AcStText`** | `STRING_8` | `'Acc'` | [Biến cấu hình thông số] Chuỗi văn bản hiển thị trên HMI khi ở trạng thái Tăng tốc (Accelerate State) |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_DcStText`** | `STRING_8` | `'Decc'` | [Biến cấu hình thông số] Chuỗi văn bản hiển thị trên HMI khi ở trạng thái Giảm tốc (Decelerate State) |
| **`Cfg_DecTime`** | `DINT` | `10` | [Biến cấu hình thông số] Thời gian giảm tốc dừng máy của động cơ (giây) |
| **`Cfg_Desc`** | `STRING_40` | `'Discrete Input'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_Label`** | `STRING_20` | `'Discrete Input'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_PulseMin`** | `DINT` | `0` | [Biến cấu hình thông số] Số xung định mức trên mỗi phút (Pulses per Minute) tương ứng với 100% tốc độ quay |
| **`Cfg_RStText`** | `STRING_8` | `'Run'` | [Biến cấu hình thông số] Chuỗi văn bản hiển thị trên HMI khi ở trạng thái Chạy (Run State) |
| **`Cfg_SpeedFdbkEU`** | `STRING_8` | `'Hz'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của tốc độ phản hồi biến tần (ví dụ: "Hz", "RPM", "%") hiển thị trên HMI |
| **`Cfg_SymbolVis`** | `BOOL` | `0` | [Biến cấu hình thông số] Cấu hình hiển thị biểu tượng trên HMI: 1 = Luôn hiển thị biểu tượng; 0 = Chỉ hiển thị khi có lỗi/bất thường |
| **`Cfg_Tag`** | `STRING_20` | `'P_DInAdv'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`Err_EngTb1`** | `BOOL` | `0` | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Có lỗi nhập thông số trên Tab Kỹ thuật số 1 (Engineering Tab 1) của HMI Faceplate |
| **`Err_EngTb2`** | `BOOL` | `0` | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Có lỗi nhập thông số trên Tab Kỹ thuật số 2 (Engineering Tab 2) của HMI Faceplate |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_DInAdv'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`IOFault`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`MCmd_InpPV`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Sử dụng lại tín hiệu PV thực tế từ cảm biến đầu vào (ngưng dùng giá trị thay thế Substitute PV) |
| **`MCmd_SubstPV`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Chuyển sang sử dụng giá trị PV thay thế (Substitute PV) do nhân viên bảo trì nhập |
| **`MRdy_InpPV`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_InpPV (Sử dụng lại tín hiệu PV thực tế từ cảm biến đầu vào (ngưng dùng giá trị thay thế Substitute PV)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_SubstPV`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_SubstPV (Chuyển sang sử dụng giá trị PV thay thế (Substitute PV) do nhân viên bảo trì nhập), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MSet_SubstPV`** | `BOOL` | `0` | [Giá trị đặt từ Bảo trì HMI] Giá trị đặt từ Nhân viên bảo trì (Maintenance HMI): Giá trị PV thay thế (Substitute PV, đơn vị EU) do nhân viên bảo trì nhập thủ công để ghi đè tín hiệu cảm biến |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`PulseSpd_ONS1`** | `BOOL` | `0` | Bit nhớ tạo xung 1 chu kỳ (One-Shot) phục vụ tính toán tốc độ xung trong khối P_DInAdv |
| **`SSW_StaticGate`** | `P_Gate` | `{...}` | Khối con P_Gate nội bộ xử lý trễ cổng điều kiện tĩnh cho chức năng Công tắc tốc độ (Speed Switch) |
| **`Sts_Equip_Start`** | `BOOL` | `0` | [Cờ báo trạng thái logic (Status)] Trạng thái báo thiết bị truyền động đang bắt đầu chu trình khởi động |
| **`TgtDisagree`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lệch trạng thái mục tiêu (Target Disagree) |
| **`TgtDisagreeGate`** | `P_Gate` | `{...}` | Khối con P_Gate nội bộ xử lý trễ cổng điều kiện cho cảnh báo Lệch trạng thái mục tiêu (Target Disagree) |
| **`TgtDisagreeW`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho cảnh báo sớm lệch trạng thái mục tiêu (Target Disagree Warning) |
| **`TgtDisagreeWarnGate`** | `P_Gate` | `{...}` | Khối con P_Gate nội bộ xử lý trễ cổng điều kiện cho cảnh báo sớm Lệch trạng thái mục tiêu (Target Disagree Warning) |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo hiện hành và trạng thái xác nhận của khối này (0 = Không có cảnh báo, 1 = Đã hết lỗi nhưng chờ Reset/Ack, 2..3 = Thấp, 4..5 = Trung bình, 6..7 = Cao, 8..9 = Khẩn cấp) |
| **`Val_PlsSpdPerc`** | `REAL` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Tốc độ quay tính toán từ tần số xung công tắc tốc độ theo phần trăm (%) |
| **`VSD_Sim`** | `DINT` | `0` | Biến nội bộ lưu giá trị tốc độ biến tần giả lập trong khối P_DInAdv |
| **`VSD_Spd_Cmd`** | `DINT` | `0` | Biến nội bộ lưu giá trị tốc độ đặt biến tần từ HMI trong khối P_DInAdv |
---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_DOut`
**Thuộc chương:** [Chương 1: Xử lý tín hiệu Vào/Ra (I/O Processing)](../01_CHUONG_1_IO_PROCESSING.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `IO Processing 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 58–62)  
**Tệp định nghĩa khối lệnh:** `P_DOut_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_DOut`:** **95** Biến đầu vào (`Input Parameters`) | **78** Biến đầu ra (`Output Parameters`) | **33** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_DOut` trong Studio 5000
- **Chức năng cốt lõi:** Khối điều khiển 1 thiết bị đầu ra số (`Out`). Hỗ trợ 3 chế độ xuất tín hiệu: **Giữ liên tục (Steady On/Off)**, **Phát 1 xung đơn có độ rộng định trước (Single Pulse)**, hoặc **Phát chuỗi xung lặp lại liên tục (Continually Pulsed / PWM chậm)**, có giám sát tiếp điểm phản hồi (`Inp_Fdbk`) và khóa liên động.
- **Khi nào CẦN sử dụng:** Khi điều khiển **còi/đèn báo hiệu, bộ sấy điện trở (Heater), cuộn hút phanh từ, búa rung gõ thành сило (chế độ xung lặp lại), hoặc van xả nước ngưng tự động** cần giao diện vận hành Manual/Auto đầy đủ trên HMI.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Không dùng để điều khiển Động cơ (dùng [`P_Motor.md`](./P_Motor.md)) hoặc Van điện từ có 2 công tắc hành trình Mở/Đóng (dùng [`P_ValveSO.md`](./P_ValveSO.md)).

### Quy trình 4 bước đấu nối và cấu hình `P_DOut`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_OnFdbk`, `Inp_OffFdbk`, `Inp_PermOK`, `Inp_NBPermOK`, `Inp_IntlkOK`, `Inp_NBIntlkOK`, `Inp_IOFault`, `Inp_Sim`, `Inp_Hand`, `Inp_Ovrd`, `Inp_OvrdCmd`, `Inp_ExtInh`, `Inp_HornInh`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_HasPulse`, `Cfg_CompletePulse`, `Cfg_HasOnFdbk`, `Cfg_HasOffFdbk`, `Cfg_UseOnFdbk`, `Cfg_UseOffFdbk`, `Cfg_FdbkFail`, `Cfg_HasPermObj`, `Cfg_HasIntlkObj`, `Cfg_HornOnChange`, `Cfg_OperOffPrio`, `Cfg_ExtOffPrio`, `Cfg_OCmdResets`, `Cfg_XCmdResets`, `Cfg_OvrdPermIntlk`, `Cfg_ShedOnFail`, `Cfg_ShedOnIOFault`, `Cfg_HasOnFailAlm`...
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_0StText`, `Cfg_1StText`, `Cfg_Area`, `Cfg_Desc`, `Cfg_Label`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_DOut` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_DOut` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PSet_Owner`, `PCmd_On`, `PCmd_Off`, `PCmd_OnPulse`, `PCmd_OffPulse`, `PCmd_ContPulse`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_OnFailAck`, `PCmd_OffFailAck`, `PCmd_IntlkTripAck`, `PCmd_IOFaultAck`, `PCmd_OnFailSuppress`, `PCmd_OnFailUnsuppress`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh điều khiển tự động, kiểm tra khối đã sẵn sàng ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển quyền về Program, sau đó gán giá trị vào `PSet_*` và kích xung `PCmd_* = 1` (khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Out_Horn`, `Val_Cmd`, `Val_Fdbk`, `Val_Sts`, `Val_Fault`, `Val_State`, `Val_NotifyAll`, `Val_UnackAlmC`, `Val_Owner`, `Sts_Out`, `Sts_Pulsing`, `Sts_Horn`, `Sts_FdbkOff`, `Sts_FdbkOn`, `Sts_FdbkFail`, `Sts_Available`, `Sts_Bypass`, `Sts_BypActive`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 95 Biến Đầu Vào (`Input Parameters`) của `P_DOut`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp_OnFdbk`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu phản hồi xác nhận thiết bị đã Bật (1 = Xác nhận thiết bị đang ON) |
| **`Inp_OffFdbk`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu phản hồi xác nhận thiết bị đã Tắt (1 = Xác nhận thiết bị đang OFF) |
| **`Inp_PermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp điều kiện Cho phép khởi động có thể Bypass (1 = Tất cả điều kiện Perm OK, cho phép khởi động; thường nối từ P_Perm.Sts_PermOK) |
| **`Inp_NBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp điều kiện Cho phép khởi động KHÔNG thể Bypass (1 = Tất cả điều kiện Non-Bypassable Perm OK; thường nối từ P_Perm.Sts_NBPermOK) |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp Khóa liên động an toàn có thể Bypass (1 = Liên động OK cho phép chạy; 0 = Cắt dừng thiết bị ngay lập tức; nối từ P_Intlk.Sts_IntlkOK) |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp Khóa liên động an toàn KHÔNG thể Bypass (1 = Liên động bất khả kháng OK; 0 = Cắt dừng thiết bị ngay lập tức; nối từ P_Intlk.Sts_NBIntlkOK) |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi truyền thông I/O của thiết bị (0 = Tốt/OK, 1 = Lỗi truyền thông I/O Fault) |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân kích hoạt chế độ Mô phỏng (Simulation): Khi = 1, khóa các ngõ ra vật lý ở mức an toàn và dùng giá trị/phản hồi giả lập nội bộ; khi = 0, vận hành thực tế bình thường |
| **`Inp_Hand`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu chọn chế độ Điều khiển bằng tay tại chỗ (Hand/Local): 1 = Chiếm quyền sang chế độ Hand (thường nối từ công tắc cứng tại tủ MCC); 0 = Nhả chế độ Hand |
| **`Inp_Ovrd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu kích hoạt chế độ Cưỡng bức ưu tiên (Override): 1 = Chiếm quyền sang chế độ Override; 0 = Nhả chế độ Override |
| **`Inp_OvrdCmd`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã lệnh điều khiển khi ở chế độ Cưỡng bức (Override): 0 = Không tác động, 1 = Tắt (Off), 2 = Bật (On), 3 = Xung đơn Tắt (Pulse Off), 4 = Xung đơn Bật (Pulse On), 5 = Băm xung liên tục (Pulse Continuously) |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân khóa cấm nguồn lệnh ngoài (External Inhibit): 1 = Cấm chế độ External chiếm quyền điều khiển; 0 = Cho phép External chiếm quyền |
| **`Inp_HornInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân khóa cấm còi báo động tại hiện trường: 1 = Cấm phát còi báo (Inhibit Horn); 0 = Cho phép phát còi báo trước khi khởi động |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Cfg_HasPulse`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép sử dụng các chế độ phát xung đơn và chuỗi xung lặp lại (Pulse ON/OFF/Continuous); 0 = Chỉ dùng chế độ Bật/Tắt duy trì (Steady ON/OFF) |
| **`Cfg_CompletePulse`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Luôn hoàn thành nốt chu kỳ xung đang phát dở khi nhận lệnh chuyển sang ON/OFF; 0 = Chuyển ngay lập tức sang trạng thái ON/OFF mới |
| **`Cfg_HasOnFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Thiết bị có đấu nối tiếp điểm phản hồi trạng thái BẬT (Inp_OnFdbk) |
| **`Cfg_HasOffFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Thiết bị có đấu nối tiếp điểm phản hồi trạng thái TẮT (Inp_OffFdbk) |
| **`Cfg_UseOnFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Sử dụng tín hiệu phản hồi BẬT (Inp_OnFdbk) để kiểm tra lỗi không bật được (OnFail); 0 = Tạm bỏ qua (Bypass) không kiểm tra |
| **`Cfg_UseOffFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Sử dụng tín hiệu phản hồi TẮT (Inp_OffFdbk) để kiểm tra lỗi không tắt được (OffFail); 0 = Tạm bỏ qua (Bypass) không kiểm tra |
| **`Cfg_FdbkFail`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Quy định tổ hợp lỗi chập tiếp điểm phản hồi: 1 = Báo lỗi khi cả 2 phản hồi cùng ON; 0 = Báo lỗi khi cả 2 phản hồi cùng OFF |
| **`Cfg_HasPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Điều kiện cho phép (P_Perm) nối vào chân Inp_PermOK và bật nút điều hướng mở Faceplate P_Perm |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Khóa liên động (P_Intlk) nối vào chân Inp_IntlkOK và bật nút điều hướng mở Faceplate P_Intlk |
| **`Cfg_HornOnChange`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 0 = Chỉ hú còi báo động khi bắt đầu cấp điện khởi động; 1 = Hú còi báo động trước mọi lần chuyển đổi trạng thái/vị trí |
| **`Cfg_OperOffPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Nút nhấn Tắt (OCmd_Off) trên HMI có quyền ưu tiên tắt được ở mọi chế độ; 0 = Chỉ tắt được ở chế độ Operator/Maintenance |
| **`Cfg_ExtOffPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Lệnh Tắt ngoài (XCmd_Off) có quyền ưu tiên tắt được ở mọi chế độ; 0 = Chỉ có hiệu lực khi ở chế độ External |
| **`Cfg_OCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khi Người vận hành ra lệnh Chạy/Mở mới (OCmd_*), khối tự động xóa luôn các chốt lỗi cũ; 0 = Bắt buộc phải nhấn nút Reset trước khi ra lệnh mới |
| **`Cfg_XCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khi hệ thống ngoài gửi lệnh Chạy/Mở mới (XCmd_*), khối tự động xóa luôn các chốt lỗi cũ; 0 = Bắt buộc phải gửi xung XCmd_Reset trước |
| **`Cfg_OvrdPermIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Chế độ Cưỡng bức (Override) được quyền bỏ qua các điều kiện Cho phép và Liên động có thể bypass; 0 = Chế độ Override vẫn phải tuân thủ mọi điều kiện |
| **`Cfg_ShedOnFail`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Đưa thiết bị về trạng thái Tắt/An toàn và phát báo động khi lỗi không đạt trạng thái mong muốn; 0 = Chỉ phát báo động |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt dừng thiết bị về trạng thái an toàn (Shed) và phát báo động khi lỗi truyền thông thẻ I/O; 0 = Chỉ phát báo động |
| **`Cfg_HasOnFailAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi không bật được thiết bị (Fail to Turn ON): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasOffFailAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi không tắt được thiết bị (Fail to Turn OFF): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_OnFailResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi không bật được thiết bị (Fail to Turn ON): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_OffFailResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi không tắt được thiết bị (Fail to Turn OFF): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_OnFailAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi không bật được thiết bị (Fail to Turn ON): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_OffFailAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi không tắt được thiết bị (Fail to Turn OFF): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_OnFailSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi không bật được thiết bị (Fail to Turn ON) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_OffFailSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi không tắt được thiết bị (Fail to Turn OFF) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_StartHornT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian hú còi cảnh báo tại hiện trường (Out_Horn) trước khi đóng điện khởi động thiết bị (giây; đặt = 0.0 để tắt còi) |
| **`Cfg_SimFdbkT`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ giả lập tín hiệu phản hồi chạy/đóng/mở trong chế độ Mô phỏng (Inp_Sim = 1, tính bằng giây) |
| **`Cfg_OnDelayT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ trước khi bắt đầu bật ngõ ra lên mức ON (giây) |
| **`Cfg_OffDelayT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ trước khi bắt đầu tắt ngõ ra về mức OFF (giây) |
| **`Cfg_OnPulseT`** | `REAL` | `0.5` | Ẩn (Tag) | [Biến cấu hình thông số] Độ rộng thời gian giữ mức BẬT (ON Time, tính bằng giây) khi phát xung đơn ON hoặc chuỗi xung liên tục |
| **`Cfg_OffPulseT`** | `REAL` | `0.5` | Ẩn (Tag) | [Biến cấu hình thông số] Độ rộng thời gian giữ mức TẮT (OFF Time, tính bằng giây) khi phát xung đơn OFF hoặc chuỗi xung liên tục |
| **`Cfg_OnFailT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian chờ tối đa (giây) từ khi xuất lệnh BẬT đến khi phải nhận được phản hồi Inp_OnFdbk = 1 trước khi báo lỗi OnFail |
| **`Cfg_OffFailT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian chờ tối đa (giây) từ khi xuất lệnh TẮT đến khi phải nhận được phản hồi TẮT trước khi báo lỗi OffFail |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Mã định danh Chủ sở hữu (Owner ID: ghi khác 0 để đăng ký chiếm quyền điều khiển, ghi 0 để nhả quyền) |
| **`PCmd_On`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Bật thiết bị lên mức ON duy trì (Steady ON) |
| **`PCmd_Off`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Tắt thiết bị về mức OFF duy trì (Steady OFF) |
| **`PCmd_OnPulse`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Phát 1 xung đơn Bật (Single Pulse ON) khi thiết bị đang OFF |
| **`PCmd_OffPulse`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Phát 1 xung đơn Tắt (Single Pulse OFF) khi thiết bị đang ON |
| **`PCmd_ContPulse`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Phát chuỗi xung Bật/Tắt liên tục (Continuous Pulse / Blink) |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Chương trình tự động (từ Operator sang Program) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Người vận hành (từ Program sang Operator) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa giữ nguồn lệnh hiện tại (không cho phép nguồn lệnh kia giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Đưa nguồn lệnh về chế độ Bình thường mặc định (Normal: Program hoặc Operator theo Cfg_ProgNormal) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`PCmd_OnFailAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi không bật được thiết bị (Fail to Turn ON) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_OffFailAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi không tắt được thiết bị (Fail to Turn OFF) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_OnFailSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi không bật được thiết bị (Fail to Turn ON) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_OnFailUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi không bật được thiết bị (Fail to Turn ON) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_OffFailSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi không tắt được thiết bị (Fail to Turn OFF) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_OffFailUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi không tắt được thiết bị (Fail to Turn OFF) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_OnFailUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi không bật được thiết bị (Fail to Turn ON) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_OffFailUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi không tắt được thiết bị (Fail to Turn OFF) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) ngay lập tức (biến xung tự động xóa về 0) |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`XCmd_On`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Bật thiết bị lên mức ON duy trì (Steady ON) |
| **`XCmd_Off`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Tắt thiết bị về mức OFF duy trì (Steady OFF) |
| **`XCmd_OnPulse`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Phát 1 xung đơn Bật (Single Pulse ON) khi thiết bị đang OFF |
| **`XCmd_OffPulse`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Phát 1 xung đơn Tắt (Single Pulse OFF) khi thiết bị đang ON |
| **`XCmd_ContPulse`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Phát chuỗi xung Bật/Tắt liên tục (Continuous Pulse / Blink) |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Chiếm quyền điều khiển (Acquire) về chế độ tương ứng |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |

## 3. Bảng chi tiết 78 Biến Đầu Ra (`Output Parameters`) của `P_DOut`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Out`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra điều khiển thiết bị (hoặc kết quả cổng điều kiện sau thời gian trễ On-Delay / Off-Delay) |
| **`Out_Horn`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra bật còi cảnh báo âm thanh tại hiện trường trước khi khởi động hoặc chuyển trạng thái thiết bị (1 = Bật còi) |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng dữ liệu tổng hợp cuối cùng (0 = Tín hiệu thực Tốt đã xác nhận, 1 = Tín hiệu thực Tốt giả định, 2 = Không cấu hình phản hồi/Tốt giả định, 8 = Đang mô phỏng, 9 = Mô phỏng vòng lặp, 10 = Giá trị nhập tay, 16 = Ngoài đặc tính kỹ thuật, 17 = Thay thế tại thiết bị/bus, 18 = Thay thế bởi Bảo trì, 19 = Ngắt bảo vệ giữ giá trị cũ, 20 = Ngắt bảo vệ dùng giá trị thay thế, 32 = Lỗi tín hiệu vượt dải/NaN, 33 = Lỗi kênh I/O, 34 = Lỗi module I/O, 35 = Lỗi cấu hình) |
| **`Val_Cmd`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã lệnh thiết bị hiện hành: 0 = Không tác động, 1 = Tắt (Off), 2 = Bật (On), 3 = Xung đơn Tắt, 4 = Xung đơn Bật, 5 = Băm xung liên tục |
| **`Val_Fdbk`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã phản hồi thiết bị: 0 = Đang chuyển trạng thái (Transition), 1 = Đã Tắt (Off), 2 = Đã Bật (On), 3 = Lỗi phản hồi không hợp lệ (Invalid) |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái xác nhận của thiết bị: 0 = Đang Tắt (Off), 1 = Đang Bật (On), 2 = Đang phát xung Tắt, 3 = Đang phát xung Bật, 4 = Đang phát xung liên tục, 5 = Đang chuyển sang Tắt, 6 = Đang chuyển sang Bật |
| **`Val_Fault`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái lỗi thiết bị (Device Fault Status — kiểu liệt kê enum: 0 = Không lỗi, 16 = Lỗi khởi động/mở, 17 = Lỗi dừng/đóng, 20 = Lo, 21 = Hi, 24 = LoLo, 25 = HiHi, 32 = Fail/I/O Fault, 34 = Lỗi cấu hình CfgErr) |
| **`Val_State`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái máy trạng thái nội bộ (Internal Logic State) dùng để tạo hiệu ứng chuyển động sơ đồ trạng thái trên HMI Faceplate |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận tổng hợp của khối này cộng với các đối tượng kênh/quá tải liên kết |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms Count) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã định danh Chủ sở hữu hiện hành của đối tượng (Current Object Owner ID: 0 = Đang không bị chiếm quyền) |
| **`Sts_Out`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Ngõ ra đang BẬT (ON / Có điện); 0 = Ngõ ra đang TẮT (OFF / Mất điện) |
| **`Sts_Pulsing`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Ngõ ra đang trong chu trình phát xung (Single Pulse hoặc Continuous Pulse) |
| **`Sts_Horn`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Còi cảnh báo khởi động tại hiện trường đang kêu (Horn Active) |
| **`Sts_FdbkOff`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu phản hồi từ thiết bị xác nhận thiết bị đang ở trạng thái TẮT (OFF) |
| **`Sts_FdbkOn`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu phản hồi từ thiết bị xác nhận thiết bị đang ở trạng thái BẬT (ON) |
| **`Sts_FdbkFail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu phản hồi từ thiết bị rơi vào trạng thái không hợp lệ (chập cả hai tiếp điểm ON và OFF) |
| **`Sts_Available`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị sẵn sàng cho Chương trình tự động (Program) điều khiển (đang ở chế độ Program và không có lỗi/liên động ngắt) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass hiện đang được bỏ qua (Bypassed) |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Chế độ Bypass bảo trì đang kích hoạt (gửi sang khối P_Intlk / P_Perm để cho phép bỏ qua các điều kiện có quyền Bypass) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị chưa sẵn sàng hoạt động (kiểm tra các bit Nrdy_* chi tiết để biết nguyên nhân cụ thể) |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang có lỗi thiết bị ngắt bảo vệ (Shed — yêu cầu nhấn Reset) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Khóa liên động an toàn không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi truyền thông thẻ I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Nrdy_PrioOff`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng: Đang dính lệnh Tắt ưu tiên từ Operator/External (cần nhấn Reset) |
| **`Nrdy_Perm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Cho phép khởi động không thỏa mãn (Permissive Not OK) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoặc cưỡng bức bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có ít nhất một cảnh báo bị Tạm hoãn (Shelved), Vô hiệu hóa (Disabled) hoặc Ức chế (Suppressed) — hiển thị biểu tượng chữ "I" trên HMI |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị đặt bộ định thời gian (Timer Preset) nằm ngoài phạm vi hợp lệ 0..2.147.483 giây |
| **`Err_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thời gian trễ mô phỏng (Cfg_SimFdbkT / Cfg_SimRampT) nằm ngoài phạm vi 0..2.147.483 giây |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình cảnh báo: Thời gian duy trì tối thiểu (Min On Time), thời gian tạm hoãn (Shelf Time) hoặc mức độ nghiêm trọng (Severity 1..1000) không hợp lệ |
| **`Sts_Hand`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Điều khiển bằng tay tại chỗ (Hand — ưu tiên cao hơn OoS, Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_OoS`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Ngưng phục vụ (Out of Service — ưu tiên cao hơn Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_Maint`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Bảo trì (Maintenance — ưu tiên cao hơn Ovrd, Ext, Prog, Oper) |
| **`Sts_Ovrd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Cưỡng bức ưu tiên (Override — ưu tiên cao hơn Ext, Prog, Oper) |
| **`Sts_Ext`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Điều khiển ngoài (External — ưu tiên cao hơn Prog, Oper) |
| **`Sts_Prog`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Chương trình tự động (Program) |
| **`Sts_ProgLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Chương trình tự động và đã Khóa (Program Locked — không cho Operator lấy quyền) |
| **`Sts_Oper`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Người vận hành trên HMI (Operator) |
| **`Sts_OperLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Người vận hành và đã Khóa (Operator Locked — không cho Program lấy quyền) |
| **`Sts_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Nguồn lệnh hiện hành đang khớp với nguồn lệnh Bình thường mặc định (Program hoặc Operator theo Cfg_ProgNormal) |
| **`Sts_ExtReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Yêu cầu chuyển sang chế độ External đang bị khóa cấm (không thể chuyển sang External từ trạng thái hiện tại) |
| **`Sts_ProgReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Yêu cầu chuyển sang chế độ Program đang bị khóa cấm (không thể chuyển sang Program từ trạng thái hiện tại) |
| **`Sts_MAcqRcvd`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đã nhận được lệnh Chiếm quyền bảo trì (MCmd_Acq) trong chu kỳ quét hiện tại |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc chốt ngắt bảo vệ (Shed) đã hết lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_OnFail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi không bật được thiết bị (Fail to Turn ON) |
| **`Alm_OnFail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi không bật được thiết bị (Fail to Turn ON) |
| **`Ack_OnFail`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi không bật được thiết bị (Fail to Turn ON) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_OnFailDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không bật được thiết bị (Fail to Turn ON) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_OnFailShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không bật được thiết bị (Fail to Turn ON) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_OnFailSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không bật được thiết bị (Fail to Turn ON) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_OffFail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi không tắt được thiết bị (Fail to Turn OFF) |
| **`Alm_OffFail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi không tắt được thiết bị (Fail to Turn OFF) |
| **`Ack_OffFail`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi không tắt được thiết bị (Fail to Turn OFF) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_OffFailDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không tắt được thiết bị (Fail to Turn OFF) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_OffFailShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không tắt được thiết bị (Fail to Turn OFF) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_OffFailSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không tắt được thiết bị (Fail to Turn OFF) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`Alm_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`Ack_IntlkTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_IntlkTripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_IntlkTripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_IntlkTripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi truyền thông thẻ I/O (I/O Fault) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi truyền thông thẻ I/O (I/O Fault) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`MRdy_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`P_DOut`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_DOut) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 33 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_DOut`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_0StText`** | `STRING_8` | `'Off'` | [Biến cấu hình thông số] Chuỗi văn bản hiển thị trên HMI khi trạng thái bằng 0 (ví dụ: "Closed", "Stopped", "Normal") |
| **`Cfg_1StText`** | `STRING_8` | `'On'` | [Biến cấu hình thông số] Chuỗi văn bản hiển thị trên HMI khi trạng thái bằng 1 (ví dụ: "Open", "Running", "Tripped") |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'Discrete On/Off Device'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_Label`** | `STRING_20` | `'Discrete Device'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_Tag`** | `STRING_20` | `'P_DOut'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc nội bộ quản lý phân quyền và chuyển mạch giữa 7 nguồn lệnh điều khiển (OoS, Hand, Maint, Ovrd, Ext, Prog, Oper) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_DOut'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`IOFault`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bỏ qua (Bypass) tất cả các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Kiểm tra lại bình thường (Check / Gỡ bỏ Bypass) toàn bộ điều kiện Khóa liên động và Cho phép |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (Bỏ qua (Bypass) tất cả các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (Kiểm tra lại bình thường (Check / Gỡ bỏ Bypass) toàn bộ điều kiện Khóa liên động và Cho phép), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`OCmd_ContPulse`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Phát chuỗi xung Bật/Tắt liên tục (Continuous Pulse / Blink) |
| **`OCmd_Off`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Tắt thiết bị về mức OFF duy trì (Steady OFF) |
| **`OCmd_OffPulse`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Phát 1 xung đơn Tắt (Single Pulse OFF) khi thiết bị đang ON |
| **`OCmd_On`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Bật thiết bị lên mức ON duy trì (Steady ON) |
| **`OCmd_OnPulse`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Phát 1 xung đơn Bật (Single Pulse ON) khi thiết bị đang OFF |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo |
| **`OffFail`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi không tắt được thiết bị (Fail to Turn OFF) |
| **`OnFail`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi không bật được thiết bị (Fail to Turn ON) |
| **`ORdy_ContPulse`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ContPulse (Phát chuỗi xung Bật/Tắt liên tục (Continuous Pulse / Blink)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Off`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Off (Tắt thiết bị về mức OFF duy trì (Steady OFF)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_OffPulse`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_OffPulse (Phát 1 xung đơn Tắt (Single Pulse OFF) khi thiết bị đang ON), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_On`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_On (Bật thiết bị lên mức ON duy trì (Steady ON)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_OnPulse`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_OnPulse (Phát 1 xung đơn Bật (Single Pulse ON) khi thiết bị đang OFF), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo hiện hành và trạng thái xác nhận của khối này (0 = Không có cảnh báo, 1 = Đã hết lỗi nhưng chờ Reset/Ack, 2..3 = Thấp, 4..5 = Trung bình, 6..7 = Cao, 8..9 = Khẩn cấp) |
---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_AOut`
**Thuộc chương:** [Chương 1: Xử lý tín hiệu Vào/Ra (I/O Processing)](../01_CHUONG_1_IO_PROCESSING.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `IO Processing 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 63–67)  
**Tệp định nghĩa khối lệnh:** `P_AOut_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_AOut`:** **61** Biến đầu vào (`Input Parameters`) | **62** Biến đầu ra (`Output Parameters`) | **23** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_AOut` trong Studio 5000
- **Chức năng cốt lõi:** Khối điều khiển trực tiếp 1 kênh đầu ra tương tự (`Out_CV`), đóng vai trò như một **Trạm điều khiển bằng tay (Manual Loading Station)** trên HMI hoặc bộ đệm xuất tín hiệu 4–20mA từ chương trình, có kẹp giới hạn biên độ (`Cfg_MinCV/MaxCV`), giới hạn tốc độ tăng/giảm (`RoC`) và đưa về giá trị an toàn khi mất liên động.
- **Khi nào CẦN sử dụng:** Khi cần điều khiển độ mở/tốc độ của một **cơ cấu chấp hành tuyến tính (bộ chỉnh lưu SCR gia nhiệt, van tiết lưu không có công tắc hành trình, bộ điều tốc cơ khí)** trực tiếp bằng thanh trượt phần trăm trên HMI hoặc từ công thức tính toán trong PLC.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi điều khiển van tuyến tính có đầy đủ phản hồi vị trí và công tắc hành trình Mở/Đóng → Dùng [`P_ValveC.md`](./P_ValveC.md).

### Quy trình 4 bước đấu nối và cấu hình `P_AOut`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_Tieback`, `Inp_IntlkOK`, `Inp_NBIntlkOK`, `Inp_IOFault`, `Inp_Sim`, `Inp_Hand`, `Inp_Ovrd`, `Inp_OvrdCV`, `Inp_ExtInh`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_ShedHold`, `Cfg_SkipRoCLim`, `Cfg_SetTrack`, `Cfg_SetTrackOvrdHand`, `Cfg_HasIntlkObj`, `Cfg_HasCVNav`, `Cfg_OvrdIntlk`, `Cfg_ShedOnIOFault`, `Cfg_DecPlcs`, `Cfg_HasIntlkTripAlm`, `Cfg_HasIOFaultAlm`, `Cfg_HasMoreObj`, `Cfg_IntlkTripResetReqd`, `Cfg_IOFaultResetReqd`, `Cfg_IntlkTripAckReqd`, `Cfg_IOFaultAckReqd`, `Cfg_IntlkTripSeverity`, `Cfg_IOFaultSeverity`...
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_CVNavTag`, `Cfg_Desc`, `Cfg_EU`, `Cfg_Label`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_AOut` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_AOut` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PSet_CV`, `PSet_Owner`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_IntlkTripAck`, `PCmd_IOFaultAck`, `PCmd_IntlkTripSuppress`, `PCmd_IntlkTripUnsuppress`, `PCmd_IOFaultSuppress`, `PCmd_IOFaultUnsuppress`, `PCmd_IntlkTripUnshelve`, `PCmd_IOFaultUnshelve`
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh điều khiển tự động, kiểm tra khối đã sẵn sàng ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển quyền về Program, sau đó gán giá trị vào `PSet_*` và kích xung `PCmd_* = 1` (khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Out_CV`, `Val_CVSet`, `Val_CVOut`, `Val_CVEUMin`, `Val_CVEUMax`, `Val_Sts`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Val_Owner`, `Sts_Ramping`, `Sts_Clamped`, `Sts_SkipRoCLim`, `Sts_Active`, `Sts_Available`, `Sts_Bypass`, `Sts_BypActive`, `Sts_NotRdy`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 61 Biến Đầu Vào (`Input Parameters`) của `P_AOut`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp_Tieback`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu phản hồi vị trí thực tế (Tieback CV, đơn vị thô của Out_CV) dùng để bám giá trị trong chế độ Hand hoặc khởi tạo |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp Khóa liên động an toàn có thể Bypass (1 = Liên động OK cho phép chạy; 0 = Cắt dừng thiết bị ngay lập tức; nối từ P_Intlk.Sts_IntlkOK) |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp Khóa liên động an toàn KHÔNG thể Bypass (1 = Liên động bất khả kháng OK; 0 = Cắt dừng thiết bị ngay lập tức; nối từ P_Intlk.Sts_NBIntlkOK) |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi truyền thông I/O của thiết bị (0 = Tốt/OK, 1 = Lỗi truyền thông I/O Fault) |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân kích hoạt chế độ Mô phỏng (Simulation): Khi = 1, khóa các ngõ ra vật lý ở mức an toàn và dùng giá trị/phản hồi giả lập nội bộ; khi = 0, vận hành thực tế bình thường |
| **`Inp_Hand`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu chọn chế độ Điều khiển bằng tay tại chỗ (Hand/Local): 1 = Chiếm quyền sang chế độ Hand (thường nối từ công tắc cứng tại tủ MCC); 0 = Nhả chế độ Hand |
| **`Inp_Ovrd`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu kích hoạt chế độ Cưỡng bức ưu tiên (Override): 1 = Chiếm quyền sang chế độ Override; 0 = Nhả chế độ Override |
| **`Inp_OvrdCV`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Giá trị đặt biến điều khiển CV khi ở chế độ Cưỡng bức (Override CV Target, đơn vị kỹ thuật EU) |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân khóa cấm nguồn lệnh ngoài (External Inhibit): 1 = Cấm chế độ External chiếm quyền điều khiển; 0 = Cho phép External chiếm quyền |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Cfg_ShedHold`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Hành động xử lý ngõ ra CV khi vi phạm Khóa liên động (Interlock Trip): 1 = Giữ nguyên giá trị CV hiện tại (Hold Last State); 0 = Đưa ngõ ra CV về giá trị an toàn định trước (Cfg_IntlkCV) |
| **`Cfg_SkipRoCLim`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Bỏ qua bộ giới hạn tốc độ thay đổi CV (Rate-of-Change Limiting) khi ở chế độ Bảo trì, Cưỡng bức (Override) hoặc khi ngắt Liên động |
| **`Cfg_SetTrack`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép các giá trị đặt (PSet_CV / OSet_CV / SP) tự động bám theo ngõ ra thực tế hiện hành để chuyển mạch không giật (Bumpless Transfer) giữa Operator và Program |
| **`Cfg_SetTrackOvrdHand`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép giá trị đặt của Program và Operator tự động bám theo giá trị CV khi đang ở chế độ Override hoặc Hand |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Khóa liên động (P_Intlk) nối vào chân Inp_IntlkOK và bật nút điều hướng mở Faceplate P_Intlk |
| **`Cfg_HasCVNav`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép hiển thị nút điều hướng nhảy tới Faceplate của khối cơ cấu chấp hành CV trên HMI |
| **`Cfg_OvrdIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Chế độ Override được quyền bỏ qua các điều kiện Khóa liên động (Interlocks) có thể bypass; 0 = Luôn kiểm tra Interlocks |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt dừng thiết bị về trạng thái an toàn (Shed) và phát báo động khi lỗi truyền thông thẻ I/O; 0 = Chỉ phát báo động |
| **`Cfg_DecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho tốc độ/giá trị trên màn hình HMI (từ 0 đến 6 chữ số) |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_MinCV`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp dưới tối thiểu cho phép của ngõ ra điều khiển CV (Minimum CV Limit, đơn vị kỹ thuật EU) |
| **`Cfg_MaxCV`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp trên tối đa cho phép của ngõ ra điều khiển CV (Maximum CV Limit, đơn vị kỹ thuật EU) |
| **`Cfg_CVRoCLimInc`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn tốc độ tăng tối đa của ngõ ra CV (Increasing Rate-of-Change Limit, đơn vị EU/giây; đặt = 0.0 để tăng tức thời không giới hạn) |
| **`Cfg_CVRoCLimDec`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn tốc độ giảm tối đa của ngõ ra CV (Decreasing Rate-of-Change Limit, đơn vị EU/giây; đặt = 0.0 để giảm tức thời không giới hạn) |
| **`Cfg_IntlkCV`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị ngõ ra CV an toàn (đơn vị kỹ thuật EU) được xuất ra khi vi phạm Khóa liên động (nếu Cfg_ShedHold = 0) |
| **`Cfg_CVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật đầu ra CV tương ứng với Cfg_CVRawMin (thường là 0.0%) |
| **`Cfg_CVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật đầu ra CV tương ứng với Cfg_CVRawMax (thường là 100.0%) |
| **`Cfg_CVRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị thô đầu ra tối thiểu (CV Raw Min) gửi ra thẻ Analog Output tương ứng với Cfg_CVEUMin |
| **`Cfg_CVRawMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị thô đầu ra tối đa (CV Raw Max) gửi ra thẻ Analog Output tương ứng với Cfg_CVEUMax |
| **`Cfg_MaxInactiveCV`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng CV tối thiểu (đơn vị CVEU) để bật cờ báo thiết bị đang tích cực hoạt động (Sts_Active = 1 trên HMI) |
| **`PSet_CV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Giá trị biến điều khiển đầu ra (Controlled Variable - CV, tính theo đơn vị kỹ thuật EU) |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Mã định danh Chủ sở hữu (Owner ID: ghi khác 0 để đăng ký chiếm quyền điều khiển, ghi 0 để nhả quyền) |
| **`XSet_CV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài] Giá trị đặt từ Hệ thống điều khiển ngoài (External): Giá trị biến điều khiển đầu ra (Controlled Variable - CV, tính theo đơn vị kỹ thuật EU) |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Chương trình tự động (từ Operator sang Program) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Người vận hành (từ Program sang Operator) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa giữ nguồn lệnh hiện tại (không cho phép nguồn lệnh kia giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Đưa nguồn lệnh về chế độ Bình thường mặc định (Normal: Program hoặc Operator theo Cfg_ProgNormal) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) ngay lập tức (biến xung tự động xóa về 0) |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Chiếm quyền điều khiển (Acquire) về chế độ tương ứng |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |

## 3. Bảng chi tiết 62 Biến Đầu Ra (`Output Parameters`) của `P_AOut`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Out_CV`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị ngõ ra điều khiển tương tự (Controlled Variable - CV) gửi tới cơ cấu chấp hành / thẻ Analog Output (đơn vị Raw hoặc EU) |
| **`Val_CVSet`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đặt CV đang được chọn (trước khi qua bộ giới hạn tốc độ thay đổi RoC, đơn vị EU) |
| **`Val_CVOut`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị biến điều khiển CV xuất ra thực tế (sau khi qua bộ giới hạn tốc độ thay đổi RoC, đơn vị EU) |
| **`Val_CVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận dưới của dải đơn vị kỹ thuật ngõ ra CV = MIN(Cfg_CVEUMin, Cfg_CVEUMax) |
| **`Val_CVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận trên của dải đơn vị kỹ thuật ngõ ra CV = MAX(Cfg_CVEUMin, Cfg_CVEUMax) |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng dữ liệu tổng hợp cuối cùng (0 = Tín hiệu thực Tốt đã xác nhận, 1 = Tín hiệu thực Tốt giả định, 2 = Không cấu hình phản hồi/Tốt giả định, 8 = Đang mô phỏng, 9 = Mô phỏng vòng lặp, 10 = Giá trị nhập tay, 16 = Ngoài đặc tính kỹ thuật, 17 = Thay thế tại thiết bị/bus, 18 = Thay thế bởi Bảo trì, 19 = Ngắt bảo vệ giữ giá trị cũ, 20 = Ngắt bảo vệ dùng giá trị thay thế, 32 = Lỗi tín hiệu vượt dải/NaN, 33 = Lỗi kênh I/O, 34 = Lỗi module I/O, 35 = Lỗi cấu hình) |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái đầu ra tương tự: 0 = Đã đạt giá trị đích (At Target), 1 = Đang giảm theo độ dốc (Ramp Down), 2 = Đang tăng theo độ dốc (Ramp Up), 3 = Đang chạm kẹp giới hạn dưới (Clamp Min), 4 = Đang chạm kẹp giới hạn trên (Clamp Max), 33 = Ngưng phục vụ (Disabled) |
| **`Val_Fault`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái lỗi thiết bị (Device Fault Status — kiểu liệt kê enum: 0 = Không lỗi, 16 = Lỗi khởi động/mở, 17 = Lỗi dừng/đóng, 20 = Lo, 21 = Hi, 24 = LoLo, 25 = HiHi, 32 = Fail/I/O Fault, 34 = Lỗi cấu hình CfgErr) |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận tổng hợp của khối này cộng với các đối tượng kênh/quá tải liên kết |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms Count) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã định danh Chủ sở hữu hiện hành của đối tượng (Current Object Owner ID: 0 = Đang không bị chiếm quyền) |
| **`Sts_Ramping`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị ngõ ra CV đang trong quá trình thay đổi theo độ dốc (Ramping) tiến về giá trị đặt |
| **`Sts_Clamped`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị PV tương tự đang bị kẹp ở ngưỡng giới hạn dưới (Cfg_PVLoLim) hoặc giới hạn trên (Cfg_PVHiLim) |
| **`Sts_SkipRoCLim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang bỏ qua bộ giới hạn tốc độ thay đổi CV trong chu kỳ này (do chuyển sang Maint, Ovrd, Intlk hoặc Hand) |
| **`Sts_Active`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị ngõ ra CV lớn hơn ngưỡng kích hoạt (Cfg_MaxInactiveCV), hiển thị biểu tượng hoạt động trên HMI |
| **`Sts_Available`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị sẵn sàng cho Chương trình tự động (Program) điều khiển (đang ở chế độ Program và không có lỗi/liên động ngắt) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass hiện đang được bỏ qua (Bypassed) |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Chế độ Bypass bảo trì đang kích hoạt (gửi sang khối P_Intlk / P_Perm để cho phép bỏ qua các điều kiện có quyền Bypass) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị chưa sẵn sàng hoạt động (kiểm tra các bit Nrdy_* chi tiết để biết nguyên nhân cụ thể) |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Khóa liên động an toàn không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi truyền thông thẻ I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoặc cưỡng bức bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có ít nhất một cảnh báo bị Tạm hoãn (Shelved), Vô hiệu hóa (Disabled) hoặc Ức chế (Suppressed) — hiển thị biểu tượng chữ "I" trên HMI |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Ngưỡng kẹp giới hạn trên của PV nhỏ hơn ngưỡng kẹp giới hạn dưới (Cfg_PVHiLim < Cfg_PVLoLim) |
| **`Err_EU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải quy đổi đơn vị kỹ thuật (Engineering Units) có giá trị Tối thiểu bằng Tối đa (Min = Max) |
| **`Err_Raw`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải giá trị thô đầu vào (Cfg_InpRawMin = Cfg_InpRawMax) bị trùng nhau |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình cảnh báo: Thời gian duy trì tối thiểu (Min On Time), thời gian tạm hoãn (Shelf Time) hoặc mức độ nghiêm trọng (Severity 1..1000) không hợp lệ |
| **`Sts_Hand`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Điều khiển bằng tay tại chỗ (Hand — ưu tiên cao hơn OoS, Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_OoS`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Ngưng phục vụ (Out of Service — ưu tiên cao hơn Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_Maint`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Bảo trì (Maintenance — ưu tiên cao hơn Ovrd, Ext, Prog, Oper) |
| **`Sts_Ovrd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Cưỡng bức ưu tiên (Override — ưu tiên cao hơn Ext, Prog, Oper) |
| **`Sts_Ext`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Điều khiển ngoài (External — ưu tiên cao hơn Prog, Oper) |
| **`Sts_Prog`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Chương trình tự động (Program) |
| **`Sts_ProgLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Chương trình tự động và đã Khóa (Program Locked — không cho Operator lấy quyền) |
| **`Sts_Oper`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Người vận hành trên HMI (Operator) |
| **`Sts_OperLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Người vận hành và đã Khóa (Operator Locked — không cho Program lấy quyền) |
| **`Sts_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Nguồn lệnh hiện hành đang khớp với nguồn lệnh Bình thường mặc định (Program hoặc Operator theo Cfg_ProgNormal) |
| **`Sts_ExtReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Yêu cầu chuyển sang chế độ External đang bị khóa cấm (không thể chuyển sang External từ trạng thái hiện tại) |
| **`Sts_ProgReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Yêu cầu chuyển sang chế độ Program đang bị khóa cấm (không thể chuyển sang Program từ trạng thái hiện tại) |
| **`Sts_MAcqRcvd`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đã nhận được lệnh Chiếm quyền bảo trì (MCmd_Acq) trong chu kỳ quét hiện tại |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc chốt ngắt bảo vệ (Shed) đã hết lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`Alm_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`Ack_IntlkTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_IntlkTripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_IntlkTripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_IntlkTripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi truyền thông thẻ I/O (I/O Fault) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi truyền thông thẻ I/O (I/O Fault) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`MRdy_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`P_AOut`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_AOut) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 23 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_AOut`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_CVNavTag`** | `STRING_NavTag` | `''` | [Biến cấu hình thông số] Tên thẻ (Tagname) của khối cơ cấu chấp hành CV đích khi nhấn nút điều hướng CV trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'Analog Output'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật (Engineering Units, ví dụ: "°C", "bar", "m3/h", "%") hiển thị trên màn hình HMI |
| **`Cfg_Label`** | `STRING_20` | `'Analog Output'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_Tag`** | `STRING_20` | `'P_AOut'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc nội bộ quản lý phân quyền và chuyển mạch giữa 7 nguồn lệnh điều khiển (OoS, Hand, Maint, Ovrd, Ext, Prog, Oper) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_AOut'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`IOFault`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bỏ qua (Bypass) tất cả các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Kiểm tra lại bình thường (Check / Gỡ bỏ Bypass) toàn bộ điều kiện Khóa liên động và Cho phép |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (Bỏ qua (Bypass) tất cả các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (Kiểm tra lại bình thường (Check / Gỡ bỏ Bypass) toàn bộ điều kiện Khóa liên động và Cho phép), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo |
| **`ORdy_CV`** | `BOOL` | `0` | [Cờ cho phép nhập dữ liệu trên HMI] 1 = Sẵn sàng nhận giá trị đặt OSet_CV (Giá trị biến điều khiển đầu ra (Controlled Variable - CV, tính theo đơn vị kỹ thuật EU)), cho phép mở ô nhập số trên HMI Faceplate |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`OSet_CV`** | `REAL` | `0` | [Giá trị đặt từ Người vận hành HMI] Giá trị đặt từ Người vận hành (Operator HMI): Giá trị biến điều khiển đầu ra (Controlled Variable - CV, tính theo đơn vị kỹ thuật EU) |
| **`Val_Notify`** | `SINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo hiện hành và trạng thái xác nhận của khối này (0 = Không có cảnh báo, 1 = Đã hết lỗi nhưng chờ Reset/Ack, 2..3 = Thấp, 4..5 = Trung bình, 6..7 = Cao, 8..9 = Khẩn cấp) |
---

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
---

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
---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_AInHART`
**Thuộc chương:** [Chương 1: Xử lý tín hiệu Vào/Ra (I/O Processing)](../01_CHUONG_1_IO_PROCESSING.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `IO Processing 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 76)  
**Tệp định nghĩa khối lệnh:** `P_AInHART_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_AInHART`:** **107** Biến đầu vào (`Input Parameters`) | **81** Biến đầu ra (`Output Parameters`) | **54** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_AInHART` trong Studio 5000
- **Chức năng cốt lõi:** Mở rộng từ `P_AIn` để đọc đồng thời tín hiệu 4–20mA và gói dữ liệu số **HART** từ thẻ Analog Input có tích hợp modem HART: đọc 4 biến động (PV, SV, TV, QV) và giải mã bit trạng thái chẩn đoán thiết bị đo hiện trường.
- **Khi nào CẦN sử dụng:** Khi sử dụng đồng hồ lưu lượng Coriolis/Vortex hoặc cảm biến thông minh có giao thức HART và muốn lấy đồng thời cả Lưu lượng khối lượng (PV), Khối lượng riêng (SV) và Nhiệt độ (TV) trên cùng 1 đôi dây 4–20mA.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi thẻ I/O hoặc cảm biến không hỗ trợ HART (dùng [`P_AIn.md`](./P_AIn.md)).

### Quy trình 4 bước đấu nối và cấu hình `P_AInHART`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Ref_ChanData`, `Ref_DiagTable`, `Inp_AV`, `Inp_AVSrcQ`, `Inp_AVNotify`, `Inp_AVBad`, `Inp_AVUncertain`, `Inp_Sim`, `Inp_HiHiGate`, `Inp_HiGate`, `Inp_LoGate`, `Inp_LoLoGate`, `Inp_FailGate`, `Inp_Reset`
   - **Các chân tham chiếu dữ liệu (`Ref_*` / `InOut`):** `Ref_ChanData`, `Ref_DiagTable` — bắt buộc gán thẻ mảng/cấu trúc tương ứng trong Controller Tags.
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_NoSubstPV`, `Cfg_HasMoreObj`, `Cfg_HasChanObj`, `Cfg_UseChanSrcQ`, `Cfg_HasHART`, `Cfg_HasPV`, `Cfg_HasSV`, `Cfg_HasTV`, `Cfg_HasFV`, `Cfg_UseVarSts`, `Cfg_UseHARTText`, `Cfg_UseHARTScaling`, `Cfg_AutoUpdDevInfo`, `Cfg_ManUpdDevInfo`, `Cfg_PVDecPlcs`, `Cfg_SVDecPlcs`, `Cfg_TVDecPlcs`, `Cfg_FVDecPlcs`...
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_Desc`, `Cfg_EU`, `Cfg_FVEU`, `Cfg_FVLabel`, `Cfg_Label`, `Cfg_PVEU`, `Cfg_PVLabel`, `Cfg_SVEU`, `Cfg_SVLabel`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_AInHART` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_AInHART` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PCmd_ClearCapt`, `PCmd_Reset`, `PCmd_HiHiAck`, `PCmd_HiAck`, `PCmd_LoAck`, `PCmd_LoLoAck`, `PCmd_FailAck`, `PCmd_FailSuppress`, `PCmd_FailUnsuppress`, `PCmd_HiHiSuppress`, `PCmd_HiHiUnsuppress`, `PCmd_HiSuppress`, `PCmd_HiUnsuppress`, `PCmd_LoLoSuppress`, `PCmd_LoLoUnsuppress`, `PCmd_LoSuppress`, `PCmd_LoUnsuppress`, `PCmd_HiHiUnshelve`...
   - **Cách sử dụng trong code PLC:** Gán giá trị đặt vào `PSet_*` (nếu có) hoặc kích xung 1 chu kỳ vào các bit lệnh `PCmd_* = 1` để xóa lỗi (`PCmd_Reset`), xác nhận cảnh báo (`PCmd_*Ack`) hoặc ức chế cảnh báo (`PCmd_*Suppress`). Khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi.
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Val`, `Val_InpAV`, `Val_PVMinCapt`, `Val_PVMaxCapt`, `Val_PVEUMin`, `Val_PVEUMax`, `Val_PV`, `Val_SV`, `Val_TV`, `Val_FV`, `Val_DiagCode1`, `Val_DiagCode2`, `Val_DiagCode3`, `Val_NAMURSts`, `Val_NAMURSts1`, `Val_NAMURSts2`, `Val_NAMURSts3`, `Val_Fault`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 107 Biến Đầu Vào (`Input Parameters`) của `P_AInHART`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Ref_ChanData`** | `HART_ChanData` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut liên kết tới cấu trúc dữ liệu kênh HART của thẻ Analog Output |
| **`Ref_DiagTable`** | `P_DiagTable[1]` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới bảng tra cứu giải mã Mã chẩn đoán HART (Diagnostic Code) sang chuỗi văn bản mô tả |
| **`Inp_AV`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu giá trị tương tự (Analog Value - AV) đọc trực tiếp từ cấu trúc dữ liệu thẻ HART (đơn vị thô Raw) |
| **`Inp_AVSrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã nguồn gốc và chất lượng tín hiệu AV đầu vào (nối từ khối kênh nếu có) |
| **`Inp_AVNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã mức độ ưu tiên cảnh báo và trạng thái xác nhận của đối tượng kênh AV liên kết |
| **`Inp_AVBad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi tín hiệu AV hoặc lỗi truyền thông thẻ I/O (1 = Lỗi/Bad, 0 = Tốt/OK) |
| **`Inp_AVUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái giá trị AV không đáng tin cậy / không chắc chắn (1 = Uncertain, 0 = OK) |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân kích hoạt chế độ Mô phỏng (Simulation): Khi = 1, khóa các ngõ ra vật lý ở mức an toàn và dùng giá trị/phản hồi giả lập nội bộ; khi = 0, vận hành thực tế bình thường |
| **`Inp_HiHiGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát mức Rất Cao (High-High): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_HiGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát mức Cao (High): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_LoGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát mức Thấp (Low): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_LoLoGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát mức Rất Thấp (Low-Low): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_FailGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát lỗi tín hiệu / hỏng kênh đo (Signal Failure): Khi = 1, bật kiểm tra ngưỡng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay); khi = 0, tắt giám sát và ức chế cảnh báo (dùng khi thiết bị công nghệ đang dừng) |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Cfg_NoSubstPV`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Khóa chức năng nhập giá trị thay thế (Substitute PV): 0 = Cho phép nhân viên bảo trì nhập giá trị PV thay thế; 1 = Cấm sử dụng tính năng Substitute PV |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_HasChanObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối chẩn đoán kênh (P_AIChan) liên kết với khối này và bật nút điều hướng mở Faceplate tương ứng |
| **`Cfg_UseChanSrcQ`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Nhận trực tiếp mã nguồn gốc và chất lượng (SrcQ) từ khối kênh P_AIChan nối ở đầu vào; 0 = Khối tự tính toán mã SrcQ nội bộ |
| **`Cfg_HasHART`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Thiết bị đo có sử dụng truyền thông kỹ thuật số HART; 0 = Thiết bị chỉ dùng tín hiệu tương tự 4–20mA thuần túy |
| **`Cfg_HasPV`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Biến sơ cấp kỹ thuật số HART (PV) được cấu hình và hiển thị trên HMI Faceplate |
| **`Cfg_HasSV`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Biến thứ cấp kỹ thuật số HART (SV) được cấu hình và hiển thị trên HMI Faceplate |
| **`Cfg_HasTV`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Biến thứ ba kỹ thuật số HART (TV) được cấu hình và hiển thị trên HMI Faceplate |
| **`Cfg_HasFV`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Biến thứ tư kỹ thuật số HART (FV) được cấu hình và hiển thị trên HMI Faceplate |
| **`Cfg_UseVarSts`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Sử dụng trạng thái chất lượng của các biến kỹ thuật số HART để đánh giá lỗi; 0 = Bỏ qua trạng thái biến HART |
| **`Cfg_UseHARTText`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tự động lấy chuỗi Tag, Mô tả, Nhãn và Đơn vị kỹ thuật đọc qua HART để hiển thị HMI; 0 = Dùng chuỗi nhập thủ công |
| **`Cfg_UseHARTScaling`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tự động lấy dải đo Raw và EU đọc từ thiết bị qua HART để quy đổi tỷ lệ; 0 = Dùng dải đo nhập thủ công trong Cfg_* |
| **`Cfg_AutoUpdDevInfo`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tự động đọc cập nhật thông tin thiết bị HART định kỳ; 0 = Không tự động cập nhật |
| **`Cfg_ManUpdDevInfo`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép yêu cầu đọc cập nhật thông tin thiết bị HART thủ công từ HMI; 0 = Không cho phép |
| **`Cfg_PVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho giá trị PV trên màn hình HMI (từ 0 đến 6 chữ số) |
| **`Cfg_SVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho giá trị SV trên màn hình HMI (từ 0 đến 6 chữ số) |
| **`Cfg_TVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho giá trị TV trên màn hình HMI (từ 0 đến 6 chữ số) |
| **`Cfg_FVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho giá trị FV trên màn hình HMI (từ 0 đến 6 chữ số) |
| **`Cfg_HasHiHiAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo mức Rất Cao (High-High): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasHiAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo mức Cao (High): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasLoAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo mức Thấp (Low): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasLoLoAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo mức Rất Thấp (Low-Low): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasFailAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HiHiResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo mức Rất Cao (High-High): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_HiResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo mức Cao (High): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_LoResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo mức Thấp (Low): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_LoLoResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo mức Rất Thấp (Low-Low): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_FailResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_HiHiAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo mức Rất Cao (High-High): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_HiAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo mức Cao (High): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_LoAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo mức Thấp (Low): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_LoLoAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo mức Rất Thấp (Low-Low): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_FailAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_HiHiSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo mức Rất Cao (High-High) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_HiSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo mức Cao (High) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_LoSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo mức Thấp (Low) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_LoLoSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo mức Rất Thấp (Low-Low) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_FailSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_InpRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đầu vào thô tối thiểu (Raw Min, ví dụ 4.0 mA hoặc 0) dùng để quy đổi tỷ lệ tuyến tính sang đơn vị kỹ thuật |
| **`Cfg_InpRawMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đầu vào thô tối đa (Raw Max, ví dụ 20.0 mA hoặc 100) dùng để quy đổi tỷ lệ tuyến tính sang đơn vị kỹ thuật |
| **`Cfg_PVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tương ứng với Raw Min (PV EU Min — hỗ trợ cả quy đổi ngược nếu đặt EUMin > EUMax) |
| **`Cfg_PVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tương ứng với Raw Max (PV EU Max — hỗ trợ cả quy đổi ngược nếu đặt EUMax < EUMin) |
| **`Cfg_FiltTC`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Hằng số thời gian của bộ lọc thông thấp bậc 1 cho tín hiệu PV (tính bằng giây; đặt = 0.0 nếu không lọc) |
| **`Cfg_HiHiDB`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng mức Rất Cao (High-High) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_HiDB`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng mức Cao (High) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_LoDB`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng mức Thấp (Low) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_LoLoDB`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng mức Rất Thấp (Low-Low) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_FailDB`** | `REAL` | `0.41666666000000002` | Ẩn (Tag) | [Biến cấu hình thông số] Vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) áp dụng cho ngưỡng lỗi tín hiệu / hỏng kênh đo (Signal Failure) nhằm chống hiện tượng báo động nhấp nháy lặp lại khi tín hiệu dao động quanh ngưỡng |
| **`Cfg_HiHiOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho mức Rất Cao (High-High): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_HiOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho mức Cao (High): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_LoOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho mức Thấp (Low): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_LoLoOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho mức Rất Thấp (Low-Low): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_FailOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây) cho lỗi tín hiệu / hỏng kênh đo (Signal Failure): Tín hiệu phải vi phạm ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/báo động (lọc nhiễu thoáng qua) |
| **`Cfg_HiHiOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho mức Rất Cao (High-High): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_HiOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho mức Cao (High): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_LoOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho mức Thấp (Low): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_LoLoOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho mức Rất Thấp (Low-Low): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_FailOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây) cho lỗi tín hiệu / hỏng kênh đo (Signal Failure): Tín hiệu phải trở về vùng an toàn (đã tính Deadband) liên tục đủ thời gian này mới xóa trạng thái/báo động |
| **`Cfg_HiHiGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho mức Rất Cao (High-High): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_HiGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho mức Cao (High): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_LoGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho mức Thấp (Low): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_LoLoGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho mức Rất Thấp (Low-Low): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_FailGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây) cho lỗi tín hiệu / hỏng kênh đo (Signal Failure): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng |
| **`Cfg_FailHiLim`** | `REAL` | `103.958336` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng giới hạn trên báo lỗi đứt dây / vượt dải tín hiệu đầu vào (Out-of-Range High, đơn vị thô Raw hoặc EU) |
| **`Cfg_FailLoLim`** | `REAL` | `-2.0833333000000001` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng giới hạn dưới báo lỗi đứt dây / vượt dải tín hiệu đầu vào (Out-of-Range Low, đơn vị thô Raw hoặc EU) |
| **`Set_SimPV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Giá trị PV giả lập (đơn vị kỹ thuật EU) được sử dụng khi bật chế độ Mô phỏng (Inp_Sim = 1) |
| **`Set_SimHARTPV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Giá trị biến sơ cấp HART (PV) giả lập dùng trong chế độ Mô phỏng (khi Inp_Sim = 1, đơn vị EU) |
| **`Set_SimHARTSV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Giá trị biến thứ cấp HART (SV) giả lập dùng trong chế độ Mô phỏng (khi Inp_Sim = 1, đơn vị EU) |
| **`Set_SimHARTTV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Giá trị biến thứ ba HART (TV) giả lập dùng trong chế độ Mô phỏng (khi Inp_Sim = 1, đơn vị EU) |
| **`Set_SimHARTFV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Giá trị biến thứ tư HART (FV) giả lập dùng trong chế độ Mô phỏng (khi Inp_Sim = 1, đơn vị EU) |
| **`PCmd_ClearCapt`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa giá trị đỉnh Cực tiểu / Cực đại (Min/Max Excursion) đã lưu của PV |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`PCmd_HiHiAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo mức Rất Cao (High-High) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_HiAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo mức Cao (High) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_LoAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo mức Thấp (Low) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_LoLoAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo mức Rất Thấp (Low-Low) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_FailAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_FailSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_HiHiSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo mức Rất Cao (High-High) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_HiHiUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo mức Rất Cao (High-High) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_HiSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo mức Cao (High) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_HiUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo mức Cao (High) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_LoLoSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo mức Rất Thấp (Low-Low) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_LoLoUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo mức Rất Thấp (Low-Low) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_LoSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo mức Thấp (Low) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_LoUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo mức Thấp (Low) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_HiHiUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo mức Rất Cao (High-High) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_HiUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo mức Cao (High) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_LoUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo mức Thấp (Low) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_LoLoUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo mức Rất Thấp (Low-Low) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_FailUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) ngay lập tức (biến xung tự động xóa về 0) |

## 3. Bảng chi tiết 81 Biến Đầu Ra (`Output Parameters`) của `P_AInHART`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Val`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu ra chính của biến quá trình PV (đã qua quy đổi đơn vị kỹ thuật EU, lọc nhiễu và chọn giá trị thay thế Substitute PV nếu có) |
| **`Val_InpAV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị tương tự thực tế tính từ kênh 4–20mA (trước khi qua bộ chọn Substitute PV) |
| **`Val_PVMinCapt`** | `REAL` | `1.5000000000000001E+38` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cực tiểu (Minimum Peak Excursion) của PV ghi nhận được kể từ lần xóa (ClearCapt) gần nhất |
| **`Val_PVMaxCapt`** | `REAL` | `-1.5000000000000001E+38` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cực đại (Maximum Peak Excursion) của PV ghi nhận được kể từ lần xóa (ClearCapt) gần nhất |
| **`Val_PVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận dưới của dải đo đơn vị kỹ thuật PV = MIN(Cfg_PVEUMin, Cfg_PVEUMax) |
| **`Val_PVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận trên của dải đo đơn vị kỹ thuật PV = MAX(Cfg_PVEUMin, Cfg_PVEUMax) |
| **`Val_PV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị biến sơ cấp kỹ thuật số (HART Primary Variable - PV, tính theo đơn vị PVEU) |
| **`Val_SV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị biến thứ cấp kỹ thuật số (HART Secondary Variable - SV, tính theo đơn vị SVEU) |
| **`Val_TV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị biến thứ ba kỹ thuật số (HART Third Variable - TV, tính theo đơn vị TVEU) |
| **`Val_FV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị biến thứ tư kỹ thuật số (HART Fourth Variable - FV, tính theo đơn vị FVEU) |
| **`Val_DiagCode1`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã chẩn đoán thiết bị HART số #1 (-1 = Không có lỗi chẩn đoán) |
| **`Val_DiagCode2`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã chẩn đoán thiết bị HART số #2 (-1 = Không có lỗi chẩn đoán) |
| **`Val_DiagCode3`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã chẩn đoán thiết bị HART số #3 (-1 = Không có lỗi chẩn đoán) |
| **`Val_NAMURSts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái chẩn đoán chuẩn NAMUR NE107 tổng hợp (mức xấu nhất trong 3 mã chẩn đoán HART) dùng để hiển thị biểu tượng trên HMI |
| **`Val_NAMURSts1`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã phân loại trạng thái chuẩn NAMUR NE107 cho mã chẩn đoán HART #1: 0 = Tốt (OK), 1 = Thông tin (Info), 2 = Yêu cầu bảo trì (Maintenance Required), 4 = Ngoài đặc tính (Off Spec), 8 = Kiểm tra chức năng (Function Check), 16 = Lỗi hỏng (Failure) |
| **`Val_NAMURSts2`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã phân loại trạng thái chuẩn NAMUR NE107 cho mã chẩn đoán HART #2: 0 = Tốt (OK), 1 = Thông tin (Info), 2 = Yêu cầu bảo trì (Maintenance Required), 4 = Ngoài đặc tính (Off Spec), 8 = Kiểm tra chức năng (Function Check), 16 = Lỗi hỏng (Failure) |
| **`Val_NAMURSts3`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã phân loại trạng thái chuẩn NAMUR NE107 cho mã chẩn đoán HART #3: 0 = Tốt (OK), 1 = Thông tin (Info), 2 = Yêu cầu bảo trì (Maintenance Required), 4 = Ngoài đặc tính (Off Spec), 8 = Kiểm tra chức năng (Function Check), 16 = Lỗi hỏng (Failure) |
| **`SrcQ_IO`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng dữ liệu tổng hợp cuối cùng (0 = Tín hiệu thực Tốt đã xác nhận, 1 = Tín hiệu thực Tốt giả định, 2 = Không cấu hình phản hồi/Tốt giả định, 8 = Đang mô phỏng, 9 = Mô phỏng vòng lặp, 10 = Giá trị nhập tay, 16 = Ngoài đặc tính kỹ thuật, 17 = Thay thế tại thiết bị/bus, 18 = Thay thế bởi Bảo trì, 19 = Ngắt bảo vệ giữ giá trị cũ, 20 = Ngắt bảo vệ dùng giá trị thay thế, 32 = Lỗi tín hiệu vượt dải/NaN, 33 = Lỗi kênh I/O, 34 = Lỗi module I/O, 35 = Lỗi cấu hình) |
| **`SrcQ_PV`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của biến sơ cấp HART PV (kiểu liệt kê enum) |
| **`SrcQ_SV`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của biến thứ cấp HART SV (kiểu liệt kê enum) |
| **`SrcQ_TV`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của biến thứ ba HART TV (kiểu liệt kê enum) |
| **`SrcQ_FV`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của biến thứ tư HART FV (kiểu liệt kê enum) |
| **`Val_Fault`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái lỗi thiết bị (Device Fault Status — kiểu liệt kê enum: 0 = Không lỗi, 16 = Lỗi khởi động/mở, 17 = Lỗi dừng/đóng, 20 = Lo, 21 = Hi, 24 = LoLo, 25 = HiHi, 32 = Fail/I/O Fault, 34 = Lỗi cấu hình CfgErr) |
| **`Val_NotifyAll`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận tổng hợp của khối này cộng với các đối tượng kênh/quá tải liên kết |
| **`Val_UnackAlmC`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms Count) |
| **`Sts_SubstPV`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang sử dụng giá trị PV thay thế (Substitute PV do nhân viên bảo trì nhập) thay cho tín hiệu cảm biến thật |
| **`Sts_PVBad`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Chất lượng tín hiệu PV Xấu (Bad Quality) hoặc tín hiệu vượt ngoài dải lỗi (Out of Range) |
| **`Sts_PVUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Chất lượng tín hiệu PV Không chắc chắn (Uncertain Quality) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoặc cưỡng bức bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có ít nhất một cảnh báo bị Tạm hoãn (Shelved), Vô hiệu hóa (Disabled) hoặc Ức chế (Suppressed) — hiển thị biểu tượng chữ "I" trên HMI |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_Raw`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải giá trị thô đầu vào (Cfg_InpRawMin = Cfg_InpRawMax) bị trùng nhau |
| **`Err_EU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải quy đổi đơn vị kỹ thuật (Engineering Units) có giá trị Tối thiểu bằng Tối đa (Min = Max) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị đặt bộ định thời gian (Timer Preset) nằm ngoài phạm vi hợp lệ 0..2.147.483 giây |
| **`Err_Filt`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thông số bộ lọc tín hiệu PV (Cfg_FiltTC hoặc Cfg_RateTime) không hợp lệ |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình cảnh báo: Thời gian duy trì tối thiểu (Min On Time), thời gian tạm hoãn (Shelf Time) hoặc mức độ nghiêm trọng (Severity 1..1000) không hợp lệ |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc chốt ngắt bảo vệ (Shed) đã hết lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_HiHiCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của mức Rất Cao (High-High) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_HiHiGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của mức Rất Cao (High-High) đã đếm xong, đang cho phép giám sát |
| **`Sts_HiHi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện mức Rất Cao (High-High) |
| **`Alm_HiHi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho mức Rất Cao (High-High) |
| **`Ack_HiHi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động mức Rất Cao (High-High) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_HiHiDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Rất Cao (High-High) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_HiHiSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Rất Cao (High-High) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_HiHiShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Rất Cao (High-High) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_HiCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của mức Cao (High) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_HiGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của mức Cao (High) đã đếm xong, đang cho phép giám sát |
| **`Sts_Hi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện mức Cao (High) |
| **`Alm_Hi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho mức Cao (High) |
| **`Ack_Hi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động mức Cao (High) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_HiDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Cao (High) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_HiSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Cao (High) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_HiShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Cao (High) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_LoCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của mức Thấp (Low) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_LoGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của mức Thấp (Low) đã đếm xong, đang cho phép giám sát |
| **`Sts_Lo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện mức Thấp (Low) |
| **`Alm_Lo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho mức Thấp (Low) |
| **`Ack_Lo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động mức Thấp (Low) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_LoDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Thấp (Low) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_LoSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Thấp (Low) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_LoShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Thấp (Low) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_LoLoCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của mức Rất Thấp (Low-Low) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_LoLoGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của mức Rất Thấp (Low-Low) đã đếm xong, đang cho phép giám sát |
| **`Sts_LoLo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện mức Rất Thấp (Low-Low) |
| **`Alm_LoLo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho mức Rất Thấp (Low-Low) |
| **`Ack_LoLo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động mức Rất Thấp (Low-Low) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_LoLoDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Rất Thấp (Low-Low) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_LoLoSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Rất Thấp (Low-Low) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_LoLoShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo mức Rất Thấp (Low-Low) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_FailCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu đầu vào hiện đang vượt ngưỡng so sánh của lỗi tín hiệu / hỏng kênh đo (Signal Failure) (trước khi tính thời gian trễ và cổng Gate) |
| **`Sts_FailGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bộ định thời trễ mở cổng (Gate Delay) của lỗi tín hiệu / hỏng kênh đo (Signal Failure) đã đếm xong, đang cho phép giám sát |
| **`Sts_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi tín hiệu / hỏng kênh đo (Signal Failure) |
| **`Alm_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi tín hiệu / hỏng kênh đo (Signal Failure) |
| **`Ack_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi tín hiệu / hỏng kênh đo (Signal Failure) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_FailDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_FailSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_FailShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`P_AInHART`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_AInHART) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 54 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_AInHART`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_8` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'Analog Input (HART)'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật (Engineering Units, ví dụ: "°C", "bar", "m3/h", "%") hiển thị trên màn hình HMI |
| **`Cfg_FVEU`** | `STRING_8` | `''` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của biến thứ tư HART FV hiển thị trên màn hình HMI |
| **`Cfg_FVLabel`** | `STRING_16` | `''` | [Biến cấu hình thông số] Nhãn mô tả cho Biến thứ tư (Fourth Variable - FV) hiển thị trên HMI |
| **`Cfg_Label`** | `STRING_20` | `'Analog Input (HART)'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_PVEU`** | `STRING_8` | `''` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của biến sơ cấp PV hiển thị trên màn hình HMI |
| **`Cfg_PVLabel`** | `STRING_16` | `''` | [Biến cấu hình thông số] Nhãn mô tả cho Biến sơ cấp (Primary Variable - PV) hiển thị trên HMI |
| **`Cfg_SVEU`** | `STRING_8` | `''` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của biến thứ cấp HART SV hiển thị trên màn hình HMI |
| **`Cfg_SVLabel`** | `STRING_16` | `''` | [Biến cấu hình thông số] Nhãn mô tả cho Biến thứ cấp (Secondary Variable - SV) hiển thị trên HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_AInHART'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`Cfg_TVEU`** | `STRING_8` | `''` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của biến thứ ba HART TV hiển thị trên màn hình HMI |
| **`Cfg_TVLabel`** | `STRING_16` | `''` | [Biến cấu hình thông số] Nhãn mô tả cho Biến thứ ba (Third Variable - TV) hiển thị trên HMI |
| **`Fail`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi tín hiệu / hỏng kênh đo (Signal Failure) |
| **`FailGate`** | `P_Gate` | `{...}` | [Khối con P_Gate nội bộ] Bộ định thời trễ mở cổng (Gate Delay), trễ bật (On-Delay) và trễ tắt (Off-Delay) cho lỗi tín hiệu / hỏng kênh đo (Signal Failure) |
| **`Hi`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho mức Cao (High) |
| **`HiGate`** | `P_Gate` | `{...}` | [Khối con P_Gate nội bộ] Bộ định thời trễ mở cổng (Gate Delay), trễ bật (On-Delay) và trễ tắt (Off-Delay) cho mức Cao (High) |
| **`HiHi`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho mức Rất Cao (High-High) |
| **`HiHiGate`** | `P_Gate` | `{...}` | [Khối con P_Gate nội bộ] Bộ định thời trễ mở cổng (Gate Delay), trễ bật (On-Delay) và trễ tắt (Off-Delay) cho mức Rất Cao (High-High) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_AInHART'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`Lo`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho mức Thấp (Low) |
| **`LoGate`** | `P_Gate` | `{...}` | [Khối con P_Gate nội bộ] Bộ định thời trễ mở cổng (Gate Delay), trễ bật (On-Delay) và trễ tắt (Off-Delay) cho mức Thấp (Low) |
| **`LoLo`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho mức Rất Thấp (Low-Low) |
| **`LoLoGate`** | `P_Gate` | `{...}` | [Khối con P_Gate nội bộ] Bộ định thời trễ mở cổng (Gate Delay), trễ bật (On-Delay) và trễ tắt (Off-Delay) cho mức Rất Thấp (Low-Low) |
| **`MCmd_InpPV`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Sử dụng lại tín hiệu PV thực tế từ cảm biến đầu vào (ngưng dùng giá trị thay thế Substitute PV) |
| **`MCmd_SubstPV`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Chuyển sang sử dụng giá trị PV thay thế (Substitute PV) do nhân viên bảo trì nhập |
| **`MCmd_UpdDevInfo`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Yêu cầu đọc cập nhật thông tin định danh thiết bị từ mạng truyền thông |
| **`MRdy_InpPV`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_InpPV (Sử dụng lại tín hiệu PV thực tế từ cảm biến đầu vào (ngưng dùng giá trị thay thế Substitute PV)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_SubstPV`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_SubstPV (Chuyển sang sử dụng giá trị PV thay thế (Substitute PV) do nhân viên bảo trì nhập), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_UpdDevInfo`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_UpdDevInfo (Yêu cầu đọc cập nhật thông tin định danh thiết bị từ mạng truyền thông), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MSet_SubstPV`** | `REAL` | `0` | [Giá trị đặt từ Bảo trì HMI] Giá trị đặt từ Nhân viên bảo trì (Maintenance HMI): Giá trị PV thay thế (Substitute PV, đơn vị EU) do nhân viên bảo trì nhập thủ công để ghi đè tín hiệu cảm biến |
| **`OCmd_ClearCapt`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa giá trị đỉnh Cực tiểu / Cực đại (Min/Max Excursion) đã lưu của PV |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`Val_DiagDesc1`** | `STRING_32` | `‘Logic not yet scanned.’` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi văn bản mô tả chẩn đoán thiết bị số #1 (tra cứu từ mã Val_DiagCode1) |
| **`Val_DiagDesc2`** | `STRING_32` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi văn bản mô tả chẩn đoán thiết bị số #2 (tra cứu từ mã Val_DiagCode2) |
| **`Val_DiagDesc3`** | `STRING_32` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi văn bản mô tả chẩn đoán thiết bị số #3 (tra cứu từ mã Val_DiagCode3) |
| **`Val_HART_Desc`** | `STRING_32` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi mô tả (Description Text) đọc trực tiếp từ thiết bị hiện trường qua giao thức HART |
| **`Val_HART_EU`** | `STRING_8` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi đơn vị kỹ thuật của giá trị tương tự đọc qua giao thức HART |
| **`Val_HART_FVEU`** | `STRING_8` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi đơn vị kỹ thuật của biến thứ tư FV đọc qua giao thức HART |
| **`Val_HART_InpRawMax`** | `REAL` | `100` | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận trên tín hiệu thô (Raw Max) nhận được qua giao thức HART |
| **`Val_HART_InpRawMin`** | `REAL` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận dưới tín hiệu thô (Raw Min) nhận được qua giao thức HART |
| **`Val_HART_Label`** | `STRING_20` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi nhãn hiển thị (Label Text) đọc trực tiếp từ thiết bị hiện trường qua giao thức HART |
| **`Val_HART_PVEU`** | `STRING_8` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi đơn vị kỹ thuật của biến sơ cấp PV đọc qua giao thức HART |
| **`Val_HART_PVEUMax`** | `REAL` | `100` | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận trên dải đo kỹ thuật (PV EU Max) nhận được qua giao thức HART |
| **`Val_HART_PVEUMin`** | `REAL` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận dưới dải đo kỹ thuật (PV EU Min) nhận được qua giao thức HART |
| **`Val_HART_SVEU`** | `STRING_8` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi đơn vị kỹ thuật của biến thứ cấp SV đọc qua giao thức HART |
| **`Val_HART_Tag`** | `STRING_20` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi tên thẻ (Tag Text) đọc trực tiếp từ bộ nhớ thiết bị hiện trường qua giao thức HART |
| **`Val_HART_TVEU`** | `STRING_8` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi đơn vị kỹ thuật của biến thứ ba TV đọc qua giao thức HART |
| **`Val_Notify`** | `SINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo hiện hành và trạng thái xác nhận của khối này (0 = Không có cảnh báo, 1 = Đã hết lỗi nhưng chờ Reset/Ack, 2..3 = Thấp, 4..5 = Trung bình, 6..7 = Cao, 8..9 = Khẩn cấp) |
---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_AOutHART`
**Thuộc chương:** [Chương 1: Xử lý tín hiệu Vào/Ra (I/O Processing)](../01_CHUONG_1_IO_PROCESSING.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `IO Processing 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 76)  
**Tệp định nghĩa khối lệnh:** `P_AOutHART_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_AOutHART`:** **80** Biến đầu vào (`Input Parameters`) | **78** Biến đầu ra (`Output Parameters`) | **48** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_AOutHART` trong Studio 5000
- **Chức năng cốt lõi:** Mở rộng từ `P_AOut` để xuất tín hiệu điều khiển 4–20mA kết hợp đọc dữ liệu phản hồi kỹ thuật số **HART** từ bộ định vị van thông minh (Smart Valve Positioner).
- **Khi nào CẦN sử dụng:** Khi xuất tín hiệu ra van điều khiển thông minh có HART và muốn đọc ngược lại vị trí thực của ty van, áp suất khí nén cấp vào bầu van và trạng thái chẩn đoán HART mà không cần kéo thêm dây AI phản hồi.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi thẻ Analog Output không có HART (dùng [`P_AOut.md`](./P_AOut.md) hoặc [`P_ValveC.md`](./P_ValveC.md)).

### Quy trình 4 bước đấu nối và cấu hình `P_AOutHART`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Ref_ChanData`, `Ref_DiagTable`, `Inp_IntlkOK`, `Inp_NBIntlkOK`, `Inp_IOFault`, `Inp_Sim`, `Inp_Hand`, `Inp_Ovrd`, `Inp_OvrdCV`, `Inp_ExtInh`, `Inp_Reset`
   - **Các chân tham chiếu dữ liệu (`Ref_*` / `InOut`):** `Ref_ChanData`, `Ref_DiagTable` — bắt buộc gán thẻ mảng/cấu trúc tương ứng trong Controller Tags.
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_ShedHold`, `Cfg_SkipRoCLim`, `Cfg_SetTrack`, `Cfg_SetTrackOvrdHand`, `Cfg_HasHART`, `Cfg_HasMoreObj`, `Cfg_HasPV`, `Cfg_HasSV`, `Cfg_HasTV`, `Cfg_HasFV`, `Cfg_UseVarSts`, `Cfg_UseHARTText`, `Cfg_UseHARTScaling`, `Cfg_AutoUpdDevInfo`, `Cfg_ManUpdDevInfo`, `Cfg_HasIntlkObj`, `Cfg_HasCVNav`, `Cfg_OvrdIntlk`...
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_CVNavTag`, `Cfg_Desc`, `Cfg_EU`, `Cfg_FVEU`, `Cfg_FVLabel`, `Cfg_Label`, `Cfg_PVEU`, `Cfg_PVLabel`, `Cfg_SVEU`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_AOutHART` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_AOutHART` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PSet_CV`, `PSet_Owner`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_IntlkTripAck`, `PCmd_IntlkTripSuppress`, `PCmd_IntlkTripUnsuppress`, `PCmd_IntlkTripUnshelve`, `PCmd_IOFaultAck`, `PCmd_IOFaultSuppress`, `PCmd_IOFaultUnsuppress`, `PCmd_IOFaultUnshelve`
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh điều khiển tự động, kiểm tra khối đã sẵn sàng ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển quyền về Program, sau đó gán giá trị vào `PSet_*` và kích xung `PCmd_* = 1` (khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Out_CV`, `Val_CVSet`, `Val_CVOut`, `Val_CVFdbk`, `Val_CVEUMin`, `Val_CVEUMax`, `Val_PV`, `Val_SV`, `Val_TV`, `Val_FV`, `Val_DiagCode1`, `Val_DiagCode2`, `Val_DiagCode3`, `Val_NAMURSts`, `Val_NAMURSts1`, `Val_NAMURSts2`, `Val_NAMURSts3`, `Val_Sts`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 80 Biến Đầu Vào (`Input Parameters`) của `P_AOutHART`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Ref_ChanData`** | `HART_ChanData` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut liên kết tới cấu trúc dữ liệu kênh HART của thẻ Analog Output |
| **`Ref_DiagTable`** | `P_DiagTable[1]` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới bảng tra cứu giải mã Mã chẩn đoán HART (Diagnostic Code) sang chuỗi văn bản mô tả |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp Khóa liên động an toàn có thể Bypass (1 = Liên động OK cho phép chạy; 0 = Cắt dừng thiết bị ngay lập tức; nối từ P_Intlk.Sts_IntlkOK) |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp Khóa liên động an toàn KHÔNG thể Bypass (1 = Liên động bất khả kháng OK; 0 = Cắt dừng thiết bị ngay lập tức; nối từ P_Intlk.Sts_NBIntlkOK) |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi truyền thông I/O của thiết bị (0 = Tốt/OK, 1 = Lỗi truyền thông I/O Fault) |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân kích hoạt chế độ Mô phỏng (Simulation): Khi = 1, khóa các ngõ ra vật lý ở mức an toàn và dùng giá trị/phản hồi giả lập nội bộ; khi = 0, vận hành thực tế bình thường |
| **`Inp_Hand`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu chọn chế độ Điều khiển bằng tay tại chỗ (Hand/Local): 1 = Chiếm quyền sang chế độ Hand (thường nối từ công tắc cứng tại tủ MCC); 0 = Nhả chế độ Hand |
| **`Inp_Ovrd`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu kích hoạt chế độ Cưỡng bức ưu tiên (Override): 1 = Chiếm quyền sang chế độ Override; 0 = Nhả chế độ Override |
| **`Inp_OvrdCV`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Giá trị đặt biến điều khiển CV khi ở chế độ Cưỡng bức (Override CV Target, đơn vị kỹ thuật EU) |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân khóa cấm nguồn lệnh ngoài (External Inhibit): 1 = Cấm chế độ External chiếm quyền điều khiển; 0 = Cho phép External chiếm quyền |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Cfg_ShedHold`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Hành động xử lý ngõ ra CV khi vi phạm Khóa liên động (Interlock Trip): 1 = Giữ nguyên giá trị CV hiện tại (Hold Last State); 0 = Đưa ngõ ra CV về giá trị an toàn định trước (Cfg_IntlkCV) |
| **`Cfg_SkipRoCLim`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Bỏ qua bộ giới hạn tốc độ thay đổi CV (Rate-of-Change Limiting) khi ở chế độ Bảo trì, Cưỡng bức (Override) hoặc khi ngắt Liên động |
| **`Cfg_SetTrack`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép các giá trị đặt (PSet_CV / OSet_CV / SP) tự động bám theo ngõ ra thực tế hiện hành để chuyển mạch không giật (Bumpless Transfer) giữa Operator và Program |
| **`Cfg_SetTrackOvrdHand`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép giá trị đặt của Program và Operator tự động bám theo giá trị CV khi đang ở chế độ Override hoặc Hand |
| **`Cfg_HasHART`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Thiết bị đo có sử dụng truyền thông kỹ thuật số HART; 0 = Thiết bị chỉ dùng tín hiệu tương tự 4–20mA thuần túy |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_HasPV`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Biến sơ cấp kỹ thuật số HART (PV) được cấu hình và hiển thị trên HMI Faceplate |
| **`Cfg_HasSV`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Biến thứ cấp kỹ thuật số HART (SV) được cấu hình và hiển thị trên HMI Faceplate |
| **`Cfg_HasTV`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Biến thứ ba kỹ thuật số HART (TV) được cấu hình và hiển thị trên HMI Faceplate |
| **`Cfg_HasFV`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Biến thứ tư kỹ thuật số HART (FV) được cấu hình và hiển thị trên HMI Faceplate |
| **`Cfg_UseVarSts`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Sử dụng trạng thái chất lượng của các biến kỹ thuật số HART để đánh giá lỗi; 0 = Bỏ qua trạng thái biến HART |
| **`Cfg_UseHARTText`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tự động lấy chuỗi Tag, Mô tả, Nhãn và Đơn vị kỹ thuật đọc qua HART để hiển thị HMI; 0 = Dùng chuỗi nhập thủ công |
| **`Cfg_UseHARTScaling`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tự động lấy dải đo Raw và EU đọc từ thiết bị qua HART để quy đổi tỷ lệ; 0 = Dùng dải đo nhập thủ công trong Cfg_* |
| **`Cfg_AutoUpdDevInfo`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tự động đọc cập nhật thông tin thiết bị HART định kỳ; 0 = Không tự động cập nhật |
| **`Cfg_ManUpdDevInfo`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép yêu cầu đọc cập nhật thông tin thiết bị HART thủ công từ HMI; 0 = Không cho phép |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Khóa liên động (P_Intlk) nối vào chân Inp_IntlkOK và bật nút điều hướng mở Faceplate P_Intlk |
| **`Cfg_HasCVNav`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép hiển thị nút điều hướng nhảy tới Faceplate của khối cơ cấu chấp hành CV trên HMI |
| **`Cfg_OvrdIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Chế độ Override được quyền bỏ qua các điều kiện Khóa liên động (Interlocks) có thể bypass; 0 = Luôn kiểm tra Interlocks |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt dừng thiết bị về trạng thái an toàn (Shed) và phát báo động khi lỗi truyền thông thẻ I/O; 0 = Chỉ phát báo động |
| **`Cfg_DecPlcs`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho tốc độ/giá trị trên màn hình HMI (từ 0 đến 6 chữ số) |
| **`Cfg_PVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho giá trị PV trên màn hình HMI (từ 0 đến 6 chữ số) |
| **`Cfg_SVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho giá trị SV trên màn hình HMI (từ 0 đến 6 chữ số) |
| **`Cfg_TVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho giá trị TV trên màn hình HMI (từ 0 đến 6 chữ số) |
| **`Cfg_FVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho giá trị FV trên màn hình HMI (từ 0 đến 6 chữ số) |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_MinCV`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp dưới tối thiểu cho phép của ngõ ra điều khiển CV (Minimum CV Limit, đơn vị kỹ thuật EU) |
| **`Cfg_MaxCV`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp trên tối đa cho phép của ngõ ra điều khiển CV (Maximum CV Limit, đơn vị kỹ thuật EU) |
| **`Cfg_CVRoCLimInc`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn tốc độ tăng tối đa của ngõ ra CV (Increasing Rate-of-Change Limit, đơn vị EU/giây; đặt = 0.0 để tăng tức thời không giới hạn) |
| **`Cfg_CVRoCLimDec`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn tốc độ giảm tối đa của ngõ ra CV (Decreasing Rate-of-Change Limit, đơn vị EU/giây; đặt = 0.0 để giảm tức thời không giới hạn) |
| **`Cfg_IntlkCV`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị ngõ ra CV an toàn (đơn vị kỹ thuật EU) được xuất ra khi vi phạm Khóa liên động (nếu Cfg_ShedHold = 0) |
| **`Cfg_CVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật đầu ra CV tương ứng với Cfg_CVRawMin (thường là 0.0%) |
| **`Cfg_CVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật đầu ra CV tương ứng với Cfg_CVRawMax (thường là 100.0%) |
| **`Cfg_CVRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị thô đầu ra tối thiểu (CV Raw Min) gửi ra thẻ Analog Output tương ứng với Cfg_CVEUMin |
| **`Cfg_CVRawMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị thô đầu ra tối đa (CV Raw Max) gửi ra thẻ Analog Output tương ứng với Cfg_CVEUMax |
| **`Cfg_MaxInactiveCV`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng CV tối thiểu (đơn vị CVEU) để bật cờ báo thiết bị đang tích cực hoạt động (Sts_Active = 1 trên HMI) |
| **`PSet_CV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Giá trị biến điều khiển đầu ra (Controlled Variable - CV, tính theo đơn vị kỹ thuật EU) |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Mã định danh Chủ sở hữu (Owner ID: ghi khác 0 để đăng ký chiếm quyền điều khiển, ghi 0 để nhả quyền) |
| **`XSet_CV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài] Giá trị đặt từ Hệ thống điều khiển ngoài (External): Giá trị biến điều khiển đầu ra (Controlled Variable - CV, tính theo đơn vị kỹ thuật EU) |
| **`Set_SimHARTPV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Giá trị biến sơ cấp HART (PV) giả lập dùng trong chế độ Mô phỏng (khi Inp_Sim = 1, đơn vị EU) |
| **`Set_SimHARTSV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Giá trị biến thứ cấp HART (SV) giả lập dùng trong chế độ Mô phỏng (khi Inp_Sim = 1, đơn vị EU) |
| **`Set_SimHARTTV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Giá trị biến thứ ba HART (TV) giả lập dùng trong chế độ Mô phỏng (khi Inp_Sim = 1, đơn vị EU) |
| **`Set_SimHARTFV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt / Mô phỏng] Giá trị biến thứ tư HART (FV) giả lập dùng trong chế độ Mô phỏng (khi Inp_Sim = 1, đơn vị EU) |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Chương trình tự động (từ Operator sang Program) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Người vận hành (từ Program sang Operator) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa giữ nguồn lệnh hiện tại (không cho phép nguồn lệnh kia giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Đưa nguồn lệnh về chế độ Bình thường mặc định (Normal: Program hoặc Operator theo Cfg_ProgNormal) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) ngay lập tức (biến xung tự động xóa về 0) |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Chiếm quyền điều khiển (Acquire) về chế độ tương ứng |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |

## 3. Bảng chi tiết 78 Biến Đầu Ra (`Output Parameters`) của `P_AOutHART`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Out_CV`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị ngõ ra điều khiển tương tự (Controlled Variable - CV) gửi tới cơ cấu chấp hành / thẻ Analog Output (đơn vị Raw hoặc EU) |
| **`Val_CVSet`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đặt CV đang được chọn (trước khi qua bộ giới hạn tốc độ thay đổi RoC, đơn vị EU) |
| **`Val_CVOut`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị biến điều khiển CV xuất ra thực tế (sau khi qua bộ giới hạn tốc độ thay đổi RoC, đơn vị EU) |
| **`Val_CVFdbk`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị phản hồi CV đọc ngược từ kênh Analog Output hoặc cơ cấu chấp hành (đơn vị EU) |
| **`Val_CVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận dưới của dải đơn vị kỹ thuật ngõ ra CV = MIN(Cfg_CVEUMin, Cfg_CVEUMax) |
| **`Val_CVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận trên của dải đơn vị kỹ thuật ngõ ra CV = MAX(Cfg_CVEUMin, Cfg_CVEUMax) |
| **`Val_PV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị biến sơ cấp kỹ thuật số (HART Primary Variable - PV, tính theo đơn vị PVEU) |
| **`Val_SV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị biến thứ cấp kỹ thuật số (HART Secondary Variable - SV, tính theo đơn vị SVEU) |
| **`Val_TV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị biến thứ ba kỹ thuật số (HART Third Variable - TV, tính theo đơn vị TVEU) |
| **`Val_FV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị biến thứ tư kỹ thuật số (HART Fourth Variable - FV, tính theo đơn vị FVEU) |
| **`Val_DiagCode1`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã chẩn đoán thiết bị HART số #1 (-1 = Không có lỗi chẩn đoán) |
| **`Val_DiagCode2`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã chẩn đoán thiết bị HART số #2 (-1 = Không có lỗi chẩn đoán) |
| **`Val_DiagCode3`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã chẩn đoán thiết bị HART số #3 (-1 = Không có lỗi chẩn đoán) |
| **`Val_NAMURSts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái chẩn đoán chuẩn NAMUR NE107 tổng hợp (mức xấu nhất trong 3 mã chẩn đoán HART) dùng để hiển thị biểu tượng trên HMI |
| **`Val_NAMURSts1`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã phân loại trạng thái chuẩn NAMUR NE107 cho mã chẩn đoán HART #1: 0 = Tốt (OK), 1 = Thông tin (Info), 2 = Yêu cầu bảo trì (Maintenance Required), 4 = Ngoài đặc tính (Off Spec), 8 = Kiểm tra chức năng (Function Check), 16 = Lỗi hỏng (Failure) |
| **`Val_NAMURSts2`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã phân loại trạng thái chuẩn NAMUR NE107 cho mã chẩn đoán HART #2: 0 = Tốt (OK), 1 = Thông tin (Info), 2 = Yêu cầu bảo trì (Maintenance Required), 4 = Ngoài đặc tính (Off Spec), 8 = Kiểm tra chức năng (Function Check), 16 = Lỗi hỏng (Failure) |
| **`Val_NAMURSts3`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã phân loại trạng thái chuẩn NAMUR NE107 cho mã chẩn đoán HART #3: 0 = Tốt (OK), 1 = Thông tin (Info), 2 = Yêu cầu bảo trì (Maintenance Required), 4 = Ngoài đặc tính (Off Spec), 8 = Kiểm tra chức năng (Function Check), 16 = Lỗi hỏng (Failure) |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng dữ liệu tổng hợp cuối cùng (0 = Tín hiệu thực Tốt đã xác nhận, 1 = Tín hiệu thực Tốt giả định, 2 = Không cấu hình phản hồi/Tốt giả định, 8 = Đang mô phỏng, 9 = Mô phỏng vòng lặp, 10 = Giá trị nhập tay, 16 = Ngoài đặc tính kỹ thuật, 17 = Thay thế tại thiết bị/bus, 18 = Thay thế bởi Bảo trì, 19 = Ngắt bảo vệ giữ giá trị cũ, 20 = Ngắt bảo vệ dùng giá trị thay thế, 32 = Lỗi tín hiệu vượt dải/NaN, 33 = Lỗi kênh I/O, 34 = Lỗi module I/O, 35 = Lỗi cấu hình) |
| **`SrcQ_PV`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của biến sơ cấp HART PV (kiểu liệt kê enum) |
| **`SrcQ_SV`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của biến thứ cấp HART SV (kiểu liệt kê enum) |
| **`SrcQ_TV`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của biến thứ ba HART TV (kiểu liệt kê enum) |
| **`SrcQ_FV`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của biến thứ tư HART FV (kiểu liệt kê enum) |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái đầu ra tương tự HART: 0 = Đã đạt giá trị đích (At Target), 1 = Đang giảm (Ramp Down), 2 = Đang tăng (Ramp Up), 3 = Đang kẹp giới hạn dưới (Clamp Min), 4 = Đang kẹp giới hạn trên (Clamp Max), 33 = Ngưng phục vụ (Disabled) |
| **`Val_Fault`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái lỗi thiết bị (Device Fault Status — kiểu liệt kê enum: 0 = Không lỗi, 16 = Lỗi khởi động/mở, 17 = Lỗi dừng/đóng, 20 = Lo, 21 = Hi, 24 = LoLo, 25 = HiHi, 32 = Fail/I/O Fault, 34 = Lỗi cấu hình CfgErr) |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận tổng hợp của khối này cộng với các đối tượng kênh/quá tải liên kết |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms Count) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã định danh Chủ sở hữu hiện hành của đối tượng (Current Object Owner ID: 0 = Đang không bị chiếm quyền) |
| **`Sts_Ramping`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị ngõ ra CV đang trong quá trình thay đổi theo độ dốc (Ramping) tiến về giá trị đặt |
| **`Sts_Clamped`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị PV tương tự đang bị kẹp ở ngưỡng giới hạn dưới (Cfg_PVLoLim) hoặc giới hạn trên (Cfg_PVHiLim) |
| **`Sts_SkipRoCLim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang bỏ qua bộ giới hạn tốc độ thay đổi CV trong chu kỳ này (do chuyển sang Maint, Ovrd, Intlk hoặc Hand) |
| **`Sts_Active`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị ngõ ra CV lớn hơn ngưỡng kích hoạt (Cfg_MaxInactiveCV), hiển thị biểu tượng hoạt động trên HMI |
| **`Sts_Available`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị sẵn sàng cho Chương trình tự động (Program) điều khiển (đang ở chế độ Program và không có lỗi/liên động ngắt) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass hiện đang được bỏ qua (Bypassed) |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Chế độ Bypass bảo trì đang kích hoạt (gửi sang khối P_Intlk / P_Perm để cho phép bỏ qua các điều kiện có quyền Bypass) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị chưa sẵn sàng hoạt động (kiểm tra các bit Nrdy_* chi tiết để biết nguyên nhân cụ thể) |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Khóa liên động an toàn không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi truyền thông thẻ I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoặc cưỡng bức bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có ít nhất một cảnh báo bị Tạm hoãn (Shelved), Vô hiệu hóa (Disabled) hoặc Ức chế (Suppressed) — hiển thị biểu tượng chữ "I" trên HMI |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Ngưỡng kẹp giới hạn trên của PV nhỏ hơn ngưỡng kẹp giới hạn dưới (Cfg_PVHiLim < Cfg_PVLoLim) |
| **`Err_EU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải quy đổi đơn vị kỹ thuật (Engineering Units) có giá trị Tối thiểu bằng Tối đa (Min = Max) |
| **`Err_Raw`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải giá trị thô đầu vào (Cfg_InpRawMin = Cfg_InpRawMax) bị trùng nhau |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình cảnh báo: Thời gian duy trì tối thiểu (Min On Time), thời gian tạm hoãn (Shelf Time) hoặc mức độ nghiêm trọng (Severity 1..1000) không hợp lệ |
| **`Sts_Hand`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Điều khiển bằng tay tại chỗ (Hand — ưu tiên cao hơn OoS, Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_OoS`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Ngưng phục vụ (Out of Service — ưu tiên cao hơn Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_Maint`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Bảo trì (Maintenance — ưu tiên cao hơn Ovrd, Ext, Prog, Oper) |
| **`Sts_Ovrd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Cưỡng bức ưu tiên (Override — ưu tiên cao hơn Ext, Prog, Oper) |
| **`Sts_Ext`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Điều khiển ngoài (External — ưu tiên cao hơn Prog, Oper) |
| **`Sts_Prog`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Chương trình tự động (Program) |
| **`Sts_ProgLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Chương trình tự động và đã Khóa (Program Locked — không cho Operator lấy quyền) |
| **`Sts_Oper`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Người vận hành trên HMI (Operator) |
| **`Sts_OperLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Người vận hành và đã Khóa (Operator Locked — không cho Program lấy quyền) |
| **`Sts_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Nguồn lệnh hiện hành đang khớp với nguồn lệnh Bình thường mặc định (Program hoặc Operator theo Cfg_ProgNormal) |
| **`Sts_ExtReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Yêu cầu chuyển sang chế độ External đang bị khóa cấm (không thể chuyển sang External từ trạng thái hiện tại) |
| **`Sts_ProgReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Yêu cầu chuyển sang chế độ Program đang bị khóa cấm (không thể chuyển sang Program từ trạng thái hiện tại) |
| **`Sts_MAcqRcvd`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đã nhận được lệnh Chiếm quyền bảo trì (MCmd_Acq) trong chu kỳ quét hiện tại |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc chốt ngắt bảo vệ (Shed) đã hết lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`Alm_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`Ack_IntlkTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_IntlkTripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_IntlkTripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_IntlkTripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi truyền thông thẻ I/O (I/O Fault) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi truyền thông thẻ I/O (I/O Fault) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`MRdy_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`P_AOutHART`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_AOutHART) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 48 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_AOutHART`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_CVNavTag`** | `STRING_NavTag` | `''` | [Biến cấu hình thông số] Tên thẻ (Tagname) của khối cơ cấu chấp hành CV đích khi nhấn nút điều hướng CV trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'Analog Output'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật (Engineering Units, ví dụ: "°C", "bar", "m3/h", "%") hiển thị trên màn hình HMI |
| **`Cfg_FVEU`** | `STRING_8` | `''` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của biến thứ tư HART FV hiển thị trên màn hình HMI |
| **`Cfg_FVLabel`** | `STRING_16` | `''` | [Biến cấu hình thông số] Nhãn mô tả cho Biến thứ tư (Fourth Variable - FV) hiển thị trên HMI |
| **`Cfg_Label`** | `STRING_20` | `'Analog Output'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_PVEU`** | `STRING_8` | `''` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của biến sơ cấp PV hiển thị trên màn hình HMI |
| **`Cfg_PVLabel`** | `STRING_16` | `''` | [Biến cấu hình thông số] Nhãn mô tả cho Biến sơ cấp (Primary Variable - PV) hiển thị trên HMI |
| **`Cfg_SVEU`** | `STRING_8` | `''` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của biến thứ cấp HART SV hiển thị trên màn hình HMI |
| **`Cfg_SVLabel`** | `STRING_16` | `''` | [Biến cấu hình thông số] Nhãn mô tả cho Biến thứ cấp (Secondary Variable - SV) hiển thị trên HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_AOutHART'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`Cfg_TVEU`** | `STRING_8` | `''` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của biến thứ ba HART TV hiển thị trên màn hình HMI |
| **`Cfg_TVLabel`** | `STRING_16` | `''` | [Biến cấu hình thông số] Nhãn mô tả cho Biến thứ ba (Third Variable - TV) hiển thị trên HMI |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc nội bộ quản lý phân quyền và chuyển mạch giữa 7 nguồn lệnh điều khiển (OoS, Hand, Maint, Ovrd, Ext, Prog, Oper) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `??` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `??` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`IOFault`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bỏ qua (Bypass) tất cả các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Kiểm tra lại bình thường (Check / Gỡ bỏ Bypass) toàn bộ điều kiện Khóa liên động và Cho phép |
| **`MCmd_UpdDevInfo`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Yêu cầu đọc cập nhật thông tin định danh thiết bị từ mạng truyền thông |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (Bỏ qua (Bypass) tất cả các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (Kiểm tra lại bình thường (Check / Gỡ bỏ Bypass) toàn bộ điều kiện Khóa liên động và Cho phép), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_UpdDevInfo`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_UpdDevInfo (Yêu cầu đọc cập nhật thông tin định danh thiết bị từ mạng truyền thông), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo |
| **`ORdy_CV`** | `BOOL` | `0` | [Cờ cho phép nhập dữ liệu trên HMI] 1 = Sẵn sàng nhận giá trị đặt OSet_CV (Giá trị biến điều khiển đầu ra (Controlled Variable - CV, tính theo đơn vị kỹ thuật EU)), cho phép mở ô nhập số trên HMI Faceplate |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`OSet_CV`** | `REAL` | `0` | [Giá trị đặt từ Người vận hành HMI] Giá trị đặt từ Người vận hành (Operator HMI): Giá trị biến điều khiển đầu ra (Controlled Variable - CV, tính theo đơn vị kỹ thuật EU) |
| **`Val_DiagDesc1`** | `STRING_32` | `‘Logic not yet scanned.’` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi văn bản mô tả chẩn đoán thiết bị số #1 (tra cứu từ mã Val_DiagCode1) |
| **`Val_DiagDesc2`** | `STRING_32` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi văn bản mô tả chẩn đoán thiết bị số #2 (tra cứu từ mã Val_DiagCode2) |
| **`Val_DiagDesc3`** | `STRING_32` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi văn bản mô tả chẩn đoán thiết bị số #3 (tra cứu từ mã Val_DiagCode3) |
| **`Val_HART_CVEUMax`** | `REAL` | `100` | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận trên đơn vị kỹ thuật ngõ ra CV nhận được qua giao thức HART |
| **`Val_HART_CVEUMin`** | `REAL` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận dưới đơn vị kỹ thuật ngõ ra CV nhận được qua giao thức HART |
| **`Val_HART_CVRawMax`** | `REAL` | `100` | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận trên tín hiệu thô ngõ ra CV nhận được qua giao thức HART |
| **`Val_HART_CVRawMin`** | `REAL` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận dưới tín hiệu thô ngõ ra CV nhận được qua giao thức HART |
| **`Val_HART_Desc`** | `STRING_32` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi mô tả (Description Text) đọc trực tiếp từ thiết bị hiện trường qua giao thức HART |
| **`Val_HART_EU`** | `STRING_8` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi đơn vị kỹ thuật của giá trị tương tự đọc qua giao thức HART |
| **`Val_HART_FVEU`** | `STRING_8` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi đơn vị kỹ thuật của biến thứ tư FV đọc qua giao thức HART |
| **`Val_HART_Label`** | `STRING_20` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi nhãn hiển thị (Label Text) đọc trực tiếp từ thiết bị hiện trường qua giao thức HART |
| **`Val_HART_PVEU`** | `STRING_8` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi đơn vị kỹ thuật của biến sơ cấp PV đọc qua giao thức HART |
| **`Val_HART_SVEU`** | `STRING_8` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi đơn vị kỹ thuật của biến thứ cấp SV đọc qua giao thức HART |
| **`Val_HART_Tag`** | `STRING_20` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi tên thẻ (Tag Text) đọc trực tiếp từ bộ nhớ thiết bị hiện trường qua giao thức HART |
| **`Val_HART_TVEU`** | `STRING_8` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi đơn vị kỹ thuật của biến thứ ba TV đọc qua giao thức HART |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo hiện hành và trạng thái xác nhận của khối này (0 = Không có cảnh báo, 1 = Đã hết lỗi nhưng chờ Reset/Ack, 2..3 = Thấp, 4..5 = Trung bình, 6..7 = Cao, 8..9 = Khẩn cấp) |
