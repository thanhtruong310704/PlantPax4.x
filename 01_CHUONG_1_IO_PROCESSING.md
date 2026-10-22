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
**Thuộc chương:** Chương 1: Xử lý tín hiệu Vào/Ra (I/O Processing)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `IO Processing 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_AIn`:** **91** Biến đầu vào (`Input Parameters`) | **66** Biến đầu ra (`Output Parameters`) | **29** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_AIn`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_PV`, `Inp_PVSrcQ`, `Inp_PVNotify`, `Inp_PVBad`, `Inp_PVUncertain`, `Inp_Sim`, `Inp_HiHiGate`, `Inp_HiGate`, `Inp_LoGate`, `Inp_LoLoGate`, `Inp_FailGate`, `Inp_Reset`, `Cfg_HiHiLim`, `Cfg_HiLim`, `Cfg_LoLim`, `Cfg_LoLoLim`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_NoSubstPV`, `Cfg_HasChanObj`, `Cfg_UseChanSrcQ`, `Cfg_PVDecPlcs`, `Cfg_HasHiHiAlm`, `Cfg_HasHiAlm`, `Cfg_HasLoAlm`, `Cfg_HasLoLoAlm`, `Cfg_HasFailAlm`, `Cfg_HiHiResetReqd`, `Cfg_HiResetReqd`, `Cfg_LoResetReqd`, `Cfg_LoLoResetReqd`, `Cfg_FailResetReqd`, `Cfg_HasMoreObj`, `Cfg_HiHiAckReqd`, `Cfg_HiAckReqd`, `Cfg_LoAckReqd`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_EU`, `Cfg_Label`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_AIn` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PCmd_ClearCapt`, `PCmd_Reset`, `PCmd_HiHiAck`, `PCmd_HiAck`, `PCmd_LoAck`, `PCmd_LoLoAck`, `PCmd_FailAck`, `PCmd_HiHiSuppress`, `PCmd_HiSuppress`, `PCmd_LoSuppress`, `PCmd_LoLoSuppress`, `PCmd_FailSuppress`, `PCmd_HiHiUnsuppress`, `PCmd_HiUnsuppress`, `PCmd_LoUnsuppress`, `PCmd_LoLoUnsuppress`, `PCmd_HiHiUnshelve`, `PCmd_HiUnshelve`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Val`, `Val_InpPV`, `Val_PVMinCapt`, `Val_PVMaxCapt`, `Val_PVEUMin`, `Val_PVEUMax`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Sts_SubstPV`, `Sts_PVBad`, `Sts_PVUncertain`, `Sts_MaintByp`, `Sts_AlmInh`, `Sts_Err`, `Sts_RdyReset`, `Sts_RdyAck`, `Sts_HiHiCmp`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 91 Biến Đầu Vào (`Input Parameters`) của `P_AIn`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_PV`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu đo lường đầu vào (Biến quá trình PV) nối từ cảm biến / thẻ Analog Input |
| **`Inp_PVSrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã nguồn gốc và chất lượng tín hiệu đầu vào (nối từ chân SrcQ của khối P_AIChan nếu có). |
| **`Inp_PVNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã trạng thái mức độ ưu tiên và xác nhận cảnh báo của đối tượng PV liên kết. |
| **`Inp_PVBad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi tín hiệu / lỗi truyền thông đầu vào (1 = Lỗi/Bad, 0 = Tốt/OK). Thường nối trực tiếp từ bit báo lỗi kênh (Channel Fault) của thẻ Analog Input. |
| **`Inp_PVUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái chất lượng tín hiệu không chắc chắn (1 = Không chắc chắn/Uncertain, 0 = Tốt/OK), dùng để xuất ra cờ Sts_PVUncertain. |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân kích hoạt chế độ Mô phỏng: 1 = Dùng giá trị giả lập (Set_SimPV); 0 = Dùng tín hiệu thực tế (Inp_PV). |
| **`Inp_HiHiGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát ngưỡng: Khi = 1, bật kiểm tra ngưỡng tương ứng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay). Khi = 0, tắt giám sát và ép trạng thái ngõ ra về 0 (dùng để ức chế cảnh báo khi thiết bị dừng). |
| **`Inp_HiGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát ngưỡng: Khi = 1, bật kiểm tra ngưỡng tương ứng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay). Khi = 0, tắt giám sát và ép trạng thái ngõ ra về 0 (dùng để ức chế cảnh báo khi thiết bị dừng). |
| **`Inp_LoGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát ngưỡng: Khi = 1, bật kiểm tra ngưỡng tương ứng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay). Khi = 0, tắt giám sát và ép trạng thái ngõ ra về 0 (dùng để ức chế cảnh báo khi thiết bị dừng). |
| **`Inp_LoLoGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát ngưỡng: Khi = 1, bật kiểm tra ngưỡng tương ứng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay). Khi = 0, tắt giám sát và ép trạng thái ngõ ra về 0 (dùng để ức chế cảnh báo khi thiết bị dừng). |
| **`Inp_FailGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát ngưỡng: Khi = 1, bật kiểm tra ngưỡng tương ứng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay). Khi = 0, tắt giám sát và ép trạng thái ngõ ra về 0 (dùng để ức chế cảnh báo khi thiết bị dừng). |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân đầu vào dùng để xóa (Reset) tất cả các cảnh báo đang chờ Reset từ chương trình PLC (1 = Xóa cảnh báo). |
| **`Cfg_NoSubstPV`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Khóa tính năng nhập giá trị thay thế (Substitute PV) của Bảo trì: Khi = 0, cho phép nhân viên bảo trì nhập giá trị PV thay thế; khi = 1, cấm sử dụng tính năng Substitute PV. |
| **`Cfg_HasChanObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng channel liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_UseChanSrcQ`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Truyền thẳng mã chất lượng SrcQ từ khối kênh P_AIChan sang; 0 = Tự tính toán mã SrcQ nội bộ. |
| **`Cfg_PVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho giá trị PV trên HMI (từ 0 đến 6 chữ số). |
| **`Cfg_HasHiHiAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasHiAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasLoAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasLoLoAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasFailAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HiHiResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_HiResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_LoResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_LoLoResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_FailResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_HiHiAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_HiAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_LoAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_LoLoAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_FailAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_HiHiSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_HiSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_LoSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_LoLoSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_FailSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_InpRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đầu vào thô tối thiểu (Raw Min) dùng để quy đổi tỷ lệ tuyến tính. |
| **`Cfg_InpRawMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đầu vào thô tối đa (Raw Max) dùng để quy đổi tỷ lệ tuyến tính. |
| **`Cfg_PVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tối thiểu (EU Min) của PV tương ứng với Raw Min. |
| **`Cfg_PVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tối đa (EU Max) của PV tương ứng với Raw Max. |
| **`Cfg_FiltTC`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Hằng số thời gian lọc thông thấp bậc 1 của PV (giây). Đặt = 0.0 nếu không lọc. |
| **`Cfg_HiHiLim`** | `REAL` | `1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Ngưỡng giới hạn cảnh báo Rất Cao - High-High (tính theo đơn vị kỹ thuật EU). |
| **`Cfg_HiHiDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) cho ngưỡng cảnh báo để chống báo động giả lặp lại khi tín hiệu dao động quanh ngưỡng. |
| **`Cfg_HiDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) cho ngưỡng cảnh báo để chống báo động giả lặp lại khi tín hiệu dao động quanh ngưỡng. |
| **`Cfg_LoDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) cho ngưỡng cảnh báo để chống báo động giả lặp lại khi tín hiệu dao động quanh ngưỡng. |
| **`Cfg_LoLoDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) cho ngưỡng cảnh báo để chống báo động giả lặp lại khi tín hiệu dao động quanh ngưỡng. |
| **`Cfg_FailDB`** | `REAL` | `0.41666666000000002` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) cho ngưỡng cảnh báo để chống báo động giả lặp lại khi tín hiệu dao động quanh ngưỡng. |
| **`Cfg_HiHiGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng. |
| **`Cfg_HiGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng. |
| **`Cfg_LoGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng. |
| **`Cfg_LoLoGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng. |
| **`Cfg_FailGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng. |
| **`Cfg_HiHiOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây): Tín hiệu phải vượt ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/cảnh báo (chống nhiễu thoáng qua). |
| **`Cfg_HiOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây): Tín hiệu phải vượt ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/cảnh báo (chống nhiễu thoáng qua). |
| **`Cfg_LoOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây): Tín hiệu phải vượt ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/cảnh báo (chống nhiễu thoáng qua). |
| **`Cfg_LoLoOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây): Tín hiệu phải vượt ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/cảnh báo (chống nhiễu thoáng qua). |
| **`Cfg_FailOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây): Tín hiệu phải vượt ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/cảnh báo (chống nhiễu thoáng qua). |
| **`Cfg_HiHiOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây): Tín hiệu phải trở về vùng an toàn (đã trừ Deadband) liên tục đủ thời gian này mới xóa trạng thái/cảnh báo. |
| **`Cfg_HiOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây): Tín hiệu phải trở về vùng an toàn (đã trừ Deadband) liên tục đủ thời gian này mới xóa trạng thái/cảnh báo. |
| **`Cfg_LoOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây): Tín hiệu phải trở về vùng an toàn (đã trừ Deadband) liên tục đủ thời gian này mới xóa trạng thái/cảnh báo. |
| **`Cfg_LoLoOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây): Tín hiệu phải trở về vùng an toàn (đã trừ Deadband) liên tục đủ thời gian này mới xóa trạng thái/cảnh báo. |
| **`Cfg_FailOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây): Tín hiệu phải trở về vùng an toàn (đã trừ Deadband) liên tục đủ thời gian này mới xóa trạng thái/cảnh báo. |
| **`Cfg_HiLim`** | `REAL` | `1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Ngưỡng giới hạn cảnh báo Cao - High (tính theo đơn vị kỹ thuật EU). |
| **`Cfg_LoLim`** | `REAL` | `1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Ngưỡng giới hạn cảnh báo Thấp - Low (tính theo đơn vị kỹ thuật EU). |
| **`Cfg_LoLoLim`** | `REAL` | `1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Ngưỡng giới hạn cảnh báo Rất Thấp - Low-Low (tính theo đơn vị kỹ thuật EU). |
| **`Cfg_FailHiLim`** | `REAL` | `103.958336` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng giới hạn báo lỗi vượt dải trên (Out-of-Range High, tính theo đơn vị thô Raw). |
| **`Cfg_FailLoLim`** | `REAL` | `-2.0833333000000001` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng giới hạn báo lỗi vượt dải dưới / đứt dây (Out-of-Range Low, tính theo đơn vị thô Raw). |
| **`Set_SimPV`** | `REAL` | `0` | Ẩn (Tag) | Giá trị PV giả lập dùng trong chế độ Mô phỏng (khi Inp_Sim = 1, tính theo đơn vị kỹ thuật EU). |
| **`PCmd_ClearCapt`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_ClearCapt = 1 để xóa giá trị cực tiểu/cực đại (Min/Max Peak) đã lưu của PV (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_Reset = 1 để xóa tất cả các cảnh báo chốt đang chờ Reset (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_HiHiAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_HiAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_LoAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_LoLoAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_FailAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_HiHiSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Cảnh báo Rất Cao (High-Cảnh báo Cao (High Alarm)) |
| **`PCmd_HiSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Cảnh báo Cao (High Alarm) |
| **`PCmd_LoSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Cảnh báo Thấp (Low Alarm) |
| **`PCmd_LoLoSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Cảnh báo Rất Thấp (Low-Cảnh báo Thấp (Low Alarm)) |
| **`PCmd_FailSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Analog Input Failure Alarm |
| **`PCmd_HiHiUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Cảnh báo Rất Cao (High-Cảnh báo Cao (High Alarm)) |
| **`PCmd_HiUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Cảnh báo Cao (High Alarm) |
| **`PCmd_LoUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Cảnh báo Thấp (Low Alarm) |
| **`PCmd_LoLoUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Cảnh báo Rất Thấp (Low-Cảnh báo Thấp (Low Alarm)) |
| **`PCmd_HiHiUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_HiUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_LoUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_LoLoUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_FailUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |

## 3. Bảng chi tiết 66 Biến Đầu Ra (`Output Parameters`) của `P_AIn`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Val`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu ra chính PV (đã qua quy đổi đơn vị kỹ thuật EU, lọc nhiễu và thay thế PV nếu dùng) |
| **`Val_InpPV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu vào thực tế (đã quy đổi EU nhưng trước khi qua bộ chọn giá trị thay thế Substitute PV) |
| **`Val_PVMinCapt`** | `REAL` | `1.5000000000000001E+38` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị PV thấp nhất (Min Peak) ghi nhận được kể từ lần xóa gần nhất |
| **`Val_PVMaxCapt`** | `REAL` | `-1.5000000000000001E+38` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị PV cao nhất (Max Peak) ghi nhận được kể từ lần xóa gần nhất |
| **`Val_PVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cực tiểu của dải đo đơn vị kỹ thuật = MIN(Cfg_PVEUMin, Cfg_PVEUMax) |
| **`Val_PVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cực đại của dải đo đơn vị kỹ thuật = MAX(Cfg_PVEUMin, Cfg_PVEUMax) |
| **`SrcQ_IO`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final PV source and quality. GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`Val_Fault`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device fault status: 0 = None 20 = Low 21 = High 24 = Low-Low 25 = High-High 32 = Fail 34 = Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Val_NotifyAll`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận của khối này cộng với khối kênh (enum) |
| **`Val_UnackAlmC`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Sts_SubstPV`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang sử dụng giá trị PV thay thế (tín hiệu đầu vào thực đang bị ghi đè bởi Maintenance). |
| **`Sts_PVBad`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu PV có chất lượng xấu (Bad Quality) hoặc vượt ngoài dải giới hạn lỗi (Out of Range) |
| **`Sts_PVUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị PV có chất lượng không chắc chắn (Uncertain Quality) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang bị Tạm hoãn (Shelved) hoặc Vô hiệu hóa (Disabled), hiển thị biểu tượng trên HMI |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình (kiểm tra các bit Err_* chi tiết để biết nguyên nhân) |
| **`Err_Raw`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị dải thô Cfg_InpRawMin bằng Cfg_InpRawMax |
| **`Err_EU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị dải kỹ thuật Cfg_PVEUMin bằng Cfg_PVEUMax |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thời gian trễ On/Off/Gate Delay ngoài phạm vi hợp lệ (0..2147483 giây) |
| **`Err_Filt`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thông số bộ lọc PV (RateTime, hằng số thời gian TC) âm hoặc không hợp lệ |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thông số cảnh báo (Thời gian duy trì tối thiểu, thời gian Shelve, mức Severity) không hợp lệ |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc lỗi ngắt tải (Shed) đã hết điều kiện lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_HiHiCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV High-High kết quả so sánh ngưỡng trực tiếp 1=High-High |
| **`Sts_HiHiGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV High-High trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_HiHi`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị Analog vượt trên ngưỡng Rất Cao (High-High) |
| **`Alm_HiHi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Ngõ vào Analog đang ở trạng thái Cảnh báo Rất Cao (High-High) |
| **`Ack_HiHi`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Cảnh báo Rất Cao (Cảnh báo Rất Cao (High-Cảnh báo Cao (High Alarm))) đã được Xác nhận (Acknowledged) |
| **`Sts_HiHiDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Rất Cao (Cảnh báo Rất Cao (High-Cảnh báo Cao (High Alarm))) đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_HiHiSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Rất Cao (Cảnh báo Rất Cao (High-Cảnh báo Cao (High Alarm))) đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_HiHiShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Rất Cao (Cảnh báo Rất Cao (High-Cảnh báo Cao (High Alarm))) đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_HiCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV High kết quả so sánh ngưỡng trực tiếp 1=High |
| **`Sts_HiGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV High trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_Hi`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị Analog vượt trên ngưỡng Cao (High) |
| **`Alm_Hi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Ngõ vào Analog đang ở trạng thái Cảnh báo Cao (High) |
| **`Ack_Hi`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Cảnh báo Cao (Cảnh báo Cao (High Alarm)) đã được Xác nhận (Acknowledged) |
| **`Sts_HiDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Cao (Cảnh báo Cao (High Alarm)) đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_HiSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Cao (Cảnh báo Cao (High Alarm)) đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_HiShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Cao (Cảnh báo Cao (High Alarm)) đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_LoCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV Low kết quả so sánh ngưỡng trực tiếp 1=Low |
| **`Sts_LoGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV Low trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_Lo`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị Analog tụt dưới ngưỡng Thấp (Low) |
| **`Alm_Lo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Ngõ vào Analog đang ở trạng thái Cảnh báo Thấp (Low) |
| **`Ack_Lo`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Cảnh báo Thấp (Cảnh báo Thấp (Low Alarm)) đã được Xác nhận (Acknowledged) |
| **`Sts_LoDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Thấp (Cảnh báo Thấp (Low Alarm)) đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_LoSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Thấp (Cảnh báo Thấp (Low Alarm)) đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_LoShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Thấp (Cảnh báo Thấp (Low Alarm)) đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_LoLoCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV Low-Low kết quả so sánh ngưỡng trực tiếp 1=Low-Low |
| **`Sts_LoLoGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV Low-Low trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_LoLo`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị Analog tụt dưới ngưỡng Rất Thấp (Low-Low) |
| **`Alm_LoLo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Ngõ vào Analog đang ở trạng thái Cảnh báo Rất Thấp (Low-Low) |
| **`Ack_LoLo`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Cảnh báo Rất Thấp (Cảnh báo Rất Thấp (Low-Cảnh báo Thấp (Low Alarm))) đã được Xác nhận (Acknowledged) |
| **`Sts_LoLoDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Rất Thấp (Cảnh báo Rất Thấp (Low-Cảnh báo Thấp (Low Alarm))) đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_LoLoSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Rất Thấp (Cảnh báo Rất Thấp (Low-Cảnh báo Thấp (Low Alarm))) đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_LoLoShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Rất Thấp (Cảnh báo Rất Thấp (Low-Cảnh báo Thấp (Low Alarm))) đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_FailCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV Fail kết quả so sánh ngưỡng trực tiếp 1=Out of Range |
| **`Sts_FailGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV Fail trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_Fail`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Ngõ vào Analog vượt ngoài dải đo hoặc chất lượng tín hiệu xấu (PV Bad) |
| **`Alm_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Cảnh báo lỗi ngõ vào Analog (Fail Alarm) (PV Bad or Out of Range) |
| **`Ack_Fail`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Cảnh báo lỗi ngõ vào Analog (Fail Alarm) đã được Xác nhận (Acknowledged) |
| **`Sts_FailDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi ngõ vào Analog (Fail Alarm) đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_FailSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi ngõ vào Analog (Fail Alarm) đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_FailShelved`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi ngõ vào Analog (Fail Alarm) đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`P_AIn`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 29 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_AIn`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_8` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'Analog Input'` | [Biến cấu hình thông số] Description for display on HMI. This string is shown in the title bar of the faceplate. |
| **`Cfg_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Engineering Units for display on HMI |
| **`Cfg_Label`** | `STRING_20` | `'Analog Input'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_AIn'` | [Biến cấu hình thông số] Tagname for display on HMI |
| **`Fail`** | `P_Alarm` | `{...}` | Khối con quản lý Cảnh báo lỗi ngõ vào Analog (chất lượng xấu hoặc vượt dải) |
| **`FailGate`** | `P_Gate` | `{...}` | Khối con xử lý Cổng điều kiện và Trễ thời gian cho cảnh báo lỗi (Fail Gate) |
| **`Hi`** | `P_Alarm` | `{...}` | Cảnh báo Cao (High Alarm) |
| **`HiGate`** | `P_Gate` | `{...}` | Khối con xử lý Cổng điều kiện và Trễ thời gian cho cảnh báo Cao (Hi Gate) |
| **`HiHi`** | `P_Alarm` | `{...}` | Cảnh báo Rất Cao (High-Cảnh báo Cao (High Alarm)) |
| **`HiHiGate`** | `P_Gate` | `{...}` | Khối con xử lý Cổng điều kiện và Trễ thời gian cho cảnh báo Rất Cao (HiHi Gate) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_AIn'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI và phần mềm thông tin |
| **`Lo`** | `P_Alarm` | `{...}` | Cảnh báo Thấp (Low Alarm) |
| **`LoGate`** | `P_Gate` | `{...}` | Khối con xử lý Cổng điều kiện và Trễ thời gian cho cảnh báo Thấp (Lo Gate) |
| **`LoLo`** | `P_Alarm` | `{...}` | Cảnh báo Rất Thấp (Low-Cảnh báo Thấp (Low Alarm)) |
| **`LoLoGate`** | `P_Gate` | `{...}` | Khối con xử lý Cổng điều kiện và Trễ thời gian cho cảnh báo Rất Thấp (LoLo Gate) |
| **`MCmd_InpPV`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Bảo trì chọn sử dụng tín hiệu PV thực tế (chế độ bình thường) |
| **`MCmd_SubstPV`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Bảo trì chọn sử dụng giá trị PV thay thế (ghi đè đầu vào) |
| **`MRdy_InpPV`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_InpPV (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_SubstPV`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_SubstPV (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MSet_SubstPV`** | `REAL` | `0` | [Giá trị đặt từ tab Bảo trì (Maintenance)] Giá trị PV thay thế do nhân viên bảo trì nhập (đơn vị kỹ thuật EU) |
| **`OCmd_ClearCapt`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Clear the captured min / max PV excursion values |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset all Alarms requiring Reset |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset and Xác nhận (Acknowledge) all Alarms |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`Val_Notify`** | `SINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận của riêng khối này (enum) |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_AIChan`
**Thuộc chương:** Chương 1: Xử lý tín hiệu Vào/Ra (I/O Processing)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `IO Processing 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_AIChan`:** **52** Biến đầu vào (`Input Parameters`) | **45** Biến đầu ra (`Output Parameters`) | **15** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_AIChan`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_Raw`, `Inp_ModFault`, `Inp_ChanFault`, `Inp_OutOfSpec`, `Inp_FuncCheck`, `Inp_MaintReqd`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_ClampSB`, `Cfg_FailOnUncertain`, `Cfg_PVDecPlcs`, `Cfg_HasFailAlm`, `Cfg_HasMoreObj`, `Cfg_FailResetReqd`, `Cfg_FailAckReqd`, `Cfg_FailSeverity`, `Cfg_InpRawMin`, `Cfg_InpRawMax`, `Cfg_PVEUMin`, `Cfg_PVEUMax`, `Cfg_PVHiLim`, `Cfg_PVLoLim`, `Cfg_InpOORHiLim`, `Cfg_InpOORLoLim`, `Cfg_InpOORDB`, `Cfg_InpOOROnDly`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_EU`, `Cfg_Label`, `Cfg_RU`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_AIChan` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PCmd_FailAck`, `PCmd_FailSuppress`, `PCmd_FailUnsuppress`, `PCmd_FailUnshelve`, `PCmd_Reset`
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Val_InpRaw`, `Val`, `Val_PVEUMin`, `Val_PVEUMax`, `Val_Sts`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Sts_PVGood`, `Sts_PVUncertain`, `Sts_PVBad`, `Sts_UseInp`, `Sts_HoldLast`, `Sts_Clamped`, `Sts_Replaced`, `Sts_InpOORHiCmp`, `Sts_InpOORLoCmp`, `Sts_InpOutOfRange`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 52 Biến Đầu Vào (`Input Parameters`) của `P_AIChan`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_Raw`** | `REAL` | `4` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Input Signal from Transmitter or Sensor (RU) |
| **`Inp_ModFault`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = I/O module failure or module communication status bad 0 = OK |
| **`Inp_ChanFault`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = I/O channel fault or failure 0 = OK |
| **`Inp_OutOfSpec`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Out of specification (PV uncertain, from instrument) 0 = OK |
| **`Inp_FuncCheck`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Function check (PV substituted, from instrument) 0 = OK |
| **`Inp_MaintReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Maintenance Required (from instrument) |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân đầu vào dùng để xóa (Reset) tất cả các cảnh báo đang chờ Reset từ chương trình PLC (1 = Xóa cảnh báo). |
| **`Cfg_ClampSB`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Clamping Snap-To Band to Clamp when PV gets near to Limit |
| **`Cfg_FailOnUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Raise Sts_Fail (and alarm) if Bad or Uncertain quality 0 = Only if Bad quality |
| **`Cfg_PVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho giá trị PV trên HMI (từ 0 đến 6 chữ số). |
| **`Cfg_HasFailAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_FailResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_FailAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_FailSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_InpRawMin`** | `REAL` | `4` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đầu vào thô tối thiểu (Raw Min) dùng để quy đổi tỷ lệ tuyến tính. (RU) |
| **`Cfg_InpRawMax`** | `REAL` | `20` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đầu vào thô tối đa (Raw Max) dùng để quy đổi tỷ lệ tuyến tính. (RU) |
| **`Cfg_PVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] PV (Output) Minimum for Scaling (EU) |
| **`Cfg_PVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tối đa (EU Max) của PV tương ứng với Raw Max. TIP The P_AIChan instruction supports reverse scaling; either the raw (input) or engineering (scaled) range can be reversed (maximum less than minimum). |
| **`Cfg_PVHiLim`** | `REAL` | `1.5000000000000001E+38` | Ẩn (Tag) | [Biến cấu hình thông số] PV High clamping threshold (engineering units). Valid = any float greater than or equal to Cfg_PVLoLim. |
| **`Cfg_PVLoLim`** | `REAL` | `-1.5000000000000001E+38` | Ẩn (Tag) | [Biến cấu hình thông số] PV Low clamping threshold (engineering units). Valid = any float less than or equal to Cfg_PVHiLim. |
| **`Cfg_InpOORHiLim`** | `REAL` | `20.733333999999999` | Ẩn (Tag) | [Biến cấu hình thông số] Out-of-Range (fail) high limit (raw units). |
| **`Cfg_InpOORLoLim`** | `REAL` | `3.6666666999999999` | Ẩn (Tag) | [Biến cấu hình thông số] Out-of-Range (fail) low limit (raw units). |
| **`Cfg_InpOORDB`** | `REAL` | `6.6666669999999997E-2` | Ẩn (Tag) | [Biến cấu hình thông số] Out-of-Range (fail) high/low deadband (raw units). |
| **`Cfg_InpOOROnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time out-of-range to raise status (seconds). |
| **`Cfg_InpOOROffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time in-range to clear out-of-range status (seconds). |
| **`Cfg_InpOORAction`** | `BOOL` | - | Ẩn (Tag) | [Biến cấu hình thông số] PV action on: Out-of-range, stuck (unchanging), PV not a number, I/O module fault, I/O channel fault, Inp_PVUncertain, function check, maintenance required, or AOI configuration error: 1 = Pass input PV through unchanged 2 = Hold last good PV value 3 = Replace PV value with Cfg_PVReplaceVal |
| **`Cfg_InpStuckAction`** | `BOOL` | - | Ẩn (Tag) | [Biến cấu hình thông số] PV action on: Out-of-range, stuck (unchanging), PV not a number, I/O module fault, I/O channel fault, Inp_PVUncertain, function check, maintenance required, or AOI configuration error: 1 = Pass input PV through unchanged 2 = Hold last good PV value 3 = Replace PV value with Cfg_PVReplaceVal |
| **`Cfg_InpNaNAction`** | `BOOL` | - | Ẩn (Tag) | [Biến cấu hình thông số] PV action on: Out-of-range, stuck (unchanging), PV not a number, I/O module fault, I/O channel fault, Inp_PVUncertain, function check, maintenance required, or AOI configuration error: 1 = Pass input PV through unchanged 2 = Hold last good PV value 3 = Replace PV value with Cfg_PVReplaceVal |
| **`Cfg_ModFaultAction`** | `BOOL` | - | Ẩn (Tag) | [Biến cấu hình thông số] PV action on: Out-of-range, stuck (unchanging), PV not a number, I/O module fault, I/O channel fault, Inp_PVUncertain, function check, maintenance required, or AOI configuration error: 1 = Pass input PV through unchanged 2 = Hold last good PV value 3 = Replace PV value with Cfg_PVReplaceVal |
| **`Cfg_ChanFaultAction`** | `BOOL` | - | Ẩn (Tag) | [Biến cấu hình thông số] PV action on: Out-of-range, stuck (unchanging), PV not a number, I/O module fault, I/O channel fault, Inp_PVUncertain, function check, maintenance required, or AOI configuration error: 1 = Pass input PV through unchanged 2 = Hold last good PV value 3 = Replace PV value with Cfg_PVReplaceVal |
| **`Cfg_OutOfSpecAction`** | `BOOL` | - | Ẩn (Tag) | [Biến cấu hình thông số] PV action on: Out-of-range, stuck (unchanging), PV not a number, I/O module fault, I/O channel fault, Inp_PVUncertain, function check, maintenance required, or AOI configuration error: 1 = Pass input PV through unchanged 2 = Hold last good PV value 3 = Replace PV value with Cfg_PVReplaceVal |
| **`Cfg_FuncCheckAction`** | `BOOL` | - | Ẩn (Tag) | [Biến cấu hình thông số] PV action on: Out-of-range, stuck (unchanging), PV not a number, I/O module fault, I/O channel fault, Inp_PVUncertain, function check, maintenance required, or AOI configuration error: 1 = Pass input PV through unchanged 2 = Hold last good PV value 3 = Replace PV value with Cfg_PVReplaceVal |
| **`Cfg_MaintReqdAction`** | `BOOL` | - | Ẩn (Tag) | [Biến cấu hình thông số] PV action on: Out-of-range, stuck (unchanging), PV not a number, I/O module fault, I/O channel fault, Inp_PVUncertain, function check, maintenance required, or AOI configuration error: 1 = Pass input PV through unchanged 2 = Hold last good PV value 3 = Replace PV value with Cfg_PVReplaceVal |
| **`Cfg_CfgErrAction`** | `BOOL` | - | Ẩn (Tag) | [Biến cấu hình thông số] PV action on: Out-of-range, stuck (unchanging), PV not a number, I/O module fault, I/O channel fault, Inp_PVUncertain, function check, maintenance required, or AOI configuration error: 1 = Pass input PV through unchanged 2 = Hold last good PV value 3 = Replace PV value with Cfg_PVReplaceVal |
| **`Cfg_InpOORQual`** | `BOOL` | - | Ẩn (Tag) | [Biến cấu hình thông số] PV quality to report for: Out-of-range, stuck (unchanging), PV not a number, I/O module fault, I/O channel fault, Inp_PVUncertain, function check, maintenance required, or AOI configuration error: 1 = Good 2 = Uncertain 3 = Bad |
| **`Cfg_InpStuckQual`** | `BOOL` | - | Ẩn (Tag) | [Biến cấu hình thông số] PV quality to report for: Out-of-range, stuck (unchanging), PV not a number, I/O module fault, I/O channel fault, Inp_PVUncertain, function check, maintenance required, or AOI configuration error: 1 = Good 2 = Uncertain 3 = Bad |
| **`Cfg_InpNaNQual`** | `BOOL` | - | Ẩn (Tag) | [Biến cấu hình thông số] PV quality to report for: Out-of-range, stuck (unchanging), PV not a number, I/O module fault, I/O channel fault, Inp_PVUncertain, function check, maintenance required, or AOI configuration error: 1 = Good 2 = Uncertain 3 = Bad |
| **`Cfg_ModFaultQual`** | `BOOL` | - | Ẩn (Tag) | [Biến cấu hình thông số] PV quality to report for: Out-of-range, stuck (unchanging), PV not a number, I/O module fault, I/O channel fault, Inp_PVUncertain, function check, maintenance required, or AOI configuration error: 1 = Good 2 = Uncertain 3 = Bad |
| **`Cfg_ChanFaultQual`** | `BOOL` | - | Ẩn (Tag) | [Biến cấu hình thông số] PV quality to report for: Out-of-range, stuck (unchanging), PV not a number, I/O module fault, I/O channel fault, Inp_PVUncertain, function check, maintenance required, or AOI configuration error: 1 = Good 2 = Uncertain 3 = Bad |
| **`Cfg_OutOfSpecQual`** | `BOOL` | - | Ẩn (Tag) | [Biến cấu hình thông số] PV quality to report for: Out-of-range, stuck (unchanging), PV not a number, I/O module fault, I/O channel fault, Inp_PVUncertain, function check, maintenance required, or AOI configuration error: 1 = Good 2 = Uncertain 3 = Bad |
| **`Cfg_FuncCheckQual`** | `BOOL` | - | Ẩn (Tag) | [Biến cấu hình thông số] PV quality to report for: Out-of-range, stuck (unchanging), PV not a number, I/O module fault, I/O channel fault, Inp_PVUncertain, function check, maintenance required, or AOI configuration error: 1 = Good 2 = Uncertain 3 = Bad |
| **`Cfg_MaintReqdQual`** | `BOOL` | - | Ẩn (Tag) | [Biến cấu hình thông số] PV quality to report for: Out-of-range, stuck (unchanging), PV not a number, I/O module fault, I/O channel fault, Inp_PVUncertain, function check, maintenance required, or AOI configuration error: 1 = Good 2 = Uncertain 3 = Bad |
| **`Cfg_CfgErrQual`** | `BOOL` | - | Ẩn (Tag) | [Biến cấu hình thông số] PV quality to report for: Out-of-range, stuck (unchanging), PV not a number, I/O module fault, I/O channel fault, Inp_PVUncertain, function check, maintenance required, or AOI configuration error: 1 = Good 2 = Uncertain 3 = Bad |
| **`Cfg_StuckT`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Time with no change in Input to raise Stuck Status (s) |
| **`Cfg_PVReplaceVal`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Value (engineering units) to use to replace PV when action = replace. |
| **`PCmd_FailAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_FailSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Analog Input Failure Alarm |
| **`PCmd_FailUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Analog Input Failure Alarm |
| **`PCmd_FailUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |

## 3. Bảng chi tiết 45 Biến Đầu Ra (`Output Parameters`) của `P_AIChan`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Val_InpRaw`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Analog Input Value (RU) (before scaling or checking) |
| **`Val`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Process Variable Value (EU) |
| **`Val_PVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cực tiểu của dải đo đơn vị kỹ thuật = MIN(Cfg_PVEUMin, Cfg_PVEUMax) |
| **`Val_PVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cực đại của dải đo đơn vị kỹ thuật = MAX(Cfg_PVEUMin, Cfg_PVEUMax) |
| **`SrcQ_IO`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final channel status source and quality. GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`Val_Sts`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device confirmed status: 0 = PV Good 5 = PV Uncertain 6 = PV Bad 7 = Substitute PV 33 = Disabled |
| **`Val_Fault`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device fault status: 0 = none 32 = Fail 34 = Configuration error |
| **`Val_NotifyAll`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |
| **`Val_UnackAlmC`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Sts_PVGood`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=PV Quality is Good (not flagged as Bad or Uncertain) |
| **`Sts_PVUncertain`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=PV Quality is flagged as Uncertain |
| **`Sts_PVBad`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=PV Quality is flagged as Bad |
| **`Sts_UseInp`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Using Input to calculate PV (not replaced or held) |
| **`Sts_HoldLast`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Analog PV being held at last good value |
| **`Sts_Clamped`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Analog PV being clamped at Low or High Limit |
| **`Sts_Replaced`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Analog PV being replaced with configured value |
| **`Sts_InpOORHiCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Input PV exceeds Out-of-Range High Limit |
| **`Sts_InpOORLoCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Input PV exceeds Out-of-Range Low limit |
| **`Sts_InpOutOfRange`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Input is Out of Range (includes On-Delay, Deadband, Off-Delay) |
| **`Sts_InpStuck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Input is "stuck" (unchanging) |
| **`Sts_InpNaN`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Input is Not a Number (floating point exception) |
| **`Sts_ModFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Module Fault condition |
| **`Sts_ChanFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Channel Fault condition |
| **`Sts_OutOfSpec`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Working outside specifications (from instrument) |
| **`Sts_FuncCheck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Function Check (PV simulated/replaced at instrument) |
| **`Sts_MaintReqd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Maintenance is Required (from instrument) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang bị Tạm hoãn (Shelved) hoặc Vô hiệu hóa (Disabled), hiển thị biểu tượng trên HMI |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình (kiểm tra các bit Err_* chi tiết để biết nguyên nhân) |
| **`Err_Raw`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị dải thô Cfg_InpRawMin bằng Cfg_InpRawMax |
| **`Err_EU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị dải kỹ thuật Cfg_PVEUMin bằng Cfg_PVEUMax |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Timer Preset (must be 0 to |
| **`Err_DB`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: a Status Deadband is < 0.0 |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thông số cảnh báo (Thời gian duy trì tối thiểu, thời gian Shelve, mức Severity) không hợp lệ |
| **`Err_Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: PV High Clamping is less than PV Low Clamping |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc lỗi ngắt tải (Shed) đã hết điều kiện lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Analog Input Failure (Bad, Uncertain) |
| **`Alm_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Cảnh báo lỗi ngõ vào Analog (Fail Alarm) (Bad, Uncertain) |
| **`Ack_Fail`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Cảnh báo lỗi ngõ vào Analog (Fail Alarm) đã được Xác nhận (Acknowledged) |
| **`Sts_FailDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi ngõ vào Analog (Fail Alarm) đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_FailSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi ngõ vào Analog (Fail Alarm) đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_FailShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi ngõ vào Analog (Fail Alarm) đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`P_AIChan`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 15 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_AIChan`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_8` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'Analog Channel Quality'` | [Biến cấu hình thông số] Description for display on HMI. This string is shown in the title bar of the faceplate. |
| **`Cfg_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Engineering Units for display on HMI |
| **`Cfg_Label`** | `STRING_20` | `'Analog In Channel'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI |
| **`Cfg_RU`** | `STRING_8` | `'mA DC'` | [Biến cấu hình thông số] Raw Units for display on HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_AIChan'` | [Biến cấu hình thông số] Tagname for display on HMI |
| **`Fail`** | `P_Alarm` | `{...}` | Khối con quản lý Cảnh báo lỗi ngõ vào Analog (chất lượng xấu hoặc vượt dải) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_AIChan'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI và phần mềm thông tin |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset all Alarms requiring Reset |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset and Xác nhận (Acknowledge) all Alarms |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1=At least one Alarm requires Reset |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1=At least one Alarm requires Reset or Acknowledgement |
| **`Val_Notify`** | `SINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận của riêng khối này (enum) |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_AInAdv`
**Thuộc chương:** Chương 1: Xử lý tín hiệu Vào/Ra (I/O Processing)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `IO Processing 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_AInAdv`:** **138** Biến đầu vào (`Input Parameters`) | **97** Biến đầu ra (`Output Parameters`) | **34** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_AInAdv`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_PV`, `Inp_PVSrcQ`, `Inp_PVNotify`, `Inp_PVBad`, `Inp_PVUncertain`, `Inp_Sim`, `Inp_HiHiGate`, `Inp_HiGate`, `Inp_LoGate`, `Inp_LoLoGate`, `Inp_HiRoCGate`, `Inp_HiDevGate`, `Inp_LoDevGate`, `Inp_FailGate`, `Inp_Reset`, `Cfg_Ref`, `Cfg_HiHiLim`, `Cfg_HiLim`...
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_CtrlHiDB`, `Cfg_CtrlHiHiDB`, `Cfg_CtrlHiHiLim`, `Cfg_CtrlHiLim`, `Cfg_CtrlLoDB`, `Cfg_CtrlLoLim`, `Cfg_CtrlLoLoDB`, `Cfg_CtrlLoLoLim`, `Cfg_UseSqRt`, `Cfg_HasMoreObj`, `Cfg_HasRoC`, `Cfg_HasDev`, `Cfg_NoSubstPV`, `Cfg_HasChanObj`, `Cfg_UseChanSrcQ`, `Cfg_PVDecPlcs`, `Cfg_HasHiHiAlm`, `Cfg_HasHiAlm`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_EU`, `Cfg_Label`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_AInAdv` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PCmd_ClearCapt`, `PCmd_Reset`, `PCmd_HiHiAck`, `PCmd_HiAck`, `PCmd_LoAck`, `PCmd_LoLoAck`, `PCmd_HiRoCAck`, `PCmd_HiDevAck`, `PCmd_LoDevAck`, `PCmd_FailAck`, `PCmd_HiHiSuppress`, `PCmd_HiSuppress`, `PCmd_LoSuppress`, `PCmd_LoLoSuppress`, `PCmd_FailSuppress`, `PCmd_HiHiUnsuppress`, `PCmd_HiUnsuppress`, `PCmd_LoUnsuppress`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Val`, `Val_InpPV`, `Val_RoC`, `Val_Dev`, `Val_PVMinCapt`, `Val_PVMaxCapt`, `Val_PVEUMin`, `Val_PVEUMax`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Sts_SubstPV`, `Sts_PVBad`, `Sts_PVUncertain`, `Sts_MaintByp`, `Sts_AlmInh`, `Sts_Err`, `Sts_RdyReset`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 138 Biến Đầu Vào (`Input Parameters`) của `P_AInAdv`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_PV`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu đo lường đầu vào (Biến quá trình PV) nối từ cảm biến / thẻ Analog Input |
| **`Inp_PVSrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã nguồn gốc và chất lượng tín hiệu đầu vào (nối từ chân SrcQ của khối P_AIChan nếu có). |
| **`Inp_PVNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã trạng thái mức độ ưu tiên và xác nhận cảnh báo của đối tượng PV liên kết. |
| **`Inp_PVBad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Bad Signal Quality/Communication Status for Input (1 = Bad, 0 = OK). If PV is read from an analog input, then this is normally read from the analog log channel fault status. |
| **`Inp_PVUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái chất lượng tín hiệu không chắc chắn (1 = Không chắc chắn/Uncertain, 0 = Tốt/OK), dùng để xuất ra cờ Sts_PVUncertain. |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Simulation input. When set to 1, the instruction uses simulation parameters (for example, Set_SimPV) to calculate output. When set to 0, the instruction uses input parameters (for example, Inp_PV) to calculate output. |
| **`Inp_HiHiGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] These parameters are the gate inputs used for status detection. When set to 1, the corresponding analog input threshold monitoring is enabled. When enabled, the threshold detection on-delay and off-delay timers are applied after the gate delay timer. When set to 0, detection is disabled and the corresponding status output is forced off. If the status is used as an alarm, this input acts as a suppression-by-design condition. |
| **`Inp_HiGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] These parameters are the gate inputs used for status detection. When set to 1, the corresponding analog input threshold monitoring is enabled. When enabled, the threshold detection on-delay and off-delay timers are applied after the gate delay timer. When set to 0, detection is disabled and the corresponding status output is forced off. If the status is used as an alarm, this input acts as a suppression-by-design condition. |
| **`Inp_LoGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] These parameters are the gate inputs used for status detection. When set to 1, the corresponding analog input threshold monitoring is enabled. When enabled, the threshold detection on-delay and off-delay timers are applied after the gate delay timer. When set to 0, detection is disabled and the corresponding status output is forced off. If the status is used as an alarm, this input acts as a suppression-by-design condition. |
| **`Inp_LoLoGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] These parameters are the gate inputs used for status detection. When set to 1, the corresponding analog input threshold monitoring is enabled. When enabled, the threshold detection on-delay and off-delay timers are applied after the gate delay timer. When set to 0, detection is disabled and the corresponding status output is forced off. If the status is used as an alarm, this input acts as a suppression-by-design condition. |
| **`Inp_HiRoCGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] These parameters are the gate inputs used for status detection. When set to 1, the corresponding analog input threshold monitoring is enabled. When enabled, the threshold detection on-delay and off-delay timers are applied after the gate delay timer. When set to 0, detection is disabled and the corresponding status output is forced off. If the status is used as an alarm, this input acts as a suppression-by-design condition. |
| **`Inp_HiDevGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] These parameters are the gate inputs used for status detection. When set to 1, the corresponding analog input threshold monitoring is enabled. When enabled, the threshold detection on-delay and off-delay timers are applied after the gate delay timer. When set to 0, detection is disabled and the corresponding status output is forced off. If the status is used as an alarm, this input acts as a suppression-by-design condition. |
| **`Inp_LoDevGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] These parameters are the gate inputs used for status detection. When set to 1, the corresponding analog input threshold monitoring is enabled. When enabled, the threshold detection on-delay and off-delay timers are applied after the gate delay timer. When set to 0, detection is disabled and the corresponding status output is forced off. If the status is used as an alarm, this input acts as a suppression-by-design condition. |
| **`Inp_FailGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] These parameters are the gate inputs used for status detection. When set to 1, the corresponding analog input threshold monitoring is enabled. When enabled, the threshold detection on-delay and off-delay timers are applied after the gate delay timer. When set to 0, detection is disabled and the corresponding status output is forced off. If the status is used as an alarm, this input acts as a suppression-by-design condition. |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân đầu vào dùng để xóa (Reset) tất cả các cảnh báo đang chờ Reset từ chương trình PLC (1 = Xóa cảnh báo). |
| **`Cfg_CtrlHiDB`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] High Control Deadband (EU) |
| **`Cfg_CtrlHiHiDB`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] High-High Control Deadband (EU) |
| **`Cfg_CtrlHiHiLim`** | `BOOL` | `1.5000000000000001E+38` | Ẩn (Tag) | [Biến cấu hình thông số] Current High-High Control Threshold |
| **`Cfg_CtrlHiLim`** | `BOOL` | `1.5000000000000001E+38` | Ẩn (Tag) | [Biến cấu hình thông số] Current High Control Threshold |
| **`Cfg_CtrlLoDB`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Low Control Deadband (EU) |
| **`Cfg_CtrlLoLim`** | `BOOL` | `-1.5000000000000001E+38` | Ẩn (Tag) | [Biến cấu hình thông số] Current Low Control Threshold |
| **`Cfg_CtrlLoLoDB`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Low-Low Control Deadband (EU) |
| **`Cfg_CtrlLoLoLim`** | `BOOL` | `-1.5000000000000001E+38` | Ẩn (Tag) | [Biến cấu hình thông số] Current Low-Low Control Threshold |
| **`Cfg_UseSqRt`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Use square root scaling characterization for input from a differential pressure instrument to linearize to flow units. 0 = Use linear scaling. |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_HasRoC`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Enables the rate of change calculation and High Rate of Change alarm. |
| **`Cfg_HasDev`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Enables reference entry and deviation calculation and alarms. |
| **`Cfg_NoSubstPV`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Disallow selection of Substitute PV |
| **`Cfg_HasChanObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng channel liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_UseChanSrcQ`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Truyền thẳng mã chất lượng SrcQ từ khối kênh P_AIChan sang; 0 = Tự tính toán mã SrcQ nội bộ. |
| **`Cfg_PVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho giá trị PV trên HMI (từ 0 đến 6 chữ số). |
| **`Cfg_HasHiHiAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasHiAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasLoAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasLoLoAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasHiRoCAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasHiDevAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasLoDevAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasFailAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HiHiResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_HiResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_LoResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_LoLoResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_HiRoCResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_HiDevResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_LoDevResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_FailResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_HiHiAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_HiAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_LoAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_LoLoAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_HiRoCAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_HiDevAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_LoDevAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_FailAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_HiHiSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_HiSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_LoSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_LoLoSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_HiRoCSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_HiDevSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_LoDevSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_FailSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_InpRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đầu vào thô tối thiểu (Raw Min) dùng để quy đổi tỷ lệ tuyến tính. |
| **`Cfg_InpRawMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đầu vào thô tối đa (Raw Max) dùng để quy đổi tỷ lệ tuyến tính. |
| **`Cfg_PVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] PV (Output) Minimum for Scaling to EU |
| **`Cfg_PVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] PV (Output) Maximum for Scaling to EU |
| **`Cfg_FiltTC`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] PV Filter Time Constant (sec), 0.0 = unfiltered |
| **`Cfg_RateTime`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] PV Rate of Change time base (sec) 60=/min, 3600=/hr |
| **`Cfg_Ref`** | `REAL` | `0` | Hiện (Pin) | [Biến cấu hình thông số] Analog Reference for Deviation Alarms (EU) |
| **`Cfg_HiHiLim`** | `REAL` | `1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Current High-High Status Threshold |
| **`Cfg_HiHiDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] High-High Status Deadband (EU) |
| **`Cfg_HiHiOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time above High-High Limit to raise Status (sec) |
| **`Cfg_HiHiOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time below High-High Limit (minus deadband) to clear Status (sec) |
| **`Cfg_HiHiGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] High-High Status Gate Delay (s) |
| **`Cfg_HiLim`** | `REAL` | `1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Current High Status Threshold |
| **`Cfg_HiDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] High Status Deadband (EU) |
| **`Cfg_HiOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time above High Limit to raise Status (sec) |
| **`Cfg_HiOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time below High Limit (minus deadband) to clear Status (sec) |
| **`Cfg_HiGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] High Status Gate Delay (s) |
| **`Cfg_LoLim`** | `REAL` | `-1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Current Low Status Threshold |
| **`Cfg_LoDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Low Status Deadband (EU) |
| **`Cfg_LoOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time below Low Limit to raise Status (sec) |
| **`Cfg_LoOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time above Low Limit (plus deadband) to clear Status (sec) |
| **`Cfg_LoGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Low Status Gate Delay (s) |
| **`Cfg_LoLoLim`** | `REAL` | `-1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Current Low-Low Status Threshold |
| **`Cfg_LoLoDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Low-Low Status Deadband (EU) |
| **`Cfg_LoLoOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time below Low-Low Limit to raise Status (sec) |
| **`Cfg_LoLoOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time above Low-Low Limit (plus deadband) to clear Status (sec) |
| **`Cfg_LoLoGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Low-Low Status Gate Delay (s) |
| **`Cfg_HiRoCLim`** | `REAL` | `1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Current High Rate of Change Status Threshold |
| **`Cfg_HiRoCDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] High Rate of Change Status Deadband (EU/rate time) |
| **`Cfg_HiRoCOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time above High Rate of Change Limit to raise Status (sec) |
| **`Cfg_HiRoCOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time below High Rate of Change Limit (minus deadband) to clear Status (sec) |
| **`Cfg_HiRoCGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] High Rate of Change Status Gate Delay (s) |
| **`Cfg_HiDevLim`** | `REAL` | `1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Current High Deviation Status Threshold |
| **`Cfg_HiDevDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] High Deviation Status Deadband (EU) |
| **`Cfg_HiDevOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time above High Deviation Limit to raise Status (sec) |
| **`Cfg_HiDevOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time below High Deviation Limit (minus deadband) to clear Status (sec) |
| **`Cfg_HiDevGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] High Deviation Status Gate Delay (s) |
| **`Cfg_LoDevLim`** | `REAL` | `1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Current Low Deviation Status Threshold |
| **`Cfg_LoDevDB`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Low Deviation Status Deadband (EU) |
| **`Cfg_LoDevOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time below Low Deviation Limit to raise Status (sec) |
| **`Cfg_LoDevOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time above Low Deviation Limit (plus deadband) to clear Status (sec) |
| **`Cfg_LoDevGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Low Deviation Status Gate Delay (s) |
| **`Cfg_FailHiLim`** | `REAL` | `103.958336` | Ẩn (Tag) | [Biến cấu hình thông số] Out-of-Range (fail) High Limit (EU) |
| **`Cfg_FailLoLim`** | `REAL` | `-2.0833333000000001` | Ẩn (Tag) | [Biến cấu hình thông số] Out-of-Range (fail) Low Limit (EU) |
| **`Cfg_FailDB`** | `REAL` | `0.41666666000000002` | Ẩn (Tag) | [Biến cấu hình thông số] Out-of-Range (fail) High/Low Deadband (EU) |
| **`Cfg_FailOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time Bad or Out of Range to raise Fail Status (sec) |
| **`Cfg_FailOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time OK or In Range to clear Fail Status (sec) |
| **`Cfg_FailGateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Fail Status Gate Delay (s) |
| **`MSet_SubstPV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ tab Bảo trì (Maintenance)] Giá trị PV thay thế do nhân viên bảo trì nhập (đơn vị kỹ thuật EU) |
| **`Set_SimPV`** | `REAL` | `0` | Ẩn (Tag) | PV used in simulation (Inp_Sim = 1) (engineering units). |
| **`PCmd_ClearCapt`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_ClearCapt = 1 để xóa giá trị cực tiểu/cực đại (Min/Max Peak) đã lưu của PV (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_Reset = 1 để xóa tất cả các cảnh báo chốt đang chờ Reset (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_HiHiAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_HiAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_LoAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_LoLoAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_HiRoCAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_HiDevAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_LoDevAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_FailAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_HiHiSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Cảnh báo Rất Cao (High-Cảnh báo Cao (High Alarm)) |
| **`PCmd_HiSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Cảnh báo Cao (High Alarm) |
| **`PCmd_LoSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Cảnh báo Thấp (Low Alarm) |
| **`PCmd_LoLoSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Cảnh báo Rất Thấp (Low-Cảnh báo Thấp (Low Alarm)) |
| **`PCmd_FailSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Analog Input Failure Alarm |
| **`PCmd_HiHiUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Cảnh báo Rất Cao (High-Cảnh báo Cao (High Alarm)) |
| **`PCmd_HiUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Cảnh báo Cao (High Alarm) |
| **`PCmd_LoUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Cảnh báo Thấp (Low Alarm) |
| **`PCmd_LoLoUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Cảnh báo Rất Thấp (Low-Cảnh báo Thấp (Low Alarm)) |
| **`PCmd_HiHiUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_HiUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_LoUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_LoLoUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_FailUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |

