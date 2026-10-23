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

---
# PHẦN TRA CỨU CHI TIẾT TOÀN BỘ BIẾN (INPUT, OUTPUT, LOCAL TAGS), CÁCH SỬ DỤNG & CẤU HÌNH TỪNG KHỐI AOI

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_Motor`
**Thuộc chương:** [Chương 3: Động cơ, Biến tần & Rơ-le Quá tải (Motors & Drives)](../03_CHUONG_3_MOTORS_VA_BIEN_TAN.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Motors 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 127–131)  
**Tệp định nghĩa khối lệnh:** `P_Motor_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_Motor`:** **87** Biến đầu vào (`Input Parameters`) | **79** Biến đầu ra (`Output Parameters`) | **27** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_Motor` trong Studio 5000
- **Chức năng cốt lõi:** Khối điều khiển **Động cơ 1 tốc độ, không đảo chiều (Single-Speed Non-Reversing Motor)**. Hỗ trợ mạch Contactor giữ duy trì (`Out_Run`) hoặc mạch xung tự giữ 3 dây (`Out_Start` / `Out_Stop`), nhấp thử (`Jog`), giám sát tiếp điểm phụ `Inp_RunFdbk` để báo lỗi `FailToStart` / `FailToStop`.
- **Khi nào CẦN sử dụng:** Điều khiển mọi **bơm, quạt, máy khuấy, băng tải chạy 1 chiều 1 tốc độ** đóng/cắt qua khởi động từ (Contactor DOL / Sao-Tam giác). *(Lưu ý: nhớ bật `Cfg_HasRunFdbk = 1` và `Cfg_UseRunFdbk = 1` khi có đấu dây phản hồi chạy)*.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi động cơ có 2 cấp tốc độ ([`P_Motor2Spd.md`](./P_Motor2Spd.md)), có đảo chiều quay ([`P_MotorRev.md`](./P_MotorRev.md)) hoặc dùng biến tần (`P_PF*`, `P_VSD`).

### Quy trình 4 bước đấu nối và cấu hình `P_Motor`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_RunFdbk`, `Inp_PermOK`, `Inp_NBPermOK`, `Inp_IntlkOK`, `Inp_NBIntlkOK`, `Inp_OvldNotify`, `Inp_HornInh`, `Inp_IOFault`, `Inp_Sim`, `Inp_Hand`, `Inp_Ovrd`, `Inp_OvrdCmd`, `Inp_ExtInh`, `Inp_Reset`, `Inp_IntlkAvail`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_HasJog`, `Cfg_HasRunFdbk`, `Cfg_UseRunFdbk`, `Cfg_AllowLocal`, `Cfg_HasPermObj`, `Cfg_HasIntlkObj`, `Cfg_HasResInhObj`, `Cfg_HasRunTimeObj`, `Cfg_HasMoreObj`, `Cfg_HasOvldObj`, `Cfg_OperStopPrio`, `Cfg_ExtStopPrio`, `Cfg_OCmdResets`, `Cfg_XCmdResets`, `Cfg_OvrdPermIntlk`, `Cfg_ShedOnFailToStart`, `Cfg_ShedOnIOFault`, `Cfg_HasFailToStartAlm`...
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_Desc`, `Cfg_Label`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_Motor` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_Motor` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PSet_Owner`, `PCmd_Start`, `PCmd_Stop`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_FailToStartAck`, `PCmd_FailToStopAck`, `PCmd_IntlkTripAck`, `PCmd_IOFaultAck`, `PCmd_FailToStartSuppress`, `PCmd_FailToStopSuppress`, `PCmd_IntlkTripSuppress`, `PCmd_IOFaultSuppress`, `PCmd_FailToStartUnsuppress`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh điều khiển tự động, kiểm tra khối đã sẵn sàng ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển quyền về Program, sau đó gán giá trị vào `PSet_*` và kích xung `PCmd_* = 1` (khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Out_Run`, `Out_Start`, `Out_Stop`, `Out_Horn`, `Val_Cmd`, `Val_Fdbk`, `Val_Sts`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Val_Owner`, `Sts_Stopped`, `Sts_Starting`, `Sts_Running`, `Sts_Stopping`, `Sts_Jogging`, `Sts_Available`, `Sts_Bypass`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 87 Biến Đầu Vào (`Input Parameters`) của `P_Motor`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp_RunFdbk`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tiếp điểm phụ phản hồi trạng thái Chạy từ động cơ/khởi động từ (1 = Động cơ đang chạy, 0 = Động cơ đã dừng) |
| **`Inp_PermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp điều kiện Cho phép khởi động có thể Bypass (1 = Tất cả điều kiện Perm OK, cho phép khởi động; thường nối từ P_Perm.Sts_PermOK) |
| **`Inp_NBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp điều kiện Cho phép khởi động KHÔNG thể Bypass (1 = Tất cả điều kiện Non-Bypassable Perm OK; thường nối từ P_Perm.Sts_NBPermOK) |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp Khóa liên động an toàn có thể Bypass (1 = Liên động OK cho phép chạy; 0 = Cắt dừng thiết bị ngay lập tức; nối từ P_Intlk.Sts_IntlkOK) |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp Khóa liên động an toàn KHÔNG thể Bypass (1 = Liên động bất khả kháng OK; 0 = Cắt dừng thiết bị ngay lập tức; nối từ P_Intlk.Sts_NBIntlkOK) |
| **`Inp_OvldNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã mức độ ưu tiên cảnh báo và xác nhận (enum) nối từ khối Rơ-le quá tải (P_E300Ovld / P_E3Ovld / P_E1PlusE) |
| **`Inp_HornInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân khóa cấm còi báo động tại hiện trường: 1 = Cấm phát còi báo (Inhibit Horn); 0 = Cho phép phát còi báo trước khi khởi động |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi truyền thông I/O của thiết bị (0 = Tốt/OK, 1 = Lỗi truyền thông I/O Fault) |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân kích hoạt chế độ Mô phỏng (Simulation): Khi = 1, khóa các ngõ ra vật lý ở mức an toàn và dùng giá trị/phản hồi giả lập nội bộ; khi = 0, vận hành thực tế bình thường |
| **`Inp_Hand`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu chọn chế độ Điều khiển bằng tay tại chỗ (Hand/Local): 1 = Chiếm quyền sang chế độ Hand (thường nối từ công tắc cứng tại tủ MCC); 0 = Nhả chế độ Hand |
| **`Inp_Ovrd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu kích hoạt chế độ Cưỡng bức ưu tiên (Override): 1 = Chiếm quyền sang chế độ Override; 0 = Nhả chế độ Override |
| **`Inp_OvrdCmd`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã lệnh điều khiển động cơ khi ở chế độ Cưỡng bức (Override): 0 = Không tác động, 1 = Cưỡng bức Dừng (Stop), 2 = Cưỡng bức Chạy (Start) |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân khóa cấm nguồn lệnh ngoài (External Inhibit): 1 = Cấm chế độ External chiếm quyền điều khiển; 0 = Cho phép External chiếm quyền |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Cfg_HasJog`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép và hiển thị nút nhấn Nhấp thử (Jog) trên màn hình HMI; 0 = Khóa cấm lệnh Jog |
| **`Cfg_HasRunFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Động cơ có đấu nối tiếp điểm phụ phản hồi chạy (Inp_RunFdbk) về PLC; 0 = Không có tiếp điểm phản hồi chạy |
| **`Cfg_UseRunFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Sử dụng tiếp điểm phản hồi chạy để kiểm tra lỗi FailToStart/FailToStop; 0 = Tạm bỏ qua (Bypass bảo trì) không kiểm tra phản hồi chạy |
| **`Cfg_AllowLocal`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép khởi động/dừng động cơ tại chỗ ở tủ MCC mà không phát báo động lỗi; 0 = Chỉ cho phép điều khiển từ HMI hoặc Program |
| **`Cfg_HasPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Điều kiện cho phép (P_Perm) nối vào chân Inp_PermOK và bật nút điều hướng mở Faceplate P_Perm |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Khóa liên động (P_Intlk) nối vào chân Inp_IntlkOK và bật nút điều hướng mở Faceplate P_Intlk |
| **`Cfg_HasResInhObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Cấm khởi động lại (P_ResInh) đi kèm và bật nút điều hướng mở Faceplate P_ResInh |
| **`Cfg_HasRunTimeObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Đếm giờ chạy (P_RunTime) đi kèm và bật nút điều hướng mở Faceplate P_RunTime |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_HasOvldObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Rơ-le quá tải (P_E300Ovld / P_E3Ovld / P_E1PlusE) đi kèm và bật nút điều hướng mở Faceplate |
| **`Cfg_OperStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Nút nhấn Dừng của Người vận hành (OCmd_Stop) có quyền ưu tiên cao nhất, bấm dừng được ngay cả khi máy đang chạy ở chế độ Program; 0 = OCmd_Stop chỉ có tác dụng khi ở chế độ Operator/Maintenance |
| **`Cfg_ExtStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Lệnh Dừng từ hệ thống ngoài (XCmd_Stop) có hiệu lực ưu tiên ở mọi chế độ; 0 = Chỉ có hiệu lực khi ở chế độ External |
| **`Cfg_OCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khi Người vận hành ra lệnh Chạy/Mở mới (OCmd_*), khối tự động xóa luôn các chốt lỗi cũ; 0 = Bắt buộc phải nhấn nút Reset trước khi ra lệnh mới |
| **`Cfg_XCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khi hệ thống ngoài gửi lệnh Chạy/Mở mới (XCmd_*), khối tự động xóa luôn các chốt lỗi cũ; 0 = Bắt buộc phải gửi xung XCmd_Reset trước |
| **`Cfg_OvrdPermIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Chế độ Cưỡng bức (Override) được quyền bỏ qua các điều kiện Cho phép và Liên động có thể bypass; 0 = Chế độ Override vẫn phải tuân thủ mọi điều kiện |
| **`Cfg_ShedOnFailToStart`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt lệnh chạy (Shed) và phát báo động khi động cơ bị lỗi Không khởi động được (Fail to Start); 0 = Chỉ phát báo động nhưng vẫn giữ ngõ ra |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt dừng thiết bị về trạng thái an toàn (Shed) và phát báo động khi lỗi truyền thông thẻ I/O; 0 = Chỉ phát báo động |
| **`Cfg_HasFailToStartAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi không khởi động được (Fail to Start): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasFailToStopAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi không dừng được (Fail to Stop): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_FailToStartResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi không khởi động được (Fail to Start): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_FailToStopResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi không dừng được (Fail to Stop): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_FailToStartAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi không khởi động được (Fail to Start): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_FailToStopAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi không dừng được (Fail to Stop): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_FailToStartSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi không khởi động được (Fail to Start) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_FailToStopSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi không dừng được (Fail to Stop) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_StartHornT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian hú còi cảnh báo tại hiện trường (Out_Horn) trước khi đóng điện khởi động thiết bị (giây; đặt = 0.0 để tắt còi) |
| **`Cfg_SimFdbkT`** | `REAL` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ giả lập tín hiệu phản hồi chạy/đóng/mở trong chế độ Mô phỏng (Inp_Sim = 1, tính bằng giây) |
| **`Cfg_FailToStartT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian chờ tối đa (giây) từ lúc xuất lệnh Chạy đến khi phải nhận được phản hồi chạy (Inp_RunFdbk = 1) trước khi báo lỗi FailToStart |
| **`Cfg_FailToStopT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian chờ tối đa (giây) từ lúc xuất lệnh Dừng đến khi phản hồi chạy phải về 0 trước khi báo lỗi FailToStop |
| **`Cfg_MaxJogT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian tối đa cho phép giữ lệnh chạy nhấp thử Jog liên tục (giây; đặt = 0.0 là không giới hạn) |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Mã định danh Chủ sở hữu (Owner ID: ghi khác 0 để đăng ký chiếm quyền điều khiển, ghi 0 để nhả quyền) |
| **`PCmd_Start`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khởi động (Start) thiết bị/động cơ |
| **`PCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Dừng (Stop) thiết bị/động cơ |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Chương trình tự động (từ Operator sang Program) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Người vận hành (từ Program sang Operator) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa giữ nguồn lệnh hiện tại (không cho phép nguồn lệnh kia giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Đưa nguồn lệnh về chế độ Bình thường mặc định (Normal: Program hoặc Operator theo Cfg_ProgNormal) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`PCmd_FailToStartAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi không khởi động được (Fail to Start) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_FailToStopAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi không dừng được (Fail to Stop) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_FailToStartSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi không khởi động được (Fail to Start) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStopSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi không dừng được (Fail to Stop) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStartUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi không khởi động được (Fail to Start) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStopUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi không dừng được (Fail to Stop) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStartUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi không khởi động được (Fail to Start) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_FailToStopUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi không dừng được (Fail to Stop) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) ngay lập tức (biến xung tự động xóa về 0) |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`XCmd_Start`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Khởi động (Start) thiết bị/động cơ |
| **`XCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Dừng (Stop) thiết bị/động cơ |
| **`XCmd_Jog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhấp thử (Jog) thiết bị/động cơ khi giữ nút |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`Inp_IntlkAvail`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu báo điều kiện Khóa liên động sẵn sàng cho phép gọi máy trong nhóm (1 = Interlock Availability OK) |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Chiếm quyền điều khiển (Acquire) về chế độ tương ứng |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |

## 3. Bảng chi tiết 79 Biến Đầu Ra (`Output Parameters`) của `P_Motor`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Out_Run`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra duy trì lệnh Chạy động cơ trong mạch 2 dây (1 = Chạy động cơ, 0 = Dừng động cơ) |
| **`Out_Start`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra phát xung Khởi động động cơ trong mạch 3 dây (1 = Khởi động động cơ, 0 = Giữ nguyên trạng thái hiện tại) |
| **`Out_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra điều khiển Dừng động cơ hoặc dừng hành trình van (1 = Cấp lệnh Dừng, 0 = Giữ nguyên trạng thái) |
| **`Out_Horn`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra bật còi cảnh báo âm thanh tại hiện trường trước khi khởi động hoặc chuyển trạng thái thiết bị (1 = Bật còi) |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng dữ liệu tổng hợp cuối cùng (0 = Tín hiệu thực Tốt đã xác nhận, 1 = Tín hiệu thực Tốt giả định, 2 = Không cấu hình phản hồi/Tốt giả định, 8 = Đang mô phỏng, 9 = Mô phỏng vòng lặp, 10 = Giá trị nhập tay, 16 = Ngoài đặc tính kỹ thuật, 17 = Thay thế tại thiết bị/bus, 18 = Thay thế bởi Bảo trì, 19 = Ngắt bảo vệ giữ giá trị cũ, 20 = Ngắt bảo vệ dùng giá trị thay thế, 32 = Lỗi tín hiệu vượt dải/NaN, 33 = Lỗi kênh I/O, 34 = Lỗi module I/O, 35 = Lỗi cấu hình) |
| **`Val_Cmd`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã lệnh điều khiển động cơ hiện hành: 0 = Không tác động, 1 = Dừng (Stop), 2 = Khởi động chạy (Start) |
| **`Val_Fdbk`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã phản hồi trạng thái động cơ: 0 = Đã dừng (Stopped), 1 = Đang chạy (Running) |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái xác nhận của động cơ: 0 = Chưa xác định, 1 = Đang dừng (Stopped), 2 = Đang chạy (Running), 5 = Đang khởi động (Starting), 8 = Đang chạy nhấp thử (Jogging), 10 = Đang dừng (Stopping), 33 = Ngưng phục vụ (Disabled) |
| **`Val_Fault`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái lỗi thiết bị (Device Fault Status — kiểu liệt kê enum: 0 = Không lỗi, 16 = Lỗi khởi động/mở, 17 = Lỗi dừng/đóng, 20 = Lo, 21 = Hi, 24 = LoLo, 25 = HiHi, 32 = Fail/I/O Fault, 34 = Lỗi cấu hình CfgErr) |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận tổng hợp của khối này cộng với các đối tượng kênh/quá tải liên kết |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms Count) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã định danh Chủ sở hữu hiện hành của đối tượng (Current Object Owner ID: 0 = Đang không bị chiếm quyền) |
| **`Sts_Stopped`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần được yêu cầu dừng và đã xác nhận Dừng hoàn toàn (Confirmed Stopped) |
| **`Sts_Starting`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần được yêu cầu chạy và đang chờ tín hiệu phản hồi chạy (Starting) |
| **`Sts_Running`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần được yêu cầu chạy và đã xác nhận đang Chạy (Confirmed Running) |
| **`Sts_Stopping`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần được yêu cầu dừng và đang chờ tín hiệu phản hồi về dừng (Stopping) |
| **`Sts_Jogging`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần đang chạy nhấp thử (Jogging) |
| **`Sts_Available`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị sẵn sàng cho Chương trình tự động (Program) điều khiển (đang ở chế độ Program và không có lỗi/liên động ngắt) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass hiện đang được bỏ qua (Bypassed) |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Chế độ Bypass bảo trì đang kích hoạt (gửi sang khối P_Intlk / P_Perm để cho phép bỏ qua các điều kiện có quyền Bypass) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị chưa sẵn sàng hoạt động (kiểm tra các bit Nrdy_* chi tiết để biết nguyên nhân cụ thể) |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang có lỗi thiết bị ngắt bảo vệ (Shed — yêu cầu nhấn Reset) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Khóa liên động an toàn không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi truyền thông thẻ I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Nrdy_Perm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Cho phép khởi động không thỏa mãn (Permissive Not OK) |
| **`Nrdy_PrioStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng: Đang dính lệnh Dừng ưu tiên từ Operator/External (cần nhấn Reset trước khi chạy lại) |
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
| **`Sts_Oper`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Người vận hành trên HMI (Operator) |
| **`Sts_OperLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Người vận hành và đã Khóa (Operator Locked — không cho Program lấy quyền) |
| **`Sts_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Nguồn lệnh hiện hành đang khớp với nguồn lệnh Bình thường mặc định (Program hoặc Operator theo Cfg_ProgNormal) |
| **`Sts_ExtReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Yêu cầu chuyển sang chế độ External đang bị khóa cấm (không thể chuyển sang External từ trạng thái hiện tại) |
| **`Sts_ProgReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Yêu cầu chuyển sang chế độ Program đang bị khóa cấm (không thể chuyển sang Program từ trạng thái hiện tại) |
| **`Sts_MAcqRcvd`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đã nhận được lệnh Chiếm quyền bảo trì (MCmd_Acq) trong chu kỳ quét hiện tại |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc chốt ngắt bảo vệ (Shed) đã hết lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi không khởi động được (Fail to Start) |
| **`Alm_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi không khởi động được (Fail to Start) |
| **`Ack_FailToStart`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi không khởi động được (Fail to Start) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_FailToStartDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không khởi động được (Fail to Start) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_FailToStartShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không khởi động được (Fail to Start) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_FailToStartSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không khởi động được (Fail to Start) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi không dừng được (Fail to Stop) |
| **`Alm_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi không dừng được (Fail to Stop) |
| **`Ack_FailToStop`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi không dừng được (Fail to Stop) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_FailToStopDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không dừng được (Fail to Stop) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_FailToStopShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không dừng được (Fail to Stop) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_FailToStopSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không dừng được (Fail to Stop) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
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
| **`Sts_Horn`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Còi cảnh báo khởi động tại hiện trường đang kêu (Horn Active) |
| **`P_Motor`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_Motor) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 27 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_Motor`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'Single Speed Motor'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_Label`** | `STRING_20` | `'Motor Control'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_Tag`** | `STRING_20` | `'P_Motor'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc nội bộ quản lý phân quyền và chuyển mạch giữa 7 nguồn lệnh điều khiển (OoS, Hand, Maint, Ovrd, Ext, Prog, Oper) |
| **`FailToStart`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi không khởi động được (Fail to Start) |
| **`FailToStop`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi không dừng được (Fail to Stop) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_Motor'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`IOFault`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bỏ qua (Bypass) tất cả các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Kiểm tra lại bình thường (Check / Gỡ bỏ Bypass) toàn bộ điều kiện Khóa liên động và Cho phép |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (Bỏ qua (Bypass) tất cả các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (Kiểm tra lại bình thường (Check / Gỡ bỏ Bypass) toàn bộ điều kiện Khóa liên động và Cho phép), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`OCmd_Jog`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Nhấp thử (Jog) thiết bị/động cơ khi giữ nút |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo |
| **`OCmd_Start`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Khởi động (Start) thiết bị/động cơ |
| **`OCmd_Stop`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Dừng (Stop) thiết bị/động cơ |
| **`ORdy_Jog`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Jog (Nhấp thử (Jog) thiết bị/động cơ khi giữ nút), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Start`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Start (Khởi động (Start) thiết bị/động cơ), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Stop`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Stop (Dừng (Stop) thiết bị/động cơ), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo hiện hành và trạng thái xác nhận của khối này (0 = Không có cảnh báo, 1 = Đã hết lỗi nhưng chờ Reset/Ack, 2..3 = Thấp, 4..5 = Trung bình, 6..7 = Cao, 8..9 = Khẩn cấp) |
---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_Motor2Spd`
**Thuộc chương:** [Chương 3: Động cơ, Biến tần & Rơ-le Quá tải (Motors & Drives)](../03_CHUONG_3_MOTORS_VA_BIEN_TAN.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Motors 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 132–135)  
**Tệp định nghĩa khối lệnh:** `P_Motor2Spd_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_Motor2Spd`:** **94** Biến đầu vào (`Input Parameters`) | **83** Biến đầu ra (`Output Parameters`) | **31** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_Motor2Spd` trong Studio 5000
- **Chức năng cốt lõi:** Khối điều khiển **Động cơ 2 cấp tốc độ (Two-Speed Motor: Slow / Fast / Stopped)** với 2 ngõ ra cuộn dây `Out_Slow`, `Out_Fast` và 2 phản hồi `Inp_SlowFdbk`, `Inp_FastFdbk`, có bộ định thời gian trễ giảm tốc an toàn khi chuyển từ Nhanh về Chậm.
- **Khi nào CẦN sử dụng:** Điều khiển **quạt tháp giải nhiệt 2 tốc độ, máy khuấy 2 cấp tốc độ, bơm 2 cấp** dùng động cơ cực đổi (Dahlander) hoặc 2 cuộn dây stator độc lập.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi tốc độ thay đổi vô cấp bằng biến tần → Dùng `P_PF*` hoặc [`P_VSD.md`](./P_VSD.md).

### Quy trình 4 bước đấu nối và cấu hình `P_Motor2Spd`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_SlowRunFdbk`, `Inp_FastRunFdbk`, `Inp_SlowPermOK`, `Inp_SlowNBPermOK`, `Inp_FastPermOK`, `Inp_FastNBPermOK`, `Inp_IntlkOK`, `Inp_NBIntlkOK`, `Inp_OvldNotify`, `Inp_IOFault`, `Inp_Sim`, `Inp_Hand`, `Inp_Ovrd`, `Inp_OvrdCmd`, `Inp_ExtInh`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_HasJog`, `Cfg_HasRunFdbk`, `Cfg_UseRunFdbk`, `Cfg_AllowLocal`, `Cfg_HasSlowPermObj`, `Cfg_HasFastPermObj`, `Cfg_HasIntlkObj`, `Cfg_HasResInhObj`, `Cfg_HasRunTimeObj`, `Cfg_HasMoreObj`, `Cfg_HasOvldObj`, `Cfg_OperStopPrio`, `Cfg_ExtStopPrio`, `Cfg_OCmdResets`, `Cfg_XCmdResets`, `Cfg_OvrdPermIntlk`, `Cfg_ShedOnFailToStart`, `Cfg_ShedOnIOFault`...
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_Desc`, `Cfg_FastText`, `Cfg_Label`, `Cfg_SlowText`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_Motor2Spd` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_Motor2Spd` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PSet_Owner`, `PCmd_StartFast`, `PCmd_StartSlow`, `PCmd_Stop`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_FailToStartAck`, `PCmd_FailToStartSuppress`, `PCmd_FailToStartUnsuppress`, `PCmd_FailToStartUnshelve`, `PCmd_FailToStopAck`, `PCmd_FailToStopSuppress`, `PCmd_FailToStopUnsuppress`, `PCmd_FailToStopUnshelve`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh điều khiển tự động, kiểm tra khối đã sẵn sàng ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển quyền về Program, sau đó gán giá trị vào `PSet_*` và kích xung `PCmd_* = 1` (khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Out_RunSlow`, `Out_RunFast`, `Out_StartSlow`, `Out_StartFast`, `Out_Stop`, `Out_Horn`, `Val_Cmd`, `Val_Fdbk`, `Val_Sts`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Val_Owner`, `Sts_Stopped`, `Sts_StartingSlow`, `Sts_StartingFast`, `Sts_RunningSlow`, `Sts_RunningFast`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 94 Biến Đầu Vào (`Input Parameters`) của `P_Motor2Spd`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp_SlowRunFdbk`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tiếp điểm phụ phản hồi động cơ đang Chạy Chậm (1 = Đang chạy tốc độ Chậm Slow) |
| **`Inp_FastRunFdbk`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tiếp điểm phụ phản hồi động cơ đang Chạy Nhanh (1 = Đang chạy tốc độ Nhanh Fast) |
| **`Inp_SlowPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Điều kiện Cho phép khởi động tốc độ Chậm (có thể bypass) đều OK |
| **`Inp_SlowNBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Điều kiện Cho phép khởi động tốc độ Chậm (không thể bypass) đều OK |
| **`Inp_FastPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Điều kiện Cho phép khởi động tốc độ Nhanh (có thể bypass) đều OK |
| **`Inp_FastNBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Điều kiện Cho phép khởi động tốc độ Nhanh (không thể bypass) đều OK |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp Khóa liên động an toàn có thể Bypass (1 = Liên động OK cho phép chạy; 0 = Cắt dừng thiết bị ngay lập tức; nối từ P_Intlk.Sts_IntlkOK) |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp Khóa liên động an toàn KHÔNG thể Bypass (1 = Liên động bất khả kháng OK; 0 = Cắt dừng thiết bị ngay lập tức; nối từ P_Intlk.Sts_NBIntlkOK) |
| **`Inp_OvldNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã mức độ ưu tiên cảnh báo và xác nhận (enum) nối từ khối Rơ-le quá tải (P_E300Ovld / P_E3Ovld / P_E1PlusE) |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi truyền thông I/O của thiết bị (0 = Tốt/OK, 1 = Lỗi truyền thông I/O Fault) |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân kích hoạt chế độ Mô phỏng (Simulation): Khi = 1, khóa các ngõ ra vật lý ở mức an toàn và dùng giá trị/phản hồi giả lập nội bộ; khi = 0, vận hành thực tế bình thường |
| **`Inp_Hand`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu chọn chế độ Điều khiển bằng tay tại chỗ (Hand/Local): 1 = Chiếm quyền sang chế độ Hand (thường nối từ công tắc cứng tại tủ MCC); 0 = Nhả chế độ Hand |
| **`Inp_Ovrd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu kích hoạt chế độ Cưỡng bức ưu tiên (Override): 1 = Chiếm quyền sang chế độ Override; 0 = Nhả chế độ Override |
| **`Inp_OvrdCmd`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã lệnh động cơ 2 tốc độ khi ở chế độ Cưỡng bức (Override): 0 = Không tác động, 1 = Dừng (Stop), 2 = Chạy Nhanh (Run Fast), 3 = Chạy Chậm (Run Slow) |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân khóa cấm nguồn lệnh ngoài (External Inhibit): 1 = Cấm chế độ External chiếm quyền điều khiển; 0 = Cho phép External chiếm quyền |
| **`Inp_HornInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân khóa cấm còi báo động tại hiện trường: 1 = Cấm phát còi báo (Inhibit Horn); 0 = Cho phép phát còi báo trước khi khởi động |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Cfg_HasJog`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép và hiển thị nút nhấn Nhấp thử (Jog) trên màn hình HMI; 0 = Khóa cấm lệnh Jog |
| **`Cfg_HasRunFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Động cơ có đấu nối tiếp điểm phụ phản hồi chạy (Inp_RunFdbk) về PLC; 0 = Không có tiếp điểm phản hồi chạy |
| **`Cfg_UseRunFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Sử dụng tiếp điểm phản hồi chạy để kiểm tra lỗi FailToStart/FailToStop; 0 = Tạm bỏ qua (Bypass bảo trì) không kiểm tra phản hồi chạy |
| **`Cfg_AllowLocal`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép khởi động/dừng động cơ tại chỗ ở tủ MCC mà không phát báo động lỗi; 0 = Chỉ cho phép điều khiển từ HMI hoặc Program |
| **`Cfg_HasSlowPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối P_Perm nối vào chân cho phép chạy Chậm (Inp_SlowPermOK) |
| **`Cfg_HasFastPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối P_Perm nối vào chân cho phép chạy Nhanh (Inp_FastPermOK) |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Khóa liên động (P_Intlk) nối vào chân Inp_IntlkOK và bật nút điều hướng mở Faceplate P_Intlk |
| **`Cfg_HasResInhObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Cấm khởi động lại (P_ResInh) đi kèm và bật nút điều hướng mở Faceplate P_ResInh |
| **`Cfg_HasRunTimeObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Đếm giờ chạy (P_RunTime) đi kèm và bật nút điều hướng mở Faceplate P_RunTime |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_HasOvldObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Rơ-le quá tải (P_E300Ovld / P_E3Ovld / P_E1PlusE) đi kèm và bật nút điều hướng mở Faceplate |
| **`Cfg_OperStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Nút nhấn Dừng của Người vận hành (OCmd_Stop) có quyền ưu tiên cao nhất, bấm dừng được ngay cả khi máy đang chạy ở chế độ Program; 0 = OCmd_Stop chỉ có tác dụng khi ở chế độ Operator/Maintenance |
| **`Cfg_ExtStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Lệnh Dừng từ hệ thống ngoài (XCmd_Stop) có hiệu lực ưu tiên ở mọi chế độ; 0 = Chỉ có hiệu lực khi ở chế độ External |
| **`Cfg_OCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khi Người vận hành ra lệnh Chạy/Mở mới (OCmd_*), khối tự động xóa luôn các chốt lỗi cũ; 0 = Bắt buộc phải nhấn nút Reset trước khi ra lệnh mới |
| **`Cfg_XCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khi hệ thống ngoài gửi lệnh Chạy/Mở mới (XCmd_*), khối tự động xóa luôn các chốt lỗi cũ; 0 = Bắt buộc phải gửi xung XCmd_Reset trước |
| **`Cfg_OvrdPermIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Chế độ Cưỡng bức (Override) được quyền bỏ qua các điều kiện Cho phép và Liên động có thể bypass; 0 = Chế độ Override vẫn phải tuân thủ mọi điều kiện |
| **`Cfg_ShedOnFailToStart`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt lệnh chạy (Shed) và phát báo động khi động cơ bị lỗi Không khởi động được (Fail to Start); 0 = Chỉ phát báo động nhưng vẫn giữ ngõ ra |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt dừng thiết bị về trạng thái an toàn (Shed) và phát báo động khi lỗi truyền thông thẻ I/O; 0 = Chỉ phát báo động |
| **`Cfg_HasFailToStartAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi không khởi động được (Fail to Start): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasFailToStopAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi không dừng được (Fail to Stop): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_FailToStartResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi không khởi động được (Fail to Start): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_FailToStopResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi không dừng được (Fail to Stop): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_FailToStartAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi không khởi động được (Fail to Start): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_FailToStopAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi không dừng được (Fail to Stop): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_FailToStartSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi không khởi động được (Fail to Start) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_FailToStopSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi không dừng được (Fail to Stop) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_ChangeSpdT`** | `REAL` | `3` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở hết các Contactor chờ động cơ giảm tốc khi chuyển đổi giữa 2 cấp tốc độ Nhanh/Chậm (giây) |
| **`Cfg_StartHornT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian hú còi cảnh báo tại hiện trường (Out_Horn) trước khi đóng điện khởi động thiết bị (giây; đặt = 0.0 để tắt còi) |
| **`Cfg_SimFdbkT`** | `REAL` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ giả lập tín hiệu phản hồi chạy/đóng/mở trong chế độ Mô phỏng (Inp_Sim = 1, tính bằng giây) |
| **`Cfg_FailToStartT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian chờ tối đa (giây) từ lúc xuất lệnh Chạy đến khi phải nhận được phản hồi chạy (Inp_RunFdbk = 1) trước khi báo lỗi FailToStart |
| **`Cfg_FailToStopT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian chờ tối đa (giây) từ lúc xuất lệnh Dừng đến khi phản hồi chạy phải về 0 trước khi báo lỗi FailToStop |
| **`Cfg_MaxJogT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian tối đa cho phép giữ lệnh chạy nhấp thử Jog liên tục (giây; đặt = 0.0 là không giới hạn) |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Mã định danh Chủ sở hữu (Owner ID: ghi khác 0 để đăng ký chiếm quyền điều khiển, ghi 0 để nhả quyền) |
| **`PCmd_StartFast`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khởi động ở tốc độ Nhanh (Start Fast) |
| **`PCmd_StartSlow`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khởi động ở tốc độ Chậm (Start Slow) |
| **`PCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Dừng (Stop) thiết bị/động cơ |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Chương trình tự động (từ Operator sang Program) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Người vận hành (từ Program sang Operator) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa giữ nguồn lệnh hiện tại (không cho phép nguồn lệnh kia giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Đưa nguồn lệnh về chế độ Bình thường mặc định (Normal: Program hoặc Operator theo Cfg_ProgNormal) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`PCmd_FailToStartAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi không khởi động được (Fail to Start) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_FailToStartSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi không khởi động được (Fail to Start) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStartUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi không khởi động được (Fail to Start) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStartUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi không khởi động được (Fail to Start) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_FailToStopAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi không dừng được (Fail to Stop) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_FailToStopSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi không dừng được (Fail to Stop) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStopUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi không dừng được (Fail to Stop) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStopUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi không dừng được (Fail to Stop) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) ngay lập tức (biến xung tự động xóa về 0) |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |
| **`Sts_Horn`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Còi cảnh báo khởi động tại hiện trường đang kêu (Horn Active) |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`XCmd_StartSlow`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Khởi động ở tốc độ Chậm (Start Slow) |
| **`XCmd_StartFast`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Khởi động ở tốc độ Nhanh (Start Fast) |
| **`XCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Dừng (Stop) thiết bị/động cơ |
| **`XCmd_Jog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhấp thử (Jog) thiết bị/động cơ khi giữ nút |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Chiếm quyền điều khiển (Acquire) về chế độ tương ứng |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |

## 3. Bảng chi tiết 83 Biến Đầu Ra (`Output Parameters`) của `P_Motor2Spd`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Out_RunSlow`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra duy trì lệnh Chạy Chậm động cơ 2 tốc độ (1 = Chạy Chậm, 0 = Dừng) |
| **`Out_RunFast`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra duy trì lệnh Chạy Nhanh động cơ 2 tốc độ (1 = Chạy Nhanh, 0 = Dừng) |
| **`Out_StartSlow`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra phát xung Khởi động Chậm động cơ 2 tốc độ (1 = Kích xung chạy Chậm) |
| **`Out_StartFast`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra phát xung Khởi động Nhanh động cơ 2 tốc độ (1 = Kích xung chạy Nhanh) |
| **`Out_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra điều khiển Dừng động cơ hoặc dừng hành trình van (1 = Cấp lệnh Dừng, 0 = Giữ nguyên trạng thái) |
| **`Out_Horn`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra bật còi cảnh báo âm thanh tại hiện trường trước khi khởi động hoặc chuyển trạng thái thiết bị (1 = Bật còi) |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng dữ liệu tổng hợp cuối cùng (0 = Tín hiệu thực Tốt đã xác nhận, 1 = Tín hiệu thực Tốt giả định, 2 = Không cấu hình phản hồi/Tốt giả định, 8 = Đang mô phỏng, 9 = Mô phỏng vòng lặp, 10 = Giá trị nhập tay, 16 = Ngoài đặc tính kỹ thuật, 17 = Thay thế tại thiết bị/bus, 18 = Thay thế bởi Bảo trì, 19 = Ngắt bảo vệ giữ giá trị cũ, 20 = Ngắt bảo vệ dùng giá trị thay thế, 32 = Lỗi tín hiệu vượt dải/NaN, 33 = Lỗi kênh I/O, 34 = Lỗi module I/O, 35 = Lỗi cấu hình) |
| **`Val_Cmd`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã lệnh động cơ 2 tốc độ hiện hành: 0 = Không tác động, 1 = Dừng (Stop), 3 = Khởi động Nhanh (Start Fast), 4 = Khởi động Chậm (Start Slow) |
| **`Val_Fdbk`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã phản hồi động cơ 2 tốc độ: 0 = Đã dừng (Stopped), 2 = Đang chạy Nhanh (Running Fast), 3 = Đang chạy Chậm (Running Slow), 6 = Lỗi chập cả hai phản hồi (Invalid) |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái xác nhận động cơ 2 tốc độ: 0 = Chưa xác định, 1 = Đang dừng (Stopped), 3 = Đang chạy Nhanh, 4 = Đang chạy Chậm, 7 = Đang dừng (Stopping), 9 = Đang khởi động Nhanh, 10 = Đang khởi động Chậm, 33 = Ngưng phục vụ (Disabled) |
| **`Val_Fault`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái lỗi thiết bị (Device Fault Status — kiểu liệt kê enum: 0 = Không lỗi, 16 = Lỗi khởi động/mở, 17 = Lỗi dừng/đóng, 20 = Lo, 21 = Hi, 24 = LoLo, 25 = HiHi, 32 = Fail/I/O Fault, 34 = Lỗi cấu hình CfgErr) |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận tổng hợp của khối này cộng với các đối tượng kênh/quá tải liên kết |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms Count) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã định danh Chủ sở hữu hiện hành của đối tượng (Current Object Owner ID: 0 = Đang không bị chiếm quyền) |
| **`Sts_Stopped`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần được yêu cầu dừng và đã xác nhận Dừng hoàn toàn (Confirmed Stopped) |
| **`Sts_StartingSlow`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ 2 tốc độ đang khởi động ở tốc độ Chậm và chờ phản hồi (Starting Slow) |
| **`Sts_StartingFast`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ 2 tốc độ đang khởi động ở tốc độ Nhanh và chờ phản hồi (Starting Fast) |
| **`Sts_RunningSlow`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ 2 tốc độ đã xác nhận đang chạy ở tốc độ Chậm (Confirmed Running Slow) |
| **`Sts_RunningFast`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ 2 tốc độ đã xác nhận đang chạy ở tốc độ Nhanh (Confirmed Running Fast) |
| **`Sts_Stopping`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần được yêu cầu dừng và đang chờ tín hiệu phản hồi về dừng (Stopping) |
| **`Sts_Jogging`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần đang chạy nhấp thử (Jogging) |
| **`Sts_Available`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị sẵn sàng cho Chương trình tự động (Program) điều khiển (đang ở chế độ Program và không có lỗi/liên động ngắt) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass hiện đang được bỏ qua (Bypassed) |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Chế độ Bypass bảo trì đang kích hoạt (gửi sang khối P_Intlk / P_Perm để cho phép bỏ qua các điều kiện có quyền Bypass) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị chưa sẵn sàng hoạt động (kiểm tra các bit Nrdy_* chi tiết để biết nguyên nhân cụ thể) |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang có lỗi thiết bị ngắt bảo vệ (Shed — yêu cầu nhấn Reset) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Khóa liên động an toàn không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi truyền thông thẻ I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Nrdy_Perm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Cho phép khởi động không thỏa mãn (Permissive Not OK) |
| **`Nrdy_PrioStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng: Đang dính lệnh Dừng ưu tiên từ Operator/External (cần nhấn Reset trước khi chạy lại) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoặc cưỡng bức bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có ít nhất một cảnh báo bị Tạm hoãn (Shelved), Vô hiệu hóa (Disabled) hoặc Ức chế (Suppressed) — hiển thị biểu tượng chữ "I" trên HMI |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị đặt bộ định thời gian (Timer Preset) nằm ngoài phạm vi hợp lệ 0..2.147.483 giây |
| **`Err_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thời gian trễ mô phỏng (Cfg_SimFdbkT / Cfg_SimRampT) nằm ngoài phạm vi 0..2.147.483 giây |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình cảnh báo: Thời gian duy trì tối thiểu (Min On Time), thời gian tạm hoãn (Shelf Time) hoặc mức độ nghiêm trọng (Severity 1..1000) không hợp lệ |
| **`Err_Pause`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thời gian tạm dừng giữa các bước (Pause Time) ngoài phạm vi 0..2.147.483 giây |
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
| **`Sts_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi không khởi động được (Fail to Start) |
| **`Alm_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi không khởi động được (Fail to Start) |
| **`Ack_FailToStart`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi không khởi động được (Fail to Start) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_FailToStartDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không khởi động được (Fail to Start) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_FailToStartShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không khởi động được (Fail to Start) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_FailToStartSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không khởi động được (Fail to Start) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi không dừng được (Fail to Stop) |
| **`Alm_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi không dừng được (Fail to Stop) |
| **`Ack_FailToStop`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi không dừng được (Fail to Stop) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_FailToStopDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không dừng được (Fail to Stop) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_FailToStopShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không dừng được (Fail to Stop) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_FailToStopSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không dừng được (Fail to Stop) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
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
| **`P_Motor2Spd`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_Motor2Spd) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 31 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_Motor2Spd`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'Two Speed Motor'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_FastText`** | `STRING_16` | `'Fast'` | [Biến cấu hình thông số] Chuỗi văn bản mô tả tốc độ Nhanh (Fast Speed) trên HMI |
| **`Cfg_Label`** | `STRING_20` | `'Motor Control'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_SlowText`** | `STRING_16` | `'Slow'` | [Biến cấu hình thông số] Chuỗi văn bản mô tả tốc độ Chậm (Slow Speed) trên HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_Motor2Spd'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc nội bộ quản lý phân quyền và chuyển mạch giữa 7 nguồn lệnh điều khiển (OoS, Hand, Maint, Ovrd, Ext, Prog, Oper) |
| **`FailToStart`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi không khởi động được (Fail to Start) |
| **`FailToStop`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi không dừng được (Fail to Stop) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_Motor2Spd'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`IOFault`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bỏ qua (Bypass) tất cả các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Kiểm tra lại bình thường (Check / Gỡ bỏ Bypass) toàn bộ điều kiện Khóa liên động và Cho phép |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (Bỏ qua (Bypass) tất cả các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (Kiểm tra lại bình thường (Check / Gỡ bỏ Bypass) toàn bộ điều kiện Khóa liên động và Cho phép), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`OCmd_Jog`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Nhấp thử (Jog) thiết bị/động cơ khi giữ nút |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo |
| **`OCmd_StartFast`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Khởi động ở tốc độ Nhanh (Start Fast) |
| **`OCmd_StartSlow`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Khởi động ở tốc độ Chậm (Start Slow) |
| **`OCmd_Stop`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Dừng (Stop) thiết bị/động cơ |
| **`ORdy_Jog`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Jog (Nhấp thử (Jog) thiết bị/động cơ khi giữ nút), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_StartFast`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_StartFast (Khởi động ở tốc độ Nhanh (Start Fast)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_StartSlow`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_StartSlow (Khởi động ở tốc độ Chậm (Start Slow)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Stop`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Stop (Dừng (Stop) thiết bị/động cơ), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo hiện hành và trạng thái xác nhận của khối này (0 = Không có cảnh báo, 1 = Đã hết lỗi nhưng chờ Reset/Ack, 2..3 = Thấp, 4..5 = Trung bình, 6..7 = Cao, 8..9 = Khẩn cấp) |
---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_MotorRev`
**Thuộc chương:** [Chương 3: Động cơ, Biến tần & Rơ-le Quá tải (Motors & Drives)](../03_CHUONG_3_MOTORS_VA_BIEN_TAN.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Motors 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 136–139)  
**Tệp định nghĩa khối lệnh:** `P_MotorRev_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_MotorRev`:** **95** Biến đầu vào (`Input Parameters`) | **85** Biến đầu ra (`Output Parameters`) | **33** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_MotorRev` trong Studio 5000
- **Chức năng cốt lõi:** Khối điều khiển **Động cơ đảo chiều quay (Reversing Motor: Forward / Reverse / Stopped)** với cặp ngõ ra `Out_Fwd`, `Out_Rev`, cặp phản hồi `Inp_FwdFdbk`, `Inp_RevFdbk` và thời gian trễ dừng hãm bắt buộc khi đảo chiều để chống ngắn mạch và bảo vệ hộp số.
- **Khi nào CẦN sử dụng:** Điều khiển **băng tải đảo chiều, vít tải 2 chiều, tời nâng hạ, máy cán, cửa cổng công nghiệp** dùng bộ khởi động từ kép (FVR).
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi điều khiển van điện MOV có công tắc hành trình Mở/Đóng và công tắc mô-men xoắn → Dùng [`P_ValveMO.md`](./P_ValveMO.md).

### Quy trình 4 bước đấu nối và cấu hình `P_MotorRev`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_FwdRunFdbk`, `Inp_RevRunFdbk`, `Inp_FwdPermOK`, `Inp_FwdNBPermOK`, `Inp_RevPermOK`, `Inp_RevNBPermOK`, `Inp_IntlkOK`, `Inp_NBIntlkOK`, `Inp_OvldNotify`, `Inp_IOFault`, `Inp_Sim`, `Inp_Hand`, `Inp_Ovrd`, `Inp_OvrdCmd`, `Inp_ExtInh`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_HasJog`, `Cfg_HasRunFdbk`, `Cfg_UseRunFdbk`, `Cfg_AllowLocal`, `Cfg_HasFwdPermObj`, `Cfg_HasRevPermObj`, `Cfg_HasIntlkObj`, `Cfg_HasResInhObj`, `Cfg_HasRunTimeObj`, `Cfg_HasMoreObj`, `Cfg_HasOvldObj`, `Cfg_OperStopPrio`, `Cfg_ExtStopPrio`, `Cfg_OCmdResets`, `Cfg_XCmdResets`, `Cfg_OvrdPermIntlk`, `Cfg_ShedOnFailToStart`, `Cfg_ShedOnIOFault`...
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_Desc`, `Cfg_FwdText`, `Cfg_Label`, `Cfg_RevText`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_MotorRev` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_MotorRev` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PSet_Owner`, `PCmd_StartFwd`, `PCmd_StartRev`, `PCmd_Stop`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_FailToStartAck`, `PCmd_FailToStartSuppress`, `PCmd_FailToStartUnsuppress`, `PCmd_FailToStartUnshelve`, `PCmd_FailToStopAck`, `PCmd_FailToStopSuppress`, `PCmd_FailToStopUnsuppress`, `PCmd_FailToStopUnshelve`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh điều khiển tự động, kiểm tra khối đã sẵn sàng ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển quyền về Program, sau đó gán giá trị vào `PSet_*` và kích xung `PCmd_* = 1` (khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Out_RunFwd`, `Out_RunRev`, `Out_StartFwd`, `Out_StartRev`, `Out_Stop`, `Out_Horn`, `Val_Cmd`, `Val_Fdbk`, `Val_Sts`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Val_Owner`, `Sts_Stopped`, `Sts_StartingFwd`, `Sts_StartingRev`, `Sts_RunningFwd`, `Sts_RunningRev`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 95 Biến Đầu Vào (`Input Parameters`) của `P_MotorRev`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp_FwdRunFdbk`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tiếp điểm phụ phản hồi động cơ đang Chạy Thuận (1 = Đang chạy Thuận Forward) |
| **`Inp_RevRunFdbk`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tiếp điểm phụ phản hồi động cơ đang Chạy Nghịch (1 = Đang chạy Nghịch Reverse) |
| **`Inp_FwdPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Điều kiện Cho phép khởi động chiều Thuận (có thể bypass) đều OK |
| **`Inp_FwdNBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Điều kiện Cho phép khởi động chiều Thuận (không thể bypass) đều OK |
| **`Inp_RevPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Điều kiện Cho phép khởi động chiều Nghịch (có thể bypass) đều OK |
| **`Inp_RevNBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Điều kiện Cho phép khởi động chiều Nghịch (không thể bypass) đều OK |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp Khóa liên động an toàn có thể Bypass (1 = Liên động OK cho phép chạy; 0 = Cắt dừng thiết bị ngay lập tức; nối từ P_Intlk.Sts_IntlkOK) |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp Khóa liên động an toàn KHÔNG thể Bypass (1 = Liên động bất khả kháng OK; 0 = Cắt dừng thiết bị ngay lập tức; nối từ P_Intlk.Sts_NBIntlkOK) |
| **`Inp_OvldNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã mức độ ưu tiên cảnh báo và xác nhận (enum) nối từ khối Rơ-le quá tải (P_E300Ovld / P_E3Ovld / P_E1PlusE) |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi truyền thông I/O của thiết bị (0 = Tốt/OK, 1 = Lỗi truyền thông I/O Fault) |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân kích hoạt chế độ Mô phỏng (Simulation): Khi = 1, khóa các ngõ ra vật lý ở mức an toàn và dùng giá trị/phản hồi giả lập nội bộ; khi = 0, vận hành thực tế bình thường |
| **`Inp_Hand`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu chọn chế độ Điều khiển bằng tay tại chỗ (Hand/Local): 1 = Chiếm quyền sang chế độ Hand (thường nối từ công tắc cứng tại tủ MCC); 0 = Nhả chế độ Hand |
| **`Inp_Ovrd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu kích hoạt chế độ Cưỡng bức ưu tiên (Override): 1 = Chiếm quyền sang chế độ Override; 0 = Nhả chế độ Override |
| **`Inp_OvrdCmd`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã lệnh động cơ đảo chiều khi ở chế độ Cưỡng bức (Override): 0 = Không tác động, 1 = Dừng (Stop), 2 = Chạy Thuận (Forward), 3 = Chạy Nghịch (Reverse) |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân khóa cấm nguồn lệnh ngoài (External Inhibit): 1 = Cấm chế độ External chiếm quyền điều khiển; 0 = Cho phép External chiếm quyền |
| **`Inp_HornInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân khóa cấm còi báo động tại hiện trường: 1 = Cấm phát còi báo (Inhibit Horn); 0 = Cho phép phát còi báo trước khi khởi động |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Inp_IntlkAvail`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu báo điều kiện Khóa liên động sẵn sàng cho phép gọi máy trong nhóm (1 = Interlock Availability OK) |
| **`Cfg_HasJog`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép và hiển thị nút nhấn Nhấp thử (Jog) trên màn hình HMI; 0 = Khóa cấm lệnh Jog |
| **`Cfg_HasRunFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Động cơ có đấu nối tiếp điểm phụ phản hồi chạy (Inp_RunFdbk) về PLC; 0 = Không có tiếp điểm phản hồi chạy |
| **`Cfg_UseRunFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Sử dụng tiếp điểm phản hồi chạy để kiểm tra lỗi FailToStart/FailToStop; 0 = Tạm bỏ qua (Bypass bảo trì) không kiểm tra phản hồi chạy |
| **`Cfg_AllowLocal`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép khởi động/dừng động cơ tại chỗ ở tủ MCC mà không phát báo động lỗi; 0 = Chỉ cho phép điều khiển từ HMI hoặc Program |
| **`Cfg_HasFwdPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối P_Perm nối vào chân cho phép chạy Thuận (Inp_FwdPermOK) |
| **`Cfg_HasRevPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối P_Perm nối vào chân cho phép chạy Nghịch (Inp_RevPermOK) |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Khóa liên động (P_Intlk) nối vào chân Inp_IntlkOK và bật nút điều hướng mở Faceplate P_Intlk |
| **`Cfg_HasResInhObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Cấm khởi động lại (P_ResInh) đi kèm và bật nút điều hướng mở Faceplate P_ResInh |
| **`Cfg_HasRunTimeObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Đếm giờ chạy (P_RunTime) đi kèm và bật nút điều hướng mở Faceplate P_RunTime |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_HasOvldObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Rơ-le quá tải (P_E300Ovld / P_E3Ovld / P_E1PlusE) đi kèm và bật nút điều hướng mở Faceplate |
| **`Cfg_OperStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Nút nhấn Dừng của Người vận hành (OCmd_Stop) có quyền ưu tiên cao nhất, bấm dừng được ngay cả khi máy đang chạy ở chế độ Program; 0 = OCmd_Stop chỉ có tác dụng khi ở chế độ Operator/Maintenance |
| **`Cfg_ExtStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Lệnh Dừng từ hệ thống ngoài (XCmd_Stop) có hiệu lực ưu tiên ở mọi chế độ; 0 = Chỉ có hiệu lực khi ở chế độ External |
| **`Cfg_OCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khi Người vận hành ra lệnh Chạy/Mở mới (OCmd_*), khối tự động xóa luôn các chốt lỗi cũ; 0 = Bắt buộc phải nhấn nút Reset trước khi ra lệnh mới |
| **`Cfg_XCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khi hệ thống ngoài gửi lệnh Chạy/Mở mới (XCmd_*), khối tự động xóa luôn các chốt lỗi cũ; 0 = Bắt buộc phải gửi xung XCmd_Reset trước |
| **`Cfg_OvrdPermIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Chế độ Cưỡng bức (Override) được quyền bỏ qua các điều kiện Cho phép và Liên động có thể bypass; 0 = Chế độ Override vẫn phải tuân thủ mọi điều kiện |
| **`Cfg_ShedOnFailToStart`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt lệnh chạy (Shed) và phát báo động khi động cơ bị lỗi Không khởi động được (Fail to Start); 0 = Chỉ phát báo động nhưng vẫn giữ ngõ ra |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt dừng thiết bị về trạng thái an toàn (Shed) và phát báo động khi lỗi truyền thông thẻ I/O; 0 = Chỉ phát báo động |
| **`Cfg_HasFailToStartAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi không khởi động được (Fail to Start): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasFailToStopAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi không dừng được (Fail to Stop): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_FailToStartResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi không khởi động được (Fail to Start): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_FailToStopResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi không dừng được (Fail to Stop): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_FailToStartAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi không khởi động được (Fail to Start): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_FailToStopAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi không dừng được (Fail to Stop): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_FailToStartSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi không khởi động được (Fail to Start) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_FailToStopSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi không dừng được (Fail to Stop) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_ChangeDirT`** | `REAL` | `3` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ mở hết các Contactor chờ động cơ dừng hẳn trước khi đảo chiều quay Thuận/Nghịch (giây) |
| **`Cfg_StartHornT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian hú còi cảnh báo tại hiện trường (Out_Horn) trước khi đóng điện khởi động thiết bị (giây; đặt = 0.0 để tắt còi) |
| **`Cfg_SimFdbkT`** | `REAL` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ giả lập tín hiệu phản hồi chạy/đóng/mở trong chế độ Mô phỏng (Inp_Sim = 1, tính bằng giây) |
| **`Cfg_FailToStartT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian chờ tối đa (giây) từ lúc xuất lệnh Chạy đến khi phải nhận được phản hồi chạy (Inp_RunFdbk = 1) trước khi báo lỗi FailToStart |
| **`Cfg_FailToStopT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian chờ tối đa (giây) từ lúc xuất lệnh Dừng đến khi phản hồi chạy phải về 0 trước khi báo lỗi FailToStop |
| **`Cfg_MaxJogT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian tối đa cho phép giữ lệnh chạy nhấp thử Jog liên tục (giây; đặt = 0.0 là không giới hạn) |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Mã định danh Chủ sở hữu (Owner ID: ghi khác 0 để đăng ký chiếm quyền điều khiển, ghi 0 để nhả quyền) |
| **`PCmd_StartFwd`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khởi động chạy Thuận (Start Forward) |
| **`PCmd_StartRev`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khởi động chạy Nghịch (Start Reverse) |
| **`PCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Dừng (Stop) thiết bị/động cơ |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Chương trình tự động (từ Operator sang Program) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Người vận hành (từ Program sang Operator) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa giữ nguồn lệnh hiện tại (không cho phép nguồn lệnh kia giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Đưa nguồn lệnh về chế độ Bình thường mặc định (Normal: Program hoặc Operator theo Cfg_ProgNormal) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`PCmd_FailToStartAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi không khởi động được (Fail to Start) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_FailToStartSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi không khởi động được (Fail to Start) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStartUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi không khởi động được (Fail to Start) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStartUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi không khởi động được (Fail to Start) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_FailToStopAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi không dừng được (Fail to Stop) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_FailToStopSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi không dừng được (Fail to Stop) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStopUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi không dừng được (Fail to Stop) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStopUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi không dừng được (Fail to Stop) ngay lập tức (biến xung tự động xóa về 0) |
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
| **`XCmd_StartFwd`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Khởi động chạy Thuận (Start Forward) |
| **`XCmd_StartRev`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Khởi động chạy Nghịch (Start Reverse) |
| **`XCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Dừng (Stop) thiết bị/động cơ |
| **`XCmd_JogFwd`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhấp thử chạy Thuận (Jog Forward) |
| **`XCmd_JogRev`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhấp thử chạy Nghịch (Jog Reverse) |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Chiếm quyền điều khiển (Acquire) về chế độ tương ứng |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |

## 3. Bảng chi tiết 85 Biến Đầu Ra (`Output Parameters`) của `P_MotorRev`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Out_RunFwd`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra duy trì lệnh Chạy Thuận động cơ (1 = Chạy Thuận, 0 = Dừng) |
| **`Out_RunRev`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra duy trì lệnh Chạy Nghịch động cơ (1 = Chạy Nghịch, 0 = Dừng) |
| **`Out_StartFwd`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra phát xung Khởi động Thuận động cơ (1 = Kích xung chạy Thuận) |
| **`Out_StartRev`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra phát xung Khởi động Nghịch động cơ (1 = Kích xung chạy Nghịch) |
| **`Out_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra điều khiển Dừng động cơ hoặc dừng hành trình van (1 = Cấp lệnh Dừng, 0 = Giữ nguyên trạng thái) |
| **`Out_Horn`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra bật còi cảnh báo âm thanh tại hiện trường trước khi khởi động hoặc chuyển trạng thái thiết bị (1 = Bật còi) |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng dữ liệu tổng hợp cuối cùng (0 = Tín hiệu thực Tốt đã xác nhận, 1 = Tín hiệu thực Tốt giả định, 2 = Không cấu hình phản hồi/Tốt giả định, 8 = Đang mô phỏng, 9 = Mô phỏng vòng lặp, 10 = Giá trị nhập tay, 16 = Ngoài đặc tính kỹ thuật, 17 = Thay thế tại thiết bị/bus, 18 = Thay thế bởi Bảo trì, 19 = Ngắt bảo vệ giữ giá trị cũ, 20 = Ngắt bảo vệ dùng giá trị thay thế, 32 = Lỗi tín hiệu vượt dải/NaN, 33 = Lỗi kênh I/O, 34 = Lỗi module I/O, 35 = Lỗi cấu hình) |
| **`Val_Cmd`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã lệnh động cơ đảo chiều hiện hành: 0 = Không tác động, 1 = Dừng (Stop), 5 = Khởi động Thuận (Start Forward), 6 = Khởi động Nghịch (Start Reverse) |
| **`Val_Fdbk`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã phản hồi động cơ đảo chiều: 0 = Đã dừng (Stopped), 4 = Đang chạy Thuận (Running Forward), 5 = Đang chạy Nghịch (Running Reverse), 6 = Lỗi chập cả hai phản hồi (Invalid) |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái xác nhận động cơ đảo chiều: 0 = Chưa xác định, 1 = Đang dừng, 5 = Đang chạy Thuận, 6 = Đang chạy Nghịch, 7 = Đang dừng, 11 = Đang khởi động Thuận, 12 = Đang khởi động Nghịch, 32 = Ngưng phục vụ |
| **`Val_Fault`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái lỗi thiết bị (Device Fault Status — kiểu liệt kê enum: 0 = Không lỗi, 16 = Lỗi khởi động/mở, 17 = Lỗi dừng/đóng, 20 = Lo, 21 = Hi, 24 = LoLo, 25 = HiHi, 32 = Fail/I/O Fault, 34 = Lỗi cấu hình CfgErr) |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận tổng hợp của khối này cộng với các đối tượng kênh/quá tải liên kết |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms Count) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã định danh Chủ sở hữu hiện hành của đối tượng (Current Object Owner ID: 0 = Đang không bị chiếm quyền) |
| **`Sts_Stopped`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần được yêu cầu dừng và đã xác nhận Dừng hoàn toàn (Confirmed Stopped) |
| **`Sts_StartingFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần đang khởi động theo chiều Thuận và chờ phản hồi (Starting Forward) |
| **`Sts_StartingRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần đang khởi động theo chiều Nghịch và chờ phản hồi (Starting Reverse) |
| **`Sts_RunningFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần đang chạy theo chiều Thuận ổn định (Confirmed Running Forward) |
| **`Sts_RunningRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần đang chạy theo chiều Nghịch ổn định (Confirmed Running Reverse) |
| **`Sts_Stopping`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần được yêu cầu dừng và đang chờ tín hiệu phản hồi về dừng (Stopping) |
| **`Sts_JoggingFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần đang chạy nhấp thử theo chiều Thuận (Jogging Forward) |
| **`Sts_JoggingRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần đang chạy nhấp thử theo chiều Nghịch (Jogging Reverse) |
| **`Sts_Available`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị sẵn sàng cho Chương trình tự động (Program) điều khiển (đang ở chế độ Program và không có lỗi/liên động ngắt) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass hiện đang được bỏ qua (Bypassed) |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Chế độ Bypass bảo trì đang kích hoạt (gửi sang khối P_Intlk / P_Perm để cho phép bỏ qua các điều kiện có quyền Bypass) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị chưa sẵn sàng hoạt động (kiểm tra các bit Nrdy_* chi tiết để biết nguyên nhân cụ thể) |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang có lỗi thiết bị ngắt bảo vệ (Shed — yêu cầu nhấn Reset) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Khóa liên động an toàn không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi truyền thông thẻ I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Nrdy_Perm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Cho phép khởi động không thỏa mãn (Permissive Not OK) |
| **`Nrdy_PrioStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng: Đang dính lệnh Dừng ưu tiên từ Operator/External (cần nhấn Reset trước khi chạy lại) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoặc cưỡng bức bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có ít nhất một cảnh báo bị Tạm hoãn (Shelved), Vô hiệu hóa (Disabled) hoặc Ức chế (Suppressed) — hiển thị biểu tượng chữ "I" trên HMI |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị đặt bộ định thời gian (Timer Preset) nằm ngoài phạm vi hợp lệ 0..2.147.483 giây |
| **`Err_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thời gian trễ mô phỏng (Cfg_SimFdbkT / Cfg_SimRampT) nằm ngoài phạm vi 0..2.147.483 giây |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình cảnh báo: Thời gian duy trì tối thiểu (Min On Time), thời gian tạm hoãn (Shelf Time) hoặc mức độ nghiêm trọng (Severity 1..1000) không hợp lệ |
| **`Err_Pause`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thời gian tạm dừng giữa các bước (Pause Time) ngoài phạm vi 0..2.147.483 giây |
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
| **`Sts_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi không khởi động được (Fail to Start) |
| **`Alm_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi không khởi động được (Fail to Start) |
| **`Ack_FailToStart`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi không khởi động được (Fail to Start) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_FailToStartDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không khởi động được (Fail to Start) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_FailToStartShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không khởi động được (Fail to Start) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_FailToStartSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không khởi động được (Fail to Start) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi không dừng được (Fail to Stop) |
| **`Alm_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi không dừng được (Fail to Stop) |
| **`Ack_FailToStop`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi không dừng được (Fail to Stop) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_FailToStopDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không dừng được (Fail to Stop) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_FailToStopShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không dừng được (Fail to Stop) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_FailToStopSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không dừng được (Fail to Stop) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
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
| **`Sts_Horn`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Còi cảnh báo khởi động tại hiện trường đang kêu (Horn Active) |
| **`P_MotorRev`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_MotorRev) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 33 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_MotorRev`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'Reversing Motor'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_FwdText`** | `STRING_16` | `'Forward'` | [Biến cấu hình thông số] Chuỗi văn bản mô tả chiều quay Thuận (Forward Direction) trên HMI |
| **`Cfg_Label`** | `STRING_20` | `'Motor Control'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_RevText`** | `STRING_16` | `'Reverse'` | [Biến cấu hình thông số] Chuỗi văn bản mô tả chiều quay Nghịch (Reverse Direction) trên HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_MotorRev'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc nội bộ quản lý phân quyền và chuyển mạch giữa 7 nguồn lệnh điều khiển (OoS, Hand, Maint, Ovrd, Ext, Prog, Oper) |
| **`FailToStart`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi không khởi động được (Fail to Start) |
| **`FailToStop`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi không dừng được (Fail to Stop) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_MotorRev'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`IOFault`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bỏ qua (Bypass) tất cả các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Kiểm tra lại bình thường (Check / Gỡ bỏ Bypass) toàn bộ điều kiện Khóa liên động và Cho phép |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (Bỏ qua (Bypass) tất cả các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (Kiểm tra lại bình thường (Check / Gỡ bỏ Bypass) toàn bộ điều kiện Khóa liên động và Cho phép), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`OCmd_JogFwd`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Nhấp thử chạy Thuận (Jog Forward) |
| **`OCmd_JogRev`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Nhấp thử chạy Nghịch (Jog Reverse) |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo |
| **`OCmd_StartFwd`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Khởi động chạy Thuận (Start Forward) |
| **`OCmd_StartRev`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Khởi động chạy Nghịch (Start Reverse) |
| **`OCmd_Stop`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Dừng (Stop) thiết bị/động cơ |
| **`ORdy_JogFwd`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_JogFwd (Nhấp thử chạy Thuận (Jog Forward)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_JogRev`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_JogRev (Nhấp thử chạy Nghịch (Jog Reverse)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_StartFwd`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_StartFwd (Khởi động chạy Thuận (Start Forward)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_StartRev`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_StartRev (Khởi động chạy Nghịch (Start Reverse)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Stop`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Stop (Dừng (Stop) thiết bị/động cơ), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo hiện hành và trạng thái xác nhận của khối này (0 = Không có cảnh báo, 1 = Đã hết lỗi nhưng chờ Reset/Ack, 2..3 = Thấp, 4..5 = Trung bình, 6..7 = Cao, 8..9 = Khẩn cấp) |
---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_MotorHO`
**Thuộc chương:** [Chương 3: Động cơ, Biến tần & Rơ-le Quá tải (Motors & Drives)](../03_CHUONG_3_MOTORS_VA_BIEN_TAN.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Motors 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 140–142)  
**Tệp định nghĩa khối lệnh:** `P_MotorHO_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_MotorHO`:** **50** Biến đầu vào (`Input Parameters`) | **52** Biến đầu ra (`Output Parameters`) | **27** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_MotorHO` trong Studio 5000
- **Chức năng cốt lõi:** Khối giám sát **Động cơ vận hành bằng tay tại chỗ (Hand-Operated Motor)**. PLC không xuất lệnh khởi động mà chỉ đọc tiếp điểm phản hồi chạy (1 tốc độ, 2 tốc độ hoặc thuận/nghịch) để hiển thị lên SCADA và tùy chọn xuất 1 xung `Out_Trip` để ngắt khẩn cấp khi vi phạm liên động `Inp_IntlkOK`.
- **Khi nào CẦN sử dụng:** Khi có các **động cơ phụ trợ (bơm dầu bôi trơn tại chỗ, quạt thông gió nhà xưởng, máy gia công cục bộ)** khởi động bằng nút nhấn cơ khí tại tủ hiện trường nhưng cần theo dõi trạng thái và cắt liên động an toàn trên DCS.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi cần khởi động động cơ từ màn hình HMI hoặc từ chương trình PLC.

### Quy trình 4 bước đấu nối và cấu hình `P_MotorHO`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_RunFastFwdFdbk`, `Inp_SlowRevFdbk`, `Inp_IntlkOK`, `Inp_NBIntlkOK`, `Inp_OvldNotify`, `Inp_IOFault`, `Inp_Sim`, `Inp_SimRunFastFwd`, `Inp_SimSlowRev`, `Inp_SimStopped`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_HasTrip`, `Cfg_2Spd`, `Cfg_Rev`, `Cfg_HasIntlkObj`, `Cfg_HasRunTimeObj`, `Cfg_HasMoreObj`, `Cfg_HasOvldObj`, `Cfg_OCmdResets`, `Cfg_ShedOnFailToTrip`, `Cfg_ShedOnIOFault`, `Cfg_HasTripFailAlm`, `Cfg_HasIntlkTripAlm`, `Cfg_HasIOFaultAlm`, `Cfg_TripFailResetReqd`, `Cfg_IntlkTripResetReqd`, `Cfg_IOFaultResetReqd`, `Cfg_TripFailAckReqd`, `Cfg_IntlkTripAckReqd`...
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_Desc`, `Cfg_FastFwdText`, `Cfg_Label`, `Cfg_SlowRevText`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_MotorHO` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_MotorHO` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PCmd_Trip`, `PCmd_Reset`, `PCmd_TripFailAck`, `PCmd_TripFailSuppress`, `PCmd_TripFailUnsuppress`, `PCmd_TripFailUnshelve`, `PCmd_IntlkTripAck`, `PCmd_IntlkTripSuppress`, `PCmd_IntlkTripUnsuppress`, `PCmd_IntlkTripUnshelve`, `PCmd_IOFaultAck`, `PCmd_IOFaultSuppress`, `PCmd_IOFaultUnsuppress`, `PCmd_IOFaultUnshelve`
   - **Cách sử dụng trong code PLC:** Gán giá trị đặt vào `PSet_*` (nếu có) hoặc kích xung 1 chu kỳ vào các bit lệnh `PCmd_* = 1` để xóa lỗi (`PCmd_Reset`), xác nhận cảnh báo (`PCmd_*Ack`) hoặc ức chế cảnh báo (`PCmd_*Suppress`). Khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi.
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Out_Trip`, `Val_Cmd`, `Val_Fdbk`, `Val_Sts`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Sts_RunningFastFwd`, `Sts_RunningSlowRev`, `Sts_Stopped`, `Sts_Tripping`, `Sts_Bypass`, `Sts_BypActive`, `Sts_OoS`, `Sts_BadFdbk`, `Sts_NotRdy`, `Sts_MaintByp`, `Sts_AlmInh`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 50 Biến Đầu Vào (`Input Parameters`) của `P_MotorHO`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp_RunFastFwdFdbk`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu phản hồi động cơ đang Chạy (1 tốc độ), Chạy Nhanh (2 tốc độ) hoặc Chạy Thuận (đảo chiều) |
| **`Inp_SlowRevFdbk`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu phản hồi động cơ đang Chạy Chậm (2 tốc độ) hoặc Chạy Nghịch (đảo chiều) |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp Khóa liên động an toàn có thể Bypass (1 = Liên động OK cho phép chạy; 0 = Cắt dừng thiết bị ngay lập tức; nối từ P_Intlk.Sts_IntlkOK) |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp Khóa liên động an toàn KHÔNG thể Bypass (1 = Liên động bất khả kháng OK; 0 = Cắt dừng thiết bị ngay lập tức; nối từ P_Intlk.Sts_NBIntlkOK) |
| **`Inp_OvldNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã mức độ ưu tiên cảnh báo và xác nhận (enum) nối từ khối Rơ-le quá tải (P_E300Ovld / P_E3Ovld / P_E1PlusE) |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi truyền thông I/O của thiết bị (0 = Tốt/OK, 1 = Lỗi truyền thông I/O Fault) |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân kích hoạt chế độ Mô phỏng (Simulation): Khi = 1, khóa các ngõ ra vật lý ở mức an toàn và dùng giá trị/phản hồi giả lập nội bộ; khi = 0, vận hành thực tế bình thường |
| **`Inp_SimRunFastFwd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu giả lập trạng thái động cơ đang Chạy / Chạy Nhanh / Chạy Thuận khi mô phỏng (1 = Đặt về Running/Fast/Fwd) |
| **`Inp_SimSlowRev`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu giả lập trạng thái động cơ đang Chạy Chậm / Chạy Nghịch khi mô phỏng (1 = Đặt về Slow/Reverse) |
| **`Inp_SimStopped`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu giả lập trạng thái động cơ đã Dừng khi chạy mô phỏng (1 = Đặt trạng thái mô phỏng về Stopped) |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Cfg_HasTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Có đấu nối ngõ ra Out_Trip để cắt khẩn cấp thiết bị; 0 = Chỉ giám sát trạng thái thuần túy |
| **`Cfg_2Spd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Động cơ thuộc loại 2 cấp tốc độ (Nhanh, Chậm, Dừng) |
| **`Cfg_Rev`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Động cơ thuộc loại có đảo chiều quay (Thuận, Nghịch, Dừng) |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Khóa liên động (P_Intlk) nối vào chân Inp_IntlkOK và bật nút điều hướng mở Faceplate P_Intlk |
| **`Cfg_HasRunTimeObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Đếm giờ chạy (P_RunTime) đi kèm và bật nút điều hướng mở Faceplate P_RunTime |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_HasOvldObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Rơ-le quá tải (P_E300Ovld / P_E3Ovld / P_E1PlusE) đi kèm và bật nút điều hướng mở Faceplate |
| **`Cfg_OCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khi Người vận hành ra lệnh Chạy/Mở mới (OCmd_*), khối tự động xóa luôn các chốt lỗi cũ; 0 = Bắt buộc phải nhấn nút Reset trước khi ra lệnh mới |
| **`Cfg_ShedOnFailToTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tiếp tục giữ lệnh cắt Trip và báo động khi phát hiện lỗi không cắt được máy (Fail to Trip); 0 = Chỉ phát báo động |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt dừng thiết bị về trạng thái an toàn (Shed) và phát báo động khi lỗi truyền thông thẻ I/O; 0 = Chỉ phát báo động |
| **`Cfg_HasTripFailAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi không cắt được động cơ khi xuất lệnh Trip (Fail to Trip): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_TripFailResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi không cắt được động cơ khi xuất lệnh Trip (Fail to Trip): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_TripFailAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi không cắt được động cơ khi xuất lệnh Trip (Fail to Trip): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_TripFailSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi không cắt được động cơ khi xuất lệnh Trip (Fail to Trip) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_SimFdbkT`** | `REAL` | `3` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ giả lập tín hiệu phản hồi chạy/đóng/mở trong chế độ Mô phỏng (Inp_Sim = 1, tính bằng giây) |
| **`Cfg_TripFailT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian chờ tối đa (giây) sau khi xuất lệnh cắt khẩn cấp (Out_Trip = 1) để thiết bị dừng/đóng hoàn toàn trước khi báo lỗi TripFail |
| **`PCmd_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Cắt khẩn cấp (Trip) thiết bị/động cơ |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`PCmd_TripFailAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi không cắt được động cơ khi xuất lệnh Trip (Fail to Trip) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_TripFailSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi không cắt được động cơ khi xuất lệnh Trip (Fail to Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_TripFailUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi không cắt được động cơ khi xuất lệnh Trip (Fail to Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_TripFailUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi không cắt được động cơ khi xuất lệnh Trip (Fail to Trip) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) ngay lập tức (biến xung tự động xóa về 0) |

## 3. Bảng chi tiết 52 Biến Đầu Ra (`Output Parameters`) của `P_MotorHO`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Out_Trip`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra cắt khẩn cấp (Trip) đưa động cơ/van về trạng thái an toàn (1 = Cắt/Trip thiết bị) |
| **`SrcQ_IO`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng dữ liệu tổng hợp cuối cùng (0 = Tín hiệu thực Tốt đã xác nhận, 1 = Tín hiệu thực Tốt giả định, 2 = Không cấu hình phản hồi/Tốt giả định, 8 = Đang mô phỏng, 9 = Mô phỏng vòng lặp, 10 = Giá trị nhập tay, 16 = Ngoài đặc tính kỹ thuật, 17 = Thay thế tại thiết bị/bus, 18 = Thay thế bởi Bảo trì, 19 = Ngắt bảo vệ giữ giá trị cũ, 20 = Ngắt bảo vệ dùng giá trị thay thế, 32 = Lỗi tín hiệu vượt dải/NaN, 33 = Lỗi kênh I/O, 34 = Lỗi module I/O, 35 = Lỗi cấu hình) |
| **`Val_Cmd`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã lệnh động cơ vận hành tay: 0 = Không tác động, 7 = Cắt khẩn cấp (Trip) |
| **`Val_Fdbk`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã phản hồi động cơ vận hành tay: 0 = Dừng, 1 = Chạy, 2 = Chạy Nhanh, 3 = Chạy Chậm, 4 = Chạy Thuận, 5 = Chạy Nghịch, 6 = Lỗi phản hồi không hợp lệ |
| **`Val_Sts`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái xác nhận động cơ vận hành tay: 0 = Chưa xác định, 1 = Dừng, 2 = Chạy, 3 = Nhanh, 4 = Chậm, 5 = Thuận, 6 = Nghịch, 13 = Đang cắt Trip, 33 = Ngưng phục vụ |
| **`Val_Fault`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái lỗi thiết bị (Device Fault Status — kiểu liệt kê enum: 0 = Không lỗi, 16 = Lỗi khởi động/mở, 17 = Lỗi dừng/đóng, 20 = Lo, 21 = Hi, 24 = LoLo, 25 = HiHi, 32 = Fail/I/O Fault, 34 = Lỗi cấu hình CfgErr) |
| **`Val_NotifyAll`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận tổng hợp của khối này cộng với các đối tượng kênh/quá tải liên kết |
| **`Val_UnackAlmC`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms Count) |
| **`Sts_RunningFastFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ đang Chạy (1 tốc độ), Chạy Nhanh (2 tốc độ) hoặc Chạy Thuận (đảo chiều) |
| **`Sts_RunningSlowRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ đang Chạy Chậm (2 tốc độ) hoặc Chạy Nghịch (đảo chiều) |
| **`Sts_Stopped`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần được yêu cầu dừng và đã xác nhận Dừng hoàn toàn (Confirmed Stopped) |
| **`Sts_Tripping`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất lệnh cắt khẩn cấp (Trip) động cơ/van và chờ xác nhận đã dừng/đóng |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass hiện đang được bỏ qua (Bypassed) |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Chế độ Bypass bảo trì đang kích hoạt (gửi sang khối P_Intlk / P_Perm để cho phép bỏ qua các điều kiện có quyền Bypass) |
| **`Sts_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Ngưng phục vụ (Out of Service — ưu tiên cao hơn Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_BadFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Lỗi mâu thuẫn tiếp điểm phản hồi (báo đồng thời cả Chạy Nhanh và Chậm, hoặc cả Thuận và Nghịch cùng lúc) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị chưa sẵn sàng hoạt động (kiểm tra các bit Nrdy_* chi tiết để biết nguyên nhân cụ thể) |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang có lỗi thiết bị ngắt bảo vệ (Shed — yêu cầu nhấn Reset) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Khóa liên động an toàn không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi truyền thông thẻ I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Nrdy_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng: Thiết bị đang bị cắt bảo vệ quá tải (Tripped tại thiết bị hoặc do lệnh Trip) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoặc cưỡng bức bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có ít nhất một cảnh báo bị Tạm hoãn (Shelved), Vô hiệu hóa (Disabled) hoặc Ức chế (Suppressed) — hiển thị biểu tượng chữ "I" trên HMI |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị đặt bộ định thời gian (Timer Preset) nằm ngoài phạm vi hợp lệ 0..2.147.483 giây |
| **`Err_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thời gian trễ mô phỏng (Cfg_SimFdbkT / Cfg_SimRampT) nằm ngoài phạm vi 0..2.147.483 giây |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình cảnh báo: Thời gian duy trì tối thiểu (Min On Time), thời gian tạm hoãn (Shelf Time) hoặc mức độ nghiêm trọng (Severity 1..1000) không hợp lệ |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc chốt ngắt bảo vệ (Shed) đã hết lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_TripFail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi không cắt được động cơ khi xuất lệnh Trip (Fail to Trip) |
| **`Alm_TripFail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi không cắt được động cơ khi xuất lệnh Trip (Fail to Trip) |
| **`Ack_TripFail`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi không cắt được động cơ khi xuất lệnh Trip (Fail to Trip) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_TripFailDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không cắt được động cơ khi xuất lệnh Trip (Fail to Trip) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_TripFailShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không cắt được động cơ khi xuất lệnh Trip (Fail to Trip) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_TripFailSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không cắt được động cơ khi xuất lệnh Trip (Fail to Trip) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
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
| **`P_MotorHO`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_MotorHO) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 27 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_MotorHO`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'Hand Operated Motor'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_FastFwdText`** | `STRING_16` | `'Running'` | [Biến cấu hình thông số] Chuỗi văn bản hiển thị trên HMI cho trạng thái Chạy / Chạy Nhanh / Chạy Thuận |
| **`Cfg_Label`** | `STRING_20` | `'Motor Monitor'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_SlowRevText`** | `STRING_16` | `'Slow'` | [Biến cấu hình thông số] Chuỗi văn bản hiển thị trên HMI cho trạng thái Chạy Chậm / Chạy Nghịch |
| **`Cfg_Tag`** | `STRING_20` | `'P_MotorHO'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_MotorHO'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`IOFault`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bỏ qua (Bypass) tất cả các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Kiểm tra lại bình thường (Check / Gỡ bỏ Bypass) toàn bộ điều kiện Khóa liên động và Cho phép |
| **`MCmd_IS`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Đưa thiết bị trở lại trạng thái Phục vụ bình thường (In Service) |
| **`MCmd_OoS`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Đưa thiết bị vào trạng thái Ngưng phục vụ (Out of Service) |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (Bỏ qua (Bypass) tất cả các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (Kiểm tra lại bình thường (Check / Gỡ bỏ Bypass) toàn bộ điều kiện Khóa liên động và Cho phép), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_IS`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_IS (Đưa thiết bị trở lại trạng thái Phục vụ bình thường (In Service)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_OoS`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_OoS (Đưa thiết bị vào trạng thái Ngưng phục vụ (Out of Service)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo |
| **`OCmd_Trip`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Cắt khẩn cấp (Trip) thiết bị/động cơ |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Trip`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Trip (Cắt khẩn cấp (Trip) thiết bị/động cơ), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`TripFail`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi không cắt được động cơ khi xuất lệnh Trip (Fail to Trip) |
| **`Val_Notify`** | `SINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo hiện hành và trạng thái xác nhận của khối này (0 = Không có cảnh báo, 1 = Đã hết lỗi nhưng chờ Reset/Ack, 2..3 = Thấp, 4..5 = Trung bình, 6..7 = Cao, 8..9 = Khẩn cấp) |
---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_D4SD`
**Thuộc chương:** [Chương 3: Động cơ, Biến tần & Rơ-le Quá tải (Motors & Drives)](../03_CHUONG_3_MOTORS_VA_BIEN_TAN.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Motors 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 359–366)  
**Tệp định nghĩa khối lệnh:** `P_D4SD_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_D4SD`:** **116** Biến đầu vào (`Input Parameters`) | **80** Biến đầu ra (`Output Parameters`) | **33** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_D4SD` trong Studio 5000
- **Chức năng cốt lõi:** Khối điều khiển **Thiết bị Rời rạc 2, 3 hoặc 4 Trạng thái (`Discrete 2-, 3-, or 4-State Device`)**. Cho phép định nghĩa bảng chân trị gồm tối đa **4 ngõ ra (`Out_0..3` — giữ nguyên hoặc phát xung)** và **4 ngõ vào phản hồi (`Inp_Fdbk0..3`)** cho từng trạng thái hoạt động.
- **Khi nào CẦN sử dụng:** Khi điều khiển các **cụm thiết bị chấp hành tổ hợp đặc thù** không khớp với các khối chuẩn: ví dụ bộ van khí nén 3 vị trí (Đóng / Mở hé / Mở hết), cụm cửa gió Damper 4 chế độ, hoặc máy bơm có chế độ Chạy Thủy lực / Chạy Rửa / Dừng.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi thiết bị là Động cơ chuẩn (`P_Motor*`), Van chuẩn (`P_Valve*`) hoặc Thiết bị 2..8 vị trí xoay/tịnh tiến ([`P_nPos.md`](./P_nPos.md)).

### Quy trình 4 bước đấu nối và cấu hình `P_D4SD`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_FdbkA`, `Inp_FdbkB`, `Inp_FdbkC`, `Inp_FdbkD`, `Inp_DeviceFault`, `Inp_PermOK0`, `Inp_NBPermOK0`, `Inp_PermOK1`, `Inp_NBPermOK1`, `Inp_PermOK2`, `Inp_NBPermOK2`, `Inp_PermOK3`, `Inp_NBPermOK3`, `Inp_IntlkOK`, `Inp_NBIntlkOK`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_St0onShed`, `Cfg_NumStates`, `Cfg_OutSt0Write`, `Cfg_OutSt0State`, `Cfg_OutSt1Write`, `Cfg_OutSt1State`, `Cfg_OutSt2Write`, `Cfg_OutSt2State`, `Cfg_OutSt3Write`, `Cfg_OutSt3State`, `Cfg_FdbkSt0Check`, `Cfg_FdbkSt0State`, `Cfg_FdbkSt1Check`, `Cfg_FdbkSt1State`, `Cfg_FdbkSt2Check`, `Cfg_FdbkSt2State`, `Cfg_FdbkSt3Check`, `Cfg_FdbkSt3State`...
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_Desc`, `Cfg_Label`, `Cfg_St0Text`, `Cfg_St1Text`, `Cfg_St2Text`, `Cfg_St3Text`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_D4SD` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_D4SD` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PSet_Owner`, `PCmd_St0`, `PCmd_St1`, `PCmd_St2`, `PCmd_St3`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_FailAck`, `PCmd_FailSuppress`, `PCmd_FailUnsuppress`, `PCmd_FailUnshelve`, `PCmd_DeviceFaultAck`, `PCmd_DeviceFaultSuppress`, `PCmd_DeviceFaultUnsuppress`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh điều khiển tự động, kiểm tra khối đã sẵn sàng ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển quyền về Program, sau đó gán giá trị vào `PSet_*` và kích xung `PCmd_* = 1` (khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Out_Horn`, `Out_A`, `Out_B`, `Out_C`, `Out_D`, `Val_Cmd`, `Val_Sts`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Val_Owner`, `Sts_St0`, `Sts_St1`, `Sts_St2`, `Sts_St3`, `Sts_Moving`, `Sts_Available`, `Sts_Bypass`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 116 Biến Đầu Vào (`Input Parameters`) của `P_D4SD`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp_FdbkA`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu phản hồi rời rạc kênh A từ thiết bị hiện trường (dùng để đối chiếu với bảng trạng thái phản hồi Cfg_FdbkSt*) |
| **`Inp_FdbkB`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu phản hồi rời rạc kênh B từ thiết bị hiện trường (dùng để đối chiếu với bảng trạng thái phản hồi Cfg_FdbkSt*) |
| **`Inp_FdbkC`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu phản hồi rời rạc kênh C từ thiết bị hiện trường (dùng để đối chiếu với bảng trạng thái phản hồi Cfg_FdbkSt*) |
| **`Inp_FdbkD`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu phản hồi rời rạc kênh D từ thiết bị hiện trường (dùng để đối chiếu với bảng trạng thái phản hồi Cfg_FdbkSt*) |
| **`Inp_DeviceFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu báo lỗi từ thiết bị hiện trường (1 = Thiết bị có lỗi Fault, 0 = Bình thường) |
| **`Inp_PermOK0`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Các điều kiện Cho phép chuyển sang Trạng thái 0 (có thể bypass) đều thỏa mãn (OK) |
| **`Inp_NBPermOK0`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Các điều kiện Cho phép chuyển sang Trạng thái 0 (không thể bypass) đều thỏa mãn (OK) |
| **`Inp_PermOK1`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Các điều kiện Cho phép chuyển sang Trạng thái 1 (có thể bypass) đều thỏa mãn (OK) |
| **`Inp_NBPermOK1`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Các điều kiện Cho phép chuyển sang Trạng thái 1 (không thể bypass) đều thỏa mãn (OK) |
| **`Inp_PermOK2`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Các điều kiện Cho phép chuyển sang Trạng thái 2 (có thể bypass) đều thỏa mãn (OK) |
| **`Inp_NBPermOK2`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Các điều kiện Cho phép chuyển sang Trạng thái 2 (không thể bypass) đều thỏa mãn (OK) |
| **`Inp_PermOK3`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Các điều kiện Cho phép chuyển sang Trạng thái 3 (có thể bypass) đều thỏa mãn (OK) |
| **`Inp_NBPermOK3`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Các điều kiện Cho phép chuyển sang Trạng thái 3 (không thể bypass) đều thỏa mãn (OK) |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp Khóa liên động an toàn có thể Bypass (1 = Liên động OK cho phép chạy; 0 = Cắt dừng thiết bị ngay lập tức; nối từ P_Intlk.Sts_IntlkOK) |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp Khóa liên động an toàn KHÔNG thể Bypass (1 = Liên động bất khả kháng OK; 0 = Cắt dừng thiết bị ngay lập tức; nối từ P_Intlk.Sts_NBIntlkOK) |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi truyền thông I/O của thiết bị (0 = Tốt/OK, 1 = Lỗi truyền thông I/O Fault) |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân kích hoạt chế độ Mô phỏng (Simulation): Khi = 1, khóa các ngõ ra vật lý ở mức an toàn và dùng giá trị/phản hồi giả lập nội bộ; khi = 0, vận hành thực tế bình thường |
| **`Inp_Hand`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu chọn chế độ Điều khiển bằng tay tại chỗ (Hand/Local): 1 = Chiếm quyền sang chế độ Hand (thường nối từ công tắc cứng tại tủ MCC); 0 = Nhả chế độ Hand |
| **`Inp_Ovrd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu kích hoạt chế độ Cưỡng bức ưu tiên (Override): 1 = Chiếm quyền sang chế độ Override; 0 = Nhả chế độ Override |
| **`Inp_OvrdCmd`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã lệnh thiết bị khi ở chế độ Cưỡng bức (Override): 0 = Không tác động, 1 = Sang Trạng thái 0, 2 = Sang Trạng thái 1, 3 = Sang Trạng thái 2, 4 = Sang Trạng thái 3 |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân khóa cấm nguồn lệnh ngoài (External Inhibit): 1 = Cấm chế độ External chiếm quyền điều khiển; 0 = Cho phép External chiếm quyền |
| **`Inp_HornInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân khóa cấm còi báo động tại hiện trường: 1 = Cấm phát còi báo (Inhibit Horn); 0 = Cho phép phát còi báo trước khi khởi động |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Cfg_St0onShed`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Đưa thiết bị về Trạng thái 0 an toàn khi xảy ra ngắt bảo vệ (Shed); 0 = Giữ nguyên trạng thái hiện tại |
| **`Cfg_NumStates`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số lượng trạng thái vận hành của thiết bị P_D4SD (từ 2 đến 4 trạng thái) |
| **`Cfg_OutSt0Write`** | `SINT` | `2#0000_1111` | Ẩn (Tag) | [Biến cấu hình thông số] Mặt nạ 4-bit xác định những ngõ ra nào (Out_A..Out_D) được phép ghi giá trị mới khi chuyển sang Trạng thái 0 |
| **`Cfg_OutSt0State`** | `SINT` | `2#0000_0001` | Ẩn (Tag) | [Biến cấu hình thông số] Tổ hợp mức logic (Bitmask 4-bit cho Out_A..Out_D) cần xuất ra khi điều khiển thiết bị sang Trạng thái 0 |
| **`Cfg_OutSt1Write`** | `SINT` | `2#0000_1111` | Ẩn (Tag) | [Biến cấu hình thông số] Mặt nạ 4-bit xác định những ngõ ra nào (Out_A..Out_D) được phép ghi giá trị mới khi chuyển sang Trạng thái 1 |
| **`Cfg_OutSt1State`** | `SINT` | `2#0000_0010` | Ẩn (Tag) | [Biến cấu hình thông số] Tổ hợp mức logic (Bitmask 4-bit cho Out_A..Out_D) cần xuất ra khi điều khiển thiết bị sang Trạng thái 1 |
| **`Cfg_OutSt2Write`** | `SINT` | `2#0000_1111` | Ẩn (Tag) | [Biến cấu hình thông số] Mặt nạ 4-bit xác định những ngõ ra nào (Out_A..Out_D) được phép ghi giá trị mới khi chuyển sang Trạng thái 2 |
| **`Cfg_OutSt2State`** | `SINT` | `2#0000_0100` | Ẩn (Tag) | [Biến cấu hình thông số] Tổ hợp mức logic (Bitmask 4-bit cho Out_A..Out_D) cần xuất ra khi điều khiển thiết bị sang Trạng thái 2 |
| **`Cfg_OutSt3Write`** | `SINT` | `2#0000_1111` | Ẩn (Tag) | [Biến cấu hình thông số] Mặt nạ 4-bit xác định những ngõ ra nào (Out_A..Out_D) được phép ghi giá trị mới khi chuyển sang Trạng thái 3 |
| **`Cfg_OutSt3State`** | `SINT` | `2#0000_1000` | Ẩn (Tag) | [Biến cấu hình thông số] Tổ hợp mức logic (Bitmask 4-bit cho Out_A..Out_D) cần xuất ra khi điều khiển thiết bị sang Trạng thái 3 |
| **`Cfg_FdbkSt0Check`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Mặt nạ 4-bit xác định những chân phản hồi nào (Inp_FdbkA..D) bắt buộc phải kiểm tra ở Trạng thái 0 |
| **`Cfg_FdbkSt0State`** | `SINT` | `2#0000_0001` | Ẩn (Tag) | [Biến cấu hình thông số] Tổ hợp mức logic phản hồi chuẩn (Bitmask 4-bit cho Inp_FdbkA..D) để xác nhận thiết bị đã vào Trạng thái 0 |
| **`Cfg_FdbkSt1Check`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Mặt nạ 4-bit xác định những chân phản hồi nào (Inp_FdbkA..D) bắt buộc phải kiểm tra ở Trạng thái 1 |
| **`Cfg_FdbkSt1State`** | `SINT` | `2#0000_0010` | Ẩn (Tag) | [Biến cấu hình thông số] Tổ hợp mức logic phản hồi chuẩn (Bitmask 4-bit cho Inp_FdbkA..D) để xác nhận thiết bị đã vào Trạng thái 1 |
| **`Cfg_FdbkSt2Check`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Mặt nạ 4-bit xác định những chân phản hồi nào (Inp_FdbkA..D) bắt buộc phải kiểm tra ở Trạng thái 2 |
| **`Cfg_FdbkSt2State`** | `SINT` | `2#0000_0100` | Ẩn (Tag) | [Biến cấu hình thông số] Tổ hợp mức logic phản hồi chuẩn (Bitmask 4-bit cho Inp_FdbkA..D) để xác nhận thiết bị đã vào Trạng thái 2 |
| **`Cfg_FdbkSt3Check`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Mặt nạ 4-bit xác định những chân phản hồi nào (Inp_FdbkA..D) bắt buộc phải kiểm tra ở Trạng thái 3 |
| **`Cfg_FdbkSt3State`** | `SINT` | `2#0000_1000` | Ẩn (Tag) | [Biến cấu hình thông số] Tổ hợp mức logic phản hồi chuẩn (Bitmask 4-bit cho Inp_FdbkA..D) để xác nhận thiết bị đã vào Trạng thái 3 |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_HasPerm0Obj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối P_Perm nối vào chân cho phép Trạng thái 0 (Inp_PermOK0) và bật nút điều hướng tương ứng |
| **`Cfg_HasPerm1Obj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối P_Perm nối vào chân cho phép Trạng thái 1 (Inp_PermOK1) và bật nút điều hướng tương ứng |
| **`Cfg_HasPerm2Obj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối P_Perm nối vào chân cho phép Trạng thái 2 (Inp_PermOK2) và bật nút điều hướng tương ứng |
| **`Cfg_HasPerm3Obj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối P_Perm nối vào chân cho phép Trạng thái 3 (Inp_PermOK3) và bật nút điều hướng tương ứng |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Khóa liên động (P_Intlk) nối vào chân Inp_IntlkOK và bật nút điều hướng mở Faceplate P_Intlk |
| **`Cfg_OperSt0Prio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Lệnh chuyển sang Trạng thái 0 của Người vận hành (OCmd_St0) được ưu tiên chấp nhận ở mọi chế độ; 0 = Chỉ chấp nhận khi ở chế độ Operator |
| **`Cfg_ExtSt0Prio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Lệnh chuyển sang Trạng thái 0 từ bên ngoài (XCmd_St0) được ưu tiên chấp nhận ở mọi chế độ; 0 = Chỉ chấp nhận khi ở chế độ External |
| **`Cfg_OCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khi Người vận hành ra lệnh Chạy/Mở mới (OCmd_*), khối tự động xóa luôn các chốt lỗi cũ; 0 = Bắt buộc phải nhấn nút Reset trước khi ra lệnh mới |
| **`Cfg_XCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khi hệ thống ngoài gửi lệnh Chạy/Mở mới (XCmd_*), khối tự động xóa luôn các chốt lỗi cũ; 0 = Bắt buộc phải gửi xung XCmd_Reset trước |
| **`Cfg_OvrdPermIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Chế độ Cưỡng bức (Override) được quyền bỏ qua các điều kiện Cho phép và Liên động có thể bypass; 0 = Chế độ Override vẫn phải tuân thủ mọi điều kiện |
| **`Cfg_ShedOnFail`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Đưa thiết bị về trạng thái Tắt/An toàn và phát báo động khi lỗi không đạt trạng thái mong muốn; 0 = Chỉ phát báo động |
| **`Cfg_ShedOnDeviceFault`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Đưa thiết bị về Trạng thái 0 an toàn và báo động khi có lỗi thiết bị (Device Fault); 0 = Chỉ phát báo động |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt dừng thiết bị về trạng thái an toàn (Shed) và phát báo động khi lỗi truyền thông thẻ I/O; 0 = Chỉ phát báo động |
| **`Cfg_HasFailAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasDeviceFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi thiết bị hiện trường (Device Fault): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_FailResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_DeviceFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi thiết bị hiện trường (Device Fault): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_FailAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_DeviceFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi thiết bị hiện trường (Device Fault): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_FailSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_DeviceFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi thiết bị hiện trường (Device Fault) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_OutAPulseT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Độ rộng xung xuất ra chân Out_A (giây; đặt = 0.0 để duy trì ngõ ra liên tục) |
| **`Cfg_OutBPulseT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Độ rộng xung xuất ra chân Out_B (giây; đặt = 0.0 để duy trì ngõ ra liên tục) |
| **`Cfg_OutCPulseT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Độ rộng xung xuất ra chân Out_C (giây; đặt = 0.0 để duy trì ngõ ra liên tục) |
| **`Cfg_OutDPulseT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Độ rộng xung xuất ra chân Out_D (giây; đặt = 0.0 để duy trì ngõ ra liên tục) |
| **`Cfg_SimFdbkT`** | `REAL` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ giả lập tín hiệu phản hồi chạy/đóng/mở trong chế độ Mô phỏng (Inp_Sim = 1, tính bằng giây) |
| **`Cfg_FailT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian chờ tối đa (giây) sau khi ra lệnh chuyển trạng thái để thiết bị đạt tới trạng thái phản hồi mới trước khi báo lỗi |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Mã định danh Chủ sở hữu (Owner ID: ghi khác 0 để đăng ký chiếm quyền điều khiển, ghi 0 để nhả quyền) |
| **`PCmd_St0`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Đưa thiết bị về Trạng thái 0 (State 0) |
| **`PCmd_St1`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Đưa thiết bị sang Trạng thái 1 (State 1) |
| **`PCmd_St2`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Đưa thiết bị sang Trạng thái 2 (State 2) |
| **`PCmd_St3`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Đưa thiết bị sang Trạng thái 3 (State 3) |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Chương trình tự động (từ Operator sang Program) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Người vận hành (từ Program sang Operator) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa giữ nguồn lệnh hiện tại (không cho phép nguồn lệnh kia giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Đưa nguồn lệnh về chế độ Bình thường mặc định (Normal: Program hoặc Operator theo Cfg_ProgNormal) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`PCmd_FailAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_FailSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_DeviceFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi thiết bị hiện trường (Device Fault) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_DeviceFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi thiết bị hiện trường (Device Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_DeviceFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi thiết bị hiện trường (Device Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_DeviceFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi thiết bị hiện trường (Device Fault) ngay lập tức (biến xung tự động xóa về 0) |
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
| **`XCmd_St0`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Đưa thiết bị về Trạng thái 0 (State 0) |
| **`XCmd_St1`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Đưa thiết bị sang Trạng thái 1 (State 1) |
| **`XCmd_St2`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Đưa thiết bị sang Trạng thái 2 (State 2) |
| **`XCmd_St3`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Đưa thiết bị sang Trạng thái 3 (State 3) |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`Cfg_HornOnChange`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 0 = Chỉ hú còi báo động khi bắt đầu cấp điện khởi động; 1 = Hú còi báo động trước mọi lần chuyển đổi trạng thái/vị trí |
| **`Cfg_StartHornT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian hú còi cảnh báo tại hiện trường (Out_Horn) trước khi đóng điện khởi động thiết bị (giây; đặt = 0.0 để tắt còi) |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Chiếm quyền điều khiển (Acquire) về chế độ tương ứng |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |

## 3. Bảng chi tiết 80 Biến Đầu Ra (`Output Parameters`) của `P_D4SD`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Out_Horn`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra bật còi cảnh báo âm thanh tại hiện trường trước khi khởi động hoặc chuyển trạng thái thiết bị (1 = Bật còi) |
| **`Out_A`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra vật lý A điều khiển thiết bị chấp hành (1 = Cấp điện/Kích hoạt, 0 = Ngắt điện) |
| **`Out_B`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra vật lý B điều khiển thiết bị chấp hành (1 = Cấp điện/Kích hoạt, 0 = Ngắt điện) |
| **`Out_C`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra vật lý C điều khiển thiết bị chấp hành (1 = Cấp điện/Kích hoạt, 0 = Ngắt điện) |
| **`Out_D`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra vật lý D điều khiển thiết bị chấp hành (1 = Cấp điện/Kích hoạt, 0 = Ngắt điện) |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng dữ liệu tổng hợp cuối cùng (0 = Tín hiệu thực Tốt đã xác nhận, 1 = Tín hiệu thực Tốt giả định, 2 = Không cấu hình phản hồi/Tốt giả định, 8 = Đang mô phỏng, 9 = Mô phỏng vòng lặp, 10 = Giá trị nhập tay, 16 = Ngoài đặc tính kỹ thuật, 17 = Thay thế tại thiết bị/bus, 18 = Thay thế bởi Bảo trì, 19 = Ngắt bảo vệ giữ giá trị cũ, 20 = Ngắt bảo vệ dùng giá trị thay thế, 32 = Lỗi tín hiệu vượt dải/NaN, 33 = Lỗi kênh I/O, 34 = Lỗi module I/O, 35 = Lỗi cấu hình) |
| **`Val_Cmd`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã lệnh điều khiển thiết bị hiện hành: 0 = Không tác động, 1 = Trạng thái 0, 2 = Trạng thái 1, 3 = Trạng thái 2, 4 = Trạng thái 3 |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái xác nhận của thiết bị: 0 = Khởi động/Reset, 1 = Trạng thái 0, 2 = Trạng thái 1, 3 = Trạng thái 2, 4 = Trạng thái 3, 9 = Đang chuyển trạng thái (Moving), 33 = Ngưng phục vụ |
| **`Val_Fault`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái lỗi thiết bị (Device Fault Status — kiểu liệt kê enum: 0 = Không lỗi, 16 = Lỗi khởi động/mở, 17 = Lỗi dừng/đóng, 20 = Lo, 21 = Hi, 24 = LoLo, 25 = HiHi, 32 = Fail/I/O Fault, 34 = Lỗi cấu hình CfgErr) |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận tổng hợp của khối này cộng với các đối tượng kênh/quá tải liên kết |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms Count) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã định danh Chủ sở hữu hiện hành của đối tượng (Current Object Owner ID: 0 = Đang không bị chiếm quyền) |
| **`Sts_St0`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị được lệnh và đã xác nhận đang ở Trạng thái 0 (Confirmed in State 0) |
| **`Sts_St1`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị được lệnh và đã xác nhận đang ở Trạng thái 1 (Confirmed in State 1) |
| **`Sts_St2`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị được lệnh và đã xác nhận đang ở Trạng thái 2 (Confirmed in State 2) |
| **`Sts_St3`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị được lệnh và đã xác nhận đang ở Trạng thái 3 (Confirmed in State 3) |
| **`Sts_Moving`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị đang trong quá trình chuyển trạng thái (chưa nhận được phản hồi xác nhận đã tới trạng thái đích) |
| **`Sts_Available`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị sẵn sàng cho Chương trình tự động (Program) điều khiển (đang ở chế độ Program và không có lỗi/liên động ngắt) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass hiện đang được bỏ qua (Bypassed) |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Chế độ Bypass bảo trì đang kích hoạt (gửi sang khối P_Intlk / P_Perm để cho phép bỏ qua các điều kiện có quyền Bypass) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị chưa sẵn sàng hoạt động (kiểm tra các bit Nrdy_* chi tiết để biết nguyên nhân cụ thể) |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_EqpFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi thiết bị chấp hành ngoài (External Equipment Fault — cần nhấn Reset) |
| **`Nrdy_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang có lỗi thiết bị ngắt bảo vệ (Shed — yêu cầu nhấn Reset) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Khóa liên động an toàn không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi truyền thông thẻ I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Nrdy_PrioSt0`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng: Đang dính lệnh đưa về Trạng thái 0 ưu tiên từ Operator/External (cần nhấn Reset) |
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
| **`Sts_Ext`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Điều khiển ngoài (External — ưu tiên cao hơn Prog, Oper) |
| **`Sts_Prog`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Chương trình tự động (Program) |
| **`Sts_ProgLocked`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Chương trình tự động và đã Khóa (Program Locked — không cho Operator lấy quyền) |
| **`Sts_Oper`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Người vận hành trên HMI (Operator) |
| **`Sts_OperLocked`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Người vận hành và đã Khóa (Operator Locked — không cho Program lấy quyền) |
| **`Sts_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Nguồn lệnh hiện hành đang khớp với nguồn lệnh Bình thường mặc định (Program hoặc Operator theo Cfg_ProgNormal) |
| **`Sts_ExtReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Yêu cầu chuyển sang chế độ External đang bị khóa cấm (không thể chuyển sang External từ trạng thái hiện tại) |
| **`Sts_ProgReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Yêu cầu chuyển sang chế độ Program đang bị khóa cấm (không thể chuyển sang Program từ trạng thái hiện tại) |
| **`Sts_MAcqRcvd`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đã nhận được lệnh Chiếm quyền bảo trì (MCmd_Acq) trong chu kỳ quét hiện tại |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc chốt ngắt bảo vệ (Shed) đã hết lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi tín hiệu / hỏng kênh đo (Signal Failure) |
| **`Alm_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi tín hiệu / hỏng kênh đo (Signal Failure) |
| **`Ack_Fail`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi tín hiệu / hỏng kênh đo (Signal Failure) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_FailDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_FailShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_FailSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi tín hiệu / hỏng kênh đo (Signal Failure) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_DeviceFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi thiết bị hiện trường (Device Fault) |
| **`Alm_DeviceFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi thiết bị hiện trường (Device Fault) |
| **`Ack_DeviceFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi thiết bị hiện trường (Device Fault) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_DeviceFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi thiết bị hiện trường (Device Fault) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_DeviceFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi thiết bị hiện trường (Device Fault) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_DeviceFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi thiết bị hiện trường (Device Fault) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
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
| **`P_D4SD`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_D4SD) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |
| **`Sts_Horn`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Còi cảnh báo khởi động tại hiện trường đang kêu (Horn Active) |

## 4. Bảng chi tiết 33 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_D4SD`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'Discrete 2, 3 or 4 S'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_Label`** | `STRING_20` | `'Discrete Device'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_St0Text`** | `STRING_8` | `'Off'` | [Biến cấu hình thông số] Chuỗi văn bản mô tả tên Trạng thái 0 (State 0) hiển thị trên màn hình HMI |
| **`Cfg_St1Text`** | `STRING_8` | `'On'` | [Biến cấu hình thông số] Chuỗi văn bản mô tả tên Trạng thái 1 (State 1) hiển thị trên màn hình HMI |
| **`Cfg_St2Text`** | `STRING_8` | `''` | [Biến cấu hình thông số] Chuỗi văn bản mô tả tên Trạng thái 2 (State 2) hiển thị trên màn hình HMI |
| **`Cfg_St3Text`** | `STRING_8` | `''` | [Biến cấu hình thông số] Chuỗi văn bản mô tả tên Trạng thái 3 (State 3) hiển thị trên màn hình HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_D4SD'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc nội bộ quản lý phân quyền và chuyển mạch giữa 7 nguồn lệnh điều khiển (OoS, Hand, Maint, Ovrd, Ext, Prog, Oper) |
| **`DeviceFault`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi thiết bị hiện trường (Device Fault) |
| **`Fail`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi tín hiệu / hỏng kênh đo (Signal Failure) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_D4SD'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`IOFault`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bỏ qua (Bypass) tất cả các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Kiểm tra lại bình thường (Check / Gỡ bỏ Bypass) toàn bộ điều kiện Khóa liên động và Cho phép |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (Bỏ qua (Bypass) tất cả các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (Kiểm tra lại bình thường (Check / Gỡ bỏ Bypass) toàn bộ điều kiện Khóa liên động và Cho phép), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo |
| **`OCmd_St0`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Đưa thiết bị về Trạng thái 0 (State 0) |
| **`OCmd_St1`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Đưa thiết bị sang Trạng thái 1 (State 1) |
| **`OCmd_St2`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Đưa thiết bị sang Trạng thái 2 (State 2) |
| **`OCmd_St3`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Đưa thiết bị sang Trạng thái 3 (State 3) |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_St0`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_St0 (Đưa thiết bị về Trạng thái 0 (State 0)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_St1`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_St1 (Đưa thiết bị sang Trạng thái 1 (State 1)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_St2`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_St2 (Đưa thiết bị sang Trạng thái 2 (State 2)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_St3`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_St3 (Đưa thiết bị sang Trạng thái 3 (State 3)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo hiện hành và trạng thái xác nhận của khối này (0 = Không có cảnh báo, 1 = Đã hết lỗi nhưng chờ Reset/Ack, 2..3 = Thấp, 4..5 = Trung bình, 6..7 = Cao, 8..9 = Khẩn cấp) |
---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_PF52x`
**Thuộc chương:** [Chương 3: Động cơ, Biến tần & Rơ-le Quá tải (Motors & Drives)](../03_CHUONG_3_MOTORS_VA_BIEN_TAN.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Motors 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 143–157)  
**Tệp định nghĩa khối lệnh:** `P_PF52x_4.10.00_RUNG.L5X`  
**Thống kê số lượng biến của `P_PF52x`:** **121** Biến đầu vào (`Input Parameters`) | **118** Biến đầu ra (`Output Parameters`) | **43** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_PF52x` trong Studio 5000
- **Chức năng cốt lõi:** Khối điều khiển chuyên dụng cho biến tần hạ thế **Allen-Bradley PowerFlex 523 và PowerFlex 525** qua mạng EtherNet/IP. Điều khiển Chạy Thuận/Nghịch, Jog, đặt tốc độ `SpeedRef`, đọc tần số thực, dòng điện (`Output Current`), công suất (`Output Power`) và tự động dịch mã lỗi biến tần ra văn bản qua bảng `PF525_FaultCodeList`.
- **Khi nào CẦN sử dụng:** Khi dự án sử dụng biến tần **PowerFlex 523 hoặc PowerFlex 525** kết nối mạng EtherNet/IP với PLC Logix (nhập trực tiếp file `P_PF52x_4.10.00_RUNG.L5X` đã cấu hình sẵn Datalinks).
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi dùng biến tần dòng PowerFlex 750 (`P_PF753`/`P_PF755`) hoặc biến tần hãng khác ([`P_VSD.md`](./P_VSD.md)).

### Quy trình 4 bước đấu nối và cấu hình `P_PF52x`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp`, `Out`, `Ref_FaultCodeList`, `Inp_FwdPermOK`, `Inp_FwdNBPermOK`, `Inp_RevPermOK`, `Inp_RevNBPermOK`, `Inp_IntlkOK`, `Inp_NBIntlkOK`, `Inp_IOFault`, `Inp_Sim`, `Inp_Hand`, `Inp_Ovrd`, `Inp_OvrdCmd`, `Inp_OvrdSpeed`
   - **Các chân tham chiếu dữ liệu (`Ref_*` / `InOut`):** `Ref_FaultCodeList`, `Ref_FaultCodeList` — bắt buộc gán thẻ mảng/cấu trúc tương ứng trong Controller Tags.
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_HasReverse`, `Cfg_HasJog`, `Cfg_AllowLocal`, `Cfg_HasFwdPermObj`, `Cfg_HasRevPermObj`, `Cfg_HasIntlkObj`, `Cfg_HasResInhObj`, `Cfg_HasRunTimeObj`, `Cfg_HasMoreObj`, `Cfg_SetTrack`, `Cfg_SetTrackOvrdHand`, `Cfg_OperStopPrio`, `Cfg_ExtStopPrio`, `Cfg_OCmdResets`, `Cfg_XCmdResets`, `Cfg_OvrdPermIntlk`, `Cfg_ShedOnFailToStart`, `Cfg_ShedOnIOFault`...
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_Desc`, `Cfg_DigIn1Txt`, `Cfg_DigIn2Txt`, `Cfg_DigIn3Txt`, `Cfg_DigIn4Txt`, `Cfg_FwdText`, `Cfg_Label`, `Cfg_RevText`, `Cfg_SpeedFdbkEU`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_PF52x` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_PF52x` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PSet_SpeedRef`, `PSet_Owner`, `PCmd_StartFwd`, `PCmd_StartRev`, `PCmd_Stop`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_FailToStartAck`, `PCmd_FailToStartSuppress`, `PCmd_FailToStartUnsuppress`, `PCmd_FailToStartUnshelve`, `PCmd_FailToStopAck`, `PCmd_FailToStopSuppress`, `PCmd_FailToStopUnsuppress`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh điều khiển tự động, kiểm tra khối đã sẵn sàng ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển quyền về Program, sau đó gán giá trị vào `PSet_*` và kích xung `PCmd_* = 1` (khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Out_Horn`, `Val_SpeedRef`, `Val_SpeedFdbk`, `Val_SpeedRefRaw`, `Val_SpeedFdbkRaw`, `Val_SpeedRefEUMin`, `Val_SpeedRefEUMax`, `Val_SpeedFdbkEUMin`, `Val_SpeedFdbkEUMax`, `Val_OutCurrent`, `Val_OutPower`, `Val_LastFaultCode`, `Val_Cmd`, `Val_Fdbk`, `Val_Sts`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 121 Biến Đầu Vào (`Input Parameters`) của `P_PF52x`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp`** | `P_PF52x_Inp` | `-` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu điều kiện đầu vào (1 = Kích hoạt điều kiện / Báo động, 0 = Bình thường) |
| **`Out`** | `P_PF52x_Out` | `-` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra điều khiển thiết bị (hoặc kết quả cổng điều kiện sau thời gian trễ On-Delay / Off-Delay) |
| **`Ref_FaultCodeList`** | `P_DescList[2]` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới mảng bảng tra cứu chứa danh sách Mã lỗi (DINT) và Chuỗi mô tả lỗi (STRING) của biến tần/khởi động mềm |
| **`Inp_FwdPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Điều kiện Cho phép khởi động chiều Thuận (có thể bypass) đều OK |
| **`Inp_FwdNBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Điều kiện Cho phép khởi động chiều Thuận (không thể bypass) đều OK |
| **`Inp_RevPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Điều kiện Cho phép khởi động chiều Nghịch (có thể bypass) đều OK |
| **`Inp_RevNBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Điều kiện Cho phép khởi động chiều Nghịch (không thể bypass) đều OK |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp Khóa liên động an toàn có thể Bypass (1 = Liên động OK cho phép chạy; 0 = Cắt dừng thiết bị ngay lập tức; nối từ P_Intlk.Sts_IntlkOK) |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp Khóa liên động an toàn KHÔNG thể Bypass (1 = Liên động bất khả kháng OK; 0 = Cắt dừng thiết bị ngay lập tức; nối từ P_Intlk.Sts_NBIntlkOK) |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi truyền thông I/O của thiết bị (0 = Tốt/OK, 1 = Lỗi truyền thông I/O Fault) |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân kích hoạt chế độ Mô phỏng (Simulation): Khi = 1, khóa các ngõ ra vật lý ở mức an toàn và dùng giá trị/phản hồi giả lập nội bộ; khi = 0, vận hành thực tế bình thường |
| **`Inp_Hand`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu chọn chế độ Điều khiển bằng tay tại chỗ (Hand/Local): 1 = Chiếm quyền sang chế độ Hand (thường nối từ công tắc cứng tại tủ MCC); 0 = Nhả chế độ Hand |
| **`Inp_Ovrd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu kích hoạt chế độ Cưỡng bức ưu tiên (Override): 1 = Chiếm quyền sang chế độ Override; 0 = Nhả chế độ Override |
| **`Inp_OvrdCmd`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã lệnh điều khiển thiết bị khi ở chế độ Cưỡng bức (Override Command — kiểu số nguyên phụ thuộc vào từng loại thiết bị: 0 = Không tác động, 1 = Dừng/Đóng/Tắt, 2 = Chạy/Mở/Bật...) |
| **`Inp_OvrdSpeed`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tốc độ đặt yêu cầu cho biến tần khi ở chế độ Cưỡng bức (Override Speed Reference, đơn vị EU) |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân khóa cấm nguồn lệnh ngoài (External Inhibit): 1 = Cấm chế độ External chiếm quyền điều khiển; 0 = Cho phép External chiếm quyền |
| **`Inp_HornInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân khóa cấm còi báo động tại hiện trường: 1 = Cấm phát còi báo (Inhibit Horn); 0 = Cho phép phát còi báo trước khi khởi động |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Cfg_HasReverse`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép biến tần chạy cả 2 chiều Thuận và Nghịch; 0 = Chỉ cho phép chạy 1 chiều Thuận (Forward Only) |
| **`Cfg_HasJog`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép và hiển thị nút nhấn Nhấp thử (Jog) trên màn hình HMI; 0 = Khóa cấm lệnh Jog |
| **`Cfg_AllowLocal`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép khởi động/dừng động cơ tại chỗ ở tủ MCC mà không phát báo động lỗi; 0 = Chỉ cho phép điều khiển từ HMI hoặc Program |
| **`Cfg_HasFwdPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối P_Perm nối vào chân cho phép chạy Thuận (Inp_FwdPermOK) |
| **`Cfg_HasRevPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối P_Perm nối vào chân cho phép chạy Nghịch (Inp_RevPermOK) |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Khóa liên động (P_Intlk) nối vào chân Inp_IntlkOK và bật nút điều hướng mở Faceplate P_Intlk |
| **`Cfg_HasResInhObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Cấm khởi động lại (P_ResInh) đi kèm và bật nút điều hướng mở Faceplate P_ResInh |
| **`Cfg_HasRunTimeObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Đếm giờ chạy (P_RunTime) đi kèm và bật nút điều hướng mở Faceplate P_RunTime |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_SetTrack`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép các giá trị đặt (PSet_CV / OSet_CV / SP) tự động bám theo ngõ ra thực tế hiện hành để chuyển mạch không giật (Bumpless Transfer) giữa Operator và Program |
| **`Cfg_SetTrackOvrdHand`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép giá trị đặt của Program và Operator tự động bám theo giá trị CV khi đang ở chế độ Override hoặc Hand |
| **`Cfg_OperStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Nút nhấn Dừng của Người vận hành (OCmd_Stop) có quyền ưu tiên cao nhất, bấm dừng được ngay cả khi máy đang chạy ở chế độ Program; 0 = OCmd_Stop chỉ có tác dụng khi ở chế độ Operator/Maintenance |
| **`Cfg_ExtStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Lệnh Dừng từ hệ thống ngoài (XCmd_Stop) có hiệu lực ưu tiên ở mọi chế độ; 0 = Chỉ có hiệu lực khi ở chế độ External |
| **`Cfg_OCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khi Người vận hành ra lệnh Chạy/Mở mới (OCmd_*), khối tự động xóa luôn các chốt lỗi cũ; 0 = Bắt buộc phải nhấn nút Reset trước khi ra lệnh mới |
| **`Cfg_XCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khi hệ thống ngoài gửi lệnh Chạy/Mở mới (XCmd_*), khối tự động xóa luôn các chốt lỗi cũ; 0 = Bắt buộc phải gửi xung XCmd_Reset trước |
| **`Cfg_OvrdPermIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Chế độ Cưỡng bức (Override) được quyền bỏ qua các điều kiện Cho phép và Liên động có thể bypass; 0 = Chế độ Override vẫn phải tuân thủ mọi điều kiện |
| **`Cfg_ShedOnFailToStart`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt lệnh chạy (Shed) và phát báo động khi động cơ bị lỗi Không khởi động được (Fail to Start); 0 = Chỉ phát báo động nhưng vẫn giữ ngõ ra |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt dừng thiết bị về trạng thái an toàn (Shed) và phát báo động khi lỗi truyền thông thẻ I/O; 0 = Chỉ phát báo động |
| **`Cfg_SimScaleEU`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Trong chế độ mô phỏng, quy đổi tốc độ đặt sang tốc độ phản hồi theo đơn vị kỹ thuật EU |
| **`Cfg_SimScaleRaw`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Trong chế độ mô phỏng, quy đổi tốc độ đặt sang tốc độ phản hồi theo đơn vị thô Raw |
| **`Cfg_DecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho tốc độ/giá trị trên màn hình HMI (từ 0 đến 6 chữ số) |
| **`Cfg_HasFailToStartAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi không khởi động được (Fail to Start): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasFailToStopAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi không dừng được (Fail to Stop): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasDriveFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi biến tần / bộ truyền động (Drive Fault): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_FailToStartResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi không khởi động được (Fail to Start): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_FailToStopResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi không dừng được (Fail to Stop): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_DriveFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi biến tần / bộ truyền động (Drive Fault): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_FailToStartAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi không khởi động được (Fail to Start): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_FailToStopAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi không dừng được (Fail to Stop): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_DriveFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi biến tần / bộ truyền động (Drive Fault): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_FailToStartSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi không khởi động được (Fail to Start) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_FailToStopSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi không dừng được (Fail to Stop) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_DriveFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_MinSpdRef`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp dưới tối thiểu cho phép đặt của tốc độ biến tần (Minimum Speed Reference, đơn vị EU) |
| **`Cfg_MaxSpdRef`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp trên tối đa cho phép đặt của tốc độ biến tần (Maximum Speed Reference, đơn vị EU) |
| **`Cfg_SpeedRefRawMin`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị thô tối thiểu của tốc độ đặt gửi xuống biến tần (Speed Reference Raw Min) |
| **`Cfg_SpeedRefRawMax`** | `DINT` | `6000` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị thô tối đa của tốc độ đặt gửi xuống biến tần (Speed Reference Raw Max) |
| **`Cfg_SpeedRefEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tối thiểu của tốc độ đặt (Speed Reference EU Min) |
| **`Cfg_SpeedRefEUMax`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tối đa của tốc độ đặt (Speed Reference EU Max) |
| **`Cfg_SpeedFdbkRawMin`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị thô tối thiểu của tốc độ phản hồi từ biến tần (Speed Feedback Raw Min) |
| **`Cfg_SpeedFdbkRawMax`** | `DINT` | `6000` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị thô tối đa của tốc độ phản hồi từ biến tần (Speed Feedback Raw Max) |
| **`Cfg_SpeedFdbkEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tối thiểu của tốc độ phản hồi (Speed Feedback EU Min) |
| **`Cfg_SpeedFdbkEUMax`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tối đa của tốc độ phản hồi (Speed Feedback EU Max) |
| **`Cfg_StartHornT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian hú còi cảnh báo tại hiện trường (Out_Horn) trước khi đóng điện khởi động thiết bị (giây; đặt = 0.0 để tắt còi) |
| **`Cfg_SimRampT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian tăng/giảm tốc độ phản hồi giả lập từ 0 đến 100% trong chế độ Mô phỏng biến tần (giây) |
| **`Cfg_FailToStartT`** | `REAL` | `15` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian chờ tối đa (giây) từ lúc xuất lệnh Chạy đến khi phải nhận được phản hồi chạy (Inp_RunFdbk = 1) trước khi báo lỗi FailToStart |
| **`Cfg_FailToStopT`** | `REAL` | `15` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian chờ tối đa (giây) từ lúc xuất lệnh Dừng đến khi phản hồi chạy phải về 0 trước khi báo lỗi FailToStop |
| **`Cfg_ResetPulseT`** | `REAL` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Độ rộng xung xuất ra chân xóa lỗi biến tần/khởi động mềm (giây) |
| **`Cfg_MaxJogT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian tối đa cho phép giữ lệnh chạy nhấp thử Jog liên tục (giây; đặt = 0.0 là không giới hạn) |
| **`Cfg_OperKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ bit cấu hình cho phép Operator giữ quyền chỉnh thông số ngay cả khi khối ở chế độ Program (.0 = Giữ quyền chọn kênh PV, .1 = Giữ quyền chỉnh ngưỡng cảnh báo) |
| **`Cfg_ProgKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ bit cấu hình cho phép Program giữ quyền chỉnh thông số ngay cả khi khối ở chế độ Operator (.0 = Giữ quyền chọn kênh PV, .1 = Giữ quyền chỉnh ngưỡng cảnh báo) |
| **`Cfg_ExtKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ bit cấu hình cho phép External giữ quyền điều khiển khi ở Program/Operator (.0 = Giữ tốc độ đặt Reference, .1 = Giữ lệnh điều khiển Commands) |
| **`PSet_SpeedRef`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Giá trị tốc độ đặt yêu cầu cho biến tần (Speed Reference, tính theo đơn vị kỹ thuật EU như Hz, RPM hoặc %) |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Mã định danh Chủ sở hữu (Owner ID: ghi khác 0 để đăng ký chiếm quyền điều khiển, ghi 0 để nhả quyền) |
| **`XSet_SpeedRef`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài] Giá trị đặt từ Hệ thống điều khiển ngoài (External): Giá trị tốc độ đặt yêu cầu cho biến tần (Speed Reference, tính theo đơn vị kỹ thuật EU như Hz, RPM hoặc %) |
| **`PCmd_StartFwd`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khởi động chạy Thuận (Start Forward) |
| **`PCmd_StartRev`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khởi động chạy Nghịch (Start Reverse) |
| **`PCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Dừng (Stop) thiết bị/động cơ |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Chương trình tự động (từ Operator sang Program) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Người vận hành (từ Program sang Operator) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa giữ nguồn lệnh hiện tại (không cho phép nguồn lệnh kia giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Đưa nguồn lệnh về chế độ Bình thường mặc định (Normal: Program hoặc Operator theo Cfg_ProgNormal) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`PCmd_FailToStartAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi không khởi động được (Fail to Start) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_FailToStartSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi không khởi động được (Fail to Start) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStartUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi không khởi động được (Fail to Start) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStartUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi không khởi động được (Fail to Start) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_FailToStopAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi không dừng được (Fail to Stop) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_FailToStopSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi không dừng được (Fail to Stop) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStopUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi không dừng được (Fail to Stop) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStopUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi không dừng được (Fail to Stop) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_DriveFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_DriveFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_DriveFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_DriveFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) ngay lập tức (biến xung tự động xóa về 0) |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`XCmd_StartFwd`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Khởi động chạy Thuận (Start Forward) |
| **`XCmd_StartRev`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Khởi động chạy Nghịch (Start Reverse) |
| **`XCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Dừng (Stop) thiết bị/động cơ |
| **`XCmd_JogFwd`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhấp thử chạy Thuận (Jog Forward) |
| **`XCmd_JogRev`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhấp thử chạy Nghịch (Jog Reverse) |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Chiếm quyền điều khiển (Acquire) về chế độ tương ứng |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |

## 3. Bảng chi tiết 118 Biến Đầu Ra (`Output Parameters`) của `P_PF52x`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Inp`** | `P_PF52x_Inp` | `-` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu điều kiện đầu vào (1 = Kích hoạt điều kiện / Báo động, 0 = Bình thường) |
| **`Out`** | `P_PF52x_Out` | `-` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra điều khiển thiết bị (hoặc kết quả cổng điều kiện sau thời gian trễ On-Delay / Off-Delay) |
| **`Ref_FaultCodeList`** | `P_DescList[2]` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới mảng bảng tra cứu chứa danh sách Mã lỗi (DINT) và Chuỗi mô tả lỗi (STRING) của biến tần/khởi động mềm |
| **`Out_Horn`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra bật còi cảnh báo âm thanh tại hiện trường trước khi khởi động hoặc chuyển trạng thái thiết bị (1 = Bật còi) |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng dữ liệu tổng hợp cuối cùng (0 = Tín hiệu thực Tốt đã xác nhận, 1 = Tín hiệu thực Tốt giả định, 2 = Không cấu hình phản hồi/Tốt giả định, 8 = Đang mô phỏng, 9 = Mô phỏng vòng lặp, 10 = Giá trị nhập tay, 16 = Ngoài đặc tính kỹ thuật, 17 = Thay thế tại thiết bị/bus, 18 = Thay thế bởi Bảo trì, 19 = Ngắt bảo vệ giữ giá trị cũ, 20 = Ngắt bảo vệ dùng giá trị thay thế, 32 = Lỗi tín hiệu vượt dải/NaN, 33 = Lỗi kênh I/O, 34 = Lỗi module I/O, 35 = Lỗi cấu hình) |
| **`Val_SpeedRef`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị tốc độ đặt (Speed Reference Target) đang gửi xuống biến tần (đơn vị kỹ thuật EU) |
| **`Val_SpeedFdbk`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị tốc độ phản hồi thực tế (Actual Speed Feedback) đọc từ biến tần (đơn vị kỹ thuật EU) |
| **`Val_SpeedRefRaw`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Bản sao giá trị tốc độ đặt gửi xuống biến tần tính theo đơn vị thô (Raw Units) để hiển thị trên HMI Faceplate |
| **`Val_SpeedFdbkRaw`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Bản sao giá trị tốc độ phản hồi từ biến tần tính theo đơn vị thô (Raw Units) để hiển thị trên HMI Faceplate |
| **`Val_SpeedRefEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận dưới của dải tốc độ đặt (Speed Reference EU Min) |
| **`Val_SpeedRefEUMax`** | `REAL` | `130` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận trên của dải tốc độ đặt (Speed Reference EU Max) |
| **`Val_SpeedFdbkEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận dưới của dải tốc độ phản hồi (Speed Feedback EU Min) |
| **`Val_SpeedFdbkEUMax`** | `REAL` | `130` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận trên của dải tốc độ phản hồi (Speed Feedback EU Max) |
| **`Val_OutCurrent`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Dòng điện ngõ ra thực tế của biến tần (Output Current, đơn vị Ampe) |
| **`Val_OutPower`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Công suất ngõ ra thực tế của biến tần (Output Power, đơn vị kW) |
| **`Val_LastFaultCode`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã lỗi gần nhất của biến tần (Last Drive Fault Code) |
| **`Val_Cmd`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã lệnh điều khiển thiết bị đang xuất ra (Device Command enum: 0 = Không tác động/Tắt, 1 = Dừng/Đóng/Off, 2 = Chạy/Mở/On...) |
| **`Val_Fdbk`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái phản hồi thực tế từ thiết bị hiện trường (Device Feedback enum: 0 = Đang dừng/Đang chuyển trạng thái, 1 = Đang chạy/Đóng/Off, 2 = Mở/On, 3 = Phản hồi không hợp lệ) |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái hoạt động xác nhận của thiết bị (Device Confirmed Status enum dùng để điều khiển màu sắc và biểu tượng động trên HMI) |
| **`Val_Fault`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái lỗi thiết bị (Device Fault Status — kiểu liệt kê enum: 0 = Không lỗi, 16 = Lỗi khởi động/mở, 17 = Lỗi dừng/đóng, 20 = Lo, 21 = Hi, 24 = LoLo, 25 = HiHi, 32 = Fail/I/O Fault, 34 = Lỗi cấu hình CfgErr) |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận tổng hợp của khối này cộng với các đối tượng kênh/quá tải liên kết |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms Count) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã định danh Chủ sở hữu hiện hành của đối tượng (Current Object Owner ID: 0 = Đang không bị chiếm quyền) |
| **`Sts_Stopped`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần được yêu cầu dừng và đã xác nhận Dừng hoàn toàn (Confirmed Stopped) |
| **`Sts_StartingFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần đang khởi động theo chiều Thuận và chờ phản hồi (Starting Forward) |
| **`Sts_StartingRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần đang khởi động theo chiều Nghịch và chờ phản hồi (Starting Reverse) |
| **`Sts_RunningFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần đang chạy theo chiều Thuận ổn định (Confirmed Running Forward) |
| **`Sts_RunningRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần đang chạy theo chiều Nghịch ổn định (Confirmed Running Reverse) |
| **`Sts_StoppingFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Biến tần đang chạy Thuận được yêu cầu dừng và đang giảm tốc chờ dừng hẳn |
| **`Sts_StoppingRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Biến tần đang chạy Nghịch được yêu cầu dừng và đang giảm tốc chờ dừng hẳn |
| **`Sts_JoggingFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần đang chạy nhấp thử theo chiều Thuận (Jogging Forward) |
| **`Sts_JoggingRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần đang chạy nhấp thử theo chiều Nghịch (Jogging Reverse) |
| **`Sts_Horn`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Còi cảnh báo khởi động tại hiện trường đang kêu (Horn Active) |
| **`Sts_CommandDir`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Chiều quay đang được lệnh của biến tần: 1 = Chiều Thuận (Forward), 0 = Chiều Nghịch (Reverse) |
| **`Sts_ActualDir`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Chiều quay thực tế của biến tần: 1 = Đang quay Thuận (Forward), 0 = Đang quay Nghịch (Reverse) |
| **`Sts_Accel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Biến tần đang trong quá trình Tăng tốc (Accelerating) |
| **`Sts_Decel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Biến tần đang trong quá trình Giảm tốc (Decelerating) |
| **`Sts_NotReady`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị chưa sẵn sàng hoạt động (kiểm tra các bit Nrdy_* chi tiết để biết nguyên nhân cụ thể) |
| **`Sts_AtSpeed`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Biến tần đang chạy đúng tại tốc độ đặt tham chiếu (At Reference Speed) |
| **`Sts_SpeedLimited`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị tốc độ đặt (Speed Reference) đang bị kẹp tại giới hạn Cfg_MinSpdRef hoặc Cfg_MaxSpdRef |
| **`Sts_DigIn1`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Trạng thái ngõ vào số #1 (Digital Input 1) đọc từ biến tần |
| **`Sts_DigIn2`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Trạng thái ngõ vào số #2 (Digital Input 2) đọc từ biến tần |
| **`Sts_DigIn3`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Trạng thái ngõ vào số #3 (Digital Input 3) đọc từ biến tần |
| **`Sts_DigIn4`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Trạng thái ngõ vào số #4 (Digital Input 4) đọc từ biến tần |
| **`Sts_Available`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị sẵn sàng cho Chương trình tự động (Program) điều khiển (đang ở chế độ Program và không có lỗi/liên động ngắt) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass hiện đang được bỏ qua (Bypassed) |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Chế độ Bypass bảo trì đang kích hoạt (gửi sang khối P_Intlk / P_Perm để cho phép bỏ qua các điều kiện có quyền Bypass) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị chưa sẵn sàng hoạt động (kiểm tra các bit Nrdy_* chi tiết để biết nguyên nhân cụ thể) |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_DriveNR`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Biến tần báo trạng thái chưa sẵn sàng (Drive Not Ready) |
| **`Nrdy_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang có lỗi thiết bị ngắt bảo vệ (Shed — yêu cầu nhấn Reset) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Khóa liên động an toàn không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi truyền thông thẻ I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Nrdy_Perm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Cho phép khởi động không thỏa mãn (Permissive Not OK) |
| **`Nrdy_PrioStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng: Đang dính lệnh Dừng ưu tiên từ Operator/External (cần nhấn Reset trước khi chạy lại) |
| **`Nrdy_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng: Thiết bị đang bị cắt bảo vệ quá tải (Tripped tại thiết bị hoặc do lệnh Trip) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoặc cưỡng bức bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có ít nhất một cảnh báo bị Tạm hoãn (Shelved), Vô hiệu hóa (Disabled) hoặc Ức chế (Suppressed) — hiển thị biểu tượng chữ "I" trên HMI |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị đặt bộ định thời gian (Timer Preset) nằm ngoài phạm vi hợp lệ 0..2.147.483 giây |
| **`Err_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thời gian trễ mô phỏng (Cfg_SimFdbkT / Cfg_SimRampT) nằm ngoài phạm vi 0..2.147.483 giây |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình cảnh báo: Thời gian duy trì tối thiểu (Min On Time), thời gian tạm hoãn (Shelf Time) hoặc mức độ nghiêm trọng (Severity 1..1000) không hợp lệ |
| **`Err_FdbkRaw`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải đơn vị thô của tốc độ phản hồi (Speed Feedback Raw) có Min = Max |
| **`Err_FdbkEU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải đơn vị kỹ thuật của tốc độ phản hồi (Speed Feedback EU) có Min = Max |
| **`Err_RefLim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giới hạn kẹp tốc độ đặt bị ngược (Cfg_MinSpdRef > Cfg_MaxSpdRef) |
| **`Err_RefEU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải đơn vị kỹ thuật của tốc độ đặt (Speed Reference EU) có Min = Max |
| **`Err_RefRaw`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải đơn vị thô của tốc độ đặt (Speed Reference Raw) có Min = Max |
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
| **`Sts_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi không khởi động được (Fail to Start) |
| **`Alm_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi không khởi động được (Fail to Start) |
| **`Ack_FailToStart`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi không khởi động được (Fail to Start) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_FailToStartDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không khởi động được (Fail to Start) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_FailToStartShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không khởi động được (Fail to Start) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_FailToStartSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không khởi động được (Fail to Start) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi không dừng được (Fail to Stop) |
| **`Alm_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi không dừng được (Fail to Stop) |
| **`Ack_FailToStop`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi không dừng được (Fail to Stop) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_FailToStopDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không dừng được (Fail to Stop) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_FailToStopShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không dừng được (Fail to Stop) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_FailToStopSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không dừng được (Fail to Stop) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`Alm_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`Ack_IntlkTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_IntlkTripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_IntlkTripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_IntlkTripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_DriveFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi biến tần / bộ truyền động (Drive Fault) |
| **`Alm_DriveFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi biến tần / bộ truyền động (Drive Fault) |
| **`Ack_DriveFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi biến tần / bộ truyền động (Drive Fault) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_DriveFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_DriveFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_DriveFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi truyền thông thẻ I/O (I/O Fault) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi truyền thông thẻ I/O (I/O Fault) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`MRdy_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`P_PF52x`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_PF52x) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 43 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_PF52x`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'PF523 / PF525 Variable Frequency Drive'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_DigIn1Txt`** | `STRING_20` | `'Digital Input 1'` | [Biến cấu hình thông số] Nhãn mô tả cho ngõ vào số #1 (Digital Input 1) của biến tần hiển thị trên HMI |
| **`Cfg_DigIn2Txt`** | `STRING_20` | `'Digital Input 2'` | [Biến cấu hình thông số] Nhãn mô tả cho ngõ vào số #2 (Digital Input 2) của biến tần hiển thị trên HMI |
| **`Cfg_DigIn3Txt`** | `STRING_20` | `'Digital Input 3'` | [Biến cấu hình thông số] Nhãn mô tả cho ngõ vào số #3 (Digital Input 3) của biến tần hiển thị trên HMI |
| **`Cfg_DigIn4Txt`** | `STRING_20` | `'Digital Input 4'` | [Biến cấu hình thông số] Nhãn mô tả cho ngõ vào số #4 (Digital Input 4) của biến tần hiển thị trên HMI |
| **`Cfg_FwdText`** | `STRING_16` | `'Forward'` | [Biến cấu hình thông số] Chuỗi văn bản mô tả chiều quay Thuận (Forward Direction) trên HMI |
| **`Cfg_Label`** | `STRING_20` | `'Motor Speed Control'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_RevText`** | `STRING_16` | `'Reverse'` | [Biến cấu hình thông số] Chuỗi văn bản mô tả chiều quay Nghịch (Reverse Direction) trên HMI |
| **`Cfg_SpeedFdbkEU`** | `STRING_8` | `'Hz'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của tốc độ phản hồi biến tần (ví dụ: "Hz", "RPM", "%") hiển thị trên HMI |
| **`Cfg_SpeedRefEU`** | `STRING_8` | `'Hz'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của tốc độ đặt biến tần (ví dụ: "Hz", "RPM", "%") hiển thị trên HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_PF52x'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc nội bộ quản lý phân quyền và chuyển mạch giữa 7 nguồn lệnh điều khiển (OoS, Hand, Maint, Ovrd, Ext, Prog, Oper) |
| **`DriveFault`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi biến tần / bộ truyền động (Drive Fault) |
| **`FailToStart`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi không khởi động được (Fail to Start) |
| **`FailToStop`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi không dừng được (Fail to Stop) |
| **`HMI_Lib`** | `STRING_12` | `??` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `??` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `??` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`IOFault`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bỏ qua (Bypass) tất cả các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Kiểm tra lại bình thường (Check / Gỡ bỏ Bypass) toàn bộ điều kiện Khóa liên động và Cho phép |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (Bỏ qua (Bypass) tất cả các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (Kiểm tra lại bình thường (Check / Gỡ bỏ Bypass) toàn bộ điều kiện Khóa liên động và Cho phép), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`OCmd_JogFwd`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Nhấp thử chạy Thuận (Jog Forward) |
| **`OCmd_JogRev`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Nhấp thử chạy Nghịch (Jog Reverse) |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo |
| **`OCmd_StartFwd`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Khởi động chạy Thuận (Start Forward) |
| **`OCmd_StartRev`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Khởi động chạy Nghịch (Start Reverse) |
| **`OCmd_Stop`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Dừng (Stop) thiết bị/động cơ |
| **`ORdy_JogFwd`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_JogFwd (Nhấp thử chạy Thuận (Jog Forward)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_JogRev`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_JogRev (Nhấp thử chạy Nghịch (Jog Reverse)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_SpeedRef`** | `BOOL` | `0` | [Cờ cho phép nhập dữ liệu trên HMI] 1 = Sẵn sàng nhận giá trị đặt OSet_SpeedRef (Giá trị tốc độ đặt yêu cầu cho biến tần (Speed Reference, tính theo đơn vị kỹ thuật EU như Hz, RPM hoặc %)), cho phép mở ô nhập số trên HMI Faceplate |
| **`ORdy_StartFwd`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_StartFwd (Khởi động chạy Thuận (Start Forward)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_StartRev`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_StartRev (Khởi động chạy Nghịch (Start Reverse)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Stop`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Stop (Dừng (Stop) thiết bị/động cơ), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`OSet_SpeedRef`** | `REAL` | `0` | [Giá trị đặt từ Người vận hành HMI] Giá trị đặt từ Người vận hành (Operator HMI): Giá trị tốc độ đặt yêu cầu cho biến tần (Speed Reference, tính theo đơn vị kỹ thuật EU như Hz, RPM hoặc %) |
| **`Val_FaultDesc`** | `STRING_40` | `‘Logic not yet scanned.’` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi văn bản mô tả chi tiết nguyên nhân lỗi biến tần (tra cứu tự động từ mã lỗi Val_LastFaultCode) |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo hiện hành và trạng thái xác nhận của khối này (0 = Không có cảnh báo, 1 = Đã hết lỗi nhưng chờ Reset/Ack, 2..3 = Thấp, 4..5 = Trung bình, 6..7 = Cao, 8..9 = Khẩn cấp) |
---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_PF753`
**Thuộc chương:** [Chương 3: Động cơ, Biến tần & Rơ-le Quá tải (Motors & Drives)](../03_CHUONG_3_MOTORS_VA_BIEN_TAN.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Motors 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 158–173)  
**Tệp định nghĩa khối lệnh:** `P_PF753_4.10.00_RUNG.L5X`  
**Thống kê số lượng biến của `P_PF753`:** **123** Biến đầu vào (`Input Parameters`) | **121** Biến đầu ra (`Output Parameters`) | **40** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_PF753` trong Studio 5000
- **Chức năng cốt lõi:** Khối điều khiển chuyên dụng cho biến tần hạ thế tải nặng **Allen-Bradley PowerFlex 753** qua mạng EtherNet/IP hoặc ControlNet, tích hợp sẵn cấu trúc Datalinks đọc dòng điện, công suất, điện áp DC Bus và giải mã toàn bộ bảng lỗi/cảnh báo của dòng 753.
- **Khi nào CẦN sử dụng:** Khi điều khiển bơm, quạt, máy nén, máy khuấy công suất vừa và lớn sử dụng biến tần **PowerFlex 753**.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi dùng biến tần PowerFlex 755 ([`P_PF755.md`](./P_PF755.md)).

### Quy trình 4 bước đấu nối và cấu hình `P_PF753`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp`, `Out`, `Ref_GetFaultMSG`, `Ref_GetFaultDest`, `Inp_FwdPermOK`, `Inp_FwdNBPermOK`, `Inp_RevPermOK`, `Inp_RevNBPermOK`, `Inp_IntlkOK`, `Inp_NBIntlkOK`, `Inp_IntlkAvail`, `Inp_IOFault`, `Inp_Sim`, `Inp_Hand`, `Inp_Ovrd`
   - **Các chân tham chiếu dữ liệu (`Ref_*` / `InOut`):** `Ref_GetFaultMSG`, `Ref_GetFaultDest`, `Ref_GetFaultMSG`, `Ref_GetFaultDest` — bắt buộc gán thẻ mảng/cấu trúc tương ứng trong Controller Tags.
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_HasReverse`, `Cfg_HasJog`, `Cfg_AllowLocal`, `Cfg_HasFwdPermObj`, `Cfg_HasRevPermObj`, `Cfg_HasIntlkObj`, `Cfg_HasMoreObj`, `Cfg_HasResInhObj`, `Cfg_HasRunTimeObj`, `Cfg_SetTrack`, `Cfg_SetTrackOvrdHand`, `Cfg_OperStopPrio`, `Cfg_ExtStopPrio`, `Cfg_OCmdResets`, `Cfg_XCmdResets`, `Cfg_OvrdPermIntlk`, `Cfg_ShedOnFailToStart`, `Cfg_ShedOnIOFault`...
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_Desc`, `Cfg_FwdText`, `Cfg_Label`, `Cfg_RevText`, `Cfg_SpeedFdbkEU`, `Cfg_SpeedRefEU`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_PF753` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_PF753` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PSet_SpeedRef`, `PSet_Owner`, `PCmd_StartFwd`, `PCmd_StartRev`, `PCmd_Stop`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_FailToStartAck`, `PCmd_FailToStartSuppress`, `PCmd_FailToStartUnsuppress`, `PCmd_FailToStartUnshelve`, `PCmd_FailToStopAck`, `PCmd_FailToStopSuppress`, `PCmd_FailToStopUnsuppress`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh điều khiển tự động, kiểm tra khối đã sẵn sàng ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển quyền về Program, sau đó gán giá trị vào `PSet_*` và kích xung `PCmd_* = 1` (khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Out_Horn`, `Val_SpeedRef`, `Val_SpeedFdbk`, `Val_SpeedRefSrc`, `Val_SpeedRefRaw`, `Val_SpeedFdbkRaw`, `Val_SpeedRefEUMin`, `Val_SpeedRefEUMax`, `Val_SpeedFdbkEUMin`, `Val_SpeedFdbkEUMax`, `Val_LastFaultCode`, `Val_Cmd`, `Val_Fdbk`, `Val_Sts`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Val_Owner`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 123 Biến Đầu Vào (`Input Parameters`) của `P_PF753`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp`** | `P_PF6000_Inp` | `-` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu điều kiện đầu vào (1 = Kích hoạt điều kiện / Báo động, 0 = Bình thường) |
| **`Out`** | `P_PF6000_Out` | `-` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra điều khiển thiết bị (hoặc kết quả cổng điều kiện sau thời gian trễ On-Delay / Off-Delay) |
| **`Ref_GetFaultMSG`** | `MESSAGE` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới thẻ lệnh truyền thông MSG dùng để đọc bản ghi lỗi gần nhất từ thiết bị |
| **`Ref_GetFaultDest`** | `P_PFComm_FltAlmRec` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới vùng đệm (Buffer) chứa dữ liệu trả về từ lệnh MSG đọc mã lỗi gần nhất |
| **`Inp_FwdPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Điều kiện Cho phép khởi động chiều Thuận (có thể bypass) đều OK |
| **`Inp_FwdNBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Điều kiện Cho phép khởi động chiều Thuận (không thể bypass) đều OK |
| **`Inp_RevPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Điều kiện Cho phép khởi động chiều Nghịch (có thể bypass) đều OK |
| **`Inp_RevNBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Điều kiện Cho phép khởi động chiều Nghịch (không thể bypass) đều OK |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp Khóa liên động an toàn có thể Bypass (1 = Liên động OK cho phép chạy; 0 = Cắt dừng thiết bị ngay lập tức; nối từ P_Intlk.Sts_IntlkOK) |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp Khóa liên động an toàn KHÔNG thể Bypass (1 = Liên động bất khả kháng OK; 0 = Cắt dừng thiết bị ngay lập tức; nối từ P_Intlk.Sts_NBIntlkOK) |
| **`Inp_IntlkAvail`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu báo điều kiện Khóa liên động sẵn sàng cho phép gọi máy trong nhóm (1 = Interlock Availability OK) |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi truyền thông I/O của thiết bị (0 = Tốt/OK, 1 = Lỗi truyền thông I/O Fault) |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân kích hoạt chế độ Mô phỏng (Simulation): Khi = 1, khóa các ngõ ra vật lý ở mức an toàn và dùng giá trị/phản hồi giả lập nội bộ; khi = 0, vận hành thực tế bình thường |
| **`Inp_Hand`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu chọn chế độ Điều khiển bằng tay tại chỗ (Hand/Local): 1 = Chiếm quyền sang chế độ Hand (thường nối từ công tắc cứng tại tủ MCC); 0 = Nhả chế độ Hand |
| **`Inp_Ovrd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu kích hoạt chế độ Cưỡng bức ưu tiên (Override): 1 = Chiếm quyền sang chế độ Override; 0 = Nhả chế độ Override |
| **`Inp_OvrdCmd`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã lệnh điều khiển thiết bị khi ở chế độ Cưỡng bức (Override Command — kiểu số nguyên phụ thuộc vào từng loại thiết bị: 0 = Không tác động, 1 = Dừng/Đóng/Tắt, 2 = Chạy/Mở/Bật...) |
| **`Inp_OvrdSpeed`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tốc độ đặt yêu cầu cho biến tần khi ở chế độ Cưỡng bức (Override Speed Reference, đơn vị EU) |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân khóa cấm nguồn lệnh ngoài (External Inhibit): 1 = Cấm chế độ External chiếm quyền điều khiển; 0 = Cho phép External chiếm quyền |
| **`Inp_HornInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân khóa cấm còi báo động tại hiện trường: 1 = Cấm phát còi báo (Inhibit Horn); 0 = Cho phép phát còi báo trước khi khởi động |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Cfg_HasReverse`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép biến tần chạy cả 2 chiều Thuận và Nghịch; 0 = Chỉ cho phép chạy 1 chiều Thuận (Forward Only) |
| **`Cfg_HasJog`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép và hiển thị nút nhấn Nhấp thử (Jog) trên màn hình HMI; 0 = Khóa cấm lệnh Jog |
| **`Cfg_AllowLocal`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép khởi động/dừng động cơ tại chỗ ở tủ MCC mà không phát báo động lỗi; 0 = Chỉ cho phép điều khiển từ HMI hoặc Program |
| **`Cfg_HasFwdPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối P_Perm nối vào chân cho phép chạy Thuận (Inp_FwdPermOK) |
| **`Cfg_HasRevPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối P_Perm nối vào chân cho phép chạy Nghịch (Inp_RevPermOK) |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Khóa liên động (P_Intlk) nối vào chân Inp_IntlkOK và bật nút điều hướng mở Faceplate P_Intlk |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_HasResInhObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Cấm khởi động lại (P_ResInh) đi kèm và bật nút điều hướng mở Faceplate P_ResInh |
| **`Cfg_HasRunTimeObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Đếm giờ chạy (P_RunTime) đi kèm và bật nút điều hướng mở Faceplate P_RunTime |
| **`Cfg_SetTrack`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép các giá trị đặt (PSet_CV / OSet_CV / SP) tự động bám theo ngõ ra thực tế hiện hành để chuyển mạch không giật (Bumpless Transfer) giữa Operator và Program |
| **`Cfg_SetTrackOvrdHand`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép giá trị đặt của Program và Operator tự động bám theo giá trị CV khi đang ở chế độ Override hoặc Hand |
| **`Cfg_OperStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Nút nhấn Dừng của Người vận hành (OCmd_Stop) có quyền ưu tiên cao nhất, bấm dừng được ngay cả khi máy đang chạy ở chế độ Program; 0 = OCmd_Stop chỉ có tác dụng khi ở chế độ Operator/Maintenance |
| **`Cfg_ExtStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Lệnh Dừng từ hệ thống ngoài (XCmd_Stop) có hiệu lực ưu tiên ở mọi chế độ; 0 = Chỉ có hiệu lực khi ở chế độ External |
| **`Cfg_OCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khi Người vận hành ra lệnh Chạy/Mở mới (OCmd_*), khối tự động xóa luôn các chốt lỗi cũ; 0 = Bắt buộc phải nhấn nút Reset trước khi ra lệnh mới |
| **`Cfg_XCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khi hệ thống ngoài gửi lệnh Chạy/Mở mới (XCmd_*), khối tự động xóa luôn các chốt lỗi cũ; 0 = Bắt buộc phải gửi xung XCmd_Reset trước |
| **`Cfg_OvrdPermIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Chế độ Cưỡng bức (Override) được quyền bỏ qua các điều kiện Cho phép và Liên động có thể bypass; 0 = Chế độ Override vẫn phải tuân thủ mọi điều kiện |
| **`Cfg_ShedOnFailToStart`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt lệnh chạy (Shed) và phát báo động khi động cơ bị lỗi Không khởi động được (Fail to Start); 0 = Chỉ phát báo động nhưng vẫn giữ ngõ ra |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt dừng thiết bị về trạng thái an toàn (Shed) và phát báo động khi lỗi truyền thông thẻ I/O; 0 = Chỉ phát báo động |
| **`Cfg_SimScaleEU`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Trong chế độ mô phỏng, quy đổi tốc độ đặt sang tốc độ phản hồi theo đơn vị kỹ thuật EU |
| **`Cfg_SimScaleRaw`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Trong chế độ mô phỏng, quy đổi tốc độ đặt sang tốc độ phản hồi theo đơn vị thô Raw |
| **`Cfg_DecPlcs`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho tốc độ/giá trị trên màn hình HMI (từ 0 đến 6 chữ số) |
| **`Cfg_HasFailToStartAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi không khởi động được (Fail to Start): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasFailToStopAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi không dừng được (Fail to Stop): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasDriveFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi biến tần / bộ truyền động (Drive Fault): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_FailToStartResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi không khởi động được (Fail to Start): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_FailToStopResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi không dừng được (Fail to Stop): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_DriveFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi biến tần / bộ truyền động (Drive Fault): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_FailToStartAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi không khởi động được (Fail to Start): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_FailToStopAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi không dừng được (Fail to Stop): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_DriveFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi biến tần / bộ truyền động (Drive Fault): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_FailToStartSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi không khởi động được (Fail to Start) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_FailToStopSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi không dừng được (Fail to Stop) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_DriveFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_MinSpdRef`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp dưới tối thiểu cho phép đặt của tốc độ biến tần (Minimum Speed Reference, đơn vị EU) |
| **`Cfg_MaxSpdRef`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp trên tối đa cho phép đặt của tốc độ biến tần (Maximum Speed Reference, đơn vị EU) |
| **`Cfg_SpeedRefRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị thô tối thiểu của tốc độ đặt gửi xuống biến tần (Speed Reference Raw Min) |
| **`Cfg_SpeedRefRawMax`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị thô tối đa của tốc độ đặt gửi xuống biến tần (Speed Reference Raw Max) |
| **`Cfg_SpeedRefEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tối thiểu của tốc độ đặt (Speed Reference EU Min) |
| **`Cfg_SpeedRefEUMax`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tối đa của tốc độ đặt (Speed Reference EU Max) |
| **`Cfg_SpeedFdbkRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị thô tối thiểu của tốc độ phản hồi từ biến tần (Speed Feedback Raw Min) |
| **`Cfg_SpeedFdbkRawMax`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị thô tối đa của tốc độ phản hồi từ biến tần (Speed Feedback Raw Max) |
| **`Cfg_SpeedFdbkEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tối thiểu của tốc độ phản hồi (Speed Feedback EU Min) |
| **`Cfg_SpeedFdbkEUMax`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tối đa của tốc độ phản hồi (Speed Feedback EU Max) |
| **`Cfg_StartHornT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian hú còi cảnh báo tại hiện trường (Out_Horn) trước khi đóng điện khởi động thiết bị (giây; đặt = 0.0 để tắt còi) |
| **`Cfg_SimRampT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian tăng/giảm tốc độ phản hồi giả lập từ 0 đến 100% trong chế độ Mô phỏng biến tần (giây) |
| **`Cfg_FailToStartT`** | `REAL` | `15` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian chờ tối đa (giây) từ lúc xuất lệnh Chạy đến khi phải nhận được phản hồi chạy (Inp_RunFdbk = 1) trước khi báo lỗi FailToStart |
| **`Cfg_FailToStopT`** | `REAL` | `15` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian chờ tối đa (giây) từ lúc xuất lệnh Dừng đến khi phản hồi chạy phải về 0 trước khi báo lỗi FailToStop |
| **`Cfg_ResetPulseT`** | `REAL` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Độ rộng xung xuất ra chân xóa lỗi biến tần/khởi động mềm (giây) |
| **`Cfg_WatchdogT`** | `REAL` | `5` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian giám sát truyền thông mạng với biến tần (Communications Watchdog Timeout, tính bằng giây) |
| **`Cfg_OperKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ bit cấu hình cho phép Operator giữ quyền chỉnh thông số ngay cả khi khối ở chế độ Program (.0 = Giữ quyền chọn kênh PV, .1 = Giữ quyền chỉnh ngưỡng cảnh báo) |
| **`Cfg_ProgKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ bit cấu hình cho phép Program giữ quyền chỉnh thông số ngay cả khi khối ở chế độ Operator (.0 = Giữ quyền chọn kênh PV, .1 = Giữ quyền chỉnh ngưỡng cảnh báo) |
| **`Cfg_ExtKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ bit cấu hình cho phép External giữ quyền điều khiển khi ở Program/Operator (.0 = Giữ tốc độ đặt Reference, .1 = Giữ lệnh điều khiển Commands) |
| **`PSet_SpeedRef`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Giá trị tốc độ đặt yêu cầu cho biến tần (Speed Reference, tính theo đơn vị kỹ thuật EU như Hz, RPM hoặc %) |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Mã định danh Chủ sở hữu (Owner ID: ghi khác 0 để đăng ký chiếm quyền điều khiển, ghi 0 để nhả quyền) |
| **`XSet_SpeedRef`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài] Giá trị đặt từ Hệ thống điều khiển ngoài (External): Giá trị tốc độ đặt yêu cầu cho biến tần (Speed Reference, tính theo đơn vị kỹ thuật EU như Hz, RPM hoặc %) |
| **`PCmd_StartFwd`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khởi động chạy Thuận (Start Forward) |
| **`PCmd_StartRev`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khởi động chạy Nghịch (Start Reverse) |
| **`PCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Dừng (Stop) thiết bị/động cơ |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Chương trình tự động (từ Operator sang Program) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Người vận hành (từ Program sang Operator) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa giữ nguồn lệnh hiện tại (không cho phép nguồn lệnh kia giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Đưa nguồn lệnh về chế độ Bình thường mặc định (Normal: Program hoặc Operator theo Cfg_ProgNormal) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`PCmd_FailToStartAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi không khởi động được (Fail to Start) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_FailToStartSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi không khởi động được (Fail to Start) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStartUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi không khởi động được (Fail to Start) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStartUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi không khởi động được (Fail to Start) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_FailToStopAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi không dừng được (Fail to Stop) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_FailToStopSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi không dừng được (Fail to Stop) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStopUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi không dừng được (Fail to Stop) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStopUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi không dừng được (Fail to Stop) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_DriveFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_DriveFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_DriveFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_DriveFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) ngay lập tức (biến xung tự động xóa về 0) |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`XCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Dừng (Stop) thiết bị/động cơ |
| **`XCmd_StartFwd`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Khởi động chạy Thuận (Start Forward) |
| **`XCmd_StartRev`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Khởi động chạy Nghịch (Start Reverse) |
| **`XCmd_JogFwd`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhấp thử chạy Thuận (Jog Forward) |
| **`XCmd_JogRev`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhấp thử chạy Nghịch (Jog Reverse) |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Chiếm quyền điều khiển (Acquire) về chế độ tương ứng |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |

## 3. Bảng chi tiết 121 Biến Đầu Ra (`Output Parameters`) của `P_PF753`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Inp`** | `P_PF6000_Inp` | `-` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu điều kiện đầu vào (1 = Kích hoạt điều kiện / Báo động, 0 = Bình thường) |
| **`Out`** | `P_PF6000_Out` | `-` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra điều khiển thiết bị (hoặc kết quả cổng điều kiện sau thời gian trễ On-Delay / Off-Delay) |
| **`Ref_GetFaultMSG`** | `MESSAGE` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới thẻ lệnh truyền thông MSG dùng để đọc bản ghi lỗi gần nhất từ thiết bị |
| **`Ref_GetFaultDest`** | `P_PFComm_FltAlmRec` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới vùng đệm (Buffer) chứa dữ liệu trả về từ lệnh MSG đọc mã lỗi gần nhất |
| **`Out_Horn`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra bật còi cảnh báo âm thanh tại hiện trường trước khi khởi động hoặc chuyển trạng thái thiết bị (1 = Bật còi) |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng dữ liệu tổng hợp cuối cùng (0 = Tín hiệu thực Tốt đã xác nhận, 1 = Tín hiệu thực Tốt giả định, 2 = Không cấu hình phản hồi/Tốt giả định, 8 = Đang mô phỏng, 9 = Mô phỏng vòng lặp, 10 = Giá trị nhập tay, 16 = Ngoài đặc tính kỹ thuật, 17 = Thay thế tại thiết bị/bus, 18 = Thay thế bởi Bảo trì, 19 = Ngắt bảo vệ giữ giá trị cũ, 20 = Ngắt bảo vệ dùng giá trị thay thế, 32 = Lỗi tín hiệu vượt dải/NaN, 33 = Lỗi kênh I/O, 34 = Lỗi module I/O, 35 = Lỗi cấu hình) |
| **`Val_SpeedRef`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị tốc độ đặt (Speed Reference Target) đang gửi xuống biến tần (đơn vị kỹ thuật EU) |
| **`Val_SpeedFdbk`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị tốc độ phản hồi thực tế (Actual Speed Feedback) đọc từ biến tần (đơn vị kỹ thuật EU) |
| **`Val_SpeedRefSrc`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã nguồn tham chiếu tốc độ hiện hành của biến tần (enum: 1 = Ngõ vào A, 2 = Ngõ vào B, 3..7 = Tốc độ đặt trước 3..7, 16..31 = Đặt bằng tay) |
| **`Val_SpeedRefRaw`** | `INT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Bản sao giá trị tốc độ đặt gửi xuống biến tần tính theo đơn vị thô (Raw Units) để hiển thị trên HMI Faceplate |
| **`Val_SpeedFdbkRaw`** | `INT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Bản sao giá trị tốc độ phản hồi từ biến tần tính theo đơn vị thô (Raw Units) để hiển thị trên HMI Faceplate |
| **`Val_SpeedRefEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận dưới của dải tốc độ đặt (Speed Reference EU Min) |
| **`Val_SpeedRefEUMax`** | `REAL` | `130` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận trên của dải tốc độ đặt (Speed Reference EU Max) |
| **`Val_SpeedFdbkEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận dưới của dải tốc độ phản hồi (Speed Feedback EU Min) |
| **`Val_SpeedFdbkEUMax`** | `REAL` | `130` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận trên của dải tốc độ phản hồi (Speed Feedback EU Max) |
| **`Val_LastFaultCode`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã lỗi gần nhất của biến tần (Last Drive Fault Code) |
| **`Val_Cmd`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã lệnh điều khiển thiết bị đang xuất ra (Device Command enum: 0 = Không tác động/Tắt, 1 = Dừng/Đóng/Off, 2 = Chạy/Mở/On...) |
| **`Val_Fdbk`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái phản hồi thực tế từ thiết bị hiện trường (Device Feedback enum: 0 = Đang dừng/Đang chuyển trạng thái, 1 = Đang chạy/Đóng/Off, 2 = Mở/On, 3 = Phản hồi không hợp lệ) |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái hoạt động xác nhận của thiết bị (Device Confirmed Status enum dùng để điều khiển màu sắc và biểu tượng động trên HMI) |
| **`Val_Fault`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái lỗi thiết bị (Device Fault Status — kiểu liệt kê enum: 0 = Không lỗi, 16 = Lỗi khởi động/mở, 17 = Lỗi dừng/đóng, 20 = Lo, 21 = Hi, 24 = LoLo, 25 = HiHi, 32 = Fail/I/O Fault, 34 = Lỗi cấu hình CfgErr) |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận tổng hợp của khối này cộng với các đối tượng kênh/quá tải liên kết |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms Count) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã định danh Chủ sở hữu hiện hành của đối tượng (Current Object Owner ID: 0 = Đang không bị chiếm quyền) |
| **`Sts_Stopped`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần được yêu cầu dừng và đã xác nhận Dừng hoàn toàn (Confirmed Stopped) |
| **`Sts_StartingFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần đang khởi động theo chiều Thuận và chờ phản hồi (Starting Forward) |
| **`Sts_StartingRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần đang khởi động theo chiều Nghịch và chờ phản hồi (Starting Reverse) |
| **`Sts_RunningFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần đang chạy theo chiều Thuận ổn định (Confirmed Running Forward) |
| **`Sts_RunningRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần đang chạy theo chiều Nghịch ổn định (Confirmed Running Reverse) |
| **`Sts_StoppingFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Biến tần đang chạy Thuận được yêu cầu dừng và đang giảm tốc chờ dừng hẳn |
| **`Sts_StoppingRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Biến tần đang chạy Nghịch được yêu cầu dừng và đang giảm tốc chờ dừng hẳn |
| **`Sts_JoggingFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần đang chạy nhấp thử theo chiều Thuận (Jogging Forward) |
| **`Sts_JoggingRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần đang chạy nhấp thử theo chiều Nghịch (Jogging Reverse) |
| **`Sts_Horn`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Còi cảnh báo khởi động tại hiện trường đang kêu (Horn Active) |
| **`Sts_CommandDir`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Chiều quay đang được lệnh của biến tần: 1 = Chiều Thuận (Forward), 0 = Chiều Nghịch (Reverse) |
| **`Sts_ActualDir`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Chiều quay thực tế của biến tần: 1 = Đang quay Thuận (Forward), 0 = Đang quay Nghịch (Reverse) |
| **`Sts_Accel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Biến tần đang trong quá trình Tăng tốc (Accelerating) |
| **`Sts_Decel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Biến tần đang trong quá trình Giảm tốc (Decelerating) |
| **`Sts_NotReady`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị chưa sẵn sàng hoạt động (kiểm tra các bit Nrdy_* chi tiết để biết nguyên nhân cụ thể) |
| **`Sts_Alarm`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Biến tần đang có cảnh báo (kiểm tra màn hình biến tần hoặc mã Val_LastAlarmCode) |
| **`Sts_AtSpeed`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Biến tần đang chạy đúng tại tốc độ đặt tham chiếu (At Reference Speed) |
| **`Sts_SpeedLimited`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị tốc độ đặt (Speed Reference) đang bị kẹp tại giới hạn Cfg_MinSpdRef hoặc Cfg_MaxSpdRef |
| **`Sts_DriveSts1`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ trạng thái biến tần số #1 (Drive Status Word 1 — ánh xạ từng bit theo tài liệu biến tần) |
| **`Sts_DriveSts2`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ trạng thái biến tần số #2 (Drive Status Word 2 — ánh xạ từng bit theo tài liệu biến tần) |
| **`Sts_FaultStsA`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ trạng thái lỗi biến tần A (Drive Fault Status A — ánh xạ từng bit) |
| **`Sts_FaultStsB`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ trạng thái lỗi biến tần B (Drive Fault Status B — ánh xạ từng bit) |
| **`Sts_PMSts`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ trạng thái bảo trì dự đoán của biến tần (Predictive Maintenance Status Word) |
| **`Sts_StartInhibits`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ bit báo các nguyên nhân biến tần đang bị khóa cấm khởi động (Drive Start Inhibit Reasons) |
| **`Sts_MsgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi khi thực thi lệnh MSG đọc thông tin mã lỗi/cảnh báo từ biến tần |
| **`Sts_Available`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị sẵn sàng cho Chương trình tự động (Program) điều khiển (đang ở chế độ Program và không có lỗi/liên động ngắt) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass hiện đang được bỏ qua (Bypassed) |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Chế độ Bypass bảo trì đang kích hoạt (gửi sang khối P_Intlk / P_Perm để cho phép bỏ qua các điều kiện có quyền Bypass) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị chưa sẵn sàng hoạt động (kiểm tra các bit Nrdy_* chi tiết để biết nguyên nhân cụ thể) |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_DriveNR`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Biến tần báo trạng thái chưa sẵn sàng (Drive Not Ready) |
| **`Nrdy_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang có lỗi thiết bị ngắt bảo vệ (Shed — yêu cầu nhấn Reset) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Khóa liên động an toàn không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi truyền thông thẻ I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Nrdy_Perm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Cho phép khởi động không thỏa mãn (Permissive Not OK) |
| **`Nrdy_PrioStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng: Đang dính lệnh Dừng ưu tiên từ Operator/External (cần nhấn Reset trước khi chạy lại) |
| **`Nrdy_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng: Thiết bị đang bị cắt bảo vệ quá tải (Tripped tại thiết bị hoặc do lệnh Trip) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoặc cưỡng bức bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có ít nhất một cảnh báo bị Tạm hoãn (Shelved), Vô hiệu hóa (Disabled) hoặc Ức chế (Suppressed) — hiển thị biểu tượng chữ "I" trên HMI |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị đặt bộ định thời gian (Timer Preset) nằm ngoài phạm vi hợp lệ 0..2.147.483 giây |
| **`Err_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thời gian trễ mô phỏng (Cfg_SimFdbkT / Cfg_SimRampT) nằm ngoài phạm vi 0..2.147.483 giây |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình cảnh báo: Thời gian duy trì tối thiểu (Min On Time), thời gian tạm hoãn (Shelf Time) hoặc mức độ nghiêm trọng (Severity 1..1000) không hợp lệ |
| **`Err_FdbkEU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải đơn vị kỹ thuật của tốc độ phản hồi (Speed Feedback EU) có Min = Max |
| **`Err_RefLim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giới hạn kẹp tốc độ đặt bị ngược (Cfg_MinSpdRef > Cfg_MaxSpdRef) |
| **`Err_RefEU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải đơn vị kỹ thuật của tốc độ đặt (Speed Reference EU) có Min = Max |
| **`Err_RefRaw`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải đơn vị thô của tốc độ đặt (Speed Reference Raw) có Min = Max |
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
| **`Sts_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi không khởi động được (Fail to Start) |
| **`Alm_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi không khởi động được (Fail to Start) |
| **`Ack_FailToStart`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi không khởi động được (Fail to Start) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_FailToStartDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không khởi động được (Fail to Start) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_FailToStartShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không khởi động được (Fail to Start) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_FailToStartSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không khởi động được (Fail to Start) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi không dừng được (Fail to Stop) |
| **`Alm_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi không dừng được (Fail to Stop) |
| **`Ack_FailToStop`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi không dừng được (Fail to Stop) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_FailToStopDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không dừng được (Fail to Stop) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_FailToStopShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không dừng được (Fail to Stop) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_FailToStopSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không dừng được (Fail to Stop) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`Alm_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`Ack_IntlkTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_IntlkTripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_IntlkTripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_IntlkTripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_DriveFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi biến tần / bộ truyền động (Drive Fault) |
| **`Alm_DriveFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi biến tần / bộ truyền động (Drive Fault) |
| **`Ack_DriveFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi biến tần / bộ truyền động (Drive Fault) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_DriveFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_DriveFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_DriveFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi truyền thông thẻ I/O (I/O Fault) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi truyền thông thẻ I/O (I/O Fault) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`MRdy_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`P_PF6000`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_PF6000) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 40 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_PF753`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'PF6000 MV Variable Frequency Drive'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_FwdText`** | `STRING_16` | `' '` | [Biến cấu hình thông số] Chuỗi văn bản mô tả chiều quay Thuận (Forward Direction) trên HMI |
| **`Cfg_Label`** | `STRING_20` | `'Motor Speed Control'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_RevText`** | `STRING_16` | `' '` | [Biến cấu hình thông số] Chuỗi văn bản mô tả chiều quay Nghịch (Reverse Direction) trên HMI |
| **`Cfg_SpeedFdbkEU`** | `STRING_8` | `'Hz'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của tốc độ phản hồi biến tần (ví dụ: "Hz", "RPM", "%") hiển thị trên HMI |
| **`Cfg_SpeedRefEU`** | `STRING_8` | `'Hz'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của tốc độ đặt biến tần (ví dụ: "Hz", "RPM", "%") hiển thị trên HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_PF6000'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc nội bộ quản lý phân quyền và chuyển mạch giữa 7 nguồn lệnh điều khiển (OoS, Hand, Maint, Ovrd, Ext, Prog, Oper) |
| **`DriveFault`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi biến tần / bộ truyền động (Drive Fault) |
| **`FailToStart`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi không khởi động được (Fail to Start) |
| **`FailToStop`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi không dừng được (Fail to Stop) |
| **`HMI_FWMajor`** | `DINT` | `5` | Số phiên bản Firmware chính (Major Revision) của biến tần hiển thị trên HMI |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_PF6000'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`IOFault`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bỏ qua (Bypass) tất cả các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Kiểm tra lại bình thường (Check / Gỡ bỏ Bypass) toàn bộ điều kiện Khóa liên động và Cho phép |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (Bỏ qua (Bypass) tất cả các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (Kiểm tra lại bình thường (Check / Gỡ bỏ Bypass) toàn bộ điều kiện Khóa liên động và Cho phép), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`OCmd_JogFwd`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Nhấp thử chạy Thuận (Jog Forward) |
| **`OCmd_JogRev`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Nhấp thử chạy Nghịch (Jog Reverse) |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo |
| **`OCmd_StartFwd`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Khởi động chạy Thuận (Start Forward) |
| **`OCmd_StartRev`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Khởi động chạy Nghịch (Start Reverse) |
| **`OCmd_Stop`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Dừng (Stop) thiết bị/động cơ |
| **`ORdy_JogFwd`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_JogFwd (Nhấp thử chạy Thuận (Jog Forward)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_JogRev`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_JogRev (Nhấp thử chạy Nghịch (Jog Reverse)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_SpeedRef`** | `BOOL` | `0` | [Cờ cho phép nhập dữ liệu trên HMI] 1 = Sẵn sàng nhận giá trị đặt OSet_SpeedRef (Giá trị tốc độ đặt yêu cầu cho biến tần (Speed Reference, tính theo đơn vị kỹ thuật EU như Hz, RPM hoặc %)), cho phép mở ô nhập số trên HMI Faceplate |
| **`ORdy_StartFwd`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_StartFwd (Khởi động chạy Thuận (Start Forward)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_StartRev`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_StartRev (Khởi động chạy Nghịch (Start Reverse)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Stop`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Stop (Dừng (Stop) thiết bị/động cơ), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`OSet_SpeedRef`** | `REAL` | `0` | [Giá trị đặt từ Người vận hành HMI] Giá trị đặt từ Người vận hành (Operator HMI): Giá trị tốc độ đặt yêu cầu cho biến tần (Speed Reference, tính theo đơn vị kỹ thuật EU như Hz, RPM hoặc %) |
| **`Val_FaultDesc`** | `STRING_16` | `‘Not yet scanned.’` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi văn bản mô tả chi tiết nguyên nhân lỗi biến tần (tra cứu tự động từ mã lỗi Val_LastFaultCode) |
| **`Val_Notify`** | `DINT` | `??` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo hiện hành và trạng thái xác nhận của khối này (0 = Không có cảnh báo, 1 = Đã hết lỗi nhưng chờ Reset/Ack, 2..3 = Thấp, 4..5 = Trung bình, 6..7 = Cao, 8..9 = Khẩn cấp) |
---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_PF755`
**Thuộc chương:** [Chương 3: Động cơ, Biến tần & Rơ-le Quá tải (Motors & Drives)](../03_CHUONG_3_MOTORS_VA_BIEN_TAN.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Motors 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 174–191)  
**Tệp định nghĩa khối lệnh:** `P_PF755_4.10.00_RUNG.L5X`  
**Thống kê số lượng biến của `P_PF755`:** **117** Biến đầu vào (`Input Parameters`) | **123** Biến đầu ra (`Output Parameters`) | **40** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_PF755` trong Studio 5000
- **Chức năng cốt lõi:** Khối điều khiển chuyên dụng cho biến tần cao cấp **Allen-Bradley PowerFlex 755, 755TL (Low Harmonic) và 755TR (Regenerative — trả năng lượng về lưới)** qua mạng EtherNet/IP.
- **Khi nào CẦN sử dụng:** Khi dự án dùng biến tần **PowerFlex 755 / 755TL / 755TR** (đặc biệt trong các ứng dụng tải nặng, hãm tái sinh hoặc yêu cầu sóng hài thấp).
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi dùng biến tần PowerFlex 753 ([`P_PF753.md`](./P_PF753.md)) hoặc PowerFlex 525 ([`P_PF52x.md`](./P_PF52x.md)).

### Quy trình 4 bước đấu nối và cấu hình `P_PF755`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp`, `Out`, `Ref_GetFaultMSG`, `Ref_GetFaultDest`, `Inp_FwdPermOK`, `Inp_FwdNBPermOK`, `Inp_RevPermOK`, `Inp_RevNBPermOK`, `Inp_IntlkOK`, `Inp_NBIntlkOK`, `Inp_IOFault`, `Inp_Sim`, `Inp_Hand`, `Inp_Ovrd`, `Inp_OvrdCmd`
   - **Các chân tham chiếu dữ liệu (`Ref_*` / `InOut`):** `Ref_GetFaultMSG`, `Ref_GetFaultDest`, `Ref_GetFaultMSG`, `Ref_GetFaultDest` — bắt buộc gán thẻ mảng/cấu trúc tương ứng trong Controller Tags.
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_PF755T`, `Cfg_HasReverse`, `Cfg_HasJog`, `Cfg_AllowLocal`, `Cfg_HasFwdPermObj`, `Cfg_HasRevPermObj`, `Cfg_HasIntlkObj`, `Cfg_HasMoreObj`, `Cfg_HasResInhObj`, `Cfg_HasRunTimeObj`, `Cfg_SetTrack`, `Cfg_SetTrackOvrdHand`, `Cfg_OperStopPrio`, `Cfg_ExtStopPrio`, `Cfg_OCmdResets`, `Cfg_XCmdResets`, `Cfg_OvrdPermIntlk`, `Cfg_ShedOnFailToStart`...
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_Desc`, `Cfg_FwdText`, `Cfg_Label`, `Cfg_RevText`, `Cfg_SpeedFdbkEU`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_PF755` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_PF755` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PSet_SpeedRef`, `PSet_Owner`, `PCmd_StartFwd`, `PCmd_StartRev`, `PCmd_Stop`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_FailToStartAck`, `PCmd_FailToStartSuppress`, `PCmd_FailToStartUnsuppress`, `PCmd_FailToStartUnshelve`, `PCmd_FailToStopAck`, `PCmd_FailToStopSuppress`, `PCmd_FailToStopUnsuppress`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh điều khiển tự động, kiểm tra khối đã sẵn sàng ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển quyền về Program, sau đó gán giá trị vào `PSet_*` và kích xung `PCmd_* = 1` (khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Out_Horn`, `Val_SpeedRef`, `Val_SpeedFdbk`, `Val_OutCurrent`, `Val_TorqCurrFdbk`, `Val_OutPower`, `Val_OvldC`, `Val_Temp`, `Val_ElapsedMWHr`, `Val_ElapsedRunT`, `Val_SpeedRefSrc`, `Val_SpeedEUMin`, `Val_SpeedEUMax`, `Val_LastFaultCode`, `Val_Cmd`, `Val_Fdbk`, `Val_Sts`, `Val_Fault`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 117 Biến Đầu Vào (`Input Parameters`) của `P_PF755`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp`** | `P_PF755_Inp` | `-` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu điều kiện đầu vào (1 = Kích hoạt điều kiện / Báo động, 0 = Bình thường) |
| **`Out`** | `P_PF755_Out` | `-` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra điều khiển thiết bị (hoặc kết quả cổng điều kiện sau thời gian trễ On-Delay / Off-Delay) |
| **`Ref_GetFaultMSG`** | `MESSAGE` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới thẻ lệnh truyền thông MSG dùng để đọc bản ghi lỗi gần nhất từ thiết bị |
| **`Ref_GetFaultDest`** | `P_PFEmb_FltAlmRec` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới vùng đệm (Buffer) chứa dữ liệu trả về từ lệnh MSG đọc mã lỗi gần nhất |
| **`Inp_FwdPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Điều kiện Cho phép khởi động chiều Thuận (có thể bypass) đều OK |
| **`Inp_FwdNBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Điều kiện Cho phép khởi động chiều Thuận (không thể bypass) đều OK |
| **`Inp_RevPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Điều kiện Cho phép khởi động chiều Nghịch (có thể bypass) đều OK |
| **`Inp_RevNBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Điều kiện Cho phép khởi động chiều Nghịch (không thể bypass) đều OK |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp Khóa liên động an toàn có thể Bypass (1 = Liên động OK cho phép chạy; 0 = Cắt dừng thiết bị ngay lập tức; nối từ P_Intlk.Sts_IntlkOK) |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp Khóa liên động an toàn KHÔNG thể Bypass (1 = Liên động bất khả kháng OK; 0 = Cắt dừng thiết bị ngay lập tức; nối từ P_Intlk.Sts_NBIntlkOK) |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi truyền thông I/O của thiết bị (0 = Tốt/OK, 1 = Lỗi truyền thông I/O Fault) |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân kích hoạt chế độ Mô phỏng (Simulation): Khi = 1, khóa các ngõ ra vật lý ở mức an toàn và dùng giá trị/phản hồi giả lập nội bộ; khi = 0, vận hành thực tế bình thường |
| **`Inp_Hand`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu chọn chế độ Điều khiển bằng tay tại chỗ (Hand/Local): 1 = Chiếm quyền sang chế độ Hand (thường nối từ công tắc cứng tại tủ MCC); 0 = Nhả chế độ Hand |
| **`Inp_Ovrd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu kích hoạt chế độ Cưỡng bức ưu tiên (Override): 1 = Chiếm quyền sang chế độ Override; 0 = Nhả chế độ Override |
| **`Inp_OvrdCmd`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã lệnh điều khiển thiết bị khi ở chế độ Cưỡng bức (Override Command — kiểu số nguyên phụ thuộc vào từng loại thiết bị: 0 = Không tác động, 1 = Dừng/Đóng/Tắt, 2 = Chạy/Mở/Bật...) |
| **`Inp_OvrdSpeed`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tốc độ đặt yêu cầu cho biến tần khi ở chế độ Cưỡng bức (Override Speed Reference, đơn vị EU) |
| **`Inp_ExtIhn`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân khóa cấm nguồn lệnh ngoài (External Inhibit): 1 = Cấm chế độ External chiếm quyền điều khiển; 0 = Cho phép External chiếm quyền |
| **`Inp_HornInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân khóa cấm còi báo động tại hiện trường: 1 = Cấm phát còi báo (Inhibit Horn); 0 = Cho phép phát còi báo trước khi khởi động |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Cfg_PF755T`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cấu hình tương thích cho dòng biến tần PowerFlex 755T (755TL / 755TR / 755TM) |
| **`Cfg_HasReverse`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép biến tần chạy cả 2 chiều Thuận và Nghịch; 0 = Chỉ cho phép chạy 1 chiều Thuận (Forward Only) |
| **`Cfg_HasJog`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép và hiển thị nút nhấn Nhấp thử (Jog) trên màn hình HMI; 0 = Khóa cấm lệnh Jog |
| **`Cfg_AllowLocal`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép khởi động/dừng động cơ tại chỗ ở tủ MCC mà không phát báo động lỗi; 0 = Chỉ cho phép điều khiển từ HMI hoặc Program |
| **`Cfg_HasFwdPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối P_Perm nối vào chân cho phép chạy Thuận (Inp_FwdPermOK) |
| **`Cfg_HasRevPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối P_Perm nối vào chân cho phép chạy Nghịch (Inp_RevPermOK) |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Khóa liên động (P_Intlk) nối vào chân Inp_IntlkOK và bật nút điều hướng mở Faceplate P_Intlk |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_HasResInhObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Cấm khởi động lại (P_ResInh) đi kèm và bật nút điều hướng mở Faceplate P_ResInh |
| **`Cfg_HasRunTimeObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Đếm giờ chạy (P_RunTime) đi kèm và bật nút điều hướng mở Faceplate P_RunTime |
| **`Cfg_SetTrack`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép các giá trị đặt (PSet_CV / OSet_CV / SP) tự động bám theo ngõ ra thực tế hiện hành để chuyển mạch không giật (Bumpless Transfer) giữa Operator và Program |
| **`Cfg_SetTrackOvrdHand`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép giá trị đặt của Program và Operator tự động bám theo giá trị CV khi đang ở chế độ Override hoặc Hand |
| **`Cfg_OperStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Nút nhấn Dừng của Người vận hành (OCmd_Stop) có quyền ưu tiên cao nhất, bấm dừng được ngay cả khi máy đang chạy ở chế độ Program; 0 = OCmd_Stop chỉ có tác dụng khi ở chế độ Operator/Maintenance |
| **`Cfg_ExtStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Lệnh Dừng từ hệ thống ngoài (XCmd_Stop) có hiệu lực ưu tiên ở mọi chế độ; 0 = Chỉ có hiệu lực khi ở chế độ External |
| **`Cfg_OCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khi Người vận hành ra lệnh Chạy/Mở mới (OCmd_*), khối tự động xóa luôn các chốt lỗi cũ; 0 = Bắt buộc phải nhấn nút Reset trước khi ra lệnh mới |
| **`Cfg_XCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khi hệ thống ngoài gửi lệnh Chạy/Mở mới (XCmd_*), khối tự động xóa luôn các chốt lỗi cũ; 0 = Bắt buộc phải gửi xung XCmd_Reset trước |
| **`Cfg_OvrdPermIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Chế độ Cưỡng bức (Override) được quyền bỏ qua các điều kiện Cho phép và Liên động có thể bypass; 0 = Chế độ Override vẫn phải tuân thủ mọi điều kiện |
| **`Cfg_ShedOnFailToStart`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt lệnh chạy (Shed) và phát báo động khi động cơ bị lỗi Không khởi động được (Fail to Start); 0 = Chỉ phát báo động nhưng vẫn giữ ngõ ra |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt dừng thiết bị về trạng thái an toàn (Shed) và phát báo động khi lỗi truyền thông thẻ I/O; 0 = Chỉ phát báo động |
| **`Cfg_DecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho tốc độ/giá trị trên màn hình HMI (từ 0 đến 6 chữ số) |
| **`Cfg_HasFailToStartAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi không khởi động được (Fail to Start): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasFailToStopAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi không dừng được (Fail to Stop): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasDriveFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi biến tần / bộ truyền động (Drive Fault): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_FailToStartResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi không khởi động được (Fail to Start): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_FailToStopResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi không dừng được (Fail to Stop): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_DriveFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi biến tần / bộ truyền động (Drive Fault): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_FailToStartAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi không khởi động được (Fail to Start): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_FailToStopAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi không dừng được (Fail to Stop): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_DriveFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi biến tần / bộ truyền động (Drive Fault): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_FailToStartSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi không khởi động được (Fail to Start) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_FailToStopSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi không dừng được (Fail to Stop) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_DriveFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_MinSpdRef`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp dưới tối thiểu cho phép đặt của tốc độ biến tần (Minimum Speed Reference, đơn vị EU) |
| **`Cfg_MaxSpdRef`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp trên tối đa cho phép đặt của tốc độ biến tần (Maximum Speed Reference, đơn vị EU) |
| **`Cfg_SpeedEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị tốc độ theo đơn vị kỹ thuật tối thiểu (Speed EU Min, ví dụ: 0.0 Hz / 0 RPM) |
| **`Cfg_SpeedEUMax`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị tốc độ theo đơn vị kỹ thuật tối đa (Speed EU Max, ví dụ: 50.0 Hz / 1500 RPM) |
| **`Cfg_SpeedRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị tốc độ thô tối thiểu của biến tần (Speed Raw Min, ví dụ: 0) |
| **`Cfg_SpeedRawMax`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị tốc độ thô tối đa của biến tần (Speed Raw Max, ví dụ: 32767 hoặc tần số tối đa) |
| **`Cfg_StartHornT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian hú còi cảnh báo tại hiện trường (Out_Horn) trước khi đóng điện khởi động thiết bị (giây; đặt = 0.0 để tắt còi) |
| **`Cfg_SimRampT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian tăng/giảm tốc độ phản hồi giả lập từ 0 đến 100% trong chế độ Mô phỏng biến tần (giây) |
| **`Cfg_FailToStartT`** | `REAL` | `15` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian chờ tối đa (giây) từ lúc xuất lệnh Chạy đến khi phải nhận được phản hồi chạy (Inp_RunFdbk = 1) trước khi báo lỗi FailToStart |
| **`Cfg_FailToStopT`** | `REAL` | `15` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian chờ tối đa (giây) từ lúc xuất lệnh Dừng đến khi phản hồi chạy phải về 0 trước khi báo lỗi FailToStop |
| **`Cfg_ResetPulseT`** | `REAL` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Độ rộng xung xuất ra chân xóa lỗi biến tần/khởi động mềm (giây) |
| **`Cfg_MaxJogT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian tối đa cho phép giữ lệnh chạy nhấp thử Jog liên tục (giây; đặt = 0.0 là không giới hạn) |
| **`Cfg_OperKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ bit cấu hình cho phép Operator giữ quyền chỉnh thông số ngay cả khi khối ở chế độ Program (.0 = Giữ quyền chọn kênh PV, .1 = Giữ quyền chỉnh ngưỡng cảnh báo) |
| **`Cfg_ProgKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ bit cấu hình cho phép Program giữ quyền chỉnh thông số ngay cả khi khối ở chế độ Operator (.0 = Giữ quyền chọn kênh PV, .1 = Giữ quyền chỉnh ngưỡng cảnh báo) |
| **`Cfg_ExtKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ bit cấu hình cho phép External giữ quyền điều khiển khi ở Program/Operator (.0 = Giữ tốc độ đặt Reference, .1 = Giữ lệnh điều khiển Commands) |
| **`PSet_SpeedRef`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Giá trị tốc độ đặt yêu cầu cho biến tần (Speed Reference, tính theo đơn vị kỹ thuật EU như Hz, RPM hoặc %) |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Mã định danh Chủ sở hữu (Owner ID: ghi khác 0 để đăng ký chiếm quyền điều khiển, ghi 0 để nhả quyền) |
| **`XSet_SpeedRef`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài] Giá trị đặt từ Hệ thống điều khiển ngoài (External): Giá trị tốc độ đặt yêu cầu cho biến tần (Speed Reference, tính theo đơn vị kỹ thuật EU như Hz, RPM hoặc %) |
| **`PCmd_StartFwd`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khởi động chạy Thuận (Start Forward) |
| **`PCmd_StartRev`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khởi động chạy Nghịch (Start Reverse) |
| **`PCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Dừng (Stop) thiết bị/động cơ |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Chương trình tự động (từ Operator sang Program) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Người vận hành (từ Program sang Operator) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa giữ nguồn lệnh hiện tại (không cho phép nguồn lệnh kia giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Đưa nguồn lệnh về chế độ Bình thường mặc định (Normal: Program hoặc Operator theo Cfg_ProgNormal) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`PCmd_FailToStartAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi không khởi động được (Fail to Start) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_FailToStartSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi không khởi động được (Fail to Start) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStartUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi không khởi động được (Fail to Start) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStartUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi không khởi động được (Fail to Start) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_FailToStopAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi không dừng được (Fail to Stop) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_FailToStopSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi không dừng được (Fail to Stop) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStopUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi không dừng được (Fail to Stop) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStopUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi không dừng được (Fail to Stop) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_DriveFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_DriveFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_DriveFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_DriveFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) ngay lập tức (biến xung tự động xóa về 0) |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`XCmd_StartFwd`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Khởi động chạy Thuận (Start Forward) |
| **`XCmd_StartRev`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Khởi động chạy Nghịch (Start Reverse) |
| **`XCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Dừng (Stop) thiết bị/động cơ |
| **`XCmd_JogFwd`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhấp thử chạy Thuận (Jog Forward) |
| **`XCmd_JogRev`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhấp thử chạy Nghịch (Jog Reverse) |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Chiếm quyền điều khiển (Acquire) về chế độ tương ứng |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |

## 3. Bảng chi tiết 123 Biến Đầu Ra (`Output Parameters`) của `P_PF755`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Inp`** | `P_PF755_Inp` | `-` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu điều kiện đầu vào (1 = Kích hoạt điều kiện / Báo động, 0 = Bình thường) |
| **`Out`** | `P_PF755_Out` | `-` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra điều khiển thiết bị (hoặc kết quả cổng điều kiện sau thời gian trễ On-Delay / Off-Delay) |
| **`Ref_GetFaultMSG`** | `MESSAGE` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới thẻ lệnh truyền thông MSG dùng để đọc bản ghi lỗi gần nhất từ thiết bị |
| **`Ref_GetFaultDest`** | `P_PFEmb_FltAlmRec` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới vùng đệm (Buffer) chứa dữ liệu trả về từ lệnh MSG đọc mã lỗi gần nhất |
| **`Out_Horn`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra bật còi cảnh báo âm thanh tại hiện trường trước khi khởi động hoặc chuyển trạng thái thiết bị (1 = Bật còi) |
| **`SrcQ_IO`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng dữ liệu tổng hợp cuối cùng (0 = Tín hiệu thực Tốt đã xác nhận, 1 = Tín hiệu thực Tốt giả định, 2 = Không cấu hình phản hồi/Tốt giả định, 8 = Đang mô phỏng, 9 = Mô phỏng vòng lặp, 10 = Giá trị nhập tay, 16 = Ngoài đặc tính kỹ thuật, 17 = Thay thế tại thiết bị/bus, 18 = Thay thế bởi Bảo trì, 19 = Ngắt bảo vệ giữ giá trị cũ, 20 = Ngắt bảo vệ dùng giá trị thay thế, 32 = Lỗi tín hiệu vượt dải/NaN, 33 = Lỗi kênh I/O, 34 = Lỗi module I/O, 35 = Lỗi cấu hình) |
| **`Val_SpeedRef`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị tốc độ đặt (Speed Reference Target) đang gửi xuống biến tần (đơn vị kỹ thuật EU) |
| **`Val_SpeedFdbk`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị tốc độ phản hồi thực tế (Actual Speed Feedback) đọc từ biến tần (đơn vị kỹ thuật EU) |
| **`Val_OutCurrent`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Dòng điện ngõ ra thực tế của biến tần (Output Current, đơn vị Ampe) |
| **`Val_TorqCurrFdbk`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Dòng điện tạo mô-men xoắn phản hồi của biến tần (Torque Current Feedback) |
| **`Val_OutPower`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Công suất ngõ ra thực tế của biến tần (Output Power, đơn vị kW) |
| **`Val_OvldC`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Bộ đếm phần trăm quá tải nhiệt tích lũy (% Overload Count) |
| **`Val_Temp`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Nhiệt độ bên trong biến tần (Drive Temperature, đơn vị °C) |
| **`Val_ElapsedMWHr`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng điện năng tiêu thụ tích lũy của biến tần (Elapsed Megawatt-Hours, đơn vị MWh) |
| **`Val_ElapsedRunT`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng thời gian vận hành tích lũy đọc từ biến tần (Elapsed Run Time, đơn vị Giờ) |
| **`Val_SpeedRefSrc`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã nguồn tham chiếu tốc độ hiện hành của biến tần (enum: 1 = Ngõ vào A, 2 = Ngõ vào B, 3..7 = Tốc độ đặt trước 3..7, 16..31 = Đặt bằng tay) |
| **`Val_SpeedEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận dưới của dải tốc độ kỹ thuật = MIN(Cfg_SpeedFdbkEUMin, Cfg_SpeedFdbkEUMax) |
| **`Val_SpeedEUMax`** | `REAL` | `130` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận trên của dải tốc độ kỹ thuật = MAX(Cfg_SpeedFdbkEUMin, Cfg_SpeedFdbkEUMax) |
| **`Val_LastFaultCode`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã lỗi gần nhất của biến tần (Last Drive Fault Code) |
| **`Val_Cmd`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã lệnh điều khiển thiết bị đang xuất ra (Device Command enum: 0 = Không tác động/Tắt, 1 = Dừng/Đóng/Off, 2 = Chạy/Mở/On...) |
| **`Val_Fdbk`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái phản hồi thực tế từ thiết bị hiện trường (Device Feedback enum: 0 = Đang dừng/Đang chuyển trạng thái, 1 = Đang chạy/Đóng/Off, 2 = Mở/On, 3 = Phản hồi không hợp lệ) |
| **`Val_Sts`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái hoạt động xác nhận của thiết bị (Device Confirmed Status enum dùng để điều khiển màu sắc và biểu tượng động trên HMI) |
| **`Val_Fault`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái lỗi thiết bị (Device Fault Status — kiểu liệt kê enum: 0 = Không lỗi, 16 = Lỗi khởi động/mở, 17 = Lỗi dừng/đóng, 20 = Lo, 21 = Hi, 24 = LoLo, 25 = HiHi, 32 = Fail/I/O Fault, 34 = Lỗi cấu hình CfgErr) |
| **`Val_NotifyAll`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận tổng hợp của khối này cộng với các đối tượng kênh/quá tải liên kết |
| **`Val_UnackAlmC`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms Count) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã định danh Chủ sở hữu hiện hành của đối tượng (Current Object Owner ID: 0 = Đang không bị chiếm quyền) |
| **`Sts_Stopped`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần được yêu cầu dừng và đã xác nhận Dừng hoàn toàn (Confirmed Stopped) |
| **`Sts_StartingFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần đang khởi động theo chiều Thuận và chờ phản hồi (Starting Forward) |
| **`Sts_StartingRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần đang khởi động theo chiều Nghịch và chờ phản hồi (Starting Reverse) |
| **`Sts_RunningFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần đang chạy theo chiều Thuận ổn định (Confirmed Running Forward) |
| **`Sts_RunningRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần đang chạy theo chiều Nghịch ổn định (Confirmed Running Reverse) |
| **`Sts_StoppingFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Biến tần đang chạy Thuận được yêu cầu dừng và đang giảm tốc chờ dừng hẳn |
| **`Sts_StoppingRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Biến tần đang chạy Nghịch được yêu cầu dừng và đang giảm tốc chờ dừng hẳn |
| **`Sts_JoggingFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần đang chạy nhấp thử theo chiều Thuận (Jogging Forward) |
| **`Sts_JoggingRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần đang chạy nhấp thử theo chiều Nghịch (Jogging Reverse) |
| **`Sts_Horn`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Còi cảnh báo khởi động tại hiện trường đang kêu (Horn Active) |
| **`Sts_CommandDir`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Chiều quay đang được lệnh của biến tần: 1 = Chiều Thuận (Forward), 0 = Chiều Nghịch (Reverse) |
| **`Sts_ActualDir`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Chiều quay thực tế của biến tần: 1 = Đang quay Thuận (Forward), 0 = Đang quay Nghịch (Reverse) |
| **`Sts_Accel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Biến tần đang trong quá trình Tăng tốc (Accelerating) |
| **`Sts_Decel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Biến tần đang trong quá trình Giảm tốc (Decelerating) |
| **`Sts_NotReady`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị chưa sẵn sàng hoạt động (kiểm tra các bit Nrdy_* chi tiết để biết nguyên nhân cụ thể) |
| **`Sts_Alarm`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Biến tần đang có cảnh báo (kiểm tra màn hình biến tần hoặc mã Val_LastAlarmCode) |
| **`Sts_AtSpeed`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Biến tần đang chạy đúng tại tốc độ đặt tham chiếu (At Reference Speed) |
| **`Sts_SpeedLimited`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị tốc độ đặt (Speed Reference) đang bị kẹp tại giới hạn Cfg_MinSpdRef hoặc Cfg_MaxSpdRef |
| **`Sts_DriveSts1`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ trạng thái biến tần số #1 (Drive Status Word 1 — ánh xạ từng bit theo tài liệu biến tần) |
| **`Sts_DriveSts2`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ trạng thái biến tần số #2 (Drive Status Word 2 — ánh xạ từng bit theo tài liệu biến tần) |
| **`Sts_FaultStsA`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ trạng thái lỗi biến tần A (Drive Fault Status A — ánh xạ từng bit) |
| **`Sts_FaultStsB`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ trạng thái lỗi biến tần B (Drive Fault Status B — ánh xạ từng bit) |
| **`Sts_PMSts`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ trạng thái bảo trì dự đoán của biến tần (Predictive Maintenance Status Word) |
| **`Sts_StartInhibits`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ bit báo các nguyên nhân biến tần đang bị khóa cấm khởi động (Drive Start Inhibit Reasons) |
| **`Sts_MsgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi khi thực thi lệnh MSG đọc thông tin mã lỗi/cảnh báo từ biến tần |
| **`Sts_Available`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị sẵn sàng cho Chương trình tự động (Program) điều khiển (đang ở chế độ Program và không có lỗi/liên động ngắt) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass hiện đang được bỏ qua (Bypassed) |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Chế độ Bypass bảo trì đang kích hoạt (gửi sang khối P_Intlk / P_Perm để cho phép bỏ qua các điều kiện có quyền Bypass) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị chưa sẵn sàng hoạt động (kiểm tra các bit Nrdy_* chi tiết để biết nguyên nhân cụ thể) |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_DriveNR`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Biến tần báo trạng thái chưa sẵn sàng (Drive Not Ready) |
| **`Nrdy_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang có lỗi thiết bị ngắt bảo vệ (Shed — yêu cầu nhấn Reset) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Khóa liên động an toàn không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi truyền thông thẻ I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Nrdy_Perm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Cho phép khởi động không thỏa mãn (Permissive Not OK) |
| **`Nrdy_PrioStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng: Đang dính lệnh Dừng ưu tiên từ Operator/External (cần nhấn Reset trước khi chạy lại) |
| **`Nrdy_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng: Thiết bị đang bị cắt bảo vệ quá tải (Tripped tại thiết bị hoặc do lệnh Trip) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoặc cưỡng bức bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có ít nhất một cảnh báo bị Tạm hoãn (Shelved), Vô hiệu hóa (Disabled) hoặc Ức chế (Suppressed) — hiển thị biểu tượng chữ "I" trên HMI |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị đặt bộ định thời gian (Timer Preset) nằm ngoài phạm vi hợp lệ 0..2.147.483 giây |
| **`Err_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thời gian trễ mô phỏng (Cfg_SimFdbkT / Cfg_SimRampT) nằm ngoài phạm vi 0..2.147.483 giây |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình cảnh báo: Thời gian duy trì tối thiểu (Min On Time), thời gian tạm hoãn (Shelf Time) hoặc mức độ nghiêm trọng (Severity 1..1000) không hợp lệ |
| **`Err_EU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải quy đổi đơn vị kỹ thuật (Engineering Units) có giá trị Tối thiểu bằng Tối đa (Min = Max) |
| **`Err_Raw`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải giá trị thô đầu vào (Cfg_InpRawMin = Cfg_InpRawMax) bị trùng nhau |
| **`Err_RefLim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giới hạn kẹp tốc độ đặt bị ngược (Cfg_MinSpdRef > Cfg_MaxSpdRef) |
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
| **`Sts_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi không khởi động được (Fail to Start) |
| **`Alm_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi không khởi động được (Fail to Start) |
| **`Ack_FailToStart`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi không khởi động được (Fail to Start) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_FailToStartDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không khởi động được (Fail to Start) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_FailToStartShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không khởi động được (Fail to Start) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_FailToStartSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không khởi động được (Fail to Start) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi không dừng được (Fail to Stop) |
| **`Alm_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi không dừng được (Fail to Stop) |
| **`Ack_FailToStop`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi không dừng được (Fail to Stop) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_FailToStopDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không dừng được (Fail to Stop) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_FailToStopShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không dừng được (Fail to Stop) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_FailToStopSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không dừng được (Fail to Stop) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`Alm_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`Ack_IntlkTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_IntlkTripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_IntlkTripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_IntlkTripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_DriveFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi biến tần / bộ truyền động (Drive Fault) |
| **`Alm_DriveFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi biến tần / bộ truyền động (Drive Fault) |
| **`Ack_DriveFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi biến tần / bộ truyền động (Drive Fault) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_DriveFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_DriveFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_DriveFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi truyền thông thẻ I/O (I/O Fault) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi truyền thông thẻ I/O (I/O Fault) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`MRdy_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`P_PF755`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_PF755) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 40 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_PF755`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'PowerFlex 755 Variable Frequency Drive'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_FwdText`** | `STRING_16` | `'Forward'` | [Biến cấu hình thông số] Chuỗi văn bản mô tả chiều quay Thuận (Forward Direction) trên HMI |
| **`Cfg_Label`** | `STRING_20` | `'Motor Speed Control'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_RevText`** | `STRING_16` | `'Reverse'` | [Biến cấu hình thông số] Chuỗi văn bản mô tả chiều quay Nghịch (Reverse Direction) trên HMI |
| **`Cfg_SpeedFdbkEU`** | `STRING_8` | `'Hz'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của tốc độ phản hồi biến tần (ví dụ: "Hz", "RPM", "%") hiển thị trên HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_PF755'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc nội bộ quản lý phân quyền và chuyển mạch giữa 7 nguồn lệnh điều khiển (OoS, Hand, Maint, Ovrd, Ext, Prog, Oper) |
| **`DriveFault`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi biến tần / bộ truyền động (Drive Fault) |
| **`FailToStart`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi không khởi động được (Fail to Start) |
| **`FailToStop`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi không dừng được (Fail to Stop) |
| **`HMI_HasMore`** | `BOOL` | `0` | Cờ nội bộ báo cho HMI hiển thị nút mở rộng thông tin |
| **`HMI_Lib`** | `STRING_8` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_12` | `'P_PF755'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`IOFault`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bỏ qua (Bypass) tất cả các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Kiểm tra lại bình thường (Check / Gỡ bỏ Bypass) toàn bộ điều kiện Khóa liên động và Cho phép |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (Bỏ qua (Bypass) tất cả các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (Kiểm tra lại bình thường (Check / Gỡ bỏ Bypass) toàn bộ điều kiện Khóa liên động và Cho phép), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`OCmd_JogFwd`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Nhấp thử chạy Thuận (Jog Forward) |
| **`OCmd_JogRev`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Nhấp thử chạy Nghịch (Jog Reverse) |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo |
| **`OCmd_StartFwd`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Khởi động chạy Thuận (Start Forward) |
| **`OCmd_StartRev`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Khởi động chạy Nghịch (Start Reverse) |
| **`OCmd_Stop`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Dừng (Stop) thiết bị/động cơ |
| **`ORdy_JogFwd`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_JogFwd (Nhấp thử chạy Thuận (Jog Forward)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_JogRev`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_JogRev (Nhấp thử chạy Nghịch (Jog Reverse)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_SpeedRef`** | `BOOL` | `0` | [Cờ cho phép nhập dữ liệu trên HMI] 1 = Sẵn sàng nhận giá trị đặt OSet_SpeedRef (Giá trị tốc độ đặt yêu cầu cho biến tần (Speed Reference, tính theo đơn vị kỹ thuật EU như Hz, RPM hoặc %)), cho phép mở ô nhập số trên HMI Faceplate |
| **`ORdy_StartFwd`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_StartFwd (Khởi động chạy Thuận (Start Forward)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_StartRev`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_StartRev (Khởi động chạy Nghịch (Start Reverse)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Stop`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Stop (Dừng (Stop) thiết bị/động cơ), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`OSet_SpeedRef`** | `REAL` | `0` | [Giá trị đặt từ Người vận hành HMI] Giá trị đặt từ Người vận hành (Operator HMI): Giá trị tốc độ đặt yêu cầu cho biến tần (Speed Reference, tính theo đơn vị kỹ thuật EU như Hz, RPM hoặc %) |
| **`Val_DriveEU`** | `STRING_8` | `'Hz'` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi đơn vị tốc độ của biến tần (Hz hoặc RPM) để hiển thị trên HMI |
| **`Val_FaultDesc`** | `STRING_16` | `‘Not yet scanned.’` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi văn bản mô tả chi tiết nguyên nhân lỗi biến tần (tra cứu tự động từ mã lỗi Val_LastFaultCode) |
| **`Val_Notify`** | `SINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo hiện hành và trạng thái xác nhận của khối này (0 = Không có cảnh báo, 1 = Đã hết lỗi nhưng chờ Reset/Ack, 2..3 = Thấp, 4..5 = Trung bình, 6..7 = Cao, 8..9 = Khẩn cấp) |
---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_PF6000`
**Thuộc chương:** [Chương 3: Động cơ, Biến tần & Rơ-le Quá tải (Motors & Drives)](../03_CHUONG_3_MOTORS_VA_BIEN_TAN.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Motors 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 192–203)  
**Tệp định nghĩa khối lệnh:** `P_PF6000_4.10.00_RUNG.L5X`  
**Thống kê số lượng biến của `P_PF6000`:** **112** Biến đầu vào (`Input Parameters`) | **111** Biến đầu ra (`Output Parameters`) | **44** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_PF6000` trong Studio 5000
- **Chức năng cốt lõi:** Khối điều khiển biến tần trung thế **Allen-Bradley PowerFlex 6000 Medium Voltage Drive** (2.3 kV – 11 kV). Giám sát máy cắt trung thế đầu vào, trạng thái cầu công suất, dòng/áp/công suất trung thế và giải mã bảng lỗi trung thế.
- **Khi nào CẦN sử dụng:** Khi điều khiển động cơ trung thế công suất lớn (bơm cấp nước thô, quạt ID/FD lò hơi, máy nghiền, máy nén trung thế) dùng **PowerFlex 6000**.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi dùng biến tần trung thế PowerFlex 7000 ([`P_PF7000.md`](./P_PF7000.md)).

### Quy trình 4 bước đấu nối và cấu hình `P_PF6000`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp`, `Out`, `Ref_FaultCodeList`, `Inp_PermOK`, `Inp_NBPermOK`, `Inp_IntlkOK`, `Inp_NBIntlkOK`, `Inp_Sim`, `Inp_Hand`, `Inp_Ovrd`, `Inp_OvrdCmd`, `Inp_OvrdSpeed`, `Inp_ExtInh`, `Inp_HornInh`, `Inp_Reset`
   - **Các chân tham chiếu dữ liệu (`Ref_*` / `InOut`):** `Ref_FaultCodeList`, `Ref_FaultCodeList` — bắt buộc gán thẻ mảng/cấu trúc tương ứng trong Controller Tags.
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_AllowLocal`, `Cfg_HasMoreObj`, `Cfg_HasParRWObj`, `Cfg_HasPermObj`, `Cfg_HasIntlkObj`, `Cfg_HasResInhObj`, `Cfg_HasRunTimeObj`, `Cfg_SetTrack`, `Cfg_SetTrackOvrdHand`, `Cfg_OperStopPrio`, `Cfg_ExtStopPrio`, `Cfg_OCmdResets`, `Cfg_XCmdResets`, `Cfg_OvrdPermIntlk`, `Cfg_ShedOnFailToStart`, `Cfg_ShedOnIOFault`, `Cfg_SimScaleEU`, `Cfg_SimScaleRaw`...
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_Desc`, `Cfg_FwdText`, `Cfg_Label`, `Cfg_RevText`, `Cfg_SpeedFdbkEU`, `Cfg_SpeedRefEU`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_PF6000` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_PF6000` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PSet_SpeedRef`, `PSet_Owner`, `PCmd_Start`, `PCmd_Stop`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_FailToStartAck`, `PCmd_FailToStartSuppress`, `PCmd_FailToStartUnsuppress`, `PCmd_FailToStartUnshelve`, `PCmd_FailToStopAck`, `PCmd_FailToStopSuppress`, `PCmd_FailToStopUnsuppress`, `PCmd_FailToStopUnshelve`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh điều khiển tự động, kiểm tra khối đã sẵn sàng ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển quyền về Program, sau đó gán giá trị vào `PSet_*` và kích xung `PCmd_* = 1` (khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Out_Horn`, `Val_SpeedRef`, `Val_SpeedFdbk`, `Val_SpeedRefRaw`, `Val_SpeedFdbkRaw`, `Val_SpeedRefEUMin`, `Val_SpeedRefEUMax`, `Val_SpeedFdbkEUMin`, `Val_SpeedFdbkEUMax`, `Val_MotorCurrent`, `Val_MotorCurrentPct`, `Val_MotorVoltage`, `Val_OutputPower`, `Val_MotorPowerPct`, `Val_TotalPowerPct`, `Val_MotorSpeedPct`, `Val_Cmd`, `Val_Fdbk`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 112 Biến Đầu Vào (`Input Parameters`) của `P_PF6000`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp`** | `P_PF6000_Inp` | `-` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu điều kiện đầu vào (1 = Kích hoạt điều kiện / Báo động, 0 = Bình thường) |
| **`Out`** | `P_PF6000_Out` | `-` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra điều khiển thiết bị (hoặc kết quả cổng điều kiện sau thời gian trễ On-Delay / Off-Delay) |
| **`Ref_FaultCodeList`** | `P_DescList[1]` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới mảng bảng tra cứu chứa danh sách Mã lỗi (DINT) và Chuỗi mô tả lỗi (STRING) của biến tần/khởi động mềm |
| **`Inp_PermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp điều kiện Cho phép khởi động có thể Bypass (1 = Tất cả điều kiện Perm OK, cho phép khởi động; thường nối từ P_Perm.Sts_PermOK) |
| **`Inp_NBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp điều kiện Cho phép khởi động KHÔNG thể Bypass (1 = Tất cả điều kiện Non-Bypassable Perm OK; thường nối từ P_Perm.Sts_NBPermOK) |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp Khóa liên động an toàn có thể Bypass (1 = Liên động OK cho phép chạy; 0 = Cắt dừng thiết bị ngay lập tức; nối từ P_Intlk.Sts_IntlkOK) |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp Khóa liên động an toàn KHÔNG thể Bypass (1 = Liên động bất khả kháng OK; 0 = Cắt dừng thiết bị ngay lập tức; nối từ P_Intlk.Sts_NBIntlkOK) |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân kích hoạt chế độ Mô phỏng (Simulation): Khi = 1, khóa các ngõ ra vật lý ở mức an toàn và dùng giá trị/phản hồi giả lập nội bộ; khi = 0, vận hành thực tế bình thường |
| **`Inp_Hand`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu chọn chế độ Điều khiển bằng tay tại chỗ (Hand/Local): 1 = Chiếm quyền sang chế độ Hand (thường nối từ công tắc cứng tại tủ MCC); 0 = Nhả chế độ Hand |
| **`Inp_Ovrd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu kích hoạt chế độ Cưỡng bức ưu tiên (Override): 1 = Chiếm quyền sang chế độ Override; 0 = Nhả chế độ Override |
| **`Inp_OvrdCmd`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã lệnh điều khiển thiết bị khi ở chế độ Cưỡng bức (Override Command — kiểu số nguyên phụ thuộc vào từng loại thiết bị: 0 = Không tác động, 1 = Dừng/Đóng/Tắt, 2 = Chạy/Mở/Bật...) |
| **`Inp_OvrdSpeed`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tốc độ đặt yêu cầu cho biến tần khi ở chế độ Cưỡng bức (Override Speed Reference, đơn vị EU) |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân khóa cấm nguồn lệnh ngoài (External Inhibit): 1 = Cấm chế độ External chiếm quyền điều khiển; 0 = Cho phép External chiếm quyền |
| **`Inp_HornInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân khóa cấm còi báo động tại hiện trường: 1 = Cấm phát còi báo (Inhibit Horn); 0 = Cho phép phát còi báo trước khi khởi động |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Cfg_AllowLocal`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép khởi động/dừng động cơ tại chỗ ở tủ MCC mà không phát báo động lỗi; 0 = Chỉ cho phép điều khiển từ HMI hoặc Program |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_HasParRWObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép bật nút điều hướng mở cửa sổ đọc/ghi tham số biến tần (Parameter Read/Write Popup) trên HMI |
| **`Cfg_HasPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Điều kiện cho phép (P_Perm) nối vào chân Inp_PermOK và bật nút điều hướng mở Faceplate P_Perm |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Khóa liên động (P_Intlk) nối vào chân Inp_IntlkOK và bật nút điều hướng mở Faceplate P_Intlk |
| **`Cfg_HasResInhObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Cấm khởi động lại (P_ResInh) đi kèm và bật nút điều hướng mở Faceplate P_ResInh |
| **`Cfg_HasRunTimeObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Đếm giờ chạy (P_RunTime) đi kèm và bật nút điều hướng mở Faceplate P_RunTime |
| **`Cfg_SetTrack`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép các giá trị đặt (PSet_CV / OSet_CV / SP) tự động bám theo ngõ ra thực tế hiện hành để chuyển mạch không giật (Bumpless Transfer) giữa Operator và Program |
| **`Cfg_SetTrackOvrdHand`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép giá trị đặt của Program và Operator tự động bám theo giá trị CV khi đang ở chế độ Override hoặc Hand |
| **`Cfg_OperStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Nút nhấn Dừng của Người vận hành (OCmd_Stop) có quyền ưu tiên cao nhất, bấm dừng được ngay cả khi máy đang chạy ở chế độ Program; 0 = OCmd_Stop chỉ có tác dụng khi ở chế độ Operator/Maintenance |
| **`Cfg_ExtStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Lệnh Dừng từ hệ thống ngoài (XCmd_Stop) có hiệu lực ưu tiên ở mọi chế độ; 0 = Chỉ có hiệu lực khi ở chế độ External |
| **`Cfg_OCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khi Người vận hành ra lệnh Chạy/Mở mới (OCmd_*), khối tự động xóa luôn các chốt lỗi cũ; 0 = Bắt buộc phải nhấn nút Reset trước khi ra lệnh mới |
| **`Cfg_XCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khi hệ thống ngoài gửi lệnh Chạy/Mở mới (XCmd_*), khối tự động xóa luôn các chốt lỗi cũ; 0 = Bắt buộc phải gửi xung XCmd_Reset trước |
| **`Cfg_OvrdPermIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Chế độ Cưỡng bức (Override) được quyền bỏ qua các điều kiện Cho phép và Liên động có thể bypass; 0 = Chế độ Override vẫn phải tuân thủ mọi điều kiện |
| **`Cfg_ShedOnFailToStart`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt lệnh chạy (Shed) và phát báo động khi động cơ bị lỗi Không khởi động được (Fail to Start); 0 = Chỉ phát báo động nhưng vẫn giữ ngõ ra |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt dừng thiết bị về trạng thái an toàn (Shed) và phát báo động khi lỗi truyền thông thẻ I/O; 0 = Chỉ phát báo động |
| **`Cfg_SimScaleEU`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Trong chế độ mô phỏng, quy đổi tốc độ đặt sang tốc độ phản hồi theo đơn vị kỹ thuật EU |
| **`Cfg_SimScaleRaw`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Trong chế độ mô phỏng, quy đổi tốc độ đặt sang tốc độ phản hồi theo đơn vị thô Raw |
| **`Cfg_DecPlcs`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho tốc độ/giá trị trên màn hình HMI (từ 0 đến 6 chữ số) |
| **`Cfg_HasFailToStartAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi không khởi động được (Fail to Start): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasFailToStopAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi không dừng được (Fail to Stop): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasDriveFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi biến tần / bộ truyền động (Drive Fault): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_FailToStartResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi không khởi động được (Fail to Start): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_FailToStopResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi không dừng được (Fail to Stop): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_DriveFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi biến tần / bộ truyền động (Drive Fault): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_FailToStartAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi không khởi động được (Fail to Start): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_FailToStopAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi không dừng được (Fail to Stop): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_DriveFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi biến tần / bộ truyền động (Drive Fault): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_FailToStartSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi không khởi động được (Fail to Start) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_FailToStopSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi không dừng được (Fail to Stop) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_DriveFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_MinSpdRef`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp dưới tối thiểu cho phép đặt của tốc độ biến tần (Minimum Speed Reference, đơn vị EU) |
| **`Cfg_MaxSpdRef`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp trên tối đa cho phép đặt của tốc độ biến tần (Maximum Speed Reference, đơn vị EU) |
| **`Cfg_SpeedRefRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị thô tối thiểu của tốc độ đặt gửi xuống biến tần (Speed Reference Raw Min) |
| **`Cfg_SpeedRefRawMax`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị thô tối đa của tốc độ đặt gửi xuống biến tần (Speed Reference Raw Max) |
| **`Cfg_SpeedRefEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tối thiểu của tốc độ đặt (Speed Reference EU Min) |
| **`Cfg_SpeedRefEUMax`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tối đa của tốc độ đặt (Speed Reference EU Max) |
| **`Cfg_SpeedFdbkRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị thô tối thiểu của tốc độ phản hồi từ biến tần (Speed Feedback Raw Min) |
| **`Cfg_SpeedFdbkRawMax`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị thô tối đa của tốc độ phản hồi từ biến tần (Speed Feedback Raw Max) |
| **`Cfg_SpeedFdbkEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tối thiểu của tốc độ phản hồi (Speed Feedback EU Min) |
| **`Cfg_SpeedFdbkEUMax`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tối đa của tốc độ phản hồi (Speed Feedback EU Max) |
| **`Cfg_StartHornT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian hú còi cảnh báo tại hiện trường (Out_Horn) trước khi đóng điện khởi động thiết bị (giây; đặt = 0.0 để tắt còi) |
| **`Cfg_SimRampT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian tăng/giảm tốc độ phản hồi giả lập từ 0 đến 100% trong chế độ Mô phỏng biến tần (giây) |
| **`Cfg_FailToStartT`** | `REAL` | `15` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian chờ tối đa (giây) từ lúc xuất lệnh Chạy đến khi phải nhận được phản hồi chạy (Inp_RunFdbk = 1) trước khi báo lỗi FailToStart |
| **`Cfg_FailToStopT`** | `REAL` | `15` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian chờ tối đa (giây) từ lúc xuất lệnh Dừng đến khi phản hồi chạy phải về 0 trước khi báo lỗi FailToStop |
| **`Cfg_ResetPulseT`** | `REAL` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Độ rộng xung xuất ra chân xóa lỗi biến tần/khởi động mềm (giây) |
| **`Cfg_WatchdogT`** | `REAL` | `5` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian giám sát truyền thông mạng với biến tần (Communications Watchdog Timeout, tính bằng giây) |
| **`Cfg_OperKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ bit cấu hình cho phép Operator giữ quyền chỉnh thông số ngay cả khi khối ở chế độ Program (.0 = Giữ quyền chọn kênh PV, .1 = Giữ quyền chỉnh ngưỡng cảnh báo) |
| **`Cfg_ProgKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ bit cấu hình cho phép Program giữ quyền chỉnh thông số ngay cả khi khối ở chế độ Operator (.0 = Giữ quyền chọn kênh PV, .1 = Giữ quyền chỉnh ngưỡng cảnh báo) |
| **`Cfg_ExtKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ bit cấu hình cho phép External giữ quyền điều khiển khi ở Program/Operator (.0 = Giữ tốc độ đặt Reference, .1 = Giữ lệnh điều khiển Commands) |
| **`PSet_SpeedRef`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Giá trị tốc độ đặt yêu cầu cho biến tần (Speed Reference, tính theo đơn vị kỹ thuật EU như Hz, RPM hoặc %) |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Mã định danh Chủ sở hữu (Owner ID: ghi khác 0 để đăng ký chiếm quyền điều khiển, ghi 0 để nhả quyền) |
| **`XSet_SpeedRef`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài] Giá trị đặt từ Hệ thống điều khiển ngoài (External): Giá trị tốc độ đặt yêu cầu cho biến tần (Speed Reference, tính theo đơn vị kỹ thuật EU như Hz, RPM hoặc %) |
| **`PCmd_Start`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khởi động (Start) thiết bị/động cơ |
| **`PCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Dừng (Stop) thiết bị/động cơ |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Chương trình tự động (từ Operator sang Program) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Người vận hành (từ Program sang Operator) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa giữ nguồn lệnh hiện tại (không cho phép nguồn lệnh kia giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Đưa nguồn lệnh về chế độ Bình thường mặc định (Normal: Program hoặc Operator theo Cfg_ProgNormal) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`PCmd_FailToStartAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi không khởi động được (Fail to Start) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_FailToStartSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi không khởi động được (Fail to Start) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStartUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi không khởi động được (Fail to Start) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStartUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi không khởi động được (Fail to Start) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_FailToStopAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi không dừng được (Fail to Stop) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_FailToStopSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi không dừng được (Fail to Stop) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStopUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi không dừng được (Fail to Stop) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStopUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi không dừng được (Fail to Stop) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_DriveFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_DriveFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_DriveFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_DriveFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) ngay lập tức (biến xung tự động xóa về 0) |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`XCmd_Start`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Khởi động (Start) thiết bị/động cơ |
| **`XCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Dừng (Stop) thiết bị/động cơ |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Chiếm quyền điều khiển (Acquire) về chế độ tương ứng |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |

## 3. Bảng chi tiết 111 Biến Đầu Ra (`Output Parameters`) của `P_PF6000`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Inp`** | `P_PF6000_Inp` | `-` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu điều kiện đầu vào (1 = Kích hoạt điều kiện / Báo động, 0 = Bình thường) |
| **`Out`** | `P_PF6000_Out` | `-` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra điều khiển thiết bị (hoặc kết quả cổng điều kiện sau thời gian trễ On-Delay / Off-Delay) |
| **`Ref_FaultCodeList`** | `P_DescList[1]` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới mảng bảng tra cứu chứa danh sách Mã lỗi (DINT) và Chuỗi mô tả lỗi (STRING) của biến tần/khởi động mềm |
| **`Out_Horn`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra bật còi cảnh báo âm thanh tại hiện trường trước khi khởi động hoặc chuyển trạng thái thiết bị (1 = Bật còi) |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng dữ liệu tổng hợp cuối cùng (0 = Tín hiệu thực Tốt đã xác nhận, 1 = Tín hiệu thực Tốt giả định, 2 = Không cấu hình phản hồi/Tốt giả định, 8 = Đang mô phỏng, 9 = Mô phỏng vòng lặp, 10 = Giá trị nhập tay, 16 = Ngoài đặc tính kỹ thuật, 17 = Thay thế tại thiết bị/bus, 18 = Thay thế bởi Bảo trì, 19 = Ngắt bảo vệ giữ giá trị cũ, 20 = Ngắt bảo vệ dùng giá trị thay thế, 32 = Lỗi tín hiệu vượt dải/NaN, 33 = Lỗi kênh I/O, 34 = Lỗi module I/O, 35 = Lỗi cấu hình) |
| **`Val_SpeedRef`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị tốc độ đặt (Speed Reference Target) đang gửi xuống biến tần (đơn vị kỹ thuật EU) |
| **`Val_SpeedFdbk`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị tốc độ phản hồi thực tế (Actual Speed Feedback) đọc từ biến tần (đơn vị kỹ thuật EU) |
| **`Val_SpeedRefRaw`** | `INT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Bản sao giá trị tốc độ đặt gửi xuống biến tần tính theo đơn vị thô (Raw Units) để hiển thị trên HMI Faceplate |
| **`Val_SpeedFdbkRaw`** | `INT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Bản sao giá trị tốc độ phản hồi từ biến tần tính theo đơn vị thô (Raw Units) để hiển thị trên HMI Faceplate |
| **`Val_SpeedRefEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận dưới của dải tốc độ đặt (Speed Reference EU Min) |
| **`Val_SpeedRefEUMax`** | `REAL` | `130` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận trên của dải tốc độ đặt (Speed Reference EU Max) |
| **`Val_SpeedFdbkEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận dưới của dải tốc độ phản hồi (Speed Feedback EU Min) |
| **`Val_SpeedFdbkEUMax`** | `REAL` | `130` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận trên của dải tốc độ phản hồi (Speed Feedback EU Max) |
| **`Val_MotorCurrent`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Dòng điện thực tế cấp cho động cơ (Motor Current, đơn vị Ampe) |
| **`Val_MotorCurrentPct`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Dòng điện động cơ tính theo phần trăm dòng định mức (% Full Load Amps - % FLA) |
| **`Val_MotorVoltage`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Điện áp thực tế cấp cho động cơ (Motor Voltage, đơn vị Volt) |
| **`Val_OutputPower`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Công suất ngõ ra thực tế của biến tần (Output Power, đơn vị kW) |
| **`Val_MotorPowerPct`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Công suất động cơ tính theo phần trăm công suất định mức (% Rated Power) |
| **`Val_TotalPowerPct`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng công suất tiêu thụ tính theo phần trăm công suất định mức (% Rated Power) |
| **`Val_MotorSpeedPct`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tốc độ động cơ tính theo phần trăm tốc độ định mức trên nhãn máy (% Nameplate Speed) |
| **`Val_Cmd`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã lệnh điều khiển thiết bị đang xuất ra (Device Command enum: 0 = Không tác động/Tắt, 1 = Dừng/Đóng/Off, 2 = Chạy/Mở/On...) |
| **`Val_Fdbk`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái phản hồi thực tế từ thiết bị hiện trường (Device Feedback enum: 0 = Đang dừng/Đang chuyển trạng thái, 1 = Đang chạy/Đóng/Off, 2 = Mở/On, 3 = Phản hồi không hợp lệ) |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái hoạt động xác nhận của thiết bị (Device Confirmed Status enum dùng để điều khiển màu sắc và biểu tượng động trên HMI) |
| **`Val_Fault`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái lỗi thiết bị (Device Fault Status — kiểu liệt kê enum: 0 = Không lỗi, 16 = Lỗi khởi động/mở, 17 = Lỗi dừng/đóng, 20 = Lo, 21 = Hi, 24 = LoLo, 25 = HiHi, 32 = Fail/I/O Fault, 34 = Lỗi cấu hình CfgErr) |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận tổng hợp của khối này cộng với các đối tượng kênh/quá tải liên kết |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms Count) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã định danh Chủ sở hữu hiện hành của đối tượng (Current Object Owner ID: 0 = Đang không bị chiếm quyền) |
| **`Sts_Stopped`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần được yêu cầu dừng và đã xác nhận Dừng hoàn toàn (Confirmed Stopped) |
| **`Sts_Starting`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần được yêu cầu chạy và đang chờ tín hiệu phản hồi chạy (Starting) |
| **`Sts_Running`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần được yêu cầu chạy và đã xác nhận đang Chạy (Confirmed Running) |
| **`Sts_Stopping`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần được yêu cầu dừng và đang chờ tín hiệu phản hồi về dừng (Stopping) |
| **`Sts_Jogging`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần đang chạy nhấp thử (Jogging) |
| **`Sts_Horn`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Còi cảnh báo khởi động tại hiện trường đang kêu (Horn Active) |
| **`Sts_NotReady`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị chưa sẵn sàng hoạt động (kiểm tra các bit Nrdy_* chi tiết để biết nguyên nhân cụ thể) |
| **`Sts_WatchdogTO`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Quá thời gian giám sát truyền thông với biến tần (Communications Watchdog Timed Out — đứt kết nối mạng) |
| **`Sts_SpeedLimited`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị tốc độ đặt (Speed Reference) đang bị kẹp tại giới hạn Cfg_MinSpdRef hoặc Cfg_MaxSpdRef |
| **`Sts_LogicSts`** | `INT` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ trạng thái logic (Logic Status Word) đọc trực tiếp từ gói dữ liệu Input Assembly của biến tần |
| **`Sts_Available`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị sẵn sàng cho Chương trình tự động (Program) điều khiển (đang ở chế độ Program và không có lỗi/liên động ngắt) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass hiện đang được bỏ qua (Bypassed) |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Chế độ Bypass bảo trì đang kích hoạt (gửi sang khối P_Intlk / P_Perm để cho phép bỏ qua các điều kiện có quyền Bypass) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị chưa sẵn sàng hoạt động (kiểm tra các bit Nrdy_* chi tiết để biết nguyên nhân cụ thể) |
| **`Nrdy_BypassVsDrive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng: Cả chế độ Biến tần (Drive) và Mạch vòng (Bypass) đều đang được chọn đồng thời |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_DriveNR`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Biến tần báo trạng thái chưa sẵn sàng (Drive Not Ready) |
| **`Nrdy_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang có lỗi thiết bị ngắt bảo vệ (Shed — yêu cầu nhấn Reset) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Khóa liên động an toàn không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi truyền thông thẻ I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Nrdy_NotRemote`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng: Chưa chọn chế độ điều khiển từ xa (Remote Control) tại thiết bị, không thể khởi động |
| **`Nrdy_Perm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Cho phép khởi động không thỏa mãn (Permissive Not OK) |
| **`Nrdy_PrioStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng: Đang dính lệnh Dừng ưu tiên từ Operator/External (cần nhấn Reset trước khi chạy lại) |
| **`Nrdy_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng: Thiết bị đang bị cắt bảo vệ quá tải (Tripped tại thiết bị hoặc do lệnh Trip) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoặc cưỡng bức bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có ít nhất một cảnh báo bị Tạm hoãn (Shelved), Vô hiệu hóa (Disabled) hoặc Ức chế (Suppressed) — hiển thị biểu tượng chữ "I" trên HMI |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị đặt bộ định thời gian (Timer Preset) nằm ngoài phạm vi hợp lệ 0..2.147.483 giây |
| **`Err_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thời gian trễ mô phỏng (Cfg_SimFdbkT / Cfg_SimRampT) nằm ngoài phạm vi 0..2.147.483 giây |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình cảnh báo: Thời gian duy trì tối thiểu (Min On Time), thời gian tạm hoãn (Shelf Time) hoặc mức độ nghiêm trọng (Severity 1..1000) không hợp lệ |
| **`Err_FdbkEU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải đơn vị kỹ thuật của tốc độ phản hồi (Speed Feedback EU) có Min = Max |
| **`Err_RefLim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giới hạn kẹp tốc độ đặt bị ngược (Cfg_MinSpdRef > Cfg_MaxSpdRef) |
| **`Err_RefEU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải đơn vị kỹ thuật của tốc độ đặt (Speed Reference EU) có Min = Max |
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
| **`Sts_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi không khởi động được (Fail to Start) |
| **`Alm_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi không khởi động được (Fail to Start) |
| **`Ack_FailToStart`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi không khởi động được (Fail to Start) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_FailToStartDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không khởi động được (Fail to Start) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_FailToStartShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không khởi động được (Fail to Start) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_FailToStartSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không khởi động được (Fail to Start) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi không dừng được (Fail to Stop) |
| **`Alm_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi không dừng được (Fail to Stop) |
| **`Ack_FailToStop`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi không dừng được (Fail to Stop) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_FailToStopDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không dừng được (Fail to Stop) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_FailToStopShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không dừng được (Fail to Stop) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_FailToStopSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không dừng được (Fail to Stop) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`Alm_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`Ack_IntlkTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_IntlkTripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_IntlkTripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_IntlkTripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_DriveFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi biến tần / bộ truyền động (Drive Fault) |
| **`Alm_DriveFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi biến tần / bộ truyền động (Drive Fault) |
| **`Ack_DriveFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi biến tần / bộ truyền động (Drive Fault) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_DriveFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_DriveFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_DriveFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi truyền thông thẻ I/O (I/O Fault) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi truyền thông thẻ I/O (I/O Fault) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`MRdy_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`P_PF6000`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_PF6000) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 44 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_PF6000`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'PF6000 MV Variable Frequency Drive'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_FwdText`** | `STRING_16` | `' '` | [Biến cấu hình thông số] Chuỗi văn bản mô tả chiều quay Thuận (Forward Direction) trên HMI |
| **`Cfg_Label`** | `STRING_20` | `'Motor Speed Control'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_RevText`** | `STRING_16` | `' '` | [Biến cấu hình thông số] Chuỗi văn bản mô tả chiều quay Nghịch (Reverse Direction) trên HMI |
| **`Cfg_SpeedFdbkEU`** | `STRING_8` | `'Hz'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của tốc độ phản hồi biến tần (ví dụ: "Hz", "RPM", "%") hiển thị trên HMI |
| **`Cfg_SpeedRefEU`** | `STRING_8` | `'Hz'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của tốc độ đặt biến tần (ví dụ: "Hz", "RPM", "%") hiển thị trên HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_PF6000'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc nội bộ quản lý phân quyền và chuyển mạch giữa 7 nguồn lệnh điều khiển (OoS, Hand, Maint, Ovrd, Ext, Prog, Oper) |
| **`DriveFault`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi biến tần / bộ truyền động (Drive Fault) |
| **`FailToStart`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi không khởi động được (Fail to Start) |
| **`FailToStop`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi không dừng được (Fail to Stop) |
| **`HMI_FWMajor`** | `DINT` | `5` | Số phiên bản Firmware chính (Major Revision) của biến tần hiển thị trên HMI |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_PF6000'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`IOFault`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bỏ qua (Bypass) tất cả các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Kiểm tra lại bình thường (Check / Gỡ bỏ Bypass) toàn bộ điều kiện Khóa liên động và Cho phép |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (Bỏ qua (Bypass) tất cả các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (Kiểm tra lại bình thường (Check / Gỡ bỏ Bypass) toàn bộ điều kiện Khóa liên động và Cho phép), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo |
| **`OCmd_Start`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Khởi động (Start) thiết bị/động cơ |
| **`OCmd_Stop`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Dừng (Stop) thiết bị/động cơ |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_SpeedRef`** | `BOOL` | `0` | [Cờ cho phép nhập dữ liệu trên HMI] 1 = Sẵn sàng nhận giá trị đặt OSet_SpeedRef (Giá trị tốc độ đặt yêu cầu cho biến tần (Speed Reference, tính theo đơn vị kỹ thuật EU như Hz, RPM hoặc %)), cho phép mở ô nhập số trên HMI Faceplate |
| **`ORdy_Start`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Start (Khởi động (Start) thiết bị/động cơ), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Stop`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Stop (Dừng (Stop) thiết bị/động cơ), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`OSet_SpeedRef`** | `REAL` | `0` | [Giá trị đặt từ Người vận hành HMI] Giá trị đặt từ Người vận hành (Operator HMI): Giá trị tốc độ đặt yêu cầu cho biến tần (Speed Reference, tính theo đơn vị kỹ thuật EU như Hz, RPM hoặc %) |
| **`Val_FaultDesc[0]`** | `STRING_40` | `‘Logic not yet scanned.’` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi văn bản mô tả lỗi biến tần (phần tử [0]) tra cứu từ mã lỗi gần nhất |
| **`Val_FaultDesc[1]`** | `STRING_40` | `"  (Empty string, no characters)` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi văn bản mô tả lỗi biến tần (phần tử [1]) tra cứu từ mã lỗi gần nhất |
| **`Val_FaultDesc[2]`** | `STRING_40` | `"  (Empty string, no characters)` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi văn bản mô tả lỗi biến tần (phần tử [2]) tra cứu từ mã lỗi gần nhất |
| **`Val_FaultDesc[3]`** | `STRING_40` | `"  (Empty string, no characters)` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi văn bản mô tả lỗi biến tần (phần tử [3]) tra cứu từ mã lỗi gần nhất |
| **`Val_FaultDesc[4]`** | `STRING_40` | `"  (Empty string, no characters)` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi văn bản mô tả lỗi biến tần (phần tử [4]) tra cứu từ mã lỗi gần nhất |
| **`Val_FaultDesc[5]`** | `STRING_40` | `"  (Empty string, no characters)` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi văn bản mô tả lỗi biến tần (phần tử [5]) tra cứu từ mã lỗi gần nhất |
| **`Val_FaultDesc[6]`** | `STRING_40` | `"  (Empty string, no characters)` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi văn bản mô tả lỗi biến tần (phần tử [6]) tra cứu từ mã lỗi gần nhất |
| **`Val_FaultDesc[7]`** | `STRING_40` | `"  (Empty string, no characters)` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi văn bản mô tả lỗi biến tần (phần tử [7]) tra cứu từ mã lỗi gần nhất |
| **`Val_FaultDesc[8]`** | `STRING_40` | `"  (Empty string, no characters)` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi văn bản mô tả lỗi biến tần (phần tử [8]) tra cứu từ mã lỗi gần nhất |
| **`Val_FaultDesc[9]`** | `STRING_40` | `"  (Empty string, no characters)` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi văn bản mô tả lỗi biến tần (phần tử [9]) tra cứu từ mã lỗi gần nhất |
| **`Val_LastFaultCode`** | `DINT[10]` | `{...}` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã lỗi gần nhất của biến tần (Last Drive Fault Code) |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo hiện hành và trạng thái xác nhận của khối này (0 = Không có cảnh báo, 1 = Đã hết lỗi nhưng chờ Reset/Ack, 2..3 = Thấp, 4..5 = Trung bình, 6..7 = Cao, 8..9 = Khẩn cấp) |
---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_PF7000`
**Thuộc chương:** [Chương 3: Động cơ, Biến tần & Rơ-le Quá tải (Motors & Drives)](../03_CHUONG_3_MOTORS_VA_BIEN_TAN.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Motors 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 204–223)  
**Tệp định nghĩa khối lệnh:** `P_PF7000_4.10.00_RUNG.L5X`  
**Thống kê số lượng biến của `P_PF7000`:** **127** Biến đầu vào (`Input Parameters`) | **149** Biến đầu ra (`Output Parameters`) | **42** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_PF7000` trong Studio 5000
- **Chức năng cốt lõi:** Khối điều khiển biến tần trung thế **Allen-Bradley PowerFlex 7000 Medium Voltage Drive** (công nghệ Direct-to-Drive). Quản lý toàn diện trình tự đóng/cắt máy cắt trung thế, chỉnh lưu/nghịch lưu và chẩn đoán lỗi chuyên sâu.
- **Khi nào CẦN sử dụng:** Khi hệ thống trang bị biến tần trung thế **PowerFlex 7000**.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi dùng biến tần hạ thế hoặc PowerFlex 6000.

### Quy trình 4 bước đấu nối và cấu hình `P_PF7000`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp`, `Out`, `Ref_GetParSrc`, `Ref_GetParMSG`, `Ref_GetParDest`, `Ref_DriveFaultMSG`, `Ref_DriveAlarmMSG`, `Ref_FaultAlarmDest`, `Ref_RunTimeMSG`, `Ref_RunTimeDest`, `Inp_FwdPermOK`, `Inp_FwdNBPermOK`, `Inp_RevPermOK`, `Inp_RevNBPermOK`, `Inp_IntlkOK`
   - **Các chân tham chiếu dữ liệu (`Ref_*` / `InOut`):** `Ref_GetParSrc`, `Ref_GetParMSG`, `Ref_GetParDest`, `Ref_DriveFaultMSG`, `Ref_DriveAlarmMSG`, `Ref_FaultAlarmDest`, `Ref_RunTimeMSG`, `Ref_RunTimeDest`, `Ref_GetParSrc`, `Ref_GetParMSG`, `Ref_GetParDest`, `Ref_DriveFaultMSG`, `Ref_DriveAlarmMSG`, `Ref_FaultAlarmDest`, `Ref_RunTimeMSG`, `Ref_RunTimeDest` — bắt buộc gán thẻ mảng/cấu trúc tương ứng trong Controller Tags.
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_HasJog`, `Cfg_AllowLocal`, `Cfg_HasFwdPermObj`, `Cfg_HasRevPermObj`, `Cfg_HasIntlkObj`, `Cfg_HasMoreObj`, `Cfg_HasResInhObj`, `Cfg_HasRunTimeObj`, `Cfg_SetTrack`, `Cfg_SetTrackOvrdHand`, `Cfg_OperStopPrio`, `Cfg_ExtStopPrio`, `Cfg_OCmdResets`, `Cfg_XCmdResets`, `Cfg_OvrdPermIntlk`, `Cfg_ShedOnFailToStart`, `Cfg_ShedOnIOFault`, `Cfg_SimScaleEU`...
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_Desc`, `Cfg_FwdText`, `Cfg_Label`, `Cfg_RevText`, `Cfg_SpeedFdbkEU`, `Cfg_SpeedRefEU`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_PF7000` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_PF7000` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PSet_SpeedRef`, `PSet_Owner`, `PCmd_StartFwd`, `PCmd_StartRev`, `PCmd_Stop`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_FailToStartAck`, `PCmd_FailToStartSuppress`, `PCmd_FailToStartUnsuppress`, `PCmd_FailToStartUnshelve`, `PCmd_FailToStopAck`, `PCmd_FailToStopSuppress`, `PCmd_FailToStopUnsuppress`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh điều khiển tự động, kiểm tra khối đã sẵn sàng ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển quyền về Program, sau đó gán giá trị vào `PSet_*` và kích xung `PCmd_* = 1` (khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Out_Horn`, `Val_SpeedRef`, `Val_SpeedFdbk`, `Val_SpeedRefRaw`, `Val_SpeedFdbkRaw`, `Val_SpeedRefEUMin`, `Val_SpeedRefEUMax`, `Val_SpeedFdbkEUMin`, `Val_SpeedFdbkEUMax`, `Val_SpeedFdbkRPM`, `Val_TorquePctUnfilt`, `Val_MotorTorquePct`, `Val_MotorCurrent`, `Val_MotorCurrentPct`, `Val_MotorVolts`, `Val_MotorVoltsPct`, `Val_MotorPower`, `Val_MotorPowerPct`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 127 Biến Đầu Vào (`Input Parameters`) của `P_PF7000`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp`** | `P_PF7000_Inp` | `-` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu điều kiện đầu vào (1 = Kích hoạt điều kiện / Báo động, 0 = Bình thường) |
| **`Out`** | `P_PF7000_Out` | `-` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra điều khiển thiết bị (hoặc kết quả cổng điều kiện sau thời gian trễ On-Delay / Off-Delay) |
| **`Ref_GetParSrc`** | `INT[36]` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới cấu trúc dữ liệu ngõ ra (Output Assembly) của biến tần trung thế PowerFlex 7000 |
| **`Ref_GetParMSG`** | `MESSAGE` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới thẻ lệnh truyền thông MSG đọc giá trị tham số tiếp theo của biến tần |
| **`Ref_GetParDest`** | `INT[36]` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới vùng đệm (Buffer) nhận dữ liệu từ lệnh MSG đọc tham số biến tần |
| **`Ref_DriveFaultMSG`** | `MESSAGE` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới thẻ lệnh truyền thông MSG dùng để đọc bản ghi Lỗi (Fault Record) gần nhất của biến tần |
| **`Ref_DriveAlarmMSG`** | `MESSAGE` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới thẻ lệnh truyền thông MSG dùng để đọc bản ghi Cảnh báo (Alarm Record) gần nhất của biến tần |
| **`Ref_FaultAlarmDest`** | `P_PFComm_FltAlmRec` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới vùng đệm (Buffer) chứa dữ liệu trả về từ lệnh MSG đọc bản ghi Lỗi hoặc Cảnh báo của biến tần |
| **`Ref_RunTimeMSG`** | `MESSAGE` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới thẻ lệnh truyền thông MSG đọc tổng thời gian vận hành từ biến tần |
| **`Ref_RunTimeDest`** | `LINT` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới vùng đệm (Buffer) nhận dữ liệu thời gian chạy tích lũy từ lệnh MSG |
| **`Inp_FwdPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Điều kiện Cho phép khởi động chiều Thuận (có thể bypass) đều OK |
| **`Inp_FwdNBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Điều kiện Cho phép khởi động chiều Thuận (không thể bypass) đều OK |
| **`Inp_RevPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Điều kiện Cho phép khởi động chiều Nghịch (có thể bypass) đều OK |
| **`Inp_RevNBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Điều kiện Cho phép khởi động chiều Nghịch (không thể bypass) đều OK |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp Khóa liên động an toàn có thể Bypass (1 = Liên động OK cho phép chạy; 0 = Cắt dừng thiết bị ngay lập tức; nối từ P_Intlk.Sts_IntlkOK) |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp Khóa liên động an toàn KHÔNG thể Bypass (1 = Liên động bất khả kháng OK; 0 = Cắt dừng thiết bị ngay lập tức; nối từ P_Intlk.Sts_NBIntlkOK) |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi truyền thông I/O của thiết bị (0 = Tốt/OK, 1 = Lỗi truyền thông I/O Fault) |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân kích hoạt chế độ Mô phỏng (Simulation): Khi = 1, khóa các ngõ ra vật lý ở mức an toàn và dùng giá trị/phản hồi giả lập nội bộ; khi = 0, vận hành thực tế bình thường |
| **`Inp_Hand`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu chọn chế độ Điều khiển bằng tay tại chỗ (Hand/Local): 1 = Chiếm quyền sang chế độ Hand (thường nối từ công tắc cứng tại tủ MCC); 0 = Nhả chế độ Hand |
| **`Inp_Ovrd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu kích hoạt chế độ Cưỡng bức ưu tiên (Override): 1 = Chiếm quyền sang chế độ Override; 0 = Nhả chế độ Override |
| **`Inp_OvrdCmd`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã lệnh điều khiển thiết bị khi ở chế độ Cưỡng bức (Override Command — kiểu số nguyên phụ thuộc vào từng loại thiết bị: 0 = Không tác động, 1 = Dừng/Đóng/Tắt, 2 = Chạy/Mở/Bật...) |
| **`Inp_OvrdSpeed`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tốc độ đặt yêu cầu cho biến tần khi ở chế độ Cưỡng bức (Override Speed Reference, đơn vị EU) |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân khóa cấm nguồn lệnh ngoài (External Inhibit): 1 = Cấm chế độ External chiếm quyền điều khiển; 0 = Cho phép External chiếm quyền |
| **`Inp_HornInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân khóa cấm còi báo động tại hiện trường: 1 = Cấm phát còi báo (Inhibit Horn); 0 = Cho phép phát còi báo trước khi khởi động |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Cfg_HasJog`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép và hiển thị nút nhấn Nhấp thử (Jog) trên màn hình HMI; 0 = Khóa cấm lệnh Jog |
| **`Cfg_AllowLocal`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép khởi động/dừng động cơ tại chỗ ở tủ MCC mà không phát báo động lỗi; 0 = Chỉ cho phép điều khiển từ HMI hoặc Program |
| **`Cfg_HasFwdPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối P_Perm nối vào chân cho phép chạy Thuận (Inp_FwdPermOK) |
| **`Cfg_HasRevPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối P_Perm nối vào chân cho phép chạy Nghịch (Inp_RevPermOK) |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Khóa liên động (P_Intlk) nối vào chân Inp_IntlkOK và bật nút điều hướng mở Faceplate P_Intlk |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_HasResInhObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Cấm khởi động lại (P_ResInh) đi kèm và bật nút điều hướng mở Faceplate P_ResInh |
| **`Cfg_HasRunTimeObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Đếm giờ chạy (P_RunTime) đi kèm và bật nút điều hướng mở Faceplate P_RunTime |
| **`Cfg_SetTrack`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép các giá trị đặt (PSet_CV / OSet_CV / SP) tự động bám theo ngõ ra thực tế hiện hành để chuyển mạch không giật (Bumpless Transfer) giữa Operator và Program |
| **`Cfg_SetTrackOvrdHand`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép giá trị đặt của Program và Operator tự động bám theo giá trị CV khi đang ở chế độ Override hoặc Hand |
| **`Cfg_OperStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Nút nhấn Dừng của Người vận hành (OCmd_Stop) có quyền ưu tiên cao nhất, bấm dừng được ngay cả khi máy đang chạy ở chế độ Program; 0 = OCmd_Stop chỉ có tác dụng khi ở chế độ Operator/Maintenance |
| **`Cfg_ExtStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Lệnh Dừng từ hệ thống ngoài (XCmd_Stop) có hiệu lực ưu tiên ở mọi chế độ; 0 = Chỉ có hiệu lực khi ở chế độ External |
| **`Cfg_OCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khi Người vận hành ra lệnh Chạy/Mở mới (OCmd_*), khối tự động xóa luôn các chốt lỗi cũ; 0 = Bắt buộc phải nhấn nút Reset trước khi ra lệnh mới |
| **`Cfg_XCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khi hệ thống ngoài gửi lệnh Chạy/Mở mới (XCmd_*), khối tự động xóa luôn các chốt lỗi cũ; 0 = Bắt buộc phải gửi xung XCmd_Reset trước |
| **`Cfg_OvrdPermIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Chế độ Cưỡng bức (Override) được quyền bỏ qua các điều kiện Cho phép và Liên động có thể bypass; 0 = Chế độ Override vẫn phải tuân thủ mọi điều kiện |
| **`Cfg_ShedOnFailToStart`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt lệnh chạy (Shed) và phát báo động khi động cơ bị lỗi Không khởi động được (Fail to Start); 0 = Chỉ phát báo động nhưng vẫn giữ ngõ ra |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt dừng thiết bị về trạng thái an toàn (Shed) và phát báo động khi lỗi truyền thông thẻ I/O; 0 = Chỉ phát báo động |
| **`Cfg_SimScaleEU`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Trong chế độ mô phỏng, quy đổi tốc độ đặt sang tốc độ phản hồi theo đơn vị kỹ thuật EU |
| **`Cfg_SimScaleRaw`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Trong chế độ mô phỏng, quy đổi tốc độ đặt sang tốc độ phản hồi theo đơn vị thô Raw |
| **`Cfg_DecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho tốc độ/giá trị trên màn hình HMI (từ 0 đến 6 chữ số) |
| **`Cfg_HasFailToStartAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi không khởi động được (Fail to Start): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasFailToStopAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi không dừng được (Fail to Stop): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasDriveFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi biến tần / bộ truyền động (Drive Fault): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_FailToStartResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi không khởi động được (Fail to Start): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_FailToStopResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi không dừng được (Fail to Stop): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_DriveFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi biến tần / bộ truyền động (Drive Fault): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_FailToStartAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi không khởi động được (Fail to Start): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_FailToStopAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi không dừng được (Fail to Stop): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_DriveFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi biến tần / bộ truyền động (Drive Fault): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_FailToStartSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi không khởi động được (Fail to Start) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_FailToStopSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi không dừng được (Fail to Stop) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_DriveFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_MinSpdRef`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp dưới tối thiểu cho phép đặt của tốc độ biến tần (Minimum Speed Reference, đơn vị EU) |
| **`Cfg_MaxSpdRef`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp trên tối đa cho phép đặt của tốc độ biến tần (Maximum Speed Reference, đơn vị EU) |
| **`Cfg_SpeedRefRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị thô tối thiểu của tốc độ đặt gửi xuống biến tần (Speed Reference Raw Min) |
| **`Cfg_SpeedRefRawMax`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị thô tối đa của tốc độ đặt gửi xuống biến tần (Speed Reference Raw Max) |
| **`Cfg_SpeedRefEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tối thiểu của tốc độ đặt (Speed Reference EU Min) |
| **`Cfg_SpeedRefEUMax`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tối đa của tốc độ đặt (Speed Reference EU Max) |
| **`Cfg_SpeedFdbkRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị thô tối thiểu của tốc độ phản hồi từ biến tần (Speed Feedback Raw Min) |
| **`Cfg_SpeedFdbkRawMax`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị thô tối đa của tốc độ phản hồi từ biến tần (Speed Feedback Raw Max) |
| **`Cfg_SpeedFdbkEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tối thiểu của tốc độ phản hồi (Speed Feedback EU Min) |
| **`Cfg_SpeedFdbkEUMax`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tối đa của tốc độ phản hồi (Speed Feedback EU Max) |
| **`Cfg_StartHornT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian hú còi cảnh báo tại hiện trường (Out_Horn) trước khi đóng điện khởi động thiết bị (giây; đặt = 0.0 để tắt còi) |
| **`Cfg_SimRampT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian tăng/giảm tốc độ phản hồi giả lập từ 0 đến 100% trong chế độ Mô phỏng biến tần (giây) |
| **`Cfg_FailToStartT`** | `REAL` | `15` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian chờ tối đa (giây) từ lúc xuất lệnh Chạy đến khi phải nhận được phản hồi chạy (Inp_RunFdbk = 1) trước khi báo lỗi FailToStart |
| **`Cfg_FailToStopT`** | `REAL` | `15` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian chờ tối đa (giây) từ lúc xuất lệnh Dừng đến khi phản hồi chạy phải về 0 trước khi báo lỗi FailToStop |
| **`Cfg_ResetPulseT`** | `REAL` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Độ rộng xung xuất ra chân xóa lỗi biến tần/khởi động mềm (giây) |
| **`Cfg_MaxJogT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian tối đa cho phép giữ lệnh chạy nhấp thử Jog liên tục (giây; đặt = 0.0 là không giới hạn) |
| **`Cfg_OperKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ bit cấu hình cho phép Operator giữ quyền chỉnh thông số ngay cả khi khối ở chế độ Program (.0 = Giữ quyền chọn kênh PV, .1 = Giữ quyền chỉnh ngưỡng cảnh báo) |
| **`Cfg_ProgKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ bit cấu hình cho phép Program giữ quyền chỉnh thông số ngay cả khi khối ở chế độ Operator (.0 = Giữ quyền chọn kênh PV, .1 = Giữ quyền chỉnh ngưỡng cảnh báo) |
| **`Cfg_ExtKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ bit cấu hình cho phép External giữ quyền điều khiển khi ở Program/Operator (.0 = Giữ tốc độ đặt Reference, .1 = Giữ lệnh điều khiển Commands) |
| **`PSet_SpeedRef`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Giá trị tốc độ đặt yêu cầu cho biến tần (Speed Reference, tính theo đơn vị kỹ thuật EU như Hz, RPM hoặc %) |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Mã định danh Chủ sở hữu (Owner ID: ghi khác 0 để đăng ký chiếm quyền điều khiển, ghi 0 để nhả quyền) |
| **`XSet_SpeedRef`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài] Giá trị đặt từ Hệ thống điều khiển ngoài (External): Giá trị tốc độ đặt yêu cầu cho biến tần (Speed Reference, tính theo đơn vị kỹ thuật EU như Hz, RPM hoặc %) |
| **`PCmd_StartFwd`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khởi động chạy Thuận (Start Forward) |
| **`PCmd_StartRev`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khởi động chạy Nghịch (Start Reverse) |
| **`PCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Dừng (Stop) thiết bị/động cơ |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Chương trình tự động (từ Operator sang Program) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Người vận hành (từ Program sang Operator) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa giữ nguồn lệnh hiện tại (không cho phép nguồn lệnh kia giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Đưa nguồn lệnh về chế độ Bình thường mặc định (Normal: Program hoặc Operator theo Cfg_ProgNormal) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`PCmd_FailToStartAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi không khởi động được (Fail to Start) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_FailToStartSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi không khởi động được (Fail to Start) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStartUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi không khởi động được (Fail to Start) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStartUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi không khởi động được (Fail to Start) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_FailToStopAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi không dừng được (Fail to Stop) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_FailToStopSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi không dừng được (Fail to Stop) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStopUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi không dừng được (Fail to Stop) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStopUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi không dừng được (Fail to Stop) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_DriveFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_DriveFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_DriveFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_DriveFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) ngay lập tức (biến xung tự động xóa về 0) |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`XCmd_StartFwd`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Khởi động chạy Thuận (Start Forward) |
| **`XCmd_StartRev`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Khởi động chạy Nghịch (Start Reverse) |
| **`XCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Dừng (Stop) thiết bị/động cơ |
| **`XCmd_JogFwd`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhấp thử chạy Thuận (Jog Forward) |
| **`XCmd_JogRev`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhấp thử chạy Nghịch (Jog Reverse) |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Chiếm quyền điều khiển (Acquire) về chế độ tương ứng |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |

## 3. Bảng chi tiết 149 Biến Đầu Ra (`Output Parameters`) của `P_PF7000`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Inp`** | `P_PF7000_Inp` | `-` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu điều kiện đầu vào (1 = Kích hoạt điều kiện / Báo động, 0 = Bình thường) |
| **`Out`** | `P_PF7000_Out` | `-` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra điều khiển thiết bị (hoặc kết quả cổng điều kiện sau thời gian trễ On-Delay / Off-Delay) |
| **`Ref_GetParSrc`** | `INT[36]` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới cấu trúc dữ liệu ngõ ra (Output Assembly) của biến tần trung thế PowerFlex 7000 |
| **`Ref_GetParMSG`** | `MESSAGE` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới thẻ lệnh truyền thông MSG đọc giá trị tham số tiếp theo của biến tần |
| **`Ref_GetParDest`** | `INT[36]` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới vùng đệm (Buffer) nhận dữ liệu từ lệnh MSG đọc tham số biến tần |
| **`Ref_DriveFaultMSG`** | `MESSAGE` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới thẻ lệnh truyền thông MSG dùng để đọc bản ghi Lỗi (Fault Record) gần nhất của biến tần |
| **`Ref_DriveAlarmMSG`** | `MESSAGE` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới thẻ lệnh truyền thông MSG dùng để đọc bản ghi Cảnh báo (Alarm Record) gần nhất của biến tần |
| **`Ref_FaultAlarmDest`** | `P_PFComm_FltAlmRec` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới vùng đệm (Buffer) chứa dữ liệu trả về từ lệnh MSG đọc bản ghi Lỗi hoặc Cảnh báo của biến tần |
| **`Ref_RunTimeMSG`** | `MESSAGE` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới thẻ lệnh truyền thông MSG đọc tổng thời gian vận hành từ biến tần |
| **`Ref_RunTimeDest`** | `LINT` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới vùng đệm (Buffer) nhận dữ liệu thời gian chạy tích lũy từ lệnh MSG |
| **`Out_Horn`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra bật còi cảnh báo âm thanh tại hiện trường trước khi khởi động hoặc chuyển trạng thái thiết bị (1 = Bật còi) |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng dữ liệu tổng hợp cuối cùng (0 = Tín hiệu thực Tốt đã xác nhận, 1 = Tín hiệu thực Tốt giả định, 2 = Không cấu hình phản hồi/Tốt giả định, 8 = Đang mô phỏng, 9 = Mô phỏng vòng lặp, 10 = Giá trị nhập tay, 16 = Ngoài đặc tính kỹ thuật, 17 = Thay thế tại thiết bị/bus, 18 = Thay thế bởi Bảo trì, 19 = Ngắt bảo vệ giữ giá trị cũ, 20 = Ngắt bảo vệ dùng giá trị thay thế, 32 = Lỗi tín hiệu vượt dải/NaN, 33 = Lỗi kênh I/O, 34 = Lỗi module I/O, 35 = Lỗi cấu hình) |
| **`Val_SpeedRef`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị tốc độ đặt (Speed Reference Target) đang gửi xuống biến tần (đơn vị kỹ thuật EU) |
| **`Val_SpeedFdbk`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị tốc độ phản hồi thực tế (Actual Speed Feedback) đọc từ biến tần (đơn vị kỹ thuật EU) |
| **`Val_SpeedRefRaw`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Bản sao giá trị tốc độ đặt gửi xuống biến tần tính theo đơn vị thô (Raw Units) để hiển thị trên HMI Faceplate |
| **`Val_SpeedFdbkRaw`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Bản sao giá trị tốc độ phản hồi từ biến tần tính theo đơn vị thô (Raw Units) để hiển thị trên HMI Faceplate |
| **`Val_SpeedRefEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận dưới của dải tốc độ đặt (Speed Reference EU Min) |
| **`Val_SpeedRefEUMax`** | `REAL` | `130` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận trên của dải tốc độ đặt (Speed Reference EU Max) |
| **`Val_SpeedFdbkEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận dưới của dải tốc độ phản hồi (Speed Feedback EU Min) |
| **`Val_SpeedFdbkEUMax`** | `REAL` | `130` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận trên của dải tốc độ phản hồi (Speed Feedback EU Max) |
| **`Val_SpeedFdbkRPM`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tốc độ quay thực tế của động cơ tính bằng vòng/phút (RPM — đọc qua Datalink biến tần) |
| **`Val_TorquePctUnfilt`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mô-men xoắn phản hồi của động cơ chưa lọc nhiễu (% Torque Unfiltered — đọc qua Datalink) |
| **`Val_MotorTorquePct`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mô-men xoắn của động cơ tính theo phần trăm mô-men định mức (% Rated Torque) |
| **`Val_MotorCurrent`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Dòng điện thực tế cấp cho động cơ (Motor Current, đơn vị Ampe) |
| **`Val_MotorCurrentPct`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Dòng điện động cơ tính theo phần trăm dòng định mức (% Full Load Amps - % FLA) |
| **`Val_MotorVolts`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Điện áp thực tế cấp cho động cơ đã lọc nhiễu (Motor Voltage, đơn vị Volt — đọc qua Datalink) |
| **`Val_MotorVoltsPct`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Điện áp động cơ tính theo phần trăm điện áp định mức trên nhãn máy (% Nameplate Volts) |
| **`Val_MotorPower`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Công suất điện thực tế cấp cho động cơ đã lọc nhiễu (Motor Power, đơn vị kW) |
| **`Val_MotorPowerPct`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Công suất động cơ tính theo phần trăm công suất định mức (% Rated Power) |
| **`Val_MotorOvldPct`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mức tích lũy quá tải nhiệt của động cơ (% Motor Overload Count) |
| **`Val_DriveOvldPct`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mức tích lũy quá tải nhiệt của biến tần (% Drive Overload Count) |
| **`Val_RectTemp`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Nhiệt độ cánh tản nhiệt khối Chỉnh lưu của biến tần (Rectifier Heatsink Temperature, đơn vị °C) |
| **`Val_InvTemp`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Nhiệt độ cánh tản nhiệt khối Nghịch lưu của biến tần (Inverter Heatsink Temperature, đơn vị °C) |
| **`Val_RectVoltsPct`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Điện áp đo tại đầu vào cầu chỉnh lưu tính theo phần trăm định mức (%) |
| **`Val_LineVolts`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Điện áp lưới đo tại đầu vào cầu chỉnh lưu của biến tần (Line Input Voltage, đơn vị Volt) |
| **`Val_LineVoltsPct`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Điện áp lưới đầu vào ước tính (trước cuộn kháng) tính theo phần trăm định mức (%) |
| **`Val_LineCurrent`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Dòng điện lưới đầu vào của biến tần (Measured Line Input Current, đơn vị Ampe) |
| **`Val_LineFreq`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tần số điện lưới đầu vào của biến tần (Line Frequency, đơn vị Hz) |
| **`Val_LinePower`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Công suất tác dụng tiêu thụ ở phía lưới đầu vào biến tần (Real Line Power, đơn vị kW) |
| **`Val_LinePowerPct`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Công suất tác dụng đầu vào biến tần tính theo phần trăm công suất định mức (%) |
| **`Val_TotRunTime`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng thời gian vận hành tích lũy của biến tần (Total Elapsed Run Time, đơn vị Giờ) |
| **`Val_LastFaultCode`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã lỗi gần nhất của biến tần (Last Drive Fault Code) |
| **`Val_LastAlarmCode`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã cảnh báo gần nhất của biến tần (Last Drive Alarm Code) |
| **`Val_Cmd`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã lệnh điều khiển thiết bị đang xuất ra (Device Command enum: 0 = Không tác động/Tắt, 1 = Dừng/Đóng/Off, 2 = Chạy/Mở/On...) |
| **`Val_Fdbk`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái phản hồi thực tế từ thiết bị hiện trường (Device Feedback enum: 0 = Đang dừng/Đang chuyển trạng thái, 1 = Đang chạy/Đóng/Off, 2 = Mở/On, 3 = Phản hồi không hợp lệ) |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái hoạt động xác nhận của thiết bị (Device Confirmed Status enum dùng để điều khiển màu sắc và biểu tượng động trên HMI) |
| **`Val_Fault`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái lỗi thiết bị (Device Fault Status — kiểu liệt kê enum: 0 = Không lỗi, 16 = Lỗi khởi động/mở, 17 = Lỗi dừng/đóng, 20 = Lo, 21 = Hi, 24 = LoLo, 25 = HiHi, 32 = Fail/I/O Fault, 34 = Lỗi cấu hình CfgErr) |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận tổng hợp của khối này cộng với các đối tượng kênh/quá tải liên kết |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms Count) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã định danh Chủ sở hữu hiện hành của đối tượng (Current Object Owner ID: 0 = Đang không bị chiếm quyền) |
| **`Sts_Stopped`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần được yêu cầu dừng và đã xác nhận Dừng hoàn toàn (Confirmed Stopped) |
| **`Sts_StartingFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần đang khởi động theo chiều Thuận và chờ phản hồi (Starting Forward) |
| **`Sts_StartingRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần đang khởi động theo chiều Nghịch và chờ phản hồi (Starting Reverse) |
| **`Sts_RunningFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần đang chạy theo chiều Thuận ổn định (Confirmed Running Forward) |
| **`Sts_RunningRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần đang chạy theo chiều Nghịch ổn định (Confirmed Running Reverse) |
| **`Sts_StoppingFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Biến tần đang chạy Thuận được yêu cầu dừng và đang giảm tốc chờ dừng hẳn |
| **`Sts_StoppingRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Biến tần đang chạy Nghịch được yêu cầu dừng và đang giảm tốc chờ dừng hẳn |
| **`Sts_JoggingFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần đang chạy nhấp thử theo chiều Thuận (Jogging Forward) |
| **`Sts_JoggingRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần đang chạy nhấp thử theo chiều Nghịch (Jogging Reverse) |
| **`Sts_Horn`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Còi cảnh báo khởi động tại hiện trường đang kêu (Horn Active) |
| **`Sts_CommandDir`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Chiều quay đang được lệnh của biến tần: 1 = Chiều Thuận (Forward), 0 = Chiều Nghịch (Reverse) |
| **`Sts_ActualDir`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Chiều quay thực tế của biến tần: 1 = Đang quay Thuận (Forward), 0 = Đang quay Nghịch (Reverse) |
| **`Sts_Accel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Biến tần đang trong quá trình Tăng tốc (Accelerating) |
| **`Sts_Decel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Biến tần đang trong quá trình Giảm tốc (Decelerating) |
| **`Sts_NotReady`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị chưa sẵn sàng hoạt động (kiểm tra các bit Nrdy_* chi tiết để biết nguyên nhân cụ thể) |
| **`Sts_DriveAlarm`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Biến tần đang có cảnh báo (Drive Alarm Active) |
| **`Sts_AtSpeed`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Biến tần đang chạy đúng tại tốc độ đặt tham chiếu (At Reference Speed) |
| **`Sts_SpeedLimited`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị tốc độ đặt (Speed Reference) đang bị kẹp tại giới hạn Cfg_MinSpdRef hoặc Cfg_MaxSpdRef |
| **`Sts_LogicSts`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ trạng thái logic (Logic Status Word) đọc trực tiếp từ gói dữ liệu Input Assembly của biến tần |
| **`Sts_DriveSts1`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ trạng thái biến tần số #1 (Drive Status Word 1 — ánh xạ từng bit theo tài liệu biến tần) |
| **`Sts_DriveSts2`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ trạng thái biến tần số #2 (Drive Status Word 2 — ánh xạ từng bit theo tài liệu biến tần) |
| **`Sts_DriveNotRdy1`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ cờ báo nguyên nhân biến tần chưa sẵn sàng số #1 (Drive Not Ready Flag Word 1) |
| **`Sts_DriveNotRdy2`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ cờ báo nguyên nhân biến tần chưa sẵn sàng số #2 (Drive Not Ready Flag Word 2) |
| **`Sts_Contactors`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ trạng thái các Contactor của bộ khởi động mềm (Contactor Status Word) |
| **`Sts_HdwOpts`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ trạng thái các tùy chọn phần cứng gắn thêm (Hardware Options Word) |
| **`Sts_SpecialFeat1`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ trạng thái tính năng đặc biệt số 1 của thiết bị |
| **`Sts_Available`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị sẵn sàng cho Chương trình tự động (Program) điều khiển (đang ở chế độ Program và không có lỗi/liên động ngắt) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass hiện đang được bỏ qua (Bypassed) |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Chế độ Bypass bảo trì đang kích hoạt (gửi sang khối P_Intlk / P_Perm để cho phép bỏ qua các điều kiện có quyền Bypass) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị chưa sẵn sàng hoạt động (kiểm tra các bit Nrdy_* chi tiết để biết nguyên nhân cụ thể) |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_DriveNR`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Biến tần báo trạng thái chưa sẵn sàng (Drive Not Ready) |
| **`Nrdy_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang có lỗi thiết bị ngắt bảo vệ (Shed — yêu cầu nhấn Reset) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Khóa liên động an toàn không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi truyền thông thẻ I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Nrdy_Perm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Cho phép khởi động không thỏa mãn (Permissive Not OK) |
| **`Nrdy_PrioStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng: Đang dính lệnh Dừng ưu tiên từ Operator/External (cần nhấn Reset trước khi chạy lại) |
| **`Nrdy_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng: Thiết bị đang bị cắt bảo vệ quá tải (Tripped tại thiết bị hoặc do lệnh Trip) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoặc cưỡng bức bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có ít nhất một cảnh báo bị Tạm hoãn (Shelved), Vô hiệu hóa (Disabled) hoặc Ức chế (Suppressed) — hiển thị biểu tượng chữ "I" trên HMI |
| **`Sts_MsgErr`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi khi thực thi lệnh MSG đọc thông tin mã lỗi/cảnh báo từ biến tần |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị đặt bộ định thời gian (Timer Preset) nằm ngoài phạm vi hợp lệ 0..2.147.483 giây |
| **`Err_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thời gian trễ mô phỏng (Cfg_SimFdbkT / Cfg_SimRampT) nằm ngoài phạm vi 0..2.147.483 giây |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình cảnh báo: Thời gian duy trì tối thiểu (Min On Time), thời gian tạm hoãn (Shelf Time) hoặc mức độ nghiêm trọng (Severity 1..1000) không hợp lệ |
| **`Err_FdbkEU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải đơn vị kỹ thuật của tốc độ phản hồi (Speed Feedback EU) có Min = Max |
| **`Err_RefLim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giới hạn kẹp tốc độ đặt bị ngược (Cfg_MinSpdRef > Cfg_MaxSpdRef) |
| **`Err_RefEU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải đơn vị kỹ thuật của tốc độ đặt (Speed Reference EU) có Min = Max |
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
| **`Sts_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi không khởi động được (Fail to Start) |
| **`Alm_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi không khởi động được (Fail to Start) |
| **`Ack_FailToStart`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi không khởi động được (Fail to Start) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_FailToStartDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không khởi động được (Fail to Start) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_FailToStartShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không khởi động được (Fail to Start) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_FailToStartSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không khởi động được (Fail to Start) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi không dừng được (Fail to Stop) |
| **`Alm_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi không dừng được (Fail to Stop) |
| **`Ack_FailToStop`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi không dừng được (Fail to Stop) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_FailToStopDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không dừng được (Fail to Stop) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_FailToStopShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không dừng được (Fail to Stop) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_FailToStopSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không dừng được (Fail to Stop) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`Alm_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`Ack_IntlkTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_IntlkTripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_IntlkTripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_IntlkTripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_DriveFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi biến tần / bộ truyền động (Drive Fault) |
| **`Alm_DriveFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi biến tần / bộ truyền động (Drive Fault) |
| **`Ack_DriveFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi biến tần / bộ truyền động (Drive Fault) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_DriveFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_DriveFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_DriveFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi truyền thông thẻ I/O (I/O Fault) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi truyền thông thẻ I/O (I/O Fault) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`MRdy_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`P_PF7000`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_PF7000) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 42 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_PF7000`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'PowerFlex 7000 Variable Frequency Drive'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_FwdText`** | `STRING_16` | `'Forward'` | [Biến cấu hình thông số] Chuỗi văn bản mô tả chiều quay Thuận (Forward Direction) trên HMI |
| **`Cfg_Label`** | `STRING_20` | `'Motor Speed Control'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_RevText`** | `STRING_16` | `'Reverse'` | [Biến cấu hình thông số] Chuỗi văn bản mô tả chiều quay Nghịch (Reverse Direction) trên HMI |
| **`Cfg_SpeedFdbkEU`** | `STRING_8` | `'Hz'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của tốc độ phản hồi biến tần (ví dụ: "Hz", "RPM", "%") hiển thị trên HMI |
| **`Cfg_SpeedRefEU`** | `STRING_8` | `'Hz'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của tốc độ đặt biến tần (ví dụ: "Hz", "RPM", "%") hiển thị trên HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_PF7000'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc nội bộ quản lý phân quyền và chuyển mạch giữa 7 nguồn lệnh điều khiển (OoS, Hand, Maint, Ovrd, Ext, Prog, Oper) |
| **`DriveFault`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi biến tần / bộ truyền động (Drive Fault) |
| **`FailToStart`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi không khởi động được (Fail to Start) |
| **`FailToStop`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi không dừng được (Fail to Stop) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_PF7000'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`IOFault`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bỏ qua (Bypass) tất cả các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Kiểm tra lại bình thường (Check / Gỡ bỏ Bypass) toàn bộ điều kiện Khóa liên động và Cho phép |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (Bỏ qua (Bypass) tất cả các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (Kiểm tra lại bình thường (Check / Gỡ bỏ Bypass) toàn bộ điều kiện Khóa liên động và Cho phép), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`OCmd_JogFwd`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Nhấp thử chạy Thuận (Jog Forward) |
| **`OCmd_JogRev`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Nhấp thử chạy Nghịch (Jog Reverse) |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo |
| **`OCmd_StartFwd`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Khởi động chạy Thuận (Start Forward) |
| **`OCmd_StartRev`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Khởi động chạy Nghịch (Start Reverse) |
| **`OCmd_Stop`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Dừng (Stop) thiết bị/động cơ |
| **`ORdy_JogFwd`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_JogFwd (Nhấp thử chạy Thuận (Jog Forward)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_JogRev`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_JogRev (Nhấp thử chạy Nghịch (Jog Reverse)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_SpeedRef`** | `BOOL` | `0` | [Cờ cho phép nhập dữ liệu trên HMI] 1 = Sẵn sàng nhận giá trị đặt OSet_SpeedRef (Giá trị tốc độ đặt yêu cầu cho biến tần (Speed Reference, tính theo đơn vị kỹ thuật EU như Hz, RPM hoặc %)), cho phép mở ô nhập số trên HMI Faceplate |
| **`ORdy_StartFwd`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_StartFwd (Khởi động chạy Thuận (Start Forward)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_StartRev`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_StartRev (Khởi động chạy Nghịch (Start Reverse)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Stop`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Stop (Dừng (Stop) thiết bị/động cơ), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`OSet_SpeedRef`** | `REAL` | `0` | [Giá trị đặt từ Người vận hành HMI] Giá trị đặt từ Người vận hành (Operator HMI): Giá trị tốc độ đặt yêu cầu cho biến tần (Speed Reference, tính theo đơn vị kỹ thuật EU như Hz, RPM hoặc %) |
| **`Val_AlarmDesc`** | `STRING_16` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi văn bản mô tả chi tiết cảnh báo của biến tần (tra cứu tự động từ mã cảnh báo Val_LastAlarmCode) |
| **`Val_AlarmDT`** | `DateTime` | `{...}` | [Giá trị thực tế đầu ra (Logic/HMI)] Ngày và giờ xảy ra cảnh báo biến tần gần nhất (Alarm Date & Time) |
| **`Val_FaultDesc`** | `STRING_16` | `‘Not yet scanned.’` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi văn bản mô tả chi tiết nguyên nhân lỗi biến tần (tra cứu tự động từ mã lỗi Val_LastFaultCode) |
| **`Val_FaultDT`** | `DateTime` | `{...}` | [Giá trị thực tế đầu ra (Logic/HMI)] Ngày và giờ xảy ra lỗi biến tần gần nhất (Fault Date & Time) |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo hiện hành và trạng thái xác nhận của khối này (0 = Không có cảnh báo, 1 = Đã hết lỗi nhưng chờ Reset/Ack, 2..3 = Thấp, 4..5 = Trung bình, 6..7 = Cao, 8..9 = Khẩn cấp) |
---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_E1PlusE`
**Thuộc chương:** [Chương 3: Động cơ, Biến tần & Rơ-le Quá tải (Motors & Drives)](../03_CHUONG_3_MOTORS_VA_BIEN_TAN.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Motors 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 252–256)  
**Tệp định nghĩa khối lệnh:** `P_E1PlusE_4.10.00_RUNG.L5X`  
**Thống kê số lượng biến của `P_E1PlusE`:** **38** Biến đầu vào (`Input Parameters`) | **49** Biến đầu ra (`Output Parameters`) | **20** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_E1PlusE` trong Studio 5000
- **Chức năng cốt lõi:** Khối giám sát **Rơ-le nhiệt điện tử E1 Plus** có gắn module truyền thông EtherNet/IP `193-ETN`. Đọc % dòng tải (% FLA), trạng thái Trip/Warning và gửi lệnh xóa lỗi từ xa (`Remote Reset`).
- **Khi nào CẦN sử dụng:** Khi tủ MCC dùng rơ-le quá tải **E1 Plus kèm module `193-ETN`** đi cùng với khối động cơ `P_Motor` / `P_MotorRev` / `P_Motor2Spd`.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi dùng rơ-le quá tải thế hệ mới E300 ([`P_E300Ovld.md`](./P_E300Ovld.md)).

### Quy trình 4 bước đấu nối và cấu hình `P_E1PlusE`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp`, `Inp_TripReset`, `Inp_IOFault`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_AllowOperReset`, `Cfg_AllowMaintReset`, `Cfg_HasWarnAlm`, `Cfg_HasTripAlm`, `Cfg_HasMoreObj`, `Cfg_HasIOFaultAlm`, `Cfg_WarnResetReqd`, `Cfg_TripResetReqd`, `Cfg_IOFaultResetReqd`, `Cfg_WarnAckReqd`, `Cfg_TripAckReqd`, `Cfg_IOFaultAckReqd`, `Cfg_WarnSeverity`, `Cfg_TripSeverity`, `Cfg_IOFaultSeverity`, `Cfg_PulseT`, `Cfg_LocalResetReqdT`, `Cfg_LocalResetReqdNum`
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_Desc`, `Cfg_Inp1Txt`, `Cfg_Inp2Txt`, `Cfg_Label`, `Cfg_OutATxt`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_E1PlusE` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_E1PlusE` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PCmd_TripReset`, `PCmd_Reset`, `PCmd_WarnAck`, `PCmd_WarnSuppress`, `PCmd_WarnUnsuppress`, `PCmd_WarnUnshelve`, `PCmd_TripAck`, `PCmd_TripSuppress`, `PCmd_TripUnsuppress`, `PCmd_TripUnshelve`, `PCmd_IOFaultAck`, `PCmd_IOFaultSuppress`, `PCmd_IOFaultUnsuppress`, `PCmd_IOFaultUnshelve`
   - **Cách sử dụng trong code PLC:** Gán giá trị đặt vào `PSet_*` (nếu có) hoặc kích xung 1 chu kỳ vào các bit lệnh `PCmd_* = 1` để xóa lỗi (`PCmd_Reset`), xác nhận cảnh báo (`PCmd_*Ack`) hoặc ức chế cảnh báo (`PCmd_*Suppress`). Khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi.
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Out_TripReset`, `Val_AvgPctFLA`, `Val_PctTherm`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Sts_WarnBits`, `Sts_TripBits`, `Sts_Inp1`, `Sts_Inp2`, `Sts_OutA`, `Sts_MotorCurr`, `Sts_LocalResetReqd`, `Sts_TripLog0`, `Sts_TripLog1`, `Sts_TripLog2`, `Sts_TripLog3`, `Sts_TripLog4`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 38 Biến Đầu Vào (`Input Parameters`) của `P_E1PlusE`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp`** | `P_E1PlusE_Inp` | `-` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu điều kiện đầu vào (1 = Kích hoạt điều kiện / Báo động, 0 = Bình thường) |
| **`Inp_TripReset`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu yêu cầu gửi lệnh xóa lỗi quá tải (Trip Reset) tới rơ-le quá tải (1 = Gửi lệnh Reset) |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi truyền thông I/O của thiết bị (0 = Tốt/OK, 1 = Lỗi truyền thông I/O Fault) |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Cfg_AllowOperReset`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép Người vận hành (Operator) nhấn nút xóa lỗi quá tải (Trip Reset) từ màn hình HMI |
| **`Cfg_AllowMaintReset`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép Nhân viên bảo trì (Maintenance) nhấn nút xóa lỗi quá tải (Trip Reset) từ màn hình HMI |
| **`Cfg_HasWarnAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_WarnResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_TripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_WarnAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_TripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_WarnSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_TripSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_PulseT`** | `REAL` | `5` | Ẩn (Tag) | [Biến cấu hình thông số] Độ rộng thời gian phát xung Reset hoặc Remote Trip gửi xuống rơ-le quá tải (giây) |
| **`Cfg_LocalResetReqdT`** | `REAL` | `3600` | Ẩn (Tag) | [Biến cấu hình thông số] Khung thời gian giám sát số lần Reset quá tải từ xa liên tiếp (giây) |
| **`Cfg_LocalResetReqdNum`** | `DINT` | `3` | Ẩn (Tag) | [Biến cấu hình thông số] Số lần nhấn Reset quá tải từ xa tối đa cho phép trong khoảng thời gian Cfg_LocalResetReqdT trước khi khóa bắt buộc phải xuống Reset tại chỗ ở tủ MCC |
| **`PCmd_TripReset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gửi xung xóa lỗi quá tải (Trip Reset) tới rơ-le nhiệt/biến tần |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`PCmd_WarnAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_WarnSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_WarnUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_WarnUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_TripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_TripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_TripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_TripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) ngay lập tức (biến xung tự động xóa về 0) |
| **`Sts_TripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |

## 3. Bảng chi tiết 49 Biến Đầu Ra (`Output Parameters`) của `P_E1PlusE`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Inp`** | `P_E1PlusE_Inp` | `-` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu điều kiện đầu vào (1 = Kích hoạt điều kiện / Báo động, 0 = Bình thường) |
| **`Out_TripReset`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra phát xung xóa lỗi quá tải cho rơ-le nhiệt (1 = Reset Overload Trip) |
| **`Val_AvgPctFLA`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Dòng điện trung bình 3 pha tính theo phần trăm dòng định mức (% Full Load Amps) |
| **`Val_PctTherm`** | `INT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Phần trăm dung lượng nhiệt đã sử dụng của động cơ (% Thermal Usage — đạt 100% sẽ cắt bảo vệ quá tải) |
| **`SrcQ_IO`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng dữ liệu tổng hợp cuối cùng (0 = Tín hiệu thực Tốt đã xác nhận, 1 = Tín hiệu thực Tốt giả định, 2 = Không cấu hình phản hồi/Tốt giả định, 8 = Đang mô phỏng, 9 = Mô phỏng vòng lặp, 10 = Giá trị nhập tay, 16 = Ngoài đặc tính kỹ thuật, 17 = Thay thế tại thiết bị/bus, 18 = Thay thế bởi Bảo trì, 19 = Ngắt bảo vệ giữ giá trị cũ, 20 = Ngắt bảo vệ dùng giá trị thay thế, 32 = Lỗi tín hiệu vượt dải/NaN, 33 = Lỗi kênh I/O, 34 = Lỗi module I/O, 35 = Lỗi cấu hình) |
| **`Val_Fault`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái lỗi thiết bị (Device Fault Status — kiểu liệt kê enum: 0 = Không lỗi, 16 = Lỗi khởi động/mở, 17 = Lỗi dừng/đóng, 20 = Lo, 21 = Hi, 24 = LoLo, 25 = HiHi, 32 = Fail/I/O Fault, 34 = Lỗi cấu hình CfgErr) |
| **`Val_NotifyAll`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận tổng hợp của khối này cộng với các đối tượng kênh/quá tải liên kết |
| **`Val_UnackAlmC`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms Count) |
| **`Sts_WarnBits`** | `INT` | `2#0000_0000_0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit chi tiết báo nguyên nhân gây cảnh báo sớm (Warning Reason Bits) đọc từ rơ-le quá tải |
| **`Sts_TripBits`** | `INT` | `2#0000_0000_0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit chi tiết báo nguyên nhân gây cắt mạch (Trip Reason Bits) đọc từ rơ-le quá tải |
| **`Sts_Inp1`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái ngõ vào rời rạc số #1 trên rơ-le quá tải |
| **`Sts_Inp2`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái ngõ vào rời rạc số #2 trên rơ-le quá tải |
| **`Sts_OutA`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái tiếp điểm ngõ ra rời rạc A của rơ-le quá tải |
| **`Sts_MotorCurr`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang phát hiện có dòng điện chạy qua động cơ (Motor Current Present) |
| **`Sts_LocalResetReqd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đã vượt quá số lần Reset từ xa cho phép: bắt buộc kỹ thuật viên phải xuống tận tủ MCC kiểm tra và nhấn nút Reset tại chỗ |
| **`Sts_TripLog0`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Mã nguyên nhân cắt bảo vệ trong nhật ký lỗi thứ 0 (Trip Log 0 — 0 là lần cắt gần nhất) |
| **`Sts_TripLog1`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Mã nguyên nhân cắt bảo vệ trong nhật ký lỗi thứ 1 (Trip Log 1 — 0 là lần cắt gần nhất) |
| **`Sts_TripLog2`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Mã nguyên nhân cắt bảo vệ trong nhật ký lỗi thứ 2 (Trip Log 2 — 0 là lần cắt gần nhất) |
| **`Sts_TripLog3`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Mã nguyên nhân cắt bảo vệ trong nhật ký lỗi thứ 3 (Trip Log 3 — 0 là lần cắt gần nhất) |
| **`Sts_TripLog4`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Mã nguyên nhân cắt bảo vệ trong nhật ký lỗi thứ 4 (Trip Log 4 — 0 là lần cắt gần nhất) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị chưa sẵn sàng hoạt động (kiểm tra các bit Nrdy_* chi tiết để biết nguyên nhân cụ thể) |
| **`Nrdy_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng: Thiết bị đang bị cắt bảo vệ quá tải (Tripped tại thiết bị hoặc do lệnh Trip) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi truyền thông thẻ I/O (yêu cầu nhấn Reset) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có ít nhất một cảnh báo bị Tạm hoãn (Shelved), Vô hiệu hóa (Disabled) hoặc Ức chế (Suppressed) — hiển thị biểu tượng chữ "I" trên HMI |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị đặt bộ định thời gian (Timer Preset) nằm ngoài phạm vi hợp lệ 0..2.147.483 giây |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình cảnh báo: Thời gian duy trì tối thiểu (Min On Time), thời gian tạm hoãn (Shelf Time) hoặc mức độ nghiêm trọng (Severity 1..1000) không hợp lệ |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc chốt ngắt bảo vệ (Shed) đã hết lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_Warn`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) |
| **`Alm_Warn`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) |
| **`Ack_Warn`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_WarnDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_WarnShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_WarnSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_Trip`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện rơ-le quá tải tác động cắt mạch (Overload Trip) |
| **`Alm_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho rơ-le quá tải tác động cắt mạch (Overload Trip) |
| **`Ack_Trip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động rơ-le quá tải tác động cắt mạch (Overload Trip) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_TripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_TripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_IOFault`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi truyền thông thẻ I/O (I/O Fault) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi truyền thông thẻ I/O (I/O Fault) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`P_E1PlusE`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_E1PlusE) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 20 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_E1PlusE`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'Motor Overload Relay'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_Inp1Txt`** | `STRING_20` | `'Run Feedback'` | [Biến cấu hình thông số] Nhãn mô tả cho ngõ vào rời rạc số #1 hiển thị trên HMI |
| **`Cfg_Inp2Txt`** | `STRING_20` | `''` | [Biến cấu hình thông số] Nhãn mô tả cho ngõ vào rời rạc số #2 hiển thị trên HMI |
| **`Cfg_Label`** | `STRING_20` | `'Overload Relay'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_OutATxt`** | `STRING_20` | `'Starter Energized'` | [Biến cấu hình thông số] Nhãn mô tả cho ngõ ra Out_A hiển thị trên HMI Faceplate |
| **`Cfg_Tag`** | `STRING_20` | `'P_E1PlusE'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_E1PlusE'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`IOFault`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo |
| **`OCmd_TripReset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Gửi xung xóa lỗi quá tải (Trip Reset) tới rơ-le nhiệt/biến tần |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_TripReset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_TripReset (Gửi xung xóa lỗi quá tải (Trip Reset) tới rơ-le nhiệt/biến tần), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`Trip`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho rơ-le quá tải tác động cắt mạch (Overload Trip) |
| **`Val_Notify`** | `SINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo hiện hành và trạng thái xác nhận của khối này (0 = Không có cảnh báo, 1 = Đã hết lỗi nhưng chờ Reset/Ack, 2..3 = Thấp, 4..5 = Trung bình, 6..7 = Cao, 8..9 = Khẩn cấp) |
| **`Warn`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) |
---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_E3Ovld`
**Thuộc chương:** [Chương 3: Động cơ, Biến tần & Rơ-le Quá tải (Motors & Drives)](../03_CHUONG_3_MOTORS_VA_BIEN_TAN.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Motors 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 257–261)  
**Tệp định nghĩa khối lệnh:** `P_E3Ovld_4.10.00_RUNG.L5X`  
**Thống kê số lượng biến của `P_E3Ovld`:** **43** Biến đầu vào (`Input Parameters`) | **49** Biến đầu ra (`Output Parameters`) | **27** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_E3Ovld` trong Studio 5000
- **Chức năng cốt lõi:** Khối giám sát **Rơ-le bảo vệ quá tải điện tử E3 và E3 Plus** (`193/592-EC1..-EC5`) trên mạng DeviceNet hoặc qua cầu nối `2100-ENET`. Đọc dòng điện từng pha, % dung lượng nhiệt (% Thermal Capacity Used), dòng rò chạm đất và thời gian chờ nguội trước khi Reset.
- **Khi nào CẦN sử dụng:** Khi bảo trì hoặc nâng cấp các tủ MCC IntelliCENTER hiện hữu đang dùng rơ-le **E3 / E3 Plus**.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi thiết kế tủ mới dùng rơ-le E300 ([`P_E300Ovld.md`](./P_E300Ovld.md)).

### Quy trình 4 bước đấu nối và cấu hình `P_E3Ovld`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp`, `Inp_Trip`, `Inp_TripReset`, `Inp_IOFault`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_E3Plus`, `Cfg_EC5`, `Cfg_AllowOperTrip`, `Cfg_AllowOperReset`, `Cfg_AllowMaintTrip`, `Cfg_HasMoreObj`, `Cfg_AllowMaintReset`, `Cfg_HasWarnAlm`, `Cfg_HasTripAlm`, `Cfg_HasIOFaultAlm`, `Cfg_WarnResetReqd`, `Cfg_TripResetReqd`, `Cfg_IOFaultResetReqd`, `Cfg_WarnAckReqd`, `Cfg_TripAckReqd`, `Cfg_IOFaultAckReqd`, `Cfg_WarnSeverity`, `Cfg_TripSeverity`...
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_Desc`, `Cfg_Inp1Txt`, `Cfg_Inp2Txt`, `Cfg_Inp3Txt`, `Cfg_Inp4Txt`, `Cfg_Inp5Txt`, `Cfg_Inp6Txt`, `Cfg_Label`, `Cfg_OutATxt`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_E3Ovld` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_E3Ovld` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PCmd_Trip`, `PCmd_TripReset`, `PCmd_Reset`, `PCmd_WarnAck`, `PCmd_WarnSuppress`, `PCmd_WarnUnsuppress`, `PCmd_WarnUnshelve`, `PCmd_TripAck`, `PCmd_TripSuppress`, `PCmd_TripUnsuppress`, `PCmd_TripUnshelve`, `PCmd_IOFaultAck`, `PCmd_IOFaultSuppress`, `PCmd_IOFaultUnsuppress`, `PCmd_IOFaultUnshelve`
   - **Cách sử dụng trong code PLC:** Gán giá trị đặt vào `PSet_*` (nếu có) hoặc kích xung 1 chu kỳ vào các bit lệnh `PCmd_* = 1` để xóa lỗi (`PCmd_Reset`), xác nhận cảnh báo (`PCmd_*Ack`) hoặc ức chế cảnh báo (`PCmd_*Suppress`). Khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi.
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Out_TripReset`, `Out_RemoteTrip`, `Val_AvgPctFLA`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Sts_WarnBits`, `Sts_TripBits`, `Sts_Inp1`, `Sts_Inp2`, `Sts_Inp3`, `Sts_Inp4`, `Sts_Inp5`, `Sts_Inp6`, `Sts_OutA`, `Sts_OutB`, `Sts_LocalResetReqd`, `Sts_NotRdy`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 43 Biến Đầu Vào (`Input Parameters`) của `P_E3Ovld`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp`** | `P_E3Ovld_Inp` | `-` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu điều kiện đầu vào (1 = Kích hoạt điều kiện / Báo động, 0 = Bình thường) |
| **`Inp_Trip`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu yêu cầu gửi lệnh cắt bảo vệ từ xa (Remote Trip) tới rơ-le quá tải (1 = Gửi lệnh Trip) |
| **`Inp_TripReset`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu yêu cầu gửi lệnh xóa lỗi quá tải (Trip Reset) tới rơ-le quá tải (1 = Gửi lệnh Reset) |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi truyền thông I/O của thiết bị (0 = Tốt/OK, 1 = Lỗi truyền thông I/O Fault) |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Cfg_E3Plus`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Loại phần cứng rơ-le quá tải: 0 = Rơ-le E3 (2 DI, 1 DO); 1 = Rơ-le E3 Plus (4 DI, 2 DO) |
| **`Cfg_EC5`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Loại module rơ-le E3 Plus: 0 = Mã 193-EC2/-EC3 (4 DI, 2 DO); 1 = Mã 193-EC5 (6 DI, 2 DO) |
| **`Cfg_AllowOperTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép Người vận hành kích hoạt lệnh cắt bảo vệ từ xa (Remote Trip) trên HMI |
| **`Cfg_AllowOperReset`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép Người vận hành (Operator) nhấn nút xóa lỗi quá tải (Trip Reset) từ màn hình HMI |
| **`Cfg_AllowMaintTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép Nhân viên bảo trì kích hoạt lệnh cắt bảo vệ từ xa (Remote Trip) trên HMI |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_AllowMaintReset`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép Nhân viên bảo trì (Maintenance) nhấn nút xóa lỗi quá tải (Trip Reset) từ màn hình HMI |
| **`Cfg_HasWarnAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasTripAlm`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_WarnResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_TripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_WarnAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_TripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_WarnSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_TripSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_PulseT`** | `REAL` | `5` | Ẩn (Tag) | [Biến cấu hình thông số] Độ rộng thời gian phát xung Reset hoặc Remote Trip gửi xuống rơ-le quá tải (giây) |
| **`Cfg_LocalResetReqdT`** | `REAL` | `3600` | Ẩn (Tag) | [Biến cấu hình thông số] Khung thời gian giám sát số lần Reset quá tải từ xa liên tiếp (giây) |
| **`Cfg_LocalResetReqdNum`** | `DINT` | `3` | Ẩn (Tag) | [Biến cấu hình thông số] Số lần nhấn Reset quá tải từ xa tối đa cho phép trong khoảng thời gian Cfg_LocalResetReqdT trước khi khóa bắt buộc phải xuống Reset tại chỗ ở tủ MCC |
| **`PCmd_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Cắt khẩn cấp (Trip) thiết bị/động cơ |
| **`PCmd_TripReset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gửi xung xóa lỗi quá tải (Trip Reset) tới rơ-le nhiệt/biến tần |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`PCmd_WarnAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_WarnSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_WarnUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_WarnUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_TripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_TripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_TripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_TripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) ngay lập tức (biến xung tự động xóa về 0) |

## 3. Bảng chi tiết 49 Biến Đầu Ra (`Output Parameters`) của `P_E3Ovld`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Inp`** | `P_E3Ovld_Inp` | `-` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu điều kiện đầu vào (1 = Kích hoạt điều kiện / Báo động, 0 = Bình thường) |
| **`Out_TripReset`** | `BOOL` | `??` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra phát xung xóa lỗi quá tải cho rơ-le nhiệt (1 = Reset Overload Trip) |
| **`Out_RemoteTrip`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra kích hoạt cắt bảo vệ từ xa cho rơ-le quá tải (1 = Kích hoạt Remote Trip) |
| **`Val_AvgPctFLA`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Dòng điện trung bình 3 pha tính theo phần trăm dòng định mức (% Full Load Amps) |
| **`SrcQ_IO`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng dữ liệu tổng hợp cuối cùng (0 = Tín hiệu thực Tốt đã xác nhận, 1 = Tín hiệu thực Tốt giả định, 2 = Không cấu hình phản hồi/Tốt giả định, 8 = Đang mô phỏng, 9 = Mô phỏng vòng lặp, 10 = Giá trị nhập tay, 16 = Ngoài đặc tính kỹ thuật, 17 = Thay thế tại thiết bị/bus, 18 = Thay thế bởi Bảo trì, 19 = Ngắt bảo vệ giữ giá trị cũ, 20 = Ngắt bảo vệ dùng giá trị thay thế, 32 = Lỗi tín hiệu vượt dải/NaN, 33 = Lỗi kênh I/O, 34 = Lỗi module I/O, 35 = Lỗi cấu hình) |
| **`Val_Fault`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái lỗi thiết bị (Device Fault Status — kiểu liệt kê enum: 0 = Không lỗi, 16 = Lỗi khởi động/mở, 17 = Lỗi dừng/đóng, 20 = Lo, 21 = Hi, 24 = LoLo, 25 = HiHi, 32 = Fail/I/O Fault, 34 = Lỗi cấu hình CfgErr) |
| **`Val_NotifyAll`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận tổng hợp của khối này cộng với các đối tượng kênh/quá tải liên kết |
| **`Val_UnackAlmC`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms Count) |
| **`Sts_WarnBits`** | `INT` | `2#0000_0000_0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit chi tiết báo nguyên nhân gây cảnh báo sớm (Warning Reason Bits) đọc từ rơ-le quá tải |
| **`Sts_TripBits`** | `INT` | `2#0000_0000_0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Từ bit chi tiết báo nguyên nhân gây cắt mạch (Trip Reason Bits) đọc từ rơ-le quá tải |
| **`Sts_Inp1`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái ngõ vào rời rạc số #1 trên rơ-le quá tải |
| **`Sts_Inp2`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái ngõ vào rời rạc số #2 trên rơ-le quá tải |
| **`Sts_Inp3`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái ngõ vào rời rạc số #3 trên rơ-le quá tải |
| **`Sts_Inp4`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái ngõ vào rời rạc số #4 trên rơ-le quá tải |
| **`Sts_Inp5`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Trạng thái ngõ vào rời rạc số #5 trên rơ-le quá tải |
| **`Sts_Inp6`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Trạng thái ngõ vào rời rạc số #6 trên rơ-le quá tải |
| **`Sts_OutA`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái tiếp điểm ngõ ra rời rạc A của rơ-le quá tải |
| **`Sts_OutB`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái tiếp điểm ngõ ra rời rạc B của rơ-le quá tải (chỉ có trên E3 Plus) |
| **`Sts_LocalResetReqd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đã vượt quá số lần Reset từ xa cho phép: bắt buộc kỹ thuật viên phải xuống tận tủ MCC kiểm tra và nhấn nút Reset tại chỗ |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị chưa sẵn sàng hoạt động (kiểm tra các bit Nrdy_* chi tiết để biết nguyên nhân cụ thể) |
| **`Nrdy_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng: Thiết bị đang bị cắt bảo vệ quá tải (Tripped tại thiết bị hoặc do lệnh Trip) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi truyền thông thẻ I/O (yêu cầu nhấn Reset) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có ít nhất một cảnh báo bị Tạm hoãn (Shelved), Vô hiệu hóa (Disabled) hoặc Ức chế (Suppressed) — hiển thị biểu tượng chữ "I" trên HMI |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị đặt bộ định thời gian (Timer Preset) nằm ngoài phạm vi hợp lệ 0..2.147.483 giây |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình cảnh báo: Thời gian duy trì tối thiểu (Min On Time), thời gian tạm hoãn (Shelf Time) hoặc mức độ nghiêm trọng (Severity 1..1000) không hợp lệ |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc chốt ngắt bảo vệ (Shed) đã hết lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_Warn`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) |
| **`Alm_Warn`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) |
| **`Ack_Warn`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_WarnDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_WarnShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_WarnSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_Trip`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện rơ-le quá tải tác động cắt mạch (Overload Trip) |
| **`Alm_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho rơ-le quá tải tác động cắt mạch (Overload Trip) |
| **`Ack_Trip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động rơ-le quá tải tác động cắt mạch (Overload Trip) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_TripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_TripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_TripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_IOFault`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi truyền thông thẻ I/O (I/O Fault) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi truyền thông thẻ I/O (I/O Fault) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`P_E3Ovld`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_E3Ovld) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 27 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_E3Ovld`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'Motor Overload Relay'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_Inp1Txt`** | `STRING_20` | `'Run Feedback'` | [Biến cấu hình thông số] Nhãn mô tả cho ngõ vào rời rạc số #1 hiển thị trên HMI |
| **`Cfg_Inp2Txt`** | `STRING_20` | `'Disconnect Closed'` | [Biến cấu hình thông số] Nhãn mô tả cho ngõ vào rời rạc số #2 hiển thị trên HMI |
| **`Cfg_Inp3Txt`** | `STRING_20` | `''` | [Biến cấu hình thông số] Nhãn mô tả cho ngõ vào rời rạc số #3 hiển thị trên HMI |
| **`Cfg_Inp4Txt`** | `STRING_20` | `''` | [Biến cấu hình thông số] Nhãn mô tả cho ngõ vào rời rạc số #4 hiển thị trên HMI |
| **`Cfg_Inp5Txt`** | `STRING_20` | `''` | [Biến cấu hình thông số] Nhãn mô tả cho ngõ vào rời rạc số #5 hiển thị trên HMI |
| **`Cfg_Inp6Txt`** | `STRING_20` | `''` | [Biến cấu hình thông số] Nhãn mô tả cho ngõ vào rời rạc số #6 hiển thị trên HMI |
| **`Cfg_Label`** | `STRING_20` | `'Overload Relay'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_OutATxt`** | `STRING_20` | `'Starter Energized'` | [Biến cấu hình thông số] Nhãn mô tả cho ngõ ra Out_A hiển thị trên HMI Faceplate |
| **`Cfg_OutBTxt`** | `STRING_20` | `''` | [Biến cấu hình thông số] Nhãn mô tả cho ngõ ra Out_B hiển thị trên HMI Faceplate |
| **`Cfg_Tag`** | `STRING_20` | `'P_E3Ovld'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_E3Ovld'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`IOFault`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo |
| **`OCmd_Trip`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Cắt khẩn cấp (Trip) thiết bị/động cơ |
| **`OCmd_TripReset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Gửi xung xóa lỗi quá tải (Trip Reset) tới rơ-le nhiệt/biến tần |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Trip`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Trip (Cắt khẩn cấp (Trip) thiết bị/động cơ), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_TripReset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_TripReset (Gửi xung xóa lỗi quá tải (Trip Reset) tới rơ-le nhiệt/biến tần), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`Trip`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho rơ-le quá tải tác động cắt mạch (Overload Trip) |
| **`Val_Notify`** | `SINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo hiện hành và trạng thái xác nhận của khối này (0 = Không có cảnh báo, 1 = Đã hết lỗi nhưng chờ Reset/Ack, 2..3 = Thấp, 4..5 = Trung bình, 6..7 = Cao, 8..9 = Khẩn cấp) |
| **`Warn`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) |
---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_E300Ovld`
**Thuộc chương:** [Chương 3: Động cơ, Biến tần & Rơ-le Quá tải (Motors & Drives)](../03_CHUONG_3_MOTORS_VA_BIEN_TAN.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Motors 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 262–268)  
**Tệp định nghĩa khối lệnh:** `P_E300Ovld_4.10.00_RUNG.L5X`  
**Thống kê số lượng biến của `P_E300Ovld`:** **43** Biến đầu vào (`Input Parameters`) | **98** Biến đầu ra (`Output Parameters`) | **31** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_E300Ovld` trong Studio 5000
- **Chức năng cốt lõi:** Khối giám sát & điều khiển **Rơ-le bảo vệ động cơ điện tử thế hệ mới E300 (`193-ECM-ETR`)** trên mạng EtherNet/IP. Đọc đầy đủ dòng 3 pha, áp, công suất, % nhiệt tích lũy, đếm ngược thời gian chờ phục hồi nhiệt, cảnh báo sớm sắp quá tải và hỗ trợ `Remote Reset` / `Remote Test Trip`.
- **Khi nào CẦN sử dụng:** Khi thiết kế tủ động lực thông minh (IntelliCENTER MCC) sử dụng rơ-le **E300 EtherNet/IP** kết hợp với `P_Motor*`.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi rơ-le nhiệt chỉ là loại cơ khí thông thường đấu 1 tiếp điểm NC báo lỗi vào `P_DIn` / `P_Intlk`.

### Quy trình 4 bước đấu nối và cấu hình `P_E300Ovld`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp`, `Ref_TripCodeList`, `Ref_WarningCodeList`, `Inp_Trip`, `Inp_TripReset`, `Inp_Reset`
   - **Các chân tham chiếu dữ liệu (`Ref_*` / `InOut`):** `Ref_TripCodeList`, `Ref_WarningCodeList`, `Ref_TripCodeList`, `Ref_WarningCodeList` — bắt buộc gán thẻ mảng/cấu trúc tương ứng trong Controller Tags.
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_AllowOperTrip`, `Cfg_AllowOperReset`, `Cfg_AllowMaintTrip`, `Cfg_HasMoreObj`, `Cfg_AllowMaintReset`, `Cfg_HasWarnAlm`, `Cfg_HasTripAlm`, `Cfg_HasIOFaultAlm`, `Cfg_WarnResetReqd`, `Cfg_TripResetReqd`, `Cfg_IOFaultResetReqd`, `Cfg_WarnAckReqd`, `Cfg_TripAckReqd`, `Cfg_IOFaultAckReqd`, `Cfg_WarnSeverity`, `Cfg_TripSeverity`, `Cfg_IOFaultSeverity`, `Cfg_PulseT`...
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_Desc`, `Cfg_InpPt00Txt`, `Cfg_InpPt01Txt`, `Cfg_InpPt02Txt`, `Cfg_InpPt03Txt`, `Cfg_InpPt04Txt`, `Cfg_InpPt05Txt`, `Cfg_Label`, `Cfg_OutPt00Txt`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_E300Ovld` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_E300Ovld` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PCmd_Trip`, `PCmd_TripReset`, `PCmd_Reset`, `PCmd_WarnAck`, `PCmd_WarnSuppress`, `PCmd_WarnUnsuppress`, `PCmd_WarnUnshelve`, `PCmd_TripAck`, `PCmd_TripSuppress`, `PCmd_TripUnsuppress`, `PCmd_TripUnshelve`, `PCmd_IOFaultAck`, `PCmd_IOFaultSuppress`, `PCmd_IOFaultUnsuppress`, `PCmd_IOFaultUnshelve`
   - **Cách sử dụng trong code PLC:** Gán giá trị đặt vào `PSet_*` (nếu có) hoặc kích xung 1 chu kỳ vào các bit lệnh `PCmd_* = 1` để xóa lỗi (`PCmd_Reset`), xác nhận cảnh báo (`PCmd_*Ack`) hoặc ức chế cảnh báo (`PCmd_*Suppress`). Khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi.
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Out_TripReset`, `Out_RemoteTrip`, `Val_PctTherm`, `Val_CurrImbal`, `Val_AvgPctFLA`, `Val_AvgCurr`, `Val_L1Curr`, `Val_L2Curr`, `Val_L3Curr`, `Val_GndFaultCurr`, `Val_AvgVolts`, `Val_L1L2Volts`, `Val_L2L3Volts`, `Val_L3L1Volts`, `Val_TotRealPwr`, `Val_TotReactPwr`, `Val_TotAppPwr`, `Val_PwrFact`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 43 Biến Đầu Vào (`Input Parameters`) của `P_E300Ovld`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp`** | `P_E300Ovld_Inp` | `-` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu điều kiện đầu vào (1 = Kích hoạt điều kiện / Báo động, 0 = Bình thường) |
| **`Ref_TripCodeList`** | `P_DescList[1]` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới mảng bảng tra cứu danh sách Mã lịch sử cắt (Trip Codes) và mô tả nguyên nhân của rơ-le quá tải |
| **`Ref_WarningCodeList`** | `P_DescList[1]` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới mảng bảng tra cứu danh sách Mã cảnh báo (Warning Codes) và mô tả nguyên nhân của rơ-le quá tải |
| **`Inp_Trip`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu yêu cầu gửi lệnh cắt bảo vệ từ xa (Remote Trip) tới rơ-le quá tải (1 = Gửi lệnh Trip) |
| **`Inp_TripReset`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu yêu cầu gửi lệnh xóa lỗi quá tải (Trip Reset) tới rơ-le quá tải (1 = Gửi lệnh Reset) |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Cfg_AllowOperTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép Người vận hành kích hoạt lệnh cắt bảo vệ từ xa (Remote Trip) trên HMI |
| **`Cfg_AllowOperReset`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép Người vận hành (Operator) nhấn nút xóa lỗi quá tải (Trip Reset) từ màn hình HMI |
| **`Cfg_AllowMaintTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép Nhân viên bảo trì kích hoạt lệnh cắt bảo vệ từ xa (Remote Trip) trên HMI |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_AllowMaintReset`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép Nhân viên bảo trì (Maintenance) nhấn nút xóa lỗi quá tải (Trip Reset) từ màn hình HMI |
| **`Cfg_HasWarnAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_WarnResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_TripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_WarnAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_TripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_WarnSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_TripSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_PulseT`** | `REAL` | `5` | Ẩn (Tag) | [Biến cấu hình thông số] Độ rộng thời gian phát xung Reset hoặc Remote Trip gửi xuống rơ-le quá tải (giây) |
| **`Cfg_LocalResetReqdT`** | `REAL` | `3600` | Ẩn (Tag) | [Biến cấu hình thông số] Khung thời gian giám sát số lần Reset quá tải từ xa liên tiếp (giây) |
| **`Cfg_LocalResetReqdNum`** | `DINT` | `3` | Ẩn (Tag) | [Biến cấu hình thông số] Số lần nhấn Reset quá tải từ xa tối đa cho phép trong khoảng thời gian Cfg_LocalResetReqdT trước khi khóa bắt buộc phải xuống Reset tại chỗ ở tủ MCC |
| **`PCmd_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Cắt khẩn cấp (Trip) thiết bị/động cơ |
| **`PCmd_TripReset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gửi xung xóa lỗi quá tải (Trip Reset) tới rơ-le nhiệt/biến tần |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`PCmd_WarnAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_WarnSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_WarnUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_WarnUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_TripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_TripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_TripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_TripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) ngay lập tức (biến xung tự động xóa về 0) |
| **`Sts_TripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |

## 3. Bảng chi tiết 98 Biến Đầu Ra (`Output Parameters`) của `P_E300Ovld`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Inp`** | `P_E300Ovld_Inp` | `-` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu điều kiện đầu vào (1 = Kích hoạt điều kiện / Báo động, 0 = Bình thường) |
| **`Ref_TripCodeList`** | `P_DescList[1]` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới mảng bảng tra cứu danh sách Mã lịch sử cắt (Trip Codes) và mô tả nguyên nhân của rơ-le quá tải |
| **`Ref_WarningCodeList`** | `P_DescList[1]` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới mảng bảng tra cứu danh sách Mã cảnh báo (Warning Codes) và mô tả nguyên nhân của rơ-le quá tải |
| **`Out_TripReset`** | `BOOL` | `??` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra phát xung xóa lỗi quá tải cho rơ-le nhiệt (1 = Reset Overload Trip) |
| **`Out_RemoteTrip`** | `BOOL` | `??` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra kích hoạt cắt bảo vệ từ xa cho rơ-le quá tải (1 = Kích hoạt Remote Trip) |
| **`Val_PctTherm`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Phần trăm dung lượng nhiệt đã sử dụng của động cơ (% Thermal Usage — đạt 100% sẽ cắt bảo vệ quá tải) |
| **`Val_CurrImbal`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Độ lệch (mất cân bằng) dòng điện giữa 3 pha (% Current Imbalance) |
| **`Val_AvgPctFLA`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Dòng điện trung bình 3 pha tính theo phần trăm dòng định mức (% Full Load Amps) |
| **`Val_AvgCurr`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Dòng điện trung bình 3 pha của động cơ (Average Current, đơn vị Ampe) |
| **`Val_L1Curr`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Dòng điện pha L1 của động cơ (Ampe) |
| **`Val_L2Curr`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Dòng điện pha L2 của động cơ (Ampe) |
| **`Val_L3Curr`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Dòng điện pha L3 của động cơ (Ampe) |
| **`Val_GndFaultCurr`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Dòng điện rò chạm đất đo được (Ground Fault Current, đơn vị Ampe) |
| **`Val_AvgVolts`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Điện áp dây trung bình giữa các pha (Average Line-to-Line Voltage, đơn vị Volt) |
| **`Val_L1L2Volts`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Điện áp dây giữa pha L1 và pha L2 (Volt) |
| **`Val_L2L3Volts`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Điện áp dây giữa pha L2 và pha L3 (Volt) |
| **`Val_L3L1Volts`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Điện áp dây giữa pha L3 và pha L1 (Volt) |
| **`Val_TotRealPwr`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng công suất tác dụng 3 pha (Total Real Power, đơn vị kW) |
| **`Val_TotReactPwr`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng công suất phản kháng 3 pha (Total Reactive Power, đơn vị kVAR) |
| **`Val_TotAppPwr`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng công suất biểu kiến 3 pha (Total Apparent Power, đơn vị kVA) |
| **`Val_PwrFact`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Hệ số công suất cosφ của động cơ (% Power Factor) |
| **`Val_MinToReset`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Số phút còn lại phải chờ nguội trước khi rơ-le quá tải cho phép nhấn Reset (trong định dạng mmm:ss) |
| **`Val_SecToReset`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Số giây lẻ còn lại phải chờ nguội trước khi rơ-le quá tải cho phép nhấn Reset (trong định dạng mmm:ss) |
| **`Val_MinToStart`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Số phút còn lại cho đến khi động cơ có thể khởi động lại (trong định dạng mmm:ss) |
| **`Val_SecToStart`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Số giây lẻ còn lại cho đến khi động cơ có thể khởi động lại (trong định dạng mmm:ss) |
| **`Val_TripCode`** | `INT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã nguyên nhân cắt bảo vệ gần nhất (Trip History 0 Code) đọc từ rơ-le quá tải |
| **`Val_WarningCode`** | `INT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã nguyên nhân cảnh báo quá tải gần nhất (Warning History 0 Code) đọc từ rơ-le quá tải |
| **`Val_InvCfgParam`** | `INT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Số hiệu tham số (Parameter ID #) bị cài đặt sai cấu hình bên trong rơ-le quá tải E300 |
| **`SrcQ_IO`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `SINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng dữ liệu tổng hợp cuối cùng (0 = Tín hiệu thực Tốt đã xác nhận, 1 = Tín hiệu thực Tốt giả định, 2 = Không cấu hình phản hồi/Tốt giả định, 8 = Đang mô phỏng, 9 = Mô phỏng vòng lặp, 10 = Giá trị nhập tay, 16 = Ngoài đặc tính kỹ thuật, 17 = Thay thế tại thiết bị/bus, 18 = Thay thế bởi Bảo trì, 19 = Ngắt bảo vệ giữ giá trị cũ, 20 = Ngắt bảo vệ dùng giá trị thay thế, 32 = Lỗi tín hiệu vượt dải/NaN, 33 = Lỗi kênh I/O, 34 = Lỗi module I/O, 35 = Lỗi cấu hình) |
| **`Val_Fault`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái lỗi thiết bị (Device Fault Status — kiểu liệt kê enum: 0 = Không lỗi, 16 = Lỗi khởi động/mở, 17 = Lỗi dừng/đóng, 20 = Lo, 21 = Hi, 24 = LoLo, 25 = HiHi, 32 = Fail/I/O Fault, 34 = Lỗi cấu hình CfgErr) |
| **`Val_NotifyAll`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận tổng hợp của khối này cộng với các đối tượng kênh/quá tải liên kết |
| **`Val_UnackAlmC`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms Count) |
| **`Sts_MotorCurr`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang phát hiện có dòng điện chạy qua động cơ (Motor Current Present) |
| **`Sts_GFCurr`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang phát hiện có dòng điện rò chạm đất (Ground Fault Current Present) |
| **`Sts_MotorVolts`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang phát hiện có điện áp cấp vào động cơ (Motor Voltage Present) |
| **`Sts_EStartEna`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đã bật tính năng cho phép khởi động khẩn cấp (Emergency Start Enabled) |
| **`Sts_DevLgxEna`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đã kích hoạt chức năng logic nội bộ DeviceLogix trên rơ-le quá tải |
| **`Sts_FdbkTmoEna`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đã bật chức năng giám sát quá thời gian phản hồi (Feedback Timeout Enabled) |
| **`Sts_OpStaPres`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Rơ-le E300 có gắn trạm nút nhấn vận hành tại chỗ (Operator Station Present) |
| **`Sts_VoltSense`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có trang bị chức năng đo lường điện áp trên rơ-le quá tải |
| **`Sts_IntGndFaultSense`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có trang bị cảm biến dòng rò chạm đất nội bộ (Internal Ground Fault Sensing) |
| **`Sts_ExtGndFaultSense`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có trang bị cảm biến dòng rò chạm đất bên ngoài (External Ground Fault Sensing) |
| **`Sts_PTCSense`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có trang bị cảm biến nhiệt điện trở PTC đo nhiệt độ cuộn dây động cơ |
| **`Sts_InpPt00`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái điểm ngõ vào rời rạc Pt00 trên rơ-le quá tải E300 |
| **`Sts_InpPt01`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái điểm ngõ vào rời rạc Pt01 trên rơ-le quá tải E300 |
| **`Sts_InpPt02`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Trạng thái điểm ngõ vào rời rạc Pt02 trên rơ-le quá tải E300 |
| **`Sts_InpPt03`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Trạng thái điểm ngõ vào rời rạc Pt03 trên rơ-le quá tải E300 |
| **`Sts_InpPt04`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Trạng thái điểm ngõ vào rời rạc Pt04 trên rơ-le quá tải E300 |
| **`Sts_InpPt05`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Trạng thái điểm ngõ vào rời rạc Pt05 trên rơ-le quá tải E300 |
| **`Sts_OutPt00`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trạng thái điểm ngõ ra rời rạc Pt00 trên rơ-le quá tải E300 |
| **`Sts_OutPt01`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Trạng thái điểm ngõ ra rời rạc Pt01 trên rơ-le quá tải E300 |
| **`Sts_OutPt02`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Trạng thái điểm ngõ ra rời rạc Pt02 trên rơ-le quá tải E300 |
| **`Sts_DeviceSts1`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ trạng thái thiết bị số 1 của rơ-le E300 (trạng thái module điều khiển, đo lường và mở rộng) |
| **`Sts_InpSts1`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ trạng thái các ngõ vào số trên module mở rộng của rơ-le quá tải |
| **`Sts_OutSts`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ trạng thái các ngõ ra số trên module mở rộng của rơ-le quá tải |
| **`Sts_OpSta`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ trạng thái trạm vận hành tại chỗ (Local Operator Station Status) của rơ-le E300 |
| **`Sts_CurrentTrip`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ bit trạng thái cắt bảo vệ do sự cố về Dòng điện (Current Trip Status Bits) |
| **`Sts_CurrentWarning`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ bit trạng thái cảnh báo sớm về Dòng điện (Current Warning Status Bits) |
| **`Sts_VoltageTrip`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ bit trạng thái cắt bảo vệ do sự cố về Điện áp (Voltage Trip Status Bits) |
| **`Sts_VoltageWarning`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ bit trạng thái cảnh báo sớm về Điện áp (Voltage Warning Status Bits) |
| **`Sts_PowerTrip`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ bit trạng thái cắt bảo vệ do sự cố về Công suất (Power Trip Status Bits) |
| **`Sts_PowerWarning`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ bit trạng thái cảnh báo sớm về Công suất (Power Warning Status Bits) |
| **`Sts_ControlTrip`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ bit trạng thái cắt bảo vệ do mạch điều khiển (Control Trip Status Bits) |
| **`Sts_ControlWarning`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ bit trạng thái cảnh báo về mạch điều khiển (Control Warning Status Bits) |
| **`Sts_AnalogTrip`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ bit trạng thái cắt bảo vệ từ kênh cảm biến tương tự (Analog Trip Status Bits) |
| **`Sts_AnalogWarning`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ bit trạng thái cảnh báo từ kênh cảm biến tương tự (Analog Warning Status Bits) |
| **`Sts_DevLgxOutputs`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Từ trạng thái các ngõ ra điều khiển bởi DeviceLogix |
| **`Sts_LocalResetReqd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đã vượt quá số lần Reset từ xa cho phép: bắt buộc kỹ thuật viên phải xuống tận tủ MCC kiểm tra và nhấn nút Reset tại chỗ |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị chưa sẵn sàng hoạt động (kiểm tra các bit Nrdy_* chi tiết để biết nguyên nhân cụ thể) |
| **`Nrdy_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng: Thiết bị đang bị cắt bảo vệ quá tải (Tripped tại thiết bị hoặc do lệnh Trip) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi truyền thông thẻ I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OvldNR`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng: Rơ-le bảo vệ quá tải chưa hoàn tất khởi động nguồn (Overload Powerup Not Complete) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có ít nhất một cảnh báo bị Tạm hoãn (Shelved), Vô hiệu hóa (Disabled) hoặc Ức chế (Suppressed) — hiển thị biểu tượng chữ "I" trên HMI |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị đặt bộ định thời gian (Timer Preset) nằm ngoài phạm vi hợp lệ 0..2.147.483 giây |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc chốt ngắt bảo vệ (Shed) đã hết lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình cảnh báo: Thời gian duy trì tối thiểu (Min On Time), thời gian tạm hoãn (Shelf Time) hoặc mức độ nghiêm trọng (Severity 1..1000) không hợp lệ |
| **`Sts_Warn`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) |
| **`Alm_Warn`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) |
| **`Ack_Warn`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_WarnDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_WarnShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_WarnSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_Trip`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện rơ-le quá tải tác động cắt mạch (Overload Trip) |
| **`Alm_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho rơ-le quá tải tác động cắt mạch (Overload Trip) |
| **`Ack_Trip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động rơ-le quá tải tác động cắt mạch (Overload Trip) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_TripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_TripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo rơ-le quá tải tác động cắt mạch (Overload Trip) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_IOFault`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi truyền thông thẻ I/O (I/O Fault) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi truyền thông thẻ I/O (I/O Fault) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`P_E300Ovld`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_E300Ovld) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 31 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_E300Ovld`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_8` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'Motor Overload Relay'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_InpPt00Txt`** | `STRING_20` | `'Starter Aux Contact'` | [Biến cấu hình thông số] Nhãn mô tả cho điểm ngõ vào rời rạc Pt00 của rơ-le E300 hiển thị trên HMI |
| **`Cfg_InpPt01Txt`** | `STRING_20` | `''` | [Biến cấu hình thông số] Nhãn mô tả cho điểm ngõ vào rời rạc Pt01 của rơ-le E300 hiển thị trên HMI |
| **`Cfg_InpPt02Txt`** | `STRING_20` | `''` | [Biến cấu hình thông số] Nhãn mô tả cho điểm ngõ vào rời rạc Pt02 của rơ-le E300 hiển thị trên HMI |
| **`Cfg_InpPt03Txt`** | `STRING_20` | `''` | [Biến cấu hình thông số] Nhãn mô tả cho điểm ngõ vào rời rạc Pt03 của rơ-le E300 hiển thị trên HMI |
| **`Cfg_InpPt04Txt`** | `STRING_20` | `''` | [Biến cấu hình thông số] Nhãn mô tả cho điểm ngõ vào rời rạc Pt04 của rơ-le E300 hiển thị trên HMI |
| **`Cfg_InpPt05Txt`** | `STRING_20` | `''` | [Biến cấu hình thông số] Nhãn mô tả cho điểm ngõ vào rời rạc Pt05 của rơ-le E300 hiển thị trên HMI |
| **`Cfg_Label`** | `STRING_20` | `'Overload Relay'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_OutPt00Txt`** | `STRING_20` | `'Starter Energized'` | [Biến cấu hình thông số] Nhãn mô tả cho điểm ngõ ra rời rạc Pt00 của rơ-le E300 hiển thị trên HMI |
| **`Cfg_OutPt01Txt`** | `STRING_20` | `''` | [Biến cấu hình thông số] Nhãn mô tả cho điểm ngõ ra rời rạc Pt01 của rơ-le E300 hiển thị trên HMI |
| **`Cfg_OutPt02Txt`** | `STRING_20` | `''` | [Biến cấu hình thông số] Nhãn mô tả cho điểm ngõ ra rời rạc Pt02 của rơ-le E300 hiển thị trên HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_E300Ovld'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_E300Ovld'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`IOFault`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo |
| **`OCmd_Trip`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Cắt khẩn cấp (Trip) thiết bị/động cơ |
| **`OCmd_TripReset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Gửi xung xóa lỗi quá tải (Trip Reset) tới rơ-le nhiệt/biến tần |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Trip`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Trip (Cắt khẩn cấp (Trip) thiết bị/động cơ), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_TripReset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_TripReset (Gửi xung xóa lỗi quá tải (Trip Reset) tới rơ-le nhiệt/biến tần), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`Trip`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho rơ-le quá tải tác động cắt mạch (Overload Trip) |
| **`Val_AnalogIn`** | `INT[12]` | `{...}` | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị kênh đầu vào tương tự đọc từ thiết bị (0 đến 65535 đơn vị thô UINT) |
| **`Val_Notify`** | `SINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo hiện hành và trạng thái xác nhận của khối này (0 = Không có cảnh báo, 1 = Đã hết lỗi nhưng chờ Reset/Ack, 2..3 = Thấp, 4..5 = Trung bình, 6..7 = Cao, 8..9 = Khẩn cấp) |
| **`Val_TripDesc`** | `STRING_40` | `'` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi văn bản mô tả nguyên nhân cắt bảo vệ quá tải (tra cứu từ mã Val_TripCode) |
| **`Val_WarningDesc`** | `STRING_40` | `‘Logic not yet scanned.’` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi văn bản mô tả nguyên nhân cảnh báo quá tải (tra cứu từ mã Val_WarningCode) |
| **`Warn`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho cảnh báo sớm sắp quá tải / sắp cắt (Overload Warning) |
---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_ResInh`
**Thuộc chương:** [Chương 3: Động cơ, Biến tần & Rơ-le Quá tải (Motors & Drives)](../03_CHUONG_3_MOTORS_VA_BIEN_TAN.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Motors 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 273–278)  
**Tệp định nghĩa khối lệnh:** `P_ResInh_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_ResInh`:** **13** Biến đầu vào (`Input Parameters`) | **8** Biến đầu ra (`Output Parameters`) | **7** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_ResInh` trong Studio 5000
- **Chức năng cốt lõi:** Khối **Khóa cấm khởi động lại (Restart Inhibit)** bảo vệ nhiệt cho động cơ công suất lớn. Tính toán mô hình tích lũy nhiệt mỗi lần khởi động: giới hạn số lần khởi động liên tiếp khi nguội/nóng, ép thời gian nghỉ tối thiểu giữa 2 lần chạy và xuất đồng hồ đếm ngược thời gian chờ cho phép chạy lại (`Out_OKToStart`).
- **Khi nào CẦN sử dụng:** Gắn kèm với **các động cơ trung thế hoặc hạ thế công suất lớn (> 75 kW)** có quán tính tải nặng (quạt lớn, máy nghiền, bơm lớn) để ngăn người vận hành nhấn Start liên tục làm cháy cuộn dây stator.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Không cần dùng cho các động cơ công suất nhỏ khởi động nhẹ nhàng.

### Quy trình 4 bước đấu nối và cấu hình `P_ResInh`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_Stopped`, `Inp_Starting`, `Inp_Running`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_ThreeColdStarts`, `Cfg_FirstFailCold`, `Cfg_SubseqFailCold`, `Cfg_FirstFailHot`, `Cfg_SubseqFailHot`, `Cfg_HasMoreObj`, `Cfg_HotRestartOK`, `Cfg_RestartHot`, `Cfg_HotToCold`
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_Desc`, `Cfg_Label`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_ResInh` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_ResInh` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Khối `P_ResInh` thực hiện tính toán/xử lý trực tiếp theo các chân đầu vào `Inp_*` và thông số cấu hình `Cfg_*` trong mỗi chu kỳ quét mà không yêu cầu kích lệnh điều khiển `PCmd_*` từ chương trình.
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Val_MinToReady`, `Val_SecToReady`, `Val_Fdbk`, `Val_State`, `Sts_Ready`, `Sts_Err`
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 13 Biến Đầu Vào (`Input Parameters`) của `P_ResInh`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp_Stopped`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Bit trạng thái xác nhận thiết bị đã dừng hoàn toàn (1 = Confirmed Stopped) |
| **`Inp_Starting`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Bit trạng thái báo thiết bị đang trong quá trình khởi động (1 = Starting) |
| **`Inp_Running`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Bit trạng thái xác nhận biến tần/thiết bị đang chạy (1 = Confirmed Running) |
| **`Cfg_ThreeColdStarts`** | `REAL` | `0.5` | Ẩn (Tag) | [Biến cấu hình thông số] Khung thời gian (giờ) mà trong đó động cơ đang nguội được phép khởi động tối đa 3 lần liên tiếp |
| **`Cfg_FirstFailCold`** | `REAL` | `0.5` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian động cơ đang nguội phải chờ sau lần khởi động thất bại đầu tiên trước khi cho phép đề lại (giờ) |
| **`Cfg_SubseqFailCold`** | `REAL` | `4` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian động cơ đang nguội phải chờ sau lần khởi động thất bại thứ 2 trở đi (giờ) |
| **`Cfg_FirstFailHot`** | `REAL` | `0.5` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian động cơ đang nóng phải chờ sau lần khởi động thất bại đầu tiên trước khi cho phép đề lại (giờ) |
| **`Cfg_SubseqFailHot`** | `REAL` | `4` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian động cơ đang nóng phải chờ sau lần khởi động thất bại thứ 2 trở đi (giờ) |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_HotRestartOK`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian động cơ đã chạy liên tục đủ nóng ổn định để cho phép khởi động lại ngay sau khi dừng (giờ) |
| **`Cfg_RestartHot`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian chờ bắt buộc để khởi động lại động cơ đang nóng nếu dừng trước khi đạt thời gian Cfg_HotRestartOK (giờ) |
| **`Cfg_HotToCold`** | `REAL` | `16` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian động cơ đang dừng cần nghỉ để nguội hoàn toàn về trạng thái Nguội (Cold Motor, tính bằng giờ) |

## 3. Bảng chi tiết 8 Biến Đầu Ra (`Output Parameters`) của `P_ResInh`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Val_MinToReady`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Số phút còn lại phải chờ trước khi động cơ được phép khởi động lại (hiển thị mmm:ss trên HMI) |
| **`Val_SecToReady`** | `SINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Số giây lẻ còn lại phải chờ trước khi động cơ được phép khởi động lại (hiển thị mmm:ss trên HMI) |
| **`Val_Fdbk`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái phản hồi thực tế từ thiết bị hiện trường (Device Feedback enum: 0 = Đang dừng/Đang chuyển trạng thái, 1 = Đang chạy/Đóng/Off, 2 = Mở/On, 3 = Phản hồi không hợp lệ) |
| **`Val_State`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái máy trạng thái nội bộ (Internal Logic State) dùng để tạo hiệu ứng chuyển động sơ đồ trạng thái trên HMI Faceplate |
| **`Sts_Ready`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị đã sẵn sàng cho phép khởi động (0 = Chưa sẵn sàng / Đang bị khóa cấm khởi động lại) |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`P_ResInh`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_ResInh) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 7 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_ResInh`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_8` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'Restart Inhibit'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_Label`** | `STRING_20` | `'Restart Inhibit'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_Tag`** | `STRING_20` | `'P_ResInh'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_ResInh'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_RunTime`
**Thuộc chương:** [Chương 3: Động cơ, Biến tần & Rơ-le Quá tải (Motors & Drives)](../03_CHUONG_3_MOTORS_VA_BIEN_TAN.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Motors 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 269–272)  
**Tệp định nghĩa khối lệnh:** `P_RunTime_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_RunTime`:** **10** Biến đầu vào (`Input Parameters`) | **6** Biến đầu ra (`Output Parameters`) | **7** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_RunTime` trong Studio 5000
- **Chức năng cốt lõi:** Khối **Đếm Tổng số giờ chạy (`Val_RunHrs`) và Tổng số lần khởi động (`Val_Starts`)** của động cơ/thiết bị. Phát cảnh báo nhắc bảo trì khi số giờ chạy vượt mốc định kỳ và cấp dữ liệu giờ chạy cho khối `P_LLS` để cân bằng độ mòn giữa các bơm.
- **Khi nào CẦN sử dụng:** Gắn kèm với **mọi khối Động cơ (`P_Motor*`) và Biến tần (`P_PF*`, `P_VSD`)** để theo dõi lịch thay dầu/bạc đạn và xoay vòng bơm đều nhau. *(Mẹo: đặt tên thẻ là `[Tên_Motor]_RunTime` và bật `Cfg_HasRunTimeObj = 1` trên khối Motor)*.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi không có nhu cầu thống kê giờ chạy bảo trì.

### Quy trình 4 bước đấu nối và cấu hình `P_RunTime`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp_Starting`, `Inp_Running`
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_HasMoreObj`
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_Desc`, `Cfg_Label`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_RunTime` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_RunTime` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PCmd_ClearStarts`, `PCmd_ClearMaxHrs`, `PCmd_ClearTotHrs`
   - **Cách sử dụng trong code PLC:** Gán giá trị đặt vào `PSet_*` (nếu có) hoặc kích xung 1 chu kỳ vào các bit lệnh `PCmd_* = 1` để xóa lỗi (`PCmd_Reset`), xác nhận cảnh báo (`PCmd_*Ack`) hoặc ức chế cảnh báo (`PCmd_*Suppress`). Khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi.
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Val_Starts`, `Val_CurRunHrs`, `Val_MaxRunHrs`, `Val_TotRunHrs`
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 10 Biến Đầu Vào (`Input Parameters`) của `P_RunTime`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Inp_Starting`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Bit trạng thái báo thiết bị đang trong quá trình khởi động (1 = Starting) |
| **`Inp_Running`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Bit trạng thái xác nhận biến tần/thiết bị đang chạy (1 = Confirmed Running) |
| **`PCmd_ClearStarts`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa bộ đếm tổng số lần khởi động của thiết bị về 0 |
| **`PCmd_ClearMaxHrs`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa thống kê số giờ chạy liên tục dài nhất của một lần khởi động về 0 |
| **`PCmd_ClearTotHrs`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa tổng số giờ vận hành tích lũy của thiết bị về 0 |
| **`MCmd_ClearStarts`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Xóa bộ đếm tổng số lần khởi động của thiết bị về 0 |
| **`MCmd_ClearMaxHrs`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Xóa thống kê số giờ chạy liên tục dài nhất của một lần khởi động về 0 |
| **`MCmd_ClearTotHrs`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Xóa tổng số giờ vận hành tích lũy của thiết bị về 0 |

## 3. Bảng chi tiết 6 Biến Đầu Ra (`Output Parameters`) của `P_RunTime`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Val_Starts`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lần khởi động của động cơ (Total Motor Starts Count) |
| **`Val_CurRunHrs`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Số giờ chạy liên tục của lần khởi động hiện tại (Current Run Hours) |
| **`Val_MaxRunHrs`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Số giờ chạy liên tục dài nhất trong một lần khởi động từng ghi nhận được (Maximum Continuous Run Hours) |
| **`Val_TotRunHrs`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số giờ vận hành tích lũy của động cơ từ lúc đưa vào sử dụng hoặc từ lần xóa gần nhất (Total Accumulated Run Hours) |
| **`P_RunTime`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_RunTime) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 7 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_RunTime`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_8` | `'HMI'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'Accumulated Run Time (Hours)'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_Label`** | `STRING_20` | `'Run Time'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_Tag`** | `STRING_20` | `'P_RunTime'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_RunTime'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_SMC50`
**Thuộc chương:** [Chương 3: Động cơ, Biến tần & Rơ-le Quá tải (Motors & Drives)](../03_CHUONG_3_MOTORS_VA_BIEN_TAN.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Motors 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 224–229)  
**Tệp định nghĩa khối lệnh:** `P_SMC50_4.10.00_RUNG.L5X`  
**Thống kê số lượng biến của `P_SMC50`:** **93** Biến đầu vào (`Input Parameters`) | **106** Biến đầu ra (`Output Parameters`) | **30** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_SMC50` trong Studio 5000
- **Chức năng cốt lõi:** Khối điều khiển bộ khởi động mềm thông minh **Allen-Bradley SMC-50 Smart Motor Controller** qua mạng truyền thông. Hỗ trợ Start, Stop, Jog (chạy chậm), đọc dòng 3 pha, công suất, % nhiệt tích lũy và giải mã lỗi `SMC50_FaultCodeList`.
- **Khi nào CẦN sử dụng:** Khi khởi động động cơ bằng bộ khởi động mềm **SMC-50** kết nối mạng EtherNet/IP.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi khởi động mềm chỉ đấu dây cứng Digital I/O đơn giản (có thể dùng [`P_Motor.md`](./P_Motor.md)) hoặc dùng dòng SMC Flex ([`P_SMCFlex.md`](./P_SMCFlex.md)).

### Quy trình 4 bước đấu nối và cấu hình `P_SMC50`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp`, `Out`, `Ref_FaultCodeList`, `Inp_PermOK`, `Inp_NBPermOK`, `Inp_IntlkOK`, `Inp_NBIntlkOK`, `Inp_IOFault`, `Inp_Sim`, `Inp_Hand`, `Inp_Ovrd`, `Inp_OvrdCmd`, `Inp_ExtInh`, `Inp_HornInh`, `Inp_Reset`
   - **Các chân tham chiếu dữ liệu (`Ref_*` / `InOut`):** `Ref_FaultCodeList`, `Ref_FaultCodeList` — bắt buộc gán thẻ mảng/cấu trúc tương ứng trong Controller Tags.
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_HasJog`, `Cfg_AllowLocal`, `Cfg_HasPermObj`, `Cfg_HasIntlkObj`, `Cfg_HasResInhObj`, `Cfg_HasRunTimeObj`, `Cfg_OperStopPrio`, `Cfg_ExtStopPrio`, `Cfg_OCmdResets`, `Cfg_XCmdResets`, `Cfg_OvrdPermIntlk`, `Cfg_ShedOnFailToStart`, `Cfg_ShedOnIOFault`, `Cfg_HasFailToStartAlm`, `Cfg_HasFailToStopAlm`, `Cfg_HasIntlkTripAlm`, `Cfg_HasMotorFaultAlm`, `Cfg_HasIOFaultAlm`...
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_Desc`, `Cfg_Label`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_SMC50` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_SMC50` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PSet_Owner`, `PCmd_Start`, `PCmd_Stop`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_FailToStartAck`, `PCmd_FailToStartSuppress`, `PCmd_FailToStartUnsuppress`, `PCmd_FailToStartUnshelve`, `PCmd_FailToStopAck`, `PCmd_FailToStopSuppress`, `PCmd_FailToStopUnsuppress`, `PCmd_FailToStopUnshelve`, `PCmd_IntlkTripAck`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh điều khiển tự động, kiểm tra khối đã sẵn sàng ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển quyền về Program, sau đó gán giá trị vào `PSet_*` và kích xung `PCmd_* = 1` (khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Out_Horn`, `Val_AvgPctFLA`, `Val_PctTherm`, `Val_CurAvg`, `Val_RealPwr`, `Val_PwrFact`, `Val_MinToTrip`, `Val_SecToTrip`, `Val_MinToReset`, `Val_SecToReset`, `Val_LastFaultCode`, `Val_Cmd`, `Val_Fdbk`, `Val_Sts`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Val_Owner`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 93 Biến Đầu Vào (`Input Parameters`) của `P_SMC50`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp`** | `P_SMC50_Inp` | `-` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu điều kiện đầu vào (1 = Kích hoạt điều kiện / Báo động, 0 = Bình thường) |
| **`Out`** | `P_SMC50_Out` | `-` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra điều khiển thiết bị (hoặc kết quả cổng điều kiện sau thời gian trễ On-Delay / Off-Delay) |
| **`Ref_FaultCodeList`** | `P_DescList[1]` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới mảng bảng tra cứu chứa danh sách Mã lỗi (DINT) và Chuỗi mô tả lỗi (STRING) của biến tần/khởi động mềm |
| **`Inp_PermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp điều kiện Cho phép khởi động có thể Bypass (1 = Tất cả điều kiện Perm OK, cho phép khởi động; thường nối từ P_Perm.Sts_PermOK) |
| **`Inp_NBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp điều kiện Cho phép khởi động KHÔNG thể Bypass (1 = Tất cả điều kiện Non-Bypassable Perm OK; thường nối từ P_Perm.Sts_NBPermOK) |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp Khóa liên động an toàn có thể Bypass (1 = Liên động OK cho phép chạy; 0 = Cắt dừng thiết bị ngay lập tức; nối từ P_Intlk.Sts_IntlkOK) |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp Khóa liên động an toàn KHÔNG thể Bypass (1 = Liên động bất khả kháng OK; 0 = Cắt dừng thiết bị ngay lập tức; nối từ P_Intlk.Sts_NBIntlkOK) |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi truyền thông I/O của thiết bị (0 = Tốt/OK, 1 = Lỗi truyền thông I/O Fault) |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân kích hoạt chế độ Mô phỏng (Simulation): Khi = 1, khóa các ngõ ra vật lý ở mức an toàn và dùng giá trị/phản hồi giả lập nội bộ; khi = 0, vận hành thực tế bình thường |
| **`Inp_Hand`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu chọn chế độ Điều khiển bằng tay tại chỗ (Hand/Local): 1 = Chiếm quyền sang chế độ Hand (thường nối từ công tắc cứng tại tủ MCC); 0 = Nhả chế độ Hand |
| **`Inp_Ovrd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu kích hoạt chế độ Cưỡng bức ưu tiên (Override): 1 = Chiếm quyền sang chế độ Override; 0 = Nhả chế độ Override |
| **`Inp_OvrdCmd`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã lệnh điều khiển thiết bị khi ở chế độ Cưỡng bức (Override Command — kiểu số nguyên phụ thuộc vào từng loại thiết bị: 0 = Không tác động, 1 = Dừng/Đóng/Tắt, 2 = Chạy/Mở/Bật...) |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân khóa cấm nguồn lệnh ngoài (External Inhibit): 1 = Cấm chế độ External chiếm quyền điều khiển; 0 = Cho phép External chiếm quyền |
| **`Inp_HornInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân khóa cấm còi báo động tại hiện trường: 1 = Cấm phát còi báo (Inhibit Horn); 0 = Cho phép phát còi báo trước khi khởi động |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Cfg_HasJog`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép và hiển thị nút nhấn Nhấp thử (Jog) trên màn hình HMI; 0 = Khóa cấm lệnh Jog |
| **`Cfg_AllowLocal`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép khởi động/dừng động cơ tại chỗ ở tủ MCC mà không phát báo động lỗi; 0 = Chỉ cho phép điều khiển từ HMI hoặc Program |
| **`Cfg_HasPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Điều kiện cho phép (P_Perm) nối vào chân Inp_PermOK và bật nút điều hướng mở Faceplate P_Perm |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Khóa liên động (P_Intlk) nối vào chân Inp_IntlkOK và bật nút điều hướng mở Faceplate P_Intlk |
| **`Cfg_HasResInhObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Cấm khởi động lại (P_ResInh) đi kèm và bật nút điều hướng mở Faceplate P_ResInh |
| **`Cfg_HasRunTimeObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Đếm giờ chạy (P_RunTime) đi kèm và bật nút điều hướng mở Faceplate P_RunTime |
| **`Cfg_OperStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Nút nhấn Dừng của Người vận hành (OCmd_Stop) có quyền ưu tiên cao nhất, bấm dừng được ngay cả khi máy đang chạy ở chế độ Program; 0 = OCmd_Stop chỉ có tác dụng khi ở chế độ Operator/Maintenance |
| **`Cfg_ExtStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Lệnh Dừng từ hệ thống ngoài (XCmd_Stop) có hiệu lực ưu tiên ở mọi chế độ; 0 = Chỉ có hiệu lực khi ở chế độ External |
| **`Cfg_OCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khi Người vận hành ra lệnh Chạy/Mở mới (OCmd_*), khối tự động xóa luôn các chốt lỗi cũ; 0 = Bắt buộc phải nhấn nút Reset trước khi ra lệnh mới |
| **`Cfg_XCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khi hệ thống ngoài gửi lệnh Chạy/Mở mới (XCmd_*), khối tự động xóa luôn các chốt lỗi cũ; 0 = Bắt buộc phải gửi xung XCmd_Reset trước |
| **`Cfg_OvrdPermIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Chế độ Cưỡng bức (Override) được quyền bỏ qua các điều kiện Cho phép và Liên động có thể bypass; 0 = Chế độ Override vẫn phải tuân thủ mọi điều kiện |
| **`Cfg_ShedOnFailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt lệnh chạy (Shed) và phát báo động khi động cơ bị lỗi Không khởi động được (Fail to Start); 0 = Chỉ phát báo động nhưng vẫn giữ ngõ ra |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt dừng thiết bị về trạng thái an toàn (Shed) và phát báo động khi lỗi truyền thông thẻ I/O; 0 = Chỉ phát báo động |
| **`Cfg_HasFailToStartAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi không khởi động được (Fail to Start): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasFailToStopAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi không dừng được (Fail to Stop): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasMotorFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi động cơ (Motor Fault): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_FailToStartResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi không khởi động được (Fail to Start): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_FailToStopResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi không dừng được (Fail to Stop): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_MotorFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi động cơ (Motor Fault): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_FailToStartAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi không khởi động được (Fail to Start): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_FailToStopAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi không dừng được (Fail to Stop): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_MotorFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi động cơ (Motor Fault): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_FailToStartSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi không khởi động được (Fail to Start) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_FailToStopSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi không dừng được (Fail to Stop) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_MotorFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi động cơ (Motor Fault) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_StartHornT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian hú còi cảnh báo tại hiện trường (Out_Horn) trước khi đóng điện khởi động thiết bị (giây; đặt = 0.0 để tắt còi) |
| **`Cfg_SimFdbkT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ giả lập tín hiệu phản hồi chạy/đóng/mở trong chế độ Mô phỏng (Inp_Sim = 1, tính bằng giây) |
| **`Cfg_FailToStartT`** | `REAL` | `15` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian chờ tối đa (giây) từ lúc xuất lệnh Chạy đến khi phải nhận được phản hồi chạy (Inp_RunFdbk = 1) trước khi báo lỗi FailToStart |
| **`Cfg_FailToStopT`** | `REAL` | `15` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian chờ tối đa (giây) từ lúc xuất lệnh Dừng đến khi phản hồi chạy phải về 0 trước khi báo lỗi FailToStop |
| **`Cfg_ResetPulseT`** | `REAL` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Độ rộng xung xuất ra chân xóa lỗi biến tần/khởi động mềm (giây) |
| **`Cfg_MaxJogT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian tối đa cho phép giữ lệnh chạy nhấp thử Jog liên tục (giây; đặt = 0.0 là không giới hạn) |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Mã định danh Chủ sở hữu (Owner ID: ghi khác 0 để đăng ký chiếm quyền điều khiển, ghi 0 để nhả quyền) |
| **`PCmd_Start`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khởi động (Start) thiết bị/động cơ |
| **`PCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Dừng (Stop) thiết bị/động cơ |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Chương trình tự động (từ Operator sang Program) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Người vận hành (từ Program sang Operator) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa giữ nguồn lệnh hiện tại (không cho phép nguồn lệnh kia giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Đưa nguồn lệnh về chế độ Bình thường mặc định (Normal: Program hoặc Operator theo Cfg_ProgNormal) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`PCmd_FailToStartAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi không khởi động được (Fail to Start) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_FailToStartSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi không khởi động được (Fail to Start) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStartUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi không khởi động được (Fail to Start) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStartUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi không khởi động được (Fail to Start) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_FailToStopAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi không dừng được (Fail to Stop) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_FailToStopSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi không dừng được (Fail to Stop) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStopUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi không dừng được (Fail to Stop) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStopUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi không dừng được (Fail to Stop) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_MotorFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi động cơ (Motor Fault) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_MotorFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi động cơ (Motor Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_MotorFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi động cơ (Motor Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_MotorFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi động cơ (Motor Fault) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) ngay lập tức (biến xung tự động xóa về 0) |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`XCmd_Start`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Khởi động (Start) thiết bị/động cơ |
| **`XCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Dừng (Stop) thiết bị/động cơ |
| **`XCmd_Jog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhấp thử (Jog) thiết bị/động cơ khi giữ nút |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Chiếm quyền điều khiển (Acquire) về chế độ tương ứng |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |

## 3. Bảng chi tiết 106 Biến Đầu Ra (`Output Parameters`) của `P_SMC50`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Inp`** | `P_SMC50_Inp` | `-` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu điều kiện đầu vào (1 = Kích hoạt điều kiện / Báo động, 0 = Bình thường) |
| **`Out`** | `P_SMC50_Out` | `-` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra điều khiển thiết bị (hoặc kết quả cổng điều kiện sau thời gian trễ On-Delay / Off-Delay) |
| **`Ref_FaultCodeList`** | `P_DescList[1]` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới mảng bảng tra cứu chứa danh sách Mã lỗi (DINT) và Chuỗi mô tả lỗi (STRING) của biến tần/khởi động mềm |
| **`Out_Horn`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra bật còi cảnh báo âm thanh tại hiện trường trước khi khởi động hoặc chuyển trạng thái thiết bị (1 = Bật còi) |
| **`Val_AvgPctFLA`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Dòng điện trung bình 3 pha tính theo phần trăm dòng định mức (% Full Load Amps) |
| **`Val_PctTherm`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Phần trăm dung lượng nhiệt đã sử dụng của động cơ (% Thermal Usage — đạt 100% sẽ cắt bảo vệ quá tải) |
| **`Val_CurAvg`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Dòng điện ngõ ra trung bình 3 pha của động cơ (3-Phase Average Current, đơn vị Ampe) |
| **`Val_RealPwr`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Công suất tác dụng tiêu thụ của động cơ (Real Power = U × I × cosφ, đơn vị MW) |
| **`Val_PwrFact`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Hệ số công suất cosφ của động cơ (% Power Factor) |
| **`Val_MinToTrip`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Số phút ước tính còn lại trước khi rơ-le quá tải tác động cắt mạch (Time to Trip, định dạng mm:ss) |
| **`Val_SecToTrip`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Số giây lẻ ước tính còn lại trước khi rơ-le quá tải tác động cắt mạch (Time to Trip, định dạng mm:ss) |
| **`Val_MinToReset`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Số phút còn lại phải chờ nguội trước khi rơ-le quá tải cho phép nhấn Reset (trong định dạng mmm:ss) |
| **`Val_SecToReset`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Số giây lẻ còn lại phải chờ nguội trước khi rơ-le quá tải cho phép nhấn Reset (trong định dạng mmm:ss) |
| **`Val_LastFaultCode`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã lỗi gần nhất của biến tần (Last Drive Fault Code) |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng dữ liệu tổng hợp cuối cùng (0 = Tín hiệu thực Tốt đã xác nhận, 1 = Tín hiệu thực Tốt giả định, 2 = Không cấu hình phản hồi/Tốt giả định, 8 = Đang mô phỏng, 9 = Mô phỏng vòng lặp, 10 = Giá trị nhập tay, 16 = Ngoài đặc tính kỹ thuật, 17 = Thay thế tại thiết bị/bus, 18 = Thay thế bởi Bảo trì, 19 = Ngắt bảo vệ giữ giá trị cũ, 20 = Ngắt bảo vệ dùng giá trị thay thế, 32 = Lỗi tín hiệu vượt dải/NaN, 33 = Lỗi kênh I/O, 34 = Lỗi module I/O, 35 = Lỗi cấu hình) |
| **`Val_Cmd`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã lệnh điều khiển thiết bị đang xuất ra (Device Command enum: 0 = Không tác động/Tắt, 1 = Dừng/Đóng/Off, 2 = Chạy/Mở/On...) |
| **`Val_Fdbk`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái phản hồi thực tế từ thiết bị hiện trường (Device Feedback enum: 0 = Đang dừng/Đang chuyển trạng thái, 1 = Đang chạy/Đóng/Off, 2 = Mở/On, 3 = Phản hồi không hợp lệ) |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái hoạt động xác nhận của thiết bị (Device Confirmed Status enum dùng để điều khiển màu sắc và biểu tượng động trên HMI) |
| **`Val_Fault`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái lỗi thiết bị (Device Fault Status — kiểu liệt kê enum: 0 = Không lỗi, 16 = Lỗi khởi động/mở, 17 = Lỗi dừng/đóng, 20 = Lo, 21 = Hi, 24 = LoLo, 25 = HiHi, 32 = Fail/I/O Fault, 34 = Lỗi cấu hình CfgErr) |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận tổng hợp của khối này cộng với các đối tượng kênh/quá tải liên kết |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms Count) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã định danh Chủ sở hữu hiện hành của đối tượng (Current Object Owner ID: 0 = Đang không bị chiếm quyền) |
| **`Sts_Stopped`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần được yêu cầu dừng và đã xác nhận Dừng hoàn toàn (Confirmed Stopped) |
| **`Sts_Starting`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần được yêu cầu chạy và đang chờ tín hiệu phản hồi chạy (Starting) |
| **`Sts_Running`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần được yêu cầu chạy và đã xác nhận đang Chạy (Confirmed Running) |
| **`Sts_Stopping`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần được yêu cầu dừng và đang chờ tín hiệu phản hồi về dừng (Stopping) |
| **`Sts_Jogging`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần đang chạy nhấp thử (Jogging) |
| **`Sts_NotReady`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị chưa sẵn sàng hoạt động (kiểm tra các bit Nrdy_* chi tiết để biết nguyên nhân cụ thể) |
| **`Sts_Alarm`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Biến tần đang có cảnh báo (kiểm tra màn hình biến tần hoặc mã Val_LastAlarmCode) |
| **`Sts_AtSpeed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Biến tần đang chạy đúng tại tốc độ đặt tham chiếu (At Reference Speed) |
| **`Sts_CtrlPwrOK`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Nguồn điện điều khiển (Control Power) tốt (OK); 0 = Mất nguồn điều khiển |
| **`Sts_Phasing`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Thứ tự pha nguồn lưới: 1 = Thứ tự pha thuận ABC; 0 = Thứ tự pha ngược CBA |
| **`Sts_PhasingActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Phát hiện nguồn lưới 3 pha hợp lệ; 0 = Không phát hiện nguồn 3 pha hợp lệ |
| **`Sts_StartIsol`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Contactor khởi động / cách ly (Start/Isolation Contactor) đã đóng; 0 = Đang mở |
| **`Sts_ExtBypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Contactor mạch vòng ngoài (External Bypass Contactor) đang đóng; 0 = Đang mở |
| **`Sts_Opt1Inp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Ngõ vào tùy chọn số 1 (Option 1 Input) đang kích hoạt; 0 = Không kích hoạt |
| **`Sts_Opt2Inp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Ngõ vào tùy chọn số 2 (Option 2 Input) đang kích hoạt; 0 = Không kích hoạt |
| **`Sts_Available`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị sẵn sàng cho Chương trình tự động (Program) điều khiển (đang ở chế độ Program và không có lỗi/liên động ngắt) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass hiện đang được bỏ qua (Bypassed) |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Chế độ Bypass bảo trì đang kích hoạt (gửi sang khối P_Intlk / P_Perm để cho phép bỏ qua các điều kiện có quyền Bypass) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị chưa sẵn sàng hoạt động (kiểm tra các bit Nrdy_* chi tiết để biết nguyên nhân cụ thể) |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_DriveNR`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Biến tần báo trạng thái chưa sẵn sàng (Drive Not Ready) |
| **`Nrdy_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang có lỗi thiết bị ngắt bảo vệ (Shed — yêu cầu nhấn Reset) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Khóa liên động an toàn không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi truyền thông thẻ I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Nrdy_Perm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Cho phép khởi động không thỏa mãn (Permissive Not OK) |
| **`Nrdy_PrioStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng: Đang dính lệnh Dừng ưu tiên từ Operator/External (cần nhấn Reset trước khi chạy lại) |
| **`Nrdy_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng: Thiết bị đang bị cắt bảo vệ quá tải (Tripped tại thiết bị hoặc do lệnh Trip) |
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
| **`Sts_Oper`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Người vận hành trên HMI (Operator) |
| **`Sts_OperLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Người vận hành và đã Khóa (Operator Locked — không cho Program lấy quyền) |
| **`Sts_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Nguồn lệnh hiện hành đang khớp với nguồn lệnh Bình thường mặc định (Program hoặc Operator theo Cfg_ProgNormal) |
| **`Sts_ExtReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Yêu cầu chuyển sang chế độ External đang bị khóa cấm (không thể chuyển sang External từ trạng thái hiện tại) |
| **`Sts_ProgReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Yêu cầu chuyển sang chế độ Program đang bị khóa cấm (không thể chuyển sang Program từ trạng thái hiện tại) |
| **`Sts_MAcqRcvd`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đã nhận được lệnh Chiếm quyền bảo trì (MCmd_Acq) trong chu kỳ quét hiện tại |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc chốt ngắt bảo vệ (Shed) đã hết lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi không khởi động được (Fail to Start) |
| **`Alm_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi không khởi động được (Fail to Start) |
| **`Ack_FailToStart`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi không khởi động được (Fail to Start) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_FailToStartDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không khởi động được (Fail to Start) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_FailToStartShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không khởi động được (Fail to Start) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_FailToStartSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không khởi động được (Fail to Start) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi không dừng được (Fail to Stop) |
| **`Alm_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi không dừng được (Fail to Stop) |
| **`Ack_FailToStop`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi không dừng được (Fail to Stop) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_FailToStopDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không dừng được (Fail to Stop) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_FailToStopShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không dừng được (Fail to Stop) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_FailToStopSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không dừng được (Fail to Stop) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`Alm_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`Ack_IntlkTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_IntlkTripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_IntlkTripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_IntlkTripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_MotorFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi động cơ (Motor Fault) |
| **`Alm_MotorFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi động cơ (Motor Fault) |
| **`Ack_MotorFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi động cơ (Motor Fault) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_MotorFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi động cơ (Motor Fault) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_MotorFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi động cơ (Motor Fault) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_MotorFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi động cơ (Motor Fault) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi truyền thông thẻ I/O (I/O Fault) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi truyền thông thẻ I/O (I/O Fault) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`MRdy_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`P_SMC50`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_SMC50) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 30 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_SMC50`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'SMC-50 Smart Motor Controller'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_Label`** | `STRING_20` | `'Motor Control'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_Tag`** | `STRING_20` | `'P_SMC50'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc nội bộ quản lý phân quyền và chuyển mạch giữa 7 nguồn lệnh điều khiển (OoS, Hand, Maint, Ovrd, Ext, Prog, Oper) |
| **`FailToStart`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi không khởi động được (Fail to Start) |
| **`FailToStop`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi không dừng được (Fail to Stop) |
| **`HMI_HasMore`** | `BOOL` | `0` | Cờ nội bộ báo cho HMI hiển thị nút mở rộng thông tin |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_SMC50'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`IOFault`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bỏ qua (Bypass) tất cả các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Kiểm tra lại bình thường (Check / Gỡ bỏ Bypass) toàn bộ điều kiện Khóa liên động và Cho phép |
| **`MotorFault`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi động cơ (Motor Fault) |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (Bỏ qua (Bypass) tất cả các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (Kiểm tra lại bình thường (Check / Gỡ bỏ Bypass) toàn bộ điều kiện Khóa liên động và Cho phép), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`OCmd_Jog`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Nhấp thử (Jog) thiết bị/động cơ khi giữ nút |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo |
| **`OCmd_Start`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Khởi động (Start) thiết bị/động cơ |
| **`OCmd_Stop`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Dừng (Stop) thiết bị/động cơ |
| **`ORdy_Jog`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Jog (Nhấp thử (Jog) thiết bị/động cơ khi giữ nút), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Start`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Start (Khởi động (Start) thiết bị/động cơ), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Stop`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Stop (Dừng (Stop) thiết bị/động cơ), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`Val_FaultDesc`** | `STRING_40` | `‘Logic not yet scanned.’` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi văn bản mô tả chi tiết nguyên nhân lỗi biến tần (tra cứu tự động từ mã lỗi Val_LastFaultCode) |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo hiện hành và trạng thái xác nhận của khối này (0 = Không có cảnh báo, 1 = Đã hết lỗi nhưng chờ Reset/Ack, 2..3 = Thấp, 4..5 = Trung bình, 6..7 = Cao, 8..9 = Khẩn cấp) |
---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_SMCFlex`
**Thuộc chương:** [Chương 3: Động cơ, Biến tần & Rơ-le Quá tải (Motors & Drives)](../03_CHUONG_3_MOTORS_VA_BIEN_TAN.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Motors 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 230–235)  
**Tệp định nghĩa khối lệnh:** `P_SMCFlex_4.10.00_RUNG.L5X`  
**Thống kê số lượng biến của `P_SMCFlex`:** **95** Biến đầu vào (`Input Parameters`) | **104** Biến đầu ra (`Output Parameters`) | **30** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_SMCFlex` trong Studio 5000
- **Chức năng cốt lõi:** Khối điều khiển bộ khởi động mềm thông minh **Allen-Bradley SMC Flex Smart Motor Controller** qua mạng truyền thông, đọc đầy đủ thông số điện năng, dung lượng nhiệt và mã lỗi của dòng SMC Flex.
- **Khi nào CẦN sử dụng:** Khi sử dụng bộ khởi động mềm **SMC Flex** kết nối mạng với PLC Logix.
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi dùng bộ khởi động mềm SMC-50 ([`P_SMC50.md`](./P_SMC50.md)).

### Quy trình 4 bước đấu nối và cấu hình `P_SMCFlex`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Inp`, `Out`, `Ref_FaultCodeList`, `Inp_PermOK`, `Inp_NBPermOK`, `Inp_IntlkOK`, `Inp_NBIntlkOK`, `Inp_IntlkAvail`, `Inp_IOFault`, `Inp_Sim`, `Inp_Hand`, `Inp_Ovrd`, `Inp_OvrdCmd`, `Inp_ExtInh`, `Inp_HornInh`
   - **Các chân tham chiếu dữ liệu (`Ref_*` / `InOut`):** `Ref_FaultCodeList`, `Ref_FaultCodeList` — bắt buộc gán thẻ mảng/cấu trúc tương ứng trong Controller Tags.
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_PwrUnits`, `Cfg_HasJog`, `Cfg_AllowLocal`, `Cfg_HasPermObj`, `Cfg_HasIntlkObj`, `Cfg_HasResInhObj`, `Cfg_HasRunTimeObj`, `Cfg_OperStopPrio`, `Cfg_ExtStopPrio`, `Cfg_OCmdResets`, `Cfg_XCmdResets`, `Cfg_OvrdPermIntlk`, `Cfg_ShedOnFailToStart`, `Cfg_HasMoreObj`, `Cfg_ShedOnIOFault`, `Cfg_HasFailToStartAlm`, `Cfg_HasFailToStopAlm`, `Cfg_HasIntlkTripAlm`...
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_Desc`, `Cfg_Label`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_SMCFlex` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_SMCFlex` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PSet_Owner`, `PCmd_Start`, `PCmd_Stop`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_FailToStartAck`, `PCmd_FailToStartSuppress`, `PCmd_FailToStartUnsuppress`, `PCmd_FailToStartUnshelve`, `PCmd_FailToStopAck`, `PCmd_FailToStopSuppress`, `PCmd_FailToStopUnsuppress`, `PCmd_FailToStopUnshelve`, `PCmd_IntlkTripAck`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh điều khiển tự động, kiểm tra khối đã sẵn sàng ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển quyền về Program, sau đó gán giá trị vào `PSet_*` và kích xung `PCmd_* = 1` (khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Out_Horn`, `Val_AvgPctFLA`, `Val_PctTherm`, `Val_CurPhaseA`, `Val_CurPhaseB`, `Val_CurPhaseC`, `Val_RealPwr`, `Val_PwrFact`, `Val_LastFaultCode`, `Val_Cmd`, `Val_Fdbk`, `Val_Sts`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Val_Owner`, `Sts_Stopped`, `Sts_Starting`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 95 Biến Đầu Vào (`Input Parameters`) của `P_SMCFlex`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Inp`** | `P_SMCFlex_Inp` | `-` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu điều kiện đầu vào (1 = Kích hoạt điều kiện / Báo động, 0 = Bình thường) |
| **`Out`** | `P_SMCFlex_Out` | `-` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra điều khiển thiết bị (hoặc kết quả cổng điều kiện sau thời gian trễ On-Delay / Off-Delay) |
| **`Ref_FaultCodeList`** | `P_DescList[1]` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới mảng bảng tra cứu chứa danh sách Mã lỗi (DINT) và Chuỗi mô tả lỗi (STRING) của biến tần/khởi động mềm |
| **`Inp_PermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp điều kiện Cho phép khởi động có thể Bypass (1 = Tất cả điều kiện Perm OK, cho phép khởi động; thường nối từ P_Perm.Sts_PermOK) |
| **`Inp_NBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp điều kiện Cho phép khởi động KHÔNG thể Bypass (1 = Tất cả điều kiện Non-Bypassable Perm OK; thường nối từ P_Perm.Sts_NBPermOK) |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp Khóa liên động an toàn có thể Bypass (1 = Liên động OK cho phép chạy; 0 = Cắt dừng thiết bị ngay lập tức; nối từ P_Intlk.Sts_IntlkOK) |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp Khóa liên động an toàn KHÔNG thể Bypass (1 = Liên động bất khả kháng OK; 0 = Cắt dừng thiết bị ngay lập tức; nối từ P_Intlk.Sts_NBIntlkOK) |
| **`Inp_IntlkAvail`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu báo điều kiện Khóa liên động sẵn sàng cho phép gọi máy trong nhóm (1 = Interlock Availability OK) |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi truyền thông I/O của thiết bị (0 = Tốt/OK, 1 = Lỗi truyền thông I/O Fault) |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân kích hoạt chế độ Mô phỏng (Simulation): Khi = 1, khóa các ngõ ra vật lý ở mức an toàn và dùng giá trị/phản hồi giả lập nội bộ; khi = 0, vận hành thực tế bình thường |
| **`Inp_Hand`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu chọn chế độ Điều khiển bằng tay tại chỗ (Hand/Local): 1 = Chiếm quyền sang chế độ Hand (thường nối từ công tắc cứng tại tủ MCC); 0 = Nhả chế độ Hand |
| **`Inp_Ovrd`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu kích hoạt chế độ Cưỡng bức ưu tiên (Override): 1 = Chiếm quyền sang chế độ Override; 0 = Nhả chế độ Override |
| **`Inp_OvrdCmd`** | `DINT` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Mã lệnh điều khiển thiết bị khi ở chế độ Cưỡng bức (Override Command — kiểu số nguyên phụ thuộc vào từng loại thiết bị: 0 = Không tác động, 1 = Dừng/Đóng/Tắt, 2 = Chạy/Mở/Bật...) |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân khóa cấm nguồn lệnh ngoài (External Inhibit): 1 = Cấm chế độ External chiếm quyền điều khiển; 0 = Cho phép External chiếm quyền |
| **`Inp_HornInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân khóa cấm còi báo động tại hiện trường: 1 = Cấm phát còi báo (Inhibit Horn); 0 = Cho phép phát còi báo trước khi khởi động |
| **`Inp_Reset`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Cfg_PwrUnits`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Đơn vị đo công suất hiển thị của biến tần trung thế: 1 = Megawatt (MW); 0 = Kilowatt (kW) |
| **`Cfg_HasJog`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép và hiển thị nút nhấn Nhấp thử (Jog) trên màn hình HMI; 0 = Khóa cấm lệnh Jog |
| **`Cfg_AllowLocal`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép khởi động/dừng động cơ tại chỗ ở tủ MCC mà không phát báo động lỗi; 0 = Chỉ cho phép điều khiển từ HMI hoặc Program |
| **`Cfg_HasPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Điều kiện cho phép (P_Perm) nối vào chân Inp_PermOK và bật nút điều hướng mở Faceplate P_Perm |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Khóa liên động (P_Intlk) nối vào chân Inp_IntlkOK và bật nút điều hướng mở Faceplate P_Intlk |
| **`Cfg_HasResInhObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Cấm khởi động lại (P_ResInh) đi kèm và bật nút điều hướng mở Faceplate P_ResInh |
| **`Cfg_HasRunTimeObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Đếm giờ chạy (P_RunTime) đi kèm và bật nút điều hướng mở Faceplate P_RunTime |
| **`Cfg_OperStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Nút nhấn Dừng của Người vận hành (OCmd_Stop) có quyền ưu tiên cao nhất, bấm dừng được ngay cả khi máy đang chạy ở chế độ Program; 0 = OCmd_Stop chỉ có tác dụng khi ở chế độ Operator/Maintenance |
| **`Cfg_ExtStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Lệnh Dừng từ hệ thống ngoài (XCmd_Stop) có hiệu lực ưu tiên ở mọi chế độ; 0 = Chỉ có hiệu lực khi ở chế độ External |
| **`Cfg_OCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khi Người vận hành ra lệnh Chạy/Mở mới (OCmd_*), khối tự động xóa luôn các chốt lỗi cũ; 0 = Bắt buộc phải nhấn nút Reset trước khi ra lệnh mới |
| **`Cfg_XCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khi hệ thống ngoài gửi lệnh Chạy/Mở mới (XCmd_*), khối tự động xóa luôn các chốt lỗi cũ; 0 = Bắt buộc phải gửi xung XCmd_Reset trước |
| **`Cfg_OvrdPermIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Chế độ Cưỡng bức (Override) được quyền bỏ qua các điều kiện Cho phép và Liên động có thể bypass; 0 = Chế độ Override vẫn phải tuân thủ mọi điều kiện |
| **`Cfg_ShedOnFailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt lệnh chạy (Shed) và phát báo động khi động cơ bị lỗi Không khởi động được (Fail to Start); 0 = Chỉ phát báo động nhưng vẫn giữ ngõ ra |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt dừng thiết bị về trạng thái an toàn (Shed) và phát báo động khi lỗi truyền thông thẻ I/O; 0 = Chỉ phát báo động |
| **`Cfg_HasFailToStartAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi không khởi động được (Fail to Start): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasFailToStopAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi không dừng được (Fail to Stop): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasMotorFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi động cơ (Motor Fault): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_FailToStartResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi không khởi động được (Fail to Start): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_FailToStopResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi không dừng được (Fail to Stop): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_MotorFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi động cơ (Motor Fault): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_FailToStartAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi không khởi động được (Fail to Start): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_FailToStopAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi không dừng được (Fail to Stop): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_MotorFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi động cơ (Motor Fault): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_FailToStartSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi không khởi động được (Fail to Start) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_FailToStopSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi không dừng được (Fail to Stop) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_MotorFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi động cơ (Motor Fault) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_StartHornT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian hú còi cảnh báo tại hiện trường (Out_Horn) trước khi đóng điện khởi động thiết bị (giây; đặt = 0.0 để tắt còi) |
| **`Cfg_SimFdbkT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ giả lập tín hiệu phản hồi chạy/đóng/mở trong chế độ Mô phỏng (Inp_Sim = 1, tính bằng giây) |
| **`Cfg_FailToStartT`** | `REAL` | `15` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian chờ tối đa (giây) từ lúc xuất lệnh Chạy đến khi phải nhận được phản hồi chạy (Inp_RunFdbk = 1) trước khi báo lỗi FailToStart |
| **`Cfg_FailToStopT`** | `REAL` | `15` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian chờ tối đa (giây) từ lúc xuất lệnh Dừng đến khi phản hồi chạy phải về 0 trước khi báo lỗi FailToStop |
| **`Cfg_ResetPulseT`** | `REAL` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Độ rộng xung xuất ra chân xóa lỗi biến tần/khởi động mềm (giây) |
| **`Cfg_MaxJogT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian tối đa cho phép giữ lệnh chạy nhấp thử Jog liên tục (giây; đặt = 0.0 là không giới hạn) |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Mã định danh Chủ sở hữu (Owner ID: ghi khác 0 để đăng ký chiếm quyền điều khiển, ghi 0 để nhả quyền) |
| **`PCmd_Start`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khởi động (Start) thiết bị/động cơ |
| **`PCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Dừng (Stop) thiết bị/động cơ |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Chương trình tự động (từ Operator sang Program) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Người vận hành (từ Program sang Operator) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa giữ nguồn lệnh hiện tại (không cho phép nguồn lệnh kia giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Đưa nguồn lệnh về chế độ Bình thường mặc định (Normal: Program hoặc Operator theo Cfg_ProgNormal) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`PCmd_FailToStartAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi không khởi động được (Fail to Start) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_FailToStartSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi không khởi động được (Fail to Start) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStartUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi không khởi động được (Fail to Start) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStartUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi không khởi động được (Fail to Start) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_FailToStopAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi không dừng được (Fail to Stop) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_FailToStopSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi không dừng được (Fail to Stop) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStopUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi không dừng được (Fail to Stop) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStopUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi không dừng được (Fail to Stop) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_MotorFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi động cơ (Motor Fault) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_MotorFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi động cơ (Motor Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_MotorFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi động cơ (Motor Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_MotorFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi động cơ (Motor Fault) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) ngay lập tức (biến xung tự động xóa về 0) |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`XCmd_Start`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Khởi động (Start) thiết bị/động cơ |
| **`XCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Dừng (Stop) thiết bị/động cơ |
| **`XCmd_Jog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhấp thử (Jog) thiết bị/động cơ khi giữ nút |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Chiếm quyền điều khiển (Acquire) về chế độ tương ứng |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |

## 3. Bảng chi tiết 104 Biến Đầu Ra (`Output Parameters`) của `P_SMCFlex`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Inp`** | `P_SMCFlex_Inp` | `-` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu điều kiện đầu vào (1 = Kích hoạt điều kiện / Báo động, 0 = Bình thường) |
| **`Out`** | `P_SMCFlex_Out` | `-` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra điều khiển thiết bị (hoặc kết quả cổng điều kiện sau thời gian trễ On-Delay / Off-Delay) |
| **`Ref_FaultCodeList`** | `P_DescList[1]` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới mảng bảng tra cứu chứa danh sách Mã lỗi (DINT) và Chuỗi mô tả lỗi (STRING) của biến tần/khởi động mềm |
| **`Out_Horn`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra bật còi cảnh báo âm thanh tại hiện trường trước khi khởi động hoặc chuyển trạng thái thiết bị (1 = Bật còi) |
| **`Val_AvgPctFLA`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Dòng điện trung bình 3 pha tính theo phần trăm dòng định mức (% Full Load Amps) |
| **`Val_PctTherm`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Phần trăm dung lượng nhiệt đã sử dụng của động cơ (% Thermal Usage — đạt 100% sẽ cắt bảo vệ quá tải) |
| **`Val_CurPhaseA`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Dòng điện pha A của động cơ (Ampe) |
| **`Val_CurPhaseB`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Dòng điện pha B của động cơ (Ampe) |
| **`Val_CurPhaseC`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Dòng điện pha C của động cơ (Ampe) |
| **`Val_RealPwr`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Công suất tác dụng tiêu thụ của động cơ (Real Power = U × I × cosφ, đơn vị MW) |
| **`Val_PwrFact`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Hệ số công suất cosφ của động cơ (% Power Factor) |
| **`Val_LastFaultCode`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã lỗi gần nhất của biến tần (Last Drive Fault Code) |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng dữ liệu tổng hợp cuối cùng (0 = Tín hiệu thực Tốt đã xác nhận, 1 = Tín hiệu thực Tốt giả định, 2 = Không cấu hình phản hồi/Tốt giả định, 8 = Đang mô phỏng, 9 = Mô phỏng vòng lặp, 10 = Giá trị nhập tay, 16 = Ngoài đặc tính kỹ thuật, 17 = Thay thế tại thiết bị/bus, 18 = Thay thế bởi Bảo trì, 19 = Ngắt bảo vệ giữ giá trị cũ, 20 = Ngắt bảo vệ dùng giá trị thay thế, 32 = Lỗi tín hiệu vượt dải/NaN, 33 = Lỗi kênh I/O, 34 = Lỗi module I/O, 35 = Lỗi cấu hình) |
| **`Val_Cmd`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã lệnh điều khiển thiết bị đang xuất ra (Device Command enum: 0 = Không tác động/Tắt, 1 = Dừng/Đóng/Off, 2 = Chạy/Mở/On...) |
| **`Val_Fdbk`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái phản hồi thực tế từ thiết bị hiện trường (Device Feedback enum: 0 = Đang dừng/Đang chuyển trạng thái, 1 = Đang chạy/Đóng/Off, 2 = Mở/On, 3 = Phản hồi không hợp lệ) |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái hoạt động xác nhận của thiết bị (Device Confirmed Status enum dùng để điều khiển màu sắc và biểu tượng động trên HMI) |
| **`Val_Fault`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái lỗi thiết bị (Device Fault Status — kiểu liệt kê enum: 0 = Không lỗi, 16 = Lỗi khởi động/mở, 17 = Lỗi dừng/đóng, 20 = Lo, 21 = Hi, 24 = LoLo, 25 = HiHi, 32 = Fail/I/O Fault, 34 = Lỗi cấu hình CfgErr) |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận tổng hợp của khối này cộng với các đối tượng kênh/quá tải liên kết |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms Count) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã định danh Chủ sở hữu hiện hành của đối tượng (Current Object Owner ID: 0 = Đang không bị chiếm quyền) |
| **`Sts_Stopped`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần được yêu cầu dừng và đã xác nhận Dừng hoàn toàn (Confirmed Stopped) |
| **`Sts_Starting`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần được yêu cầu chạy và đang chờ tín hiệu phản hồi chạy (Starting) |
| **`Sts_Running`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần được yêu cầu chạy và đã xác nhận đang Chạy (Confirmed Running) |
| **`Sts_Stopping`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần được yêu cầu dừng và đang chờ tín hiệu phản hồi về dừng (Stopping) |
| **`Sts_Jogging`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần đang chạy nhấp thử (Jogging) |
| **`Sts_NotReady`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị chưa sẵn sàng hoạt động (kiểm tra các bit Nrdy_* chi tiết để biết nguyên nhân cụ thể) |
| **`Sts_Alarm`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Biến tần đang có cảnh báo (kiểm tra màn hình biến tần hoặc mã Val_LastAlarmCode) |
| **`Sts_AtSpeed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Biến tần đang chạy đúng tại tốc độ đặt tham chiếu (At Reference Speed) |
| **`Sts_CtrlPwrOK`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Nguồn điện điều khiển (Control Power) tốt (OK); 0 = Mất nguồn điều khiển |
| **`Sts_Phasing`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Thứ tự pha nguồn lưới: 1 = Thứ tự pha thuận ABC; 0 = Thứ tự pha ngược CBA |
| **`Sts_PhasingActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Phát hiện nguồn lưới 3 pha hợp lệ; 0 = Không phát hiện nguồn 3 pha hợp lệ |
| **`Sts_StartIsol`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Contactor khởi động / cách ly (Start/Isolation Contactor) đã đóng; 0 = Đang mở |
| **`Sts_ExtBypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Contactor mạch vòng ngoài (External Bypass Contactor) đang đóng; 0 = Đang mở |
| **`Sts_Opt1Inp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Ngõ vào tùy chọn số 1 (Option 1 Input) đang kích hoạt; 0 = Không kích hoạt |
| **`Sts_Opt2Inp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Ngõ vào tùy chọn số 2 (Option 2 Input) đang kích hoạt; 0 = Không kích hoạt |
| **`Sts_Available`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị sẵn sàng cho Chương trình tự động (Program) điều khiển (đang ở chế độ Program và không có lỗi/liên động ngắt) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass hiện đang được bỏ qua (Bypassed) |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Chế độ Bypass bảo trì đang kích hoạt (gửi sang khối P_Intlk / P_Perm để cho phép bỏ qua các điều kiện có quyền Bypass) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị chưa sẵn sàng hoạt động (kiểm tra các bit Nrdy_* chi tiết để biết nguyên nhân cụ thể) |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_DriveNR`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Biến tần báo trạng thái chưa sẵn sàng (Drive Not Ready) |
| **`Nrdy_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang có lỗi thiết bị ngắt bảo vệ (Shed — yêu cầu nhấn Reset) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Khóa liên động an toàn không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi truyền thông thẻ I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Nrdy_PrioStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng: Đang dính lệnh Dừng ưu tiên từ Operator/External (cần nhấn Reset trước khi chạy lại) |
| **`Nrdy_Perm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Cho phép khởi động không thỏa mãn (Permissive Not OK) |
| **`Nrdy_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng: Thiết bị đang bị cắt bảo vệ quá tải (Tripped tại thiết bị hoặc do lệnh Trip) |
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
| **`Sts_Oper`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Người vận hành trên HMI (Operator) |
| **`Sts_OperLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang ở chế độ Người vận hành và đã Khóa (Operator Locked — không cho Program lấy quyền) |
| **`Sts_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Nguồn lệnh hiện hành đang khớp với nguồn lệnh Bình thường mặc định (Program hoặc Operator theo Cfg_ProgNormal) |
| **`Sts_ExtReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Yêu cầu chuyển sang chế độ External đang bị khóa cấm (không thể chuyển sang External từ trạng thái hiện tại) |
| **`Sts_ProgReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Yêu cầu chuyển sang chế độ Program đang bị khóa cấm (không thể chuyển sang Program từ trạng thái hiện tại) |
| **`Sts_MAcqRcvd`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đã nhận được lệnh Chiếm quyền bảo trì (MCmd_Acq) trong chu kỳ quét hiện tại |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc chốt ngắt bảo vệ (Shed) đã hết lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi không khởi động được (Fail to Start) |
| **`Alm_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi không khởi động được (Fail to Start) |
| **`Ack_FailToStart`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi không khởi động được (Fail to Start) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_FailToStartDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không khởi động được (Fail to Start) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_FailToStartShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không khởi động được (Fail to Start) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_FailToStartSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không khởi động được (Fail to Start) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi không dừng được (Fail to Stop) |
| **`Alm_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi không dừng được (Fail to Stop) |
| **`Ack_FailToStop`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi không dừng được (Fail to Stop) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_FailToStopDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không dừng được (Fail to Stop) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_FailToStopShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không dừng được (Fail to Stop) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_FailToStopSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không dừng được (Fail to Stop) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`Alm_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`Ack_IntlkTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_IntlkTripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_IntlkTripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_IntlkTripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_MotorFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi động cơ (Motor Fault) |
| **`Alm_MotorFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi động cơ (Motor Fault) |
| **`Ack_MotorFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi động cơ (Motor Fault) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_MotorFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi động cơ (Motor Fault) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_MotorFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi động cơ (Motor Fault) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_MotorFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi động cơ (Motor Fault) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi truyền thông thẻ I/O (I/O Fault) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi truyền thông thẻ I/O (I/O Fault) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`MRdy_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`P_SMCFlex`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_SMCFlex) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 30 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_SMCFlex`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'SMC-Flex Smart Motor Controller'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_Label`** | `STRING_20` | `'Motor Control'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_Tag`** | `STRING_20` | `'P_SMCFlex'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc nội bộ quản lý phân quyền và chuyển mạch giữa 7 nguồn lệnh điều khiển (OoS, Hand, Maint, Ovrd, Ext, Prog, Oper) |
| **`FailToStart`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi không khởi động được (Fail to Start) |
| **`FailToStop`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi không dừng được (Fail to Stop) |
| **`HMI_HasMore`** | `BOOL` | `0` | Cờ nội bộ báo cho HMI hiển thị nút mở rộng thông tin |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_SMCFlex'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`IOFault`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bỏ qua (Bypass) tất cả các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Kiểm tra lại bình thường (Check / Gỡ bỏ Bypass) toàn bộ điều kiện Khóa liên động và Cho phép |
| **`MotorFault`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi động cơ (Motor Fault) |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (Bỏ qua (Bypass) tất cả các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (Kiểm tra lại bình thường (Check / Gỡ bỏ Bypass) toàn bộ điều kiện Khóa liên động và Cho phép), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`OCmd_Jog`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Nhấp thử (Jog) thiết bị/động cơ khi giữ nút |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo |
| **`OCmd_Start`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Khởi động (Start) thiết bị/động cơ |
| **`OCmd_Stop`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Dừng (Stop) thiết bị/động cơ |
| **`ORdy_Jog`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Jog (Nhấp thử (Jog) thiết bị/động cơ khi giữ nút), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Start`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Start (Khởi động (Start) thiết bị/động cơ), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Stop`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Stop (Dừng (Stop) thiết bị/động cơ), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`Val_FaultDesc`** | `STRING_40` | `‘Logic not yet scanned.’` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi văn bản mô tả chi tiết nguyên nhân lỗi biến tần (tra cứu tự động từ mã lỗi Val_LastFaultCode) |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo hiện hành và trạng thái xác nhận của khối này (0 = Không có cảnh báo, 1 = Đã hết lỗi nhưng chờ Reset/Ack, 2..3 = Thấp, 4..5 = Trung bình, 6..7 = Cao, 8..9 = Khẩn cấp) |
---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_VSD`
**Thuộc chương:** [Chương 3: Động cơ, Biến tần & Rơ-le Quá tải (Motors & Drives)](../03_CHUONG_3_MOTORS_VA_BIEN_TAN.md)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Motors 4.1.xlsx` đính kèm trong ấn phẩm `PROCES-RM013I-EN-P` (Trang 236–251)  
**Tệp định nghĩa khối lệnh:** `P_VSD_4.10.00_AOI.L5X`  
**Thống kê số lượng biến của `P_VSD`:** **153** Biến đầu vào (`Input Parameters`) | **125** Biến đầu ra (`Output Parameters`) | **45** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Tổng quan Chức năng & Hướng dẫn Tích hợp Khối `P_VSD` trong Studio 5000
- **Chức năng cốt lõi:** Khối điều khiển **Biến tần Đa năng Tổng quát (Generic Variable-Speed Drive)**. Cung cấp đầy đủ các chân tín hiệu rời rạc (`Out_Start`, `Out_Stop`, `Out_Fwd`, `Out_Rev`, `Out_ClearFault`, `Inp_Running`, `Inp_Faulted`) và tín hiệu tương tự (`Out_SpeedRef`, `Inp_SpeedFdbk`).
- **Khi nào CẦN sử dụng:** 1. Khi điều khiển **biến tần của bất kỳ hãng nào (ABB, Siemens, Danfoss, Yaskawa, Schneider, Mitsubishi...)** qua dây cứng I/O hoặc qua mạng Modbus/Profibus/EtherNet/IP. 2. Khi điều khiển biến tần Allen-Bradley đời cũ (PowerFlex 4/40/70/700, Bulletin 1336).
- **Khi nào KHÔNG sử dụng (Khối thay thế):** Khi dùng biến tần PowerFlex 523/525/753/755/6000/7000 đã có khối chuyên dụng riêng ở trên.

### Quy trình 4 bước đấu nối và cấu hình `P_VSD`:
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - **Các biến đầu vào tiêu biểu:** `Ref_FaultCodeList`, `Inp_SpeedFdbk`, `Inp_LastFaultCode`, `Inp_Datalink`, `Inp_Ready`, `Inp_Running`, `Inp_CommandDir`, `Inp_ActualDir`, `Inp_Accelerating`, `Inp_Decelerating`, `Inp_Alarm`, `Inp_Faulted`, `Inp_AtSpeed`, `Inp_FwdPermOK`, `Inp_FwdNBPermOK`
   - **Các chân tham chiếu dữ liệu (`Ref_*` / `InOut`):** `Ref_FaultCodeList`, `Ref_FaultCodeList` — bắt buộc gán thẻ mảng/cấu trúc tương ứng trong Controller Tags.
   - **Cách đấu nối:** Nối trực tiếp các chân `Inp_*` với thẻ I/O vật lý hoặc các cờ trạng thái từ khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`). Khi chạy thử nghiệm phần mềm không có phần cứng thật, đặt `Inp_Sim = 1` để kích hoạt chế độ mô phỏng nội bộ.
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - **Biến `Cfg_*` trong Input Parameters:** `Cfg_HasReverse`, `Cfg_HasJog`, `Cfg_HasMoreObj`, `Cfg_AllowLocal`, `Cfg_HasRunFdbk`, `Cfg_UseRunFdbk`, `Cfg_HasSpeedFdbk`, `Cfg_UseSpeedFdbk`, `Cfg_HasInpDatalink`, `Cfg_HasOutDatalink`, `Cfg_HasFwdPermObj`, `Cfg_HasRevPermObj`, `Cfg_HasIntlkObj`, `Cfg_HasResInhObj`, `Cfg_HasRunTimeObj`, `Cfg_SetTrack`, `Cfg_SetTrackOvrdHand`, `Cfg_OperStopPrio`...
   - **Biến `Cfg_*` trong Local Tags (hiển thị HMI):** `Cfg_Area`, `Cfg_Desc`, `Cfg_FwdText`, `Cfg_InpDatalinkEU`, `Cfg_InpDatalinkLabel`, `Cfg_Label`, `Cfg_OutDatalinkEU`, `Cfg_OutDatalinkLabel`, `Cfg_RevText`, `Cfg_SpeedFdbkEU`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối `P_VSD` -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên HMI Faceplate khi đang chạy Online) để cài đặt dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Sau khi cấu hình, luôn kiểm tra cờ `Sts_Err = 0` để chắc chắn không có thông số nào cài đặt sai phạm vi.
3. **Cách điều khiển khối `P_VSD` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Các biến lệnh & giá trị đặt Program:** `PSet_SpeedRef`, `PSet_OutDatalink`, `PSet_Owner`, `PCmd_StartFwd`, `PCmd_StartRev`, `PCmd_Stop`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_FailToStartAck`, `PCmd_FailToStartSuppress`, `PCmd_FailToStartUnsuppress`, `PCmd_FailToStartUnshelve`, `PCmd_FailToStopAck`, `PCmd_FailToStopSuppress`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh điều khiển tự động, kiểm tra khối đã sẵn sàng ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển quyền về Program, sau đó gán giá trị vào `PSet_*` và kích xung `PCmd_* = 1` (khối sẽ tự động xóa bit `PCmd_*` về 0 sau khi thực thi).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - **Các biến đầu ra tiêu biểu:** `Out_SpeedRef`, `Out_Datalink`, `Out_Run`, `Out_Stop`, `Out_Start`, `Out_Jog`, `Out_ClearFault`, `Out_Fwd`, `Out_Rev`, `Out_Horn`, `Val_SpeedRef`, `Val_SpeedFdbk`, `Val_InpDatalink`, `Val_OutDatalink`, `Val_SpeedRefEUMin`, `Val_SpeedRefEUMax`, `Val_SpeedFdbkEUMin`, `Val_SpeedFdbkEUMax`...
   - **Cách sử dụng:** Nối các chân `Out_*` ra thẻ Output vật lý hoặc khối chức năng phía sau. Sử dụng các cờ trạng thái thực tế `Sts_*` (như `Sts_Running`, `Sts_Opened`, `Sts_IntlkOK`, `Sts_HiHiCmp`...) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các cờ `Alm_*` và `Val_Notify` để cảnh báo trên HMI hoặc kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 153 Biến Đầu Vào (`Input Parameters`) của `P_VSD`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | Chân kích hoạt thực thi khối AOI trên Rung (Enable In): Trong Ladder (LD), nếu điều kiện Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc bỏ trống không nối dây thì khối hoạt động bình thường |
| **`Ref_FaultCodeList`** | `P_DescList[1]` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới mảng bảng tra cứu chứa danh sách Mã lỗi (DINT) và Chuỗi mô tả lỗi (STRING) của biến tần/khởi động mềm |
| **`Inp_SpeedFdbk`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu phản hồi tốc độ thực tế từ biến tần (Speed Feedback, đơn vị thô hoặc 0..100%) |
| **`Inp_LastFaultCode`** | `DINT` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Mã lỗi gần nhất của biến tần (kiểu số nguyên liệt kê đọc từ biến tần) |
| **`Inp_Datalink`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu đầu vào kênh dữ liệu phụ trợ (Input Datalink) đọc từ biến tần (đơn vị thô Raw) |
| **`Inp_Ready`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Bit trạng thái báo biến tần đã sẵn sàng khởi động (1 = Drive Ready to Run) |
| **`Inp_Running`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Bit trạng thái xác nhận biến tần/thiết bị đang chạy (1 = Confirmed Running) |
| **`Inp_CommandDir`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Chiều quay đang được lệnh của biến tần (1 = Lệnh quay Thuận Forward, 0 = Lệnh quay Nghịch Reverse) |
| **`Inp_ActualDir`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Chiều quay thực tế hiện tại của biến tần (1 = Đang quay Thuận Forward, 0 = Đang quay Nghịch Reverse) |
| **`Inp_Accelerating`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Bit trạng thái báo biến tần đang trong quá trình tăng tốc (1 = Accelerating) |
| **`Inp_Decelerating`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Bit trạng thái báo biến tần đang trong quá trình giảm tốc (1 = Decelerating) |
| **`Inp_Alarm`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Bit trạng thái báo biến tần đang có cảnh báo (1 = Drive Alarm Active) |
| **`Inp_Faulted`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Bit trạng thái báo biến tần đang bị lỗi cắt bảo vệ (1 = Drive Faulted) |
| **`Inp_AtSpeed`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Bit trạng thái báo biến tần đã đạt tới tốc độ đặt yêu cầu (1 = Drive At Reference Speed) |
| **`Inp_FwdPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Điều kiện Cho phép khởi động chiều Thuận (có thể bypass) đều OK |
| **`Inp_FwdNBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Điều kiện Cho phép khởi động chiều Thuận (không thể bypass) đều OK |
| **`Inp_RevPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Điều kiện Cho phép khởi động chiều Nghịch (có thể bypass) đều OK |
| **`Inp_RevNBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Điều kiện Cho phép khởi động chiều Nghịch (không thể bypass) đều OK |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp Khóa liên động an toàn có thể Bypass (1 = Liên động OK cho phép chạy; 0 = Cắt dừng thiết bị ngay lập tức; nối từ P_Intlk.Sts_IntlkOK) |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tổng hợp Khóa liên động an toàn KHÔNG thể Bypass (1 = Liên động bất khả kháng OK; 0 = Cắt dừng thiết bị ngay lập tức; nối từ P_Intlk.Sts_NBIntlkOK) |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi truyền thông I/O của thiết bị (0 = Tốt/OK, 1 = Lỗi truyền thông I/O Fault) |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân kích hoạt chế độ Mô phỏng (Simulation): Khi = 1, khóa các ngõ ra vật lý ở mức an toàn và dùng giá trị/phản hồi giả lập nội bộ; khi = 0, vận hành thực tế bình thường |
| **`Inp_Hand`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu chọn chế độ Điều khiển bằng tay tại chỗ (Hand/Local): 1 = Chiếm quyền sang chế độ Hand (thường nối từ công tắc cứng tại tủ MCC); 0 = Nhả chế độ Hand |
| **`Inp_Ovrd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu kích hoạt chế độ Cưỡng bức ưu tiên (Override): 1 = Chiếm quyền sang chế độ Override; 0 = Nhả chế độ Override |
| **`Inp_OvrdCmd`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã lệnh điều khiển thiết bị khi ở chế độ Cưỡng bức (Override Command — kiểu số nguyên phụ thuộc vào từng loại thiết bị: 0 = Không tác động, 1 = Dừng/Đóng/Tắt, 2 = Chạy/Mở/Bật...) |
| **`Inp_OvrdSpeed`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tốc độ đặt yêu cầu cho biến tần khi ở chế độ Cưỡng bức (Override Speed Reference, đơn vị EU) |
| **`Inp_OvrdOutDatalink`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Giá trị xuất ra kênh Output Datalink khi ở chế độ Cưỡng bức (đơn vị EU) |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân khóa cấm nguồn lệnh ngoài (External Inhibit): 1 = Cấm chế độ External chiếm quyền điều khiển; 0 = Cho phép External chiếm quyền |
| **`Inp_HornInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân khóa cấm còi báo động tại hiện trường: 1 = Cấm phát còi báo (Inhibit Horn); 0 = Cho phép phát còi báo trước khi khởi động |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân nhận xung xóa lỗi từ chương trình PLC: Khi = 1, xóa tất cả các cảnh báo chốt (Latched) và chốt ngắt bảo vệ (Shed) đã hết điều kiện lỗi |
| **`Inp_IntlkAvail`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu báo điều kiện Khóa liên động sẵn sàng cho phép gọi máy trong nhóm (1 = Interlock Availability OK) |
| **`Cfg_HasReverse`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép biến tần chạy cả 2 chiều Thuận và Nghịch; 0 = Chỉ cho phép chạy 1 chiều Thuận (Forward Only) |
| **`Cfg_HasJog`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép và hiển thị nút nhấn Nhấp thử (Jog) trên màn hình HMI; 0 = Khóa cấm lệnh Jog |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng mở cửa sổ chi tiết |
| **`Cfg_AllowLocal`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép khởi động/dừng động cơ tại chỗ ở tủ MCC mà không phát báo động lỗi; 0 = Chỉ cho phép điều khiển từ HMI hoặc Program |
| **`Cfg_HasRunFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Động cơ có đấu nối tiếp điểm phụ phản hồi chạy (Inp_RunFdbk) về PLC; 0 = Không có tiếp điểm phản hồi chạy |
| **`Cfg_UseRunFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Sử dụng tiếp điểm phản hồi chạy để kiểm tra lỗi FailToStart/FailToStop; 0 = Tạm bỏ qua (Bypass bảo trì) không kiểm tra phản hồi chạy |
| **`Cfg_HasSpeedFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Biến tần có trả tín hiệu phản hồi tốc độ thực tế về chân Inp_SpeedFdbk |
| **`Cfg_UseSpeedFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Dựa vào ngưỡng tốc độ phản hồi thực tế để xác nhận trạng thái đang chạy; 0 = Dựa vào bit trạng thái Active của biến tần |
| **`Cfg_HasInpDatalink`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Có kết nối và sử dụng kênh dữ liệu phụ trợ đầu vào (Input Datalink) từ biến tần |
| **`Cfg_HasOutDatalink`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Có kết nối và sử dụng kênh dữ liệu phụ trợ đầu ra (Output Datalink) xuống biến tần |
| **`Cfg_HasFwdPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối P_Perm nối vào chân cho phép chạy Thuận (Inp_FwdPermOK) |
| **`Cfg_HasRevPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối P_Perm nối vào chân cho phép chạy Nghịch (Inp_RevPermOK) |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Khóa liên động (P_Intlk) nối vào chân Inp_IntlkOK và bật nút điều hướng mở Faceplate P_Intlk |
| **`Cfg_HasResInhObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Cấm khởi động lại (P_ResInh) đi kèm và bật nút điều hướng mở Faceplate P_ResInh |
| **`Cfg_HasRunTimeObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có khối Đếm giờ chạy (P_RunTime) đi kèm và bật nút điều hướng mở Faceplate P_RunTime |
| **`Cfg_SetTrack`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép các giá trị đặt (PSet_CV / OSet_CV / SP) tự động bám theo ngõ ra thực tế hiện hành để chuyển mạch không giật (Bumpless Transfer) giữa Operator và Program |
| **`Cfg_SetTrackOvrdHand`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép giá trị đặt của Program và Operator tự động bám theo giá trị CV khi đang ở chế độ Override hoặc Hand |
| **`Cfg_OperStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Nút nhấn Dừng của Người vận hành (OCmd_Stop) có quyền ưu tiên cao nhất, bấm dừng được ngay cả khi máy đang chạy ở chế độ Program; 0 = OCmd_Stop chỉ có tác dụng khi ở chế độ Operator/Maintenance |
| **`Cfg_ExtStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Lệnh Dừng từ hệ thống ngoài (XCmd_Stop) có hiệu lực ưu tiên ở mọi chế độ; 0 = Chỉ có hiệu lực khi ở chế độ External |
| **`Cfg_OCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khi Người vận hành ra lệnh Chạy/Mở mới (OCmd_*), khối tự động xóa luôn các chốt lỗi cũ; 0 = Bắt buộc phải nhấn nút Reset trước khi ra lệnh mới |
| **`Cfg_XCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Khi hệ thống ngoài gửi lệnh Chạy/Mở mới (XCmd_*), khối tự động xóa luôn các chốt lỗi cũ; 0 = Bắt buộc phải gửi xung XCmd_Reset trước |
| **`Cfg_OvrdPermIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Chế độ Cưỡng bức (Override) được quyền bỏ qua các điều kiện Cho phép và Liên động có thể bypass; 0 = Chế độ Override vẫn phải tuân thủ mọi điều kiện |
| **`Cfg_ShedOnFailToStart`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt lệnh chạy (Shed) và phát báo động khi động cơ bị lỗi Không khởi động được (Fail to Start); 0 = Chỉ phát báo động nhưng vẫn giữ ngõ ra |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt dừng thiết bị về trạng thái an toàn (Shed) và phát báo động khi lỗi truyền thông thẻ I/O; 0 = Chỉ phát báo động |
| **`Cfg_SimScaleEU`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Trong chế độ mô phỏng, quy đổi tốc độ đặt sang tốc độ phản hồi theo đơn vị kỹ thuật EU |
| **`Cfg_SimScaleRaw`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Trong chế độ mô phỏng, quy đổi tốc độ đặt sang tốc độ phản hồi theo đơn vị thô Raw |
| **`Cfg_DecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho tốc độ/giá trị trên màn hình HMI (từ 0 đến 6 chữ số) |
| **`Cfg_InpDLDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho kênh Input Datalink trên HMI (từ 0 đến 6 chữ số) |
| **`Cfg_OutDLDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Số chữ số thập phân hiển thị cho kênh Output Datalink trên HMI (từ 0 đến 6 chữ số) |
| **`Cfg_HasFailToStartAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi không khởi động được (Fail to Start): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasFailToStopAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi không dừng được (Fail to Stop): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasDriveFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi biến tần / bộ truyền động (Drive Fault): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Có sử dụng và giám sát cảnh báo này; 0 = Vô hiệu hóa cảnh báo |
| **`Cfg_FailToStartResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi không khởi động được (Fail to Start): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_FailToStopResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi không dừng được (Fail to Stop): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_DriveFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi biến tần / bộ truyền động (Drive Fault): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Tự chốt giữ (Latch) cảnh báo và bắt buộc phải gửi lệnh Reset sau khi hết điều kiện lỗi mới xóa được; 0 = Tự động xóa cảnh báo ngay khi tín hiệu trở về bình thường |
| **`Cfg_FailToStartAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi không khởi động được (Fail to Start): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_FailToStopAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi không dừng được (Fail to Stop): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_DriveFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi biến tần / bộ truyền động (Drive Fault): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cho cảnh báo lỗi truyền thông thẻ I/O (I/O Fault): 1 = Bắt buộc người vận hành hoặc chương trình phải gửi lệnh Ack khi có báo động; 0 = Tự động xác nhận ngay khi báo động xuất hiện |
| **`Cfg_FailToStartSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi không khởi động được (Fail to Start) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_FailToStopSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi không dừng được (Fail to Stop) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_DriveFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Mức độ nghiêm trọng (Severity) của cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) để điều khiển màu sắc và ưu tiên trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent) |
| **`Cfg_MinSpdRef`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp dưới tối thiểu cho phép đặt của tốc độ biến tần (Minimum Speed Reference, đơn vị EU) |
| **`Cfg_MaxSpdRef`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp trên tối đa cho phép đặt của tốc độ biến tần (Maximum Speed Reference, đơn vị EU) |
| **`Cfg_SpeedRefRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị thô tối thiểu của tốc độ đặt gửi xuống biến tần (Speed Reference Raw Min) |
| **`Cfg_SpeedRefRawMax`** | `REAL` | `32767` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị thô tối đa của tốc độ đặt gửi xuống biến tần (Speed Reference Raw Max) |
| **`Cfg_SpeedRefEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tối thiểu của tốc độ đặt (Speed Reference EU Min) |
| **`Cfg_SpeedRefEUMax`** | `REAL` | `130` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tối đa của tốc độ đặt (Speed Reference EU Max) |
| **`Cfg_SpeedFdbkRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị thô tối thiểu của tốc độ phản hồi từ biến tần (Speed Feedback Raw Min) |
| **`Cfg_SpeedFdbkRawMax`** | `REAL` | `32767` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị thô tối đa của tốc độ phản hồi từ biến tần (Speed Feedback Raw Max) |
| **`Cfg_SpeedFdbkEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tối thiểu của tốc độ phản hồi (Speed Feedback EU Min) |
| **`Cfg_SpeedFdbkEUMax`** | `REAL` | `130` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tối đa của tốc độ phản hồi (Speed Feedback EU Max) |
| **`Cfg_InpDatalinkRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị thô tối thiểu của kênh Input Datalink dùng để quy đổi tỷ lệ |
| **`Cfg_InpDatalinkRawMax`** | `REAL` | `4095` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị thô tối đa của kênh Input Datalink dùng để quy đổi tỷ lệ |
| **`Cfg_InpDatalinkEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tối thiểu của kênh Input Datalink |
| **`Cfg_InpDatalinkEUMax`** | `REAL` | `409.5` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tối đa của kênh Input Datalink |
| **`Cfg_OutDatalinkMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp dưới cho phép nhập của kênh Output Datalink (đơn vị EU) |
| **`Cfg_OutDatalinkMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Giới hạn kẹp trên cho phép nhập của kênh Output Datalink (đơn vị EU) |
| **`Cfg_OutDatalinkRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị thô tối thiểu của kênh Output Datalink dùng để quy đổi tỷ lệ |
| **`Cfg_OutDatalinkRawMax`** | `REAL` | `4095` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị thô tối đa của kênh Output Datalink dùng để quy đổi tỷ lệ |
| **`Cfg_OutDatalinkEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tối thiểu của kênh Output Datalink |
| **`Cfg_OutDatalinkEUMax`** | `REAL` | `409.5` | Ẩn (Tag) | [Biến cấu hình thông số] Giá trị đơn vị kỹ thuật tối đa của kênh Output Datalink |
| **`Cfg_StartHornT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian hú còi cảnh báo tại hiện trường (Out_Horn) trước khi đóng điện khởi động thiết bị (giây; đặt = 0.0 để tắt còi) |
| **`Cfg_SimRampT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian tăng/giảm tốc độ phản hồi giả lập từ 0 đến 100% trong chế độ Mô phỏng biến tần (giây) |
| **`Cfg_FailToStartT`** | `REAL` | `15` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian chờ tối đa (giây) từ lúc xuất lệnh Chạy đến khi phải nhận được phản hồi chạy (Inp_RunFdbk = 1) trước khi báo lỗi FailToStart |
| **`Cfg_FailToStopT`** | `REAL` | `15` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian chờ tối đa (giây) từ lúc xuất lệnh Dừng đến khi phản hồi chạy phải về 0 trước khi báo lỗi FailToStop |
| **`Cfg_ResetPulseT`** | `REAL` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Độ rộng xung xuất ra chân xóa lỗi biến tần/khởi động mềm (giây) |
| **`Cfg_MaxJogT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian tối đa cho phép giữ lệnh chạy nhấp thử Jog liên tục (giây; đặt = 0.0 là không giới hạn) |
| **`Cfg_OperKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ bit cấu hình cho phép Operator giữ quyền chỉnh thông số ngay cả khi khối ở chế độ Program (.0 = Giữ quyền chọn kênh PV, .1 = Giữ quyền chỉnh ngưỡng cảnh báo) |
| **`Cfg_ProgKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ bit cấu hình cho phép Program giữ quyền chỉnh thông số ngay cả khi khối ở chế độ Operator (.0 = Giữ quyền chọn kênh PV, .1 = Giữ quyền chỉnh ngưỡng cảnh báo) |
| **`Cfg_ExtKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Từ bit cấu hình cho phép External giữ quyền điều khiển khi ở Program/Operator (.0 = Giữ tốc độ đặt Reference, .1 = Giữ lệnh điều khiển Commands) |
| **`PSet_SpeedRef`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Giá trị tốc độ đặt yêu cầu cho biến tần (Speed Reference, tính theo đơn vị kỹ thuật EU như Hz, RPM hoặc %) |
| **`PSet_OutDatalink`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Giá trị xuất ra kênh dữ liệu mở rộng của biến tần (Output Datalink, đơn vị kỹ thuật EU) |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Giá trị đặt từ Chương trình tự động (Program): Mã định danh Chủ sở hữu (Owner ID: ghi khác 0 để đăng ký chiếm quyền điều khiển, ghi 0 để nhả quyền) |
| **`XSet_SpeedRef`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài] Giá trị đặt từ Hệ thống điều khiển ngoài (External): Giá trị tốc độ đặt yêu cầu cho biến tần (Speed Reference, tính theo đơn vị kỹ thuật EU như Hz, RPM hoặc %) |
| **`XSet_OutDatalink`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài] Giá trị đặt từ Hệ thống điều khiển ngoài (External): Giá trị xuất ra kênh dữ liệu mở rộng của biến tần (Output Datalink, đơn vị kỹ thuật EU) |
| **`PCmd_StartFwd`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khởi động chạy Thuận (Start Forward) |
| **`PCmd_StartRev`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khởi động chạy Nghịch (Start Reverse) |
| **`PCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Dừng (Stop) thiết bị/động cơ |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Chương trình tự động (từ Operator sang Program) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Chuyển nguồn lệnh sang chế độ Người vận hành (từ Program sang Operator) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa giữ nguồn lệnh hiện tại (không cho phép nguồn lệnh kia giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Đưa nguồn lệnh về chế độ Bình thường mặc định (Normal: Program hoặc Operator theo Cfg_ProgNormal) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`PCmd_FailToStartAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi không khởi động được (Fail to Start) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_FailToStartSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi không khởi động được (Fail to Start) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStartUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi không khởi động được (Fail to Start) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStartUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi không khởi động được (Fail to Start) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_FailToStopAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi không dừng được (Fail to Stop) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_FailToStopSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi không dừng được (Fail to Stop) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStopUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi không dừng được (Fail to Stop) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_FailToStopUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi không dừng được (Fail to Stop) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_DriveFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_DriveFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_DriveFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_DriveFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) ngay lập tức (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) (biến xung tự động xóa về 0 sau khi thực thi) |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) từ chương trình (biến xung tự động xóa về 0) |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) ngay lập tức (biến xung tự động xóa về 0) |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program) |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Chiếm quyền điều khiển (Acquire) về chế độ tương ứng |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program |
| **`XCmd_StartFwd`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Khởi động chạy Thuận (Start Forward) |
| **`XCmd_StartRev`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Khởi động chạy Nghịch (Start Reverse) |
| **`XCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Dừng (Stop) thiết bị/động cơ |
| **`XCmd_JogFwd`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhấp thử chạy Thuận (Jog Forward) |
| **`XCmd_JogRev`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhấp thử chạy Nghịch (Jog Reverse) |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |

## 3. Bảng chi tiết 125 Biến Đầu Ra (`Output Parameters`) của `P_VSD`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | Ngõ ra nối tầng Rung (Enable Out): Luôn phản ánh nguyên vẹn trạng thái của chân đầu vào EnableIn để nối tiếp sang các khối lệnh phía sau trên cùng nhánh Rung |
| **`Ref_FaultCodeList`** | `P_DescList[1]` | `-` | Hiện (Pin) | [Tham chiếu dữ liệu InOut] Tham chiếu InOut tới mảng bảng tra cứu chứa danh sách Mã lỗi (DINT) và Chuỗi mô tả lỗi (STRING) của biến tần/khởi động mềm |
| **`Out_SpeedRef`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị tốc độ đặt (Speed Reference) gửi xuống biến tần (đơn vị thô của biến tần, ví dụ 0..32767 tương ứng 0..Fmax) |
| **`Out_Datalink`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Giá trị ngõ ra kênh dữ liệu mở rộng (Output Datalink) gửi xuống biến tần (đơn vị thô Raw) |
| **`Out_Run`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra duy trì lệnh Chạy động cơ trong mạch 2 dây (1 = Chạy động cơ, 0 = Dừng động cơ) |
| **`Out_Stop`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra điều khiển Dừng động cơ hoặc dừng hành trình van (1 = Cấp lệnh Dừng, 0 = Giữ nguyên trạng thái) |
| **`Out_Start`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra phát xung Khởi động động cơ trong mạch 3 dây (1 = Khởi động động cơ, 0 = Giữ nguyên trạng thái hiện tại) |
| **`Out_Jog`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra điều khiển chạy nhấp thử (1 = Chạy Jog ở tốc độ Jog, 0 = Dừng Jog) |
| **`Out_ClearFault`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra phát xung xóa lỗi biến tần / thiết bị truyền động (1 = Gửi lệnh xóa lỗi Clear Fault) |
| **`Out_Fwd`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra chọn chiều quay Thuận cho biến tần (1 = Đặt chiều quay Thuận Forward) |
| **`Out_Rev`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra chọn chiều quay Nghịch cho biến tần (1 = Đặt chiều quay Nghịch Reverse) |
| **`Out_Horn`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Ngõ ra bật còi cảnh báo âm thanh tại hiện trường trước khi khởi động hoặc chuyển trạng thái thiết bị (1 = Bật còi) |
| **`Val_SpeedRef`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị tốc độ đặt (Speed Reference Target) đang gửi xuống biến tần (đơn vị kỹ thuật EU) |
| **`Val_SpeedFdbk`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị tốc độ phản hồi thực tế (Actual Speed Feedback) đọc từ biến tần (đơn vị kỹ thuật EU) |
| **`Val_InpDatalink`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị kênh dữ liệu phụ trợ đầu vào (Input Datalink, đơn vị kỹ thuật EU) đọc từ biến tần |
| **`Val_OutDatalink`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị kênh dữ liệu phụ trợ đầu ra (Output Datalink, đơn vị kỹ thuật EU) gửi xuống biến tần |
| **`Val_SpeedRefEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận dưới của dải tốc độ đặt (Speed Reference EU Min) |
| **`Val_SpeedRefEUMax`** | `REAL` | `130` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận trên của dải tốc độ đặt (Speed Reference EU Max) |
| **`Val_SpeedFdbkEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận dưới của dải tốc độ phản hồi (Speed Feedback EU Min) |
| **`Val_SpeedFdbkEUMax`** | `REAL` | `130` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Giá trị cận trên của dải tốc độ phản hồi (Speed Feedback EU Max) |
| **`Val_LastFaultCode`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã lỗi gần nhất của biến tần (Last Drive Fault Code) |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng của kênh tín hiệu I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | Mã nguồn gốc và chất lượng dữ liệu tổng hợp cuối cùng (0 = Tín hiệu thực Tốt đã xác nhận, 1 = Tín hiệu thực Tốt giả định, 2 = Không cấu hình phản hồi/Tốt giả định, 8 = Đang mô phỏng, 9 = Mô phỏng vòng lặp, 10 = Giá trị nhập tay, 16 = Ngoài đặc tính kỹ thuật, 17 = Thay thế tại thiết bị/bus, 18 = Thay thế bởi Bảo trì, 19 = Ngắt bảo vệ giữ giá trị cũ, 20 = Ngắt bảo vệ dùng giá trị thay thế, 32 = Lỗi tín hiệu vượt dải/NaN, 33 = Lỗi kênh I/O, 34 = Lỗi module I/O, 35 = Lỗi cấu hình) |
| **`Val_Cmd`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã lệnh điều khiển thiết bị đang xuất ra (Device Command enum: 0 = Không tác động/Tắt, 1 = Dừng/Đóng/Off, 2 = Chạy/Mở/On...) |
| **`Val_Fdbk`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái phản hồi thực tế từ thiết bị hiện trường (Device Feedback enum: 0 = Đang dừng/Đang chuyển trạng thái, 1 = Đang chạy/Đóng/Off, 2 = Mở/On, 3 = Phản hồi không hợp lệ) |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái hoạt động xác nhận của thiết bị (Device Confirmed Status enum dùng để điều khiển màu sắc và biểu tượng động trên HMI) |
| **`Val_Fault`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã trạng thái lỗi thiết bị (Device Fault Status — kiểu liệt kê enum: 0 = Không lỗi, 16 = Lỗi khởi động/mở, 17 = Lỗi dừng/đóng, 20 = Lo, 21 = Hi, 24 = LoLo, 25 = HiHi, 32 = Fail/I/O Fault, 34 = Lỗi cấu hình CfgErr) |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận tổng hợp của khối này cộng với các đối tượng kênh/quá tải liên kết |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms Count) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã định danh Chủ sở hữu hiện hành của đối tượng (Current Object Owner ID: 0 = Đang không bị chiếm quyền) |
| **`Sts_Stopped`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần được yêu cầu dừng và đã xác nhận Dừng hoàn toàn (Confirmed Stopped) |
| **`Sts_StartingFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần đang khởi động theo chiều Thuận và chờ phản hồi (Starting Forward) |
| **`Sts_StartingRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần đang khởi động theo chiều Nghịch và chờ phản hồi (Starting Reverse) |
| **`Sts_RunningFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần đang chạy theo chiều Thuận ổn định (Confirmed Running Forward) |
| **`Sts_RunningRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần đang chạy theo chiều Nghịch ổn định (Confirmed Running Reverse) |
| **`Sts_StoppingFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Biến tần đang chạy Thuận được yêu cầu dừng và đang giảm tốc chờ dừng hẳn |
| **`Sts_StoppingRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Biến tần đang chạy Nghịch được yêu cầu dừng và đang giảm tốc chờ dừng hẳn |
| **`Sts_JoggingFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần đang chạy nhấp thử theo chiều Thuận (Jogging Forward) |
| **`Sts_JoggingRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Động cơ/Biến tần đang chạy nhấp thử theo chiều Nghịch (Jogging Reverse) |
| **`Sts_CommandDir`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Chiều quay đang được lệnh của biến tần: 1 = Chiều Thuận (Forward), 0 = Chiều Nghịch (Reverse) |
| **`Sts_ActualDir`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Chiều quay thực tế của biến tần: 1 = Đang quay Thuận (Forward), 0 = Đang quay Nghịch (Reverse) |
| **`Sts_Accel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Biến tần đang trong quá trình Tăng tốc (Accelerating) |
| **`Sts_Decel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Biến tần đang trong quá trình Giảm tốc (Decelerating) |
| **`Sts_NotReady`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị chưa sẵn sàng hoạt động (kiểm tra các bit Nrdy_* chi tiết để biết nguyên nhân cụ thể) |
| **`Sts_Alarm`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Biến tần đang có cảnh báo (kiểm tra màn hình biến tần hoặc mã Val_LastAlarmCode) |
| **`Sts_AtSpeed`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Biến tần đang chạy đúng tại tốc độ đặt tham chiếu (At Reference Speed) |
| **`Sts_SpeedLimited`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Giá trị tốc độ đặt (Speed Reference) đang bị kẹp tại giới hạn Cfg_MinSpdRef hoặc Cfg_MaxSpdRef |
| **`Sts_Available`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị sẵn sàng cho Chương trình tự động (Program) điều khiển (đang ở chế độ Program và không có lỗi/liên động ngắt) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass hiện đang được bỏ qua (Bypassed) |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Chế độ Bypass bảo trì đang kích hoạt (gửi sang khối P_Intlk / P_Perm để cho phép bỏ qua các điều kiện có quyền Bypass) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị chưa sẵn sàng hoạt động (kiểm tra các bit Nrdy_* chi tiết để biết nguyên nhân cụ thể) |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_DriveNR`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Biến tần báo trạng thái chưa sẵn sàng (Drive Not Ready) |
| **`Nrdy_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang có lỗi thiết bị ngắt bảo vệ (Shed — yêu cầu nhấn Reset) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Khóa liên động an toàn không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi truyền thông thẻ I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Nrdy_Perm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Cho phép khởi động không thỏa mãn (Permissive Not OK) |
| **`Nrdy_PrioStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng: Đang dính lệnh Dừng ưu tiên từ Operator/External (cần nhấn Reset trước khi chạy lại) |
| **`Nrdy_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng: Thiết bị đang bị cắt bảo vệ quá tải (Tripped tại thiết bị hoặc do lệnh Trip) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoặc cưỡng bức bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có ít nhất một cảnh báo bị Tạm hoãn (Shelved), Vô hiệu hóa (Disabled) hoặc Ức chế (Suppressed) — hiển thị biểu tượng chữ "I" trên HMI |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình Cfg_* (kiểm tra các bit Err_* chi tiết để biết thông số bị nhập sai) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giá trị đặt bộ định thời gian (Timer Preset) nằm ngoài phạm vi hợp lệ 0..2.147.483 giây |
| **`Err_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thời gian trễ mô phỏng (Cfg_SimFdbkT / Cfg_SimRampT) nằm ngoài phạm vi 0..2.147.483 giây |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình cảnh báo: Thời gian duy trì tối thiểu (Min On Time), thời gian tạm hoãn (Shelf Time) hoặc mức độ nghiêm trọng (Severity 1..1000) không hợp lệ |
| **`Err_FdbkRaw`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải đơn vị thô của tốc độ phản hồi (Speed Feedback Raw) có Min = Max |
| **`Err_FdbkEU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải đơn vị kỹ thuật của tốc độ phản hồi (Speed Feedback EU) có Min = Max |
| **`Err_RefLim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giới hạn kẹp tốc độ đặt bị ngược (Cfg_MinSpdRef > Cfg_MaxSpdRef) |
| **`Err_RefEU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải đơn vị kỹ thuật của tốc độ đặt (Speed Reference EU) có Min = Max |
| **`Err_RefRaw`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải đơn vị thô của tốc độ đặt (Speed Reference Raw) có Min = Max |
| **`Err_InpDLRaw`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải đơn vị thô của kênh Input Datalink có Min = Max |
| **`Err_InpDLEU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải đơn vị kỹ thuật của kênh Input Datalink có Min = Max |
| **`Err_OutDLLim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Giới hạn kẹp của kênh Output Datalink bị ngược (Min > Max) |
| **`Err_OutDLEU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải đơn vị kỹ thuật của kênh Output Datalink có Min = Max |
| **`Err_OutDLRaw`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Dải đơn vị thô của kênh Output Datalink có Min = Max |
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
| **`Sts_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi không khởi động được (Fail to Start) |
| **`Alm_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi không khởi động được (Fail to Start) |
| **`Ack_FailToStart`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi không khởi động được (Fail to Start) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_FailToStartDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không khởi động được (Fail to Start) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_FailToStartShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không khởi động được (Fail to Start) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_FailToStartSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không khởi động được (Fail to Start) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi không dừng được (Fail to Stop) |
| **`Alm_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi không dừng được (Fail to Stop) |
| **`Ack_FailToStop`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi không dừng được (Fail to Stop) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_FailToStopDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không dừng được (Fail to Stop) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_FailToStopShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không dừng được (Fail to Stop) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_FailToStopSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi không dừng được (Fail to Stop) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`Alm_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`Ack_IntlkTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_IntlkTripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_IntlkTripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_IntlkTripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_DriveFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi biến tần / bộ truyền động (Drive Fault) |
| **`Alm_DriveFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi biến tần / bộ truyền động (Drive Fault) |
| **`Ack_DriveFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi biến tần / bộ truyền động (Drive Fault) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_DriveFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_DriveFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_DriveFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi biến tần / bộ truyền động (Drive Fault) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`Sts_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang xuất hiện điều kiện lỗi truyền thông thẻ I/O (I/O Fault) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Đang kích hoạt báo động cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Báo động lỗi truyền thông thẻ I/O (I/O Fault) đã được Xác nhận (Acknowledged); 0 = Chưa xác nhận |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Vô hiệu hóa (Disabled) bởi nhân viên bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Tạm hoãn (Shelved) bởi người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Cảnh báo lỗi truyền thông thẻ I/O (I/O Fault) hiện đang bị Ức chế (Suppressed) bởi chương trình PLC |
| **`MRdy_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (Nhả quyền điều khiển (Release) để trả về chế độ Operator/Program), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (Mở khóa nguồn lệnh hiện tại (cho phép chuyển đổi giữa Operator và Program)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`Sts_Horn`** | `BOOL` | `-` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Còi cảnh báo khởi động tại hiện trường đang kêu (Horn Active) |
| **`P_VSD`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên khối AOI (P_VSD) dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của phần mềm HMI |

## 4. Bảng chi tiết 45 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_VSD`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Tên phân khu công nghệ (Process Area, ví dụ: "area01") dùng để phân quyền bảo mật thao tác trên HMI |
| **`Cfg_Desc`** | `STRING_40` | `'Variable Speed Drive'` | [Biến cấu hình thông số] Chuỗi mô tả tên thiết bị/điểm đo hiển thị trên thanh tiêu đề của cửa sổ HMI Faceplate (tối đa 40 ký tự) |
| **`Cfg_FwdText`** | `STRING_16` | `'Forward'` | [Biến cấu hình thông số] Chuỗi văn bản mô tả chiều quay Thuận (Forward Direction) trên HMI |
| **`Cfg_InpDatalinkEU`** | `STRING_8` | `''` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của thông số kênh Input Datalink hiển thị trên HMI |
| **`Cfg_InpDatalinkLabel`** | `STRING_20` | `''` | [Biến cấu hình thông số] Nhãn mô tả cho thông số kênh Input Datalink hiển thị trên HMI Faceplate |
| **`Cfg_Label`** | `STRING_20` | `'Motor Speed Control'` | [Biến cấu hình thông số] Nhãn tên ngắn gọn hiển thị bên cạnh biểu tượng đồ họa (Graphic Symbol) trên màn hình SCADA/HMI (tối đa 20 ký tự) |
| **`Cfg_OutDatalinkEU`** | `STRING_8` | `''` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của thông số kênh Output Datalink hiển thị trên HMI |
| **`Cfg_OutDatalinkLabel`** | `STRING_20` | `''` | [Biến cấu hình thông số] Nhãn mô tả cho thông số kênh Output Datalink hiển thị trên HMI Faceplate |
| **`Cfg_RevText`** | `STRING_16` | `'Reverse'` | [Biến cấu hình thông số] Chuỗi văn bản mô tả chiều quay Nghịch (Reverse Direction) trên HMI |
| **`Cfg_SpeedFdbkEU`** | `STRING_8` | `'Hz'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của tốc độ phản hồi biến tần (ví dụ: "Hz", "RPM", "%") hiển thị trên HMI |
| **`Cfg_SpeedRefEU`** | `STRING_8` | `'Hz'` | [Biến cấu hình thông số] Chuỗi đơn vị kỹ thuật của tốc độ đặt biến tần (ví dụ: "Hz", "RPM", "%") hiển thị trên HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_VSD'` | [Biến cấu hình thông số] Tên thẻ định danh (Tagname) của thiết bị hiển thị trên thanh tiêu đề cửa sổ HMI Faceplate (tối đa 20 ký tự) |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc nội bộ quản lý phân quyền và chuyển mạch giữa 7 nguồn lệnh điều khiển (OoS, Hand, Maint, Ovrd, Ext, Prog, Oper) |
| **`DriveFault`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi biến tần / bộ truyền động (Drive Fault) |
| **`FailToStart`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi không khởi động được (Fail to Start) |
| **`FailToStop`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi không dừng được (Fail to Stop) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | Tên thư viện màn hình đồ họa (ví dụ: "RA-BAS") dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | Chỉ số Tab hiển thị hiện hành trên cửa sổ Faceplate (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_VSD'` | Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate và phần mềm thông tin |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho ngắt bảo vệ do vi phạm Khóa liên động (Interlock Trip) |
| **`IOFault`** | `P_Alarm` | `{...}` | [Khối con P_Alarm nội bộ] Quản lý toàn bộ vòng đời cảnh báo cho lỗi truyền thông thẻ I/O (I/O Fault) |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bỏ qua (Bypass) tất cả các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Kiểm tra lại bình thường (Check / Gỡ bỏ Bypass) toàn bộ điều kiện Khóa liên động và Cho phép |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (Bỏ qua (Bypass) tất cả các điều kiện Khóa liên động (Interlock) và Cho phép (Permissive) có quyền Bypass), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (Kiểm tra lại bình thường (Check / Gỡ bỏ Bypass) toàn bộ điều kiện Khóa liên động và Cho phép), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`OCmd_JogFwd`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Nhấp thử chạy Thuận (Jog Forward) |
| **`OCmd_JogRev`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Nhấp thử chạy Nghịch (Jog Reverse) |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo |
| **`OCmd_StartFwd`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Khởi động chạy Thuận (Start Forward) |
| **`OCmd_StartRev`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Khởi động chạy Nghịch (Start Reverse) |
| **`OCmd_Stop`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Dừng (Stop) thiết bị/động cơ |
| **`ORdy_JogFwd`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_JogFwd (Nhấp thử chạy Thuận (Jog Forward)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_JogRev`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_JogRev (Nhấp thử chạy Nghịch (Jog Reverse)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_OSet`** | `BOOL` | `0` | [Cờ cho phép nhập dữ liệu trên HMI] 1 = Sẵn sàng nhận các giá trị đặt OSet_* từ Người vận hành (cho phép mở các ô nhập số trên HMI Faceplate) |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (Xóa (Reset) tất cả các chốt lỗi ngắt tải (Shed latches) và các cảnh báo đã hết điều kiện lỗi), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (Xóa (Reset) mọi chốt lỗi và đồng thời Xác nhận (Acknowledge) toàn bộ cảnh báo), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_SpeedRef`** | `BOOL` | `0` | [Cờ cho phép nhập dữ liệu trên HMI] 1 = Sẵn sàng nhận giá trị đặt OSet_SpeedRef (Giá trị tốc độ đặt yêu cầu cho biến tần (Speed Reference, tính theo đơn vị kỹ thuật EU như Hz, RPM hoặc %)), cho phép mở ô nhập số trên HMI Faceplate |
| **`ORdy_StartFwd`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_StartFwd (Khởi động chạy Thuận (Start Forward)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_StartRev`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_StartRev (Khởi động chạy Nghịch (Start Reverse)), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`ORdy_Stop`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Stop (Dừng (Stop) thiết bị/động cơ), cho phép sáng nút nhấn tương ứng trên HMI Faceplate |
| **`OSet_OutDatalink`** | `REAL` | `0` | [Giá trị đặt từ Người vận hành HMI] Giá trị đặt từ Người vận hành (Operator HMI): Giá trị xuất ra kênh dữ liệu mở rộng của biến tần (Output Datalink, đơn vị kỹ thuật EU) |
| **`OSet_SpeedRef`** | `REAL` | `0` | [Giá trị đặt từ Người vận hành HMI] Giá trị đặt từ Người vận hành (Operator HMI): Giá trị tốc độ đặt yêu cầu cho biến tần (Speed Reference, tính theo đơn vị kỹ thuật EU như Hz, RPM hoặc %) |
| **`Val_FaultDesc`** | `STRING_40` | `‘Logic not yet scanned.’` | [Giá trị thực tế đầu ra (Logic/HMI)] Chuỗi văn bản mô tả chi tiết nguyên nhân lỗi biến tần (tra cứu tự động từ mã lỗi Val_LastFaultCode) |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo hiện hành và trạng thái xác nhận của khối này (0 = Không có cảnh báo, 1 = Đã hết lỗi nhưng chờ Reset/Ack, 2..3 = Thấp, 4..5 = Trung bình, 6..7 = Cao, 8..9 = Khẩn cấp) |
