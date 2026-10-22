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

---
# PHẦN TRA CỨU CHI TIẾT TOÀN BỘ BIẾN (INPUT, OUTPUT, LOCAL TAGS), CÁCH SỬ DỤNG & CẤU HÌNH TỪNG KHỐI AOI

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_ValveHO`
**Thuộc chương:** Chương 4: Van điều khiển & Cơ cấu chấp hành (Valves)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Valves 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_ValveHO`:** **60** Biến đầu vào (`Input Parameters`) | **60** Biến đầu ra (`Output Parameters`) | **26** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_ValveHO`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_OpenLS`, `Inp_ClosedLS`, `Inp_IntlkOK`, `Inp_NBIntlkOK`, `Inp_IOFault`, `Inp_Sim`, `Inp_SimOpen`, `Inp_SimClose`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_HasTrip`, `Cfg_TripOpen`, `Cfg_HasMoreObj`, `Cfg_HasOpenLS`, `Cfg_HasClosedLS`, `Cfg_UseOpenLS`, `Cfg_UseClosedLS`, `Cfg_LSFail`, `Cfg_HasIntlkObj`, `Cfg_HasStatsObj`, `Cfg_OCmdResets`, `Cfg_ShedOnFailToTrip`, `Cfg_ShedOnIOFault`, `Cfg_HasTripFailAlm`, `Cfg_HasTransitStallAlm`, `Cfg_HasIntlkTripAlm`, `Cfg_HasIOFaultAlm`, `Cfg_TripFailResetReqd`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_Label`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_ValveHO` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PCmd_Trip`, `PCmd_Reset`, `PCmd_TripFailAck`, `PCmd_TransitStallAck`, `PCmd_IntlkTripAck`, `PCmd_IOFaultAck`, `PCmd_TripFailSuppress`, `PCmd_TripFailUnsuppress`, `PCmd_TransitStallSuppress`, `PCmd_TransitStallUnsuppress`, `PCmd_IntlkTripSuppress`, `PCmd_IntlkTripUnsuppress`, `PCmd_IOFaultSuppress`, `PCmd_IOFaultUnsuppress`, `PCmd_TransitStallUnshelve`, `PCmd_TripFailUnshelve`, `PCmd_IntlkTripUnshelve`, `PCmd_IOFaultUnshelve`
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Out_Trip`, `Val_Cmd`, `Val_Fdbk`, `Val_Sts`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Sts_Closed`, `Sts_Opened`, `Sts_Moving`, `Sts_Tripping`, `Sts_Bypass`, `Sts_BypActive`, `Sts_OoS`, `Sts_LSFail`, `Sts_NotRdy`, `Sts_MaintByp`, `Sts_AlmInh`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 60 Biến Đầu Vào (`Input Parameters`) của `P_ValveHO`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_OpenLS`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Valve Open Limit Switch 1=confirmed OPEN |
| **`Inp_ClosedLS`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Valve Closed Limit Switch 1=confirmed CLOSED |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Interlocks OK, interlock not tripping valve |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Non-Bypassable Interlocks OK, interlock not tripping valve |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Input Communication Status 0=OK, 1=fail |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Simulation input. When set to 1, the instruction keeps the trip output de-energized and simulates a working valve. When set to 0, the instruction monitors the valve normally. |
| **`Inp_SimOpen`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Sets simulated valve state to OPEN, 0=no change |
| **`Inp_SimClose`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Sets simulated valve state to CLOSED, 0=no change |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Reset all fault conditions and latched Alarms |
| **`Cfg_HasTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Trip Output is connected to valve, 0=Monitor only |
| **`Cfg_TripOpen`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Trip opens valve, 0=Trip closes valve |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_HasOpenLS`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Valve provides an Open Limit Switch signal |
| **`Cfg_HasClosedLS`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Valve provides a Closed Limit Switch signal |
| **`Cfg_UseOpenLS`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Valve Open LS should be used for failure checking |
| **`Cfg_UseClosedLS`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Valve Closed LS should be used for failure checking |
| **`Cfg_LSFail`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=both switches ON=fail, 0=both switches OFF=fail |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tells HMI a P_Intlk is connected to Inp_IntlkOK and Inp_NBIntlkOK. IMPORTANT: The name of the Interlock object in the controller must be this object's name with the suffix ‘_Intlk’. For example, if your P_ValveHO object has the name ’ValveHO123’, then its Interlock object must be named ‘ValveHO123_Intlk’. |
| **`Cfg_HasStatsObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tells HMI a P_ValveStats is monitoring this valve. IMPORTANT: The name of the Valve Stats object in the controller must be this object's name with the suffix ‘_ValveStats’. For example, if your P_ValveHO object has the name’ValveHO123’, then its Valve Stats object must be named ‘ValveHO123_ValveStats’. |
| **`Cfg_OCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=New Oper trip cmd. resets fault, 0=reset req'd to clear fault |
| **`Cfg_ShedOnFailToTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Continue Trip and Alarm on Fail to Trip; 0=Alarm only on Fail to Trip |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Trip Valve and Alarm on I/O Fault; 0=Alarm only on I/O Fault |
| **`Cfg_HasTripFailAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Fail to Trip Alarm exists and will be checked |
| **`Cfg_HasTransitStallAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Transit Stall Alarm exists and will be checked |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Interlock Trip Alarm exists and will be checked |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=I/O Fault Alarm exists and will be checked |
| **`Cfg_TripFailResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_TransitStallResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_TripFailAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_TransitStallAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_TripFailSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_TransitStallSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_SimFdbkT`** | `REAL` | `3` | Ẩn (Tag) | [Biến cấu hình thông số] Delay to echo back of Open/Closed status when in Simulation (sec) |
| **`Cfg_TripFailT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] After tripped, time to reach trip position before Alarm (sec) |
| **`Cfg_TransitStallT`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Time not confirmed open or closed before Alarm (sec) |
| **`PCmd_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Trip Valve |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_Reset = 1 để xóa tất cả các cảnh báo chốt đang chờ Reset (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_TripFailAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_TransitStallAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_TripFailSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Fail to Trip Alarm |
| **`PCmd_TripFailUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Fail to Trip Alarm |
| **`PCmd_TransitStallSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Transit Stall Alarm |
| **`PCmd_TransitStallUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Transit Stall Alarm |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Interlock Trip Alarm |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Interlock Trip Alarm |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) I/O Fault Alarm |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) I/O Fault Alarm |
| **`PCmd_TransitStallUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_TripFailUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |

## 3. Bảng chi tiết 60 Biến Đầu Ra (`Output Parameters`) của `P_ValveHO`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Out_Trip`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1=Trip Valve to safe/fail state |
| **`SrcQ_IO`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Source and Quality of primary Val/Sts (enumeration) |
| **`Val_Cmd`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Final valve status source and quality: GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`Val_Fdbk`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Feedback 0=Moving, 1=Closed, 2=Opened, 3=Limit Switch Failure |
| **`Val_Sts`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Confirmed Sts 0=?, 1=Closed, 2=Opened, 8=Moving, 13=Tripping, 33=Disabled |
| **`Val_Fault`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Fault Status 0=None, 16=Transit Stall, 18=Fail to Trip, 32=I/O Fault, 34=Config Error |
| **`Val_NotifyAll`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |
| **`Val_UnackAlmC`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Sts_Closed`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Valve is confirmed Closed |
| **`Sts_Opened`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Valve is confirmed Open |
| **`Sts_Moving`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Valve not requested to Trip and is not confirmed Open or Closed |
| **`Sts_Tripping`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Valve requested to Trip and has not reached trip position |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Bypassable Interlocks are Bypassed |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Bypassing Active (Bypassed or Maintenance) |
| **`Sts_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Valve is Out of Service |
| **`Sts_LSFail`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Limit Switch Failure, LSs show open and closed |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device Not Ready, see detail bits for reason |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Lỗi thiết bị ngắt tải (yêu cầu nhấn Reset) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Điều kiện Khóa liên động không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Lỗi truyền thông I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Thiết bị đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Nrdy_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Tripped (at device or by command) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=An Alarm is Disabled or Shelved, display icon |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Error in Config: see detail bits for reason |
| **`Err_Has`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: must have at least one Limit Switch |
| **`Err_Use`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: must use at least one Limit Switch |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Trip Check timer preset (use 0 to 2147483) |
| **`Err_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Simulation timer preset (use 0 to 2147483) |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thông số cảnh báo (Thời gian duy trì tối thiểu, thời gian Shelve, mức Severity) không hợp lệ |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc lỗi ngắt tải (Shed) đã hết điều kiện lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_TripFail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Valve Failed to Trip (did not reach trip position) |
| **`Alm_TripFail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Valve Failed to Trip (did not reach trip position) Alarm |
| **`Ack_TripFail`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Fail to Trip Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_TripFailDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Fail to Trip Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_TripFailShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Fail to Trip Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_TripFailSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Fail to Trip Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_TransitStall`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Valve Transit Stall (valve has not reached either end of travel) |
| **`Alm_TransitStall`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Valve Transit Stall (not at either end of travel) Alarm |
| **`Ack_TransitStall`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Transit Stall Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_TransitStallDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Transit Stall Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_TransitStallShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Transit Stall Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_TransitStallSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Transit Stall Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Valve Tripped by an Interlock NOT OK |
| **`Alm_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Alarm: Valve Tripped by an Interlock NOT OK |
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
| **`P_ValveHO`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 26 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_ValveHO`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_8` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'Hand Operated Valve'` | [Biến cấu hình thông số] Description for display on HMI. This string is shown in the title bar of the faceplate. |
| **`Cfg_Label`** | `STRING_20` | `'Valve Monitor'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI. This string appears on the graphic symbol. |
| **`Cfg_Tag`** | `STRING_20` | `'P_ValveHO'` | [Biến cấu hình thông số] Tagname for display on HMI. This string is shown in the title bar of the faceplate. |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_ValveHO'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | Interlock Trip Alarm |
| **`IOFault`** | `P_Alarm` | `{...}` | I/O Fault Alarm |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bypass all Bypassable Interlocks |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Check (not bypass) all Interlocks |
| **`MCmd_IS`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để place valve In Service |
| **`MCmd_OoS`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để place valve Out of Service |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_IS`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_IS (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_OoS`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_OoS (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Oper Cmd to xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Oper Cmd to xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi, plus Xác nhận (Acknowledge) alarms |
| **`OCmd_Trip`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Trip Valve |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Trip`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Trip (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`TransitStall`** | `P_Alarm` | `{...}` | Transit Stall Alarm |
| **`TripFail`** | `P_Alarm` | `{...}` | Fail to Trip (valve did not go to trip position) Alarm |
| **`Val_Notify`** | `SINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_ValveMO`
**Thuộc chương:** Chương 4: Van điều khiển & Cơ cấu chấp hành (Valves)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Valves 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_ValveMO`:** **100** Biến đầu vào (`Input Parameters`) | **88** Biến đầu ra (`Output Parameters`) | **28** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_ValveMO`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_OpenLS`, `Inp_ClosedLS`, `Inp_ActuatorFault`, `Inp_OpenPermOK`, `Inp_OpenNBPermOK`, `Inp_ClosePermOK`, `Inp_CloseNBPermOK`, `Inp_IOFault`, `Inp_Sim`, `Inp_Hand`, `Inp_Ovrd`, `Inp_OvrdCmd`, `Inp_ExtInh`, `Inp_Reset`, `Inp_IntlkAvail`, `Inp_IntlkOK`, `Inp_NBIntlkOK`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_HasStop`, `Cfg_HasMoreObj`, `Cfg_HasOpenLS`, `Cfg_HasClosedLS`, `Cfg_UseOpenLS`, `Cfg_UseClosedLS`, `Cfg_LSFail`, `Cfg_CoastToLS`, `Cfg_HasOpenPermObj`, `Cfg_HasClosePermObj`, `Cfg_HasStatsObj`, `Cfg_OCmdResets`, `Cfg_XCmdResets`, `Cfg_OvrdPerm`, `Cfg_ShedOnFullStall`, `Cfg_ShedOnTransitStall`, `Cfg_ShedOnActuatorFault`, `Cfg_ShedOnIOFault`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_Label`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_ValveMO` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PSet_Owner`, `PCmd_Open`, `PCmd_Close`, `PCmd_Stop`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_FullStallAck`, `PCmd_TransitStallAck`, `PCmd_ActuatorFaultAck`, `PCmd_IOFaultAck`, `PCmd_FullStallSuppress`, `PCmd_FullStallUnsuppress`, `PCmd_TransitStallSuppress`, `PCmd_TransitStallUnsuppress`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Out_Open`, `Out_Close`, `Out_Stop`, `Val_Cmd`, `Val_Fdbk`, `Val_Sts`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Val_Owner`, `Sts_Closed`, `Sts_CmdToOpen`, `Sts_Opening`, `Sts_Opened`, `Sts_CmdToClose`, `Sts_Closing`, `Sts_Stopped`, `Sts_Available`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 100 Biến Đầu Vào (`Input Parameters`) của `P_ValveMO`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_OpenLS`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Valve Open Limit Switch 1=confirmed OPEN |
| **`Inp_ClosedLS`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Valve Closed Limit Switch 1=confirmed CLOSED |
| **`Inp_ActuatorFault`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Valve Actuator Fault (overload, etc.), 0=OK, 1=Fault) |
| **`Inp_OpenPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Open Permissives OK, Valve can motor open |
| **`Inp_OpenNBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Non-Bypassable Open Permissives OK, valve can motor open |
| **`Inp_ClosePermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Close Permissives OK, Valve can motor closed |
| **`Inp_CloseNBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Non-Bypassable Close Permissives OK, valve can motor closed |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Input Communication Status 0=OK, 1=fail |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Simulation input. When set to 1, the instruction keeps the outputs de-energized and simulates a working valve. When set to 0, the instruction operates the valve normally. |
| **`Inp_Hand`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Acquire Hand (typ. hardwired local), 0=Release Hand |
| **`Inp_Ovrd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Acquire Override (higher priority program logic), 0=Release Override |
| **`Inp_OvrdCmd`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Override Command 0=None, 1=Close, 2=Open, 3=Stop |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Inhibit External Acquisition, 0=Allow External Acquisition |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Reset all fault conditions and latched Alarms |
| **`Cfg_HasStop`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Stop output can be used to break local seal-in and stop valve motion |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_HasOpenLS`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Valve provides an Open Limit Switch signal |
| **`Cfg_HasClosedLS`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Valve provides a Closed Limit Switch signal |
| **`Cfg_UseOpenLS`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Valve Open LS should be used for failure checking |
| **`Cfg_UseClosedLS`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Valve Closed LS should be used for failure checking |
| **`Cfg_LSFail`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=both switches ON=fail, 0=both switches OFF=fail |
| **`Cfg_CoastToLS`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Coasting into LS when stopped changes state (compat. with 3.5-04), 0=Stop is independent |
| **`Cfg_HasOpenPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tells HMI a P_Perm is connected to Inp_OpenPermOK. IMPORTANT: The name of the Permissive object in the controller must be this object's name with the suffix ‘_OpenPerm’. For example, if your P_ValveMO object has the name‘ValveMO123’, then its Permissive object must be named‘ValveMO123_OpenPerm’. |
| **`Cfg_HasClosePermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tells HMI a P_Perm is connected to Inp_ClosePermOK. IMPORTANT: The name of the Permissive object in the controller must be this object's name with the suffix ‘_ClosePerm’. For example, if your P_ValveMO object has the name‘ValveMO123’, then its Permissive object must be named ‘ValveMO123_ClosePerm’. |
| **`Cfg_HasStatsObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tells HMI a Valve Stats is monitoring this valve. IMPORTANT: The name of the ValveStats object in the controller must be this object's name with the suffix ‘_ValveStats’. For example, if your P_ValveMO object has the name‘ValveMO123’, then its Valve Stats object must be named‘ValveMO123_ValveStats’. |
| **`Cfg_OCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=New valve OCmd resets shed latches and cleared alarms, 0=OCmdReset req'd. |
| **`Cfg_XCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=New Valve XCmd resets shed latches and cleared alarms, 0=XCmdReset req'd. |
| **`Cfg_OvrdPerm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Override ignores Bypassable Perm; 0=always use Perm |
| **`Cfg_ShedOnFullStall`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Stop Valve and Alarm on Full Stall; 0=Alarm only on Full Stall |
| **`Cfg_ShedOnTransitStall`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Stop Valve and Alarm on Transit Stall; 0=Alarm only on Transit Stall |
| **`Cfg_ShedOnActuatorFault`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Stop Valve and Alarm on Actuator Fault; 0=Alarm only on Actuator Fault |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Stop Valve and Alarm on I/O Fault; 0=Alarm only on I/O Fault |
| **`Cfg_HasFullStallAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Full Stall Alarm exists and will be checked |
| **`Cfg_HasTransitStallAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Transit Stall Alarm exists and will be checked |
| **`Cfg_HasActuatorFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Actuator Fault Alarm exists and will be checked |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=I/O Fault Alarm exists and will be checked |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Tells HMI a P_Intlk is connected to Inp_IntlkOK |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Interlock Trip Alarm exists and will be checked |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Xác nhận (Acknowledge) required for Interlock Trip Alarm |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Reset required to clear Interlock Trip Alarm |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Interlock Trip Alarm Severity: 1..250=Low, 251..500=Medium, 501..750=High, 751..1000=Urgent |
| **`Cfg_StartHornT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Time to sound audible on commanded start (sec) (0=disabled) |
| **`Inp_IntlkAvail`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Interlock Availibility OK |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Interlocks (affects Availability) OK, valve can open/close |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Non-Bypassable Interlocks OK, valve can open/close |
| **`Cfg_FullStallResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_TransitStallResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_ActuatorFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_FullStallAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_TransitStallAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_ActuatorFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_FullStallSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_TransitStallSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_ActuatorFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_OutPulseT`** | `REAL` | `5` | Ẩn (Tag) | [Biến cấu hình thông số] When this parameter is set to zero, the output for the last command is maintained ON, even after the valve/device reaches position. This configuration is for devices that require a maintained output. When this parameter is greater than zero, the output is held on until the pulse time expires, a full stall or transit stall shed occurs, or the device reaches the commanded position, whichever occurs first. For devices that require a fixed pulse time, set this parameter to the required time. For devices that require the output to be kept energized until the device reaches the commanded position, set this parameter to a ‘large’ value, at least as large as the transit stall time. |
| **`Cfg_SimFdbkT`** | `REAL` | `20` | Ẩn (Tag) | [Biến cấu hình thông số] Delay to echo back of Open/Closed status when in Simulation (sec) |
| **`Cfg_FullStallT`** | `REAL` | `15` | Ẩn (Tag) | [Biến cấu hình thông số] After command, time with no motion before Fault (sec) |
| **`Cfg_TransitStallT`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] After command, time to reach position before Fault (sec) |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Mã định danh chủ sở hữu chương trình (Program Owner ID: khác 0 để đăng ký chiếm quyền, bằng 0 để nhả quyền). |
| **`PCmd_Open`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Open Valve |
| **`PCmd_Close`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Close Valve |
| **`PCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Stop Valve Motion |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Program (Oper to Prog) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Operator (Prog to Oper) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa cứng chế độ Program (không cho phép Operator giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Unlock Program (allow Oper to acquire) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Normal (Oper or Prog) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để clear shed latches and cleared alarms |
| **`PCmd_FullStallAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_TransitStallAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_ActuatorFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_FullStallSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Full Stall Alarm |
| **`PCmd_FullStallUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Full Stall Alarm |
| **`PCmd_TransitStallSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Transit Stall Alarm |
| **`PCmd_TransitStallUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Transit Stall Alarm |
| **`PCmd_ActuatorFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Actuator Fault Alarm |
| **`PCmd_ActuatorFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Actuator Fault Alarm |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) I/O Fault Alarm |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) I/O Fault Alarm |
| **`PCmd_FullStallUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_TransitStallUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_ActuatorFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Interlock Trip Alarm |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Interlock Trip Alarm |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Interlock Trip Alarm |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Interlock Trip Alarm |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Release (Maint to Oper/Prog/Ovrd) |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Unlock Operator (allow Prog to acquire) |
| **`XCmd_Open`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Open Valve |
| **`XCmd_Close`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Close Valve |
| **`XCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Stop Valve Motion |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để clear shed latches and cleared alarms |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Acquire (Oper/Prog to Ext) |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Release (Ext to Oper/Prog) |

## 3. Bảng chi tiết 88 Biến Đầu Ra (`Output Parameters`) của `P_ValveMO`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Out_Open`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1=Run actuator motor to OPEN Valve |
| **`Out_Close`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1=Run actuator motor to CLOSE Valve |
| **`Out_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1=Break seal-in circuit in actuator to STOP Valve motion |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final valve status source and quality: GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`Val_Cmd`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Command 0=None, 1=Close, 2=Open, 3=Stop |
| **`Val_Fdbk`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Feedback 0=Moving, 1=Closed, 2=Opened, 3=Limit Switch Failure |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Confirmed Sts 0=? 1=Closed 2=Open 3=Cmd To Close 4=Cmd to Open 5=Closing 6=Opening 7=Stopped |
| **`Val_Fault`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Fault Status 0=None, 16=Transit Stall, 17 = Full Stall, 19=Actuator Fault, 32=I/O Fault, 34=Config Error |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận của khối này cộng với khối kênh (enum) |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Current Object Owner ID (0=not owned) |
| **`Sts_Closed`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Valve requested to Close and is confirmed Closed |
| **`Sts_CmdToOpen`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Valve commanded to Open, has not yet moved off Open LS |
| **`Sts_Opening`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Valve requested to open and awaiting open feedback |
| **`Sts_Opened`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Valve requested to open and is confirmed Opened |
| **`Sts_CmdToClose`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Valve commanded to Close, has not yet moved off Closed LS |
| **`Sts_Closing`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Valve requested to Close and awaiting Closed feedback |
| **`Sts_Stopped`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Valve requested to Stop and is not at either end of travel |
| **`Sts_Available`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Valve available for control by automation (Prog) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Bypassable Interlocks and Permissives are Bypassed |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Bypassing Active (Bypassed or Maintenance) |
| **`Sts_LSFail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Limit Switch Overlap Failure |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device Not Ready, see detail bits for reason |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_EqpFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: External Equipment Fault (Fault or Shed Requires Reset) |
| **`Nrdy_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Lỗi thiết bị ngắt tải (yêu cầu nhấn Reset) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Lỗi truyền thông I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Thiết bị đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Nrdy_Perm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Điều kiện Cho phép khởi động không thỏa mãn (Permissive Not OK) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=An Alarm is Disabled or Shelved, display icon |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Error in Config: see detail bits for reason |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Invalid Check or Pulse Time (use 0 to 2147483) |
| **`Err_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Simulation timer preset (use 0 to 2147483) |
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
| **`Sts_FullStall`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Valve Full Stall (failed to move at all) (One-Shot) |
| **`Alm_FullStall`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Valve Full Stall (failed to move at all) Alarm |
| **`Ack_FullStall`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Full Stall Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_FullStallDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Full Stall Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_FullStallShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Full Stall Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_FullStallSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Full Stall Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_TransitStall`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Valve Transit Stall (did not reach target position) (One-Shot) |
| **`Alm_TransitStall`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Valve Transit Stall (did not reach target pos.) Alarm |
| **`Ack_TransitStall`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Transit Stall Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_TransitStallDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Transit Stall Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_TransitStallShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Transit Stall Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_TransitStallSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Transit Stall Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_ActuatorFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Actuator Fault detected (input) |
| **`Alm_ActuatorFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Alarm: Actuator Fault Detected |
| **`Ack_ActuatorFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Actuator Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_ActuatorFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Actuator Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_ActuatorFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Actuator Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_ActuatorFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Actuator Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] I/O Comm Fault Status (0=OK, 1=Bad) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=I/O Fault Alarm |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=I/O Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_Horn`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Valve Horn Active |
| **`Sts_IntlkTrip`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Valve stopped by an Interlock NOT OK (one-shot) |
| **`Sts_IntlkTripDisabled`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Interlock Trip Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IntlkTripShelved`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Interlock Trip Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IntlkTripSuppressed`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Interlock Trip Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`MRdy_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`Ack_IntlkTrip`** | `BOOL` | - | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Interlock Trip Alarm đã được Xác nhận (Acknowledged) |
| **`Alm_IntlkTrip`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Interlock Trip Alarm |
| **`Nrdy_Intlk`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Điều kiện Khóa liên động không thỏa mãn (Interlock Not OK) |
| **`Out_Horn`** | `BOOL` | - | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1=Sound audible prior to commanded motor start |
| **`P_ValveMO`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 28 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_ValveMO`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`ActuatorFault`** | `P_Alarm` | `{...}` | Actuator Fault Alarm |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'Motor Operated Valve'` | [Biến cấu hình thông số] Description for display on HMI. This string is shown in the title bar of the faceplate. |
| **`Cfg_Label`** | `STRING_20` | `'Valve Control'` | [Biến cấu hình thông số] Label for graphic symbol that is displayed on HMI. This string appears on the graphic symbol. |
| **`Cfg_Tag`** | `STRING_20` | `'P_ValveMO'` | [Biến cấu hình thông số] Tagname for display on HMI. This string is shown in the title bar of the faceplate. |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc quản lý lựa chọn nguồn lệnh điều khiển |
| **`FullStall`** | `P_Alarm` | `{...}` | Valve Full Stall (did not move) Alarm |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_ValveMO'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | Interlock Trip Alarm |
| **`IOFault`** | `P_Alarm` | `{...}` | I/O Fault Alarm |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bypass all Bypassable Interlocks and Permissives |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Check (not bypass) all Interlocks and Permissives |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`OCmd_Close`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Close Valve |
| **`OCmd_Open`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Open Valve |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Oper Cmd to xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Oper Cmd to xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi, plus Xác nhận (Acknowledge) alarms |
| **`OCmd_Stop`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Stop Valve Motion |
| **`ORdy_Close`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Close (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Open`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Open (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Stop`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Stop (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`TransitStall`** | `P_Alarm` | `{...}` | Valve Transit Stall (moved but did not reach target) Alarm |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_ValveSO`
**Thuộc chương:** Chương 4: Van điều khiển & Cơ cấu chấp hành (Valves)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Valves 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_ValveSO`:** **83** Biến đầu vào (`Input Parameters`) | **76** Biến đầu ra (`Output Parameters`) | **25** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_ValveSO`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_OpenLS`, `Inp_ClosedLS`, `Inp_PermOK`, `Inp_NBPermOK`, `Inp_IntlkAvail`, `Inp_IntlkOK`, `Inp_NBIntlkOK`, `Inp_IOFault`, `Inp_Sim`, `Inp_Hand`, `Inp_Ovrd`, `Inp_OvrdCmd`, `Inp_ExtInh`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_StartHornT`, `Cfg_FailOpen`, `Cfg_HasMoreObj`, `Cfg_HasOpenLS`, `Cfg_HasClosedLS`, `Cfg_UseOpenLS`, `Cfg_UseClosedLS`, `Cfg_LSFail`, `Cfg_HasPermObj`, `Cfg_HasIntlkObj`, `Cfg_HasStatsObj`, `Cfg_OCmdResets`, `Cfg_XCmdResets`, `Cfg_OvrdPermIntlk`, `Cfg_ShedOnFullStall`, `Cfg_ShedOnTransitStall`, `Cfg_ShedOnIOFault`, `Cfg_HasFullStallAlm`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_Label`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_ValveSO` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PSet_Owner`, `PCmd_Open`, `PCmd_Close`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_FullStallAck`, `PCmd_TransitStallAck`, `PCmd_IntlkTripAck`, `PCmd_IOFaultAck`, `PCmd_FullStallSuppress`, `PCmd_FullStallUnsuppress`, `PCmd_TransitStallSuppress`, `PCmd_TransitStallUnsuppress`, `PCmd_IntlkTripSuppress`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Out`, `Val_Cmd`, `Val_Fdbk`, `Val_Sts`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Val_Owner`, `Sts_Closed`, `Sts_Opening`, `Sts_Opened`, `Sts_Closing`, `Sts_Available`, `Sts_Bypass`, `Sts_BypActive`, `Sts_LSFail`, `Sts_NotRdy`, `Sts_MaintByp`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 83 Biến Đầu Vào (`Input Parameters`) của `P_ValveSO`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_OpenLS`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Valve Open Limit Switch 1=confirmed OPEN |
| **`Inp_ClosedLS`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Valve Closed Limit Switch 1=confirmed CLOSED |
| **`Inp_PermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Permissives (those that can be bypassed and those that cannot be bypassed) OK, valve can energize. |
| **`Inp_NBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Permissives (those that cannot be bypassed) OK, valve can energize if permissives that can be bypassed are bypassed. |
| **`Inp_IntlkAvail`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Interlock Availibility OK |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Interlocks (those that can be bypassed and those that cannot be bypassed) OK, valve can energize. |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Interlocks (those that cannot be bypassed) OK, valve can energize if interlocks that can be bypassed are bypassed. |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Input communication status: 0 = OK 1 = Fail |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Simulation input. When set to 1, the instruction keeps the output de-energized and simulates a working solenoid valve. When set to 0, the instruction operates the valve normally. |
| **`Inp_Hand`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Acquire Hand (typ. hardwired local), 0=Release Hand |
| **`Inp_Ovrd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Acquire Override (higher priority program logic), 0=Release Override |
| **`Inp_OvrdCmd`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Override Mode Command: 0=None, 1=Close, 2=Open |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Inhibit External Acquisition, 0=Allow External Acquisition |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Reset all fault conditions and latched Alarms |
| **`Cfg_StartHornT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Time to sound audible on commanded start (sec) (0=disabled) |
| **`Cfg_FailOpen`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Fail Open (energize to close), 0=Fail Closed (energize to open) |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_HasOpenLS`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Valve provides an Open Limit Switch signal |
| **`Cfg_HasClosedLS`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Valve provides a Closed Limit Switch signal |
| **`Cfg_UseOpenLS`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Valve Open LS should be used for failure checking |
| **`Cfg_UseClosedLS`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Valve Closed LS should be used for failure checking |
| **`Cfg_LSFail`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=both switches ON=fail, 0=both switches OFF=fail |
| **`Cfg_HasPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tells the HMI a library object is connected to Inp_Perm. IMPORTANT: The name of the Permissive object in the controller must be this object's name with the suffix‘_Perm’. For example, if your P_ValveSO object has the name ’ValveSO123’, then its Permissive object must be named ‘ValveSO123_Perm’. |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tells the HMI a library object is connected to Inp_Intlk IMPORTANT: The name of the Interlock object in the controller must be this object's name with the suffix ‘_Intlk’. For example, if your P_ValveSO object has the name’ValveSO123’, then its Interlock object must be named ‘ValveSO123_Intlk’. |
| **`Cfg_HasStatsObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tells the HMI a library object is connected to Inp_Intlk IMPORTANT: The name of the Interlock object in the controller must be this object's name with the suffix ‘_Intlk’. For example, if your P_ValveSO object has the name’ValveSO123’, then its Interlock object must be named ‘ValveSO123_Intlk’. |
| **`Cfg_OCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=New valve OCmd resets shed latches and cleared alarms, 0=OCmdReset req'd. |
| **`Cfg_XCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=New valve XCmd resets shed latches and cleared alarms, 0=OCmdReset req'd. |
| **`Cfg_OvrdPermIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Override ignores Bypassable Perm/ Intlk; 0=always use Perm/Intlk |
| **`Cfg_ShedOnFullStall`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Stop Valve and Alarm on Full Stall; 0=Alarm only on Full Stall |
| **`Cfg_ShedOnTransitStall`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Stop Valve and Alarm on Transit Stall; 0=Alarm only on Transit Stall |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Stop Valve and Alarm on I/O Fault; 0=Alarm only on I/O Fault |
| **`Cfg_HasFullStallAlm`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasTransitStallAlm`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_FullStallResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_TransitStallResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_FullStallAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_TransitStallAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_FullStallSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_TransitStallSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IOFaultSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_SimFdbkT`** | `REAL` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Delay (in seconds) to echo back open/closed status when in simulation or when position feedbacks are not being used. |
| **`Cfg_FullStallT`** | `REAL` | `5` | Ẩn (Tag) | [Biến cấu hình thông số] After command, time with no motion before Fault (sec) |
| **`Cfg_TransitStallT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] After command, time to reach position before fault (sec) |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Mã định danh chủ sở hữu chương trình (Program Owner ID: khác 0 để đăng ký chiếm quyền, bằng 0 để nhả quyền). |
| **`PCmd_Open`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Open Valve |
| **`PCmd_Close`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Close Valve |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Program (Oper to Prog) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Operator (Prog to Oper) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa cứng chế độ Program (không cho phép Operator giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Unlock Program (allow Oper to acquire) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Normal (Oper or Prog) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Reset Shed Latches and Cleared Alarms |
| **`PCmd_FullStallAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_TransitStallAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_FullStallSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Full Stall Alarm |
| **`PCmd_FullStallUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Full Stall Alarm |
| **`PCmd_TransitStallSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Transit Stall Alarm |
| **`PCmd_TransitStallUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Transit Stall Alarm |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Interlock Trip Alarm |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Interlock Trip Alarm |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) I/O Fault Alarm |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) I/O Fault Alarm |
| **`PCmd_FullStallUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_TransitStallUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Release (Maint to Oper/Prog/Ovrd) |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Unlock Operator (allow Prog to acquire) |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Acquire (Oper/Prog to Ext) |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Release (Ext to Oper/Prog) |
| **`XCmd_Open`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Open Valve |
| **`XCmd_Close`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Close Valve |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để clear shed latches and cleared alarms |

## 3. Bảng chi tiết 76 Biến Đầu Ra (`Output Parameters`) của `P_ValveSO`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Out`** | `BOOL` | `0` | Hiện (Pin) | 1=Energize Valve Solenoid |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final Valve status source and quality: GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`Val_Cmd`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Command 0=None, 1=Close, 2=Open |
| **`Val_Fdbk`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Feedback 0=Moving, 1=Closed, 2=Opened, 3=Limit Switch Failure |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Confirmed Sts 0=? 1=Closed 2=Open 5=Closing, 6=Opening |
| **`Val_Fault`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Fault Status 0=None, 16=Transit Stall, 17 = Full Stall, 32=I/O Fault, 34=Config Error |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận của khối này cộng với khối kênh (enum) |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Current Object Owner ID (0=not owned) |
| **`Sts_Closed`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Valve requested to Close and is confirmed Closed |
| **`Sts_Opening`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Valve requested to open and awaiting open feedback |
| **`Sts_Opened`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Valve requested to open and is confirmed Opened |
| **`Sts_Closing`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Valve requested to Close and awaiting Closed feedback |
| **`Sts_Available`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Valve available for control by automation (Prog) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Bypassable Interlocks and Permissives are Bypassed |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Bypassing Active (Bypassed or Maintenance) |
| **`Sts_LSFail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Limit Switch Overlap Failure |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device Not Ready, see detail bits for reason |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Lỗi thiết bị ngắt tải (yêu cầu nhấn Reset) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Điều kiện Khóa liên động không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Lỗi truyền thông I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Thiết bị đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Nrdy_Perm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Điều kiện Cho phép khởi động không thỏa mãn (Permissive Not OK) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=An Alarm is Disabled or Shelved, display icon |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Error in Config: see detail bits for reason |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Invalid Check Time (use 0 to 2147483) |
| **`Err_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Invalid Simulation Time (use 0 to 2147483) |
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
| **`Sts_FullStall`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Valve Full Stall (failed to move at all) |
| **`Alm_FullStall`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Valve Full Stall (failed to move at all) Alarm |
| **`Ack_FullStall`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Full Stall Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_FullStallDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Full Stall Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_FullStallShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Full Stall Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_FullStallSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Full Stall Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_TransitStall`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Valve Transit Stall (did not reach target position) |
| **`Alm_TransitStall`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Valve Transit Stall (did not reach target pos.) Alarm |
| **`Ack_TransitStall`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Transit Stall Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_TransitStallDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Transit Stall Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_TransitStallShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Transit Stall Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_TransitStallSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Transit Stall Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IntlkTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Valve de-energized by an Interlock NOT OK |
| **`Alm_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Alarm: Valve de-energized by an Interlock NOT OK |
| **`Ack_IntlkTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Interlock Trip Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IntlkTripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Interlock Trip Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IntlkTripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Interlock Trip Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IntlkTripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Interlock Trip Alarm has been Suppressed Program |
| **`Sts_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] I/O Comm Fault Status (0=OK, 1=Bad) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=I/O Fault Alarm |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=I/O Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`MRdy_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`Out_Horn`** | `BOOL` | - | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1=Sound audible prior to commanded motor start |
| **`Sts_Horn`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Valve Horn Active |
| **`P_ValveSO`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 25 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_ValveSO`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'Solenoid Operated Valve'` | [Biến cấu hình thông số] Description for display on HMI. This string is shown in the title bar of the faceplate. |
| **`Cfg_Label`** | `STRING_20` | `'Valve Control'` | [Biến cấu hình thông số] Label for graphic symbol that is displayed on HMI. This string appears on the graphic symbol. |
| **`Cfg_Tag`** | `STRING_20` | `'P_ValveSO'` | [Biến cấu hình thông số] Tagname for display on HMI. This string is shown in the title bar of the faceplate. |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Valve Mode Selection |
| **`FullStall`** | `P_Alarm` | `{...}` | Valve Full Stall (did not move) Alarm |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_ValveSO'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | Interlock Trip Alarm |
| **`IOFault`** | `P_Alarm` | `{...}` | I/O Fault Alarm |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bypass all Bypassable Interlocks and Permissives |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Check (not bypass) all Interlocks and Permissives |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`OCmd_Close`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Close Valve |
| **`OCmd_Open`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Open Valve |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Oper Cmd to clear shed latches and cleared alarms |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Oper Cmd to xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi, plus Xác nhận (Acknowledge) alarms |
| **`ORdy_Close`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Close (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Open`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Open (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`TransitStall`** | `P_Alarm` | `{...}` | Valve Transit Stall (moved but did not reach target) Alarm |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_D4SD`
**Thuộc chương:** Chương 4: Van điều khiển & Cơ cấu chấp hành (Valves)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Valves 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_D4SD`:** **117** Biến đầu vào (`Input Parameters`) | **80** Biến đầu ra (`Output Parameters`) | **33** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_D4SD`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_FdbkA`, `Inp_FdbkB`, `Inp_FdbkC`, `Inp_FdbkD`, `Inp_DeviceFault`, `Inp_PermOK0`, `Inp_NBPermOK0`, `Inp_PermOK1`, `Inp_NBPermOK1`, `Inp_PermOK2`, `Inp_NBPermOK2`, `Inp_PermOK3`, `Inp_NBPermOK3`, `Inp_IntlkOK`, `Inp_NBIntlkOK`, `Inp_IOFault`, `Inp_Sim`, `Inp_Hand`...
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_St0onShed`, `Cfg_NumStates`, `Cfg_OutSt0Write`, `Cfg_OutSt0State`, `Cfg_OutSt1Write`, `Cfg_OutSt1State`, `Cfg_OutSt2Write`, `Cfg_OutSt2State`, `Cfg_OutSt3Write`, `Cfg_OutSt3State`, `Cfg_FdbkSt0Check`, `Cfg_FdbkSt0State`, `Cfg_FdbkSt1Check`, `Cfg_FdbkSt1State`, `Cfg_FdbkSt2Check`, `Cfg_FdbkSt2State`, `Cfg_FdbkSt3Check`, `Cfg_FdbkSt3State`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_Label`, `Cfg_St0Text`, `Cfg_St1Text`, `Cfg_St2Text`, `Cfg_St3Text`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_D4SD` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PSet_Owner`, `PCmd_St0`, `PCmd_St1`, `PCmd_St2`, `PCmd_St3`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_FailAck`, `PCmd_DeviceFaultAck`, `PCmd_IntlkTripAck`, `PCmd_IOFaultAck`, `PCmd_FailSuppress`, `PCmd_FailUnsuppress`, `PCmd_DeviceFaultSuppress`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Out_A`, `Out_B`, `Out_C`, `Out_D`, `Out_Horn`, `Val_Cmd`, `Val_Sts`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Val_Owner`, `Sts_St0`, `Sts_St1`, `Sts_St2`, `Sts_St3`, `Sts_Moving`, `Sts_Horn`, `Sts_Available`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 117 Biến Đầu Vào (`Input Parameters`) của `P_D4SD`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_FdbkA`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Feedback input signals from device. Use the feedback configuration parameters (for example, Cfg_FdbkSt0Check and Cfg_FdbkSt0State) to determine how these inputs are used to determine actual state. |
| **`Inp_FdbkB`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Feedback input signals from device. Use the feedback configuration parameters (for example, Cfg_FdbkSt0Check and Cfg_FdbkSt0State) to determine how these inputs are used to determine actual state. |
| **`Inp_FdbkC`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Feedback input signals from device. Use the feedback configuration parameters (for example, Cfg_FdbkSt0Check and Cfg_FdbkSt0State) to determine how these inputs are used to determine actual state. |
| **`Inp_FdbkD`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Feedback input signals from device. Use the feedback configuration parameters (for example, Cfg_FdbkSt0Check and Cfg_FdbkSt0State) to determine how these inputs are used to determine actual state. |
| **`Inp_DeviceFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Input Signal: Device Fault from device |
| **`Inp_PermOK0`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Permissives OK, device can be commanded to State 0 |
| **`Inp_NBPermOK0`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Non-Bypassable Permissives OK, device can be commanded to State 0 |
| **`Inp_PermOK1`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Permissives OK, device can be commanded to State 1 |
| **`Inp_NBPermOK1`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Non-Bypassable Permissives OK, device can be commanded to State 1 |
| **`Inp_PermOK2`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Permissives OK, device can be commanded to State 2 |
| **`Inp_NBPermOK2`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Non-Bypassable Permissives OK, device can be commanded to State 2 |
| **`Inp_PermOK3`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Permissives OK, device can be commanded to State 3 |
| **`Inp_NBPermOK3`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Non-Bypassable Permissives OK, device can be commanded to State 3 |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Interlocks OK, 0=Not OK, go to State 0 |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Non-Bypassable Interlocks OK, 0=Not OK, go to State 0 |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Input Communication Status 0=OK, 1=fail |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Simulation input. When set to 1, the instruction simulates a working device while keeping outputs de-energized. When set to 0, the instruction controls the device normally. |
| **`Inp_Hand`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Acquire Hand (typ. hardwired local), 0=Release Hand |
| **`Inp_Ovrd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Acquire Override (higher priority program logic), 0=Release Override |
| **`Inp_OvrdCmd`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Override Device Command: 0=None, 1=St.0, 2=St.1, 3=St.2, 4=St.3 |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Inhibit External Acquisition, 0=Allow External Acquisition |
| **`Inp_HornInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Cấm còi báo động âm thanh; 0 = Cho phép phát còi báo trước khi khởi động |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Reset all fault conditions and latched Alarms |
| **`Cfg_St0onShed`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Go to State 0 on Shed, 0=Hold position on Shed |
| **`Cfg_NumStates`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Number of device states (2..4) |
| **`Cfg_OutSt0Write`** | `SINT` | `2#0000_1111` | Ẩn (Tag) | [Biến cấu hình thông số] These configuration parameters determine how the outputs are used to command state. The parameter Cfg_OutSt[x]Write configures which outputs get written for each state. The parameter Cfg_OutSt[x]State configures what gets written for each state. For example, if all outputs must be set to 0 except Out_A which must be set to 1 to command the device to state 1, then Cfg_OutSt1Write must be 2#0000_1111 and Cfg_OutSt1State must be 2#0000_0001. By default, all four outputs are written in every state, with a single output set to 1 in each state. |
| **`Cfg_OutSt0State`** | `SINT` | `2#0000_0001` | Ẩn (Tag) | [Biến cấu hình thông số] These configuration parameters determine how the outputs are used to command state. The parameter Cfg_OutSt[x]Write configures which outputs get written for each state. The parameter Cfg_OutSt[x]State configures what gets written for each state. For example, if all outputs must be set to 0 except Out_A which must be set to 1 to command the device to state 1, then Cfg_OutSt1Write must be 2#0000_1111 and Cfg_OutSt1State must be 2#0000_0001. By default, all four outputs are written in every state, with a single output set to 1 in each state. |
| **`Cfg_OutSt1Write`** | `SINT` | `2#0000_1111` | Ẩn (Tag) | [Biến cấu hình thông số] These configuration parameters determine how the outputs are used to command state. The parameter Cfg_OutSt[x]Write configures which outputs get written for each state. The parameter Cfg_OutSt[x]State configures what gets written for each state. For example, if all outputs must be set to 0 except Out_A which must be set to 1 to command the device to state 1, then Cfg_OutSt1Write must be 2#0000_1111 and Cfg_OutSt1State must be 2#0000_0001. By default, all four outputs are written in every state, with a single output set to 1 in each state. |
| **`Cfg_OutSt1State`** | `SINT` | `2#0000_0010` | Ẩn (Tag) | [Biến cấu hình thông số] These configuration parameters determine how the outputs are used to command state. The parameter Cfg_OutSt[x]Write configures which outputs get written for each state. The parameter Cfg_OutSt[x]State configures what gets written for each state. For example, if all outputs must be set to 0 except Out_A which must be set to 1 to command the device to state 1, then Cfg_OutSt1Write must be 2#0000_1111 and Cfg_OutSt1State must be 2#0000_0001. By default, all four outputs are written in every state, with a single output set to 1 in each state. |
| **`Cfg_OutSt2Write`** | `SINT` | `2#0000_1111` | Ẩn (Tag) | [Biến cấu hình thông số] These configuration parameters determine how the outputs are used to command state. The parameter Cfg_OutSt[x]Write configures which outputs get written for each state. The parameter Cfg_OutSt[x]State configures what gets written for each state. For example, if all outputs must be set to 0 except Out_A which must be set to 1 to command the device to state 1, then Cfg_OutSt1Write must be 2#0000_1111 and Cfg_OutSt1State must be 2#0000_0001. By default, all four outputs are written in every state, with a single output set to 1 in each state. |
| **`Cfg_OutSt2State`** | `SINT` | `2#0000_0100` | Ẩn (Tag) | [Biến cấu hình thông số] These configuration parameters determine how the outputs are used to command state. The parameter Cfg_OutSt[x]Write configures which outputs get written for each state. The parameter Cfg_OutSt[x]State configures what gets written for each state. For example, if all outputs must be set to 0 except Out_A which must be set to 1 to command the device to state 1, then Cfg_OutSt1Write must be 2#0000_1111 and Cfg_OutSt1State must be 2#0000_0001. By default, all four outputs are written in every state, with a single output set to 1 in each state. |
| **`Cfg_OutSt3Write`** | `SINT` | `2#0000_1111` | Ẩn (Tag) | [Biến cấu hình thông số] These configuration parameters determine how the outputs are used to command state. The parameter Cfg_OutSt[x]Write configures which outputs get written for each state. The parameter Cfg_OutSt[x]State configures what gets written for each state. For example, if all outputs must be set to 0 except Out_A which must be set to 1 to command the device to state 1, then Cfg_OutSt1Write must be 2#0000_1111 and Cfg_OutSt1State must be 2#0000_0001. By default, all four outputs are written in every state, with a single output set to 1 in each state. |
| **`Cfg_OutSt3State`** | `SINT` | `2#0000_1000` | Ẩn (Tag) | [Biến cấu hình thông số] These configuration parameters determine how the outputs are used to command state. The parameter Cfg_OutSt[x]Write configures which outputs get written for each state. The parameter Cfg_OutSt[x]State configures what gets written for each state. For example, if all outputs must be set to 0 except Out_A which must be set to 1 to command the device to state 1, then Cfg_OutSt1Write must be 2#0000_1111 and Cfg_OutSt1State must be 2#0000_0001. By default, all four outputs are written in every state, with a single output set to 1 in each state. |
| **`Cfg_FdbkSt0Check`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] These configuration parameters determine how the inputs are used to determine state. The parameter Cfg_FdbkSt[x]Check configures which inputs get checked for each state. The parameter Cfg_FdbkSt[x]State configures the values of the inputs which determine each state. For example, if the device returns a 0 to Inp_FdbkA and Inp_FdbkB and a 1 to Inp_FdbkC when the device is in state 3, then Cfg_FdbkSt3Check must be 2#0000_0111 and Cfg_FdbkSt1Write must be 2#0000_0100. By default, all inputs are ‘don’t’ care’ in every state (no feedback checking). |
| **`Cfg_FdbkSt0State`** | `SINT` | `2#0000_0001` | Ẩn (Tag) | [Biến cấu hình thông số] These configuration parameters determine how the inputs are used to determine state. The parameter Cfg_FdbkSt[x]Check configures which inputs get checked for each state. The parameter Cfg_FdbkSt[x]State configures the values of the inputs which determine each state. For example, if the device returns a 0 to Inp_FdbkA and Inp_FdbkB and a 1 to Inp_FdbkC when the device is in state 3, then Cfg_FdbkSt3Check must be 2#0000_0111 and Cfg_FdbkSt1Write must be 2#0000_0100. By default, all inputs are ‘don’t’ care’ in every state (no feedback checking). |
| **`Cfg_FdbkSt1Check`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] These configuration parameters determine how the inputs are used to determine state. The parameter Cfg_FdbkSt[x]Check configures which inputs get checked for each state. The parameter Cfg_FdbkSt[x]State configures the values of the inputs which determine each state. For example, if the device returns a 0 to Inp_FdbkA and Inp_FdbkB and a 1 to Inp_FdbkC when the device is in state 3, then Cfg_FdbkSt3Check must be 2#0000_0111 and Cfg_FdbkSt1Write must be 2#0000_0100. By default, all inputs are ‘don’t’ care’ in every state (no feedback checking). |
| **`Cfg_FdbkSt1State`** | `SINT` | `2#0000_0010` | Ẩn (Tag) | [Biến cấu hình thông số] These configuration parameters determine how the inputs are used to determine state. The parameter Cfg_FdbkSt[x]Check configures which inputs get checked for each state. The parameter Cfg_FdbkSt[x]State configures the values of the inputs which determine each state. For example, if the device returns a 0 to Inp_FdbkA and Inp_FdbkB and a 1 to Inp_FdbkC when the device is in state 3, then Cfg_FdbkSt3Check must be 2#0000_0111 and Cfg_FdbkSt1Write must be 2#0000_0100. By default, all inputs are ‘don’t’ care’ in every state (no feedback checking). |
| **`Cfg_FdbkSt2Check`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] These configuration parameters determine how the inputs are used to determine state. The parameter Cfg_FdbkSt[x]Check configures which inputs get checked for each state. The parameter Cfg_FdbkSt[x]State configures the values of the inputs which determine each state. For example, if the device returns a 0 to Inp_FdbkA and Inp_FdbkB and a 1 to Inp_FdbkC when the device is in state 3, then Cfg_FdbkSt3Check must be 2#0000_0111 and Cfg_FdbkSt1Write must be 2#0000_0100. By default, all inputs are ‘don’t’ care’ in every state (no feedback checking). |
| **`Cfg_FdbkSt2State`** | `SINT` | `2#0000_0100` | Ẩn (Tag) | [Biến cấu hình thông số] These configuration parameters determine how the inputs are used to determine state. The parameter Cfg_FdbkSt[x]Check configures which inputs get checked for each state. The parameter Cfg_FdbkSt[x]State configures the values of the inputs which determine each state. For example, if the device returns a 0 to Inp_FdbkA and Inp_FdbkB and a 1 to Inp_FdbkC when the device is in state 3, then Cfg_FdbkSt3Check must be 2#0000_0111 and Cfg_FdbkSt1Write must be 2#0000_0100. By default, all inputs are ‘don’t’ care’ in every state (no feedback checking). |
| **`Cfg_FdbkSt3Check`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] These configuration parameters determine how the inputs are used to determine state. The parameter Cfg_FdbkSt[x]Check configures which inputs get checked for each state. The parameter Cfg_FdbkSt[x]State configures the values of the inputs which determine each state. For example, if the device returns a 0 to Inp_FdbkA and Inp_FdbkB and a 1 to Inp_FdbkC when the device is in state 3, then Cfg_FdbkSt3Check must be 2#0000_0111 and Cfg_FdbkSt1Write must be 2#0000_0100. By default, all inputs are ‘don’t’ care’ in every state (no feedback checking). |
| **`Cfg_FdbkSt3State`** | `SINT` | `2#0000_1000` | Ẩn (Tag) | [Biến cấu hình thông số] These configuration parameters determine how the inputs are used to determine state. The parameter Cfg_FdbkSt[x]Check configures which inputs get checked for each state. The parameter Cfg_FdbkSt[x]State configures the values of the inputs which determine each state. For example, if the device returns a 0 to Inp_FdbkA and Inp_FdbkB and a 1 to Inp_FdbkC when the device is in state 3, then Cfg_FdbkSt3Check must be 2#0000_0111 and Cfg_FdbkSt1Write must be 2#0000_0100. By default, all inputs are ‘don’t’ care’ in every state (no feedback checking). |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_HasPerm0Obj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng permissive liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasPerm1Obj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng permissive liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasPerm2Obj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng permissive liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasPerm3Obj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng permissive liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng interlock liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_OperSt0Prio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=OCmd_St0 any time, 0=OCmd_St0 only when Oper selected |
| **`Cfg_ExtSt0Prio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=XCmd_St0 any time, 0=XCmd_St0 only when Ext selected |
| **`Cfg_OCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=New device OCmd resets shed latches and cleared alarms, 0=OCmdReset req'd. |
| **`Cfg_XCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=New device XCmd resets shed latches and cleared alarms, 0=XCmdReset req'd. |
| **`Cfg_OvrdPermIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Override ignores Bypassable Perm/ Intlk; 0=always use Perm/Intlk |
| **`Cfg_ShedOnFail`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Go to State 0 and Alarm on Fail to reach position; 0=Alarm only on Fail |
| **`Cfg_ShedOnDeviceFault`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Go to State 0 and Alarm on Device Fault; 0=Alarm only on Device Fault |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Go to State 0 and Alarm on I/O Fault; 0=Alarm only on I/O Fault |
| **`Cfg_HasFailAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Fail to reach state Alarm exists and will be checked |
| **`Cfg_HasDeviceFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Device Fault Alarm exists and will be checked |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Interlock Trip Alarm exists and will be checked |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=I/O Fault Alarm exists and will be checked |
| **`Cfg_HornOnChange`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 0 = Horn on energize only, 1 = Horn on any state change |
| **`Cfg_FailResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_DeviceFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_FailAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_DeviceFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_FailSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_DeviceFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_OutAPulseT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Time to pulse Output A (sec) (0=output held continuously) |
| **`Cfg_OutBPulseT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Time to pulse Output B (sec) (0=output held continuously) |
| **`Cfg_OutCPulseT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Time to pulse Output C (sec) (0=output held continuously) |
| **`Cfg_OutDPulseT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Time to pulse Output D (sec) (0=output held continuously) |
| **`Cfg_SimFdbkT`** | `REAL` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Delay to echo back reaching state when in Simulation (sec) |
| **`Cfg_StartHornT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Time to sound audible on commanded start (sec) (0=disabled) |
| **`Cfg_HornOnChange`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 0 = Horn on energize only, 1 = Horn on any state change |
| **`Cfg_FailT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Time after new state requested to reach that state before Fault (sec) |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Mã định danh chủ sở hữu chương trình (Program Owner ID: khác 0 để đăng ký chiếm quyền, bằng 0 để nhả quyền). |
| **`PCmd_St0`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để set device to State 0 |
| **`PCmd_St1`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để set device to State 1 |
| **`PCmd_St2`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để set device to State 2 |
| **`PCmd_St3`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để set device to State 3 |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Program (Oper to Prog) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Operator (Prog to Oper) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa cứng chế độ Program (không cho phép Operator giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Unlock Program (allow Oper to acquire) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Normal (Oper or Prog) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để ResetShed Latches and Cleared Alarms |
| **`PCmd_FailAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_DeviceFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_FailSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Fail to Reach Target Position Alarm |
| **`PCmd_FailUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Fail to Reach Target Position Alarm |
| **`PCmd_DeviceFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Device Fault Alarm |
| **`PCmd_DeviceFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Device Fault Alarm |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Interlock Trip Alarm |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Interlock Trip Alarm |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) I/O Fault Alarm |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) I/O Fault Alarm |
| **`PCmd_FailUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_DeviceFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Release (Maint to Oper/Prog/Ovrd) |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Unlock Operator (allow Prog to acquire) |
| **`XCmd_St0`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để set device to State 0 |
| **`XCmd_St1`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để set device to State 1 |
| **`XCmd_St2`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để set device to State 2 |
| **`XCmd_St3`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để set device to State 3 |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Acquire (Oper/Prog to Ext) |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Release (Ext to Oper/Prog) |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi |

## 3. Bảng chi tiết 80 Biến Đầu Ra (`Output Parameters`) của `P_D4SD`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Out_A`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output A to Device |
| **`Out_B`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output B to Device |
| **`Out_C`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output C to Device |
| **`Out_D`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output D to Device |
| **`Out_Horn`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1=Sound audible prior to commanded energize/ state change |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final Device state source and quality. GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`Val_Cmd`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Command 0=None, 1=State 0, 2=State 1, 3=State 2, 4=State 3 |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Confirmed Sts 0=Powerup/Reset, 1=State0, 2=State1, 3=State2, 4=State3, 9=Moving |
| **`Val_Fault`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Fault Status 0=None, 16=PositionFail, 17=DeviceFault, 32=I/OFault, 34=CfgErr |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận của khối này cộng với khối kênh (enum) |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Current Object Owner ID (0=not owned) |
| **`Sts_St0`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Device commanded to and confirmed in State 0 |
| **`Sts_St1`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Device commanded to and confirmed in State 1 |
| **`Sts_St2`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Device commanded to and confirmed in State 2 |
| **`Sts_St3`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Device commanded to and confirmed in State 3 |
| **`Sts_Moving`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Device not yet confirmed in commended state |
| **`Sts_Horn`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Valve Horn Active |
| **`Sts_Available`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device available for control by automation (Prog) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Bypassable Interlocks and Permissives are Bypassed |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Interlock / Permissive Bypassing Active (Bypassed or Maintenance) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device Not Ready, see detail bits for reason |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_EqpFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: External Equipment Fault (Fault or Shed Requires Reset) |
| **`Nrdy_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Lỗi thiết bị ngắt tải (yêu cầu nhấn Reset) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Điều kiện Khóa liên động không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Lỗi truyền thông I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Thiết bị đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Nrdy_PrioSt0`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Oper/Ext Priority State 0 Command Requires Reset |
| **`Nrdy_Perm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Điều kiện Cho phép khởi động không thỏa mãn (Permissive Not OK) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device has a Maintenance Bypass function active |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=An Alarm is Disabled or Shelved, display icon |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Error in Config: see detail bits for reason |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Feeback Check timer preset (use 0 to 2147483) |
| **`Err_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Simulation timer preset (use 0 to 2147483) |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thông số cảnh báo (Thời gian duy trì tối thiểu, thời gian Shelve, mức Severity) không hợp lệ |
| **`Sts_Hand`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Hand đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn OoS, Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_OoS`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Out of Service đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_Maint`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Maintenance đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ovrd, Ext, Prog, Oper) |
| **`Sts_Ovrd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Override đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ext, Prog, Oper) |
| **`Sts_Ext`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=External đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Prog, Oper) |
| **`Sts_Prog`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Program đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_ProgLocked`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Program đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Oper`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_OperLocked`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Selection equals the Normal (Prog or Oper) |
| **`Sts_ExtReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=External request inhibited, cannot Get to External from current state |
| **`Sts_ProgReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Program request inhibited, cannot Get to Program from current state |
| **`Sts_MAcqRcvd`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Maintenance Acquire command received this scan |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc lỗi ngắt tải (Shed) đã hết điều kiện lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device failed to reach commended state |
| **`Alm_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Alarm: Device failed to reach commended state |
| **`Ack_Fail`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Fail to reach commanded state Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_FailDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Fail to reach commanded state Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_FailShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Fail to reach commanded state Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_FailSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Fail to reach commanded state alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_DeviceFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device Fault (from Inp_DeviceFault) |
| **`Alm_DeviceFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Device Fault Alarm |
| **`Ack_DeviceFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Device Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_DeviceFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_DeviceFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_DeviceFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device commanded to State 0 by an Interlock NOT OK (one-shot) |
| **`Alm_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Alarm: Device set to State 0 by an Interlock NOT OK |
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
| **`P_D4SD`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 33 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_D4SD`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'Discrete 2, 3 or 4 S'` | [Biến cấu hình thông số] Description for display on HMI. This string is shown in the title bar of the faceplate. |
| **`Cfg_Label`** | `STRING_20` | `'Discrete Device'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI. This string appears on the graphic symbol. |
| **`Cfg_St0Text`** | `STRING_8` | `'Off'` | [Biến cấu hình thông số] Text to display in State 0 |
| **`Cfg_St1Text`** | `STRING_8` | `'On'` | [Biến cấu hình thông số] Text to display in State 1 |
| **`Cfg_St2Text`** | `STRING_8` | `''` | [Biến cấu hình thông số] Text to display in State 2 |
| **`Cfg_St3Text`** | `STRING_8` | `''` | [Biến cấu hình thông số] Text to display in State 3 |
| **`Cfg_Tag`** | `STRING_20` | `'P_D4SD'` | [Biến cấu hình thông số] Tagname for display on HMI. This string is shown in the title bar of the faceplate. |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc quản lý lựa chọn nguồn lệnh điều khiển |
| **`DeviceFault`** | `P_Alarm` | `{...}` | Device Fault from device via input |
| **`Fail`** | `P_Alarm` | `{...}` | Device Fail to reach commanded position |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_D4SD'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | Interlock Trip Alarm |
| **`IOFault`** | `P_Alarm` | `{...}` | I/O Fault Alarm |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bypass Interlocks and Permissives |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Check Interlocks and Permissives |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass, enable HMI button |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check, enable HMI button |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Oper Cmd to xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Oper Cmd to xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi, plus Xác nhận (Acknowledge) alarms |
| **`OCmd_St0`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để set device to State 0 |
| **`OCmd_St1`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để set device to State 1 |
| **`OCmd_St2`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để set device to State 2 |
| **`OCmd_St3`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để set device to State 3 |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1=At least one Alarm or latched Shed requires Reset |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1=At least one Alarm or latched Shed requires Reset or Ack |
| **`ORdy_St0`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_St0, enable HMI button |
| **`ORdy_St1`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_St1, enable HMI button |
| **`ORdy_St2`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_St2, enable HMI button |
| **`ORdy_St3`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_St3, enable HMI button |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_nPos`
**Thuộc chương:** Chương 4: Van điều khiển & Cơ cấu chấp hành (Valves)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Valves 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_nPos`:** **120** Biến đầu vào (`Input Parameters`) | **98** Biến đầu ra (`Output Parameters`) | **45** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_nPos`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_Pos1Fdbk`, `Inp_Pos2Fdbk`, `Inp_Pos3Fdbk`, `Inp_Pos4Fdbk`, `Inp_Pos5Fdbk`, `Inp_Pos6Fdbk`, `Inp_Pos7Fdbk`, `Inp_Pos8Fdbk`, `Inp_LockFdbk`, `Inp_UnlockFdbk`, `Inp_CylExtFdbk`, `Inp_CylRetrFdbk`, `Inp_CylLeftFdbk`, `Inp_CylRightFdbk`, `Inp_PermOK`, `Inp_NBPermOK`, `Inp_IntlkOK`, `Inp_NBIntlkOK`...
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_HornOnChange`, `Cfg_StartHornT`, `Cfg_NumPos`, `Cfg_Circ`, `Cfg_CWOnly`, `Cfg_ViaPos1`, `Cfg_IntlkToPos1`, `Cfg_OutPosLatch`, `Cfg_HasLock`, `Cfg_HasPosFdbk`, `Cfg_UsePosFdbk`, `Cfg_HasLockFdbk`, `Cfg_UseLockFdbk`, `Cfg_HasCylFdbk`, `Cfg_UseCylFdbk`, `Cfg_HasMoreObj`, `Cfg_HasPermObj`, `Cfg_HasIntlkObj`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_Label`, `Cfg_Pos1Name`, `Cfg_Pos2Name`, `Cfg_Pos3Name`, `Cfg_Pos4Name`, `Cfg_Pos5Name`, `Cfg_Pos6Name`, `Cfg_Pos7Name`, `Cfg_Pos8Name`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_nPos` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PSet_Owner`, `PCmd_Pos1`, `PCmd_Pos2`, `PCmd_Pos3`, `PCmd_Pos4`, `PCmd_Pos5`, `PCmd_Pos6`, `PCmd_Pos7`, `PCmd_Pos8`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_PosFailAck`, `PCmd_LockFailAck`, `PCmd_IntlkTripAck`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Out_Pos1`, `Out_Pos2`, `Out_Pos3`, `Out_Pos4`, `Out_Pos5`, `Out_Pos6`, `Out_Pos7`, `Out_Pos8`, `Out_Horn`, `Out_Inc`, `Out_Dec`, `Out_Unlock`, `Out_Lock`, `Out_CylExtend`, `Out_CylRetract`, `Out_CylLeft`, `Out_CylRight`, `Val_Cmd`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 120 Biến Đầu Vào (`Input Parameters`) của `P_nPos`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_Pos1Fdbk`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Position 1 Feedback: 1=Device confirmed at Position 1 |
| **`Inp_Pos2Fdbk`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Position 2 Feedback: 1=Device confirmed at Position 2 |
| **`Inp_Pos3Fdbk`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Position 3 Feedback: 1=Device confirmed at Position 3 |
| **`Inp_Pos4Fdbk`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Position 4 Feedback: 1=Device confirmed at Position 4 |
| **`Inp_Pos5Fdbk`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Position 5 Feedback: 1=Device confirmed at Position 5 |
| **`Inp_Pos6Fdbk`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Position 6 Feedback: 1=Device confirmed at Position 6 |
| **`Inp_Pos7Fdbk`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Position 7 Feedback: 1=Device confirmed at Position 7 |
| **`Inp_Pos8Fdbk`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Position 8 Feedback: 1=Device confirmed at Position 8 |
| **`Inp_LockFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Device confirmed Locked / Sealed In-Position |
| **`Inp_UnlockFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Device confirmed Unlocked / Unsealed and free to move |
| **`Inp_CylExtFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Device indexing cylinder confirmed extended |
| **`Inp_CylRetrFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Device indexing cylinder confirmed retracted |
| **`Inp_CylLeftFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Device indexing cylinder confirmed in left position |
| **`Inp_CylRightFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Device indexing cylinder confirmed in right positioin |
| **`Inp_PermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Permissives OK, Device can energize |
| **`Inp_NBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Non-Bypassable Permissives OK, device can energize |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Interlocks OK, device can energize |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Non-Bypassable Interlocks OK, device can energize |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] I/O Communication Status 0=OK, 1=fail |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Simulation input. When set to 1, the instruction simulates a working device while keeping outputs de-energized. When set to 0, the instruction controls the device normally. |
| **`Inp_Hand`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Acquire Hand (typ. hardwired local), 0=Release Hand |
| **`Inp_Ovrd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Acquire Override (higher priority program logic), 0=Release Override |
| **`Inp_OvrdCmd`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Position command in Override mode: 0 = Stay 1 = Position 1 2 = Position 2 3 = Position 3 4 = Position 4 5 = Position 5 6 = Position 6 7 = Position 7 8 = Position 8 |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Inhibit External Acquisition, 0=Allow External Acquisition |
| **`Inp_HornInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Tells HMI a P_Perm is connected to Inp_Perm |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Reset all fault conditions and latched Alarms |
| **`Cfg_HornOnChange`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 0 = Horn on energize only, 1 = Horn on any state change |
| **`Cfg_StartHornT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Time to sound audible on commanded start (sec) (0=disabled) |
| **`Cfg_NumPos`** | `DINT` | `3` | Ẩn (Tag) | [Biến cấu hình thông số] Number of Device Positions (2 .. 8) |
| **`Cfg_Circ`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Positions are circular (rotary device, after Pos n is Pos 1) |
| **`Cfg_CWOnly`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Circular (rotary) device can only rotate "clockwise", 0=bidirectional (use shortest path) |
| **`Cfg_ViaPos1`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Drive Linear device to Pos 1 on every move; 0=move direct to cmd pos |
| **`Cfg_IntlkToPos1`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Drive to Pos 1 on Interlock or Fault; 0=De-energize only |
| **`Cfg_OutPosLatch`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Latch in Out_PosX until new Position commanded |
| **`Cfg_HasLock`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Must sequence a position lock or seal |
| **`Cfg_HasPosFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Device has feedback for all positions |
| **`Cfg_UsePosFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Position Feedback shall be used to check device position |
| **`Cfg_HasLockFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Device has locked / unlocked feedback |
| **`Cfg_UseLockFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Lock Feedback shall be used to verify locking and unlocking |
| **`Cfg_HasCylFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Device indexing cylinders have position feedback |
| **`Cfg_UseCylFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Indexing cylinder position feedback shall be checked |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_HasPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng permissive liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng interlock liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_OCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=New device OCmd resets shed latches and cleared alarms, 0=OCmdReset req'd. |
| **`Cfg_XCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=New device XCmd resets shed latches and cleared alarms, 0=XCmdReset req'd. |
| **`Cfg_CmdDuringMove`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=New pos. cmd. OK during move, 0=must wait for prev. cmd. to finish |
| **`Cfg_OvrdPermIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Override ignores Bypassable Perm/ Intlk; 0=always use Perm/Intlk |
| **`Cfg_ShedOnPosFail`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=De-energize Device and Alarm on Position Fail; 0=Alarm only |
| **`Cfg_ShedOnLockFail`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=De-energize Device and Alarm on Lock Fail; 0=Alarm only |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=De-energize Device and Alarm on I/O Fault; 0=Alarm only |
| **`Cfg_HasPosFailAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasLockFailAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_PosFailResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_LockFailResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_PosFailAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_LockFailAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_PosFailSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_LockFailSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_Retries`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Number of Retries via Pos 1 before declaring failure (0=don't retry) |
| **`Cfg_ExtendT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Delay before extending cylinder to engage for move (sec) |
| **`Cfg_VerifyT`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Delay to verify device has settled in commanded position (sec) |
| **`Cfg_PosChkT`** | `REAL` | `30` | Ẩn (Tag) | [Biến cấu hình thông số] Max time to reach target position before fail (sec) |
| **`Cfg_LockChkT`** | `REAL` | `5` | Ẩn (Tag) | [Biến cấu hình thông số] Max time to get locked / unlocked feedback before fail (sec) |
| **`Cfg_PosSimT`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Time to reach target position in simulation (sec) |
| **`Cfg_LockSimT`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Time to lock or unlock in simulation (sec) |
| **`Cfg_CylSimT`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Time to simulate index cylinder feedback in simulation (sec) |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Mã định danh chủ sở hữu chương trình (Program Owner ID: khác 0 để đăng ký chiếm quyền, bằng 0 để nhả quyền). |
| **`PCmd_Pos1`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để go to Position 1 |
| **`PCmd_Pos2`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để go to Position 2 |
| **`PCmd_Pos3`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để go to Position 3 |
| **`PCmd_Pos4`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để go to Position 4 |
| **`PCmd_Pos5`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để go to Position 5 |
| **`PCmd_Pos6`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để go to Position 6 |
| **`PCmd_Pos7`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để go to Position 7 |
| **`PCmd_Pos8`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để go to Position 8 |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Program (Oper to Prog) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Operator (Prog to Oper) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa cứng chế độ Program (không cho phép Operator giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Unlock Program (allow Oper to acquire) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Normal (Oper or Prog) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để ResetShed Latches and Cleared Alarms |
| **`PCmd_PosFailAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_LockFailAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_PosFailSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Position Failure Alarm |
| **`PCmd_PosFailUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Position Failure Alarm |
| **`PCmd_LockFailSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Lock / Unlock Failure Alarm |
| **`PCmd_LockFailUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Lock / Unlock Failure Alarm |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Interlock Trip Alarm |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Interlock Trip Alarm |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) I/O Fault Alarm |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) I/O Fault Alarm |
| **`PCmd_PosFailUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_LockFailUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Release (Maint to Oper/Prog/Ovrd) |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Unlock Operator (allow Prog to acquire) |
| **`XCmd_Pos1`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để go to Position 1 |
| **`XCmd_Pos2`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để go to Position 2 |
| **`XCmd_Pos3`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để go to Position 3 |
| **`XCmd_Pos4`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để go to Position 4 |
| **`XCmd_Pos5`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để go to Position 5 |
| **`XCmd_Pos6`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để go to Position 6 |
| **`XCmd_Pos7`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để go to Position 7 |
| **`XCmd_Pos8`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để go to Position 8 |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Acquire (Oper/Prog to Ext) |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Release (Ext to Oper/Prog) |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi |

## 3. Bảng chi tiết 98 Biến Đầu Ra (`Output Parameters`) của `P_nPos`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Out_Pos1`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output to go to Position 1 |
| **`Out_Pos2`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output to go to Position 2 |
| **`Out_Pos3`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output to go to Position 3 |
| **`Out_Pos4`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output to go to Position 4 |
| **`Out_Pos5`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output to go to Position 5 |
| **`Out_Pos6`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output to go to Position 6 |
| **`Out_Pos7`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output to go to Position 7 |
| **`Out_Pos8`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output to go to Position 8 |
| **`Out_Horn`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output to go to Position 8 |
| **`Out_Inc`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output to drive to Increasing Position ("CW") |
| **`Out_Dec`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output to drive to Decreasing Position ("CCW") |
| **`Out_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output to unlock / unseal device |
| **`Out_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output to lock / seal device |
| **`Out_CylExtend`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output to Extend Cylinder to engage ratchet device |
| **`Out_CylRetract`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output to Retract Cylinder to disengage ratchet device |
| **`Out_CylLeft`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output to ratchet device to Left (increase) |
| **`Out_CylRight`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output to ratchet device to Right (decrease) |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final position source and quality. GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`Val_Cmd`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Command 0=None, 1=Pos 1, 2=Pos 2, .., 8=Pos 8 |
| **`Val_Fdbk`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Feedback 0=Moving, 1=Pos 1, 2=Pos 2,.., 8=Pos 8, 9=Multiple Pos |
| **`Val_Sts`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Confirmed Sts 0=Powerup/Reset, 1=Pos 1, 2=Pos 2,.., 8=Pos 8, 9=Moving |
| **`Val_Fault`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Fault Status 0=None, 16=Position Fault, 17 = Lock Fault, 32=I/O Fault, 34=Config Error |
| **`Val_State`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Internal State: 0=reset/unknown, 1=in pos, 2=unlock, 3=moving to pos 1, 4=moving to cmd pos, 5=lock, 6=fault |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận của khối này cộng với khối kênh (enum) |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Current Object Owner ID (0=not owned) |
| **`Sts_Pos1`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Device confirmed in Position 1 |
| **`Sts_Pos2`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Device confirmed in Position 2 |
| **`Sts_Pos3`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Device confirmed in Position 3 |
| **`Sts_Pos4`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Device confirmed in Position 4 |
| **`Sts_Pos5`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Device confirmed in Position 5 |
| **`Sts_Pos6`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Device confirmed in Position 6 |
| **`Sts_Pos7`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Device confirmed in Position 7 |
| **`Sts_Pos8`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Device confirmed in Position 8 |
| **`Sts_Horn`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Valve Horn Active |
| **`Sts_Moving`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Device moving to target position, not yet achieved |
| **`Sts_Available`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device available for control by automation (Prog) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Bypassable Interlocks and Permissives are Bypassed |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Bypassing Active (Bypassed or Maintenance) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device Not Ready, see detail bits for reason |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Lỗi thiết bị ngắt tải (yêu cầu nhấn Reset) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Điều kiện Khóa liên động không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Lỗi truyền thông I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Thiết bị đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Nrdy_Perm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Điều kiện Cho phép khởi động không thỏa mãn (Permissive Not OK) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=An Alarm is Disabled or Shelved, display icon |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Error in Config: see detail bits for reason |
| **`Err_NumPos`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Invalid number of positions (use 2..8) |
| **`Err_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Invalid Failure (Check) Time (use 0 to 2147483) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Invalid Delay Time (use 0 to 2147483) |
| **`Err_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Invalid Simulation Time (use 0 to 2147483) |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thông số cảnh báo (Thời gian duy trì tối thiểu, thời gian Shelve, mức Severity) không hợp lệ |
| **`Sts_Hand`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Hand đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn OoS, Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_OoS`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Out of Service đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_Maint`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Maintenance đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ovrd, Ext, Prog, Oper) |
| **`Sts_Ovrd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Override đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ext, Prog, Oper) |
| **`Sts_Ext`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=External đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Prog, Oper) |
| **`Sts_Prog`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Program đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_ProgLocked`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Program đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Oper`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_OperLocked`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Selection equals the Normal (Prog or Oper) |
| **`Sts_ExtReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=External request inhibited, cannot Get to External from current state |
| **`Sts_ProgReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Program request inhibited, cannot Get to Program from current state |
| **`Sts_MAcqRcvd`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Maintenance Acquire command received this scan |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc lỗi ngắt tải (Shed) đã hết điều kiện lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_PosFail`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Position Failure Status (0=OK, 1=Bad) |
| **`Alm_PosFail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Position Failure Alarm |
| **`Ack_PosFail`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Position Failure Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_PosFailDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Position Failure Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_PosFailShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Position Failure Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_PosFailSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Position Failure Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_LockFail`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Lock / Unlock Failure Status (0=OK, 1=Bad) |
| **`Alm_LockFail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Lock / Unlock Failure Alarm |
| **`Ack_LockFail`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Lock / Unlock Failure Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_LockFailDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Lock / Unlock Failure Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_LockFailShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Lock / Unlock Failure Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_LockFailSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Lock / Unlock Failure Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IntlkTrip`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Device de-energized by an Interlock NOT OK |
| **`Alm_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Alarm: Device de-energized by an Interlock NOT OK |
| **`Ack_IntlkTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Interlock Trip Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IntlkTripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Interlock Trip Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IntlkTripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Interlock Trip Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IntlkTripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Interlock Trip Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IOFault`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] I/O Comm Fault Status (0=OK, 1=Bad) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=I/O Fault Alarm |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=I/O Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`MRdy_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Enable (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`P_nPos`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 45 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_nPos`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'n-Position Device'` | [Biến cấu hình thông số] Description for display on HMI. This string is shown in the title bar of the faceplate. |
| **`Cfg_Label`** | `STRING_20` | `'n-Position Device'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI. This string appears on the graphic symbol. |
| **`Cfg_Pos1Name`** | `STRING_16` | `'Position 1'` | [Biến cấu hình thông số] Description for Position 1 on HMI |
| **`Cfg_Pos2Name`** | `STRING_16` | `'Position 2'` | [Biến cấu hình thông số] Description for Position 2 on HMI |
| **`Cfg_Pos3Name`** | `STRING_16` | `'Position 3'` | [Biến cấu hình thông số] Description for Position 3 on HMI |
| **`Cfg_Pos4Name`** | `STRING_16` | `'Position 4'` | [Biến cấu hình thông số] Description for Position 4 on HMI |
| **`Cfg_Pos5Name`** | `STRING_16` | `'Position 5'` | [Biến cấu hình thông số] Description for Position 5 on HMI |
| **`Cfg_Pos6Name`** | `STRING_16` | `'Position 6'` | [Biến cấu hình thông số] Description for Position 6 on HMI |
| **`Cfg_Pos7Name`** | `STRING_16` | `'Position 7'` | [Biến cấu hình thông số] Description for Position 7 on HMI |
| **`Cfg_Pos8Name`** | `STRING_16` | `'Position 8'` | [Biến cấu hình thông số] Description for Position 8 on HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_nPos'` | [Biến cấu hình thông số] Description for display on HMI. This string is shown in the title bar of the faceplate. |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc quản lý lựa chọn nguồn lệnh điều khiển |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_nPos'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | Interlock Trip Alarm |
| **`IOFault`** | `P_Alarm` | `{...}` | I/O Fault Alarm |
| **`LockFail`** | `P_Alarm` | `{...}` | Lock / Unlock Failure Alarm |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bypass Interlocks and Permissives |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Check Interlocks and Permissives |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`OCmd_Pos1`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để go to Position 1 |
| **`OCmd_Pos2`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để go to Position 2 |
| **`OCmd_Pos3`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để go to Position 3 |
| **`OCmd_Pos4`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để go to Position 4 |
| **`OCmd_Pos5`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để go to Position 5 |
| **`OCmd_Pos6`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để go to Position 6 |
| **`OCmd_Pos7`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để go to Position 7 |
| **`OCmd_Pos8`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để go to Position 8 |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Oper Cmd to xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Oper Cmd to xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi, plus Xác nhận (Acknowledge) alarms |
| **`ORdy_Pos1`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Pos1 (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Pos2`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Pos2 (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Pos3`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Pos3 (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Pos4`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Pos4 (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Pos5`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Pos5 (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Pos6`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Pos6 (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Pos7`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Pos7 (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Pos8`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Pos8 (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1=At least one Alarm or latched Shed requires Reset |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1=At least one Alarm or latched Shed requires Reset or Ack |
| **`PosFail`** | `P_Alarm` | `{...}` | Position Failure Alarm |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_ValveC`
**Thuộc chương:** Chương 4: Van điều khiển & Cơ cấu chấp hành (Valves)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Valves 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_ValveC`:** **89** Biến đầu vào (`Input Parameters`) | **79** Biến đầu ra (`Output Parameters`) | **28** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_ValveC`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_PosFdbk`, `Inp_OpenLS`, `Inp_ClosedLS`, `Inp_ActuatorFault`, `Inp_IntlkOK`, `Inp_NBIntlkOK`, `Inp_IOFault`, `Inp_Sim`, `Inp_Hand`, `Inp_Ovrd`, `Inp_OvrdCV`, `Inp_ExtInh`, `Inp_Reset`, `Inp_IntlkAvail`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_ClampPosToLS`, `Cfg_ShedHold`, `Cfg_ShedOnIOFault`, `Cfg_SkipRoCLim`, `Cfg_SetTrack`, `Cfg_SetTrackOvrdHand`, `Cfg_HasPosFdbk`, `Cfg_HasOpenLS`, `Cfg_HasClosedLS`, `Cfg_UseOpenLS`, `Cfg_UseClosedLS`, `Cfg_LSFail`, `Cfg_HasMoreObj`, `Cfg_HasIntlkObj`, `Cfg_HasCVNav`, `Cfg_OvrdIntlk`, `Cfg_DecPlcs`, `Cfg_HasIntlkTripAlm`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_CVNavTag`, `Cfg_Desc`, `Cfg_EU`, `Cfg_Label`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_ValveC` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PSet_CV`, `PSet_Owner`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_IntlkTripAck`, `PCmd_ActuatorFaultAck`, `PCmd_IOFaultAck`, `PCmd_IntlkTripSuppress`, `PCmd_IntlkTripUnsuppress`, `PCmd_ActuatorFaultSuppress`, `PCmd_ActuatorFaultUnsuppress`, `PCmd_IOFaultSuppress`, `PCmd_IOFaultUnsuppress`, `PCmd_IntlkTripUnshelve`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Out_CV`, `Out_Open`, `Out_Close`, `Val_Pos`, `Val_CVSet`, `Val_CVOut`, `Val_CVEUMin`, `Val_CVEUMax`, `Val_Fdbk`, `Val_Sts`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Val_Owner`, `Sts_CVInfNaN`, `Sts_PosInfNaN`, `Sts_BumpOpen`, `Sts_BumpClose`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 89 Biến Đầu Vào (`Input Parameters`) của `P_ValveC`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_PosFdbk`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Position Feedback from Valve (in Raw Input units) |
| **`Inp_OpenLS`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Valve Open Limit Switch 1=confirmed fully open |
| **`Inp_ClosedLS`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Valve Closed Limit Switch 1=confirmed fully closed |
| **`Inp_ActuatorFault`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Valve Actuator Fault (overload, etc.), 0=OK, 1=Fault) |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Interlocks OK, Pulse Output Valve can be moved |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Non-Bypassable Interlocks OK, Pulse Output Valve can be moved |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Input / Output Communication Status 0=OK, 1=fail |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Simulation input. When set to 1, the instruction holds the analog output at zero and holds the discrete pulse outputs off while simulating a working valve. When set to 0, the instruction controls the valve position normally. |
| **`Inp_Hand`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Acquire Hand (typ. hardwired local), 0=Release Hand |
| **`Inp_Ovrd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Acquire Override (higher priority program logic), 0=Release Override |
| **`Inp_OvrdCV`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] CV target in Override Mode (CV EU) |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Inhibit External Acquisition, 0=Allow External Acquisition |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Reset shed latches and cleared alarms |
| **`Inp_IntlkAvail`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Interlock Availibility OK |
| **`Cfg_ClampPosToLS`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Clamps Val_Pos to EUMin/Max when on a Limit Switch |
| **`Cfg_ShedHold`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Hold Output on Interlock or bad PV, 0=Go to Cfg_IntlkCV |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Take shed action (hold or interlock CV) and alarm on I/O Fault. 0 = Alarm only on I/O Fault. IMPORTANT: If a condition is configured to shed the device on a fault, a reset is required to clear the shed fault to command the device. |
| **`Cfg_SkipRoCLim`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Skip RoC Limiting in Intlk, Maint, Ovrd modes |
| **`Cfg_SetTrack`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] This parameter is used to set up bumpless behavior of setting parameters when switching modes. When this parameter is 1, in Program mode the operator settings track the program settings; in Operator mode the program settings track the operator settings; and the simulation inputs match the output values (transitions are bumpless). When this parameter is 0, the operator settings and program settings are not modified by this instruction. In this case, when the mode is changed, the effective value of the setting can change depending on the program- set and operator-set values. |
| **`Cfg_SetTrackOvrdHand`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Prog/Oper Settings track Override/Hand CV |
| **`Cfg_HasPosFdbk`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Valve provides an analog position feedback, 0=simulate feedback |
| **`Cfg_HasOpenLS`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Valve provides an Open Limit Switch signal |
| **`Cfg_HasClosedLS`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Valve provides a Closed Limit Switch signal |
| **`Cfg_UseOpenLS`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Valve Open LS should be used for open/closed status |
| **`Cfg_UseClosedLS`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Valve Closed LS should be used for open/closed status |
| **`Cfg_LSFail`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1=both switches ON=fail, 0=both switches OFF=fail |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng interlock liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasCVNav`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Tells HMI to enable navigation to a connected CV object |
| **`Cfg_OvrdIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Override ignores Bypassable Intlk; 0=always use Intlk |
| **`Cfg_DecPlcs`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Number of decimal places for CV display (0..6) |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasActuatorFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_ActuatorFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_ActuatorFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the severity of each alarm that gauges the color and symbol that are used to indicate alarm status on the faceplate and global object. The following are valid values: 1…250 = Low 251…500 = Medium 501…750 = High 751…1000 = Urgent IMPORTANT: For FactoryTalk View software, version 7.0, these severity priorities drive only the indication on the global object and faceplate. The Alarm & Events definition severity drives the color and symbol that is used on the alarm banner and alarm summary as well a the value returned by the FactoryTalk Alarm and Events software display commands. |
| **`Cfg_ActuatorFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the severity of each alarm that gauges the color and symbol that are used to indicate alarm status on the faceplate and global object. The following are valid values: 1…250 = Low 251…500 = Medium 501…750 = High 751…1000 = Urgent IMPORTANT: For FactoryTalk View software, version 7.0, these severity priorities drive only the indication on the global object and faceplate. The Alarm & Events definition severity drives the color and symbol that is used on the alarm banner and alarm summary as well a the value returned by the FactoryTalk Alarm and Events software display commands. |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the severity of each alarm that gauges the color and symbol that are used to indicate alarm status on the faceplate and global object. The following are valid values: 1…250 = Low 251…500 = Medium 501…750 = High 751…1000 = Urgent IMPORTANT: For FactoryTalk View software, version 7.0, these severity priorities drive only the indication on the global object and faceplate. The Alarm & Events definition severity drives the color and symbol that is used on the alarm banner and alarm summary as well a the value returned by the FactoryTalk Alarm and Events software display commands. |
| **`Cfg_MinCV`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum CV (in CV EU, for limiting) |
| **`Cfg_MaxCV`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Maximum CV (in CV EU, for limiting) |
| **`Cfg_CVRoCLimInc`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] CV Rate of Change Limit, Increasing, (EU/sec, 0=unlimited) |
| **`Cfg_CVRoCLimDec`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] CV Rate of Change Limit, Decreasing, (EU/sec, 0=unlimited) |
| **`Cfg_IntlkCV`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] CV (target in engineering units) when interlocked (used when Cfg_ShedHold is configured to 0). |
| **`Cfg_CVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters must be set to the range of the signal connected to the setting inputs (for example, PSet_CV). The output is then scaled to the values set by Cfg_CVRawMin and Cfg_CVRawMax. |
| **`Cfg_CVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters must be set to the range of the signal connected to the setting inputs (for example, PSet_CV). The output is then scaled to the values set by Cfg_CVRawMin and Cfg_CVRawMax. |
| **`Cfg_CVRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters must be set to match the CV range of the output signals (Out_CV) in raw units. EXAMPLE: If your output card expects a value in the range of 4…20 and the P_ValveC's CV is ranged 0…100% , P_ValveC can scale the outputs for you by setting Cfg_CVRawMin to 4, Cfg_CVRawMax to 20, Cfg_CVEUMin to 0, and Cfg_CVEUMax to 100. |
| **`Cfg_CVRawMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters must be set to match the CV range of the output signals (Out_CV) in raw units. EXAMPLE: If your output card expects a value in the range of 4…20 and the P_ValveC's CV is ranged 0…100% , P_ValveC can scale the outputs for you by setting Cfg_CVRawMin to 4, Cfg_CVRawMax to 20, Cfg_CVEUMin to 0, and Cfg_CVEUMax to 100. |
| **`Cfg_FdbkRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters must be set to the range of the signal connected to the feedback signal (Inp_PosFdbk). The inputs are then scaled to the values set by Cfg_CVEUMin and Cfg_CVEUMax. |
| **`Cfg_FdbkRawMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters must be set to the range of the signal connected to the feedback signal (Inp_PosFdbk). The inputs are then scaled to the values set by Cfg_CVEUMin and Cfg_CVEUMax. |
| **`Cfg_CycleT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Open and Close Pulse Output overall period (s) |
| **`Cfg_OpenRate`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Rate at which valve moves when opening (CV EU/sec) |
| **`Cfg_CloseRate`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Rate at which valve moves when closing (CV EU/sec) |
| **`Cfg_MaxOnT`** | `REAL` | `5` | Ẩn (Tag) | [Biến cấu hình thông số] Open and Close Pulse Output maximum ON time (s) |
| **`Cfg_MinOnT`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Open and Close Pulse Output minimum ON time (s) |
| **`Cfg_BumpT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Time to bump valve open or closed (by Oper) when Fdbk Bad (sec) |
| **`Cfg_Deadtime`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Additional time on first pulse after stop or direction change |
| **`Cfg_MaxClosedPos`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Position above which valve is assumed open if limit switches not used |
| **`PSet_CV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Program Setting of Controlled Variable (output) (CV EU) |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Mã định danh chủ sở hữu chương trình (Program Owner ID: khác 0 để đăng ký chiếm quyền, bằng 0 để nhả quyền). |
| **`XSet_CV`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài (External)] External Setting of Controlled Variable (output) (CV EU) |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Program (Oper to Prog) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Operator (Prog to Oper) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa cứng chế độ Program (không cho phép Operator giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Unlock Program (allow Oper to acquire) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Normal (Oper or Prog) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_ActuatorFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Interlock Trip Alarm |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Interlock Trip Alarm |
| **`PCmd_ActuatorFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Actuator Fault Alarm |
| **`PCmd_ActuatorFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Actuator Fault Alarm |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) I/O Fault Alarm |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) I/O Fault Alarm |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_ActuatorFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Release (Maint to Oper/Prog/Ovrd) |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Unlock Operator (allow Prog to acquire) |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi |
| **`XCmd_Acq`** | `BOOL` | - | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Acquire (Oper/Prog to Ext) |
| **`XCmd_Rel`** | `BOOL` | - | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Release (Ext to Oper/Prog) |