## 3. Bảng chi tiết 97 Biến Đầu Ra (`Output Parameters`) của `P_AInAdv`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Val`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Analog Input Value (incl. Manual Override, if used) |
| **`Val_InpPV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu vào thực tế (đã quy đổi EU nhưng trước khi qua bộ chọn giá trị thay thế Substitute PV) |
| **`Val_RoC`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Analog Value Rate of Change (EU/rate time) |
| **`Val_Dev`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Calculated Deviation from Reference (EU) |
| **`Val_PVMinCapt`** | `REAL` | `1.5000000000000001E+38` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị PV thấp nhất (Min Peak) ghi nhận được kể từ lần xóa gần nhất |
| **`Val_PVMaxCapt`** | `REAL` | `-1.5000000000000001E+38` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị PV cao nhất (Max Peak) ghi nhận được kể từ lần xóa gần nhất |
| **`Val_PVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cực tiểu của dải đo đơn vị kỹ thuật = MIN(Cfg_PVEUMin, Cfg_PVEUMax) |
| **`Val_PVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cực đại của dải đo đơn vị kỹ thuật = MAX(Cfg_PVEUMin, Cfg_PVEUMax) |
| **`SrcQ_IO`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final PV Source and Quality. GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`Val_Fault`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device fault status: 0 = None 20 = Low 21 = High 22 = Low Deviation 23 = High Deviation 24 = Low Low 25 = High High 26 = High Rate of Change 32 = Fail 34 = Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Val_NotifyAll`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận của khối này cộng với khối kênh (enum) |
| **`Val_UnackAlmC`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Sts_SubstPV`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Using Substitute PV (Input being overridden) |
| **`Sts_PVBad`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu PV có chất lượng xấu (Bad Quality) hoặc vượt ngoài dải giới hạn lỗi (Out of Range) |
| **`Sts_PVUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị PV có chất lượng không chắc chắn (Uncertain Quality) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang bị Tạm hoãn (Shelved) hoặc Vô hiệu hóa (Disabled), hiển thị biểu tượng trên HMI |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Error in Config: see detail bits for reason |
| **`Err_Raw`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị dải thô Cfg_InpRawMin bằng Cfg_InpRawMax |
| **`Err_EU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị dải kỹ thuật Cfg_PVEUMin bằng Cfg_PVEUMax |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: On-Delay, Off-Delay, Gate Delay Time Invalid (use 0 to 2147483 s) |
| **`Err_Rate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Invalid Rate Time (use 1.0, 60.0, 3600.0, 86400.0...) |
| **`Err_Filt`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=-Error in Config: PV filter params (RateTime, TC) |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thông số cảnh báo (Thời gian duy trì tối thiểu, thời gian Shelve, mức Severity) không hợp lệ |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc lỗi ngắt tải (Shed) đã hết điều kiện lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_HiHiCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV High-High kết quả so sánh ngưỡng trực tiếp 1=High-High |
| **`Sts_HiHiGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV High-High trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_HiHi`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị Analog vượt trên ngưỡng Rất Cao (High-High) |
| **`Alm_HiHi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Ngõ vào Analog đang ở trạng thái Cảnh báo Rất Cao (High-High) |
| **`Ack_HiHi`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Cảnh báo Rất Cao (Cảnh báo Rất Cao (High-Cảnh báo Cao (High Alarm))) đã được Xác nhận (Acknowledged) |
| **`Sts_HiHiDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Rất Cao (Cảnh báo Rất Cao (High-Cảnh báo Cao (High Alarm))) đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_HiHiShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Rất Cao (Cảnh báo Rất Cao (High-Cảnh báo Cao (High Alarm))) đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_HiHiSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Rất Cao (Cảnh báo Rất Cao (High-Cảnh báo Cao (High Alarm))) đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_HiCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV High kết quả so sánh ngưỡng trực tiếp 1=High |
| **`Sts_HiGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV High trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_Hi`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị Analog vượt trên ngưỡng Cao (High) |
| **`Alm_Hi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Ngõ vào Analog đang ở trạng thái Cảnh báo Cao (High) |
| **`Ack_Hi`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Cảnh báo Cao (Cảnh báo Cao (High Alarm)) đã được Xác nhận (Acknowledged) |
| **`Sts_HiDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Cao (Cảnh báo Cao (High Alarm)) đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_HiShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Cao (Cảnh báo Cao (High Alarm)) đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_HiSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Cao (Cảnh báo Cao (High Alarm)) đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_LoCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV Low kết quả so sánh ngưỡng trực tiếp 1=Low |
| **`Sts_LoGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV Low trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_Lo`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị Analog tụt dưới ngưỡng Thấp (Low) |
| **`Alm_Lo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Ngõ vào Analog đang ở trạng thái Cảnh báo Thấp (Low) |
| **`Ack_Lo`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Cảnh báo Thấp (Cảnh báo Thấp (Low Alarm)) đã được Xác nhận (Acknowledged) |
| **`Sts_LoDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Thấp (Cảnh báo Thấp (Low Alarm)) đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_LoShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Thấp (Cảnh báo Thấp (Low Alarm)) đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_LoSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Thấp (Cảnh báo Thấp (Low Alarm)) đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_LoLoCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV Low-Low kết quả so sánh ngưỡng trực tiếp 1=Low-Low |
| **`Sts_LoLoGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV Low-Low trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_LoLo`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị Analog tụt dưới ngưỡng Rất Thấp (Low-Low) |
| **`Alm_LoLo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Ngõ vào Analog đang ở trạng thái Cảnh báo Rất Thấp (Low-Low) |
| **`Ack_LoLo`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Cảnh báo Rất Thấp (Cảnh báo Rất Thấp (Low-Cảnh báo Thấp (Low Alarm))) đã được Xác nhận (Acknowledged) |
| **`Sts_LoLoDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Rất Thấp (Cảnh báo Rất Thấp (Low-Cảnh báo Thấp (Low Alarm))) đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_LoLoShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Rất Thấp (Cảnh báo Rất Thấp (Low-Cảnh báo Thấp (Low Alarm))) đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_LoLoSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Low Cảnh báo Thấp (Low Alarm) đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_HiRoCCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV High Rate of Change kết quả so sánh ngưỡng trực tiếp 1=High |
| **`Sts_HiRoCGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV High Rate of Change trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_HiRoC`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Analog Input Rate of Change đang vượt trên ngưỡng Cao (High) |
| **`Alm_HiRoC`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Analog Input Rate of Change is in Cảnh báo Cao (High Alarm) |
| **`Ack_HiRoC`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=High Rate of Change Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_HiRoCDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=High Rate of Change Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_HiRoCShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=High Rate of Change Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_HiRoCSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=High Rate of Change Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_HiDevCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV High Deviation kết quả so sánh ngưỡng trực tiếp 1=Dev High |
| **`Sts_HiDevGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV High Deviation trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_HiDev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Analog Input Deviation đang vượt trên ngưỡng Cao (High) |
| **`Alm_HiDev`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Analog Input Deviation is in Cảnh báo Cao (High Alarm) |
| **`Ack_HiDev`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=High Deviation Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_HiDevDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=High Deviation Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_HiDevShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=High Deviation Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_HiDevSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=High Deviation Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_LoDevCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV Low Deviation kết quả so sánh ngưỡng trực tiếp 1=Dev Low |
| **`Sts_LoDevGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV Low Deviation trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_LoDev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Analog Input Deviation đang tụt dưới ngưỡng Thấp (Low) |
| **`Alm_LoDev`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Analog Input Deviation is in Cảnh báo Thấp (Low Alarm) |
| **`Ack_LoDev`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Low Deviation Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_LoDevDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Low Deviation Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_LoDevShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Low Deviation Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_LoDevSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Low Deviation Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_FailCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV Fail kết quả so sánh ngưỡng trực tiếp 1=Out of Range |
| **`Sts_FailGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV Fail trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_Fail`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Ngõ vào Analog vượt ngoài dải đo hoặc chất lượng tín hiệu xấu (PV Bad) |
| **`Alm_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Cảnh báo lỗi ngõ vào Analog (Fail Alarm) (Bad or Out of Range) |
| **`Ack_Fail`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Cảnh báo lỗi ngõ vào Analog (Fail Alarm) đã được Xác nhận (Acknowledged) |
| **`Sts_FailDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi ngõ vào Analog (Fail Alarm) đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_FailShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi ngõ vào Analog (Fail Alarm) đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_FailSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi ngõ vào Analog (Fail Alarm) đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_CtrlHi`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Analog Input is above High Control limit |
| **`Sts_CtrlHiHi`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Analog Input is above High-High Control limit |
| **`Sts_CtrlLo`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Analog Input is above Low Control limit |
| **`Sts_CtrlLoLo`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Analog Input is above Low-Low Control limit |
| **`P_AInAdv`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 34 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_AInAdv`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_8` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'Advanced Analog Input'` | [Biến cấu hình thông số] Description for display on HMI. This string shows in the title bar of the faceplate. |
| **`Cfg_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Engineering Units for display on HMI |
| **`Cfg_Label`** | `STRING_20` | `'Analog Input'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_AinAdv'` | [Biến cấu hình thông số] Tagname for display on HMI |
| **`Fail`** | `P_Alarm` | `{...}` | Khối con quản lý Cảnh báo lỗi ngõ vào Analog (chất lượng xấu hoặc vượt dải) |
| **`FailGate`** | `P_Gate` | `{...}` | Khối con xử lý Cổng điều kiện và Trễ thời gian cho cảnh báo lỗi (Fail Gate) |
| **`Hi`** | `P_Alarm` | `{...}` | Cảnh báo Cao (High Alarm) |
| **`HiDev`** | `P_Alarm` | `{...}` | High Deviation Alarm |
| **`HiDevGate`** | `P_Gate` | `{...}` | High Deviation Alarm Gate / Delay Block |
| **`HiGate`** | `P_Gate` | `{...}` | Khối con xử lý Cổng điều kiện và Trễ thời gian cho cảnh báo Cao (Hi Gate) |
| **`HiHi`** | `P_Alarm` | `{...}` | Cảnh báo Rất Cao (High-Cảnh báo Cao (High Alarm)) |
| **`HiHiGate`** | `P_Gate` | `{...}` | Khối con xử lý Cổng điều kiện và Trễ thời gian cho cảnh báo Rất Cao (HiHi Gate) |
| **`HiRoC`** | `P_Alarm` | `{...}` | High Rate of Change Alarm |
| **`HiRoCGate`** | `P_Gate` | `{...}` | High Rate of Change Alarm Gate / Delay Block |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_AInAdv'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI và phần mềm thông tin |
| **`Lo`** | `P_Alarm` | `{...}` | Cảnh báo Thấp (Low Alarm) |
| **`LoDev`** | `P_Alarm` | `{...}` | Low Deviation Alarm |
| **`LoDevGate`** | `P_Gate` | `{...}` | Low Deviation Alarm Gate / Delay Block |
| **`LoGate`** | `P_Gate` | `{...}` | Khối con xử lý Cổng điều kiện và Trễ thời gian cho cảnh báo Thấp (Lo Gate) |
| **`LoLo`** | `P_Alarm` | `{...}` | Cảnh báo Rất Thấp (Low-Cảnh báo Thấp (Low Alarm)) |
| **`LoLoGate`** | `P_Gate` | `{...}` | Khối con xử lý Cổng điều kiện và Trễ thời gian cho cảnh báo Rất Thấp (LoLo Gate) |
| **`MCmd_InpPV`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Bảo trì chọn sử dụng tín hiệu PV thực tế (chế độ bình thường) |
| **`MCmd_SubstPV`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Bảo trì chọn sử dụng giá trị PV thay thế (ghi đè đầu vào) |
| **`MRdy_InpPV`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_InpPV (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_SubstPV`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_SubstPV (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`OCmd_ClearCapt`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Clear the captured min / max PV excursion values |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset all Alarms requiring Reset |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset and Xác nhận (Acknowledge) all Alarms |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`Val_Notify`** | `SINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận của riêng khối này (enum) |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_AInDual`
**Thuộc chương:** Chương 1: Xử lý tín hiệu Vào/Ra (I/O Processing)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `IO Processing 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_AInDual`:** **140** Biến đầu vào (`Input Parameters`) | **100** Biến đầu ra (`Output Parameters`) | **0** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_AInDual`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_PVA`, `Inp_PVB`, `Inp_PVASrcQ`, `Inp_PVANotify`, `Inp_PVBSrcQ`, `Inp_PVBNotify`, `Inp_PVABad`, `Inp_PVBBad`, `Inp_PVAUncertain`, `Inp_PVBUncertain`, `Inp_Sim`, `Inp_HiHiGate`, `Inp_HiGate`, `Inp_LoGate`, `Inp_LoLoGate`, `Inp_DiffGate`, `Inp_FailGate`, `Inp_Reset`...
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_NoSubstPV`, `Cfg_SetTrack`, `Cfg_HasChanObjA`, `Cfg_HasChanObjB`, `Cfg_UseChanSrcQA`, `Cfg_UseChanSrcQB`, `Cfg_PVDecPlcs`, `Cfg_HasHiHiAlm`, `Cfg_HasHiAlm`, `Cfg_HasLoAlm`, `Cfg_HasLoLoAlm`, `Cfg_HasOneGoodAlm`, `Cfg_HasNoneGoodAlm`, `Cfg_HasDiffAlm`, `Cfg_HasFailAlm`, `Cfg_HasMoreObj`, `Cfg_HiHiResetReqd`, `Cfg_HiResetReqd`...
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_AInDual` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PCmd_SelA`, `PCmd_SelB`, `PCmd_SelAvg`, `PCmd_SelMin`, `PCmd_SelMax`, `PCmd_ClearCapt`, `PCmd_Reset`, `PCmd_HiHiAck`, `PCmd_HiAck`, `PCmd_LoAck`, `PCmd_LoLoAck`, `PCmd_OneGoodAck`, `PCmd_NoneGoodAck`, `PCmd_DiffAck`, `PCmd_FailAck`, `PCmd_OneGoodSuppress`, `PCmd_NoneGoodSuppress`, `PCmd_DiffSuppress`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Val`, `Val_PVA`, `Val_PVB`, `Val_AvgPV`, `Val_MinPV`, `Val_MaxPV`, `Val_InpPV`, `Val_Diff`, `Val_PVMinCapt`, `Val_PVMaxCapt`, `Val_PVEUMin`, `Val_PVEUMax`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Sts_PVASel`, `Sts_PVBSel`, `Sts_AvgSel`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 140 Biến Đầu Vào (`Input Parameters`) của `P_AInDual`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_PVA`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Raw PV Signal from Sensor / Input A |
| **`Inp_PVB`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Raw PV Signal from Sensor / Input B |
| **`Inp_PVASrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Input Source and Quality (from Channel A object, if available) (enum) |
| **`Inp_PVANotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] PVA channel object alarm prio and ack status (enum) |
| **`Inp_PVBSrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Input Source and Quality (from Channel B object, if available) (enum) |
| **`Inp_PVBNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] PVB channel object alarm prio and ack status (enum) |
| **`Inp_PVABad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi tín hiệu / lỗi truyền thông đầu vào (1 = Lỗi/Bad, 0 = Tốt/OK). Thường nối trực tiếp từ bit báo lỗi kênh (Channel Fault) của thẻ Analog Input. |
| **`Inp_PVBBad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi tín hiệu / lỗi truyền thông đầu vào (1 = Lỗi/Bad, 0 = Tốt/OK). Thường nối trực tiếp từ bit báo lỗi kênh (Channel Fault) của thẻ Analog Input. |
| **`Inp_PVAUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Uncertain Quality for Inputs (1 = Uncertain, 0 = OK). This is optional status for the input that can be used to drive the status output (Sts_PVUncertain). |
| **`Inp_PVBUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Uncertain Quality for Inputs (1 = Uncertain, 0 = OK). This is optional status for the input that can be used to drive the status output (Sts_PVUncertain). |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Simulation input. When set to 1, the instruction uses simulation parameters (for example, Set_SimPVA) to calculate output. When set to 0, the instruction uses input parameters (for example, Inp_PVA) to calculate output. |
| **`Inp_HiHiGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] These parameters are the gate inputs used for status detection. When set to 1, the corresponding analog input threshold monitoring is enabled. When enabled, the threshold detection on-delay and off-delay timers are applied after the gate delay timer. When set to 0, detection is disabled and the corresponding status output is forced off. If the status is used as an alarm, this input acts as a suppression-by-design condition. |
| **`Inp_HiGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] These parameters are the gate inputs used for status detection. When set to 1, the corresponding analog input threshold monitoring is enabled. When enabled, the threshold detection on-delay and off-delay timers are applied after the gate delay timer. When set to 0, detection is disabled and the corresponding status output is forced off. If the status is used as an alarm, this input acts as a suppression-by-design condition. |
| **`Inp_LoGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] These parameters are the gate inputs used for status detection. When set to 1, the corresponding analog input threshold monitoring is enabled. When enabled, the threshold detection on-delay and off-delay timers are applied after the gate delay timer. When set to 0, detection is disabled and the corresponding status output is forced off. If the status is used as an alarm, this input acts as a suppression-by-design condition. |
| **`Inp_LoLoGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] These parameters are the gate inputs used for status detection. When set to 1, the corresponding analog input threshold monitoring is enabled. When enabled, the threshold detection on-delay and off-delay timers are applied after the gate delay timer. When set to 0, detection is disabled and the corresponding status output is forced off. If the status is used as an alarm, this input acts as a suppression-by-design condition. |
| **`Inp_DiffGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] These parameters are the gate inputs used for status detection. When set to 1, the corresponding analog input threshold monitoring is enabled. When enabled, the threshold detection on-delay and off-delay timers are applied after the gate delay timer. When set to 0, detection is disabled and the corresponding status output is forced off. If the status is used as an alarm, this input acts as a suppression-by-design condition. |
| **`Inp_FailGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] These parameters are the gate inputs used for status detection. When set to 1, the corresponding analog input threshold monitoring is enabled. When enabled, the threshold detection on-delay and off-delay timers are applied after the gate delay timer. When set to 0, detection is disabled and the corresponding status output is forced off. If the status is used as an alarm, this input acts as a suppression-by-design condition. |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Reset all Alarms requiring reset |
| **`Cfg_NoSubstPV`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Khóa tính năng nhập giá trị thay thế (Substitute PV) của Bảo trì: Khi = 0, cho phép nhân viên bảo trì nhập giá trị PV thay thế; khi = 1, cấm sử dụng tính năng Substitute PV. When Cfg_NoSubstPV is 0, the commands MCmd_InpPV and MCmd_SubstPV are used to select the input (live) PV or the substitute PV. Sts_SubstPV is 1 when the substitute PV đang được chọn làm Nguồn lệnh điều khiển hiện hành. |
| **`Cfg_SetTrack`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] This parameter is used to set up bumpless behavior of setting parameters when switching modes. When this parameter is 1, in Program mode the operator settings track the program settings; in Operator mode the program settings track the operator settings; and the simulation inputs match the output values (transitions are bumpless). When this parameter is 0, the operator settings and program settings are not modified by this instruction. In this case, when the mode is changed, the effective value of the setting can change depending on the program-set and operator-set values. |
| **`Cfg_HasChanObjA`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng channel liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasChanObjB`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng channel liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_UseChanSrcQA`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Pass thru connected Channel A's SrcQ value, 0=generate SrcQ |
| **`Cfg_UseChanSrcQB`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Pass thru connected Channel B's SrcQ value, 0=generate SrcQ |
| **`Cfg_PVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho giá trị PV trên HMI (từ 0 đến 6 chữ số). |
| **`Cfg_HasHiHiAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasHiAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasLoAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasLoLoAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasOneGoodAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasNoneGoodAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasDiffAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasFailAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_HiHiResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_HiResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_LoResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_LoLoResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_OneGoodResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_NoneGoodResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_DiffResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_FailResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_HiHiAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_HiAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_LoAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_LoLoAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_OneGoodAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_NoneGoodAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_DiffAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_FailAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_HiHiSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_HiSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_LoSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_LoLoSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_OneGoodSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_NoneGoodSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_DiffSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_FailSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_InpRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đầu vào thô tối thiểu (Raw Min) dùng để quy đổi tỷ lệ tuyến tính. |
| **`Cfg_InpRawMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đầu vào thô tối đa (Raw Max) dùng để quy đổi tỷ lệ tuyến tính. |
| **`Cfg_PVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tối thiểu (EU Min) của PV tương ứng với Raw Min. |
| **`Cfg_PVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tối đa (EU Max) của PV tương ứng với Raw Max. TIP The P_AInDual instruction supports reverse scaling; either the raw (Input) or engineering (Scaled) range can be reversed (maximum less than minimum). |
| **`Cfg_FiltTC`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] PV filter time constant (seconds), 0.0 = unfiltered. |
| **`Cfg_HiHiLim`** | `REAL` | `1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Current High-High Status Threshold |
| **`Cfg_HiHiDB`** | `Cfg_HiHiDB
Cfg_HiDB
Cfg_LoDB
Cfg_LoLoDB
Cfg_FailDB
Cfg_DiffDB` | `1.0
1.0
1.0
1.0
0.0
1.0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) cho ngưỡng cảnh báo để chống báo động giả lặp lại khi tín hiệu dao động quanh ngưỡng. |
| **`Cfg_HiDB`** | `Cfg_HiHiDB
Cfg_HiDB
Cfg_LoDB
Cfg_LoLoDB
Cfg_FailDB
Cfg_DiffDB` | `1.0
1.0
1.0
1.0
0.0
1.0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) cho ngưỡng cảnh báo để chống báo động giả lặp lại khi tín hiệu dao động quanh ngưỡng. |
| **`Cfg_LoDB`** | `Cfg_HiHiDB
Cfg_HiDB
Cfg_LoDB
Cfg_LoLoDB
Cfg_FailDB
Cfg_DiffDB` | `1.0
1.0
1.0
1.0
0.0
1.0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) cho ngưỡng cảnh báo để chống báo động giả lặp lại khi tín hiệu dao động quanh ngưỡng. |
| **`Cfg_LoLoDB`** | `Cfg_HiHiDB
Cfg_HiDB
Cfg_LoDB
Cfg_LoLoDB
Cfg_FailDB
Cfg_DiffDB` | `1.0
1.0
1.0
1.0
0.0
1.0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) cho ngưỡng cảnh báo để chống báo động giả lặp lại khi tín hiệu dao động quanh ngưỡng. |
| **`Cfg_FailDB`** | `Cfg_HiHiDB
Cfg_HiDB
Cfg_LoDB
Cfg_LoLoDB
Cfg_FailDB
Cfg_DiffDB` | `1.0
1.0
1.0
1.0
0.0
1.0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) cho ngưỡng cảnh báo để chống báo động giả lặp lại khi tín hiệu dao động quanh ngưỡng. |
| **`Cfg_DiffDB`** | `Cfg_HiHiDB
Cfg_HiDB
Cfg_LoDB
Cfg_LoLoDB
Cfg_FailDB
Cfg_DiffDB` | `1.0
1.0
1.0
1.0
0.0
1.0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) cho ngưỡng cảnh báo để chống báo động giả lặp lại khi tín hiệu dao động quanh ngưỡng. |
| **`Cfg_HiHiGateDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng. |
| **`Cfg_HiGateDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng. |
| **`Cfg_LoGateDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng. |
| **`Cfg_LoLoGateDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng. |
| **`Cfg_DiffGateDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng. |
| **`Cfg_FailGateDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng. |
| **`Cfg_HiHiOnDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây): Tín hiệu phải vượt ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/cảnh báo (chống nhiễu thoáng qua). |
| **`Cfg_HiOnDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây): Tín hiệu phải vượt ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/cảnh báo (chống nhiễu thoáng qua). |
| **`Cfg_DiffOnDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây): Tín hiệu phải vượt ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/cảnh báo (chống nhiễu thoáng qua). |
| **`Cfg_LoOnDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây): Tín hiệu phải vượt ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/cảnh báo (chống nhiễu thoáng qua). |
| **`Cfg_LoLoOnDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây): Tín hiệu phải vượt ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/cảnh báo (chống nhiễu thoáng qua). |
| **`Cfg_FailOnDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây): Tín hiệu phải vượt ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/cảnh báo (chống nhiễu thoáng qua). |
| **`Cfg_HiHiOffDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the amount of time (in seconds) the PV must stay within each status threshold to clear the status. Off delay times are used to reduce chattering alarms. EXAMPLE: If Cfg_HiOffDly is 5 seconds, the output (Val) must be below the status limit (Val_HiHiLim) minus deadband (Cfg_HiHiDB) for 5 seconds before the status is returned to normal. |
| **`Cfg_LoOffDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the amount of time (in seconds) the PV must stay within each status threshold to clear the status. Off delay times are used to reduce chattering alarms. EXAMPLE: If Cfg_HiOffDly is 5 seconds, the output (Val) must be below the status limit (Val_HiHiLim) minus deadband (Cfg_HiHiDB) for 5 seconds before the status is returned to normal. |
| **`Cfg_LoLoOffDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the amount of time (in seconds) the PV must stay within each status threshold to clear the status. Off delay times are used to reduce chattering alarms. EXAMPLE: If Cfg_HiOffDly is 5 seconds, the output (Val) must be below the status limit (Val_HiHiLim) minus deadband (Cfg_HiHiDB) for 5 seconds before the status is returned to normal. |
| **`Cfg_FailOffDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the amount of time (in seconds) the PV must stay within each status threshold to clear the status. Off delay times are used to reduce chattering alarms. EXAMPLE: If Cfg_HiOffDly is 5 seconds, the output (Val) must be below the status limit (Val_HiHiLim) minus deadband (Cfg_HiHiDB) for 5 seconds before the status is returned to normal. |
| **`Cfg_HiOffDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the amount of time (in seconds) the PV must stay within each status threshold to clear the status. Off delay times are used to reduce chattering alarms. EXAMPLE: If Cfg_HiOffDly is 5 seconds, the output (Val) must be below the status limit (Val_HiHiLim) minus deadband (Cfg_HiHiDB) for 5 seconds before the status is returned to normal. |
| **`Cfg_DiffOffDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the amount of time (in seconds) the PV must stay within each status threshold to clear the status. Off delay times are used to reduce chattering alarms. EXAMPLE: If Cfg_HiOffDly is 5 seconds, the output (Val) must be below the status limit (Val_HiHiLim) minus deadband (Cfg_HiHiDB) for 5 seconds before the status is returned to normal. |
| **`Cfg_HiLim`** | `REAL` | `1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Current High Status Threshold |
| **`Cfg_LoLim`** | `REAL` | `-1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Current Low Status Threshold |
| **`Cfg_LoLoLim`** | `REAL` | `-1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Current Low-Low Status Threshold |
| **`Cfg_DiffLim`** | `REAL` | `1.5000000000000001E+38` | Ẩn (Tag) | [Biến cấu hình thông số] Signal difference alarm limit (engineering units-absolute delta). |
| **`Cfg_FailHiLim`** | `REAL` | `103.958336` | Ẩn (Tag) | [Biến cấu hình thông số] Out-of-range (fail) High limit (engineering units). |
| **`Cfg_FailLoLim`** | `REAL` | `-2.0833333000000001` | Ẩn (Tag) | [Biến cấu hình thông số] Ngưỡng giới hạn báo lỗi vượt dải dưới / đứt dây (Out-of-Range Low, tính theo đơn vị thô Raw). |
| **`Cfg_OperKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Operator keeps control in Program Mode: .0=PV Selection, .1=Thresholds |
| **`Cfg_ProgKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Program keeps control in Operator Mode: .0=PV Selection, .1=Thresholds |
| **`MSet_SubstPV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ tab Bảo trì (Maintenance)] Giá trị PV thay thế do nhân viên bảo trì nhập (đơn vị kỹ thuật EU) |
| **`Set_SimPVA`** | `REAL` | `0` | Ẩn (Tag) | PVs when in Simulation (Inp_Sim = 1) (engineering units). EXAMPLE: Val is set based on Set_SimPVA when Inp_Sim is set, otherwise it’s set based on Inp_PVA. |
| **`Set_SimPVB`** | `REAL` | `0` | Ẩn (Tag) | PVs when in Simulation (Inp_Sim = 1) (engineering units). EXAMPLE: Val is set based on Set_SimPVA when Inp_Sim is set, otherwise it’s set based on Inp_PVA. |
| **`PCmd_SelA`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Select Sensor A PV |
| **`PCmd_SelB`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Select Sensor B PV |
| **`PCmd_SelAvg`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Select Avg(A,B) PV |
| **`PCmd_SelMin`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Select Min(A,B) PV |
| **`PCmd_SelMax`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Select Max(A,B) PV |
| **`PCmd_ClearCapt`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_ClearCapt = 1 để xóa giá trị cực tiểu/cực đại (Min/Max Peak) đã lưu của PV (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_Reset = 1 để xóa tất cả các cảnh báo chốt đang chờ Reset (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_HiHiAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_HiAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_LoAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_LoLoAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_OneGoodAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_NoneGoodAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_DiffAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_FailAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_OneGoodSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Only One Good Signal Alarm |
| **`PCmd_NoneGoodSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) No Good Signal Alarm |
| **`PCmd_DiffSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Signal Difference Alarm |
| **`PCmd_FailSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Total Signal Failure Alarm |
| **`PCmd_HiHiSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Cảnh báo Rất Cao (High-Cảnh báo Cao (High Alarm)) |
| **`PCmd_HiSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Cảnh báo Cao (High Alarm) |
| **`PCmd_LoSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Cảnh báo Thấp (Low Alarm) |
| **`PCmd_LoLoSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Cảnh báo Rất Thấp (Low-Cảnh báo Thấp (Low Alarm)) |
| **`PCmd_FailSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Analog Input Failure Alarm |
| **`PCmd_HiHiUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Cảnh báo Rất Cao (High-Cảnh báo Cao (High Alarm)) |
| **`PCmd_HiUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Cảnh báo Cao (High Alarm) |
| **`PCmd_LoUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Cảnh báo Thấp (Low Alarm) |
| **`PCmd_LoLoUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Cảnh báo Rất Thấp (Low-Cảnh báo Thấp (Low Alarm)) |
| **`PCmd_OneGoodUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Only One Good Signal Alarm |
| **`PCmd_NoneGoodUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) No Good Signal Alarm |
| **`PCmd_DiffUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Signal Difference Alarm |
| **`PCmd_FailUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Total Signal Failure Alarm |
| **`PCmd_HiHiUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Cảnh báo Rất Cao (High-Cảnh báo Cao (High Alarm)) |
| **`PCmd_HiUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Cảnh báo Cao (High Alarm) |
| **`PCmd_LoUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Cảnh báo Thấp (Low Alarm) |
| **`PCmd_LoLoUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Cảnh báo Rất Thấp (Low-Cảnh báo Thấp (Low Alarm)) |
| **`PCmd_OneGoodUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Only One Good Signal Alarm |
| **`PCmd_NoneGoodUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) No Good Signal Alarm |
| **`PCmd_DiffUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Signal Difference Alarm |
| **`PCmd_FailUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Total Signal Failure Alarm |

## 3. Bảng chi tiết 100 Biến Đầu Ra (`Output Parameters`) của `P_AInDual`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Val`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Selected Analog PV (incl. Subst PV, if used) (EU) |
| **`Val_PVA`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Analog Value (actual) from Input A (EU) |
| **`Val_PVB`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Analog Value (actual) from Input B (EU) |
| **`Val_AvgPV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Analog Value Average of Input A and Input B (EU) |
| **`Val_MinPV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Analog Value Minimum of Input A and Input B (EU) |
| **`Val_MaxPV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Analog Value Maximum of Input A and Input B (EU) |
| **`Val_InpPV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Selected PV (before substitution, etc.) (EU) |
| **`Val_Diff`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Difference between Input A and Input B PVs (EU) |
| **`Val_PVMinCapt`** | `REAL` | `1.5000000000000001E+38` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị PV thấp nhất (Min Peak) ghi nhận được kể từ lần xóa gần nhất |
| **`Val_PVMaxCapt`** | `REAL` | `-1.5000000000000001E+38` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị PV cao nhất (Max Peak) ghi nhận được kể từ lần xóa gần nhất |
| **`Val_PVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cực tiểu của dải đo đơn vị kỹ thuật = MIN(Cfg_PVEUMin, Cfg_PVEUMax) |
| **`Val_PVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cực đại của dải đo đơn vị kỹ thuật = MAX(Cfg_PVEUMin, Cfg_PVEUMax) |
| **`SrcQ_IOA`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ_IOB`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ_IO`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final PV Source and Quality. GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`Val_Fault`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device fault status: 0 = None 20 = Low 21 = High 22 = Low Deviation 23 = High Deviation 24 = Low Low 25 = High High 26 = High Rate of Change 32 = Fail 34 = Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Val_NotifyAll`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Highest Alarm prio and ack status this object + channels (enum) |
| **`Val_UnackAlmC`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Sts_PVASel`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Input A selected for PV |
| **`Sts_PVBSel`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Input B selected as PV |
| **`Sts_AvgSel`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Avg(A,B) selected as PV |
| **`Sts_MinSel`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Min(A,B) selected as PV |
| **`Sts_MaxSel`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Max(A,B) selected as PV |
| **`Sts_SubstPV`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Using Substitute PV (Input being overridden) |
| **`Sts_PVBad`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu PV có chất lượng xấu (Bad Quality) hoặc vượt ngoài dải giới hạn lỗi (Out of Range) |
| **`Sts_PVUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị PV có chất lượng không chắc chắn (Uncertain Quality) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang bị Tạm hoãn (Shelved) hoặc Vô hiệu hóa (Disabled), hiển thị biểu tượng trên HMI |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Error in Config, see detail bits for reason |
| **`Err_Raw`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị dải thô Cfg_InpRawMin bằng Cfg_InpRawMax |
| **`Err_EU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị dải kỹ thuật Cfg_PVEUMin bằng Cfg_PVEUMax |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Min Duration Time Invalid (use 0 to 2147483 sec) |
| **`Err_Filt`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=-Error in Config: PV filter params (RateTime, TC) |
| **`Err_DB`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: a Status Deadband is < 0.0 |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thông số cảnh báo (Thời gian duy trì tối thiểu, thời gian Shelve, mức Severity) không hợp lệ |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc lỗi ngắt tải (Shed) đã hết điều kiện lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_HiHiCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV High-High kết quả so sánh ngưỡng trực tiếp 1=High-High |
| **`Sts_HiHiGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV High-High trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_HiHi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị Analog vượt trên ngưỡng Rất Cao (High-High) |
| **`Alm_HiHi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Ngõ vào Analog đang ở trạng thái Cảnh báo Rất Cao (High-High) |
| **`Ack_HiHi`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Cảnh báo Rất Cao (Cảnh báo Rất Cao (High-Cảnh báo Cao (High Alarm))) đã được Xác nhận (Acknowledged) |
| **`Sts_HiHiDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Rất Cao (Cảnh báo Rất Cao (High-Cảnh báo Cao (High Alarm))) đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_HiHiSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Rất Cao (Cảnh báo Rất Cao (High-Cảnh báo Cao (High Alarm))) đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_HiHiShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Rất Cao (Cảnh báo Rất Cao (High-Cảnh báo Cao (High Alarm))) đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_HiCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV High kết quả so sánh ngưỡng trực tiếp 1=High |
| **`Sts_HiGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV High trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_Hi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị Analog vượt trên ngưỡng Cao (High) |
| **`Alm_Hi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Ngõ vào Analog đang ở trạng thái Cảnh báo Cao (High) |
| **`Ack_Hi`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Cảnh báo Cao (Cảnh báo Cao (High Alarm)) đã được Xác nhận (Acknowledged) |
| **`Sts_HiDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Cao (Cảnh báo Cao (High Alarm)) đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_HiSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Cao (Cảnh báo Cao (High Alarm)) đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_HiShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Cao (Cảnh báo Cao (High Alarm)) đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_LoCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV Low kết quả so sánh ngưỡng trực tiếp 1=Low |
| **`Sts_LoGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV Low trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_Lo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị Analog tụt dưới ngưỡng Thấp (Low) |
| **`Alm_Lo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Ngõ vào Analog đang ở trạng thái Cảnh báo Thấp (Low) |
| **`Ack_Lo`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Cảnh báo Thấp (Cảnh báo Thấp (Low Alarm)) đã được Xác nhận (Acknowledged) |
| **`Sts_LoDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Thấp (Cảnh báo Thấp (Low Alarm)) đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_LoSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Thấp (Cảnh báo Thấp (Low Alarm)) đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_LoShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Thấp (Cảnh báo Thấp (Low Alarm)) đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_LoLoCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV Low-Low kết quả so sánh ngưỡng trực tiếp 1=Low-Low |
| **`Sts_LoLoGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV Low-Low trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_LoLo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị Analog tụt dưới ngưỡng Rất Thấp (Low-Low) |
| **`Alm_LoLo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Ngõ vào Analog đang ở trạng thái Cảnh báo Rất Thấp (Low-Low) |
| **`Ack_LoLo`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Cảnh báo Rất Thấp (Cảnh báo Rất Thấp (Low-Cảnh báo Thấp (Low Alarm))) đã được Xác nhận (Acknowledged) |
| **`Sts_LoLoDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Rất Thấp (Cảnh báo Rất Thấp (Low-Cảnh báo Thấp (Low Alarm))) đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_LoLoSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Rất Thấp (Cảnh báo Rất Thấp (Low-Cảnh báo Thấp (Low Alarm))) đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_LoLoShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Rất Thấp (Cảnh báo Rất Thấp (Low-Cảnh báo Thấp (Low Alarm))) đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_OneGood`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Only One Good PV (other is Bad or Uncertain) |
| **`Alm_OneGood`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Only One Good PV Alarm |
| **`Ack_OneGood`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Only One Good PV Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_OneGoodDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Only One Good PV Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_OneGoodSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Only One Good PV Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_OneGoodShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Only One Good PV Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_NoneGood`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=No Good PV (both Bad, or one Bad / one Uncertain) |
| **`Alm_NoneGood`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=No Good PV Alarm |
| **`Ack_NoneGood`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=No Good PV Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_NoneGoodDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=No Good PV Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_NoneGoodSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=No Good PV Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_NoneGoodShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=No Good PV Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_DiffCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Signal Difference kết quả so sánh ngưỡng trực tiếp 1=High Difference |
| **`Sts_DiffGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Signal Difference trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_Diff`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=High Signal Difference detected |
| **`Alm_Diff`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Signal Difference Alarm |
| **`Ack_Diff`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Signal Difference Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_DiffDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Signal Difference Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_DiffSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Signal Difference Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_DiffShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Signal Difference Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_FailCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV Fail kết quả so sánh ngưỡng trực tiếp 1=Out of Range |
| **`Sts_FailGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV Fail trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Total Signal Failure (borth Bad or Out of Range) |
| **`Alm_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Total Signal Failure Alarm |
| **`Ack_Fail`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Total Signal Failure Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_FailDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Total Signal Failure Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_FailSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Total Signal Failure Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_FailShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Total Signal Failure Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`P_AInDual`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_AInMulti`
**Thuộc chương:** Chương 1: Xử lý tín hiệu Vào/Ra (I/O Processing)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `IO Processing 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_AInMulti`:** **173** Biến đầu vào (`Input Parameters`) | **110** Biến đầu ra (`Output Parameters`) | **38** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_AInMulti`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_PVA`, `Inp_PVB`, `Inp_PVC`, `Inp_PVD`, `Inp_PVE`, `Inp_PVF`, `Inp_PVG`, `Inp_PVH`, `Inp_PVASrcQ`, `Inp_PVANotify`, `Inp_PVBSrcQ`, `Inp_PVBNotify`, `Inp_PVCSrcQ`, `Inp_PVCNotify`, `Inp_PVDSrcQ`, `Inp_PVDNotify`, `Inp_PVESrcQ`, `Inp_PVENotify`...
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_HasMoreObj`, `Cfg_HasPVA`, `Cfg_HasPVB`, `Cfg_HasPVC`, `Cfg_HasPVD`, `Cfg_HasPVE`, `Cfg_HasPVF`, `Cfg_HasPVG`, `Cfg_HasPVH`, `Cfg_UsePVA`, `Cfg_UsePVB`, `Cfg_UsePVC`, `Cfg_UsePVD`, `Cfg_UsePVE`, `Cfg_UsePVF`, `Cfg_UsePVG`, `Cfg_UsePVH`, `Cfg_RejectUncertain`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_EU`, `Cfg_Label`, `Cfg_PVATag`, `Cfg_PVBTag`, `Cfg_PVCTag`, `Cfg_PVDTag`, `Cfg_PVETag`, `Cfg_PVFTag`, `Cfg_PVGTag`, `Cfg_PVHTag`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_AInMulti` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PCmd_ClearCapt`, `PCmd_Reset`, `PCmd_HiHiAck`, `PCmd_HiAck`, `PCmd_LoAck`, `PCmd_LoLoAck`, `PCmd_AnyRejectAck`, `PCmd_MinGoodAck`, `PCmd_FailAck`, `PCmd_HiHiSuppress`, `PCmd_HiSuppress`, `PCmd_LoSuppress`, `PCmd_LoLoSuppress`, `PCmd_AnyRejectSuppress`, `PCmd_MinGoodSuppress`, `PCmd_FailSuppress`, `PCmd_HiHiUnsuppress`, `PCmd_HiUnsuppress`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Cfg_HiHiLim`, `Cfg_HiLim`, `Cfg_LoLoLim`, `Val`, `Val_InpPV`, `Val_PVA`, `Val_PVB`, `Val_PVC`, `Val_PVD`, `Val_PVE`, `Val_PVF`, `Val_PVG`, `Val_PVH`, `Val_PVMinCapt`, `Val_PVMaxCapt`, `Val_PVEUMin`, `Val_PVEUMax`, `Val_NumPVs`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 173 Biến Đầu Vào (`Input Parameters`) của `P_AInMulti`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_PVA`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Raw PV signals from the sensors/inputs. These values are typically read from an analog input module. Use the HasPV bits (for example, Cfg_HasPVA) to enable/disable these inputs. |
| **`Inp_PVB`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Raw PV signals from the sensors/inputs. These values are typically read from an analog input module. Use the HasPV bits (for example, Cfg_HasPVA) to enable/disable these inputs. |
| **`Inp_PVC`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Raw PV signals from the sensors/inputs. These values are typically read from an analog input module. Use the HasPV bits (for example, Cfg_HasPVA) to enable/disable these inputs. |
| **`Inp_PVD`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Raw PV signals from the sensors/inputs. These values are typically read from an analog input module. Use the HasPV bits (for example, Cfg_HasPVA) to enable/disable these inputs. |
| **`Inp_PVE`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Raw PV signals from the sensors/inputs. These values are typically read from an analog input module. Use the HasPV bits (for example, Cfg_HasPVA) to enable/disable these inputs. |
| **`Inp_PVF`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Raw PV signals from the sensors/inputs. These values are typically read from an analog input module. Use the HasPV bits (for example, Cfg_HasPVA) to enable/disable these inputs. |
| **`Inp_PVG`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Raw PV signals from the sensors/inputs. These values are typically read from an analog input module. Use the HasPV bits (for example, Cfg_HasPVA) to enable/disable these inputs. |
| **`Inp_PVH`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Raw PV signals from the sensors/inputs. These values are typically read from an analog input module. Use the HasPV bits (for example, Cfg_HasPVA) to enable/disable these inputs. |
| **`Inp_PVASrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã nguồn gốc và chất lượng tín hiệu đầu vào (nối từ chân SrcQ của khối P_AIChan nếu có). |
| **`Inp_PVANotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] PVA channel object alarm prio and ack status (enum) |
| **`Inp_PVBSrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã nguồn gốc và chất lượng tín hiệu đầu vào (nối từ chân SrcQ của khối P_AIChan nếu có). |
| **`Inp_PVBNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] PVB channel object alarm prio and ack status (enum) |
| **`Inp_PVCSrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã nguồn gốc và chất lượng tín hiệu đầu vào (nối từ chân SrcQ của khối P_AIChan nếu có). |
| **`Inp_PVCNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] PVC channel object alarm prio and ack status (enum) |
| **`Inp_PVDSrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã nguồn gốc và chất lượng tín hiệu đầu vào (nối từ chân SrcQ của khối P_AIChan nếu có). |
| **`Inp_PVDNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] PVD channel object alarm prio and ack status (enum) |
| **`Inp_PVESrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã nguồn gốc và chất lượng tín hiệu đầu vào (nối từ chân SrcQ của khối P_AIChan nếu có). |
| **`Inp_PVENotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] PVE channel object alarm prio and ack status (enum) |
| **`Inp_PVFSrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã nguồn gốc và chất lượng tín hiệu đầu vào (nối từ chân SrcQ của khối P_AIChan nếu có). |
| **`Inp_PVFNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] PVF channel object alarm prio and ack status (enum) |
| **`Inp_PVGSrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã nguồn gốc và chất lượng tín hiệu đầu vào (nối từ chân SrcQ của khối P_AIChan nếu có). |
| **`Inp_PVGNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] PVG channel object alarm prio and ack status (enum) |
| **`Inp_PVHSrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã nguồn gốc và chất lượng tín hiệu đầu vào (nối từ chân SrcQ của khối P_AIChan nếu có). |
| **`Inp_PVHNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] PVH channel object alarm prio and ack status (enum) |
| **`Inp_PVABad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi tín hiệu / lỗi truyền thông đầu vào (1 = Lỗi/Bad, 0 = Tốt/OK). Thường nối trực tiếp từ bit báo lỗi kênh (Channel Fault) của thẻ Analog Input. EXAMPLE: Inp_PVABad is connected to the channel fault status for the input connected to Inp_PVA. |
| **`Inp_PVBBad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi tín hiệu / lỗi truyền thông đầu vào (1 = Lỗi/Bad, 0 = Tốt/OK). Thường nối trực tiếp từ bit báo lỗi kênh (Channel Fault) của thẻ Analog Input. EXAMPLE: Inp_PVABad is connected to the channel fault status for the input connected to Inp_PVA. |
| **`Inp_PVCBad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi tín hiệu / lỗi truyền thông đầu vào (1 = Lỗi/Bad, 0 = Tốt/OK). Thường nối trực tiếp từ bit báo lỗi kênh (Channel Fault) của thẻ Analog Input. EXAMPLE: Inp_PVABad is connected to the channel fault status for the input connected to Inp_PVA. |
| **`Inp_PVDBad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi tín hiệu / lỗi truyền thông đầu vào (1 = Lỗi/Bad, 0 = Tốt/OK). Thường nối trực tiếp từ bit báo lỗi kênh (Channel Fault) của thẻ Analog Input. EXAMPLE: Inp_PVABad is connected to the channel fault status for the input connected to Inp_PVA. |
| **`Inp_PVEBad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi tín hiệu / lỗi truyền thông đầu vào (1 = Lỗi/Bad, 0 = Tốt/OK). Thường nối trực tiếp từ bit báo lỗi kênh (Channel Fault) của thẻ Analog Input. EXAMPLE: Inp_PVABad is connected to the channel fault status for the input connected to Inp_PVA. |
| **`Inp_PVFBad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi tín hiệu / lỗi truyền thông đầu vào (1 = Lỗi/Bad, 0 = Tốt/OK). Thường nối trực tiếp từ bit báo lỗi kênh (Channel Fault) của thẻ Analog Input. EXAMPLE: Inp_PVABad is connected to the channel fault status for the input connected to Inp_PVA. |
| **`Inp_PVGBad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi tín hiệu / lỗi truyền thông đầu vào (1 = Lỗi/Bad, 0 = Tốt/OK). Thường nối trực tiếp từ bit báo lỗi kênh (Channel Fault) của thẻ Analog Input. EXAMPLE: Inp_PVABad is connected to the channel fault status for the input connected to Inp_PVA. |
| **`Inp_PVHBad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi tín hiệu / lỗi truyền thông đầu vào (1 = Lỗi/Bad, 0 = Tốt/OK). Thường nối trực tiếp từ bit báo lỗi kênh (Channel Fault) của thẻ Analog Input. EXAMPLE: Inp_PVABad is connected to the channel fault status for the input connected to Inp_PVA. |
| **`Inp_PVAUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Uncertain Quality for Inputs (1 = Uncertain, 0 = OK). This is optional status for the input that can be used to drive the status of the output (Sts_PVUncertain). If Cfg_RejectUncertain is set to true, input signals with an uncertain status are treated as bad and are rejected from the PV calculation. |
| **`Inp_PVBUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Uncertain Quality for Inputs (1 = Uncertain, 0 = OK). This is optional status for the input that can be used to drive the status of the output (Sts_PVUncertain). If Cfg_RejectUncertain is set to true, input signals with an uncertain status are treated as bad and are rejected from the PV calculation. |
| **`Inp_PVCUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Uncertain Quality for Inputs (1 = Uncertain, 0 = OK). This is optional status for the input that can be used to drive the status of the output (Sts_PVUncertain). If Cfg_RejectUncertain is set to true, input signals with an uncertain status are treated as bad and are rejected from the PV calculation. |
| **`Inp_PVDUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Uncertain Quality for Inputs (1 = Uncertain, 0 = OK). This is optional status for the input that can be used to drive the status of the output (Sts_PVUncertain). If Cfg_RejectUncertain is set to true, input signals with an uncertain status are treated as bad and are rejected from the PV calculation. |
| **`Inp_PVEUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Uncertain Quality for Inputs (1 = Uncertain, 0 = OK). This is optional status for the input that can be used to drive the status of the output (Sts_PVUncertain). If Cfg_RejectUncertain is set to true, input signals with an uncertain status are treated as bad and are rejected from the PV calculation. |
| **`Inp_PVFUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Uncertain Quality for Inputs (1 = Uncertain, 0 = OK). This is optional status for the input that can be used to drive the status of the output (Sts_PVUncertain). If Cfg_RejectUncertain is set to true, input signals with an uncertain status are treated as bad and are rejected from the PV calculation. |
| **`Inp_PVGUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Uncertain Quality for Inputs (1 = Uncertain, 0 = OK). This is optional status for the input that can be used to drive the status of the output (Sts_PVUncertain). If Cfg_RejectUncertain is set to true, input signals with an uncertain status are treated as bad and are rejected from the PV calculation. |
| **`Inp_PVHUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Uncertain Quality for Inputs (1 = Uncertain, 0 = OK). This is optional status for the input that can be used to drive the status of the output (Sts_PVUncertain). If Cfg_RejectUncertain is set to true, input signals with an uncertain status are treated as bad and are rejected from the PV calculation. |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Simulation input. When set to 1, the instruction uses simulation parameters (for example, Set_SimPVA) to calculate output. When set to 0, the instruction uses input parameters (for example, Inp_PVA) to calculate output. |
| **`Inp_HiHiGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] These parameters are the gate inputs used for status detection. When set to 1, the corresponding analog input threshold monitoring is enabled. When enabled, the threshold detection on-delay and off-delay timers are applied after the gate delay timer. When set to 0, detection is disabled and the corresponding status output is forced off. If the status is used as an alarm, this input acts as a suppression-by-design condition. |
| **`Inp_HiGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] These parameters are the gate inputs used for status detection. When set to 1, the corresponding analog input threshold monitoring is enabled. When enabled, the threshold detection on-delay and off-delay timers are applied after the gate delay timer. When set to 0, detection is disabled and the corresponding status output is forced off. If the status is used as an alarm, this input acts as a suppression-by-design condition. |
| **`Inp_LoGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] These parameters are the gate inputs used for status detection. When set to 1, the corresponding analog input threshold monitoring is enabled. When enabled, the threshold detection on-delay and off-delay timers are applied after the gate delay timer. When set to 0, detection is disabled and the corresponding status output is forced off. If the status is used as an alarm, this input acts as a suppression-by-design condition. |
| **`Inp_LoLoGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] These parameters are the gate inputs used for status detection. When set to 1, the corresponding analog input threshold monitoring is enabled. When enabled, the threshold detection on-delay and off-delay timers are applied after the gate delay timer. When set to 0, detection is disabled and the corresponding status output is forced off. If the status is used as an alarm, this input acts as a suppression-by-design condition. |
| **`Inp_FailGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] These parameters are the gate inputs used for status detection. When set to 1, the corresponding analog input threshold monitoring is enabled. When enabled, the threshold detection on-delay and off-delay timers are applied after the gate delay timer. When set to 0, detection is disabled and the corresponding status output is forced off. If the status is used as an alarm, this input acts as a suppression-by-design condition. |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân đầu vào dùng để xóa (Reset) tất cả các cảnh báo đang chờ Reset từ chương trình PLC (1 = Xóa cảnh báo). |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_HasPVA`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Set these parameters to 1 if the corresponding PV input is wired and is to be used in the calculations. Set these parameters to 0 if the corresponding PV input is not connected or is not to be used in the calculations. This configuration indicates the engineering intent to use each PV input in calculating the output (Val). Use the UsePV bits (for example, Cfg_UsePVA) to exclude the input temporarily for maintenance purposes. |
| **`Cfg_HasPVB`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Set these parameters to 1 if the corresponding PV input is wired and is to be used in the calculations. Set these parameters to 0 if the corresponding PV input is not connected or is not to be used in the calculations. This configuration indicates the engineering intent to use each PV input in calculating the output (Val). Use the UsePV bits (for example, Cfg_UsePVA) to exclude the input temporarily for maintenance purposes. |
| **`Cfg_HasPVC`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Set these parameters to 1 if the corresponding PV input is wired and is to be used in the calculations. Set these parameters to 0 if the corresponding PV input is not connected or is not to be used in the calculations. This configuration indicates the engineering intent to use each PV input in calculating the output (Val). Use the UsePV bits (for example, Cfg_UsePVA) to exclude the input temporarily for maintenance purposes. |
| **`Cfg_HasPVD`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Set these parameters to 1 if the corresponding PV input is wired and is to be used in the calculations. Set these parameters to 0 if the corresponding PV input is not connected or is not to be used in the calculations. This configuration indicates the engineering intent to use each PV input in calculating the output (Val). Use the UsePV bits (for example, Cfg_UsePVA) to exclude the input temporarily for maintenance purposes. |
| **`Cfg_HasPVE`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Set these parameters to 1 if the corresponding PV input is wired and is to be used in the calculations. Set these parameters to 0 if the corresponding PV input is not connected or is not to be used in the calculations. This configuration indicates the engineering intent to use each PV input in calculating the output (Val). Use the UsePV bits (for example, Cfg_UsePVA) to exclude the input temporarily for maintenance purposes. |
| **`Cfg_HasPVF`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Set these parameters to 1 if the corresponding PV input is wired and is to be used in the calculations. Set these parameters to 0 if the corresponding PV input is not connected or is not to be used in the calculations. This configuration indicates the engineering intent to use each PV input in calculating the output (Val). Use the UsePV bits (for example, Cfg_UsePVA) to exclude the input temporarily for maintenance purposes. |
| **`Cfg_HasPVG`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Set these parameters to 1 if the corresponding PV input is wired and is to be used in the calculations. Set these parameters to 0 if the corresponding PV input is not connected or is not to be used in the calculations. This configuration indicates the engineering intent to use each PV input in calculating the output (Val). Use the UsePV bits (for example, Cfg_UsePVA) to exclude the input temporarily for maintenance purposes. |
| **`Cfg_HasPVH`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Set these parameters to 1 if the corresponding PV input is wired and is to be used in the calculations. Set these parameters to 0 if the corresponding PV input is not connected or is not to be used in the calculations. This configuration indicates the engineering intent to use each PV input in calculating the output (Val). Use the UsePV bits (for example, Cfg_UsePVA) to exclude the input temporarily for maintenance purposes. |
| **`Cfg_UsePVA`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Set these parameters to 1 to include the corresponding PV input in the calculation. Set these parameters to 0 to exclude the corresponding PV input from the calculation. This configuration is typically used for taking a measurement out of service temporarily for maintenance. |
| **`Cfg_UsePVB`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Set these parameters to 1 to include the corresponding PV input in the calculation. Set these parameters to 0 to exclude the corresponding PV input from the calculation. This configuration is typically used for taking a measurement out of service temporarily for maintenance. |
| **`Cfg_UsePVC`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Set these parameters to 1 to include the corresponding PV input in the calculation. Set these parameters to 0 to exclude the corresponding PV input from the calculation. This configuration is typically used for taking a measurement out of service temporarily for maintenance. |
| **`Cfg_UsePVD`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Set these parameters to 1 to include the corresponding PV input in the calculation. Set these parameters to 0 to exclude the corresponding PV input from the calculation. This configuration is typically used for taking a measurement out of service temporarily for maintenance. |
| **`Cfg_UsePVE`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Set these parameters to 1 to include the corresponding PV input in the calculation. Set these parameters to 0 to exclude the corresponding PV input from the calculation. This configuration is typically used for taking a measurement out of service temporarily for maintenance. |
| **`Cfg_UsePVF`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Set these parameters to 1 to include the corresponding PV input in the calculation. Set these parameters to 0 to exclude the corresponding PV input from the calculation. This configuration is typically used for taking a measurement out of service temporarily for maintenance. |
| **`Cfg_UsePVG`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Set these parameters to 1 to include the corresponding PV input in the calculation. Set these parameters to 0 to exclude the corresponding PV input from the calculation. This configuration is typically used for taking a measurement out of service temporarily for maintenance. |
| **`Cfg_UsePVH`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Set these parameters to 1 to include the corresponding PV input in the calculation. Set these parameters to 0 to exclude the corresponding PV input from the calculation. This configuration is typically used for taking a measurement out of service temporarily for maintenance. |
| **`Cfg_RejectUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] This parameter is used to determine behavior when an input status is set uncertain. When this parameter is 1, an input that is flagged as uncertain is rejected and not used in calculating the final PV. When this parameter is 0, an input that is flagged as uncertain is not rejected and still is used in calculating the final PV, but it causes the final PV status to be set as uncertain. |
| **`Cfg_UseStdDev`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine how to validate input values. When this parameter is 1, an input is rejected if it is more than two standard deviations from the mean. When this parameter is 0, an input is rejected if it deviates from the mean by more than the value defined by Cfg_AbsDevLim. IMPORTANT: At least four inputs must be used for the setting of 1 to be meaningful. |
| **`Cfg_CalcAvg`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine how the output (Val) is calculated. When this parameter is 1, the calculated final PV is the average (arithmetic mean) of all the good (non-rejected) PV inputs. When this parameter is 0, the calculated final PV is the median (central value) of all the good (non-rejected) PV inputs. If there are an even number of inputs, the median is calculated as the average of the two central values. If there are only two unrejected inputs, the output calculation is determined by parameter Cfg_CalcWhen2. |
| **`Cfg_NoSubstPV`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Khóa tính năng nhập giá trị thay thế (Substitute PV) của Bảo trì: Khi = 0, cho phép nhân viên bảo trì nhập giá trị PV thay thế; khi = 1, cấm sử dụng tính năng Substitute PV. When Cfg_NoSubstPV is 0, the commands MCmd_InpPV and MCmd_SubstPV are used to select the input (live) PV or the substitute PV. Sts_SubstPV is 1 when the substitute PV đang được chọn làm Nguồn lệnh điều khiển hiện hành. |
| **`Cfg_HasChanObjA`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Tells HMI a Channel object (AIChan, etc.) is used for Inp_PVA…Inp_PVH. IMPORTANT: The name of the Channel object in the controller must be this object's name with the suffix ‘_Chan’ plus the input letter (A…H). This applies to each of the eight channels (A…H). For example, if your P_AInMulti object has the name ‘AInMulti123’, then its Channel A object must be named ‘AInMulti123_ChanA’. |
| **`Cfg_HasChanObjB`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Tells HMI a Channel object (AIChan, etc.) is used for Inp_PVA…Inp_PVH. IMPORTANT: The name of the Channel object in the controller must be this object's name with the suffix ‘_Chan’ plus the input letter (A…H). This applies to each of the eight channels (A…H). For example, if your P_AInMulti object has the name ‘AInMulti123’, then its Channel A object must be named ‘AInMulti123_ChanA’. |
| **`Cfg_HasChanObjC`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Tells HMI a Channel object (AIChan, etc.) is used for Inp_PVA…Inp_PVH. IMPORTANT: The name of the Channel object in the controller must be this object's name with the suffix ‘_Chan’ plus the input letter (A…H). This applies to each of the eight channels (A…H). For example, if your P_AInMulti object has the name ‘AInMulti123’, then its Channel A object must be named ‘AInMulti123_ChanA’. |
| **`Cfg_HasChanObjD`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Tells HMI a Channel object (AIChan, etc.) is used for Inp_PVA…Inp_PVH. IMPORTANT: The name of the Channel object in the controller must be this object's name with the suffix ‘_Chan’ plus the input letter (A…H). This applies to each of the eight channels (A…H). For example, if your P_AInMulti object has the name ‘AInMulti123’, then its Channel A object must be named ‘AInMulti123_ChanA’. |
| **`Cfg_HasChanObjE`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Tells HMI a Channel object (AIChan, etc.) is used for Inp_PVA…Inp_PVH. IMPORTANT: The name of the Channel object in the controller must be this object's name with the suffix ‘_Chan’ plus the input letter (A…H). This applies to each of the eight channels (A…H). For example, if your P_AInMulti object has the name ‘AInMulti123’, then its Channel A object must be named ‘AInMulti123_ChanA’. |
| **`Cfg_HasChanObjF`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Tells HMI a Channel object (AIChan, etc.) is used for Inp_PVA…Inp_PVH. IMPORTANT: The name of the Channel object in the controller must be this object's name with the suffix ‘_Chan’ plus the input letter (A…H). This applies to each of the eight channels (A…H). For example, if your P_AInMulti object has the name ‘AInMulti123’, then its Channel A object must be named ‘AInMulti123_ChanA’. |
| **`Cfg_HasChanObjG`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Tells HMI a Channel object (AIChan, etc.) is used for Inp_PVA…Inp_PVH. IMPORTANT: The name of the Channel object in the controller must be this object's name with the suffix ‘_Chan’ plus the input letter (A…H). This applies to each of the eight channels (A…H). For example, if your P_AInMulti object has the name ‘AInMulti123’, then its Channel A object must be named ‘AInMulti123_ChanA’. |
| **`Cfg_HasChanObjH`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Tells HMI a Channel object (AIChan, etc.) is used for Inp_PVA…Inp_PVH. IMPORTANT: The name of the Channel object in the controller must be this object's name with the suffix ‘_Chan’ plus the input letter (A…H). This applies to each of the eight channels (A…H). For example, if your P_AInMulti object has the name ‘AInMulti123’, then its Channel A object must be named ‘AInMulti123_ChanA’. |
| **`Cfg_PVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho giá trị PV trên HMI (từ 0 đến 6 chữ số). |
| **`Cfg_HasHiHiAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasHiAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasLoAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasLoLoAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasAnyRejectAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasMinGoodAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasFailAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HiHiResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_HiResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_LoResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_LoLoResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_AnyRejectResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_MinGoodResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_FailResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_HiHiAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_HiAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_LoAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_LoLoAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_AnyRejectAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_MinGoodAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_FailAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_MinGood`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Min. Good inputs for good PV [1..number of "Cfg_Has" inputs] |
| **`Cfg_CalcWhen2`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] PV Calculation when only 2 good inputs: 0=Average, 1=Minimum, 2=Maximum |
| **`Cfg_HiHiSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_HiSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_LoSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_LoLoSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_AnyRejectSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_MinGoodSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_FailSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_InpRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters must be set to the range of the signal connected to the Inp_PV inputs (for example, Inp_PVA). The inputs are then scaled to the values set by Cfg_PVEUMin and Cfg_PVEUMax. |
| **`Cfg_InpRawMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters must be set to the range of the signal connected to the Inp_PV inputs (for example, Inp_PVA). The inputs are then scaled to the values set by Cfg_PVEUMin and Cfg_PVEUMax. |
| **`Cfg_PVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters must be set to match the PV range of the input signals (for example, Inp_PVA) in engineering units. EXAMPLE: If your input card provides signals in the range of 4…20, and these values represent 0…250 °C , P_AInMulti can scale the inputs for you by setting Cfg_InpRawMin to 4, Cfg_InpRawMax to 20, Cfg_PVEUMin to 0, and Cfg_PVEUMax to 250. TIP: The P_AinMulti instruction supports reverse scaling; either the raw (input) or engineering (scaled) range can be reversed (maximum less than minimum). |
| **`Cfg_PVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters must be set to match the PV range of the input signals (for example, Inp_PVA) in engineering units. EXAMPLE: If your input card provides signals in the range of 4…20, and these values represent 0…250 °C , P_AInMulti can scale the inputs for you by setting Cfg_InpRawMin to 4, Cfg_InpRawMax to 20, Cfg_PVEUMin to 0, and Cfg_PVEUMax to 250. TIP: The P_AinMulti instruction supports reverse scaling; either the raw (input) or engineering (scaled) range can be reversed (maximum less than minimum). |
| **`Cfg_AbsDevLim`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Absolute Deviation Threshold in engineering units (engineering units). When Cfg_UseStdDev is configured to 0, an input is rejected if it deviates from the mean by this threshold. |
| **`Cfg_FiltTC`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters set the filter time constant (in seconds) for a first-order (lag) filter applied to the output (Val). The filter is applied after scaling and before alarm checking. A value of 0.0 means the PV is unfiltered. |
| **`Cfg_HiHiDB`** | `BOOL` | `1.0
1.0
1.0
1.0
0.41666666` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters set the deadband (hysterisis) that is applied to each alarm limit, used to prevent a noisy signal from generating spurious alarms. EXAMPLE: If the High alarm is enabled (Cfg_HasHiAlm = 1), the Cảnh báo Cao (High Alarm) Limit (Val_HiLim) is 90 and the Cảnh báo Cao (High Alarm) Deadband (Cfg_HiDB) is 5, the high alarm is generated when the output (Val) rises above 90 and is cleared once the output (Val) falls below 85 (90 minus 5). |
| **`Cfg_HiDB`** | `BOOL` | `1.0
1.0
1.0
1.0
0.41666666` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters set the deadband (hysterisis) that is applied to each alarm limit, used to prevent a noisy signal from generating spurious alarms. EXAMPLE: If the High alarm is enabled (Cfg_HasHiAlm = 1), the Cảnh báo Cao (High Alarm) Limit (Val_HiLim) is 90 and the Cảnh báo Cao (High Alarm) Deadband (Cfg_HiDB) is 5, the high alarm is generated when the output (Val) rises above 90 and is cleared once the output (Val) falls below 85 (90 minus 5). |
| **`Cfg_LoDB`** | `BOOL` | `1.0
1.0
1.0
1.0
0.41666666` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters set the deadband (hysterisis) that is applied to each alarm limit, used to prevent a noisy signal from generating spurious alarms. EXAMPLE: If the High alarm is enabled (Cfg_HasHiAlm = 1), the Cảnh báo Cao (High Alarm) Limit (Val_HiLim) is 90 and the Cảnh báo Cao (High Alarm) Deadband (Cfg_HiDB) is 5, the high alarm is generated when the output (Val) rises above 90 and is cleared once the output (Val) falls below 85 (90 minus 5). |
| **`Cfg_LoLoDB`** | `BOOL` | `1.0
1.0
1.0
1.0
0.41666666` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters set the deadband (hysterisis) that is applied to each alarm limit, used to prevent a noisy signal from generating spurious alarms. EXAMPLE: If the High alarm is enabled (Cfg_HasHiAlm = 1), the Cảnh báo Cao (High Alarm) Limit (Val_HiLim) is 90 and the Cảnh báo Cao (High Alarm) Deadband (Cfg_HiDB) is 5, the high alarm is generated when the output (Val) rises above 90 and is cleared once the output (Val) falls below 85 (90 minus 5). |
| **`Cfg_FailDB`** | `BOOL` | `1.0
1.0
1.0
1.0
0.41666666` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters set the deadband (hysterisis) that is applied to each alarm limit, used to prevent a noisy signal from generating spurious alarms. EXAMPLE: If the High alarm is enabled (Cfg_HasHiAlm = 1), the Cảnh báo Cao (High Alarm) Limit (Val_HiLim) is 90 and the Cảnh báo Cao (High Alarm) Deadband (Cfg_HiDB) is 5, the high alarm is generated when the output (Val) rises above 90 and is cleared once the output (Val) falls below 85 (90 minus 5). |
| **`Cfg_HiHiGateDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng. |
| **`Cfg_HiGateDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng. |
| **`Cfg_LoGateDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng. |
| **`Cfg_LoLoGateDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng. |
| **`Cfg_FailGateDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng. |
| **`Cfg_HiHiOnDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây): Tín hiệu phải vượt ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/cảnh báo (chống nhiễu thoáng qua). |
| **`Cfg_HiOnDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây): Tín hiệu phải vượt ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/cảnh báo (chống nhiễu thoáng qua). |
| **`Cfg_LoOnDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây): Tín hiệu phải vượt ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/cảnh báo (chống nhiễu thoáng qua). |
| **`Cfg_LoLoOnDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây): Tín hiệu phải vượt ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/cảnh báo (chống nhiễu thoáng qua). |
| **`Cfg_FailOnDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây): Tín hiệu phải vượt ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/cảnh báo (chống nhiễu thoáng qua). |
| **`Cfg_HiHiOffDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây): Tín hiệu phải trở về vùng an toàn (đã trừ Deadband) liên tục đủ thời gian này mới xóa trạng thái/cảnh báo. |
| **`Cfg_HiOffDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây): Tín hiệu phải trở về vùng an toàn (đã trừ Deadband) liên tục đủ thời gian này mới xóa trạng thái/cảnh báo. |
| **`Cfg_LoOffDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây): Tín hiệu phải trở về vùng an toàn (đã trừ Deadband) liên tục đủ thời gian này mới xóa trạng thái/cảnh báo. |
| **`Cfg_LoLoOffDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây): Tín hiệu phải trở về vùng an toàn (đã trừ Deadband) liên tục đủ thời gian này mới xóa trạng thái/cảnh báo. |
| **`Cfg_FailOffDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây): Tín hiệu phải trở về vùng an toàn (đã trừ Deadband) liên tục đủ thời gian này mới xóa trạng thái/cảnh báo. |
| **`Cfg_FailHiLim`** | `BOOL` | `103.958336
-2.0833333` | Ẩn (Tag) | [Biến cấu hình thông số] These limits determine what range of input values (in scaled engineering units) generate a Fail alarm indicating a signal failure. When the scaled input (for example, Val_PVA) is above the Fail Hi Limit (Cfg_FailHiLim) or below the Fail Lo Limit (Cfg_FailLoLim) and the Fail alarm is enabled (Cfg_FailAlm = 1), the sensor is regarded as failed, is rejected from the calculation, and generates an alarm. If used, set failure limits outside the configured expected engineering unit range (Cfg_PVEUMin and Cfg_PVEUMax). |
| **`Cfg_FailLoLim`** | `BOOL` | `103.958336
-2.0833333` | Ẩn (Tag) | [Biến cấu hình thông số] These limits determine what range of input values (in scaled engineering units) generate a Fail alarm indicating a signal failure. When the scaled input (for example, Val_PVA) is above the Fail Hi Limit (Cfg_FailHiLim) or below the Fail Lo Limit (Cfg_FailLoLim) and the Fail alarm is enabled (Cfg_FailAlm = 1), the sensor is regarded as failed, is rejected from the calculation, and generates an alarm. If used, set failure limits outside the configured expected engineering unit range (Cfg_PVEUMin and Cfg_PVEUMax). |
| **`MSet_SubstPV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ tab Bảo trì (Maintenance)] Maintenance-entered Substitute PV in engineering units. |
| **`Set_SimPVA`** | `REAL` | `0` | Ẩn (Tag) | PVs when in Simulation (Inp_Sim=1) in engineering units. EXAMPLE: Val is set based on Set_SimPVA when Inp_Sim is set, otherwise it is set based on Inp_PVA. |
| **`Set_SimPVB`** | `REAL` | `0` | Ẩn (Tag) | PVs when in Simulation (Inp_Sim=1) in engineering units. EXAMPLE: Val is set based on Set_SimPVA when Inp_Sim is set, otherwise it is set based on Inp_PVA. |
| **`Set_SimPVC`** | `REAL` | `0` | Ẩn (Tag) | PVs when in Simulation (Inp_Sim=1) in engineering units. EXAMPLE: Val is set based on Set_SimPVA when Inp_Sim is set, otherwise it is set based on Inp_PVA. |
| **`Set_SimPVD`** | `REAL` | `0` | Ẩn (Tag) | PVs when in Simulation (Inp_Sim=1) in engineering units. EXAMPLE: Val is set based on Set_SimPVA when Inp_Sim is set, otherwise it is set based on Inp_PVA. |
| **`Set_SimPVE`** | `REAL` | `0` | Ẩn (Tag) | PVs when in Simulation (Inp_Sim=1) in engineering units. EXAMPLE: Val is set based on Set_SimPVA when Inp_Sim is set, otherwise it is set based on Inp_PVA. |
| **`Set_SimPVF`** | `REAL` | `0` | Ẩn (Tag) | PVs when in Simulation (Inp_Sim=1) in engineering units. EXAMPLE: Val is set based on Set_SimPVA when Inp_Sim is set, otherwise it is set based on Inp_PVA. |
| **`Set_SimPVG`** | `REAL` | `0` | Ẩn (Tag) | PVs when in Simulation (Inp_Sim=1) in engineering units. EXAMPLE: Val is set based on Set_SimPVA when Inp_Sim is set, otherwise it is set based on Inp_PVA. |
| **`Set_SimPVH`** | `REAL` | `0` | Ẩn (Tag) | PVs when in Simulation (Inp_Sim=1) in engineering units. EXAMPLE: Val is set based on Set_SimPVA when Inp_Sim is set, otherwise it is set based on Inp_PVA. |
| **`PCmd_ClearCapt`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_ClearCapt = 1 để xóa giá trị cực tiểu/cực đại (Min/Max Peak) đã lưu của PV (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_Reset = 1 để xóa tất cả các cảnh báo chốt đang chờ Reset (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_HiHiAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_HiAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_LoAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_LoLoAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_AnyRejectAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_MinGoodAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_FailAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_HiHiSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Cảnh báo Rất Cao (High-Cảnh báo Cao (High Alarm)) |
| **`PCmd_HiSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Cảnh báo Cao (High Alarm) |
| **`PCmd_LoSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Cảnh báo Thấp (Low Alarm) |
| **`PCmd_LoLoSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Cảnh báo Rất Thấp (Low-Cảnh báo Thấp (Low Alarm)) |
| **`PCmd_AnyRejectSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Any Input Rejected Alarm |
| **`PCmd_MinGoodSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Min Good PV Inputs Alarm |
| **`PCmd_FailSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Total Signal Failure Alarm |
| **`PCmd_HiHiUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Cảnh báo Rất Cao (High-Cảnh báo Cao (High Alarm)) |
| **`PCmd_HiUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Cảnh báo Cao (High Alarm) |
| **`PCmd_LoUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Cảnh báo Thấp (Low Alarm) |
| **`PCmd_LoLoUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Cảnh báo Rất Thấp (Low-Cảnh báo Thấp (Low Alarm)) |
| **`PCmd_AnyRejectUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Any Input Rejected Alarm |
| **`PCmd_MinGoodUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Min Good PV Inputs Alarm |
| **`PCmd_FailUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Total Signal Failure Alarm |
| **`PCmd_HiHiUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_HiUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_LoUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_LoLoUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_FailUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |

## 3. Bảng chi tiết 110 Biến Đầu Ra (`Output Parameters`) của `P_AInMulti`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Cfg_HiHiLim`** | `REAL` | `1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] High-High Status Threshold |
| **`Cfg_HiLim`** | `REAL` | `1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] High Status Threshold |
| **`Cfg_LoLoLim`** | `REAL` | `-1.5000000000000001E+38` | Hiện (Pin) | [Biến cấu hình thông số] Low-Low Status Threshold |
| **`Val`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Selected Analog PV (including substitute PV, if used) (engineering units). |
| **`Val_InpPV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Calculated PV, before selection of subst PV or filtering (EU) |
| **`Val_PVA`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Analog Value (actual) from Input A (EU) |
| **`Val_PVB`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Analog Value (actual) from Input B (EU) |
| **`Val_PVC`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Analog Value (actual) from Input C (EU) |
| **`Val_PVD`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Analog Value (actual) from Input D (EU) |
| **`Val_PVE`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Analog Value (actual) from Input E (EU) |
| **`Val_PVF`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Analog Value (actual) from Input F (EU) |
| **`Val_PVG`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Analog Value (actual) from Input G (EU) |
| **`Val_PVH`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Analog Value (actual) from Input H (EU) |
| **`Val_PVMinCapt`** | `REAL` | `1.5000000000000001E+38` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị PV thấp nhất (Min Peak) ghi nhận được kể từ lần xóa gần nhất |
| **`Val_PVMaxCapt`** | `REAL` | `-1.5000000000000001E+38` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị PV cao nhất (Max Peak) ghi nhận được kể từ lần xóa gần nhất |
| **`Val_PVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cực tiểu của dải đo đơn vị kỹ thuật = MIN(Cfg_PVEUMin, Cfg_PVEUMax) |
| **`Val_PVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cực đại của dải đo đơn vị kỹ thuật = MAX(Cfg_PVEUMin, Cfg_PVEUMax) |
| **`Val_NumPVs`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Number of PVs that are currently used in calculating Val_CalcPV |
| **`SrcQ_IOA`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ_IOB`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ_IOC`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ_IOD`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ_IOE`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ_IOF`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ_IOG`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ_IOH`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ_IO`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final PV source and quality. GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`Val_Fault`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device fault status: 0 = None 17 = AnyReject 18 = MinGood 20 = Lo 21 = Hi 24 = LoLo 25 = HiHi 32 = Fail 34 = Configuration error |
| **`Val_NotifyAll`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Highest Alarm prio and ack status this object + channels (enum) |
| **`Val_UnackAlmC`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Sts_PVAReject`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] When set to 1, indicates associated input is rejected and not used to calculate PV. |
| **`Sts_PVBReject`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] When set to 1, indicates associated input is rejected and not used to calculate PV. |
| **`Sts_PVCReject`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] When set to 1, indicates associated input is rejected and not used to calculate PV. |
| **`Sts_PVDReject`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] When set to 1, indicates associated input is rejected and not used to calculate PV. |
| **`Sts_PVEReject`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] When set to 1, indicates associated input is rejected and not used to calculate PV. |
| **`Sts_PVFReject`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] When set to 1, indicates associated input is rejected and not used to calculate PV. |
| **`Sts_PVGReject`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] When set to 1, indicates associated input is rejected and not used to calculate PV. |
| **`Sts_PVHReject`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] When set to 1, indicates associated input is rejected and not used to calculate PV. |
| **`Sts_SubstPV`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Using Substitute PV (Input being overridden) |
| **`Sts_PVBad`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu PV có chất lượng xấu (Bad Quality) hoặc vượt ngoài dải giới hạn lỗi (Out of Range) |
| **`Sts_PVUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị PV có chất lượng không chắc chắn (Uncertain Quality) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang bị Tạm hoãn (Shelved) hoặc Vô hiệu hóa (Disabled), hiển thị biểu tượng trên HMI |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Error in Config, see detail bits for reason |
| **`Err_Raw`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị dải thô Cfg_InpRawMin bằng Cfg_InpRawMax |
| **`Err_EU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị dải kỹ thuật Cfg_PVEUMin bằng Cfg_PVEUMax |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Min Duration Time Invalid (use 0 to 2147483 sec) |
| **`Err_Filt`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=-Error in Config: PV filter params (RateTime, TC) |
| **`Err_DB`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: a Status Deadband is < 0.0 |
| **`Err_Has`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: at least one Cfg_HasPVx must be 1 |
| **`Err_Use`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: at least one Cfg_UsePVx must be 1 |
| **`Err_MinGood`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Cfg_MinGood must be in the range [1..8] |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thông số cảnh báo (Thời gian duy trì tối thiểu, thời gian Shelve, mức Severity) không hợp lệ |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc lỗi ngắt tải (Shed) đã hết điều kiện lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_HiHiCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV High-High kết quả so sánh ngưỡng trực tiếp 1=High-High |
| **`Sts_HiHiGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV High-High trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_HiHi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị Analog vượt trên ngưỡng Rất Cao (High-High) |
| **`Alm_HiHi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Ngõ vào Analog đang ở trạng thái Cảnh báo Rất Cao (High-High) |
| **`Ack_HiHi`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Cảnh báo Rất Cao (Cảnh báo Rất Cao (High-Cảnh báo Cao (High Alarm))) đã được Xác nhận (Acknowledged) |
| **`Sts_HiHiDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Rất Cao (Cảnh báo Rất Cao (High-Cảnh báo Cao (High Alarm))) đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_HiHiShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Rất Cao (Cảnh báo Rất Cao (High-Cảnh báo Cao (High Alarm))) đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_HiHiSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Rất Cao (Cảnh báo Rất Cao (High-Cảnh báo Cao (High Alarm))) đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_HiCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV High kết quả so sánh ngưỡng trực tiếp 1=High |
| **`Sts_HiGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV High trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_Hi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị Analog vượt trên ngưỡng Cao (High) |
| **`Alm_Hi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Ngõ vào Analog đang ở trạng thái Cảnh báo Cao (High) |
| **`Ack_Hi`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Cảnh báo Cao (Cảnh báo Cao (High Alarm)) đã được Xác nhận (Acknowledged) |
| **`Sts_HiDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Cao (Cảnh báo Cao (High Alarm)) đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_HiShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Cao (Cảnh báo Cao (High Alarm)) đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_HiSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Cao (Cảnh báo Cao (High Alarm)) đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_LoCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV Low kết quả so sánh ngưỡng trực tiếp 1=Low |
| **`Sts_LoGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV Low trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_Lo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị Analog tụt dưới ngưỡng Thấp (Low) |
| **`Alm_Lo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Ngõ vào Analog đang ở trạng thái Cảnh báo Thấp (Low) |
| **`Ack_Lo`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Cảnh báo Thấp (Cảnh báo Thấp (Low Alarm)) đã được Xác nhận (Acknowledged) |
| **`Sts_LoDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Thấp (Cảnh báo Thấp (Low Alarm)) đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_LoShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Thấp (Cảnh báo Thấp (Low Alarm)) đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_LoSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Thấp (Cảnh báo Thấp (Low Alarm)) đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_LoLoCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV Low-Low kết quả so sánh ngưỡng trực tiếp 1=Low-Low |
| **`Sts_LoLoGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV Low-Low trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_LoLo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị Analog tụt dưới ngưỡng Rất Thấp (Low-Low) |
| **`Alm_LoLo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Ngõ vào Analog đang ở trạng thái Cảnh báo Rất Thấp (Low-Low) |
| **`Ack_LoLo`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Cảnh báo Rất Thấp (Cảnh báo Rất Thấp (Low-Cảnh báo Thấp (Low Alarm))) đã được Xác nhận (Acknowledged) |
| **`Sts_LoLoDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Rất Thấp (Cảnh báo Rất Thấp (Low-Cảnh báo Thấp (Low Alarm))) đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_LoLoShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Rất Thấp (Cảnh báo Rất Thấp (Low-Cảnh báo Thấp (Low Alarm))) đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_LoLoSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Rất Thấp (Cảnh báo Rất Thấp (Low-Cảnh báo Thấp (Low Alarm))) đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_AnyReject`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=At least one Input has been Rejected |
| **`Alm_AnyReject`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Alarm: At least one Input has been Rejected |
| **`Ack_AnyReject`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Any Input Rejected Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_AnyRejectDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Any Input Rejected Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_AnyRejectShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Any Input Rejected Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_AnyRejectSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Any Input Rejected Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_MinGood`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=At minimum required number of good inputs, next reject/fail will result in Bad PV |
| **`Alm_MinGood`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Min Good PV Inputs Alarm |
| **`Ack_MinGood`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Min Good PV Inputs Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_MinGoodDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Min Good PV Inputs Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_MinGoodShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Min Good PV Inputs Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_MinGoodSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Min Good PV Inputs Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_FailCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV Fail kết quả so sánh ngưỡng trực tiếp 1=Out of Range |
| **`Sts_FailGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV Fail trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Total Signal Failure (borth Bad or Out of Range) |
| **`Alm_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Total Signal Failure Alarm |
| **`Ack_Fail`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Total Signal Failure Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_FailDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Total Signal Failure Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_FailShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Total Signal Failure Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_FailSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Total Signal Failure Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`P_AInMulti`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 38 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_AInMulti`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`AnyReject`** | `P_Alarm` | `{...}` | Warning: At least one used Input is being rejected |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'Multiple (to 8) Analog Input'` | [Biến cấu hình thông số] Description for display on HMI. This string is shown in the title bar of the faceplate. |
| **`Cfg_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Engineering Units for display on HMI |
| **`Cfg_Label`** | `STRING_20` | `'Analog Input'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI. This string appears on the graphic symbol. |
| **`Cfg_PVATag`** | `STRING_20` | `'Sensor / Input A'` | [Biến cấu hình thông số] Tagname for Sensor/ Input A for display on HMI |
| **`Cfg_PVBTag`** | `STRING_20` | `'Sensor / Input B'` | [Biến cấu hình thông số] Tagname for Sensor/ Input B for display on HMI |
| **`Cfg_PVCTag`** | `STRING_20` | `'Sensor / Input C'` | [Biến cấu hình thông số] Tagname for Sensor/ Input C for display on HMI |
| **`Cfg_PVDTag`** | `STRING_20` | `'Sensor / Input D'` | [Biến cấu hình thông số] Tagname for Sensor/ Input D for display on HMI |
| **`Cfg_PVETag`** | `STRING_20` | `'Sensor / Input E'` | [Biến cấu hình thông số] Tagname for Sensor/ Input E for display on HMI |
| **`Cfg_PVFTag`** | `STRING_20` | `'Sensor / Input F'` | [Biến cấu hình thông số] Tagname for Sensor/ Input F for display on HMI |
| **`Cfg_PVGTag`** | `STRING_20` | `'Sensor / Input G'` | [Biến cấu hình thông số] Tagname for Sensor/ Input G for display on HMI |
| **`Cfg_PVHTag`** | `STRING_20` | `'Sensor / Input H'` | [Biến cấu hình thông số] Tagname for Sensor/ Input H for display on HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_AInMulti'` | [Biến cấu hình thông số] Tagname for indicator for display on HMI |
| **`Fail`** | `P_Alarm` | `{...}` | Total Signal Failure Alarm (Both A and B PVs bad or out of range) |
| **`FailGate`** | `P_Gate` | `{...}` | Khối con xử lý Cổng điều kiện và Trễ thời gian cho cảnh báo lỗi (Fail Gate) |
| **`Hi`** | `P_Alarm` | `{...}` | Cảnh báo Cao (High Alarm) |
| **`HiGate`** | `P_Gate` | `{...}` | Khối con xử lý Cổng điều kiện và Trễ thời gian cho cảnh báo Cao (Hi Gate) |
| **`HiHi`** | `P_Alarm` | `{...}` | Cảnh báo Rất Cao (High-Cảnh báo Cao (High Alarm)) |
| **`HiHiGate`** | `P_Gate` | `{...}` | Khối con xử lý Cổng điều kiện và Trễ thời gian cho cảnh báo Rất Cao (HiHi Gate) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_AInMulti'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI và phần mềm thông tin |
| **`Lo`** | `P_Alarm` | `{...}` | Cảnh báo Thấp (Low Alarm) |
| **`LoGate`** | `P_Gate` | `{...}` | Khối con xử lý Cổng điều kiện và Trễ thời gian cho cảnh báo Thấp (Lo Gate) |
| **`LoLo`** | `P_Alarm` | `{...}` | Cảnh báo Rất Thấp (Low-Cảnh báo Thấp (Low Alarm)) |
| **`LoLoGate`** | `P_Gate` | `{...}` | Khối con xử lý Cổng điều kiện và Trễ thời gian cho cảnh báo Rất Thấp (LoLo Gate) |
| **`MCmd_InpPV`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Bảo trì chọn sử dụng tín hiệu PV thực tế (chế độ bình thường) |
| **`MCmd_SubstPV`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Bảo trì chọn sử dụng giá trị PV thay thế (ghi đè đầu vào) |
| **`MinGood`** | `P_Alarm` | `{...}` | Minimum Good PVs Alarm (next PV lost will result in Fail) |
| **`MRdy_InpPV`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_InpPV (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_SubstPV`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_SubstPV (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`OCmd_ClearCapt`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Clear the captured min / max PV excursion values |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset all Alarms requiring Reset |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset and Xác nhận (Acknowledge) all Alarms |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`Val_Notify`** | `SINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận của riêng khối này (enum) |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_DIn`
**Thuộc chương:** Chương 1: Xử lý tín hiệu Vào/Ra (I/O Processing)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `IO Processing 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_DIn`:** **33** Biến đầu vào (`Input Parameters`) | **34** Biến đầu ra (`Output Parameters`) | **22** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_DIn`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_PV`, `Inp_IOFault`, `Inp_PVNotify`, `Inp_Target`, `Inp_Gate`, `Inp_Sim`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_NoSubstPV`, `Cfg_NormTextVis`, `Cfg_HasMoreObj`, `Cfg_HasTgtDisagreeAlm`, `Cfg_TgtDisagreeResetReqd`, `Cfg_TgtDisagreeAckReqd`, `Cfg_HasIOFaultAlm`, `Cfg_IOFaultResetReqd`, `Cfg_IOFaultAckReqd`, `Cfg_TgtDisagreeSeverity`, `Cfg_IOFaultSeverity`, `Cfg_Debounce`, `Cfg_GateDly`, `Cfg_TgtDisagreeOnDly`, `Cfg_TgtDisagreeOffDly`
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_0StText`, `Cfg_1StText`, `Cfg_Area`, `Cfg_Desc`, `Cfg_Label`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_DIn` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PCmd_Reset`, `PCmd_TgtDisagreeAck`, `PCmd_TgtDisagreeSuppress`, `PCmd_TgtDisagreeUnsuppress`, `PCmd_TgtDisagreeUnshelve`, `PCmd_IOFaultAck`, `PCmd_IOFaultSuppress`, `PCmd_IOFaultUnsuppress`, `PCmd_IOFaultUnshelve`
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Val_Sts`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Sts`, `Sts_PV`, `Sts_SubstPV`, `Sts_InpPV`, `Sts_PVBad`, `Sts_MaintByp`, `Sts_AlmInh`, `Sts_Err`, `Sts_RdyReset`, `Sts_RdyAck`, `Sts_TgtDisagreeCmp`, `Sts_TgtDisagreeGate`, `Sts_TgtDisagree`, `Alm_TgtDisagree`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 33 Biến Đầu Vào (`Input Parameters`) của `P_DIn`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_PV`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu đo lường đầu vào (Biến quá trình PV) nối từ cảm biến / thẻ Analog Input |
| **`Inp_IOFault`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Input Comm Status or Quality 1=Bad, 0=OK |
| **`Inp_PVNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã trạng thái mức độ ưu tiên và xác nhận cảnh báo của đối tượng PV liên kết. |
| **`Inp_Target`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Target State of Input (Disagree Status if not in Target State) |
| **`Inp_Gate`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] This parameter is the gate input used for status detection. When set to 1, the target disagree monitoring is enabled. The on-delay and off-delay timers are applied after the gate delay timer. When set to 0, detection is disabled and the target disagree status output is forced off. If the status is used as an alarm, this input acts as a suppression-by-design condition. |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Simulation input. When set to 1, the instruction uses simulation parameters (for example, Set_SimPV) to calculate output. When set to 0, the instruction uses input parameters (for example, Inp_PV) to calculate output. |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân đầu vào dùng để xóa (Reset) tất cả các cảnh báo đang chờ Reset từ chương trình PLC (1 = Xóa cảnh báo). |
| **`Cfg_NoSubstPV`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Khóa tính năng nhập giá trị thay thế (Substitute PV) của Bảo trì: Khi = 0, cho phép nhân viên bảo trì nhập giá trị PV thay thế; khi = 1, cấm sử dụng tính năng Substitute PV. When Cfg_NoSubstPV is 0, the commands MCmd_SubstPV and MCmd_InpPV are used to select the input PV or the substitute PV. Sts_SubstPV is 1 when the substitute PV đang được chọn làm Nguồn lệnh điều khiển hiện hành. |
| **`Cfg_NormTextVis`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Display state text in Normal state (= target). 0 = Hide state text in Normal state (some HMI objects). |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_HasTgtDisagreeAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Target Disagree Alarm exists and will be checked |
| **`Cfg_TgtDisagreeResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_TgtDisagreeAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=I/O Fault Alarm exists and will be checked |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=I/O Fault Alarm is latched in, requires Reset Command to clear |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=I/O Fault Alarm must be acknowledged |
| **`Cfg_TgtDisagreeSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] I/O Fault Alarm Severity: 1..250=Low, 251..500=Medium, 501..750=High, 751..1000=Urgent |
| **`Cfg_Debounce`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time Input Sts must maintain state (sec) |
| **`Cfg_GateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Time Inp_Gate must be true before Disagree Status is checked (sec) |
| **`Cfg_TgtDisagreeOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the minimum time (in seconds) the PV must disagree with the target for the status to be set. On-delay times are used to avoid unnecessary alarms when an output (Val) overshoots its threshold (for example, Val_HiHiLim). |
| **`Cfg_TgtDisagreeOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the amount of time (in seconds) the PV must agree with the target to clear the status. Off-delay times are used to reduce chattering alarms. EXAMPLE: If Cfg_OffDly is 5 seconds, the PV must agree with the target for 5 seconds before the status is returned to normal. |
| **`Set_SimPV`** | `BOOL` | `0` | Ẩn (Tag) | PV used in Simulation (Inp_Sim=1) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_Reset = 1 để xóa tất cả các cảnh báo chốt đang chờ Reset (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_TgtDisagreeAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_TgtDisagreeSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) the Target Disagree Alarm |
| **`PCmd_TgtDisagreeUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) the Target Disagree Alarm |
| **`PCmd_TgtDisagreeUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) the I/O Fault Alarm |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) the I/O Fault Alarm |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) the I/O Fault Alarm |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) the I/O Fault Alarm |

