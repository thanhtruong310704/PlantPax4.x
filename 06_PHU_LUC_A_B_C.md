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