## 3. Bảng chi tiết 79 Biến Đầu Ra (`Output Parameters`) của `P_ValveC`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Out_CV`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] CV Output in Raw (I/O Card) Units |
| **`Out_Open`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Pulse Output to drive valve Open |
| **`Out_Close`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Pulse Output to drive valve closed |
| **`Val_Pos`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Valve actual position from feedback (CV EU) |
| **`Val_CVSet`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Value of selected CV Setting (before rate limiting, CV EU) |
| **`Val_CVOut`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Value of CV output (after clamping and ramping) (CV EU) |
| **`Val_CVEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Minimum of scaled range = MIN (Cfg_CVEUMin, Cfg_CVEUMax) |
| **`Val_CVEUMax`** | `REAL` | `100` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Maximum of scaled range = MAX (Cfg_CVEUMin, Cfg_CVEUMax) |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final position source and quality. GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`Val_Fdbk`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Feedback 0=Moving, 1=Closed, 2=Opened, 3=Limit Switch Failure |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] 0=AtTarget, 1=RampClose, 2=RampOpen, 3=Clamp@Min, 4=Clamp@Max, 5=BumpClose, 6=BumpOpen |
| **`Val_Fault`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Fault Status 0=none, 32=I/OFault, 34=CfgErr |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận của khối này cộng với khối kênh (enum) |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Current Object Owner ID (0=not owned) |
| **`Sts_CVInfNaN`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Selected CV is Infinite or Not A Number (1.$, 1.#NaN) |
| **`Sts_PosInfNaN`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Inp_PosFdbk is Infinite or Not A Number (1.$, 1.#NaN) |
| **`Sts_BumpOpen`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Bump Open Requested or Active |
| **`Sts_BumpClose`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Bump Close Requested or Active |
| **`Sts_Ramping`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=CV is ramping to target |
| **`Sts_Clamped`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=CV Set is clamped at CVMin or CVMax |
| **`Sts_SkipRoCLim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Rate of Change Limiting was skipped this scan (Maint, Ovrd, Intlk, Hand) |
| **`Sts_Active`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=CV is greater than Cfg_MaxClosedPos, show valve symbol as "opened" |
| **`Sts_Available`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Pulse Output Valve available for control by automation (Prog) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Bypassable Interlocks are Bypassed |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Bypassing Active (Bypassed or Maintenance) |
| **`Sts_LSFail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Limit Switch Overlap Failure |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device Not Ready, see detail bits for reason |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Điều kiện Khóa liên động không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Lỗi truyền thông I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Thiết bị đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device has a Maintenance Bypass function active |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=An Alarm is Disabled or Shelved, display icon |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Error in Config: see detail bits for reason |
| **`Err_Limit`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: CV Limits swapped |
| **`Err_EU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Scaled CV EU Min = Max |
| **`Err_Raw`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Raw Output Scaling Min = Max |
| **`Err_Fdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Raw Feedback Scaling Min = Max |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Invalid Timer Preset (use 0 to 2147483) |
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
| **`Sts_ActuatorFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Actuator Fault detected (input) |
| **`Alm_ActuatorFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Alarm: Actuator Fault Detected |
| **`Ack_ActuatorFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Actuator Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_ActuatorFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Actuator Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_ActuatorFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Actuator Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_ActuatorFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Actuator Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Fault Status (0=OK, 1=Bad) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=I/O Fault Alarm |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=I/O Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=I/O Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`MRdy_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`P_ValveC`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 28 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_ValveC`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`ActuatorFault`** | `P_Alarm` | `{...}` | Actuator Fault Alarm (based on Actuator Fault Input) |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_CVNavTag`** | `STRING_NavTag` | `''` | [Biến cấu hình thông số] Tagname for destination of CV Navigation button. IMPORTANT: This tag does not work in FactoryTalk ME Software. |
| **`Cfg_Desc`** | `STRING_40` | `'Control Valve'` | [Biến cấu hình thông số] Description for display on HMI. This string is shown in the title bar of the faceplate. |
| **`Cfg_EU`** | `STRING_8` | `'% Open'` | [Biến cấu hình thông số] Engineering units for display on HMI. |
| **`Cfg_Label`** | `STRING_20` | `'Control Valve'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI. This string appears on the graphic symbol. |
| **`Cfg_Tag`** | `STRING_20` | `'P_ValveC'` | [Biến cấu hình thông số] Description for display on HMI. This string is shown in the title bar of the faceplate. |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc quản lý lựa chọn nguồn lệnh điều khiển |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_ValveC'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI và phần mềm thông tin |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | Interlock Trip Alarm |
| **`IOFault`** | `P_Alarm` | `{...}` | I/O Fault Alarm |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bypass all Bypassable Interlocks |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Check (not bypass) all Interlocks |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`OCmd_BumpClose`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để bump valve closed (avail. if Fdbk Bad) |
| **`OCmd_BumpOpen`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để bump valve open (avail. if Fdbk Bad) |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Oper Cmd to xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Oper Cmd to xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi and acknowledge all alarms |
| **`ORdy_BumpClose`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_BumpClose (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_BumpOpen`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_BumpOpen (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_CV`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OSet_CV (enables data entry field) |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`OSet_CV`** | `REAL` | `0` | [Giá trị đặt từ màn hình HMI Operator] Operator Setting of Controlled Variable (output) (CV EU) |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_ValveMP`
**Thuộc chương:** Chương 4: Van điều khiển & Cơ cấu chấp hành (Valves)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Valves 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_ValveMP`:** **90** Biến đầu vào (`Input Parameters`) | **77** Biến đầu ra (`Output Parameters`) | **38** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_ValveMP`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_OpenLS`, `Inp_ClosedLS`, `Inp_LowerLS`, `Inp_UpperLS`, `Inp_CavityInLS`, `Inp_CavityOutLS`, `Inp_PermOK`, `Inp_NBPermOK`, `Inp_IntlkOK`, `Inp_NBIntlkOK`, `Inp_IntlkAvail`, `Inp_IOFault`, `Inp_Sim`, `Inp_Hand`, `Inp_Ovrd`, `Inp_OvrdCmd`, `Inp_ExtInh`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_HasLiftLower`, `Cfg_HasLiftUpper`, `Cfg_HasSIPCavity`, `Cfg_HasSIPLower`, `Cfg_HasSIPUpper`, `Cfg_PulseLiftLower`, `Cfg_PulseLiftUpper`, `Cfg_PulseSIPLower`, `Cfg_PulseSIPUpper`, `Cfg_HasMoreObj`, `Cfg_HasPermObj`, `Cfg_HasIntlkObj`, `Cfg_HasStatsObj`, `Cfg_OCmdResets`, `Cfg_XCmdResets`, `Cfg_OvrdPermIntlk`, `Cfg_ShedOnFail`, `Cfg_ShedOnIOFault`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_FdbkStateTbl`, `Cfg_FdbkTimeTbl`, `Cfg_Label`, `Cfg_OutStateTbl`, `Cfg_FdbkReqdTbl`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_ValveMP` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PSet_Owner`, `PCmd_Close`, `PCmd_Open`, `PCmd_LiftLower`, `PCmd_LiftUpper`, `PCmd_SIPCavity`, `PCmd_SIPLower`, `PCmd_SIPUpper`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_FailAck`, `PCmd_IntlkTripAck`, `PCmd_IOFaultAck`, `PCmd_FailSuppress`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Out_Open`, `Out_Close`, `Out_LiftLower`, `Out_LiftUpper`, `Out_CavityInlet`, `Out_CavityOutlet`, `Val_Cmd`, `Val_Sts`, `Val_Fault`, `Val_State`, `Val_NotifyAll`, `Val_UnackAlmC`, `Val_Owner`, `Sts_Closed`, `Sts_Opened`, `Sts_LiftLower`, `Sts_LiftUpper`, `Sts_SIPCavity`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 90 Biến Đầu Vào (`Input Parameters`) của `P_ValveMP`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_OpenLS`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Valve Open Limit Switch |
| **`Inp_ClosedLS`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Valve Closed Limit Switch |
| **`Inp_LowerLS`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Valve Lower Seat Lift Limit Switch |
| **`Inp_UpperLS`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Valve Upper Seat Lift Limit Switch |
| **`Inp_CavityInLS`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Valve Cavity Inlet Valve Limit Switch |
| **`Inp_CavityOutLS`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Valve Cavity Outlet Valve Limit Switch |
| **`Inp_PermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Permissives OK, valve can energize |
| **`Inp_NBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Non-Bypassable Permissives OK, valve can energize |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Interlocks OK, valve can energize |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Non-Bypassable Interlocks OK, valve can energize |
| **`Inp_IntlkAvail`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Simulate working valve; 0=Open / Close / Monitor actual valve |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Input Communication Status 0=OK, 1=fail |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Simulate working valve; 0=Open / Close / Monitor actual valve |
| **`Inp_Hand`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Acquire Hand (typ. hardwired local), 0=Release Hand |
| **`Inp_Ovrd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Acquire Override (higher priority program logic), 0=Release Override |
| **`Inp_OvrdCmd`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Override mode command: 0 = None 1 = Closed 2 = Open 3 = Lift lower 4 = Lift upper 5 = SIP/CIP cavity 6 = SIP/CIP lower seat 7 = SIP/CIP upper seat |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Inhibit External Acquisition, 0=Allow External Acquisition |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Reset all fault conditions and latched Alarms |
| **`Cfg_HasLiftLower`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Valve supports Lift Lower Seat state (4) |
| **`Cfg_HasLiftUpper`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Valve supports Lift Upper Seat state (5) |
| **`Cfg_HasSIPCavity`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Valve supports SIP Cavity state (6) |
| **`Cfg_HasSIPLower`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Valve supports SIP Lower Seat state (8) |
| **`Cfg_HasSIPUpper`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Valve supports SIP Upper Seat state (10) |
| **`Cfg_PulseLiftLower`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Pulse seat lift output when doing Lift Lower |
| **`Cfg_PulseLiftUpper`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Pulse seat lift output when doing Lift Upper |
| **`Cfg_PulseSIPLower`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Pulse seat lift output when doing SIP/CIP Lower |
| **`Cfg_PulseSIPUpper`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Pulse seat lift output when doing SIP/CIP Upper |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_HasPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng permissive liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng interlock liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasStatsObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng valve statistics liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_OCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=New valve OCmd resets shed latches and cleared alarms, 0=OCmdReset req'd. |
| **`Cfg_XCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=New valve XCmd resets shed latches and cleared alarms, 0=XCmdReset req'd. |
| **`Cfg_OvrdPermIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Override ignores Bypassable Perm/ Intlk; 0=always use Perm/Intlk |
| **`Cfg_ShedOnFail`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Close valve and alarm on Position Fail. 0 = Alarm only on Fail. IMPORTANT: If a condition is configured to shed the device to the Off state on a fault, a reset is required to clear the shed fault to command the device to a state other than closed. |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Close Valve and Alarm on I/O Fault. 0 = Alarm only on I/O Fault. IMPORTANT: If a condition is configured to shed the device to the Off state on a fault, a reset is required to clear the shed fault to command the device to a state other than closed. |
| **`Cfg_HasFailAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_FailResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_FailAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_FailSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the severity of each alarm that gauges the color and symbol that are used to indicate alarm status on the faceplate and global object. The following are valid values: 1…250 = Low 251…500 = Medium 501…750 = High 751…1000 = Urgent IMPORTANT: For FactoryTalk View software version 7.0, these severity priorities drive only the indication on the global object and faceplate. The Alarm & Events definition severity drives the color and symbol that is used on the alarm banner and alarm summary as well as the value returned by the FactoryTalk Alarm and Events software display commands. |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the severity of each alarm that gauges the color and symbol that are used to indicate alarm status on the faceplate and global object. The following are valid values: 1…250 = Low 251…500 = Medium 501…750 = High 751…1000 = Urgent IMPORTANT: For FactoryTalk View software version 7.0, these severity priorities drive only the indication on the global object and faceplate. The Alarm & Events definition severity drives the color and symbol that is used on the alarm banner and alarm summary as well as the value returned by the FactoryTalk Alarm and Events software display commands. |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the severity of each alarm that gauges the color and symbol that are used to indicate alarm status on the faceplate and global object. The following are valid values: 1…250 = Low 251…500 = Medium 501…750 = High 751…1000 = Urgent IMPORTANT: For FactoryTalk View software version 7.0, these severity priorities drive only the indication on the global object and faceplate. The Alarm & Events definition severity drives the color and symbol that is used on the alarm banner and alarm summary as well as the value returned by the FactoryTalk Alarm and Events software display commands. |
| **`Cfg_PulseOpenT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Time to lift seat when pulsing seat for cleaning (sec) |
| **`Cfg_PulseCloseT`** | `REAL` | `5` | Ẩn (Tag) | [Biến cấu hình thông số] Time to close seat when pulsing seat for cleaning (sec) |
| **`Cfg_FailT`** | `REAL` | `3` | Ẩn (Tag) | [Biến cấu hình thông số] Time to give valve to achieve state before declaring valve failure (sec) |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Mã định danh chủ sở hữu chương trình (Program Owner ID: khác 0 để đăng ký chiếm quyền, bằng 0 để nhả quyền). |
| **`PCmd_Close`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Close Valve |
| **`PCmd_Open`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Open Valve |
| **`PCmd_LiftLower`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Lift Lower Seat for cleaning |
| **`PCmd_LiftUpper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Lift Upper Seat for cleaning |
| **`PCmd_SIPCavity`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để SIP valve leakage cavity |
| **`PCmd_SIPLower`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để SIP through Lower Seat |
| **`PCmd_SIPUpper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để SIP through Upper Seat |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Program (Oper to Prog) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Operator (Prog to Oper) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa cứng chế độ Program (không cho phép Operator giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Unlock Program (allow Oper to acquire) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Normal (Oper or Prog) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Reset all Alarms requiring Reset |
| **`PCmd_FailAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_FailSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Valve Failure Alarm |
| **`PCmd_FailUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Valve Failure Alarm |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Interlock Trip Alarm |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Interlock Trip Alarm |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) I/O Fault Alarm |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) I/O Fault Alarm |
| **`PCmd_FailUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Release (Maint to Oper/Prog/Ovrd) |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Unlock Operator (allow Prog to acquire) |
| **`XCmd_Close`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Close Valve |
| **`XCmd_Open`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Open Valve |
| **`XCmd_LiftLower`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Lift Lower Seat for cleaning |
| **`XCmd_LiftUpper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Lift Upper Seat for cleaning |
| **`XCmd_SIPCavity`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để SIP valve leakage cavity |
| **`XCmd_SIPLower`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để SIP through Lower Seat |
| **`XCmd_SIPUpper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để SIP through Upper Seat |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Acquire (Oper/Prog to Ext) |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Release (Ext to Oper/Prog) |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Reset all Alarms requiring Reset |

## 3. Bảng chi tiết 77 Biến Đầu Ra (`Output Parameters`) của `P_ValveMP`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Out_Open`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output to Open Valve |
| **`Out_Close`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output to Close Valve |
| **`Out_LiftLower`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output to Lift Lower Seat for cleaning |
| **`Out_LiftUpper`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output to Lift Upper Seat for Cleaning |
| **`Out_CavityInlet`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output to Cavity Inlet Valve (usually FC) |
| **`Out_CavityOutlet`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output to Cavity Outlet Valve (usually FO) |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final valve status source and quality. GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`Val_Cmd`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Command 0=None 1=Close 3=Open 4=LiftLwr 5=LiftUpr 6=ClnCav 8=ClnLwr 10=ClnUpr |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Confirmed Sts 0=? 1=Close 3=Open 4=LiftL 5=LiftU 6=ClnCav 8=ClnL 10=ClnU 11=Mov 12=PlsL 13=PlsU |
| **`Val_Fault`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Fault Status 0=None, 16=Position Fail, 32=I/O Fault, 34=Config Error |
| **`Val_State`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Actual State Number in state diagram (see documentation) |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Current Object Owner ID (0=not owned) |
| **`Sts_Closed`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Valve requested to close and is confirmed Closed |
| **`Sts_Opened`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Valve requested to open and is confirmed Opened |
| **`Sts_LiftLower`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Valve Lower Seat Lift position achieved |
| **`Sts_LiftUpper`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Valve Upper Seat Lift position achieved |
| **`Sts_SIPCavity`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Valve Cavity SIP position achieved |
| **`Sts_SIPLower`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Valve Lower Seat SIP position achieved |
| **`Sts_SIPUpper`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Valve Upper Seat SIP position achieved |
| **`Sts_Moving`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Valve moving to target position, not yet achieved |
| **`Sts_Pulsing`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Valve pulsing seat for cleaning |
| **`Sts_Available`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Valve available for control by automation (Prog) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Bypassable Interlocks and Permissives are Bypassed |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Bypassing Active (Bypassed or Maintenance) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device Not Ready, see detail bits for reason |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Lỗi thiết bị ngắt tải (yêu cầu nhấn Reset) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Điều kiện Khóa liên động không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Lỗi truyền thông I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Device Out of Service |
| **`Nrdy_Perm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Điều kiện Cho phép khởi động không thỏa mãn (Permissive Not OK) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=An Alarm is Disabled or Shelved, display icon |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Error in Config: see detail bits for reason |
| **`Err_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Invalid Failure Time (use 0 to 2147483) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Invalid Fdbk Time or Pulse Time (use 0 to 2147483 sec) |
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
| **`Sts_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Valve Failure Status (0=OK, 1=Bad) |
| **`Alm_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Valve Failure Alarm |
| **`Ack_Fail`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Valve Failure Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_FailDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Valve Failure Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_FailShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Valve Failure Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_FailSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Valve Failure Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Valve de-energized by an Interlock NOT OK |
| **`Alm_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Alarm: Valve de-energized by an Interlock NOT OK |
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
| **`P_ValveMP`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 38 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_ValveMP`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'Mix Proof Valve'` | [Biến cấu hình thông số] Description for display on HMI. This string is shown in the title bar of the faceplate. |
| **`Cfg_FdbkStateTbl`** | `SINT[11]` | `{...}` | [Biến cấu hình thông số] The bits within a word indicate an input or output state (1=on, 0=off) or if the input state is checked (FdbkReqd = 1 = input checked, 0 = not checked). The bit positions are: Bit Function Input Output Bit .0 Close valve Inp_ClosedLS Out_Close Bit .1 Open Valve Inp_OpenLS Out_Open Bit .2 Lift Lower Seat Inp_LowerLS Out_LiftLower Bit .3 Lift Upper Seat Inp_UpperLS Out_LiftUpper Bit .4 Open Cavity Inlet Inp_CavityInLS Out_CavityInlet (FC) Bit .5 Close Cavity Outlet Inp_CabityOutLS Out_CavityOutlet (FO) |
| **`Cfg_FdbkTimeTbl`** | `REAL[11]` | `{...}` | [Biến cấu hình thông số] Table of Times the Feedback must match to achieve valve State |
| **`Cfg_Label`** | `STRING_20` | `'Valve Control'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI. This string appears on the graphic symbol. |
| **`Cfg_OutStateTbl`** | `SINT[11]` | `{...}` | [Biến cấu hình thông số] Valve state tables showing, respectively, output states in given valve state, feedback inputs required (checked) to achieve valve state, and feedback input states to achieve valve state. The array index ([0]…[10]) indicates the device state as follows (per the state diagram in the manual): Index/Function [0] (intermediate state) De-energized [1] Closed [2] (intermediate state) Close cavity outlet [3] Opened [4] Lift Lower Seat [5] Lift Upper Seat [6] SIP/CIP Cavity [7] (intermediate) Lift Lower for SIP/CIP [8] SIP/CIP Lower Seat [9] (intermediate) Lift Upper for SIP/CIP [10] SIP/CIP Upper Seat |
| **`Cfg_FdbkReqdTbl`** | `SINT[11]` | `{...}` | [Biến cấu hình thông số] Valve state tables showing, respectively, output states in given valve state, feedback inputs required (checked) to achieve valve state, and feedback input states to achieve valve state. The array index ([0]…[10]) indicates the device state as follows (per the state diagram in the manual): Index/Function [0] (intermediate state) De-energized [1] Closed [2] (intermediate state) Close cavity outlet [3] Opened [4] Lift Lower Seat [5] Lift Upper Seat [6] SIP/CIP Cavity [7] (intermediate) Lift Lower for SIP/CIP [8] SIP/CIP Lower Seat [9] (intermediate) Lift Upper for SIP/CIP [10] SIP/CIP Upper Seat |
| **`Cfg_Tag`** | `STRING_20` | `'P_ValveMP'` | [Biến cấu hình thông số] Tagname for display on HMI. This string is shown in the title bar of the faceplate.. |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Command Source |
| **`Fail`** | `P_Alarm` | `{...}` | Valve Failure Alarm |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_ValveMP'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | Interlock Trip Alarm |
| **`IOFault`** | `P_Alarm` | `{...}` | I/O Fault Alarm |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bypass all Bypassable Interlocks and Permissives |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Check (not bypass) all Interlocks and Permissives |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Bypass (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Check (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`OCmd_Close`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Close Valve |
| **`OCmd_LiftLower`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Lift Lower Seat for cleaning |
| **`OCmd_LiftUpper`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Lift Upper Seat for cleaning |
| **`OCmd_Open`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Open Valve |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset all Alarms requiring Reset |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset all Alarms and latched Shed conditions |
| **`OCmd_SIPCavity`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để SIP valve leakage cavity |
| **`OCmd_SIPLower`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để SIP through Lower Seat |
| **`OCmd_SIPUpper`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để SIP through Upper Seat |
| **`ORdy_Close`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Close (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_LiftLower`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_LiftLower (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_LiftUpper`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_LiftUpper (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Open`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Open (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_SIPCavity`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_SIPCavity (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_SIPLower`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_SIPLower (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_SIPUpper`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_SIPUpper (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_ValveMPAdv`
**Thuộc chương:** Chương 4: Van điều khiển & Cơ cấu chấp hành (Valves)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Valves 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_ValveMPAdv`:** **103** Biến đầu vào (`Input Parameters`) | **85** Biến đầu ra (`Output Parameters`) | **51** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_ValveMPAdv`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_OpenLS`, `Inp_ClosedLS`, `Inp_LowerLS`, `Inp_UpperLS`, `Inp_CavityInLS`, `Inp_CavityOutLS`, `Inp_OpenIntlkOK`, `Inp_OpenNBIntlkOK`, `Inp_OpenIntlkAvail`, `Inp_LowerSeatIntlkOK`, `Inp_LowerSeatNBIntlkOK`, `Inp_LowerSeatIntlkAvail`, `Inp_UpperSeatIntlkOK`, `Inp_UpperSeatNBIntlkOK`, `Inp_UpperSeatIntlkAvail`, `Inp_CavityIntlkOK`, `Inp_CavityNBIntlkOK`, `Inp_CavityIntlkAvail`...
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_HasLiftLower`, `Cfg_HasLiftUpper`, `Cfg_HasSIPCavity`, `Cfg_HasSIPLower`, `Cfg_HasSIPUpper`, `Cfg_PulseLiftLower`, `Cfg_PulseLiftUpper`, `Cfg_PulseSIPLower`, `Cfg_PulseSIPUpper`, `Cfg_HasStatsObj`, `Cfg_HasMoreObj`, `Cfg_HasOpenIntlkObj`, `Cfg_HasLowerSeatIntlkObj`, `Cfg_HasUpperSeatIntlkObj`, `Cfg_HasCavityIntlkObj`, `Cfg_OperClosePrio`, `Cfg_ExtClosePrio`, `Cfg_OCmdResets`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_FdbkReqdTbl`, `Cfg_OutStateTbl`, `Cfg_FdbkStateTbl`, `Cfg_FdbkStateTbl[0]`, `Cfg_FdbkStateTbl[1]`, `Cfg_FdbkStateTbl[2]`, `Cfg_FdbkStateTbl[3]`, `Cfg_FdbkStateTbl[4]`, `Cfg_FdbkStateTbl[5]`, `Cfg_FdbkStateTbl[6]`, `Cfg_FdbkStateTbl[7]`, `Cfg_FdbkStateTbl[8]`, `Cfg_FdbkStateTbl[9]`, `Cfg_FdbkStateTbl[10]`, `Cfg_FdbkTimeTbl`, `Cfg_Label`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_ValveMPAdv` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PSet_Owner`, `PCmd_Close`, `PCmd_Open`, `PCmd_LiftLower`, `PCmd_LiftUpper`, `PCmd_SIPCavity`, `PCmd_SIPLower`, `PCmd_SIPUpper`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_FailAck`, `PCmd_IntlkTripAck`, `PCmd_IOFaultAck`, `PCmd_FailSuppress`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Out_Open`, `Out_Close`, `Out_LiftLower`, `Out_LiftUpper`, `Out_CavityInlet`, `Out_CavityOutlet`, `Out_Horn`, `Out_Locator`, `Val_Cmd`, `Val_Sts`, `Val_Fault`, `Val_State`, `Val_NotifyAll`, `Val_UnackAlmC`, `Val_Owner`, `Sts_Closed`, `Sts_Opened`, `Sts_LiftLower`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 103 Biến Đầu Vào (`Input Parameters`) của `P_ValveMPAdv`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_OpenLS`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Valve Open Limit Switch |
| **`Inp_ClosedLS`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Valve Closed Limit Switch |
| **`Inp_LowerLS`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Valve Lower Seat Lift Limit Switch |
| **`Inp_UpperLS`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Valve Upper Seat Lift Limit Switch |
| **`Inp_CavityInLS`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Valve Cavity Inlet Valve Limit Switch |
| **`Inp_CavityOutLS`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Valve Cavity Outlet Valve Limit Switch |
| **`Inp_OpenIntlkOK`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Interlocks OK, valve can Open, 0 = Interlocks not OK, Close valve if target/actual state is Open |
| **`Inp_OpenNBIntlkOK`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Non-Bypassable Interlocks OK, valve can Open, 0 = Not OK, Close valve if target/actual state is Open |
| **`Inp_OpenIntlkAvail`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Open Interlock Availability OK. |
| **`Inp_LowerSeatIntlkOK`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Interlocks OK, valve can lift or clean lower seat, 0 = Interlocks not OK, Close valve if target/actual state has lower seat lifted |
| **`Inp_LowerSeatNBIntlkOK`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Non-Bypassable Interlocks OK, valve can lift or clean lower seat, 0 = Not OK, Close valve if target/actual state has lower seat lifted |
| **`Inp_LowerSeatIntlkAvail`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Lower Seat Interlock Availability OK. |
| **`Inp_UpperSeatIntlkOK`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Interlocks OK, valve can lift or clean upper seat, 0 = Interlocks not OK, Close valve if target/actual state has upper seat lifted |
| **`Inp_UpperSeatNBIntlkOK`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Non-Bypassable Interlocks OK, valve can lift or clean upper seat, 0 = Not OK, Close valve if target/actual state has upper seat lifted |
| **`Inp_UpperSeatIntlkAvail`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Upper Seat Interlock Availability OK. |
| **`Inp_CavityIntlkOK`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Interlocks OK, valve can clean cavity or seat, 0 = Interlocks not OK, Close valve if target/actual state has cleaning via cavity |
| **`Inp_CavityNBIntlkOK`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Non-Bypassable Interlocks OK, valve can clean cavity or seat, 0 = Not OK, Close valve if target/actual state has cleaning via cavity |
| **`Inp_CavityIntlkAvail`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Cavity Interlock Availability OK. |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Input Communication Status 0=OK, 1=fail |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Simulate working valve; 0=Open / Close / Monitor actual valve |
| **`Inp_Hand`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Acquire Hand (typ. hardwired local), 0=Release Hand |
| **`Inp_Ovrd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Acquire Override (higher priority program logic), 0=Release Override |
| **`Inp_OvrdCmd`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Override mode command: 0 = None 1 = Closed 2 = Open 3 = Lift lower 4 = Lift upper 5 = SIP/CIP cavity 6 = SIP/CIP lower seat 7 = SIP/CIP upper seat |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Inhibit External Acquisition, 0=Allow External Acquisition |
| **`Inp_HornInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Inhibit audible alert, 0 = Allow audible alert. |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Reset all fault conditions and latched Alarms |
| **`Cfg_HasLiftLower`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Valve supports Lift Lower Seat state (4) |
| **`Cfg_HasLiftUpper`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Valve supports Lift Upper Seat state (5) |
| **`Cfg_HasSIPCavity`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Valve supports SIP Cavity state (6) |
| **`Cfg_HasSIPLower`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Valve supports SIP Lower Seat state (8) |
| **`Cfg_HasSIPUpper`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Valve supports SIP Upper Seat state (10) |
| **`Cfg_PulseLiftLower`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Pulse seat lift output when doing Lift Lower |
| **`Cfg_PulseLiftUpper`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Pulse seat lift output when doing Lift Upper |
| **`Cfg_PulseSIPLower`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Pulse seat lift output when doing SIP/CIP Lower |
| **`Cfg_PulseSIPUpper`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Pulse seat lift output when doing SIP/CIP Upper |
| **`Cfg_HasStatsObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng valve statistics liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_HasOpenIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Tells HMI an object is connected to Inp_OpenIntlkOK, Inp_OpenNBIntlkOK inputs |
| **`Cfg_HasLowerSeatIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Tells HMI an object is connected to Inp_LowerSeatIntlkOK, Inp_LowerSeatNBIntlkOK inputs |
| **`Cfg_HasUpperSeatIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Tells HMI an object is connected to Inp_UpperSeatIntlkOK, Inp_UpperSeatNBIntlkOK Inputs |
| **`Cfg_HasCavityIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Tells HMI an object is connected to Inp_CavityIntlkOK, Inp_CavityNBIntlkOK inputs |
| **`Cfg_OperClosePrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = OCmd_Close accepted any time; 0 = OCmd_Close accepted only when Oper đang được chọn làm Nguồn lệnh điều khiển hiện hành. |
| **`Cfg_ExtClosePrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = XCmd_Close accepted any time; 0 = XCmd_Close accepted only when Ext đang được chọn làm Nguồn lệnh điều khiển hiện hành. |
| **`Cfg_OCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=New valve OCmd resets shed latches and cleared alarms, 0=OCmdReset req'd. |
| **`Cfg_XCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=New valve XCmd resets shed latches and cleared alarms, 0=XCmdReset req'd. |
| **`Cfg_OvrdPermIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Override ignores Bypassable Perm/ Intlk; 0=always use Perm/Intlk |
| **`Cfg_ShedOnFail`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Close valve and alarm on Position Fail. 0 = Alarm only on Fail. IMPORTANT: If a condition is configured to shed the device to the Off state on a fault, a reset is required to clear the shed fault to command the device to a state other than closed. |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Close Valve and Alarm on I/O Fault. 0 = Alarm only on I/O Fault. IMPORTANT: If a condition is configured to shed the device to the Off state on a fault, a reset is required to clear the shed fault to command the device to a state other than closed. |
| **`Cfg_HasFailAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_FailResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_FailAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_FailSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the severity of each alarm that gauges the color and symbol that are used to indicate alarm status on the faceplate and global object. The following are valid values: 1…250 = Low 251…500 = Medium 501…750 = High 751…1000 = Urgent IMPORTANT: For FactoryTalk View software version 7.0, these severity priorities drive only the indication on the global object and faceplate. The Alarm & Events definition severity drives the color and symbol that is used on the alarm banner and alarm summary as well as the value returned by the FactoryTalk Alarm and Events software display commands. |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the severity of each alarm that gauges the color and symbol that are used to indicate alarm status on the faceplate and global object. The following are valid values: 1…250 = Low 251…500 = Medium 501…750 = High 751…1000 = Urgent IMPORTANT: For FactoryTalk View software version 7.0, these severity priorities drive only the indication on the global object and faceplate. The Alarm & Events definition severity drives the color and symbol that is used on the alarm banner and alarm summary as well as the value returned by the FactoryTalk Alarm and Events software display commands. |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the severity of each alarm that gauges the color and symbol that are used to indicate alarm status on the faceplate and global object. The following are valid values: 1…250 = Low 251…500 = Medium 501…750 = High 751…1000 = Urgent IMPORTANT: For FactoryTalk View software version 7.0, these severity priorities drive only the indication on the global object and faceplate. The Alarm & Events definition severity drives the color and symbol that is used on the alarm banner and alarm summary as well as the value returned by the FactoryTalk Alarm and Events software display commands. |
| **`Cfg_PulseOpenT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Time to lift seat when pulsing seat for cleaning (sec) |
| **`Cfg_PulseCloseT`** | `REAL` | `5` | Ẩn (Tag) | [Biến cấu hình thông số] Time to close seat when pulsing seat for cleaning (sec) |
| **`Cfg_FailT`** | `REAL` | `3` | Ẩn (Tag) | [Biến cấu hình thông số] Time to give valve to achieve state before declaring valve failure (sec) |
| **`Cfg_StartHornT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Time in seconds to sound audible on command to move from the closed position, valid = 0.0 to 1000.0 seconds, 0.0 = disabled. |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Mã định danh chủ sở hữu chương trình (Program Owner ID: khác 0 để đăng ký chiếm quyền, bằng 0 để nhả quyền). |
| **`PCmd_Close`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Close Valve |
| **`PCmd_Open`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Open Valve |
| **`PCmd_LiftLower`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Lift Lower Seat for cleaning |
| **`PCmd_LiftUpper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Lift Upper Seat for cleaning |
| **`PCmd_SIPCavity`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để SIP valve leakage cavity |
| **`PCmd_SIPLower`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để SIP through Lower Seat |
| **`PCmd_SIPUpper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để SIP through Upper Seat |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Program (Oper to Prog) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Operator (Prog to Oper) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa cứng chế độ Program (không cho phép Operator giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Unlock Program (allow Oper to acquire) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để select Normal (Oper or Prog) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Reset all Alarms requiring Reset |
| **`PCmd_FailAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_FailSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Valve Failure Alarm |
| **`PCmd_FailUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Valve Failure Alarm |
| **`PCmd_FailUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Interlock Trip Alarm |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Interlock Trip Alarm |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) I/O Fault Alarm |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) I/O Fault Alarm |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Release (Maint to Oper/Prog/Ovrd) |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Unlock Operator (allow Prog to acquire) |
| **`XCmd_Close`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Close Valve |
| **`XCmd_Open`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Open Valve |
| **`XCmd_LiftLower`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Lift Lower Seat for cleaning |
| **`XCmd_LiftUpper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Lift Upper Seat for cleaning |
| **`XCmd_SIPCavity`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để SIP valve leakage cavity |
| **`XCmd_SIPLower`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để SIP through Lower Seat |
| **`XCmd_SIPUpper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để SIP through Upper Seat |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Acquire |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Release |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Reset all Alarms requiring Reset |

## 3. Bảng chi tiết 85 Biến Đầu Ra (`Output Parameters`) của `P_ValveMPAdv`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Out_Open`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output to Open Valve |
| **`Out_Close`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output to Close Valve, 1 = Close. |
| **`Out_LiftLower`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output to Lift lower valve seat, 1 = Lift. |
| **`Out_LiftUpper`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output to Lift upper valve seat, 1 = Lift. |
| **`Out_CavityInlet`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output to Open leakage cavity cleaning inlet, 1 = Open. |
| **`Out_CavityOutlet`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output to Close leakage cavity drain / cleaning outlet, 1 = Close. |
| **`Out_Horn`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output to Horn, 1 = Sound audible prior to commanded state change. |
| **`Out_Locator`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output to Valve Locator lamp / beacon, 1 = Illuminated. |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final valve status source and quality. GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`Val_Cmd`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Command 0=None 1=Close 3=Open 4=LiftLwr 5=LiftUpr 6=ClnCav 8=ClnLwr 10=ClnUpr |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Confirmed Sts 0=? 1=Close 3=Open 4=LiftL 5=LiftU 6=ClnCav 8=ClnL 10=ClnU 11=Mov 12=PlsL 13=PlsU 14=Horn |
| **`Val_Fault`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Fault Status 0=None, 16=Position Fail, 32=I/O Fault, 34=Config Error |
| **`Val_State`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Actual State Number in state diagram (see documentation) |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Current Object Owner ID (0=not owned) |
| **`Sts_Closed`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Valve requested to close and is confirmed Closed |
| **`Sts_Opened`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Valve requested to open and is confirmed Opened |
| **`Sts_LiftLower`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Valve Lower Seat Lift position achieved |
| **`Sts_LiftUpper`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Valve Upper Seat Lift position achieved |
| **`Sts_SIPCavity`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Valve Cavity SIP position achieved |
| **`Sts_SIPLower`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Valve Lower Seat SIP position achieved |
| **`Sts_SIPUpper`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Valve Upper Seat SIP position achieved |
| **`Sts_Locator`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Locator / beacon for identifying valve is active. |
| **`Sts_Horn`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Audible alert (horn) is active. |
| **`Sts_Moving`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Valve moving to target position, not yet achieved |
| **`Sts_Pulsing`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Valve pulsing seat for cleaning |
| **`Sts_Available`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Valve available for control by automation (Prog) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Bypassable Interlocks and Permissives are Bypassed |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Bypassing Active (Bypassed or Maintenance) |
| **`Sts_CmdConflict`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Conflicting commands received this scan. |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device Not Ready, see detail bits for reason |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Lỗi thiết bị ngắt tải (yêu cầu nhấn Reset) |
| **`Nrdy_OpenIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Open Interlock not OK. Valve cannot open, and if open, valve will close. |
| **`Nrdy_LowerSeatIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Lower Seat Interlock not OK. Valve cannot life or clean lower seat, and if seat lifted, valve will close. |
| **`Nrdy_UpperSeatIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Upper Seat Interlock not OK. Valve cannot lift or clean upper seat, and if seat lifted, valve will close. |
| **`Nrdy_CavityIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Cavity Interlock not OK. Valve cannot clean cavity or seat, and if cleaning, valve will close. |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Lỗi truyền thông I/O (yêu cầu nhấn Reset) |
| **`Nrdy_PrioClose`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Operator or External priority Close command requires reset. |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1=Device Not Ready: Device Out of Service |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=An Alarm is Disabled or Shelved, display icon |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Error in Config: see detail bits for reason |
| **`Err_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Invalid Failure Time (use 0 to 2147483) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Invalid Fdbk Time or Pulse Time (use 0 to 2147483 sec) |
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
| **`Sts_ExtReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=External request inhibited |
| **`Sts_ProgReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Program request inhibited, cannot Get to Program from Current State |
| **`Sts_MAcqRcvd`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Maintenance Acquire command received this scan |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc lỗi ngắt tải (Shed) đã hết điều kiện lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Valve Failure Status (0=OK, 1=Bad) |
| **`Alm_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Valve Failure Alarm |
| **`Ack_Fail`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1=Valve Failure Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_FailDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Valve Failure Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_FailShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Valve Failure Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_FailSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Valve Failure Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Valve de-energized by an Interlock NOT OK |
| **`Alm_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1=Alarm: Valve de-energized by an Interlock NOT OK |
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
| **`P_ValveMPAdv`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 51 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_ValveMPAdv`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'Advanced Mix Proof Valve'` | [Biến cấu hình thông số] Description for display on HMI |
| **`Cfg_FdbkReqdTbl`** | `SINT[11]` | `{...}` | [Biến cấu hình thông số] Valve state tables showing, respectively, output states in given valve state, feedback inputs required (checked) to achieve valve state, and feedback input states to achieve valve state. The array index ([0]…[10]) indicates the device state as follows (per the state diagram in the manual): Index/Function [0] (intermediate state) De-energized [1] Closed [2] (intermediate state) Close cavity outlet [3] Opened [4] Lift Lower Seat [5] Lift Upper Seat [6] SIP/CIP Cavity [7] (intermediate) Lift Lower for SIP/CIP [8] SIP/CIP Lower Seat [9] (intermediate) Lift Upper for SIP/CIP [10] SIP/CIP Upper Seat |
| **`Cfg_OutStateTbl`** | `SINT[11]` | `{...}` | [Biến cấu hình thông số] Valve state tables showing, respectively, output states in given valve state, feedback inputs required (checked) to achieve valve state, and feedback input states to achieve valve state. The array index ([0]…[10]) indicates the device state as follows (per the state diagram in the manual): Index/Function [0] (intermediate state) De-energized [1] Closed [2] (intermediate state) Close cavity outlet [3] Opened [4] Lift Lower Seat [5] Lift Upper Seat [6] SIP/CIP Cavity [7] (intermediate) Lift Lower for SIP/CIP [8] SIP/CIP Lower Seat [9] (intermediate) Lift Upper for SIP/CIP [10] SIP/CIP Upper Seat |
| **`Cfg_FdbkStateTbl`** | `SINT[11]` | `{...}` | [Biến cấu hình thông số] The bits within a word indicate an input or output state (1=on, 0=off) or if the input state is checked (FdbkReqd = 1 = input checked, 0 = not checked). The bit positions are: Bit Function Input Output Bit .0 Close valve Inp_ClosedLS Out_Close Bit .1 Open Valve Inp_OpenLS Out_Open Bit .2 Lift Lower Seat Inp_LowerLS Out_LiftLower Bit .3 Lift Upper Seat Inp_UpperLS Out_LiftUpper Bit .4 Open Cavity Inlet Inp_CavityInLS Out_CavityInlet (FC) Bit .5 Close Cavity Outlet Inp_CabityOutLS Out_CavityOutlet (FO) |
| **`Cfg_FdbkStateTbl[0]`** | `SINT` | `2#0000_0001` | [Biến cấu hình thông số] Table of Feedback Input states to achieve valve State |
| **`Cfg_FdbkStateTbl[1]`** | `SINT` | `2#0000_0001` | [Biến cấu hình thông số] Table of Feedback Input states to achieve valve State |
| **`Cfg_FdbkStateTbl[2]`** | `SINT` | `2#0010_0000` | [Biến cấu hình thông số] Table of Feedback Input states to achieve valve State |
| **`Cfg_FdbkStateTbl[3]`** | `SINT` | `2#0010_0010` | [Biến cấu hình thông số] Table of Feedback Input states to achieve valve State |
| **`Cfg_FdbkStateTbl[4]`** | `SINT` | `2#0000_0100` | [Biến cấu hình thông số] Table of Feedback Input states to achieve valve State |
| **`Cfg_FdbkStateTbl[5]`** | `SINT` | `2#0000_1000` | [Biến cấu hình thông số] Table of Feedback Input states to achieve valve State |
| **`Cfg_FdbkStateTbl[6]`** | `SINT` | `2#0001_0000` | [Biến cấu hình thông số] Table of Feedback Input states to achieve valve State |
| **`Cfg_FdbkStateTbl[7]`** | `SINT` | `2#0001_0100` | [Biến cấu hình thông số] Table of Feedback Input states to achieve valve State |
| **`Cfg_FdbkStateTbl[8]`** | `SINT` | `2#0011_0100` | [Biến cấu hình thông số] Table of Feedback Input states to achieve valve State |
| **`Cfg_FdbkStateTbl[9]`** | `SINT` | `2#0001_1000` | [Biến cấu hình thông số] Table of Feedback Input states to achieve valve State |
| **`Cfg_FdbkStateTbl[10]`** | `SINT` | `2#0011_1000` | [Biến cấu hình thông số] Table of Feedback Input states to achieve valve State |
| **`Cfg_FdbkTimeTbl`** | `REAL[11]` | `{...}` | [Biến cấu hình thông số] Table of Times the Feedback must match to achieve valve State |
| **`Cfg_Label`** | `STRING_20` | `'Valve Control'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI. This string appears on the graphic symbol. |
| **`Cfg_Tag`** | `STRING_20` | `'P_ValveMP'` | [Biến cấu hình thông số] Tagname for display on HMI |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Command Source |
| **`Fail`** | `P_Alarm` | `{...}` | Valve Failure Alarm |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_ValveMPAdv'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | Interlock Trip Alarm |
| **`IOFault`** | `P_Alarm` | `{...}` | I/O Fault Alarm |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bypass all Bypassable Interlocks and Permissives |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Check (not bypass) all Interlocks and Permissives |
| **`MCmd_ToggleLocator`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để toggle on/off valve locator / beacon. |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Bypass (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Check (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_ToggleLocator`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_ToggleLocator, enable HMI button. |
| **`OCmd_Close`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Close Valve |
| **`OCmd_LiftLower`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Lift Lower Seat for cleaning |
| **`OCmd_LiftUpper`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Lift Upper Seat for cleaning |
| **`OCmd_Open`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Open Valve |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset all Alarms requiring Reset |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset all Alarms and latched Shed conditions |
| **`OCmd_SIPCavity`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để SIP valve leakage cavity |
| **`OCmd_SIPLower`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để SIP through Lower Seat |
| **`OCmd_SIPUpper`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để SIP through Upper Seat |
| **`ORdy_Close`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Close (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_LiftLower`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_LiftLower (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_LiftUpper`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_LiftUpper (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Open`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Open (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_SIPCavity`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_SIPCavity (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_SIPLower`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_SIPLower (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_SIPUpper`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_SIPUpper (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_ValveStats`
**Thuộc chương:** Chương 4: Van điều khiển & Cơ cấu chấp hành (Valves)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Valves 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_ValveStats`:** **18** Biến đầu vào (`Input Parameters`) | **34** Biến đầu ra (`Output Parameters`) | **8** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_ValveStats`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_Closed`, `Inp_Opened`, `Inp_StopOther`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_HasMoreObj`, `Cfg_HasStopOther`, `Cfg_SlowOpenT`, `Cfg_SlowCloseT`
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_Label`, `Cfg_StopOtherDesc`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_ValveStats` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PCmd_ClearTotTimes`, `PCmd_ClearMaxTimes`, `PCmd_ClearStrokeCounts`, `PCmd_ClearSlowCounts`, `PCmd_ClearMAvgs`
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Val_CurrClosedT`, `Val_LastClosedT`, `Val_TotClosedT`, `Val_MaxClosedT`, `Val_CurrOpeningT`, `Val_LastOpeningT`, `Val_TotOpeningT`, `Val_MaxOpeningT`, `Val_MAvgOpeningT`, `Val_CurrOpenedT`, `Val_LastOpenedT`, `Val_TotOpenedT`, `Val_MaxOpenedT`, `Val_CurrClosingT`, `Val_LastClosingT`, `Val_TotClosingT`, `Val_MaxClosingT`, `Val_MAvgClosingT`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 18 Biến Đầu Vào (`Input Parameters`) của `P_ValveStats`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_Closed`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Valve is confirmed Closed (from valve instruction) |
| **`Inp_Opened`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Valve is confirmed Opened (from valve instruction) |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Inp_StopOther`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1=Valve is confirmed Stopped / Other (from valve instruction) |
| **`Cfg_HasStopOther`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Valve has Stopped or other state(s) to be monitored |
| **`Cfg_SlowOpenT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Maximum Time Opening (sec) before raising Sts_SlowOpen |
| **`Cfg_SlowCloseT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Maximum Time Closing (sec) before raising Sts_SlowClose |
| **`PCmd_ClearTotTimes`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Clear Total Time statistics |
| **`PCmd_ClearMaxTimes`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Clear Maximum Time statistics |
| **`PCmd_ClearStrokeCounts`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Clear Stroke Count statistics |
| **`PCmd_ClearSlowCounts`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Clear Valve Slow Count statistics |
| **`PCmd_ClearMAvgs`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Clear Moving Average Stroke Times |
| **`MCmd_ClearTotTimes`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Clear Total Time statistics |
| **`MCmd_ClearMaxTimes`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Clear Maximum Time statistics |
| **`MCmd_ClearStrokeCounts`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Clear Stroke Count statistics |
| **`MCmd_ClearSlowCounts`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Maintenance Cmmand to Clear Valve Slow Count statistics |
| **`MCmd_ClearMAvgs`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Clear Moving Average Stroke Times |

## 3. Bảng chi tiết 34 Biến Đầu Ra (`Output Parameters`) của `P_ValveStats`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Val_CurrClosedT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Current time in Closed state (sec) |
| **`Val_LastClosedT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Time in Closed state (sec) last time valve was closed |
| **`Val_TotClosedT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Accumulated time in Closed state (hours) |
| **`Val_MaxClosedT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Maximum time in Closed state (hours) of any occurrence |
| **`Val_CurrOpeningT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Current time in Opening state (sec) |
| **`Val_LastOpeningT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Time in Opening state (sec) last time valve was opening |
| **`Val_TotOpeningT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Accumulated time in Opening state (hours) |
| **`Val_MaxOpeningT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Maximum time in Opening state (sec) of any occurrence |
| **`Val_MAvgOpeningT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Moving average Open stroke time, last 10 complete open strokes (sec) |
| **`Val_CurrOpenedT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Current time in Opened state (sec) |
| **`Val_LastOpenedT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Time in Opened state (sec) last time valve was opened |
| **`Val_TotOpenedT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Accumulated time in Opened state (hours) |
| **`Val_MaxOpenedT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Maximum time in Opened state (hours) of any occurrence |
| **`Val_CurrClosingT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Current time in Closing state (sec) |
| **`Val_LastClosingT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Time in Closing state (sec) last time valve was closing |
| **`Val_TotClosingT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Accumulated time in Closing state (hours) |
| **`Val_MaxClosingT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Maximum time in Closing state (sec) of any occurrence |
| **`Val_MAvgClosingT`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Moving average Clsoe stroke time, last 10 complete close strokes (sec) |
| **`Val_CurrStopOtherT`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Current time in Stopped / Other state (sec) |
| **`Val_LastStopOtherT`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Time in Stopped / Other state (sec) last time valve was stopped (or other) |
| **`Val_TotStopOtherT`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Accumulated time in Stopped / Other state (hours) |
| **`Val_MaxStopOtherT`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Maximum time in Stopped / Other state (hours) of any occurrence |
| **`Val_CpltOpenC`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Count of complete valve Open strokes (Closed to Opened) |
| **`Val_CpltCloseC`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Count of complete valve Close strokes (Opened to Closed) |
| **`Val_IncpltOpenC`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Count of incomplete valve Open strokes (Closed - Moving - Closed) |
| **`Val_IncpltCloseC`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Count of incomplete valve Close strokes (Opened - Moving - Opened) |
| **`Val_StopOtherC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Count of valve Stopped / Other occurrences |
| **`Val_SlowOpenC`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Count of valve Slow to Open occurrences |
| **`Val_SlowCloseC`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Count of valve Slow to Close occurrences |
| **`Sts_SlowOpen`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Last Closed to Opened stroke exceeded configured time threshold |
| **`Sts_SlowClose`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Last Opened to Closed stroke exceeded configured time threshold |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Lỗi cài đặt thông số cấu hình (Configuration Error): invalid Slow Open Time or Slow Close Time |
| **`P_ValveStats`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 8 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_ValveStats`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_8` | `'HMI'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'Valve Statistics (stroke times, counts)'` | [Biến cấu hình thông số] Description for display on HMI. The string shows in the title bar of the faceplate. |
| **`Cfg_Label`** | `STRING_20` | `'Valve Statistics'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI. This string appears on the graphic symbol. |
| **`Cfg_StopOtherDesc`** | `STRING_20` | `''` | [Biến cấu hình thông số] Name for the stopped or other state for display on HMI. |
| **`Cfg_Tag`** | `STRING_20` | `'P_ValveStats'` | [Biến cấu hình thông số] Tagname for display on HMI. This string shows in the bottom right of the Home tab of the faceplate. |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_ValveStats'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI và phần mềm thông tin |