## 3. Bảng chi tiết 34 Biến Đầu Ra (`Output Parameters`) của `P_DIn`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Enable Out: The EnableOut signal is not manipulated by this instruction. Its output always reflects EnableIn Input state. |
| **`SrcQ_IO`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final output source and quality: GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`Val_Sts`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device confirmed status: 0 = PV Good 6 = PV Bad 7 = Substitute PV |
| **`Val_Fault`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device fault status: 0 = None 16 = Target Disagree 34 = Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Val_NotifyAll`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |
| **`Val_UnackAlmC`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Sts`** | `BOOL` | `0` | Hiện (Pin) | Discrete Input Status (incl. Debounce, Manual Override, if used) |
| **`Sts_PV`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Discrete Input Status (actual, not subject to Override) |
| **`Sts_SubstPV`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Using Substitute PV (Input being overridden) |
| **`Sts_InpPV`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Using Input PV (normal) |
| **`Sts_PVBad`** | `BOOL` | `??` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Input Comm Status/ Quality (0=OK, 1=Bad) |
| **`Sts_MaintByp`** | `BOOL` | `??` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device has a Maintenance Bypass function active |
| **`Sts_AlmInh`** | `BOOL` | `??` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang bị Tạm hoãn (Shelved) hoặc Vô hiệu hóa (Disabled), hiển thị biểu tượng trên HMI |
| **`Sts_Err`** | `BOOL` | `??` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Error in Config: see detail bits for reason |
| **`Err_Timer`** | `BOOL` | `??` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Invalid Timer Preset (use 0 to 2147483) |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thông số cảnh báo (Thời gian duy trì tối thiểu, thời gian Shelve, mức Severity) không hợp lệ |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc lỗi ngắt tải (Shed) đã hết điều kiện lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_TgtDisagreeCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Input vs. Target comparicon result 1=Disagree |
| **`Sts_TgtDisagreeGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Target Disagree trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_TgtDisagree`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Input is not in Target state |
| **`Alm_TgtDisagree`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Alarm: Input is not in Target state |
| **`Ack_TgtDisagree`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Target Disagree Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_TgtDisagreeDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Target Disagree Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_TgtDisagreeShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Target Disagree Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_TgtDisagreeSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Target Disagree Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Input communication fault (Inp_IOFault = 1) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Alarm: Input communication fault |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=I/O Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`P_DIn`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 22 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_DIn`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_0StText`** | `STRING_8` | `'Off'` | [Biến cấu hình thông số] Text to display in PV=0 State |
| **`Cfg_1StText`** | `STRING_8` | `'On'` | [Biến cấu hình thông số] Text to display in PV=1 State |
| **`Cfg_Area`** | `STRING_8` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'Discrete Input'` | [Biến cấu hình thông số] Description for display on HMI |
| **`Cfg_Label`** | `STRING_20` | `'Discrete Input'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_DIn'` | [Biến cấu hình thông số] Tagname for display on HMI |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_DIn'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI và phần mềm thông tin |
| **`IOFault`** | `P_Alarm` | `{...}` | I/O Fault Alarm |
| **`MCmd_InpPV`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Bảo trì chọn sử dụng tín hiệu PV thực tế (chế độ bình thường) |
| **`MCmd_SubstPV`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Bảo trì chọn sử dụng giá trị PV thay thế (ghi đè đầu vào) |
| **`MRdy_InpPV`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_InpPV (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_SubstPV`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_SubstPV (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MSet_SubstPV`** | `BOOL` | `0` | [Giá trị đặt từ tab Bảo trì (Maintenance)] Maintenance-Entered Substitute PV |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset all latched Alarms |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset and Xác nhận (Acknowledge) all Alarms |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`TgtDisagree`** | `P_Alarm` | `{...}` | Target Disagree Alarm |
| **`TgtDisagreeGate`** | `P_Gate` | `{...}` | Target Disagree Condition Gate/Delay |
| **`Val_Notify`** | `SINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận của riêng khối này (enum) |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_DInAdv`
**Thuộc chương:** Chương 1: Xử lý tín hiệu Vào/Ra (I/O Processing)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `IO Processing 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_DInAdv`:** **62** Biến đầu vào (`Input Parameters`) | **45** Biến đầu ra (`Output Parameters`) | **40** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_DInAdv`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_Gate`, `Inp_IOFault`, `Inp_PV`, `Inp_PVNotify`, `Inp_Reset`, `Inp_RunFdbk`, `Inp_Sim`, `Inp_SpeedFdbk`, `Inp_Target`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_HasStdySSWR0`, `Cfg_Combine_Sts_RunDec`, `Cfg_Debounce`, `Cfg_DecPlcs`, `Cfg_GateDly`, `Cfg_HasSSW`, `Cfg_HasPulseSSW`, `Cfg_HasPulseSpdConv`, `Cfg_HasIOFaultAlm`, `Cfg_HasStdySSWR1`, `Cfg_HasTgtDisagreeWAlm`, `Cfg_HasVSDSpFbk`, `Cfg_HasTgtDisagreeAlm`, `Cfg_HasTgtDisWF`, `Cfg_HasMoreObj`, `Cfg_IOFaultResetReqd`, `Cfg_IOFaultAckReqd`, `Cfg_IOFaultSeverity`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_0StText`, `Cfg_1StText`, `Cfg_AccTime`, `Cfg_AcStText`, `Cfg_Area`, `Cfg_DcStText`, `Cfg_DecTime`, `Cfg_Desc`, `Cfg_Label`, `Cfg_PulseMin`, `Cfg_RStText`, `Cfg_SpeedFdbkEU`, `Cfg_SymbolVis`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_DInAdv` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PCmd_IOFaultAck`, `PCmd_Reset`, `PCmd_IOFaultSuppress`, `PCmd_IOFaultUnshelve`, `PCmd_IOFaultUnsuppress`, `PCmd_TgtDisagreeAck`, `PCmd_TgtDisagreeSuppress`, `PCmd_TgtDisagreeUnshelve`, `PCmd_TgtDisagreeUnsuppress`, `PCmd_TgtDisagreeWAck`, `PCmd_TgtDisagreeWSuppress`, `PCmd_TgtDisagreeWUnsuppress`, `PCmd_TgtDisagreeWUnshelve`
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Alm_IOFault`, `Alm_TgtDisagree`, `Alm_TgtDisagreeW`, `Sts`, `Sts_Acc`, `Sts_AlmInh`, `Sts_Dec`, `Sts_Err`, `Sts_InpPV`, `Sts_MaintByp`, `Sts_IOFault`, `Sts_IOFaultDisabled`, `Sts_IOFaultShelved`, `Sts_IOFaultSuppressed`, `Sts_PV`, `Sts_PVBad`, `Sts_RdyAck`, `Sts_RdyReset`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 62 Biến Đầu Vào (`Input Parameters`) của `P_DInAdv`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Cfg_HasStdySSWR0`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Switch Steady 0 = Run |
| **`Cfg_Combine_Sts_RunDec`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Combine Status Run and Deccelerate as Status Run |
| **`Cfg_Debounce`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time Input Sts must maintain state (sec) |
| **`Cfg_DecPlcs`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Number of decimal places for speed ref/fdbk display (0..6) |
| **`Cfg_GateDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Gate Delay (s): time after Gate Input true to enable On/Off Delay |
| **`Cfg_HasSSW`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Used as Speed Switch |
| **`Cfg_HasPulseSSW`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Switch Pulse |
| **`Cfg_HasPulseSpdConv`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Pulse Speed Conversion |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=I/O Fault Alarm exists and will be checked |
| **`Cfg_HasStdySSWR1`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Switch Steady 1 = Run |
| **`Cfg_HasTgtDisagreeWAlm`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Target Disagree Warning Alarm exists and will be checked |
| **`Cfg_HasVSDSpFbk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Switch used with VSD |
| **`Cfg_HasTgtDisagreeAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Target Disagree Alarm exists and will be checked |
| **`Cfg_HasTgtDisWF`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] DIn has Target Disagree Warning & Failure Indication |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=I/O Fault Alarm is latched in, requires Reset Command to clear |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=I/O Fault Alarm must be acknowledged |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] I/O Fault Alarm Severity: 1..250=Low, 251..500=Medium, 501..750=High, 751..1000=Urgent |
| **`Cfg_Maintain_StsRun`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] SpeedSW maintain Sts_Run during Deccelerate |
| **`Cfg_NormTextVis`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Display state text in "normal" state (= target), 0=hide (some HMI objects) |
| **`Cfg_NoSubstPV`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Disallow selection of Substitute PV |
| **`Cfg_SSW_Type`** | `SINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = SpeedSw 0 Run, 2 = SppedSw 1 Run, 4 = SpeedSW Pulse |
| **`Cfg_TgtDisagreeAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Target Disagree Alarm must be acknowledged |
| **`Cfg_TgtDisagreeOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time for Input to disagree with Target to raise Status (sec) |
| **`Cfg_TgtDisagreeOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time for Input to agree with Target to clear Status (sec) |
| **`Cfg_TgtDisagreeResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Target Disagree Alarm is latched in, requires Reset Command to clear |
| **`Cfg_TgtDisagreeSSWTgt`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] SpeedSwitch TargetDisagree Set Target |
| **`Cfg_TgtDisagreeWOnDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time for Input to disagree with Target to raise warning Status (sec) |
| **`Cfg_TgtDisagreeWOffDly`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum time for Input to agree with Target to clear warning Status (sec) |
| **`Cfg_TgtDisagreeWAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Target Disagree Warning Alarm must be acknowledged |
| **`Cfg_TgtDisagreeWResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Target Disagree Warning Alarm is latched in, requires Reset Command to clear |
| **`Cfg_TgtDisagreeSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Target Disagree Alarm Severity: 1..250=Low, 251..500=Medium, 501..750=High, 751..1000=Urgent |
| **`Cfg_TgtDisagreeWSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Target Disagree Warning Alarm Severity: 1..250=Low, 251..500=Medium, 501..750=High, 751..1000=Urgent |
| **`Cfg_UseRunFdbkSpdSW`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] SpeedSW status updates only with Motor Run Feedback |
| **`Cfg_VSD_FailTime`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] VSD Failure indication delay time |
| **`Cfg_VSD_LoLIM`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] VSD Speed Lo Limit for Warn Fail factor adjust |
| **`Cfg_VSD_WarnTime`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] VSD Warning indication delay time |
| **`Inp_Gate`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Gate Condition (=1 to enable Disagree Status) |
| **`Inp_IOFault`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Input Comm Status or Quality 1=Bad, 0=OK |
| **`Inp_PV`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu đo lường đầu vào (Biến quá trình PV) nối từ cảm biến / thẻ Analog Input |
| **`Inp_PVNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã trạng thái mức độ ưu tiên và xác nhận cảnh báo của đối tượng PV liên kết. |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Reset Alarm requiring reset |
| **`Inp_RunFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Motor Run feedback for SppedSW |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân kích hoạt chế độ Mô phỏng: 1 = Dùng giá trị giả lập (Set_SimPV); 0 = Dùng tín hiệu thực tế (Inp_PV). |
| **`Inp_SpeedFdbk`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] VSD Speed FeedBack 0 - 100% |
| **`Inp_Target`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Target State of Input (Disagree Status if not in Target State) |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) the I/O Fault Alarm |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Reset all latched Alarms |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) the I/O Fault Alarm |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) the I/O Fault Alarm |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) the I/O Fault Alarm |
| **`PCmd_TgtDisagreeAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) the Target Disagree Alarm |
| **`PCmd_TgtDisagreeSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) the Target Disagree Alarm |
| **`PCmd_TgtDisagreeUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) the Target Disagree Alarm |
| **`PCmd_TgtDisagreeUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) the Target Disagree Alarm |
| **`PCmd_TgtDisagreeWAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) the Target Disagree Warning Alarm |
| **`PCmd_TgtDisagreeWSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) the Target Disagree Warning Alarm |
| **`PCmd_TgtDisagreeWUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) the Target Disagree Warning Alarm |
| **`PCmd_TgtDisagreeWUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) the Target Disagree Warning Alarm |
| **`Set_SimPV`** | `BOOL` | `0` | Ẩn (Tag) | PV used in Simulation (Inp_Sim=1) |
| **`Set_SymbolVis`** | `BOOL` | `0` | Ẩn (Tag) | 0=Hidden except if in error. 1= Display Symbol (set from Area or Unit) |

## 3. Bảng chi tiết 45 Biến Đầu Ra (`Output Parameters`) của `P_DInAdv`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Enable Out: The EnableOut signal is not manipulated by this instruction. Its output always reflects EnableIn Input state. |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=I/O Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Ack_TgtDisagree`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Target Disagree Alarm đã được Xác nhận (Acknowledged) |
| **`Ack_TgtDisagreeW`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Target Disagree Warning Alarm đã được Xác nhận (Acknowledged) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Alarm: Input communication fault |
| **`Alm_TgtDisagree`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Alarm: Input is not in Target state |
| **`Alm_TgtDisagreeW`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Alarm: Input is not in Target state: warning |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Invalid Timer Preset (use 0 to 2147483) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Invalid Timer Preset (use 0 to 2147483) |
| **`P_DIn`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final output source and quality: GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`Sts`** | `BOOL` | `0` | Hiện (Pin) | Discrete Input Status (incl. Debounce, Manual Override, if used) |
| **`Sts_Acc`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Accelerating |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang bị Tạm hoãn (Shelved) hoặc Vô hiệu hóa (Disabled), hiển thị biểu tượng trên HMI |
| **`Sts_Dec`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Decelerating |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Error in Config: see detail bits for reason |
| **`Sts_InpPV`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Using Input PV (normal) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device has a Maintenance Bypass function active |
| **`Sts_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Input communication fault (Inp_IOFault = 1) |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_PV`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Discrete Input Status (actual, not subject to Override) |
| **`Sts_PVBad`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Input Comm Status/ Quality (0=OK, 1=Bad) |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc lỗi ngắt tải (Shed) đã hết điều kiện lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_Run`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Running |
| **`Sts_SubstPV`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Using Substitute PV (Input being overridden) |
| **`Sts_TgtDisagree`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Input is not in Target state |
| **`Sts_TgtDisagreeCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Target Disagree comparicon result 1=Disagree |
| **`Sts_TgtDisagreeWCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Target Disagree Warning comparicon result 1=Disagree |
| **`Sts_TgtDisagreeDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Target Disagree Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_TgtDisagreeGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Target Disagree trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_TgtDisagreeWDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Target Disagree Warning Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_TgtDisagreeWGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Target Disagree Warning trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_TgtDisagreeShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Target Disagree Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_TgtDisagreeSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Target Disagree Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_TgtDisagreeWSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Target Disagree Warning Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_TgtDisagreeW`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Input is not in Target state: warning |
| **`Sts_TgtDisagreeWShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Target Disagree Warning Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Val_Fault`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Fault Status 0=none, 16=Tgt Disagree, 32=I/O comms., 34=CfgErr |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Confirmed Sts 0=PV Good, 6=PV Bad, 7=Substitute PV |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |

## 4. Bảng chi tiết 40 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_DInAdv`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_0StText`** | `STRING_8` | `'Off'` | [Biến cấu hình thông số] Text to display in PV=0 State |
| **`Cfg_1StText`** | `STRING_8` | `On'` | [Biến cấu hình thông số] Text to display in PV=1 State |
| **`Cfg_AccTime`** | `DINT` | `5` | [Biến cấu hình thông số] Accelerate Time in Sec |
| **`Cfg_AcStText`** | `STRING_8` | `'Acc'` | [Biến cấu hình thông số] Text to display in Accelerate State |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_DcStText`** | `STRING_8` | `'Decc'` | [Biến cấu hình thông số] Text to display in Decelerate State |
| **`Cfg_DecTime`** | `DINT` | `10` | [Biến cấu hình thông số] Deccelerate Time |
| **`Cfg_Desc`** | `STRING_40` | `'Discrete Input'` | [Biến cấu hình thông số] Description for display on HMI |
| **`Cfg_Label`** | `STRING_20` | `'Discrete Input'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI |
| **`Cfg_PulseMin`** | `DINT` | `0` | [Biến cấu hình thông số] Pulses per Min for Speed Calc |
| **`Cfg_RStText`** | `STRING_8` | `'Run'` | [Biến cấu hình thông số] Text to display in Run State |
| **`Cfg_SpeedFdbkEU`** | `STRING_8` | `'Hz'` | [Biến cấu hình thông số] Text to display Units |
| **`Cfg_SymbolVis`** | `BOOL` | `0` | [Biến cấu hình thông số] Always display Symbol else hidden if no error condition |
| **`Cfg_Tag`** | `STRING_20` | `'P_DInAdv'` | [Biến cấu hình thông số] Tagname for display on HMI |
| **`Err_EngTb1`** | `BOOL` | `0` | [Cờ báo lỗi cài đặt thông số Cfg_*] Error on Engineering Tab 1 |
| **`Err_EngTb2`** | `BOOL` | `0` | [Cờ báo lỗi cài đặt thông số Cfg_*] Error on Engineering Tab 2 |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_DInAdv'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI và phần mềm thông tin |
| **`IOFault`** | `P_Alarm` | `{...}` | I/O Fault Alarm |
| **`MCmd_InpPV`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Bảo trì chọn sử dụng tín hiệu PV thực tế (chế độ bình thường) |
| **`MCmd_SubstPV`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Bảo trì chọn sử dụng giá trị PV thay thế (ghi đè đầu vào) |
| **`MRdy_InpPV`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_InpPV (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_SubstPV`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_SubstPV (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MSet_SubstPV`** | `BOOL` | `0` | [Giá trị đặt từ tab Bảo trì (Maintenance)] Maintenance-Entered Substitute PV |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset all latched Alarms |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset and Xác nhận (Acknowledge) all Alarms |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`PulseSpd_ONS1`** | `BOOL` | `0` | One-Shot store bit |
| **`SSW_StaticGate`** | `P_Gate` | `{...}` | Condition Gate/Delay |
| **`Sts_Equip_Start`** | `BOOL` | `0` | [Cờ báo trạng thái logic (Status)] Equipment Starting |
| **`TgtDisagree`** | `P_Alarm` | `{...}` | Target Disagree Alarm |
| **`TgtDisagreeGate`** | `P_Gate` | `{...}` | Target Disagree Condition Gate/Delay |
| **`TgtDisagreeW`** | `P_Alarm` | `{...}` | Target Disagree Warning Alarm |
| **`TgtDisagreeWarnGate`** | `P_Gate` | `{...}` | Target Disagree Warning Condition Gate/Delay |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận của riêng khối này (enum) |
| **`Val_PlsSpdPerc`** | `REAL` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Pulse Speed as % |
| **`VSD_Sim`** | `DINT` | `0` | VSD Sim Speed |
| **`VSD_Spd_Cmd`** | `DINT` | `0` | VSD Speed Command from HMI |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_DOut`
**Thuộc chương:** Chương 1: Xử lý tín hiệu Vào/Ra (I/O Processing)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `IO Processing 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_DOut`:** **95** Biến đầu vào (`Input Parameters`) | **78** Biến đầu ra (`Output Parameters`) | **33** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_DOut`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_OnFdbk`, `Inp_OffFdbk`, `Inp_PermOK`, `Inp_NBPermOK`, `Inp_IntlkOK`, `Inp_NBIntlkOK`, `Inp_IOFault`, `Inp_Sim`, `Inp_Hand`, `Inp_Ovrd`, `Inp_OvrdCmd`, `Inp_ExtInh`, `Inp_HornInh`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_HasPulse`, `Cfg_CompletePulse`, `Cfg_HasOnFdbk`, `Cfg_HasOffFdbk`, `Cfg_UseOnFdbk`, `Cfg_UseOffFdbk`, `Cfg_FdbkFail`, `Cfg_HasPermObj`, `Cfg_HasIntlkObj`, `Cfg_HornOnChange`, `Cfg_OperOffPrio`, `Cfg_ExtOffPrio`, `Cfg_OCmdResets`, `Cfg_XCmdResets`, `Cfg_OvrdPermIntlk`, `Cfg_ShedOnFail`, `Cfg_ShedOnIOFault`, `Cfg_HasOnFailAlm`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_0StText`, `Cfg_1StText`, `Cfg_Area`, `Cfg_Desc`, `Cfg_Label`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_DOut` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PSet_Owner`, `PCmd_On`, `PCmd_Off`, `PCmd_OnPulse`, `PCmd_OffPulse`, `PCmd_ContPulse`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_OnFailAck`, `PCmd_OffFailAck`, `PCmd_IntlkTripAck`, `PCmd_IOFaultAck`, `PCmd_OnFailSuppress`, `PCmd_OnFailUnsuppress`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Out`, `Out_Horn`, `Val_Cmd`, `Val_Fdbk`, `Val_Sts`, `Val_Fault`, `Val_State`, `Val_NotifyAll`, `Val_UnackAlmC`, `Val_Owner`, `Sts_Out`, `Sts_Pulsing`, `Sts_Horn`, `Sts_FdbkOff`, `Sts_FdbkOn`, `Sts_FdbkFail`, `Sts_Available`, `Sts_Bypass`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 95 Biến Đầu Vào (`Input Parameters`) của `P_DOut`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_OnFdbk`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Input signal: On feedback from device; 1 = Device confirmed On. |
| **`Inp_OffFdbk`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Input signal: Off feedback from device; 1 = Device confirmed Off. |
| **`Inp_PermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=ON Permissives OK, device can turn ON |
| **`Inp_NBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Non-Bypassable ON Permissives OK, device can turn ON |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Interlocks OK, device can turn ON and stay ON |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Non-Bypassable Interlocks OK, device can turn ON and stay ON |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] I/O communication status: 0 = OK 1 = Fail |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Simulation input. When set to 1, the instruction simulates a working device while keeping the output de-energized. When set to 0, the instruction operates the device normally. |
| **`Inp_Hand`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Select Hand (hard-wired) mode. |
| **`Inp_Ovrd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Kích hoạt chế độ Override (Cưỡng bức ưu tiên theo lệnh Inp_OvrdCmd). |
| **`Inp_OvrdCmd`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Override device command: 0 = None 1 = Off 2 = On 3 = Pulse off 4 = Pulse on 5 = Pulse continuously |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Inhibit External Acquisition, 0=Allow External Acquisition |
| **`Inp_HornInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Cấm còi báo động âm thanh; 0 = Cho phép phát còi báo trước khi khởi động |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân đầu vào dùng để xóa (Reset) tất cả các cảnh báo đang chờ Reset từ chương trình PLC (1 = Xóa cảnh báo). |
| **`Cfg_HasPulse`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Enable pulsing functions, 0=On/Off only |
| **`Cfg_CompletePulse`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Finish pulse in progress when commanded ON or Off. 0 = Switch immediately to ON or OFF state when commanded. |
| **`Cfg_HasOnFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Device provides an ON feedback signal |
| **`Cfg_HasOffFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Device provides an OFF feedback signal |
| **`Cfg_UseOnFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Device ON feedback should be used for failure checking |
| **`Cfg_UseOffFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Device OFF feedback should be used for failure checking |
| **`Cfg_FdbkFail`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Both feedbacks On is invalid. 0 = Both feedbacks Off is invalid. |
| **`Cfg_HasPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng permissive liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng interlock liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HornOnChange`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 0 = Horn on energize only, 1 = Horn on any state change |
| **`Cfg_OperOffPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = OCmd_Off has priority, accepted any time. 0 = OCmd_Off only in Operator and Maintenance mode. |
| **`Cfg_ExtOffPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=XCmd_Off any time, 0=XCmd_Off only when Operator selected |
| **`Cfg_OCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = OCmd_Off has priority, accepted any time. 0 = OCmd_Off only in Operator and Maintenance mode. |
| **`Cfg_XCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=New device XCmd resets shed latches and cleared alarms, 0=XCmdReset req'd. |
| **`Cfg_OvrdPermIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Override ignores bypassable permissives/interlocks. 0 = Always use permissives/interlocks. |
| **`Cfg_ShedOnFail`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Go to OFF State and alarm on Fail to reach position. 0 = Alarm only on Fail. IMPORTANT: If a condition is configured to shed the device to the Off state on a fault, a reset is required to clear the shed fault to command the device to a state other than Off. |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Go to OFF State and alarm on I/O Fault. 0 = Alarm only on I/O Fault. IMPORTANT: If a condition is configured to shed the device to the Off state on a fault, a reset is required to clear the shed fault to command the device to a state other than Off. |
| **`Cfg_HasOnFailAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasOffFailAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_OnFailResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_OffFailResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_OnFailAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_OffFailAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_OnFailSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_OffFailSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_StartHornT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Time to sound audible on commanded start (sec) (0=disabled) |
| **`Cfg_SimFdbkT`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Delay to echo back of ON/OFF status when in Simulation (sec) |
| **`Cfg_OnDelayT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Delay before initially turning output ON (sec) |
| **`Cfg_OffDelayT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Delay before initially turning output OFF (sec) |
| **`Cfg_OnPulseT`** | `REAL` | `0.5` | Ẩn (Tag) | [Biến cấu hình thông số] Output ON time for Pulse ON or Pulse Continuous |
| **`Cfg_OffPulseT`** | `REAL` | `0.5` | Ẩn (Tag) | [Biến cấu hình thông số] Output OFF time for Pulse OFF or Pulse Continuous |
| **`Cfg_OnFailT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Time after Output ON to get ON Feedback before Fault (sec) |
| **`Cfg_OffFailT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Time after Output OFF to get OFF Feedback before Fault (sec) |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Mã định danh chủ sở hữu chương trình (Program Owner ID: khác 0 để đăng ký chiếm quyền, bằng 0 để nhả quyền). |
| **`PCmd_On`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để turn device ON |
| **`PCmd_Off`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để turn device OFF |
| **`PCmd_OnPulse`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để pulse device (which is OFF) ON once |
| **`PCmd_OffPulse`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để pulse device (which is ON) OFF once |
| **`PCmd_ContPulse`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để pulse device continuously (blink) |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để chuyển quyền điều khiển sang Tự động (từ Operator sang Program) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để chuyển quyền điều khiển sang Bằng tay (từ Program sang Operator) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa cứng chế độ Program (không cho phép Operator giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Mở khóa chế độ Program (cho phép Operator lấy quyền điều khiển) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để trả về nguồn lệnh Bình thường mặc định (Normal: Program hoặc Operator) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_Reset = 1 để xóa tất cả các cảnh báo chốt đang chờ Reset (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_OnFailAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_OffFailAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_OnFailSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Fail to Turn ON Alarm |
| **`PCmd_OnFailUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để UnsuppressFail to Turn ON Alarm |
| **`PCmd_OffFailSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Fail to Turn OFF Alarm |
| **`PCmd_OffFailUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Fail to Turn OFF Alarm |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Interlock Trip Alarm |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Interlock Trip Alarm |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) I/O Fault Alarm |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) I/O Fault Alarm |
| **`PCmd_OnFailUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_OffFailUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Release (Maint to Oper/Prog/Ovrd) |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Unlock Operator (allow Program to acquire) |
| **`XCmd_On`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để turn device ON |
| **`XCmd_Off`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để turn device OFF |
| **`XCmd_OnPulse`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để pulse device (which is OFF) ON once |
| **`XCmd_OffPulse`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để pulse device (which is ON) OFF once |
| **`XCmd_ContPulse`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để pulse device continuously (blink) |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Acquire (Oper/Prog to Ext) |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Release (Ext to Oper/Prog) |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Ext Cmd to clear shed latches and cleared alarms |

## 3. Bảng chi tiết 78 Biến Đầu Ra (`Output Parameters`) của `P_DOut`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Out`** | `BOOL` | `0` | Hiện (Pin) | Primary Output 1=ON, 0=OFF |
| **`Out_Horn`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1=Sound audible prior to commanded energize/ state change |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final device status source and quality. GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`Val_Cmd`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device command: 0 = None 1 = Off 2 = On 3 = Pulse off 4 = Pulse on 5 = Pulse continuously |
| **`Val_Fdbk`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Feedback: 0=Transition, 1=Off, 2=On, 3=Invalid |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Status 0=off 1=on 2=pulse off 3=pulse on 4=pulse cont. 5=turning off 6=turning on |
| **`Val_Fault`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Fault Status 0=none, 16=Feedback Fault, 32=I/O Fault, 34=Config Error |
| **`Val_State`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Internal Logic State (for animating STD on faceplate), see manual |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Highest Alarm prio and ack status this object + attached (enum) |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Current Object Owner ID (0=not owned) |
| **`Sts_Out`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Output is ON (energized), 0=Output is OFF (de-energized) |
| **`Sts_Pulsing`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Output is in a pusling sequence |
| **`Sts_Horn`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Horn Active |
| **`Sts_FdbkOff`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device Feedback shows device in OFF state |
| **`Sts_FdbkOn`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device Feedback shows device in ON state |
| **`Sts_FdbkFail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Feedbacks are in an INVALID state (not ON, OFF or TRANSIT) |
| **`Sts_Available`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device available for control by automation (Prog) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Bypassable Interlocks and Permissives are Bypassed |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Interlock / Permissive Bypassing Active (Bypassed or Maintenance) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device Not Ready, see detail bits for reason |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Lỗi thiết bị ngắt tải (yêu cầu nhấn Reset) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Điều kiện Khóa liên động không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Lỗi truyền thông I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Thiết bị đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Nrdy_PrioOff`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Oper/Ext Priority Off Comand Requires Reset |
| **`Nrdy_Perm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Điều kiện Cho phép khởi động không thỏa mãn (Permissive Not OK) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device has a Maintenance Bypass function active |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang bị Tạm hoãn (Shelved) hoặc Vô hiệu hóa (Disabled), hiển thị biểu tượng trên HMI |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Error in Config: see detail bits for reason |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Feeback Check timer preset (use 0 to 2147483) |
| **`Err_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Simulation timer preset (use 0 to 2147483) |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thông số cảnh báo (Thời gian duy trì tối thiểu, thời gian Shelve, mức Severity) không hợp lệ |
| **`Sts_Hand`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Hand đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn OoS, Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_OoS`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Out of Service đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_Maint`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Maintenance đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ovrd, Ext, Prog, Oper) |
| **`Sts_Ovrd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Override đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ext, Prog, Oper) |
| **`Sts_Ext`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=External đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Prog, Oper) |
| **`Sts_Prog`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Program đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_ProgLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Program đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Oper`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_OperLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Selection equals the Normal (Prog or Oper) |
| **`Sts_ExtReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=External request inhibited, cannot Get to External from current state |
| **`Sts_ProgReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Program request inhibited, cannot Get to Program from current state |
| **`Sts_MAcqRcvd`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Maintenance Acquire command received this scan |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc lỗi ngắt tải (Shed) đã hết điều kiện lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_OnFail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device Failed to Turn ON |
| **`Alm_OnFail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Device Fail to Turn ON Alarm |
| **`Ack_OnFail`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Fail to Turn ON Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_OnFailDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Fail to Turn ON Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_OnFailShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Fail to Turn ON Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_OnFailSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Fail to Turn ON Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_OffFail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device Failed to Turn OFF |
| **`Alm_OffFail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Device Fail to Turn OFF Alarm |
| **`Ack_OffFail`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Fail to Turn OFF Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_OffFailDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Fail to Turn OFF Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_OffFailShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Fail to Turn OFF Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_OffFailSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Fail to Turn OFF Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device turned OFF by an Interlock NOT OK (one-shot) |
| **`Alm_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Alarm: Device turned OFF by an Interlock NOT OK |
| **`Ack_IntlkTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Interlock Trip Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IntlkTripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Interlock Trip Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IntlkTripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Interlock Trip Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IntlkTripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Interlock Trip Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] I/O Comm Fault Status (0=OK, 1=Bad) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=I/O Fault Alarm |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=I/O Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`MRdy_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`P_DOut`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 33 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_DOut`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_0StText`** | `STRING_8` | `'Off'` | [Biến cấu hình thông số] Text to display when device is in Off (0) state. |
| **`Cfg_1StText`** | `STRING_8` | `'On'` | [Biến cấu hình thông số] Text to display when device is in On (1) state. |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'Discrete On/Off Device'` | [Biến cấu hình thông số] Description for display on HMI. The string shows in the title bar of the faceplate. |
| **`Cfg_Label`** | `STRING_20` | `'Discrete Device'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_DOut'` | [Biến cấu hình thông số] Tagname for display on HMI |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc quản lý lựa chọn nguồn lệnh điều khiển |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_DOut'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | Interlock Trip Alarm |
| **`IOFault`** | `P_Alarm` | `{...}` | I/O Fault Alarm |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bypass all Bypassable Interlocks and Permissives |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Check (not bypass) all Interlocks and Permissives |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`OCmd_ContPulse`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để pulse device continuously (blink) |
| **`OCmd_Off`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để turn device OFF |
| **`OCmd_OffPulse`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để pulse device (which is ON) OFF once |
| **`OCmd_On`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để turn device ON |
| **`OCmd_OnPulse`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để pulse device (which is OFF) ON once |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset all Alarms requiring Reset |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset all Alarms and latched Shed conditions |
| **`OffFail`** | `P_Alarm` | `{...}` | Device Failed to Turn OFF Alarm |
| **`OnFail`** | `P_Alarm` | `{...}` | Device Failed to Turn ON Alarm |
| **`ORdy_ContPulse`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ContPulse (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Off`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Off (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_OffPulse`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_OffPulse (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_On`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_On (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_OnPulse`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_OnPulse (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_AOut`
**Thuộc chương:** Chương 1: Xử lý tín hiệu Vào/Ra (I/O Processing)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `IO Processing 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_AOut`:** **61** Biến đầu vào (`Input Parameters`) | **62** Biến đầu ra (`Output Parameters`) | **23** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_AOut`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_Tieback`, `Inp_IntlkOK`, `Inp_NBIntlkOK`, `Inp_IOFault`, `Inp_Sim`, `Inp_Hand`, `Inp_Ovrd`, `Inp_OvrdCV`, `Inp_ExtInh`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_ShedHold`, `Cfg_SkipRoCLim`, `Cfg_SetTrack`, `Cfg_SetTrackOvrdHand`, `Cfg_HasIntlkObj`, `Cfg_HasCVNav`, `Cfg_OvrdIntlk`, `Cfg_ShedOnIOFault`, `Cfg_DecPlcs`, `Cfg_HasIntlkTripAlm`, `Cfg_HasIOFaultAlm`, `Cfg_HasMoreObj`, `Cfg_IntlkTripResetReqd`, `Cfg_IOFaultResetReqd`, `Cfg_IntlkTripAckReqd`, `Cfg_IOFaultAckReqd`, `Cfg_IntlkTripSeverity`, `Cfg_IOFaultSeverity`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_CVNavTag`, `Cfg_Desc`, `Cfg_EU`, `Cfg_Label`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_AOut` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PSet_CV`, `PSet_Owner`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_IntlkTripAck`, `PCmd_IOFaultAck`, `PCmd_IntlkTripSuppress`, `PCmd_IntlkTripUnsuppress`, `PCmd_IOFaultSuppress`, `PCmd_IOFaultUnsuppress`, `PCmd_IntlkTripUnshelve`, `PCmd_IOFaultUnshelve`
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Out_CV`, `Val_CVSet`, `Val_CVOut`, `Val_CVEUMin`, `Val_CVEUMax`, `Val_Sts`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Val_Owner`, `Sts_Ramping`, `Sts_Clamped`, `Sts_SkipRoCLim`, `Sts_Active`, `Sts_Available`, `Sts_Bypass`, `Sts_BypActive`, `Sts_NotRdy`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 61 Biến Đầu Vào (`Input Parameters`) của `P_AOut`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_Tieback`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tieback CV (in raw, Out_CV units) used in Hand or Init |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Interlocks OK, Analog Output can be set |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Non-Bypassable Interlocks OK, Analog Output can be set |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Input Communication Status 0=OK, 1=fail |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Simulation input. When set to 0, the instruction operates normally. When set to 1, the instruction acts as normal but the output is held to at zero. |
| **`Inp_Hand`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Select Hand (hardwired) control strategy. |
| **`Inp_Ovrd`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Select Override control strategy. |
| **`Inp_OvrdCV`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] CV target in Override mode (engineering units). |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Inhibit External Acquisition, 0=Allow External Acquisition |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân đầu vào dùng để xóa (Reset) tất cả các cảnh báo đang chờ Reset từ chương trình PLC (1 = Xóa cảnh báo). |
| **`Cfg_ShedHold`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Hold Output on Interlock, 0=Go to Cfg_IntlkCV |
| **`Cfg_SkipRoCLim`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Skip rate of change limiting in Maintenance or Override mode and on interlock. |
| **`Cfg_SetTrack`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] This parameter is used to set up bumpless behavior of setting parameters when switching modes. When this parameter is 1, in Program mode the operator settings track the program settings; in Operator mode the program settings track the operator settings; and the simulation inputs match the output values (transitions are bumpless). When this parameter is 0, the operator settings and program settings are not modified by this instruction. In this case, when the mode is changed, the effective value of the setting can change depending on the program-set and operator-set values. |
| **`Cfg_SetTrackOvrdHand`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Prog/Operator Settings track Override/Hand CV |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tells HMI an interlock object is connected to Inp_Intlk. IMPORTANT: The name of the Interlock object in the controller must be this object's name with the suffix ‘_Intlk’. For example, if your P_AOut object has the name ’AOut123’, then its Interlock object must be named ‘AOut123_Intlk’. |
| **`Cfg_HasCVNav`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Tells HMI to enable navigation to a connected CV object |
| **`Cfg_OvrdIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Override ignores Bypassable Intlk; 0=always use Intlk |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Dừng (Stop) động cơ and Alarm on I/O Fault; 0=Alarm only on I/O Fault |
| **`Cfg_DecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Number of decimal places for CV display (0..6) |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Interlock Trip Alarm exists and will be checked |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=I/O Fault Alarm exists and will be checked |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Reset required to clear Interlock Trip Alarm |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Reset required to clear I/O Fault Alarm |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Xác nhận (Acknowledge) required for Interlock Trip Alarm |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Xác nhận (Acknowledge) required for I/O Fault Alarm |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_MinCV`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum CV (in engineering units, for limiting). |
| **`Cfg_MaxCV`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Maximum CV (in engineering units, for limiting). |
| **`Cfg_CVRoCLimInc`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Operator setting of CV rate of change limit, increasing or decreasing (in engineering units/second). |
| **`Cfg_CVRoCLimDec`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Operator setting of CV rate of change limit, increasing or decreasing (in engineering units/second). |
| **`Cfg_IntlkCV`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] CV Target when interlocked (if not Cfg_ShedHold) (EU) |
| **`Cfg_CVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] CV Minimum in Engineering Units (for scaling) |
| **`Cfg_CVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] CV Maximum in Engineering Units (for scaling) |
| **`Cfg_CVRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] CV Minimum in I/O (raw) Units (for scaling) |
| **`Cfg_CVRawMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] CV Maximum in I/O (raw) Units (for scaling) |
| **`Cfg_MaxInactiveCV`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] When Val_CVOut is greater than this value (in CV EU) set Sts_Active (for HMI) |
| **`PSet_CV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Program Setting of Controlled Variable (output) (in EU) |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Mã định danh chủ sở hữu chương trình (Program Owner ID: khác 0 để đăng ký chiếm quyền, bằng 0 để nhả quyền). |
| **`XSet_CV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài (External)] External Setting of Controlled Variable (output) (in EU) |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để chuyển quyền điều khiển sang Tự động (từ Operator sang Program) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để chuyển quyền điều khiển sang Bằng tay (từ Program sang Operator) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa cứng chế độ Program (không cho phép Operator giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Mở khóa chế độ Program (cho phép Operator lấy quyền điều khiển) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để trả về nguồn lệnh Bình thường mặc định (Normal: Program hoặc Operator) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Interlock Trip Alarm |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Interlock Trip Alarm |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) I/O Fault Alarm |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) I/O Fault Alarm |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để nhả quyền điều khiển hiện tại (Maintenance to Operator/Program/Override). |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để unlock/release (Operator to Program) ownership. |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Acquire (Oper/Prog to Ext) |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Release (Ext to Oper/Prog) |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi |

## 3. Bảng chi tiết 62 Biến Đầu Ra (`Output Parameters`) của `P_AOut`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Enable Output - System Defined Parameter |
| **`Out_CV`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] CV Output in Raw (I/O Card) Units |
| **`Val_CVSet`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Value of selected CV setting (before rate limiting, in engineering units). |
| **`Val_CVOut`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Value of CV output (after rate limiting) (in engineering units). |
| **`Val_CVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Minimum of scaled range = MIN (Cfg_CVEUMin, Cfg_CVEUMax). |
| **`Val_CVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Maximum of scaled range = MAX (Cfg_CVEUMin, Cfg_CVEUMax). |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final analog source and quality: GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] 0 = At target 1 = Ramp down 2 = Ramp up 3 = Clamp minimum 4 = Clamp maximum 33 = Disabled |
| **`Val_Fault`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device fault status: 0 = None 32 = I/O fault 34 = Configuration error |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận của khối này cộng với khối kênh (enum) |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Current Object Owner ID (0=not owned) |
| **`Sts_Ramping`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=CV is ramping to target |
| **`Sts_Clamped`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=CV Set is clamped at CVMin or CVMax |
| **`Sts_SkipRoCLim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Rate of Change Limiting was skipped this scan (Maint, Ovrd, Intlk, Hand) |
| **`Sts_Active`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=CV is greater than Cfg_MaxInactiveCV, show graphic symbol as "active" |
| **`Sts_Available`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Analog Output available for control by automation (Prog) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Bypassable Interlocks are Bypassed |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Bypassing Active (Bypassed or Maintenance) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device Not Ready, see detail bits for reason |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Điều kiện Khóa liên động không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Lỗi truyền thông I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Thiết bị đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device has a Maintenance Bypass function active |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang bị Tạm hoãn (Shelved) hoặc Vô hiệu hóa (Disabled), hiển thị biểu tượng trên HMI |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Error in Config: see detail bits for reason |
| **`Err_Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: CV Limits swapped |
| **`Err_EU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Scaled CV EU Min = Max |
| **`Err_Raw`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Raw Output Scaling Min = Max |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thông số cảnh báo (Thời gian duy trì tối thiểu, thời gian Shelve, mức Severity) không hợp lệ |
| **`Sts_Hand`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Hand đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn OoS, Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_OoS`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Out of Service đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_Maint`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Maintenance đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ovrd, Ext, Prog, Oper) |
| **`Sts_Ovrd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Override đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ext, Prog, Oper) |
| **`Sts_Ext`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=External đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Prog, Oper) |
| **`Sts_Prog`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Program đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_ProgLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Program đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Oper`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_OperLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Selection equals the Normal (Prog or Oper) |
| **`Sts_ExtReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=External request inhibited, cannot Get to External from current state |
| **`Sts_ProgReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Program request inhibited, cannot Get to Program from current state |
| **`Sts_MAcqRcvd`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Maintenance Acquire command received this scan |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc lỗi ngắt tải (Shed) đã hết điều kiện lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Status: CV held or forced by interlock NOT OK (1-shot) |
| **`Alm_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Alarm: CV held or forced by interlock NOT OK |
| **`Ack_IntlkTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Interlock Trip Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IntlkTripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Interlock Trip Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IntlkTripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Interlock Trip Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IntlkTripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Interlock Trip Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Fault Status (0=OK, 1=Bad) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=I/O Fault Alarm |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=I/O Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`MRdy_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`P_AOut`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 23 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_AOut`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_CVNavTag`** | `STRING_NavTag` | `''` | [Biến cấu hình thông số] Tagname for destination of CV Navigation button |
| **`Cfg_Desc`** | `STRING_40` | `'Analog Output'` | [Biến cấu hình thông số] Description for display on HMI |
| **`Cfg_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Engineering Units for display on HMI |
| **`Cfg_Label`** | `STRING_20` | `'Analog Output'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_AOut'` | [Biến cấu hình thông số] Tagname for display on HMI |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc quản lý lựa chọn nguồn lệnh điều khiển |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_AOut'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI và phần mềm thông tin |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | Interlock Trip Alarm |
| **`IOFault`** | `P_Alarm` | `{...}` | I/O Fault Alarm |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bypass all Bypassable Interlocks |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Check (not bypass) all Interlocks |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Oper Cmd to xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset all Alarms and latched Shed conditions |
| **`ORdy_CV`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OSet_CV (enables data entry field) |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`OSet_CV`** | `REAL` | `0` | [Giá trị đặt từ màn hình HMI Operator] Operator Setting of Controlled Variable (output) (in EU) |
| **`Val_Notify`** | `SINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_PTComp`
**Thuộc chương:** Chương 1: Xử lý tín hiệu Vào/Ra (I/O Processing)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `IO Processing 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_PTComp`:** **14** Biến đầu vào (`Input Parameters`) | **8** Biến đầu ra (`Output Parameters`) | **6** Biến cấu hình cục bộ (`Local Configuration Tags`)

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


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_StrapTbl`
**Thuộc chương:** Chương 1: Xử lý tín hiệu Vào/Ra (I/O Processing)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `IO Processing 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_StrapTbl`:** **17** Biến đầu vào (`Input Parameters`) | **10** Biến đầu ra (`Output Parameters`) | **9** Biến cấu hình cục bộ (`Local Configuration Tags`)

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


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_AInHART`
**Thuộc chương:** Chương 1: Xử lý tín hiệu Vào/Ra (I/O Processing)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `IO Processing 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_AInHART`:** **107** Biến đầu vào (`Input Parameters`) | **81** Biến đầu ra (`Output Parameters`) | **54** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_AInHART`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Ref_ChanData`, `Ref_DiagTable`, `Inp_AV`, `Inp_AVSrcQ`, `Inp_AVNotify`, `Inp_AVBad`, `Inp_AVUncertain`, `Inp_Sim`, `Inp_HiHiGate`, `Inp_HiGate`, `Inp_LoGate`, `Inp_LoLoGate`, `Inp_FailGate`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_NoSubstPV`, `Cfg_HasMoreObj`, `Cfg_HasChanObj`, `Cfg_UseChanSrcQ`, `Cfg_HasHART`, `Cfg_HasPV`, `Cfg_HasSV`, `Cfg_HasTV`, `Cfg_HasFV`, `Cfg_UseVarSts`, `Cfg_UseHARTText`, `Cfg_UseHARTScaling`, `Cfg_AutoUpdDevInfo`, `Cfg_ManUpdDevInfo`, `Cfg_PVDecPlcs`, `Cfg_SVDecPlcs`, `Cfg_TVDecPlcs`, `Cfg_FVDecPlcs`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_EU`, `Cfg_FVEU`, `Cfg_FVLabel`, `Cfg_Label`, `Cfg_PVEU`, `Cfg_PVLabel`, `Cfg_SVEU`, `Cfg_SVLabel`, `Cfg_Tag`, `Cfg_TVEU`, `Cfg_TVLabel`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_AInHART` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PCmd_ClearCapt`, `PCmd_Reset`, `PCmd_HiHiAck`, `PCmd_HiAck`, `PCmd_LoAck`, `PCmd_LoLoAck`, `PCmd_FailAck`, `PCmd_FailSuppress`, `PCmd_FailUnsuppress`, `PCmd_HiHiSuppress`, `PCmd_HiHiUnsuppress`, `PCmd_HiSuppress`, `PCmd_HiUnsuppress`, `PCmd_LoLoSuppress`, `PCmd_LoLoUnsuppress`, `PCmd_LoSuppress`, `PCmd_LoUnsuppress`, `PCmd_HiHiUnshelve`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Val`, `Val_InpAV`, `Val_PVMinCapt`, `Val_PVMaxCapt`, `Val_PVEUMin`, `Val_PVEUMax`, `Val_PV`, `Val_SV`, `Val_TV`, `Val_FV`, `Val_DiagCode1`, `Val_DiagCode2`, `Val_DiagCode3`, `Val_NAMURSts`, `Val_NAMURSts1`, `Val_NAMURSts2`, `Val_NAMURSts3`, `Val_Fault`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 107 Biến Đầu Vào (`Input Parameters`) của `P_AInHART`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Enable Input - System Defined Parameter |
| **`Ref_ChanData`** | `HART_ChanData` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] Channel Data from HART AO Channel |
| **`Ref_DiagTable`** | `P_DiagTable[1]` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] Lookup table for Diagnostic Code to text |
| **`Inp_AV`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Direct AV from Input Assembly (raw units) |
| **`Inp_AVSrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã nguồn gốc và chất lượng tín hiệu đầu vào (nối từ chân SrcQ của khối P_AIChan nếu có). |
| **`Inp_AVNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Related AV (channel) object alarm prio and ack status (enum) |
| **`Inp_AVBad`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=AV or I/O Comms Status Bad, 0=OK |
| **`Inp_AVUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=AV Value Not Reliable, 0=OK |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Use simulated analog PV (Set_SimPV) 0 = Use analog Input (Inp_PV) |
| **`Inp_HiHiGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát ngưỡng: Khi = 1, bật kiểm tra ngưỡng tương ứng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay). Khi = 0, tắt giám sát và ép trạng thái ngõ ra về 0 (dùng để ức chế cảnh báo khi thiết bị dừng). |
| **`Inp_HiGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát ngưỡng: Khi = 1, bật kiểm tra ngưỡng tương ứng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay). Khi = 0, tắt giám sát và ép trạng thái ngõ ra về 0 (dùng để ức chế cảnh báo khi thiết bị dừng). |
| **`Inp_LoGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát ngưỡng: Khi = 1, bật kiểm tra ngưỡng tương ứng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay). Khi = 0, tắt giám sát và ép trạng thái ngõ ra về 0 (dùng để ức chế cảnh báo khi thiết bị dừng). |
| **`Inp_LoLoGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát ngưỡng: Khi = 1, bật kiểm tra ngưỡng tương ứng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay). Khi = 0, tắt giám sát và ép trạng thái ngõ ra về 0 (dùng để ức chế cảnh báo khi thiết bị dừng). |
| **`Inp_FailGate`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân cổng điều kiện (Gate Input) cho phép giám sát ngưỡng: Khi = 1, bật kiểm tra ngưỡng tương ứng (áp dụng bộ định thời On-Delay/Off-Delay sau thời gian trễ mở cổng Gate Delay). Khi = 0, tắt giám sát và ép trạng thái ngõ ra về 0 (dùng để ức chế cảnh báo khi thiết bị dừng). |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Input parameter that is used to programmatically reset alarms. When set to 1, all alarms that require a reset are reset. |
| **`Cfg_NoSubstPV`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Khóa tính năng nhập giá trị thay thế (Substitute PV) của Bảo trì: Khi = 0, cho phép nhân viên bảo trì nhập giá trị PV thay thế; khi = 1, cấm sử dụng tính năng Substitute PV. |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_HasChanObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Tells HMI a Channel object (AIChan, etc.) is used for Inp_PV |
| **`Cfg_UseChanSrcQ`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Truyền thẳng mã chất lượng SrcQ từ khối kênh P_AIChan sang; 0 = Tự tính toán mã SrcQ nội bộ. |
| **`Cfg_HasHART`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = HART instrument 0 = non-HART (4…20 mA only) instrument |
| **`Cfg_HasPV`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Digital variable is configured and displayed: • PV (primary variable) • SV (secondary variable) • TV (third variable) • FV (fourth variable) |
| **`Cfg_HasSV`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Digital variable is configured and displayed: • PV (primary variable) • SV (secondary variable) • TV (third variable) • FV (fourth variable) |
| **`Cfg_HasTV`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Digital variable is configured and displayed: • PV (primary variable) • SV (secondary variable) • TV (third variable) • FV (fourth variable) |
| **`Cfg_HasFV`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Digital variable is configured and displayed: • PV (primary variable) • SV (secondary variable) • TV (third variable) • FV (fourth variable) |
| **`Cfg_UseVarSts`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Use HART digital variables' status, 0=ignore variable stattus |
| **`Cfg_UseHARTText`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Use HART text for Description, Label, Tag, engineering units 0 = Manually entered |
| **`Cfg_UseHARTScaling`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Use HART scaling for raw, engineering units ranges 0 = Manually entered ranges |
| **`Cfg_AutoUpdDevInfo`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = automatically update device information 0 = no auto update |
| **`Cfg_ManUpdDevInfo`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = allow manual device information update request 0 = disallow |
| **`Cfg_PVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho giá trị PV trên HMI (từ 0 đến 6 chữ số). |
| **`Cfg_SVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Number of decimal places for SV display (0..6) |
| **`Cfg_TVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Number of decimal places for TV display (0..6) |
| **`Cfg_FVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Number of decimal places for FV display (0..6) |
| **`Cfg_HasHiHiAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasHiAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasLoAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasLoLoAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasFailAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HiHiResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_HiResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_LoResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_LoLoResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_FailResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_HiHiAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_HiAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_LoAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_LoLoAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_FailAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_HiHiSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_HiSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_LoSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_LoLoSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_FailSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_InpRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Input (unscaled) minimum for Scaling |
| **`Cfg_InpRawMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Input (unscaled) maximum for Scaling |
| **`Cfg_PVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Analog PV (Output) minimum for Scaling to engineering units |
| **`Cfg_PVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Analog PV (Output) maximum for Scaling to engineering units |
| **`Cfg_FiltTC`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Analog PV Filter Time Constant (s), 0.0 = unfiltered |
| **`Cfg_HiHiDB`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) cho ngưỡng cảnh báo để chống báo động giả lặp lại khi tín hiệu dao động quanh ngưỡng. |
| **`Cfg_HiDB`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) cho ngưỡng cảnh báo để chống báo động giả lặp lại khi tín hiệu dao động quanh ngưỡng. |
| **`Cfg_LoDB`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) cho ngưỡng cảnh báo để chống báo động giả lặp lại khi tín hiệu dao động quanh ngưỡng. |
| **`Cfg_LoLoDB`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) cho ngưỡng cảnh báo để chống báo động giả lặp lại khi tín hiệu dao động quanh ngưỡng. |
| **`Cfg_FailDB`** | `BOOL` | `0.41666666000000002` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình vùng chết / độ trễ hồi tiếp (Deadband / Hysteresis, đơn vị EU) cho ngưỡng cảnh báo để chống báo động giả lặp lại khi tín hiệu dao động quanh ngưỡng. |
| **`Cfg_HiHiOnDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây): Tín hiệu phải vượt ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/cảnh báo (chống nhiễu thoáng qua). |
| **`Cfg_HiOnDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây): Tín hiệu phải vượt ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/cảnh báo (chống nhiễu thoáng qua). |
| **`Cfg_LoOnDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây): Tín hiệu phải vượt ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/cảnh báo (chống nhiễu thoáng qua). |
| **`Cfg_LoLoOnDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây): Tín hiệu phải vượt ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/cảnh báo (chống nhiễu thoáng qua). |
| **`Cfg_FailOnDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ tác động bật (On-Delay, tính bằng giây): Tín hiệu phải vượt ngưỡng liên tục đủ thời gian này mới kích hoạt trạng thái/cảnh báo (chống nhiễu thoáng qua). |
| **`Cfg_HiHiOffDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây): Tín hiệu phải trở về vùng an toàn (đã trừ Deadband) liên tục đủ thời gian này mới xóa trạng thái/cảnh báo. |
| **`Cfg_HiOffDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây): Tín hiệu phải trở về vùng an toàn (đã trừ Deadband) liên tục đủ thời gian này mới xóa trạng thái/cảnh báo. |
| **`Cfg_LoOffDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây): Tín hiệu phải trở về vùng an toàn (đã trừ Deadband) liên tục đủ thời gian này mới xóa trạng thái/cảnh báo. |
| **`Cfg_LoLoOffDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây): Tín hiệu phải trở về vùng an toàn (đã trừ Deadband) liên tục đủ thời gian này mới xóa trạng thái/cảnh báo. |
| **`Cfg_FailOffDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ nhả tắt (Off-Delay, tính bằng giây): Tín hiệu phải trở về vùng an toàn (đã trừ Deadband) liên tục đủ thời gian này mới xóa trạng thái/cảnh báo. |
| **`Cfg_HiHiGateDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng. |
| **`Cfg_HiGateDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng. |
| **`Cfg_LoGateDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng. |
| **`Cfg_LoLoGateDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng. |
| **`Cfg_FailGateDly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở cổng giám sát (Gate Delay, tính bằng giây): Khoảng thời gian chân cổng điều kiện (Gate Input) phải duy trì ở mức 1 trước khi bắt đầu cho phép giám sát ngưỡng. |
| **`Cfg_FailHiLim`** | `REAL` | `103.958336` | Ẩn (Tag) | [Biến cấu hình thông số] Out-of-Range (fail) High Limit (engineering units) |
| **`Cfg_FailLoLim`** | `REAL` | `-2.0833333000000001` | Ẩn (Tag) | [Biến cấu hình thông số] Out-of-Range (fail) Low Limit (engineering units) |
| **`Set_SimPV`** | `REAL` | `0` | Ẩn (Tag) | Analog PV used in Simulation (Inp_Sim = 1) (engineering units) |
| **`Set_SimHARTPV`** | `REAL` | `0` | Ẩn (Tag) | PV, SV, TV, or FV used in Simulation (Inp_Sim = 1) (primary, secondary, third, or fourth value in engineering units) |
| **`Set_SimHARTSV`** | `REAL` | `0` | Ẩn (Tag) | PV, SV, TV, or FV used in Simulation (Inp_Sim = 1) (primary, secondary, third, or fourth value in engineering units) |
| **`Set_SimHARTTV`** | `REAL` | `0` | Ẩn (Tag) | PV, SV, TV, or FV used in Simulation (Inp_Sim = 1) (primary, secondary, third, or fourth value in engineering units) |
| **`Set_SimHARTFV`** | `REAL` | `0` | Ẩn (Tag) | PV, SV, TV, or FV used in Simulation (Inp_Sim = 1) (primary, secondary, third, or fourth value in engineering units) |
| **`PCmd_ClearCapt`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_ClearCapt = 1 để xóa giá trị cực tiểu/cực đại (Min/Max Peak) đã lưu của PV (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_Reset = 1 để xóa tất cả các cảnh báo chốt đang chờ Reset (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_HiHiAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_HiAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_LoAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_LoLoAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_FailAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_FailSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Analog Input Failure Alarm |
| **`PCmd_FailUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Analog Input Failure Alarm |
| **`PCmd_HiHiSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Cảnh báo Rất Cao (High-Cảnh báo Cao (High Alarm)) |
| **`PCmd_HiHiUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Cảnh báo Rất Cao (High-Cảnh báo Cao (High Alarm)) |
| **`PCmd_HiSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Cảnh báo Cao (High Alarm) |
| **`PCmd_HiUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Cảnh báo Cao (High Alarm) |
| **`PCmd_LoLoSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Cảnh báo Rất Thấp (Low-Cảnh báo Thấp (Low Alarm)) |
| **`PCmd_LoLoUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Cảnh báo Rất Thấp (Low-Cảnh báo Thấp (Low Alarm)) |
| **`PCmd_LoSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Cảnh báo Thấp (Low Alarm) |
| **`PCmd_LoUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Cảnh báo Thấp (Low Alarm) |
| **`PCmd_HiHiUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_HiUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_LoUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_LoLoUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_FailUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |

## 3. Bảng chi tiết 81 Biến Đầu Ra (`Output Parameters`) của `P_AInHART`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Enable Output - System Defined Parameter |
| **`Val`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu ra chính PV (đã qua quy đổi đơn vị kỹ thuật EU, lọc nhiễu và thay thế PV nếu dùng) (EU) |
| **`Val_InpAV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị đầu vào thực tế (đã quy đổi EU nhưng trước khi qua bộ chọn giá trị thay thế Substitute PV) |
| **`Val_PVMinCapt`** | `REAL` | `1.5000000000000001E+38` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Captured (Analog) PV Minimum (excursion) since last cleared |
| **`Val_PVMaxCapt`** | `REAL` | `-1.5000000000000001E+38` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Captured (Analog) PV Maximum (excursion) since last cleared |
| **`Val_PVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cực tiểu của dải đo đơn vị kỹ thuật = MIN(Cfg_PVEUMin, Cfg_PVEUMax) |
| **`Val_PVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cực đại của dải đo đơn vị kỹ thuật = MAX(Cfg_PVEUMin, Cfg_PVEUMax) |
| **`Val_PV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Digital (HART) Primary Variable (PV) value (PVEU) |
| **`Val_SV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Digital (HART) Secondary Variable (SV) value (SVEU) |
| **`Val_TV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Digital (HART) Third Variable (TV) value (TVEU) |
| **`Val_FV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Digital (HART) Fourth Variable (FV) value (FVEU) |
| **`Val_DiagCode1`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] HART Diagnostic Code #1 (-1 = none) |
| **`Val_DiagCode2`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] HART Diagnostic Code #2 (-1 = none) |
| **`Val_DiagCode3`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] HART Diagnostic Code #3 (-1 = none) |
| **`Val_NAMURSts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] NAMUR NE107 Status: worst of three diagnostics, for breadcrumb |
| **`Val_NAMURSts1`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] NAMUR NE107 Status for HART Diagnostic Code: 0 = OK 1 = Information 2 = Maintenance required 4 = Off specification (uncertain) 8 = Function check (substitution) 16 = Failure |
| **`Val_NAMURSts2`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] NAMUR NE107 Status for HART Diagnostic Code: 0 = OK 1 = Information 2 = Maintenance required 4 = Off specification (uncertain) 8 = Function check (substitution) 16 = Failure |
| **`Val_NAMURSts3`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] NAMUR NE107 Status for HART Diagnostic Code: 0 = OK 1 = Information 2 = Maintenance required 4 = Off specification (uncertain) 8 = Function check (substitution) 16 = Failure |
| **`SrcQ_IO`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Source and Quality of primary analog input (enumeration) |
| **`SrcQ`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final analog PV source and quality. GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`SrcQ_PV`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Source and Quality of HART PV value (enumeration) |
| **`SrcQ_SV`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Source and Quality of HART SV value (enumeration) |
| **`SrcQ_TV`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Source and Quality of HART TV value (enumeration) |
| **`SrcQ_FV`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Source and Quality of HART FV value (enumeration) |
| **`Val_Fault`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Fault Status: 0 = none 20 = Lo 21 = Hi 24 = LoLo 25 = HiHi 32 = Fail 34 = CfgErr |
| **`Val_NotifyAll`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |
| **`Val_UnackAlmC`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Sts_SubstPV`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Using Substitute PV (Input being overridden) |
| **`Sts_PVBad`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Tín hiệu PV có chất lượng xấu (Bad Quality) hoặc vượt ngoài dải giới hạn lỗi (Out of Range) |
| **`Sts_PVUncertain`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị PV có chất lượng không chắc chắn (Uncertain Quality) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang bị Tạm hoãn (Shelved) hoặc Vô hiệu hóa (Disabled), hiển thị biểu tượng trên HMI |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình (kiểm tra các bit Err_* chi tiết để biết nguyên nhân) |
| **`Err_Raw`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị dải thô Cfg_InpRawMin bằng Cfg_InpRawMax |
| **`Err_EU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị dải kỹ thuật Cfg_PVEUMin bằng Cfg_PVEUMax |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thời gian trễ On/Off/Gate Delay ngoài phạm vi hợp lệ (0..2147483 giây) |
| **`Err_Filt`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thông số bộ lọc PV (RateTime, hằng số thời gian TC) âm hoặc không hợp lệ |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thông số cảnh báo (Thời gian duy trì tối thiểu, thời gian Shelve, mức Severity) không hợp lệ |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc lỗi ngắt tải (Shed) đã hết điều kiện lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_HiHiCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV High-High kết quả so sánh ngưỡng trực tiếp 1=High-High |
| **`Sts_HiHiGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV High-High trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_HiHi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị Analog vượt trên ngưỡng Rất Cao (High-High) |
| **`Alm_HiHi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Ngõ vào Analog đang ở trạng thái Cảnh báo Rất Cao (High-High) |
| **`Ack_HiHi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Cảnh báo Rất Cao (Cảnh báo Rất Cao (High-Cảnh báo Cao (High Alarm))) đã được Xác nhận (Acknowledged) |
| **`Sts_HiHiDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Rất Cao (Cảnh báo Rất Cao (High-Cảnh báo Cao (High Alarm))) đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_HiHiSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Rất Cao (Cảnh báo Rất Cao (High-Cảnh báo Cao (High Alarm))) đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_HiHiShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Rất Cao (Cảnh báo Rất Cao (High-Cảnh báo Cao (High Alarm))) đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_HiCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV High kết quả so sánh ngưỡng trực tiếp 1=High |
| **`Sts_HiGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV High trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_Hi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị Analog vượt trên ngưỡng Cao (High) |
| **`Alm_Hi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Ngõ vào Analog đang ở trạng thái Cảnh báo Cao (High) |
| **`Ack_Hi`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Cảnh báo Cao (Cảnh báo Cao (High Alarm)) đã được Xác nhận (Acknowledged) |
| **`Sts_HiDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Cao (Cảnh báo Cao (High Alarm)) đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_HiSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Cao (Cảnh báo Cao (High Alarm)) đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_HiShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Cao (Cảnh báo Cao (High Alarm)) đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_LoCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV Low kết quả so sánh ngưỡng trực tiếp 1=Low |
| **`Sts_LoGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV Low trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_Lo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị Analog tụt dưới ngưỡng Thấp (Low) |
| **`Alm_Lo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Ngõ vào Analog đang ở trạng thái Cảnh báo Thấp (Low) |
| **`Ack_Lo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Cảnh báo Thấp (Cảnh báo Thấp (Low Alarm)) đã được Xác nhận (Acknowledged) |
| **`Sts_LoDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Thấp (Cảnh báo Thấp (Low Alarm)) đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_LoSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Thấp (Cảnh báo Thấp (Low Alarm)) đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_LoShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Thấp (Cảnh báo Thấp (Low Alarm)) đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_LoLoCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV Low-Low kết quả so sánh ngưỡng trực tiếp 1=Low-Low |
| **`Sts_LoLoGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV Low-Low trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_LoLo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị Analog tụt dưới ngưỡng Rất Thấp (Low-Low) |
| **`Alm_LoLo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Ngõ vào Analog đang ở trạng thái Cảnh báo Rất Thấp (Low-Low) |
| **`Ack_LoLo`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Cảnh báo Rất Thấp (Cảnh báo Rất Thấp (Low-Cảnh báo Thấp (Low Alarm))) đã được Xác nhận (Acknowledged) |
| **`Sts_LoLoDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Rất Thấp (Cảnh báo Rất Thấp (Low-Cảnh báo Thấp (Low Alarm))) đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_LoLoSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Rất Thấp (Cảnh báo Rất Thấp (Low-Cảnh báo Thấp (Low Alarm))) đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_LoLoShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo Rất Thấp (Cảnh báo Rất Thấp (Low-Cảnh báo Thấp (Low Alarm))) đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_FailCmp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV Fail kết quả so sánh ngưỡng trực tiếp 1=Out of Range |
| **`Sts_FailGate`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] PV Fail trạng thái hoàn tất trễ cổng Gate (1 = Đã mở cổng) |
| **`Sts_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Ngõ vào Analog vượt ngoài dải đo hoặc chất lượng tín hiệu xấu (PV Bad) |
| **`Alm_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Cảnh báo lỗi ngõ vào Analog (Fail Alarm) (PV Bad or Out of Range) |
| **`Ack_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Cảnh báo lỗi ngõ vào Analog (Fail Alarm) đã được Xác nhận (Acknowledged) |
| **`Sts_FailDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi ngõ vào Analog (Fail Alarm) đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_FailSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi ngõ vào Analog (Fail Alarm) đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_FailShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi ngõ vào Analog (Fail Alarm) đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`P_AInHART`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 54 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_AInHART`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_8` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'Analog Input (HART)'` | [Biến cấu hình thông số] Description for display on HMI |
| **`Cfg_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Engineering Units for display on HMI (from lookup table) |
| **`Cfg_FVEU`** | `STRING_8` | `''` | [Biến cấu hình thông số] Engineering Units for HART FV display on HMI |
| **`Cfg_FVLabel`** | `STRING_16` | `''` | [Biến cấu hình thông số] Label for Fourth Variable for display on HMI |
| **`Cfg_Label`** | `STRING_20` | `'Analog Input (HART)'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI |
| **`Cfg_PVEU`** | `STRING_8` | `''` | [Biến cấu hình thông số] Engineering Units for HART PV display on HMI |
| **`Cfg_PVLabel`** | `STRING_16` | `''` | [Biến cấu hình thông số] Label for Primary Variable for display on HMI |
| **`Cfg_SVEU`** | `STRING_8` | `''` | [Biến cấu hình thông số] Engineering Units for HART SV display on HMI |
| **`Cfg_SVLabel`** | `STRING_16` | `''` | [Biến cấu hình thông số] Label for Secondary Variable for display on HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_AInHART'` | [Biến cấu hình thông số] Tagname for display on HMI |
| **`Cfg_TVEU`** | `STRING_8` | `''` | [Biến cấu hình thông số] Engineering Units for HART TV display on HMI |
| **`Cfg_TVLabel`** | `STRING_16` | `''` | [Biến cấu hình thông số] Label for Third Variable for display on HMI |
| **`Fail`** | `P_Alarm` | `{...}` | Khối con quản lý Cảnh báo lỗi ngõ vào Analog (chất lượng xấu hoặc vượt dải) |
| **`FailGate`** | `P_Gate` | `{...}` | Khối con xử lý Cổng điều kiện và Trễ thời gian cho cảnh báo lỗi (Fail Gate) |
| **`Hi`** | `P_Alarm` | `{...}` | Cảnh báo Cao (High Alarm) |
| **`HiGate`** | `P_Gate` | `{...}` | Khối con xử lý Cổng điều kiện và Trễ thời gian cho cảnh báo Cao (Hi Gate) |
| **`HiHi`** | `P_Alarm` | `{...}` | Cảnh báo Rất Cao (High-Cảnh báo Cao (High Alarm)) |
| **`HiHiGate`** | `P_Gate` | `{...}` | Khối con xử lý Cổng điều kiện và Trễ thời gian cho cảnh báo Rất Cao (HiHi Gate) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_AInHART'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI và phần mềm thông tin |
| **`Lo`** | `P_Alarm` | `{...}` | Cảnh báo Thấp (Low Alarm) |
| **`LoGate`** | `P_Gate` | `{...}` | Khối con xử lý Cổng điều kiện và Trễ thời gian cho cảnh báo Thấp (Lo Gate) |
| **`LoLo`** | `P_Alarm` | `{...}` | Cảnh báo Rất Thấp (Low-Cảnh báo Thấp (Low Alarm)) |
| **`LoLoGate`** | `P_Gate` | `{...}` | Khối con xử lý Cổng điều kiện và Trễ thời gian cho cảnh báo Rất Thấp (LoLo Gate) |
| **`MCmd_InpPV`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Bảo trì chọn sử dụng tín hiệu PV thực tế (chế độ bình thường) |
| **`MCmd_SubstPV`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Bảo trì chọn sử dụng giá trị PV thay thế (ghi đè đầu vào) |
| **`MCmd_UpdDevInfo`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để request update of device info |
| **`MRdy_InpPV`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_InpPV (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_SubstPV`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_SubstPV (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_UpdDevInfo`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_UpdDevInfo (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MSet_SubstPV`** | `REAL` | `0` | [Giá trị đặt từ tab Bảo trì (Maintenance)] Giá trị PV thay thế do nhân viên bảo trì nhập (đơn vị kỹ thuật EU) |
| **`OCmd_ClearCapt`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Clear the captured min / max PV excursion values |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset all Alarms requiring Reset |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset and Xác nhận (Acknowledge) all Alarms |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`Val_DiagDesc1`** | `STRING_32` | `‘Logic not yet scanned.’` | [Giá trị thực tế đầu ra (Logic/HMI)] Device Diagnostic Description #1 (from diag. code lookup) |
| **`Val_DiagDesc2`** | `STRING_32` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Device Diagnostic Description #2 (from diag. code lookup) |
| **`Val_DiagDesc3`** | `STRING_32` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Device Diagnostic Description #3 (from diag. code lookup) |
| **`Val_HART_Desc`** | `STRING_32` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Description Text received via HART |
| **`Val_HART_EU`** | `STRING_8` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Analog Value Engineering Units Text received via HART |
| **`Val_HART_FVEU`** | `STRING_8` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] FV Engineering Units Text received via HART |
| **`Val_HART_InpRawMax`** | `REAL` | `100` | [Giá trị thực tế đầu ra (Logic/HMI)] Signal Maximum (for scaling) received via HART |
| **`Val_HART_InpRawMin`** | `REAL` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Signal Minimum (for scaling) received via HART |
| **`Val_HART_Label`** | `STRING_20` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Label Text received via HART |
| **`Val_HART_PVEU`** | `STRING_8` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] PV Engineering Units Text received via HART |
| **`Val_HART_PVEUMax`** | `REAL` | `100` | [Giá trị thực tế đầu ra (Logic/HMI)] Analog Val;ue Maximum (for scaling) received via HART |
| **`Val_HART_PVEUMin`** | `REAL` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Analog Val;ue Minimum (for scaling) received via HART |
| **`Val_HART_SVEU`** | `STRING_8` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] SV Engineering Units Text received via HART |
| **`Val_HART_Tag`** | `STRING_20` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Tag Text received via HART |
| **`Val_HART_TVEU`** | `STRING_8` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] TV Engineering Units Text received via HART |
| **`Val_Notify`** | `SINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận của riêng khối này (enum) |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_AOutHART`
**Thuộc chương:** Chương 1: Xử lý tín hiệu Vào/Ra (I/O Processing)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `IO Processing 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_AOutHART`:** **80** Biến đầu vào (`Input Parameters`) | **78** Biến đầu ra (`Output Parameters`) | **48** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_AOutHART`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Ref_ChanData`, `Ref_DiagTable`, `Inp_IntlkOK`, `Inp_NBIntlkOK`, `Inp_IOFault`, `Inp_Sim`, `Inp_Hand`, `Inp_Ovrd`, `Inp_OvrdCV`, `Inp_ExtInh`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_ShedHold`, `Cfg_SkipRoCLim`, `Cfg_SetTrack`, `Cfg_SetTrackOvrdHand`, `Cfg_HasHART`, `Cfg_HasMoreObj`, `Cfg_HasPV`, `Cfg_HasSV`, `Cfg_HasTV`, `Cfg_HasFV`, `Cfg_UseVarSts`, `Cfg_UseHARTText`, `Cfg_UseHARTScaling`, `Cfg_AutoUpdDevInfo`, `Cfg_ManUpdDevInfo`, `Cfg_HasIntlkObj`, `Cfg_HasCVNav`, `Cfg_OvrdIntlk`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_CVNavTag`, `Cfg_Desc`, `Cfg_EU`, `Cfg_FVEU`, `Cfg_FVLabel`, `Cfg_Label`, `Cfg_PVEU`, `Cfg_PVLabel`, `Cfg_SVEU`, `Cfg_SVLabel`, `Cfg_Tag`, `Cfg_TVEU`, `Cfg_TVLabel`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_AOutHART` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PSet_CV`, `PSet_Owner`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_IntlkTripAck`, `PCmd_IntlkTripSuppress`, `PCmd_IntlkTripUnsuppress`, `PCmd_IntlkTripUnshelve`, `PCmd_IOFaultAck`, `PCmd_IOFaultSuppress`, `PCmd_IOFaultUnsuppress`, `PCmd_IOFaultUnshelve`
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Out_CV`, `Val_CVSet`, `Val_CVOut`, `Val_CVFdbk`, `Val_CVEUMin`, `Val_CVEUMax`, `Val_PV`, `Val_SV`, `Val_TV`, `Val_FV`, `Val_DiagCode1`, `Val_DiagCode2`, `Val_DiagCode3`, `Val_NAMURSts`, `Val_NAMURSts1`, `Val_NAMURSts2`, `Val_NAMURSts3`, `Val_Sts`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 80 Biến Đầu Vào (`Input Parameters`) của `P_AOutHART`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Enable Input - System Defined Parameter |
| **`Ref_ChanData`** | `HART_ChanData` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] Channel Data from HART AO Channel |
| **`Ref_DiagTable`** | `P_DiagTable[1]` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] Lookup table for Diagnostic Code to text |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Interlocks OK, Analog Output can be set |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Non-bypassable interlocks OK, analog output can be set if bypassable interlocks are bypassed. |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Input Communication Status 0=OK, 1=fail |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Simulation input. When set to 0, the instruction operates normally. When set to 1, the instruction acts as normal but the output is held to at zero. |
| **`Inp_Hand`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Acquire Hand (typically hardwired local) Mode 0 = Release Hand Mode |
| **`Inp_Ovrd`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Acquire Override (higher priority program logic) Mode 0 = Release Override Mode |
| **`Inp_OvrdCV`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] CV target in Override Mode |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Inhibit External Acquisition, 0=Allow External Acquisition |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Reset shed latches and cleared alarms |
| **`Cfg_ShedHold`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Hold Output on Interlock 0 = Go to Cfg_IntlkCV |
| **`Cfg_SkipRoCLim`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Skip rate of change limiting in Interlock, Maintenance, and Override modes |
| **`Cfg_SetTrack`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] This parameter is used to configure bumpless behavior of setting parameters when switching modes. When this parameter is 1 and the instruction is in Program mode, the operator settings track the program settings. When this parameter is 1 and the instruction in Operator mode the program settings track the operator settings; and the simulation inputs match the output values (transitions are bumpless). When this parameter is 0, the operator settings and program settings are not modified by this instruction. In this case, when the mode is changed, the effective value of the setting can change depending on the program-set and operator-set values. |
| **`Cfg_SetTrackOvrdHand`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Prog/Operator Settings track Override/Hand CV |
| **`Cfg_HasHART`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=HART instrument, 0=non-HART (4-20 mA only) instrument |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_HasPV`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Digital PV (primary variable) is configured |
| **`Cfg_HasSV`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Digital SV (secondary variable) is configured |
| **`Cfg_HasTV`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Digital TV (third variable) is configured |
| **`Cfg_HasFV`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Digital FV (fourth variable) is configured |
| **`Cfg_UseVarSts`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Use HART digital variables' status, 0=ignore variable stattus |
| **`Cfg_UseHARTText`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Use HART text for Desc, Label, Tag, EU; 0=Manually entered |
| **`Cfg_UseHARTScaling`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Use HART scaling for raw, EU ranges; 0=Manually entered ranges |
| **`Cfg_AutoUpdDevInfo`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=automatically update device info, 0=no auto update |
| **`Cfg_ManUpdDevInfo`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=allow manual device info update request, 0=disallow |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tells the HMI that an interlock object is connected to Inp_Intlk. IMPORTANT: The name of the Interlock object in the controller must be this object name with the suffix ‘_Intlk’. For example, if your P_AOutHART object has the name ’AOut123’, then its Interlock object must be named ‘AOut123_Intlk’. |
| **`Cfg_HasCVNav`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tells HMI to enable navigation to a connected CV object by using the tag name in Cfg_CVNavTag |
| **`Cfg_OvrdIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Override ignores Bypassable Intlk; 0=always use Intlk |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Dừng (Stop) động cơ and Alarm on I/O Fault; 0=Alarm only on I/O Fault |
| **`Cfg_DecPlcs`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Number of decimal places for CV display (0..6) |
| **`Cfg_PVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho giá trị PV trên HMI (từ 0 đến 6 chữ số). |
| **`Cfg_SVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Number of decimal places for SV display (0..6) |
| **`Cfg_TVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Number of decimal places for TV display (0..6) |
| **`Cfg_FVDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Number of decimal places for FV display (0..6) |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Interlock Trip Alarm exists and will be checked |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=I/O Fault Alarm exists and will be checked |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Reset required to clear Interlock Trip Alarm |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Reset required to clear I/O Fault Alarm |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Xác nhận (Acknowledge) required for Interlock Trip Alarm |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Xác nhận (Acknowledge) required for I/O Fault Alarm |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Interlock Trip Alarm Severity: 1..250=Low, 251..500=Medium, 501..750=High, 751..1000=Urgent |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] I/O Fault Alarm Severity: 1..250=Low, 251..500=Medium, 501..750=High, 751..1000=Urgent |
| **`Cfg_MinCV`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum CV (in EU, for limiting) |
| **`Cfg_MaxCV`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Maximum CV (in EU, for limiting) |
| **`Cfg_CVRoCLimInc`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] CV Rate of Change Limit, Increasing, (EU/sec, 0=unlimited) |
| **`Cfg_CVRoCLimDec`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] CV Rate of Change Limit, Increasing, (EU/sec, 0=unlimited) |
| **`Cfg_IntlkCV`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] CV Target when interlocked (if not Cfg_ShedHold) (EU) |
| **`Cfg_CVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] CV Minimum in Engineering Units (for scaling) |
| **`Cfg_CVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] CV Maximum in Engineering Units (for scaling) |
| **`Cfg_CVRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] CV Minimum in I/O (raw) Units (for scaling) |
| **`Cfg_CVRawMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] CV Maximum in I/O (raw) Units (for scaling) |
| **`Cfg_MaxInactiveCV`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] When Val_CVOut is greater than this value (in CV EU) set Sts_Active (for HMI) |
| **`PSet_CV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Program Setting of Controlled Variable (output) (in EU) |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Mã định danh chủ sở hữu chương trình (Program Owner ID: khác 0 để đăng ký chiếm quyền, bằng 0 để nhả quyền). |
| **`XSet_CV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài (External)] External Setting of Controlled Variable (output) (in EU) |
| **`Set_SimHARTPV`** | `REAL` | `0` | Ẩn (Tag) | PV used in Simulation (Inp_Sim=1) (PVEU) |
| **`Set_SimHARTSV`** | `REAL` | `0` | Ẩn (Tag) | SV used in Simulation (Inp_Sim=1) (SVEU) |
| **`Set_SimHARTTV`** | `REAL` | `0` | Ẩn (Tag) | TV used in Simulation (Inp_Sim=1) (TVEU) |
| **`Set_SimHARTFV`** | `REAL` | `0` | Ẩn (Tag) | FV used in Simulation (Inp_Sim=1) (FVEU) |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để chuyển quyền điều khiển sang Tự động (từ Operator sang Program) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để chuyển quyền điều khiển sang Bằng tay (từ Program sang Operator) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa cứng chế độ Program (không cho phép Operator giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Mở khóa chế độ Program (cho phép Operator lấy quyền điều khiển) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để trả về nguồn lệnh Bình thường mặc định (Normal: Program hoặc Operator) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Interlock Trip Alarm |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Interlock Trip Alarm |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Interlock Trip Alarm |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Interlock Trip Alarm |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) I/O Fault Alarm |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) I/O Fault Alarm |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) I/O Fault Alarm |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) I/O Fault Alarm |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Release (Maint to Oper/Prog/Ovrd) |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Unlock Operator (allow Program to acquire) |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Acquire (Oper/Prog to Ext) |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Release (Ext to Oper/Prog) |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi |

## 3. Bảng chi tiết 78 Biến Đầu Ra (`Output Parameters`) của `P_AOutHART`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Enable Output - System Defined Parameter |
| **`Out_CV`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] CV Output in Raw (I/O Card) Units |
| **`Val_CVSet`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Value of selected CV Setting (before rate limiting, in EU) |
| **`Val_CVOut`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Value of CV Output (after rate limiting) (in engineering units) |
| **`Val_CVFdbk`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Value of CV read back from analog output channel IMPORTANT: This variable is an echo of the CV provided by the I/O card and does NOT represent an actual position of any actuator device. The actual position can be configured as one of the HART variables: PV, SV, TV or FV, and depends on what data the device is configured to send in those variables. |
| **`Val_CVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Minimum of scaled range = MIN (Cfg_CVEUMin, Cfg_CVEUMax) |
| **`Val_CVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Maximum of scaled range = MAX (Cfg_CVEUMin, Cfg_CVEUMax) |
| **`Val_PV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Value of HART Primary Variable (PV) |
| **`Val_SV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Value of HART Secondary Variable (SV) |
| **`Val_TV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Value of HART Third Variable (TV) |
| **`Val_FV`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Value of HART Fourth Variable (FV) |
| **`Val_DiagCode1`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] HART Diagnostic Code #1 (-1 = none) |
| **`Val_DiagCode2`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] HART Diagnostic Code #2 (-1 = none) |
| **`Val_DiagCode3`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] HART Diagnostic Code #3 (-1 = none) |
| **`Val_NAMURSts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] NAMUR NE107 Status: worst of three diagnostics, for breadcrumb |
| **`Val_NAMURSts1`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] NAMUR NE107 Status for HART Diagnostic Code: 0 = OK 1 = Information 2 = Maintenance required 4 = Off specification (uncertain) 8 = Function check (substitution) 16 = Failure |
| **`Val_NAMURSts2`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] NAMUR NE107 Status for HART Diagnostic Code: 0 = OK 1 = Information 2 = Maintenance required 4 = Off specification (uncertain) 8 = Function check (substitution) 16 = Failure |
| **`Val_NAMURSts3`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] NAMUR NE107 Status for HART Diagnostic Code: 0 = OK 1 = Information 2 = Maintenance required 4 = Off specification (uncertain) 8 = Function check (substitution) 16 = Failure |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final analog source and quality: GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`SrcQ_PV`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Source and Quality of HART PV value |
| **`SrcQ_SV`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Source and Quality of HART SV value |
| **`SrcQ_TV`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Source and Quality of HART TV value |
| **`SrcQ_FV`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Source and Quality of HART FV value |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] 0 = At target 1 = Ramp down 2 = Ramp up 3 = Clamp minimum 4 = Clamp maximum 33 = Disabled |
| **`Val_Fault`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device fault status: 0 = None 32 = I/O fault 34 = Configuration error |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận của khối này cộng với khối kênh (enum) |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Current Object Owner ID (0=not owned) |
| **`Sts_Ramping`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=CV is ramping to target |
| **`Sts_Clamped`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=CV Set is clamped at CVMin or CVMax |
| **`Sts_SkipRoCLim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Rate of Change Limiting was skipped this scan (Maint, Ovrd, Intlk, Hand) |
| **`Sts_Active`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=CV is greater than Cfg_MaxInactiveCV, show graphic symbol as "active" |
| **`Sts_Available`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Analog Output available for control by automation (Prog) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Bypassable Interlocks are Bypassed |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Bypassing Active (Bypassed or Maintenance) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device Not Ready, see detail bits for reason |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Điều kiện Khóa liên động không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Lỗi truyền thông I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Thiết bị đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device has a Maintenance Bypass function active |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang bị Tạm hoãn (Shelved) hoặc Vô hiệu hóa (Disabled), hiển thị biểu tượng trên HMI |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Error in Config: see detail bits for reason |
| **`Err_Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: CV Limits swapped |
| **`Err_EU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Scaled CV EU Min = Max |
| **`Err_Raw`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Raw Output Scaling Min = Max |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thông số cảnh báo (Thời gian duy trì tối thiểu, thời gian Shelve, mức Severity) không hợp lệ |
| **`Sts_Hand`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Hand đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn OoS, Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_OoS`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Out of Service đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_Maint`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Maintenance đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ovrd, Ext, Prog, Oper) |
| **`Sts_Ovrd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Override đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ext, Prog, Oper) |
| **`Sts_Ext`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=External đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Prog, Oper) |
| **`Sts_Prog`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Program đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_ProgLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Program đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Oper`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_OperLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Selection equals the Normal (Prog or Oper) |
| **`Sts_ExtReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=External request inhibited, cannot Get to External from current state |
| **`Sts_ProgReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Program request inhibited, cannot Get to Program from current state |
| **`Sts_MAcqRcvd`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Maintenance Acquire command received this scan |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Sẵn sàng nhận lệnh OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Status: CV held or forced by interlock NOT OK (1-shot) |
| **`Alm_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Alarm: CV held or forced by interlock NOT OK |
| **`Ack_IntlkTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Interlock Trip Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IntlkTripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Interlock Trip Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IntlkTripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Interlock Trip Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IntlkTripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Interlock Trip Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Fault Status (0=OK, 1=Bad) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=I/O Fault Alarm |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=I/O Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`MRdy_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`P_AOutHART`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 48 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_AOutHART`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_CVNavTag`** | `STRING_NavTag` | `''` | [Biến cấu hình thông số] Tag name for destination of CV Navigation button. IMPORTANT: This function does not apply to FactoryTalk View ME software. |
| **`Cfg_Desc`** | `STRING_40` | `'Analog Output'` | [Biến cấu hình thông số] Description for display on HMI |
| **`Cfg_EU`** | `STRING_8` | `'%'` | [Biến cấu hình thông số] Engineering Units for CV display on HMI |
| **`Cfg_FVEU`** | `STRING_8` | `''` | [Biến cấu hình thông số] Engineering Units for HART FV display on HMI |
| **`Cfg_FVLabel`** | `STRING_16` | `''` | [Biến cấu hình thông số] Label for Fourth Variable for display on HMI |
| **`Cfg_Label`** | `STRING_20` | `'Analog Output'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI |
| **`Cfg_PVEU`** | `STRING_8` | `''` | [Biến cấu hình thông số] Engineering Units for HART PV display on HMI |
| **`Cfg_PVLabel`** | `STRING_16` | `''` | [Biến cấu hình thông số] Label for Primary Variable for display on HMI |
| **`Cfg_SVEU`** | `STRING_8` | `''` | [Biến cấu hình thông số] Engineering Units for HART SV display on HMI |
| **`Cfg_SVLabel`** | `STRING_16` | `''` | [Biến cấu hình thông số] Label for Secondary Variable for display on HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_AOutHART'` | [Biến cấu hình thông số] Tagname for display on HMI |
| **`Cfg_TVEU`** | `STRING_8` | `''` | [Biến cấu hình thông số] Engineering Units for HART TV display on HMI |
| **`Cfg_TVLabel`** | `STRING_16` | `''` | [Biến cấu hình thông số] Label for Third Variable for display on HMI |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc quản lý lựa chọn nguồn lệnh điều khiển |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `??` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `??` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI và phần mềm thông tin |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | Interlock Trip Alarm |
| **`IOFault`** | `P_Alarm` | `{...}` | I/O Fault Alarm |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bypass all Bypassable Interlocks |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Check (not bypass) all Interlocks |
| **`MCmd_UpdDevInfo`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để request update of device info |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_UpdDevInfo`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_UpdDevInfo (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset all Alarms requiring Reset |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset all Alarms and latched Shed conditions |
| **`ORdy_CV`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OSet_CV (enables data entry field) |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`OSet_CV`** | `REAL` | `0` | [Giá trị đặt từ màn hình HMI Operator] Operator Setting of Controlled Variable (output) (in EU) |
| **`Val_DiagDesc1`** | `STRING_32` | `‘Logic not yet scanned.’` | [Giá trị thực tế đầu ra (Logic/HMI)] Device Diagnostic Description #1 (from diag. code lookup) |
| **`Val_DiagDesc2`** | `STRING_32` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Device Diagnostic Description #2 (from diag. code lookup) |
| **`Val_DiagDesc3`** | `STRING_32` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Device Diagnostic Description #3 (from diag. code lookup) |
| **`Val_HART_CVEUMax`** | `REAL` | `100` | [Giá trị thực tế đầu ra (Logic/HMI)] Analog Val;ue Maximum (for scaling) received via HART |
| **`Val_HART_CVEUMin`** | `REAL` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Analog Val;ue Minimum (for scaling) received via HART |
| **`Val_HART_CVRawMax`** | `REAL` | `100` | [Giá trị thực tế đầu ra (Logic/HMI)] Signal Maximum (for scaling) received via HART |
| **`Val_HART_CVRawMin`** | `REAL` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Signal Minimum (for scaling) received via HART |
| **`Val_HART_Desc`** | `STRING_32` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Description Text received via HART |
| **`Val_HART_EU`** | `STRING_8` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Analog Value Engineering Units Text received via HART |
| **`Val_HART_FVEU`** | `STRING_8` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] FV Engineering Units Text received via HART |
| **`Val_HART_Label`** | `STRING_20` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Label Text received via HART |
| **`Val_HART_PVEU`** | `STRING_8` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] PV Engineering Units Text received via HART |
| **`Val_HART_SVEU`** | `STRING_8` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] SV Engineering Units Text received via HART |
| **`Val_HART_Tag`** | `STRING_20` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Tag Text received via HART |
| **`Val_HART_TVEU`** | `STRING_8` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] TV Engineering Units Text received via HART |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |

