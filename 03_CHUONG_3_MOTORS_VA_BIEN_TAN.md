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
**Thuộc chương:** Chương 3: Động cơ, Biến tần & Rơ-le Quá tải (Motors & Drives)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Motors 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_Motor`:** **87** Biến đầu vào (`Input Parameters`) | **79** Biến đầu ra (`Output Parameters`) | **27** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_Motor`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_RunFdbk`, `Inp_PermOK`, `Inp_NBPermOK`, `Inp_IntlkOK`, `Inp_NBIntlkOK`, `Inp_OvldNotify`, `Inp_HornInh`, `Inp_IOFault`, `Inp_Sim`, `Inp_Hand`, `Inp_Ovrd`, `Inp_OvrdCmd`, `Inp_ExtInh`, `Inp_Reset`, `Inp_IntlkAvail`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_HasJog`, `Cfg_HasRunFdbk`, `Cfg_UseRunFdbk`, `Cfg_AllowLocal`, `Cfg_HasPermObj`, `Cfg_HasIntlkObj`, `Cfg_HasResInhObj`, `Cfg_HasRunTimeObj`, `Cfg_HasMoreObj`, `Cfg_HasOvldObj`, `Cfg_OperStopPrio`, `Cfg_ExtStopPrio`, `Cfg_OCmdResets`, `Cfg_XCmdResets`, `Cfg_OvrdPermIntlk`, `Cfg_ShedOnFailToStart`, `Cfg_ShedOnIOFault`, `Cfg_HasFailToStartAlm`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_Label`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_Motor` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PSet_Owner`, `PCmd_Start`, `PCmd_Stop`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_FailToStartAck`, `PCmd_FailToStopAck`, `PCmd_IntlkTripAck`, `PCmd_IOFaultAck`, `PCmd_FailToStartSuppress`, `PCmd_FailToStopSuppress`, `PCmd_IntlkTripSuppress`, `PCmd_IOFaultSuppress`, `PCmd_FailToStartUnsuppress`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Out_Run`, `Out_Start`, `Out_Stop`, `Out_Horn`, `Val_Cmd`, `Val_Fdbk`, `Val_Sts`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Val_Owner`, `Sts_Stopped`, `Sts_Starting`, `Sts_Running`, `Sts_Stopping`, `Sts_Jogging`, `Sts_Available`, `Sts_Bypass`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 87 Biến Đầu Vào (`Input Parameters`) của `P_Motor`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_RunFdbk`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Tín hiệu tiếp điểm phụ phản hồi chạy từ động cơ: 1 = Động cơ đang chạy, 0 = Động cơ đang dừng |
| **`Inp_PermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Các điều kiện cho phép khởi động (có thể bypass) đều OK, cho phép động cơ khởi động. |
| **`Inp_NBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Các điều kiện cho phép khởi động (không thể bypass) đều OK, cho phép động cơ khởi động. |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Các điều kiện khóa liên động (có thể bypass) đều OK, cho phép động cơ khởi động và duy trì chạy. |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Các điều kiện khóa liên động (không thể bypass) đều OK, cho phép động cơ khởi động và duy trì chạy. |
| **`Inp_OvldNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã trạng thái mức độ ưu tiên và xác nhận cảnh báo của rơ-le quá tải liên kết. |
| **`Inp_HornInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Cấm còi báo động âm thanh; 0 = Cho phép phát còi báo trước khi khởi động |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Trạng thái lỗi truyền thông thẻ I/O đầu vào: 0 = Tốt (OK), 1 = Lỗi truyền thông (Fail) |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân kích hoạt Mô phỏng: Khi đặt = 1, khối giữ tất cả ngõ ra vật lý ở mức 0 (cắt điện an toàn) và giả lập phản hồi như một động cơ đang chạy thực tế. When set to 0, the instruction controls the motor normally. |
| **`Inp_Hand`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Chuyển sang chế độ Hand (điều khiển cứng bằng công tắc tại tủ điện hiện trường). |
| **`Inp_Ovrd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Kích hoạt chế độ Override (Cưỡng bức ưu tiên theo lệnh Inp_OvrdCmd). |
| **`Inp_OvrdCmd`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã lệnh cưỡng bức khi ở chế độ Override: 0 = Không tác động, 1 = Cưỡng bức Dừng, 2 = Cưỡng bức Chạy. |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Cấm nguồn lệnh ngoài (External) chiếm quyền điều khiển; 0 = Cho phép External chiếm quyền. |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân đầu vào dùng để xóa (Reset) tất cả các cảnh báo đang chờ Reset từ chương trình PLC (1 = Xóa cảnh báo). |
| **`Cfg_HasJog`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép và hiển thị nút nhấp thử (Jog) trên HMI; 0 = Cấm lệnh Jog. |
| **`Cfg_HasRunFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine whether the run feedback exists and is checked or if the run feedback does not exist and is not used. When the parameter is 1, the run feedback is connected and is used for failure checking. When the parameter is 0, the run feedback does not exist and is not checked. |
| **`Cfg_UseRunFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Motor run feedback is used for failure checking. 0 = Motor run feedback is bypassed by Maintenance and not checked for failures. |
| **`Cfg_AllowLocal`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép đóng/cắt tại chỗ mà không báo lỗi; 0 = Chỉ điều khiển từ HMI hoặc Program. |
| **`Cfg_HasPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng permissive liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng interlock liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasResInhObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng restart inhibit liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasRunTimeObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng runtime liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_HasOvldObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng overload object is connected and navigation to the overload liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_OperStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép nút Dừng trên HMI (OCmd_Stop) có hiệu lực ở mọi chế độ (kể cả khi đang chạy Program); 0 = Chỉ dừng được ở chế độ Operator/Maintenance. |
| **`Cfg_ExtStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép lệnh dừng ngoài (XCmd_Stop) có hiệu lực ở mọi chế độ; 0 = Chỉ có hiệu lực khi ở chế độ External. |
| **`Cfg_OCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Lệnh vận hành mới từ Operator tự động xóa luôn chốt lỗi cũ; 0 = Bắt buộc phải nhấn Reset trước khi ra lệnh mới. |
| **`Cfg_XCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Lệnh mới từ External tự động xóa luôn chốt lỗi cũ; 0 = Bắt buộc phải gửi lệnh Reset trước. |
| **`Cfg_OvrdPermIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Chế độ Override bỏ qua các liên động/điều kiện cho phép có thể bypass; 0 = Luôn tuân thủ mọi liên động. |
| **`Cfg_ShedOnFailToStart`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt lệnh chạy động cơ (Shed) và phát cảnh báo khi bị lỗi Không khởi động được (Fail to Start); 0 = Chỉ phát cảnh báo. |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt động cơ (Shed) và phát cảnh báo khi lỗi thẻ I/O; 0 = Chỉ phát cảnh báo. |
| **`Cfg_HasFailToStartAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasFailToStopAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_FailToStartResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_FailToStopResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_FailToStartAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_FailToStopAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_FailToStartSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_FailToStopSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_StartHornT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian hú còi cảnh báo tại hiện trường trước khi đóng điện khởi động động cơ (giây, 0 = tắt còi). |
| **`Cfg_SimFdbkT`** | `REAL` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian trễ giả lập phản hồi chạy/dừng trong chế độ Mô phỏng (giây). |
| **`Cfg_FailToStartT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian chờ tối đa (giây) từ lúc xuất lệnh Chạy đến khi phải nhận được phản hồi Inp_RunFdbk = 1 trước khi báo lỗi Fail to Start. |
| **`Cfg_FailToStopT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian chờ tối đa (giây) từ lúc xuất lệnh Dừng đến khi phản hồi Inp_RunFdbk phải về 0 trước khi báo lỗi Fail to Stop. |
| **`Cfg_MaxJogT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian giữ lệnh nhấp thử (Jog) tối đa cho phép (giây, 0 = không giới hạn). |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Mã định danh chủ sở hữu chương trình (Program Owner ID: khác 0 để đăng ký chiếm quyền, bằng 0 để nhả quyền). |
| **`PCmd_Start`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khởi động (Start) động cơ |
| **`PCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Dừng (Stop) động cơ |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để chuyển quyền điều khiển sang Tự động (từ Operator sang Program) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để chuyển quyền điều khiển sang Bằng tay (từ Program sang Operator) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa cứng chế độ Program (không cho phép Operator giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Mở khóa chế độ Program (cho phép Operator lấy quyền điều khiển) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để trả về nguồn lệnh Bình thường mặc định (Normal: Program hoặc Operator) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_Reset = 1 để xóa tất cả các cảnh báo chốt đang chờ Reset (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_FailToStartAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_FailToStopAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Ack = 1 để Xác nhận (Acknowledge) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_FailToStartSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Fail to Start Alarm |
| **`PCmd_FailToStopSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Fail to Stop Alarm |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Interlock Trip Alarm |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) I/O Fault Alarm |
| **`PCmd_FailToStartUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Fail to Start Alarm |
| **`PCmd_FailToStopUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Fail to Stop Alarm |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Interlock Trip Alarm |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) I/O Fault Alarm |
| **`PCmd_FailToStartUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_FailToStopUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Ghi PCmd_<Alarm>Gỡ bỏ tạm hoãn (Unshelve) = 1 để Gỡ bỏ tạm hoãn (Unshelve) alarm (Biến xung tự động xóa về 0 sau khi thực thi). |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để nhả quyền điều khiển hiện tại (Maintenance to Operator/Program/ Override) |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để unlock/release (Operator to Program) Ownership. |
| **`XCmd_Start`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Khởi động (Start) động cơ |
| **`XCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Dừng (Stop) động cơ |
| **`XCmd_Jog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhấp thử (Jog) động cơ |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi |
| **`Inp_IntlkAvail`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Interlock Availibility OK |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Acquire (Oper/Prog to Ext) |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Release (Ext to Oper/Prog) |

## 3. Bảng chi tiết 79 Biến Đầu Ra (`Output Parameters`) của `P_Motor`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Out_Run`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1 = Run Motor, 0 = Dừng (Stop) động cơ |
| **`Out_Start`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1 = Khởi động (Start) động cơ, 0 = Motor left in current state |
| **`Out_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1 = Dừng (Stop) động cơ, 0 = Motor left in current state |
| **`Out_Horn`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1 = Sound audible prior to commanded motor start |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final motor status source and quality. GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`Val_Cmd`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Command 0 = None, 1 = Stop, 2=Start |
| **`Val_Fdbk`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Feedback 0 = Stopped, 1 = Running |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Sts: 0 = ? 1 = Stopped 2=RunFwd 3=RunRev 4=RunSlow 5=StartFwd 6=StartRev 7=StartSlow 8=JogFwd 9=JogRev 10 = Sopping |
| **`Val_Fault`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Fault Status 0 = None, 16=Fail to Start, 17 = Fail to Stop, 32=I/O Fault, 34=Config Error |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận của khối này cộng với khối kênh (enum) |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Current Object Owner ID (0 = not owned) |
| **`Sts_Stopped`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor requested to stop and is confirmed stopped |
| **`Sts_Starting`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor requested to run and awaiting run feedback |
| **`Sts_Running`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor requested to run and is confirmed running |
| **`Sts_Stopping`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor requested to stop and awaiting stopped feedback |
| **`Sts_Jogging`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor requested to Jog |
| **`Sts_Available`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bypassable Interlocks and Permissives are Bypassed |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bypassing Active (Bypassed or Maintenance) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Device Not Ready, see detail bits for reason |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi thiết bị ngắt tải (yêu cầu nhấn Reset) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Khóa liên động không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi truyền thông I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Thiết bị đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Nrdy_Perm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Cho phép khởi động không thỏa mãn (Permissive Not OK) |
| **`Nrdy_PrioStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang có lệnh Dừng ưu tiên từ Operator/External (yêu cầu nhấn Reset) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo đang bị Tạm hoãn (Shelved), Vô hiệu hóa (Disabled) hoặc Ức chế (Suppressed) |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình (kiểm tra các bit Err_* chi tiết để biết nguyên nhân) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thời gian kiểm tra khởi động/dừng (FailToStartT/FailToStopT) ngoài phạm vi 0..2147483 giây |
| **`Err_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thời gian phản hồi mô phỏng (Cfg_SimFdbkT/SimRampT) ngoài phạm vi 0..2147483 giây |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thông số cảnh báo (Thời gian duy trì tối thiểu, thời gian Shelve, mức Severity) không hợp lệ |
| **`Sts_Hand`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Hand đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn OoS, Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_OoS`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Out of Service đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_Maint`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Maintenance đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ovrd, Ext, Prog, Oper) |
| **`Sts_Ovrd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Override đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ext, Prog, Oper) |
| **`Sts_Ext`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = External đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Prog, Oper) |
| **`Sts_Prog`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Program đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_ProgLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Program đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Oper`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_OperLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Selection equals the Normal (Prog or Oper) |
| **`Sts_ExtReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = External request inhibited, cannot Get to External from current state |
| **`Sts_ProgReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Program request inhibited, cannot Get to Program from current state |
| **`Sts_MAcqRcvd`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Maintenance Acquire command received this scan |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc lỗi ngắt tải (Shed) đã hết điều kiện lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Motor Failed to Start (one-shot) |
| **`Alm_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Motor Fail to Start Alarm |
| **`Ack_FailToStart`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Fail to Start Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_FailToStartDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Start Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_FailToStartShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Start Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_FailToStartSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Start Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Motor Failed to Stop |
| **`Alm_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Motor Fail to Stop Alarm |
| **`Ack_FailToStop`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Fail to Stop Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_FailToStopDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Stop Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_FailToStopShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Stop Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_FailToStopSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Stop Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Motor stopped by an Interlock NOT OK (one-shot) |
| **`Alm_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Alarm: Motor stopped by an Interlock NOT OK |
| **`Ack_IntlkTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Interlock Trip Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IntlkTripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock Trip Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IntlkTripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock Trip Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IntlkTripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock Trip Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] I/O Comm Fault Status (0 = OK, 1 = Bad) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = I/O Fault Alarm |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = I/O Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`MRdy_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`Sts_Horn`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Motor Horn Active |
| **`P_Motor`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 27 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_Motor`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'Single Speed Motor'` | [Biến cấu hình thông số] Description for display on HMI. This string is shown in the title bar of the faceplate. |
| **`Cfg_Label`** | `STRING_20` | `'Motor Control'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI. This string appears on the graphic symbol. |
| **`Cfg_Tag`** | `STRING_20` | `'P_Motor'` | [Biến cấu hình thông số] Tagname for display on HMI. This string is shown in the title bar of the faceplate. |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc quản lý lựa chọn nguồn lệnh điều khiển |
| **`FailToStart`** | `P_Alarm` | `{...}` | Motor Failed to Start Alarm |
| **`FailToStop`** | `P_Alarm` | `{...}` | Motor Failed to Stop Alarm |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_Motor'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | Interlock Trip Alarm |
| **`IOFault`** | `P_Alarm` | `{...}` | I/O Fault Alarm |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bypass all Bypassable Interlocks and Permissives |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Check (not bypass) all Interlocks and Permissives |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`OCmd_Jog`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Jog motor (not cleared by instruction!) |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi, plus Xác nhận (Acknowledge) alarms |
| **`OCmd_Start`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Khởi động (Start) động cơ |
| **`OCmd_Stop`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Dừng (Stop) động cơ |
| **`ORdy_Jog`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Jog (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Start`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Start (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Stop`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Stop (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_Motor2Spd`
**Thuộc chương:** Chương 3: Động cơ, Biến tần & Rơ-le Quá tải (Motors & Drives)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Motors 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_Motor2Spd`:** **94** Biến đầu vào (`Input Parameters`) | **83** Biến đầu ra (`Output Parameters`) | **31** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_Motor2Spd`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_SlowRunFdbk`, `Inp_FastRunFdbk`, `Inp_SlowPermOK`, `Inp_SlowNBPermOK`, `Inp_FastPermOK`, `Inp_FastNBPermOK`, `Inp_IntlkOK`, `Inp_NBIntlkOK`, `Inp_OvldNotify`, `Inp_IOFault`, `Inp_Sim`, `Inp_Hand`, `Inp_Ovrd`, `Inp_OvrdCmd`, `Inp_ExtInh`, `Inp_HornInh`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_HasJog`, `Cfg_HasRunFdbk`, `Cfg_UseRunFdbk`, `Cfg_AllowLocal`, `Cfg_HasSlowPermObj`, `Cfg_HasFastPermObj`, `Cfg_HasIntlkObj`, `Cfg_HasResInhObj`, `Cfg_HasRunTimeObj`, `Cfg_HasMoreObj`, `Cfg_HasOvldObj`, `Cfg_OperStopPrio`, `Cfg_ExtStopPrio`, `Cfg_OCmdResets`, `Cfg_XCmdResets`, `Cfg_OvrdPermIntlk`, `Cfg_ShedOnFailToStart`, `Cfg_ShedOnIOFault`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_FastText`, `Cfg_Label`, `Cfg_SlowText`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_Motor2Spd` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PSet_Owner`, `PCmd_StartFast`, `PCmd_StartSlow`, `PCmd_Stop`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_FailToStartAck`, `PCmd_FailToStartSuppress`, `PCmd_FailToStartUnsuppress`, `PCmd_FailToStartUnshelve`, `PCmd_FailToStopAck`, `PCmd_FailToStopSuppress`, `PCmd_FailToStopUnsuppress`, `PCmd_FailToStopUnshelve`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Out_RunSlow`, `Out_RunFast`, `Out_StartSlow`, `Out_StartFast`, `Out_Stop`, `Out_Horn`, `Val_Cmd`, `Val_Fdbk`, `Val_Sts`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Val_Owner`, `Sts_Stopped`, `Sts_StartingSlow`, `Sts_StartingFast`, `Sts_RunningSlow`, `Sts_RunningFast`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 94 Biến Đầu Vào (`Input Parameters`) của `P_Motor2Spd`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_SlowRunFdbk`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Feedback from motor 1 = Running Slow |
| **`Inp_FastRunFdbk`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Feedback from motor 1 = Running Fast |
| **`Inp_SlowPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Permissive OK, motor can start slow |
| **`Inp_SlowNBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Non-Bypassable Permissives OK, motor can start slow |
| **`Inp_FastPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Permissives OK, motor can start fast |
| **`Inp_FastNBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Non-Bypassable Permissives OK, motor can start fast |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Interlocks OK, motor can start/run |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Non-Bypassable Interlocks OK, motor can start/run |
| **`Inp_OvldNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã trạng thái mức độ ưu tiên và xác nhận cảnh báo của rơ-le quá tải liên kết. |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Input Communication Status 0 = OK, 1 = fail |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân kích hoạt Mô phỏng: Khi đặt = 1, khối giữ tất cả ngõ ra vật lý ở mức 0 (cắt điện an toàn) và giả lập phản hồi như một động cơ đang chạy thực tế. When set to 0, the instruction controls the motor normally. |
| **`Inp_Hand`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Acquire Hand (typ. hardwired local), 0 = Release Hand |
| **`Inp_Ovrd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Acquire Override (higher priority program logic), 0 = Release Override |
| **`Inp_OvrdCmd`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Override Command 0 = None, 1 = Stop, 2=Run Fast, 3=Run Slow |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Cấm nguồn lệnh ngoài (External) chiếm quyền điều khiển; 0 = Cho phép External chiếm quyền. |
| **`Inp_HornInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Cấm còi báo động âm thanh; 0 = Cho phép phát còi báo trước khi khởi động |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Reset Shed Latches and Cleared Alarms |
| **`Cfg_HasJog`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép và hiển thị nút nhấp thử (Jog) trên HMI; 0 = Cấm lệnh Jog. |
| **`Cfg_HasRunFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine whether the run feedback exists and is checked or if the run feedback does not exist and is not used. Use run feedback for failure checking when it is connected and the parameter is 1. When the parameter is 0, the run feedback does not exist and is not checked. |
| **`Cfg_UseRunFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Motor run feedback is used for failure checking. 0 = Motor run feedback is bypassed by Maintenance and not checked for failures. |
| **`Cfg_AllowLocal`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Allow Local Start/Stop without alarm, 0 = Start/Stop from HMI/prog only |
| **`Cfg_HasSlowPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tells the HMI that a permissive object (for example, P_Perm) is connected to Inp_SlowPermOK or Inp_FastPermOK. IMPORTANT: The name of the Slow Permissive object in the controller must be this object's name with the suffix ‘_SlowPerm’. For example, if your P_Motor2Spd object has the name ’Motor123’, then its Slow Permissive object must be named ‘Motor123_SlowPerm’. IMPORTANT: The name of the Fast Permissive object in the controller must be this object's name with the suffix ‘_FastPerm’. For example, if your P_Motor2Spd object has the name ’Motor123’, then its Fast Permissive object must be named ‘Motor123_FastPerm’. |
| **`Cfg_HasFastPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tells the HMI that a permissive object (for example, P_Perm) is connected to Inp_SlowPermOK or Inp_FastPermOK. IMPORTANT: The name of the Slow Permissive object in the controller must be this object's name with the suffix ‘_SlowPerm’. For example, if your P_Motor2Spd object has the name ’Motor123’, then its Slow Permissive object must be named ‘Motor123_SlowPerm’. IMPORTANT: The name of the Fast Permissive object in the controller must be this object's name with the suffix ‘_FastPerm’. For example, if your P_Motor2Spd object has the name ’Motor123’, then its Fast Permissive object must be named ‘Motor123_FastPerm’. |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tells the HMI that an interlock object (for example, P_Intlk) is connected to Inp_IntlkOK. IMPORTANT: The name of the Interlock object in the controller must be this object's name with the suffix ‘_Intlk’. For example, if your P_Motor2Spd object has the name ’Motor123’, then its Interlock object must be named ‘Motor123_Intlk’. |
| **`Cfg_HasResInhObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tells the HMI that a restart inhibit (for example, P_ResInh) object is connected. IMPORTANT: The name of the Restart Inhibit object in the controller must be this object's name with the suffix ‘_ResInh’. For example, if your P_Motor2Spd object has the name ’Motor123’, then its Restart Inhibit object must be named ‘Motor_ResInh’. |
| **`Cfg_HasRunTimeObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tells the HMI that a runtime (for example, P_RunTime) object is connected. IMPORTANT: The name of the runtime object in the controller must be this object's name with the suffix ‘_RunTime’. For example, if your P_Motor2Spd object has the name ‘Motor123’, then its runtime object must be named ‘Motor123_RunTime’. |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_HasOvldObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tells the HMI that an overload object is connected. IMPORTANT: The name of the Overload object in the controller must be this object's name with the suffix ‘_Ovld’. For example, if your P_Motor2Spd object has the name ‘Motor123’, then its Overload object must be named ‘Motor123_Ovld’. |
| **`Cfg_OperStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = OCmd_Stop any time, 0 = OCmd_Stop only when Operator selected |
| **`Cfg_ExtStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = XCmd_Stop any time, 0 = XCmd_Stop only when Ext selected |
| **`Cfg_OCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Lệnh vận hành mới từ Operator tự động xóa luôn chốt lỗi cũ; 0 = Bắt buộc phải nhấn Reset trước khi ra lệnh mới. |
| **`Cfg_XCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Lệnh mới từ External tự động xóa luôn chốt lỗi cũ; 0 = Bắt buộc phải gửi lệnh Reset trước. |
| **`Cfg_OvrdPermIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Chế độ Override bỏ qua các liên động/điều kiện cho phép có thể bypass; 0 = Luôn tuân thủ mọi liên động. |
| **`Cfg_ShedOnFailToStart`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt lệnh chạy động cơ (Shed) và phát cảnh báo khi bị lỗi Không khởi động được (Fail to Start); 0 = Chỉ phát cảnh báo. |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt động cơ (Shed) và phát cảnh báo khi lỗi thẻ I/O; 0 = Chỉ phát cảnh báo. |
| **`Cfg_HasFailToStartAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasFailToStopAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_FailToStartResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_FailToStopResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_FailToStartAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_FailToStopAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_FailToStartSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_FailToStopSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_ChangeSpdT`** | `REAL` | `3` | Ẩn (Tag) | [Biến cấu hình thông số] Delay with contactors open when changing speeds (seconds) |
| **`Cfg_StartHornT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian hú còi cảnh báo tại hiện trường trước khi đóng điện khởi động động cơ (giây, 0 = tắt còi). |
| **`Cfg_SimFdbkT`** | `REAL` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Delay to echo back of Running/Stopped status when in simulation or when motor does not have or is not using run feedback (secondsonds). |
| **`Cfg_FailToStartT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Time after Start to get correct Run Feedback before Fault (seconds) |
| **`Cfg_FailToStopT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Time after Stop to drop Run Feedbacks before Fault (seconds) |
| **`Cfg_MaxJogT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian giữ lệnh nhấp thử (Jog) tối đa cho phép (giây, 0 = không giới hạn). |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Mã định danh chủ sở hữu chương trình (Program Owner ID: khác 0 để đăng ký chiếm quyền, bằng 0 để nhả quyền). |
| **`PCmd_StartFast`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khởi động (Start) động cơ at Fast speed |
| **`PCmd_StartSlow`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khởi động (Start) động cơ at Slow speed |
| **`PCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Dừng (Stop) động cơ |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để chuyển quyền điều khiển sang Tự động (từ Operator sang Program) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để chuyển quyền điều khiển sang Bằng tay (từ Program sang Operator) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa cứng chế độ Program (không cho phép Operator giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Mở khóa chế độ Program (cho phép Operator lấy quyền điều khiển) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để trả về nguồn lệnh Bình thường mặc định (Normal: Program hoặc Operator) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Reset Shed Latches and Cleared Alarms |
| **`PCmd_FailToStartAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Fail to Start Alarm |
| **`PCmd_FailToStartSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Fail to Start Alarm |
| **`PCmd_FailToStartUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Fail to Start Alarm |
| **`PCmd_FailToStartUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Fail to Start Alarm |
| **`PCmd_FailToStopAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Fail to Stop Alarm |
| **`PCmd_FailToStopSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Fail to Stop Alarm |
| **`PCmd_FailToStopUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Fail to Stop Alarm |
| **`PCmd_FailToStopUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Fail to Stop Alarm |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Interlock Trip Alarm |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Interlock Trip Alarm |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Interlock Trip Alarm |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Interlock Trip Alarm |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) I/O Fault Alarm |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) I/O Fault Alarm |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) I/O Fault Alarm |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) I/O Fault Alarm |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Release (Maintenance to Operator/Program/Override) |
| **`Sts_Horn`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Device Not Ready, see detail bits for reason |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Unlock Operator (allow Program to acquire) |
| **`XCmd_StartSlow`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Khởi động (Start) động cơ at Slow speed |
| **`XCmd_StartFast`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Khởi động (Start) động cơ at Fast speed |
| **`XCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Dừng (Stop) động cơ |
| **`XCmd_Jog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Jog motor (slow) (not cleared by instruction!) |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Acquire (Oper/Prog to Ext) |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Release (Ext to Oper/Prog) |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi |

## 3. Bảng chi tiết 83 Biến Đầu Ra (`Output Parameters`) của `P_Motor2Spd`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Out_RunSlow`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1 = Run Motor Slow, 0 = Dừng (Stop) động cơ |
| **`Out_RunFast`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1 = Run Motor Fast, 0 = Dừng (Stop) động cơ |
| **`Out_StartSlow`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Pulse 1 to Khởi động (Start) động cơ at Slow speed |
| **`Out_StartFast`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Pulse 1 to Khởi động (Start) động cơ at Fast speed |
| **`Out_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Pulse 1 to Dừng (Stop) động cơ (break seal-in at starter) |
| **`Out_Horn`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1 = Sound audible prior to commanded motor start |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final motor status source and quality: GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`Val_Cmd`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device command: 0 = None 1 = Stop 3 = Start Fast 4 = Start Slow |
| **`Val_Fdbk`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device feedback: 0 = Stopped 2 = Running fast 3 = Running Slow 6 = Invalid |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device confirmed status: 0 = Unknown 1 = Stopped 3 = Running Fast 4 = Running Slow 7 = Stopping 9 = Starting Fast 10 = Starting Slow 33 = Disabled |
| **`Val_Fault`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] This is the Device Fault Status, used to show the operator the most severe device fault: 0 = None 16 = Fail to Start 17 = Fail to Stop 32 = I/O Fault 34 = Configuration error |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận của khối này cộng với khối kênh (enum) |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Current Object Owner ID (0 = not owned) |
| **`Sts_Stopped`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor requested to stop and is confirmed stopped |
| **`Sts_StartingSlow`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor requested to run slow and awaiting slow fdbk |
| **`Sts_StartingFast`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor requested to run fast and awaiting fast fdbk |
| **`Sts_RunningSlow`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor requested to run slow and is confirmed running slow |
| **`Sts_RunningFast`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor requested to run fast and is confirmed running fast |
| **`Sts_Stopping`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor requested to stop and awaiting stopped feedback |
| **`Sts_Jogging`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Motor requested to Jog |
| **`Sts_Available`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bypassable Interlocks and Permissives are Bypassed |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bypassing Active (Bypassed or Maintenance) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Device Not Ready, see detail bits for reason |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi thiết bị ngắt tải (yêu cầu nhấn Reset) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Khóa liên động không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi truyền thông I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Thiết bị đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Nrdy_Perm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Cho phép khởi động không thỏa mãn (Permissive Not OK) |
| **`Nrdy_PrioStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang có lệnh Dừng ưu tiên từ Operator/External (yêu cầu nhấn Reset) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=An Alarm is Disabled or Shelved, display icon |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình (kiểm tra các bit Err_* chi tiết để biết nguyên nhân) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Invalid Check Time (use 0 to 2147483) |
| **`Err_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thời gian phản hồi mô phỏng (Cfg_SimFdbkT/SimRampT) ngoài phạm vi 0..2147483 giây |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thông số cảnh báo (Thời gian duy trì tối thiểu, thời gian Shelve, mức Severity) không hợp lệ |
| **`Err_Pause`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in config: Invalid Pause Time (use 0 to 2147483) |
| **`Sts_Hand`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Hand đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn OoS, Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_OoS`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Out of Service đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_Maint`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Maintenance đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ovrd, Ext, Prog, Oper) |
| **`Sts_Ovrd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Override đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ext, Prog, Oper) |
| **`Sts_Ext`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = External đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Prog, Oper) |
| **`Sts_Prog`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Program đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_ProgLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Program đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Oper`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_OperLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Selection equals the Normal (Prog or Oper) |
| **`Sts_ExtReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = External request inhibited, cannot Get to External from current state |
| **`Sts_ProgReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Program request inhibited, cannot Get to Program from current state |
| **`Sts_MAcqRcvd`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Maintenance Acquire command received this scan |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc lỗi ngắt tải (Shed) đã hết điều kiện lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Motor Failed to Start (one-shot) |
| **`Alm_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Motor Fail to Start Alarm |
| **`Ack_FailToStart`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Fail to Start Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_FailToStartDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Start Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_FailToStartShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Start Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_FailToStartSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Start Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Motor Failed to Stop |
| **`Alm_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Motor Fail to Stop Alarm |
| **`Ack_FailToStop`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Fail to Stop Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_FailToStopDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Stop Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_FailToStopShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Stop Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_FailToStopSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Stop Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Motor stopped by an Interlock NOT OK (one-shot) |
| **`Alm_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Alarm: Motor stopped by an Interlock NOT OK |
| **`Ack_IntlkTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Interlock Trip Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IntlkTripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock Trip Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IntlkTripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock Trip Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IntlkTripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock Trip Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] I/O Comm Fault Status (0 = OK, 1 = Bad) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = I/O Fault Alarm |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = I/O Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`MRdy_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`P_Motor2Spd`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 31 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_Motor2Spd`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'Two Speed Motor'` | [Biến cấu hình thông số] Description for display on HMI. This string is shown in the title bar of the faceplate. |
| **`Cfg_FastText`** | `STRING_16` | `'Fast'` | [Biến cấu hình thông số] Description for FAST speed |
| **`Cfg_Label`** | `STRING_20` | `'Motor Control'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI |
| **`Cfg_SlowText`** | `STRING_16` | `'Slow'` | [Biến cấu hình thông số] Description for SLOW speed |
| **`Cfg_Tag`** | `STRING_20` | `'P_Motor2Spd'` | [Biến cấu hình thông số] Tagname for display on HMI. This string is shown in the title bar of the faceplate. |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc quản lý lựa chọn nguồn lệnh điều khiển |
| **`FailToStart`** | `P_Alarm` | `{...}` | Motor Failed to Start Alarm |
| **`FailToStop`** | `P_Alarm` | `{...}` | Motor Failed to Stop Alarm |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_Motor2Spd'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | Interlock Trip Alarm |
| **`IOFault`** | `P_Alarm` | `{...}` | I/O Fault Alarm |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bypass all Bypassable Interlocks and Permissives |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Check (not bypass) all Interlocks and Permissives |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`OCmd_Jog`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Jog motor (slow) (not cleared by instruction!) |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi, plus Xác nhận (Acknowledge) alarms |
| **`OCmd_StartFast`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Khởi động (Start) động cơ at Fast speed |
| **`OCmd_StartSlow`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Khởi động (Start) động cơ at Slow speed |
| **`OCmd_Stop`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Dừng (Stop) động cơ |
| **`ORdy_Jog`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Jog (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_StartFast`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_StartFast (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_StartSlow`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_StartSlow (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Stop`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Stop (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_MotorRev`
**Thuộc chương:** Chương 3: Động cơ, Biến tần & Rơ-le Quá tải (Motors & Drives)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Motors 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_MotorRev`:** **95** Biến đầu vào (`Input Parameters`) | **85** Biến đầu ra (`Output Parameters`) | **33** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_MotorRev`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_FwdRunFdbk`, `Inp_RevRunFdbk`, `Inp_FwdPermOK`, `Inp_FwdNBPermOK`, `Inp_RevPermOK`, `Inp_RevNBPermOK`, `Inp_IntlkOK`, `Inp_NBIntlkOK`, `Inp_OvldNotify`, `Inp_IOFault`, `Inp_Sim`, `Inp_Hand`, `Inp_Ovrd`, `Inp_OvrdCmd`, `Inp_ExtInh`, `Inp_HornInh`, `Inp_Reset`, `Inp_IntlkAvail`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_HasJog`, `Cfg_HasRunFdbk`, `Cfg_UseRunFdbk`, `Cfg_AllowLocal`, `Cfg_HasFwdPermObj`, `Cfg_HasRevPermObj`, `Cfg_HasIntlkObj`, `Cfg_HasResInhObj`, `Cfg_HasRunTimeObj`, `Cfg_HasMoreObj`, `Cfg_HasOvldObj`, `Cfg_OperStopPrio`, `Cfg_ExtStopPrio`, `Cfg_OCmdResets`, `Cfg_XCmdResets`, `Cfg_OvrdPermIntlk`, `Cfg_ShedOnFailToStart`, `Cfg_ShedOnIOFault`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_FwdText`, `Cfg_Label`, `Cfg_RevText`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_MotorRev` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PSet_Owner`, `PCmd_StartFwd`, `PCmd_StartRev`, `PCmd_Stop`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_FailToStartAck`, `PCmd_FailToStartSuppress`, `PCmd_FailToStartUnsuppress`, `PCmd_FailToStartUnshelve`, `PCmd_FailToStopAck`, `PCmd_FailToStopSuppress`, `PCmd_FailToStopUnsuppress`, `PCmd_FailToStopUnshelve`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Out_RunFwd`, `Out_RunRev`, `Out_StartFwd`, `Out_StartRev`, `Out_Stop`, `Out_Horn`, `Val_Cmd`, `Val_Fdbk`, `Val_Sts`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Val_Owner`, `Sts_Stopped`, `Sts_StartingFwd`, `Sts_StartingRev`, `Sts_RunningFwd`, `Sts_RunningRev`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 95 Biến Đầu Vào (`Input Parameters`) của `P_MotorRev`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_FwdRunFdbk`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Feedback from motor 1 = Running Forward |
| **`Inp_RevRunFdbk`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Feedback from motor 1 = Running Reverse |
| **`Inp_FwdPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Permissives OK, motor can start Forward |
| **`Inp_FwdNBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Non-Bypassable Permissives OK, motor can start Forward |
| **`Inp_RevPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Permissives OK, motor can start Reverse |
| **`Inp_RevNBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Non-Bypassable Permissives OK, motor can start Reverse |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Interlocks OK, motor can start/run |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Non-Bypassable Interlocks OK, motor can start/run |
| **`Inp_OvldNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã trạng thái mức độ ưu tiên và xác nhận cảnh báo của rơ-le quá tải liên kết. |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Input communication status: 0 = OK 1 = Fail |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Chân kích hoạt Mô phỏng: Khi đặt = 1, khối giữ tất cả ngõ ra vật lý ở mức 0 (cắt điện an toàn) và giả lập phản hồi như một động cơ đang chạy thực tế. When set to 0, the instruction controls the motor normally. |
| **`Inp_Hand`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Acquire Hand (typ. hardwired local), 0 = Release Hand |
| **`Inp_Ovrd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Acquire Override (higher priority program logic), 0 = Release Override |
| **`Inp_OvrdCmd`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Override Mode Command 0 = None, 1 = Stop, 2=Fwd, 3=Rev |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Cấm nguồn lệnh ngoài (External) chiếm quyền điều khiển; 0 = Cho phép External chiếm quyền. |
| **`Inp_HornInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Cấm còi báo động âm thanh; 0 = Cho phép phát còi báo trước khi khởi động |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Reset Shed Latches and Cleared Alarms |
| **`Inp_IntlkAvail`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Interlock Availibility OK |
| **`Cfg_HasJog`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép và hiển thị nút nhấp thử (Jog) trên HMI; 0 = Cấm lệnh Jog. |
| **`Cfg_HasRunFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine whether the run feedback exists and is checked or if the run feedback does not exist and is not used. Use run feedback for failure checking when it is connected and the parameter is 1. When the parameter is 0, the run feedback does not exist and is not checked. |
| **`Cfg_UseRunFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Use motor run feedback siignals for failure checking |
| **`Cfg_AllowLocal`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Allow Local Start/Stop without alarm, 0 = Start/Stop from HMI/prog only |
| **`Cfg_HasFwdPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tells the HMI that a permissive object (for example, P_Perm) is connected to Inp_FwdPermOK or Inp_RevPermOK. IMPORTANT: The name of the Forward Permissive object in the controller must be this object's name with the suffix ‘_Perm’. For example, if your P_MotorRev object has the name ’Motor123’, then its Forward Permissive object must be named ‘Motor123_FwdPerm’. IMPORTANT: The name of the Reverse Permissive object in the controller must be this object's name with the suffix ‘_RevPerm’. For example, if your P_MotorRev object has the name ’Motor123’, then its Reverse Permissive object must be named ‘Motor123_RevPerm’. |
| **`Cfg_HasRevPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tells the HMI that a permissive object (for example, P_Perm) is connected to Inp_FwdPermOK or Inp_RevPermOK. IMPORTANT: The name of the Forward Permissive object in the controller must be this object's name with the suffix ‘_Perm’. For example, if your P_MotorRev object has the name ’Motor123’, then its Forward Permissive object must be named ‘Motor123_FwdPerm’. IMPORTANT: The name of the Reverse Permissive object in the controller must be this object's name with the suffix ‘_RevPerm’. For example, if your P_MotorRev object has the name ’Motor123’, then its Reverse Permissive object must be named ‘Motor123_RevPerm’. |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tells the HMI that an interlock object (for example, P_Intlk) is connected to Inp_IntlkOK. IMPORTANT: The name of the Interlock object in the controller must be this object's name with the suffix ‘_Intlk’. For example, if your P_MotorRev object has the name ’Motor123’, then its Interlock object must be named ‘Motor123_Intlk’. |
| **`Cfg_HasResInhObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tells HMI a P_ResInh Restart Inhibit is connected IMPORTANT: The name of the Restart Inhibit object in the controller must be this object's name with the suffix ‘_ResInh’. For example, if your P_MotorRev object has the name ’Motor123’, then its Restart Inhibit object must be named ‘Motor123_ResInh’. |
| **`Cfg_HasRunTimeObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tells HMI a P_RunTime is connected IMPORTANT: The name of the runtime object in the controller must be this object's name with the suffix ‘_RunTime’. For example, if your P_MotorRev object has the name ‘Motor123’, then its runtime object must be named ‘Motor123_RunTime’. |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_HasOvldObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tells HMI an Overload object exists (E3+, etc.) IMPORTANT: The name of the Overload object in the controller must be this object's name with the suffix ‘_Ovld’. For example, if your P_MotorRev object has the name ‘Motor123’, then its Overload object must be named ‘Motor123_Ovld’. |
| **`Cfg_OperStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = OCmd_Stop any time, 0 = OCmd_Stop only when Operator selected |
| **`Cfg_ExtStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = XCmd_Stop any time, 0 = XCmd_Stop only when Ext selected |
| **`Cfg_OCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Lệnh vận hành mới từ Operator tự động xóa luôn chốt lỗi cũ; 0 = Bắt buộc phải nhấn Reset trước khi ra lệnh mới. |
| **`Cfg_XCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Lệnh mới từ External tự động xóa luôn chốt lỗi cũ; 0 = Bắt buộc phải gửi lệnh Reset trước. |
| **`Cfg_OvrdPermIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Chế độ Override bỏ qua các liên động/điều kiện cho phép có thể bypass; 0 = Luôn tuân thủ mọi liên động. |
| **`Cfg_ShedOnFailToStart`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt lệnh chạy động cơ (Shed) và phát cảnh báo khi bị lỗi Không khởi động được (Fail to Start); 0 = Chỉ phát cảnh báo. |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt động cơ (Shed) và phát cảnh báo khi lỗi thẻ I/O; 0 = Chỉ phát cảnh báo. |
| **`Cfg_HasFailToStartAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Fail to Start Alarm exists and will be checked |
| **`Cfg_HasFailToStopAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = IFail to Stop Alarm exists and will be checked |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Interlock Trip Alarm exists and will be checked |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = I/O Fault Alarm exists and will be checked |
| **`Cfg_FailToStartResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Reset requied to clear Fail to Start Alarm |
| **`Cfg_FailToStopResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Reset required to clear Fail to Stop Alarm |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Reset required to clear Interlock Trip Alarm |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Reset required to clear I/O Fault Alarm |
| **`Cfg_FailToStartAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Xác nhận (Acknowledge) required for Fail to Start Alarm |
| **`Cfg_FailToStopAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Xác nhận (Acknowledge) required for Fail to Stop Alarm |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Xác nhận (Acknowledge) required for Interlock Trip Alarm |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Xác nhận (Acknowledge) required for I/O Fault Alarm |
| **`Cfg_FailToStartSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_FailToStopSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_ChangeDirT`** | `REAL` | `3` | Ẩn (Tag) | [Biến cấu hình thông số] Delay with contactors open when changing directions (seconds) |
| **`Cfg_StartHornT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian hú còi cảnh báo tại hiện trường trước khi đóng điện khởi động động cơ (giây, 0 = tắt còi). |
| **`Cfg_SimFdbkT`** | `REAL` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Delay to echo back of Running/Stopped status when in Simulation (seconds) |
| **`Cfg_FailToStartT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Time after Start to get correct Run Feedback before Fault (seconds) |
| **`Cfg_FailToStopT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Time after Stop to drop Run Feedbacks before Fault (seconds) |
| **`Cfg_MaxJogT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian giữ lệnh nhấp thử (Jog) tối đa cho phép (giây, 0 = không giới hạn). |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Mã định danh chủ sở hữu chương trình (Program Owner ID: khác 0 để đăng ký chiếm quyền, bằng 0 để nhả quyền). |
| **`PCmd_StartFwd`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khởi động (Start) động cơ Forward |
| **`PCmd_StartRev`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khởi động (Start) động cơ Reverse |
| **`PCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Dừng (Stop) động cơ |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để chuyển quyền điều khiển sang Tự động (từ Operator sang Program) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để chuyển quyền điều khiển sang Bằng tay (từ Program sang Operator) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa cứng chế độ Program (không cho phép Operator giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Mở khóa chế độ Program (cho phép Operator lấy quyền điều khiển) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để trả về nguồn lệnh Bình thường mặc định (Normal: Program hoặc Operator) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Reset Shed Latches and Cleared Alarms |
| **`PCmd_FailToStartAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Fail to Start Alarm |
| **`PCmd_FailToStartSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Fail to Start Alarm |
| **`PCmd_FailToStartUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Fail to Start Alarm |
| **`PCmd_FailToStartUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Fail to Start Alarm |
| **`PCmd_FailToStopAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Fail to Stop Alarm |
| **`PCmd_FailToStopSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Fail to Stop Alarm |
| **`PCmd_FailToStopUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Fail to Stop Alarm |
| **`PCmd_FailToStopUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Fail to Stop Alarm |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Interlock Trip Alarm |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Interlock Trip Alarm |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Interlock Trip Alarm |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Interlock Trip Alarm |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) I/O Fault Alarm |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) I/O Fault Alarm |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) I/O Fault Alarm |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) I/O Fault Alarm |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Release (Maintenance to Operator/Program/Override) |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Unlock Operator (allow Program to acquire) |
| **`XCmd_StartFwd`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Khởi động (Start) động cơ Forward |
| **`XCmd_StartRev`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Khởi động (Start) động cơ Reverse |
| **`XCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Dừng (Stop) động cơ |
| **`XCmd_JogFwd`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhấp thử (Jog) động cơ Forward (not cleared by instruction!) |
| **`XCmd_JogRev`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhấp thử (Jog) động cơ Reverse (not cleared by instruction!) |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Acquire (Oper/Prog to Ext) |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Release (Ext to Oper/Prog) |

## 3. Bảng chi tiết 85 Biến Đầu Ra (`Output Parameters`) của `P_MotorRev`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Out_RunFwd`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1 = Run Motor Forward, 0 = Dừng (Stop) động cơ |
| **`Out_RunRev`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1 = Run Motor Reverse, 0 = Dừng (Stop) động cơ |
| **`Out_StartFwd`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Pulse 1 to Khởi động (Start) động cơ Forward |
| **`Out_StartRev`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Pulse 1 to Khởi động (Start) động cơ Reverse |
| **`Out_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Pulse 1 to Dừng (Stop) động cơ (break seal-in at starter) |
| **`Out_Horn`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1 = Sound audible prior to commanded motor start |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final motor status source and quality: GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`Val_Cmd`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device command: 0 = None 1 = Stop 5 = Start forward 6 = Start reverse |
| **`Val_Fdbk`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device feedback: 0 = Stopped 4 = Running forward 5 = Running reverse 6 = Invalid |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device status: 0 = Unknown 1 = Stopped 5 = Run forward 6 = Run reverse 7 = Stopping 11 = Starting forward 12 = Starting reverse 32 = Disabled |
| **`Val_Fault`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device fault status: 0 = None 16 = Fail to Start 17 = Fail to Stop 32 = I/O fault 34 = Configuration error |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận của khối này cộng với khối kênh (enum) |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Current Object Owner ID (0 = not owned) |
| **`Sts_Stopped`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor requested to stop and is confirmed stopped |
| **`Sts_StartingFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor requested to run forward and awaiting fwd fdbk |
| **`Sts_StartingRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor requested to run reverse and awaiting rev fdbk |
| **`Sts_RunningFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor requested to run fowrard and is confirmed running fowrard |
| **`Sts_RunningRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor requested to run reverse and is confirmed running reverse |
| **`Sts_Stopping`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor requested to stop and awaiting stopped feedback |
| **`Sts_JoggingFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor requested to Jog Forward |
| **`Sts_JoggingRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor requested to Jog Reverse |
| **`Sts_Available`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bypassable Interlocks and Permissives are Bypassed |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bypassing Active (Bypassed or Maintenance) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Device Not Ready, see detail bits for reason |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi thiết bị ngắt tải (yêu cầu nhấn Reset) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Khóa liên động không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi truyền thông I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Thiết bị đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Nrdy_Perm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Cho phép khởi động không thỏa mãn (Permissive Not OK) |
| **`Nrdy_PrioStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang có lệnh Dừng ưu tiên từ Operator/External (yêu cầu nhấn Reset) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=An Alarm is Disabled or Shelved, display icon |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình (kiểm tra các bit Err_* chi tiết để biết nguyên nhân) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Invalid Check Time (use 0 to 2147483) |
| **`Err_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thời gian phản hồi mô phỏng (Cfg_SimFdbkT/SimRampT) ngoài phạm vi 0..2147483 giây |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thông số cảnh báo (Thời gian duy trì tối thiểu, thời gian Shelve, mức Severity) không hợp lệ |
| **`Err_Pause`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Invalid Pause Time (use 0 to 2147483) |
| **`Sts_Hand`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Hand đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn OoS, Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_OoS`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Out of Service đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_Maint`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Maintenance đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ovrd, Ext, Prog, Oper) |
| **`Sts_Ovrd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Override đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ext, Prog, Oper) |
| **`Sts_Ext`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = External đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Prog, Oper) |
| **`Sts_Prog`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Program đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_ProgLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Program đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Oper`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_OperLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Selection equals the Normal (Prog or Oper) |
| **`Sts_ExtReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = External request inhibited, cannot Get to External from current state |
| **`Sts_ProgReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Program request inhibited, cannot Get to Program from current state |
| **`Sts_MAcqRcvd`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Maintenance Acquire command received this scan |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc lỗi ngắt tải (Shed) đã hết điều kiện lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Motor Failed to Start (one-shot) |
| **`Alm_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Motor Fail to Start Alarm |
| **`Ack_FailToStart`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Fail to Start Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_FailToStartDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Start Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_FailToStartShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Start Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_FailToStartSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Start Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Motor Failed to Stop |
| **`Alm_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Motor Fail to Stop Alarm |
| **`Ack_FailToStop`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Fail to Stop Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_FailToStopDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Stop Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_FailToStopShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Stop Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_FailToStopSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Stop Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Motor stopped by an Interlock NOT OK (one-shot) |
| **`Alm_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Alarm: Motor stopped by an Interlock NOT OK |
| **`Ack_IntlkTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Interlock Trip Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IntlkTripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock Trip Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IntlkTripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock Trip Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IntlkTripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock Trip Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] I/O Comm Fault Status (0 = OK, 1 = Bad) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = I/O Fault Alarm |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = I/O Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`MRdy_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`Sts_Horn`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Motor Horn Active |
| **`P_MotorRev`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 33 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_MotorRev`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'Reversing Motor'` | [Biến cấu hình thông số] Description for display on HMI. This string is shown in the title bar of the faceplate. |
| **`Cfg_FwdText`** | `STRING_16` | `'Forward'` | [Biến cấu hình thông số] Description for Forward direction |
| **`Cfg_Label`** | `STRING_20` | `'Motor Control'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI. This string appears on the graphic symbol. |
| **`Cfg_RevText`** | `STRING_16` | `'Reverse'` | [Biến cấu hình thông số] Description for Reverse direction |
| **`Cfg_Tag`** | `STRING_20` | `'P_MotorRev'` | [Biến cấu hình thông số] Tagname for display on HMI. This string is shown in the title bar of the faceplate. |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Motor Mode Selection |
| **`FailToStart`** | `P_Alarm` | `{...}` | Motor Failed to Start Alarm |
| **`FailToStop`** | `P_Alarm` | `{...}` | Motor Failed to Stop Alarm |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_MotorRev'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | Interlock Trip Alarm |
| **`IOFault`** | `P_Alarm` | `{...}` | I/O Fault Alarm |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bypass all Bypassable Interlocks and Permissives |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Check (not bypass) all Interlocks and Permissives |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Bypass (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Check (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`OCmd_JogFwd`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Nhấp thử (Jog) động cơ Forward (not cleared by instruction!) |
| **`OCmd_JogRev`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Nhấp thử (Jog) động cơ Reverse (not cleared by instruction!) |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi, plus Xác nhận (Acknowledge) alarms |
| **`OCmd_StartFwd`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Khởi động (Start) động cơ Forward |
| **`OCmd_StartRev`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Khởi động (Start) động cơ Reverse |
| **`OCmd_Stop`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Dừng (Stop) động cơ |
| **`ORdy_JogFwd`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_JogFwd (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_JogRev`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_JogRev (enabled HMI button) |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_StartFwd`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_StartFwd (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_StartRev`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_StartRev (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Stop`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Stop (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_MotorHO`
**Thuộc chương:** Chương 3: Động cơ, Biến tần & Rơ-le Quá tải (Motors & Drives)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Motors 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_MotorHO`:** **50** Biến đầu vào (`Input Parameters`) | **52** Biến đầu ra (`Output Parameters`) | **27** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_MotorHO`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_RunFastFwdFdbk`, `Inp_SlowRevFdbk`, `Inp_IntlkOK`, `Inp_NBIntlkOK`, `Inp_OvldNotify`, `Inp_IOFault`, `Inp_Sim`, `Inp_SimRunFastFwd`, `Inp_SimSlowRev`, `Inp_SimStopped`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_HasTrip`, `Cfg_2Spd`, `Cfg_Rev`, `Cfg_HasIntlkObj`, `Cfg_HasRunTimeObj`, `Cfg_HasMoreObj`, `Cfg_HasOvldObj`, `Cfg_OCmdResets`, `Cfg_ShedOnFailToTrip`, `Cfg_ShedOnIOFault`, `Cfg_HasTripFailAlm`, `Cfg_HasIntlkTripAlm`, `Cfg_HasIOFaultAlm`, `Cfg_TripFailResetReqd`, `Cfg_IntlkTripResetReqd`, `Cfg_IOFaultResetReqd`, `Cfg_TripFailAckReqd`, `Cfg_IntlkTripAckReqd`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_FastFwdText`, `Cfg_Label`, `Cfg_SlowRevText`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_MotorHO` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PCmd_Trip`, `PCmd_Reset`, `PCmd_TripFailAck`, `PCmd_TripFailSuppress`, `PCmd_TripFailUnsuppress`, `PCmd_TripFailUnshelve`, `PCmd_IntlkTripAck`, `PCmd_IntlkTripSuppress`, `PCmd_IntlkTripUnsuppress`, `PCmd_IntlkTripUnshelve`, `PCmd_IOFaultAck`, `PCmd_IOFaultSuppress`, `PCmd_IOFaultUnsuppress`, `PCmd_IOFaultUnshelve`
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Out_Trip`, `Val_Cmd`, `Val_Fdbk`, `Val_Sts`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Sts_RunningFastFwd`, `Sts_RunningSlowRev`, `Sts_Stopped`, `Sts_Tripping`, `Sts_Bypass`, `Sts_BypActive`, `Sts_OoS`, `Sts_BadFdbk`, `Sts_NotRdy`, `Sts_MaintByp`, `Sts_AlmInh`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 50 Biến Đầu Vào (`Input Parameters`) của `P_MotorHO`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_RunFastFwdFdbk`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Motor Running (single speed), fast (2-speed) or forward (reversing) |
| **`Inp_SlowRevFdbk`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Motor running slow (2-speed) or reverse (reversing) |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Interlocks OK, interlock not tripping motor |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Non-Bypassable Interlocks OK, interlock not tripping motor |
| **`Inp_OvldNotify`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Mã trạng thái mức độ ưu tiên và xác nhận cảnh báo của rơ-le quá tải liên kết. |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Input Communication Status 0 = OK, 1 = fail |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Simulation input. When set to 1, the instruction uses simulation inputs (for example, Inp_SimRunFastFwd). When set to 0, the instruction uses input parameters (for example, Inp_RunFastFwdFdbk). |
| **`Inp_SimRunFastFwd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Sets simulated motor to running (fast or forward), 0 = no change |
| **`Inp_SimSlowRev`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Sets simulated motor to running slow or reverse, 0 = no change |
| **`Inp_SimStopped`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Sets simulated motor to stopped, 0 = no change |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Reset all fault conditions and latched Alarms |
| **`Cfg_HasTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Trip Output is connected to motor, 0 = Monitor only |
| **`Cfg_2Spd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Motor is 2 speed (fast, slow or stopped) |
| **`Cfg_Rev`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Motor is reversing (forward, reverse or stopped) |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tells HMI a P_Intlk is connected to Inp_Intlk. IMPORTANT: The name of the Interlock object in the controller must be this object's name with the suffix ‘_Intlk’. For example, if your P_MotorHO object has the name’MotorHO123’, then its Interlock object must be named ‘MotorHO123_Intlk’. |
| **`Cfg_HasRunTimeObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tells HMI a P_RunTime is connected. IMPORTANT: The name of the runtime object in the controller must be this object's name with the suffix ‘_RunTime’. For example, if your P_MotorHO object has the name ‘MotorHO123’, then its runtime object must be named ‘MotorHO123_RunTime’. |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_HasOvldObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Enables navigation to connected overload object’s faceplate. IMPORTANT: The name of the Overload object in the controller must be this object's name with the suffix ‘_Ovld’. For example, if your P_MotorHO object has the name ‘MotorHO123’, then its Overload object must be named ‘MotorHO123_Ovld’. |
| **`Cfg_OCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = OCmd_Trip. resets fault, 0 = reset req'd to reset fault |
| **`Cfg_ShedOnFailToTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Continue Trip and Alarm on Fail to Trip; 0 = Alarm only on Fail to Trip |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Trip Motor and Alarm on I/O Fault; 0 = Alarm only on I/O Fault |
| **`Cfg_HasTripFailAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Fail to Trip Alarm exists and will be checked |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Interlock Trip Alarm exists and will be checked |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = I/O Fault Alarm exists and will be checked |
| **`Cfg_TripFailResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Reset requied to clear Fail to Trip Alarm |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Reset required to clear Interlock Trip Alarm |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Reset required to clear I/O Fault Alarm |
| **`Cfg_TripFailAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Xác nhận (Acknowledge) required for Fail to Trip Alarm |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Xác nhận (Acknowledge) required for Interlock Trip Alarm |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Xác nhận (Acknowledge) required for I/O Fault Alarm |
| **`Cfg_TripFailSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_SimFdbkT`** | `REAL` | `3` | Ẩn (Tag) | [Biến cấu hình thông số] Delay to echo back of Open/Closed status when in Simulation (seconds) |
| **`Cfg_TripFailT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] After tripped, time to reach trip position before Alarm (seconds) |
| **`PCmd_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Trip Motor |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để ResetShed Latches and Cleared Alarms |
| **`PCmd_TripFailAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Fail to Trip Alarm |
| **`PCmd_TripFailSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Fail to Trip Alarm |
| **`PCmd_TripFailUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Fail to Trip Alarm |
| **`PCmd_TripFailUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Unsehlve Fail to Trip Alarm |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Interlock Trip Alarm |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Interlock Trip Alarm |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Interlock Trip Alarm |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Interlock Trip Alarm |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) I/O Fault Alarm |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) I/O Fault Alarm |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) I/O Fault Alarm |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) I/O Fault Alarm |

## 3. Bảng chi tiết 52 Biến Đầu Ra (`Output Parameters`) của `P_MotorHO`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Out_Trip`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1 = Trip (stop) Motor |
| **`SrcQ_IO`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final motor status source and quality: GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`Val_Cmd`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Command 0 = None, 7=Trip |
| **`Val_Fdbk`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Feedback 0 = Stop, 1 = Run, 2=Run Fast, 3=Run Slow, 4=Run Fwd, 5=Run Rev, 6=Invalid |
| **`Val_Sts`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] This is the Primary Status used to show the operator the confirmed device status: 0 = Powerup or Unknown 1 = Stop 2 = Run 3 = Fast 4 = Slow 5 = Forward 6 = Reverse 13 = Tripping 33 = Disabled |
| **`Val_Fault`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] This is the Device Fault Status used to show the operator the most severe device fault: 0 = None 18 = Fail to Trip 32 = I/O Fault 34 = Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Val_NotifyAll`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |
| **`Val_UnackAlmC`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Sts_RunningFastFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor is Running (single-speed), Fwd (reversing) or Fast (2-speed) |
| **`Sts_RunningSlowRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor is Running Rev (reversing) or Slow (2-speed) |
| **`Sts_Stopped`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor is confirmed Stopped |
| **`Sts_Tripping`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor requested to Trip and is not yet stopped |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bypassable Interlocks are Bypassed |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bypassing Active (Bypassed or Maintenance) |
| **`Sts_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Motor is Out of Service |
| **`Sts_BadFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Motor reports both fast and slow (fwd and rev) feedback |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Device Not Ready, see detail bits for reason |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi thiết bị ngắt tải (yêu cầu nhấn Reset) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Khóa liên động không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi truyền thông I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Thiết bị đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Nrdy_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Tripped (at device or by command) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=An Alarm is Disabled or Shelved, display icon |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình (kiểm tra các bit Err_* chi tiết để biết nguyên nhân) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Trip Check timer preset (use 0 to 2147483) |
| **`Err_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thời gian phản hồi mô phỏng (Cfg_SimFdbkT/SimRampT) ngoài phạm vi 0..2147483 giây |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thông số cảnh báo (Thời gian duy trì tối thiểu, thời gian Shelve, mức Severity) không hợp lệ |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc lỗi ngắt tải (Shed) đã hết điều kiện lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_TripFail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Motor Failed to Trip(did not stop) |
| **`Alm_TripFail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Motor Failed to Trip(did not stop) Alarm |
| **`Ack_TripFail`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Fail to Trip Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_TripFailDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Trip Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_TripFailShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Trip Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_TripFailSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Trip Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Motor Tripped by an Interlock NOT OK |
| **`Alm_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Alarm: Motor Tripped by an Interlock NOT OK |
| **`Ack_IntlkTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Interlock Trip Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IntlkTripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock Trip Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IntlkTripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock Trip Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IntlkTripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock Trip Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] I/O Comm Fault Status (0 = OK, 1 = Bad) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = I/O Fault Alarm |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = I/O Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`P_MotorHO`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 27 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_MotorHO`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'Hand Operated Motor'` | [Biến cấu hình thông số] Description for display on HMI. This string is shown in the title bar of the faceplate. |
| **`Cfg_FastFwdText`** | `STRING_16` | `'Running'` | [Biến cấu hình thông số] Text for Running, Fast or Forward for display on HMI |
| **`Cfg_Label`** | `STRING_20` | `'Motor Monitor'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI. This string appears on the graphic symbol. |
| **`Cfg_SlowRevText`** | `STRING_16` | `'Slow'` | [Biến cấu hình thông số] Text for Slow or Reverse for display on HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_MotorHO'` | [Biến cấu hình thông số] Tagname for display on HMI. This string is shown in the title bar of the faceplate. |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_MotorHO'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | Interlock Trip Alarm |
| **`IOFault`** | `P_Alarm` | `{...}` | I/O Fault Alarm |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bypass all Bypassable Interlocks and Permissives |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Check (not bypass) all Interlocks and Permissives |
| **`MCmd_IS`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để place motor In Service |
| **`MCmd_OoS`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để place motor Out of Service |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_IS`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_IS (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_OoS`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_OoS (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi, plus Xác nhận (Acknowledge) alarms |
| **`OCmd_Trip`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Trip Motor |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Trip`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Trip (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`TripFail`** | `P_Alarm` | `{...}` | Fail to Trip (motor did not stop) Alarm |
| **`Val_Notify`** | `SINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_D4SD`
**Thuộc chương:** Chương 3: Động cơ, Biến tần & Rơ-le Quá tải (Motors & Drives)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Motors 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_D4SD`:** **112** Biến đầu vào (`Input Parameters`) | **79** Biến đầu ra (`Output Parameters`) | **33** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_D4SD`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_FdbkA`, `Inp_FdbkB`, `Inp_FdbkC`, `Inp_FdbkD`, `Inp_DeviceFault`, `Inp_PermOK0`, `Inp_NBPermOK0`, `Inp_PermOK1`, `Inp_NBPermOK1`, `Inp_PermOK2`, `Inp_NBPermOK2`, `Inp_PermOK3`, `Inp_NBPermOK3`, `Inp_IntlkOK`, `Inp_NBIntlkOK`, `Inp_IOFault`, `Inp_Sim`, `Inp_Hand`...
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_St0onShed`, `Cfg_NumStates`, `Cfg_OutSt0Write`, `Cfg_OutSt0State`, `Cfg_OutSt1Write`, `Cfg_OutSt1State`, `Cfg_OutSt2Write`, `Cfg_OutSt2State`, `Cfg_OutSt3Write`, `Cfg_OutSt3State`, `Cfg_FdbkSt0Check`, `Cfg_FdbkSt0State`, `Cfg_FdbkSt1Check`, `Cfg_FdbkSt1State`, `Cfg_FdbkSt2Check`, `Cfg_FdbkSt2State`, `Cfg_FdbkSt3Check`, `Cfg_FdbkSt3State`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_Label`, `Cfg_St0Text`, `Cfg_St1Text`, `Cfg_St2Text`, `Cfg_St3Text`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_D4SD` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PSet_Owner`, `PCmd_St0`, `PCmd_St1`, `PCmd_St2`, `PCmd_St3`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_FailAck`, `PCmd_FailSuppress`, `PCmd_FailUnsuppress`, `PCmd_FailUnshelve`, `PCmd_DeviceFaultAck`, `PCmd_DeviceFaultSuppress`, `PCmd_DeviceFaultUnsuppress`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Out_Horn`, `Out_A`, `Out_B`, `Out_C`, `Out_D`, `Val_Cmd`, `Val_Sts`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Val_Owner`, `Sts_St0`, `Sts_St1`, `Sts_St2`, `Sts_St3`, `Sts_Moving`, `Sts_Available`, `Sts_Bypass`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 112 Biến Đầu Vào (`Input Parameters`) của `P_D4SD`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_FdbkA`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Feedback input signals from device. Use the feedback configuration parameters (for example, Cfg_FdbkSt0Check and Cfg_FdbkSt0State) to determine how these inputs are used to determine actual state. |
| **`Inp_FdbkB`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Feedback input signals from device. Use the feedback configuration parameters (for example, Cfg_FdbkSt0Check and Cfg_FdbkSt0State) to determine how these inputs are used to determine actual state. |
| **`Inp_FdbkC`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Feedback input signals from device. Use the feedback configuration parameters (for example, Cfg_FdbkSt0Check and Cfg_FdbkSt0State) to determine how these inputs are used to determine actual state. |
| **`Inp_FdbkD`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Feedback input signals from device. Use the feedback configuration parameters (for example, Cfg_FdbkSt0Check and Cfg_FdbkSt0State) to determine how these inputs are used to determine actual state. |
| **`Inp_DeviceFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Input Signal: Device fault from device. 1 = fault |
| **`Inp_PermOK0`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Permissives OK, device can be commanded to State 0 |
| **`Inp_NBPermOK0`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Non-Bypassable Permissives OK, device can be commanded to State 0 |
| **`Inp_PermOK1`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Permissives OK, device can be commanded to State 1 |
| **`Inp_NBPermOK1`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Non-Bypassable Permissives OK, device can be commanded to State 1 |
| **`Inp_PermOK2`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Permissives OK, device can be commanded to State 2 |
| **`Inp_NBPermOK2`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Non-Bypassable Permissives OK, device can be commanded to State 2 |
| **`Inp_PermOK3`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Permissives OK, device can be commanded to State 3 |
| **`Inp_NBPermOK3`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Non-Bypassable Permissives OK, device can be commanded to State 3 |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Interlocks OK, 0 = Not OK, go to State 0 |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Non-Bypassable Interlocks OK, 0 = Not OK, go to State 0 |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Input Communication Status 0 = OK, 1 = fail |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Simulation input. When set to 1, the instruction simulates a working device while keeping outputs de-energized. When set to 0, the instruction controls the device normally. |
| **`Inp_Hand`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Acquire Hand (typ. hardwired local), 0 = Release Hand |
| **`Inp_Ovrd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Acquire Override (higher priority program logic), 0 = Release Override |
| **`Inp_OvrdCmd`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Override mode device command: 0 = None 1 = State 0 2 = State 1 3 = State 2 4 = State 3 |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Cấm nguồn lệnh ngoài (External) chiếm quyền điều khiển; 0 = Cho phép External chiếm quyền. |
| **`Inp_HornInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Cấm còi báo động âm thanh; 0 = Cho phép phát còi báo trước khi khởi động |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Reset all fault conditions and latched Alarms |
| **`Cfg_St0onShed`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Go to State 0 on Shed, 0 = Hold position on Shed |
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
| **`Cfg_OperSt0Prio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = OCmd_St0 has priority, accepted any time 0 = OCmd_St0 only in Operator |
| **`Cfg_ExtSt0Prio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = XCmd_St0 any time, 0 = XCmd_St0 only when Ext selected |
| **`Cfg_OCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = New device OCmd resets shed latches and cleared alarms, 0 = OCmdReset req'd. |
| **`Cfg_XCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = New device XCmd resets shed latches and cleared alarms, 0 = XCmdReset req'd. |
| **`Cfg_OvrdPermIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Chế độ Override bỏ qua các liên động/điều kiện cho phép có thể bypass; 0 = Luôn tuân thủ mọi liên động. |
| **`Cfg_ShedOnFail`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Go to State 0 and Alarm on Fail to reach position; 0 = Alarm only on Fail |
| **`Cfg_ShedOnDeviceFault`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Go to State 0 and Alarm on Device Fault; 0 = Alarm only on Device Fault |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Go to State 0 and Alarm on I/O Fault; 0 = Alarm only on I/O Fault |
| **`Cfg_HasFailAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Fail to reach state Alarm exists and will be checked |
| **`Cfg_HasDeviceFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Device Fault Alarm exists and will be checked |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Interlock Trip Alarm exists and will be checked |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = I/O Fault Alarm exists and will be checked |
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
| **`Cfg_OutAPulseT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Time to pulse Output A (seconds) (0 = output held continuously) |
| **`Cfg_OutBPulseT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Time to pulse Output B (seconds) (0 = output held continuously) |
| **`Cfg_OutCPulseT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Time to pulse Output C (seconds) (0 = output held continuously) |
| **`Cfg_OutDPulseT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Time to pulse Output D (seconds) (0 = output held continuously) |
| **`Cfg_SimFdbkT`** | `REAL` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Delay to echo back reaching state when in Simulation (seconds) |
| **`Cfg_FailT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Time after new state requested to reach that state before Fault (seconds) |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Mã định danh chủ sở hữu chương trình (Program Owner ID: khác 0 để đăng ký chiếm quyền, bằng 0 để nhả quyền). |
| **`PCmd_St0`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để set device to State 0 |
| **`PCmd_St1`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để set device to State 1 |
| **`PCmd_St2`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để set device to State 2 |
| **`PCmd_St3`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để set device to State 3 |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để chuyển quyền điều khiển sang Tự động (từ Operator sang Program) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để chuyển quyền điều khiển sang Bằng tay (từ Program sang Operator) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa cứng chế độ Program (không cho phép Operator giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Mở khóa chế độ Program (cho phép Operator lấy quyền điều khiển) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để trả về nguồn lệnh Bình thường mặc định (Normal: Program hoặc Operator) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để ResetShed Latches and Cleared Alarms |
| **`PCmd_FailAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Fail to Reach Target Position Alarm |
| **`PCmd_FailSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Fail to Reach Target Position Alarm |
| **`PCmd_FailUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Fail to Reach Target Position Alarm |
| **`PCmd_FailUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Fail to Reach Target Position Alarm |
| **`PCmd_DeviceFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Device Fault Alarm |
| **`PCmd_DeviceFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Device Fault Alarm |
| **`PCmd_DeviceFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Device Fault Alarm |
| **`PCmd_DeviceFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Device Fault Alarm |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Interlock Trip Alarm |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Interlock Trip Alarm |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Interlock Trip Alarm |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Interlock Trip Alarm |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) I/O Fault Alarm |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) I/O Fault Alarm |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) I/O Fault Alarm |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) I/O Fault Alarm |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Release (Maintenance to Operator/Program/Override) |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Unlock Operator (allow Program to acquire) |
| **`XCmd_St0`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để set device to State 0 |
| **`XCmd_St1`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để set device to State 1 |
| **`XCmd_St2`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để set device to State 2 |
| **`XCmd_St3`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để set device to State 3 |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi |

## 3. Bảng chi tiết 79 Biến Đầu Ra (`Output Parameters`) của `P_D4SD`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Out_Horn`** | `BOOL` | - | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1=Sound audible prior to commanded energize/ state change |
| **`Out_A`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output A to Device |
| **`Out_B`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output B to Device |
| **`Out_C`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output C to Device |
| **`Out_D`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Output D to Device |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final Device state source and quality. GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`Val_Cmd`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device command: 0 = None 1 = State 0 2 = State 1 3 = State 2 4 = State 3 |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device confirmed status: 0 = Powerup/Reset 1 = State 0 2 = State 1 3 = State 2 4 = State 3 9 = Moving 33 = Disabled |
| **`Val_Fault`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device fault status: 0 = None 16 = Position Fail 17 = Device Fault 32 = I/O Fault 34 = Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận của khối này cộng với khối kênh (enum) |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Current Object Owner ID (0 = not owned) |
| **`Sts_St0`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Device commanded to and confirmed in State 0 |
| **`Sts_St1`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Device commanded to and confirmed in State 1 |
| **`Sts_St2`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Device commanded to and confirmed in State 2 |
| **`Sts_St3`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Device commanded to and confirmed in State 3 |
| **`Sts_Moving`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Device not yet confirmed in commended state |
| **`Sts_Available`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Device available for control by automation (Prog) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bypassable Interlocks and Permissives are Bypassed |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock / Permissive Bypassing Active (Bypassed or Maintenance) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Device Not Ready, see detail bits for reason |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_EqpFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: External Equipment Fault (Fault or Shed Requires Reset) |
| **`Nrdy_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi thiết bị ngắt tải (yêu cầu nhấn Reset) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Khóa liên động không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi truyền thông I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Thiết bị đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Nrdy_PrioSt0`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Operator/External Priority State 0 Command Requires Reset |
| **`Nrdy_Perm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Cho phép khởi động không thỏa mãn (Permissive Not OK) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Device has a Maintenance Bypass function active |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=An Alarm is Disabled or Shelved, display icon |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình (kiểm tra các bit Err_* chi tiết để biết nguyên nhân) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Feeback Check timer preset (use 0 to 2147483) |
| **`Err_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thời gian phản hồi mô phỏng (Cfg_SimFdbkT/SimRampT) ngoài phạm vi 0..2147483 giây |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thông số cảnh báo (Thời gian duy trì tối thiểu, thời gian Shelve, mức Severity) không hợp lệ |
| **`Sts_Hand`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Hand đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn OoS, Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_OoS`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Out of Service đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_Maint`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Maintenance đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ovrd, Ext, Prog, Oper) |
| **`Sts_Ovrd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Override đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ext, Prog, Oper) |
| **`Sts_Ext`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = External đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Prog, Oper) |
| **`Sts_Prog`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Program đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_ProgLocked`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Program đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Oper`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_OperLocked`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Selection equals the Normal (Prog or Oper) |
| **`Sts_ExtReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = External request inhibited, cannot Get to External from current state |
| **`Sts_ProgReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Program request inhibited, cannot Get to Program from current state |
| **`Sts_MAcqRcvd`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Maintenance Acquire command received this scan |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc lỗi ngắt tải (Shed) đã hết điều kiện lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Device failed to reach commended state |
| **`Alm_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Alarm: Device failed to reach commended state |
| **`Ack_Fail`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Fail to reach commanded state Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_FailDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to reach commanded state Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_FailShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to reach commanded state Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_FailSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to reach commanded state alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_DeviceFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Device Fault (from Inp_DeviceFault) |
| **`Alm_DeviceFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Device Fault Alarm |
| **`Ack_DeviceFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Device Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_DeviceFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Device Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_DeviceFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Device Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_DeviceFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Device Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Device commanded to State 0 by an Interlock NOT OK (one-shot) |
| **`Alm_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Alarm: Device set to State 0 by an Interlock NOT OK |
| **`Ack_IntlkTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Interlock Trip Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IntlkTripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock Trip Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IntlkTripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock Trip Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IntlkTripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock Trip Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] I/O Comm Fault Status (0 = OK, 1 = Bad) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = I/O Fault Alarm |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = I/O Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
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
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi, plus Xác nhận (Acknowledge) alarms |
| **`OCmd_St0`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để set device to State 0 |
| **`OCmd_St1`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để set device to State 1 |
| **`OCmd_St2`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để set device to State 2 |
| **`OCmd_St3`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để set device to State 3 |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = At least one Alarm or latched Shed requires Reset |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = At least one Alarm or latched Shed requires Reset or Ack |
| **`ORdy_St0`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_St0, enable HMI button |
| **`ORdy_St1`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_St1, enable HMI button |
| **`ORdy_St2`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_St2, enable HMI button |
| **`ORdy_St3`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_St3, enable HMI button |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_PF52x`
**Thuộc chương:** Chương 3: Động cơ, Biến tần & Rơ-le Quá tải (Motors & Drives)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Motors 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_PF52x`:** **121** Biến đầu vào (`Input Parameters`) | **118** Biến đầu ra (`Output Parameters`) | **43** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_PF52x`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp`, `Out`, `Ref_FaultCodeList`, `Inp_FwdPermOK`, `Inp_FwdNBPermOK`, `Inp_RevPermOK`, `Inp_RevNBPermOK`, `Inp_IntlkOK`, `Inp_NBIntlkOK`, `Inp_IOFault`, `Inp_Sim`, `Inp_Hand`, `Inp_Ovrd`, `Inp_OvrdCmd`, `Inp_OvrdSpeed`, `Inp_ExtInh`, `Inp_HornInh`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_HasReverse`, `Cfg_HasJog`, `Cfg_AllowLocal`, `Cfg_HasFwdPermObj`, `Cfg_HasRevPermObj`, `Cfg_HasIntlkObj`, `Cfg_HasResInhObj`, `Cfg_HasRunTimeObj`, `Cfg_HasMoreObj`, `Cfg_SetTrack`, `Cfg_SetTrackOvrdHand`, `Cfg_OperStopPrio`, `Cfg_ExtStopPrio`, `Cfg_OCmdResets`, `Cfg_XCmdResets`, `Cfg_OvrdPermIntlk`, `Cfg_ShedOnFailToStart`, `Cfg_ShedOnIOFault`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_DigIn1Txt`, `Cfg_DigIn2Txt`, `Cfg_DigIn3Txt`, `Cfg_DigIn4Txt`, `Cfg_FwdText`, `Cfg_Label`, `Cfg_RevText`, `Cfg_SpeedFdbkEU`, `Cfg_SpeedRefEU`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_PF52x` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PSet_SpeedRef`, `PSet_Owner`, `PCmd_StartFwd`, `PCmd_StartRev`, `PCmd_Stop`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_FailToStartAck`, `PCmd_FailToStartSuppress`, `PCmd_FailToStartUnsuppress`, `PCmd_FailToStartUnshelve`, `PCmd_FailToStopAck`, `PCmd_FailToStopSuppress`, `PCmd_FailToStopUnsuppress`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Inp`, `Out`, `Ref_FaultCodeList`, `Out_Horn`, `Val_SpeedRef`, `Val_SpeedFdbk`, `Val_SpeedRefRaw`, `Val_SpeedFdbkRaw`, `Val_SpeedRefEUMin`, `Val_SpeedRefEUMax`, `Val_SpeedFdbkEUMin`, `Val_SpeedFdbkEUMax`, `Val_OutCurrent`, `Val_OutPower`, `Val_LastFaultCode`, `Val_Cmd`, `Val_Fdbk`, `Val_Sts`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 121 Biến Đầu Vào (`Input Parameters`) của `P_PF52x`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp`** | `P_PF52x_Inp` | - | Hiện (Pin) | Common part of PF523 / PF525 Input Assembly |
| **`Out`** | `P_PF52x_Out` | - | Hiện (Pin) | Common part of PF523 / PF525 Output Assembly |
| **`Ref_FaultCodeList`** | `P_DescList[2]` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] Tag containing List of Fault Codes (DINT) and their Descriptions (STRING) |
| **`Inp_FwdPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Permissives OK, drive can start Forward |
| **`Inp_FwdNBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Non-Bypassable Permissives OK, drive can start Forward |
| **`Inp_RevPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Permissives OK, drive can start Reverse |
| **`Inp_RevNBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Non-Bypassable Permissives OK, motor can start Reverse |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Interlocks OK, drive can start/run |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Non-Bypassable Interlocks OK, drive can start/run |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Input Communication Status 0 = OK, 1 = fail |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Simulation input. When set to 1, the instruction keeps outputs de-energized (zero) and simulates a working drive. When set to 0, the instruction operates the drive normally. |
| **`Inp_Hand`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Acquire Hand (typ. hardwired local) Mode, 0 = Release Hand Mode |
| **`Inp_Ovrd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Acquire Override (higher priority program logic), 0 = Release Override |
| **`Inp_OvrdCmd`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Override Mode Command: 0 = None, 1 = Stop, 2=Start Fwd 3=Start Rev |
| **`Inp_OvrdSpeed`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Value to set Speed Reference in Override Mode (SpeedRef Engineering Units) |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Cấm nguồn lệnh ngoài (External) chiếm quyền điều khiển; 0 = Cho phép External chiếm quyền. |
| **`Inp_HornInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Cấm còi báo động âm thanh; 0 = Cho phép phát còi báo trước khi khởi động |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Reset Shed Latches, Cleared Alarms, Drive Fault |
| **`Cfg_HasReverse`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Drive can be run reverse, 0 = Forward only |
| **`Cfg_HasJog`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Drive Jog Command enabled/visible, 0 = Drive Jog Command not allowed |
| **`Cfg_AllowLocal`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Allow Local Start/Stop without alarm, 0 = Start/Stop from HMI/prog only |
| **`Cfg_HasFwdPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng forward permissive liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasRevPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng reverse permissive liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng interlock liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasResInhObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng restart inhibit liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasRunTimeObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng runtime liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_SetTrack`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] This parameter is used to set up bumpless behavior of setting parameters when switching modes. When this parameter is 1: • In Program mode the operator settings track the program settings. • In Operator mode the program settings track the operator settings. • The simulation inputs match the output values (transitions are bumpless). When this parameter is 0, the operator settings and program settings are not modified by this instruction. In this case, when the mode is changed, the effective value of the setting can change depending on the program-set and operator-set values. |
| **`Cfg_SetTrackOvrdHand`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Program/Operator Settings track Override/Hand speed reference |
| **`Cfg_OperStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = OCmd_Stop any time, 0 = OCmd_Stop only when Operator selected |
| **`Cfg_ExtStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = XCmd_Stop any time, 0 = XCmd_Stop only when Ext selected |
| **`Cfg_OCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = New drive OCmd resets shed latches and cleared alarms, 0 = OCmdReset req'd. |
| **`Cfg_XCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = New drive XCmd resets shed latches and cleared alarms, 0 = XCmdReset req'd. |
| **`Cfg_OvrdPermIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Chế độ Override bỏ qua các liên động/điều kiện cho phép có thể bypass; 0 = Luôn tuân thủ mọi liên động. |
| **`Cfg_ShedOnFailToStart`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Stop motor and alarm on Fail to Start. 0 = Alarm only on Fail to Start. IMPORTANT: If a condition is configured to shed the device to the Stopped state on a fault, a reset is required to clear the shed fault. The drive can now be commanded to a state other than Stopped. |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Stop motor and alarm on I/O Fault. 0 = Alarm only on I/O Fault. IMPORTANT: If a condition is configured to shed the device to the Stopped state on a fault, a reset is required to clear the shed fault. The drive can now be commanded to a state other than Stopped. |
| **`Cfg_SimScaleEU`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] If Cfg_SimScaleEU = 1, in simulation, scale Speed Ref engineering units to Speed feedback engineering units. If Cfg_SimScaleRaw = 1, in simulation, scale Speed Ref engineering units to raw, then raw to Speed feedback engineering units. If both of these parameters are zero, the simulated speed reference is ramped to the simulated speed feedback without scaling. |
| **`Cfg_SimScaleRaw`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] If Cfg_SimScaleEU = 1, in simulation, scale Speed Ref engineering units to Speed feedback engineering units. If Cfg_SimScaleRaw = 1, in simulation, scale Speed Ref engineering units to raw, then raw to Speed feedback engineering units. If both of these parameters are zero, the simulated speed reference is ramped to the simulated speed feedback without scaling. |
| **`Cfg_DecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Number of decimal places for speed ref/fdbk display (0..6) |
| **`Cfg_HasFailToStartAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasFailToStopAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasDriveFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_FailToStartResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_FailToStopResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_DriveFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_FailToStartAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_FailToStopAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_DriveFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_FailToStartSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_FailToStopSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_DriveFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_MinSpdRef`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum Speed Reference in Engineering Units (for limiting) |
| **`Cfg_MaxSpdRef`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Maximum Speed Reference in Engineering Units (for limiting) |
| **`Cfg_SpeedRefRawMin`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Reference Minimum in Drive (raw) Units (for scaling) |
| **`Cfg_SpeedRefRawMax`** | `DINT` | `6000` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Reference Maximum in Drive (raw) Units (for scaling) |
| **`Cfg_SpeedRefEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Reference Minimum in Engineering Units (for scaling) |
| **`Cfg_SpeedRefEUMax`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Reference Maximum in Engineering Units (for scaling) |
| **`Cfg_SpeedFdbkRawMin`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Feedback Minimum in Drive (raw) Units (for scaling) |
| **`Cfg_SpeedFdbkRawMax`** | `DINT` | `6000` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Feedback Maximum in Drive (raw) Units (for scaling) |
| **`Cfg_SpeedFdbkEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Feedback Minimum in Engineering Units (for scaling) |
| **`Cfg_SpeedFdbkEUMax`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Feedback Maximum in Engineering Units (for scaling) |
| **`Cfg_StartHornT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian hú còi cảnh báo tại hiện trường trước khi đóng điện khởi động động cơ (giây, 0 = tắt còi). |
| **`Cfg_SimRampT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Time to ramp speed fdbk when in Simulation (seconds) |
| **`Cfg_FailToStartT`** | `REAL` | `15` | Ẩn (Tag) | [Biến cấu hình thông số] Time after Start to get Run Feedback before Fault (seconds) |
| **`Cfg_FailToStopT`** | `REAL` | `15` | Ẩn (Tag) | [Biến cấu hình thông số] Time after Stop to drop Run Feedback before Fault (seconds) |
| **`Cfg_ResetPulseT`** | `REAL` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Time to pulse Out_Reset to clear drive fault |
| **`Cfg_MaxJogT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian giữ lệnh nhấp thử (Jog) tối đa cho phép (giây, 0 = không giới hạn). |
| **`Cfg_OperKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Operator keeps control in Program or Ext: .0 = Reference, .1 = Commands |
| **`Cfg_ProgKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Program keeps control in Operator or Ext: .0 = Reference, .1 = Commands |
| **`Cfg_ExtKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Ext keeps control in Program or Oper: .0 = Reference, .1 = Commands |
| **`PSet_SpeedRef`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Program Setting of Speed Reference (Engineering Units) |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Mã định danh chủ sở hữu chương trình (Program Owner ID: khác 0 để đăng ký chiếm quyền, bằng 0 để nhả quyền). |
| **`XSet_SpeedRef`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài (External)] External Setting of Speed Reference (Engineering Units) |
| **`PCmd_StartFwd`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Start Drive Forward |
| **`PCmd_StartRev`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Start Drive Reverse |
| **`PCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Stop Drive |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để chuyển quyền điều khiển sang Tự động (từ Operator sang Program) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để chuyển quyền điều khiển sang Bằng tay (từ Program sang Operator) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa cứng chế độ Program (không cho phép Operator giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Mở khóa chế độ Program (cho phép Operator lấy quyền điều khiển) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để trả về nguồn lệnh Bình thường mặc định (Normal: Program hoặc Operator) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Reset Shed Latches, Cleared Alarms, Drive Fault |
| **`PCmd_FailToStartAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Fail to Start Alarm |
| **`PCmd_FailToStartSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Fail to Start Alarm |
| **`PCmd_FailToStartUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Fail to Start Alarm |
| **`PCmd_FailToStartUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Fail to Start Alarm |
| **`PCmd_FailToStopAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Fail to Stop Alarm |
| **`PCmd_FailToStopSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Fail to Stop Alarm |
| **`PCmd_FailToStopUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Fail to Stop Alarm |
| **`PCmd_FailToStopUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Fail to Stop Alarm |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Interlock Trip Alarm |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Interlock Trip Alarm |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Interlock Trip Alarm |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Interlock Trip Alarm |
| **`PCmd_DriveFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Drive Fault Alarm |
| **`PCmd_DriveFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Drive Fault Alarm |
| **`PCmd_DriveFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Drive Fault Alarm |
| **`PCmd_DriveFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Drive Fault Alarm |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) I/O Fault Alarm |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) I/O Fault Alarm |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) I/O Fault Alarm |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) I/O Fault Alarm |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Release (Maintenance to Operator/Program/Override) |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Unlock Operator (allow Program to acquire) |
| **`XCmd_StartFwd`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Start Drive Forward |
| **`XCmd_StartRev`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Start Drive Reverse |
| **`XCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Stop Drive |
| **`XCmd_JogFwd`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Jog Drive Forward |
| **`XCmd_JogRev`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Jog Drive Reverse |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Acquire (Oper/Prog to Ext) |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Release (Ext to Oper/Prog) |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Reset Shed Latches, Cleared Alarms, Drive Fault |

## 3. Bảng chi tiết 118 Biến Đầu Ra (`Output Parameters`) của `P_PF52x`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Inp`** | `P_PF52x_Inp` | - | Hiện (Pin) | Common part of PF523 / PF525 Input Assembly |
| **`Out`** | `P_PF52x_Out` | - | Hiện (Pin) | Common part of PF523 / PF525 Output Assembly |
| **`Ref_FaultCodeList`** | `P_DescList[2]` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] Tag containing List of Fault Codes (DINT) and their Descriptions (STRING) |
| **`Out_Horn`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1 = Sound audible prior to commanded motor start |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final drive status source and quality: GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`Val_SpeedRef`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Speed Reference (target) to drive |
| **`Val_SpeedFdbk`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Speed Feedback (actual) from drive |
| **`Val_SpeedRefRaw`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Copy of Speed Reference Output (in Raw units) for faceplate |
| **`Val_SpeedFdbkRaw`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Copy of Speed Feedback Input (in Raw units) for faceplate |
| **`Val_SpeedRefEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Minimum of Speed Reference = MIN (Cfg_SpeedFdbkEUMin, Cfg_SpeedFdbkEUMax) |
| **`Val_SpeedRefEUMax`** | `REAL` | `130` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Maximum of Speed Reference = MAX (Cfg_SpeedFdbkEUMin, Cfg_SpeedFdbkEUMax) |
| **`Val_SpeedFdbkEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Minimum of Speed Feedback = MIN (Cfg_SpeedFdbkEUMin, Cfg_SpeedFdbkEUMax) |
| **`Val_SpeedFdbkEUMax`** | `REAL` | `130` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Maximum of Speed Feedback = MAX (Cfg_SpeedFdbkEUMin, Cfg_SpeedFdbkEUMax) |
| **`Val_OutCurrent`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Drive output current (Amps) |
| **`Val_OutPower`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Drive output power (kW) |
| **`Val_LastFaultCode`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Last drive fault code (enum) (Par 951) |
| **`Val_Cmd`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Command 0 = None, 1 = Stop, 2=StartFwd 3=StartRev 4=JogFwd 5=JogRev |
| **`Val_Fdbk`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Feedback 0 = Stopped, 1 = Running Fwd 2=Running Rev 3=Accelerating 4=Decelerating |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Sts: 0 = ? 1 = Stopped 2=RunFwd 3=RunRev 4=RunSlow 5=StartFwd 6=StartRev 7=StartSlow 8=JogFwd 9=JogRev 10 = Stopping |
| **`Val_Fault`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Fault Status 0 = None, 16=Fail to Start, 17 = Fail to Stop, 18=Drive Fault, 32=I/O Fault, 34=Config Error |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận của khối này cộng với khối kênh (enum) |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Current Object Owner ID (0 = not owned) |
| **`Sts_Stopped`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive requested to stop and is confirmed stopped |
| **`Sts_StartingFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive requested to run forward and awaiting run feedback |
| **`Sts_StartingRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive requested to run reverse and awaiting run feedback |
| **`Sts_RunningFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive requested to run and is confirmed running forward |
| **`Sts_RunningRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive requested to run and is confirmed running reverse |
| **`Sts_StoppingFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive running forward requested to stop and awaiting stopped feedback |
| **`Sts_StoppingRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive running reverse requested to stop and awaiting stopped feedback |
| **`Sts_JoggingFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive requested to Jog Forward |
| **`Sts_JoggingRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive requested to Jog Reverse |
| **`Sts_Horn`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Valve Horn Active |
| **`Sts_CommandDir`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive commanded to Forward, 0 = Reverse |
| **`Sts_ActualDir`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive Actual direction is Forward, 0 = Reverse |
| **`Sts_Accel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive is Accelerating |
| **`Sts_Decel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive is Decelerating |
| **`Sts_NotReady`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Device Not Ready, see detail bits for reason |
| **`Sts_AtSpeed`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive is running at reference speed |
| **`Sts_SpeedLimited`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Speed Reference Setting exceeds configured Max/Min limit |
| **`Sts_DigIn1`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Drive Digital Input 1 Status |
| **`Sts_DigIn2`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Drive Digital Input 2 Status |
| **`Sts_DigIn3`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Drive Digital Input 3 Status |
| **`Sts_DigIn4`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Drive Digital Input 4 Status |
| **`Sts_Available`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive available for control by automation (Prog) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bypassable Interlocks and Permissives are Bypassed |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bypassing Active (Bypassed or Maintenance) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Motor is Not Ready to Run (independent of Mode) |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_DriveNR`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Device Note Ready: Drive Not Ready |
| **`Nrdy_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi thiết bị ngắt tải (yêu cầu nhấn Reset) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Khóa liên động không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi truyền thông I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Thiết bị đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Nrdy_Perm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Cho phép khởi động không thỏa mãn (Permissive Not OK) |
| **`Nrdy_PrioStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang có lệnh Dừng ưu tiên từ Operator/External (yêu cầu nhấn Reset) |
| **`Nrdy_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Device not Ready: Deivce Tripped (Drive Fault) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=An Alarm is Disabled or Shelved, display icon |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình (kiểm tra các bit Err_* chi tiết để biết nguyên nhân) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Invalid Check or Reset Pulse Time (use 0 to 2147483) |
| **`Err_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: simulation timer preset: use 0 to 2147483) |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thông số cảnh báo (Thời gian duy trì tối thiểu, thời gian Shelve, mức Severity) không hợp lệ |
| **`Err_FdbkRaw`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Erron in Config: Speed Fdbk Raw Min = Max |
| **`Err_FdbkEU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Speed Fdbk Engineering Units Min = Max |
| **`Err_RefLim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Speed Ref Limit Min > Max |
| **`Err_RefEU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Speed Ref Engineering Units Min = Max |
| **`Err_RefRaw`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Speed Ref Raw Min = Max |
| **`Sts_Hand`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Hand đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn OoS, Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_OoS`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Out of Service đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_Maint`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Maintenance đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ovrd, Ext, Prog, Oper) |
| **`Sts_Ovrd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Override đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ext, Prog, Oper) |
| **`Sts_Ext`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = External đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Prog, Oper) |
| **`Sts_Prog`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Program đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_ProgLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Program đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Oper`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_OperLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Selection equals the Normal (Prog or Oper) |
| **`Sts_ExtReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = External request inhibited, cannot Get to External from current state |
| **`Sts_ProgReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Program request inhibited, cannot Get to Program from current state |
| **`Sts_MAcqRcvd`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Maintenance Acquire command received this scan |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = The drive, or a latched alarm or shed condition, is ready to be reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive Failed to Start (One-Shot) |
| **`Alm_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Drive Fail to Start Alarm |
| **`Ack_FailToStart`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Fail to Start Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_FailToStartDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Start Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_FailToStartShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Start Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_FailToStartSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Start Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive Failed to Stop |
| **`Alm_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Drive Fail to Stop Alarm |
| **`Ack_FailToStop`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Fail to Stop Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_FailToStopDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Stop Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_FailToStopShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Stop Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_FailToStopSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Stop Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive was stopped by an Interlock NOT OK (One-Shot) |
| **`Alm_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Alarm: Drive stopped by an Interlock NOT OK |
| **`Ack_IntlkTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Interlock Trip Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IntlkTripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock Trip Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IntlkTripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock Trip Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IntlkTripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock Trip Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_DriveFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive Fault (see drive display or manual) |
| **`Alm_DriveFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Alarm: Drive Fault (see drive display or manual) |
| **`Ack_DriveFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Drive Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_DriveFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_DriveFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_DriveFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] I/O Comm Fault Status (0 = OK, 1 = Bad) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = I/O Fault Alarm |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = I/O Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`MRdy_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`P_PF52x`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 43 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_PF52x`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'PF523 / PF525 Variable Frequency Drive'` | [Biến cấu hình thông số] Description for display on HMI. This string is shown in the title bar of the faceplate. |
| **`Cfg_DigIn1Txt`** | `STRING_20` | `'Digital Input 1'` | [Biến cấu hình thông số] Drive Digital Input 1 Label |
| **`Cfg_DigIn2Txt`** | `STRING_20` | `'Digital Input 2'` | [Biến cấu hình thông số] Drive Digital Input 2 Label |
| **`Cfg_DigIn3Txt`** | `STRING_20` | `'Digital Input 3'` | [Biến cấu hình thông số] Drive Digital Input 3 Label |
| **`Cfg_DigIn4Txt`** | `STRING_20` | `'Digital Input 4'` | [Biến cấu hình thông số] Drive Digital Input 4 Label |
| **`Cfg_FwdText`** | `STRING_16` | `'Forward'` | [Biến cấu hình thông số] Name for Forward Direction, e.g., "Up", "Forward" |
| **`Cfg_Label`** | `STRING_20` | `'Motor Speed Control'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI. This string appears on the graphic symbol. |
| **`Cfg_RevText`** | `STRING_16` | `'Reverse'` | [Biến cấu hình thông số] Name for Reverse Direction, e.g., "Down", "Reverse" |
| **`Cfg_SpeedFdbkEU`** | `STRING_8` | `'Hz'` | [Biến cấu hình thông số] Speed Feedback Engineering Units for display on HMI |
| **`Cfg_SpeedRefEU`** | `STRING_8` | `'Hz'` | [Biến cấu hình thông số] Speed Reference Engineering Units for display on HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_PF52x'` | [Biến cấu hình thông số] Tagname for display on HMI. This string is shown in the title bar of the faceplate. |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc quản lý lựa chọn nguồn lệnh điều khiển |
| **`DriveFault`** | `P_Alarm` | `{...}` | Drive Faulted Alarm |
| **`FailToStart`** | `P_Alarm` | `{...}` | Drive Failed to Start Alarm |
| **`FailToStop`** | `P_Alarm` | `{...}` | Drive Failed to Stop Alarm |
| **`HMI_Lib`** | `STRING_12` | `??` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `??` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `??` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | Interlock Trip Alarm |
| **`IOFault`** | `P_Alarm` | `{...}` | I/O Fault Alarm |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bypass all Bypassable Interlocks and Permissives |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Check (not bypass) all Interlocks and Permissives |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`OCmd_JogFwd`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Jog Drive Forward (not cleared by instruction!) |
| **`OCmd_JogRev`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Jog Drive Reverse (not cleared by instruction!) |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset Shed Latches, Cleared Alarms, Drive Fault |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset Shed Latches, Cleared Alarms, Drive Fault, and Xác nhận (Acknowledge) Alarms |
| **`OCmd_StartFwd`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Start Drive Forward |
| **`OCmd_StartRev`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Start Drive Reverse |
| **`OCmd_Stop`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Stop Drive |
| **`ORdy_JogFwd`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_JogFwd (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_JogRev`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_JogRev (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_SpeedRef`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OSet_SpeedRef (enables data entry field) |
| **`ORdy_StartFwd`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_StartFwd (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_StartRev`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_StartRev (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Stop`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Stop (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`OSet_SpeedRef`** | `REAL` | `0` | [Giá trị đặt từ màn hình HMI Operator] Operator Setting of Speed Reference (Engineering Units) |
| **`Val_FaultDesc`** | `STRING_40` | `‘Logic not yet scanned.’` | [Giá trị thực tế đầu ra (Logic/HMI)] Description of Drive Fault (given LastFaultCode) |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_PF753`
**Thuộc chương:** Chương 3: Động cơ, Biến tần & Rơ-le Quá tải (Motors & Drives)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Motors 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_PF753`:** **123** Biến đầu vào (`Input Parameters`) | **122** Biến đầu ra (`Output Parameters`) | **40** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_PF753`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp`, `Out`, `Ref_GetFaultMSG`, `Ref_GetFaultDest`, `Inp_FwdPermOK`, `Inp_FwdNBPermOK`, `Inp_RevPermOK`, `Inp_RevNBPermOK`, `Inp_IntlkOK`, `Inp_NBIntlkOK`, `Inp_IntlkAvail`, `Inp_IOFault`, `Inp_Sim`, `Inp_Hand`, `Inp_Ovrd`, `Inp_OvrdCmd`, `Inp_OvrdSpeed`, `Inp_ExtInh`...
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_HasReverse`, `Cfg_HasJog`, `Cfg_AllowLocal`, `Cfg_HasFwdPermObj`, `Cfg_HasRevPermObj`, `Cfg_HasIntlkObj`, `Cfg_HasMoreObj`, `Cfg_HasResInhObj`, `Cfg_HasRunTimeObj`, `Cfg_SetTrack`, `Cfg_SetTrackOvrdHand`, `Cfg_OperStopPrio`, `Cfg_ExtStopPrio`, `Cfg_OCmdResets`, `Cfg_XCmdResets`, `Cfg_OvrdPermIntlk`, `Cfg_ShedOnFailToStart`, `Cfg_ShedOnIOFault`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_FwdText`, `Cfg_Label`, `Cfg_RevText`, `Cfg_SpeedFdbkEU`, `Cfg_SpeedRefEU`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_PF753` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PSet_SpeedRef`, `PSet_Owner`, `PCmd_StartFwd`, `PCmd_StartRev`, `PCmd_Stop`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_FailToStartAck`, `PCmd_FailToStartSuppress`, `PCmd_FailToStartUnsuppress`, `PCmd_FailToStartUnshelve`, `PCmd_FailToStopAck`, `PCmd_FailToStopSuppress`, `PCmd_FailToStopUnsuppress`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Inp`, `Out`, `Ref_GetFaultMSG`, `Ref_GetFaultDest`, `Out_Horn`, `Val_SpeedRef`, `Val_SpeedFdbk`, `Val_SpeedRefSrc`, `Val_SpeedRefRaw`, `Val_SpeedFdbkRaw`, `Val_SpeedRefEUMin`, `Val_SpeedRefEUMax`, `Val_SpeedFdbkEUMin`, `Val_SpeedFdbkEUMax`, `Val_LastFaultCode`, `Val_Cmd`, `Val_Fdbk`, `Val_Sts`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 123 Biến Đầu Vào (`Input Parameters`) của `P_PF753`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp`** | `P_PF6000_Inp` | - | Hiện (Pin) | Common part of PowerFlex 753 Input Assembly |
| **`Out`** | `P_PF6000_Out` | - | Hiện (Pin) | Common part of PowerFlex 753 Output Assembly |
| **`Ref_GetFaultMSG`** | `MESSAGE` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] MSG to Get Last Fault Record |
| **`Ref_GetFaultDest`** | `P_PFComm_FltAlmRec` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] Buffer for data from Fault Record or Alarm Record MSG |
| **`Inp_FwdPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Permissives OK, drive can start Forward |
| **`Inp_FwdNBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Non-Bypassable Permissives OK, drive can start Forward |
| **`Inp_RevPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Permissives OK, drive can start Reverse |
| **`Inp_RevNBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1=Non-Bypassable Permissives OK, motor can start Reverse |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Interlocks OK, drive can start/run |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Non-Bypassable Interlocks OK, drive can start/run |
| **`Inp_IntlkAvail`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Interlock Availibility OK |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Input Communication Status 0=OK, 1=fail |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Simulation input. When set to 1, the instruction keeps outputs de-energized (zero) and simulates a working drive. When set to 0, the instruction operates the drive normally. |
| **`Inp_Hand`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Acquire Hand (typ. hardwired local) Mode, 0 = Release Hand Mode |
| **`Inp_Ovrd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Acquire Override (higher priority program logic), 0 = Release Override |
| **`Inp_OvrdCmd`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Override Mode Command: 0 = None, 1 = Stop, 2=Start |
| **`Inp_OvrdSpeed`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Value to set Speed Reference in Override Mode (SpeedRef Engineering Units) |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Cấm nguồn lệnh ngoài (External) chiếm quyền điều khiển; 0 = Cho phép External chiếm quyền. |
| **`Inp_HornInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Cấm còi báo động âm thanh; 0 = Cho phép phát còi báo trước khi khởi động |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Reset Shed Latches, Cleared Alarms, Drive Fault |
| **`Cfg_HasReverse`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Drive can be run reverse, 0=Forward only |
| **`Cfg_HasJog`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Drive Jog Command enabled/visible, 0=Drive Jog Command not allowed |
| **`Cfg_AllowLocal`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Allow Local Start/Stop without alarm, 0 = Start/Stop from HMI/prog only |
| **`Cfg_HasFwdPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Tells HMI a P_Perm is connected to Inp_FwdPerm |
| **`Cfg_HasRevPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Tells HMI a P_Perm is connected to Inp_RevPerm |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng interlock liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_HasResInhObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng restart inhibit liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasRunTimeObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng runtime liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_SetTrack`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] This parameter is used to set up bumpless behavior of setting parameters when switching modes. When this parameter is 1, in Program mode the operator settings track the program settings; in Operator mode the program settings track the operator settings; and the simulation inputs match the output values (transitions are bumpless). When this parameter is 0, the operator settings and program settings are not modified by this instruction. In this case, when the mode is changed, the effective value of the setting can change depending on the program-set and operator-set values. |
| **`Cfg_SetTrackOvrdHand`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Program/Operator Settings track Override/Hand speed reference |
| **`Cfg_OperStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = OCmd_Stop any time, 0 = OCmd_Stop only when Operator selected |
| **`Cfg_ExtStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = XCmd_Stop any time, 0 = XCmd_Stop only when Ext selected |
| **`Cfg_OCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = New drive OCmd resets shed latches and cleared alarms, 0 = OCmdReset req'd. |
| **`Cfg_XCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = New drive XCmd resets shed latches and cleared alarms, 0 = XCmdReset req'd. |
| **`Cfg_OvrdPermIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Chế độ Override bỏ qua các liên động/điều kiện cho phép có thể bypass; 0 = Luôn tuân thủ mọi liên động. |
| **`Cfg_ShedOnFailToStart`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Stop motor and alarm on Fail to Start. 0 = Alarm only on Fail to Start. IMPORTANT: If a condition is configured to shed the device to the Stopped state on a fault, a reset is required to clear the shed fault to command the drive to a state other than Stopped. |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Stop motor and alarm on I/O Fault. 0 = Alarm only on I/O Fault. IMPORTANT: If a condition is configured to shed the device to the Stopped state on a fault, a reset is required to clear the shed fault to command the drive to a state other than Stopped. |
| **`Cfg_SimScaleEU`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = In simulation, scale Speed Ref Engineering Units to Speed Fdbk Engineering Units |
| **`Cfg_SimScaleRaw`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = In simulation, scale Speed Ref Engineering Units to raw, then raw to Speed Fdbk Engineering Units |
| **`Cfg_DecPlcs`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Number of decimal places for speed ref/fdbk display (0..6) |
| **`Cfg_HasFailToStartAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasFailToStopAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasDriveFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_FailToStartResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_FailToStopResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_DriveFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_FailToStartAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_FailToStopAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_DriveFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_FailToStartSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_FailToStopSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_DriveFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_MinSpdRef`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum Speed Reference in Engineering Units (for limiting) |
| **`Cfg_MaxSpdRef`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Maximum Speed Reference in Engineering Units (for limiting) |
| **`Cfg_SpeedRefRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Reference Minimum in Drive (raw) Units (for scaling) |
| **`Cfg_SpeedRefRawMax`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Reference Maximum in Drive (raw) Units (for scaling) |
| **`Cfg_SpeedRefEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Reference Minimum in Engineering Units (for scaling) |
| **`Cfg_SpeedRefEUMax`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Reference Maximum in Engineering Units (for scaling) |
| **`Cfg_SpeedFdbkRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Feedback Minimum in Drive (raw) Units (for scaling) |
| **`Cfg_SpeedFdbkRawMax`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Feedback Maximum in Drive (raw) Units (for scaling) |
| **`Cfg_SpeedFdbkEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Feedback Minimum in Engineering Units (for scaling) |
| **`Cfg_SpeedFdbkEUMax`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Feedback Maximum in Engineering Units (for scaling) |
| **`Cfg_StartHornT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian hú còi cảnh báo tại hiện trường trước khi đóng điện khởi động động cơ (giây, 0 = tắt còi). |
| **`Cfg_SimRampT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Time to ramp speed fdbk when in Simulation (seconds) |
| **`Cfg_FailToStartT`** | `REAL` | `15` | Ẩn (Tag) | [Biến cấu hình thông số] Time after Start to get Run Feedback before Fault (seconds) |
| **`Cfg_FailToStopT`** | `REAL` | `15` | Ẩn (Tag) | [Biến cấu hình thông số] Time after Stop to drop Run Feedback before Fault (seconds) |
| **`Cfg_ResetPulseT`** | `REAL` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Time to pulse Out_Reset to clear drive fault |
| **`Cfg_WatchdogT`** | `REAL` | `5` | Ẩn (Tag) | [Biến cấu hình thông số] Communications Watchdog Timeout time (seconds) |
| **`Cfg_OperKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Operator keeps control in Program or Ext: .0 = Reference, .1 = Commands |
| **`Cfg_ProgKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Program keeps control in Operator or Ext: .0 = Reference, .1 = Commands |
| **`Cfg_ExtKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Ext keeps control in Program or Oper: .0 = Reference, .1 = Commands |
| **`PSet_SpeedRef`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Program Setting of Speed Reference (Engineering Units) |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Mã định danh chủ sở hữu chương trình (Program Owner ID: khác 0 để đăng ký chiếm quyền, bằng 0 để nhả quyền). |
| **`XSet_SpeedRef`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài (External)] External Setting of Speed Reference (Engineering Units) |
| **`PCmd_StartFwd`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Start Drive Forward |
| **`PCmd_StartRev`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Start Drive Reverse |
| **`PCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Stop Drive |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để chuyển quyền điều khiển sang Tự động (từ Operator sang Program) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để chuyển quyền điều khiển sang Bằng tay (từ Program sang Operator) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa cứng chế độ Program (không cho phép Operator giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Mở khóa chế độ Program (cho phép Operator lấy quyền điều khiển) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để trả về nguồn lệnh Bình thường mặc định (Normal: Program hoặc Operator) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Reset drive fault and all Alarms requiring Reset |
| **`PCmd_FailToStartAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Fail to Start Alarm |
| **`PCmd_FailToStartSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Fail to Start Alarm |
| **`PCmd_FailToStartUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Fail to Start Alarm |
| **`PCmd_FailToStartUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Fail to Start Alarm |
| **`PCmd_FailToStopAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Fail to Stop Alarm |
| **`PCmd_FailToStopSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Fail to Stop Alarm |
| **`PCmd_FailToStopUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Fail to Stop Alarm |
| **`PCmd_FailToStopUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Fail to Stop Alarm |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Interlock Trip Alarm |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Interlock Trip Alarm |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Interlock Trip Alarm |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Interlock Trip Alarm |
| **`PCmd_DriveFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Drive Fault Alarm |
| **`PCmd_DriveFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Drive Fault Alarm |
| **`PCmd_DriveFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Drive Fault Alarm |
| **`PCmd_DriveFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Drive Fault Alarm |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) I/O Fault Alarm |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) I/O Fault Alarm |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) I/O Fault Alarm |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) I/O Fault Alarm |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Release (Maintenance to Operator/Program/Override) |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Unlock Operator (allow Program to acquire) |
| **`XCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Stop Drive |
| **`XCmd_StartFwd`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Start Drive Forward |
| **`XCmd_StartRev`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Start Drive Reverse |
| **`XCmd_JogFwd`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Jog Drive Forward |
| **`XCmd_JogRev`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Jog Drive Reverse |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Acquire (Oper/Prog to Ext) |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Release (Ext to Oper/Prog) |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Reset Shed Latches, Cleared Alarms, Drive Fault |

## 3. Bảng chi tiết 122 Biến Đầu Ra (`Output Parameters`) của `P_PF753`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Inp`** | `P_PF6000_Inp` | - | Hiện (Pin) | PowerFlex 6000 Input Assembly (<drivename>:I) |
| **`Out`** | `P_PF6000_Out` | - | Hiện (Pin) | PowerFlex 6000 Output Assembly (<drivename>:O) |
| **`Ref_GetFaultMSG`** | `MESSAGE` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] MSG to Get Last Fault Record |
| **`Ref_GetFaultDest`** | `P_PFComm_FltAlmRec` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] Buffer for data from Fault Record or Alarm Record MSG |
| **`Out_Horn`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1 = Sound audible prior to commanded motor start |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final source and quality: GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`Val_SpeedRef`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Speed Reference (target) to drive |
| **`Val_SpeedFdbk`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Speed Feedback (actual) from drive |
| **`Val_SpeedRefSrc`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Speed Reference Source (enum) 1=A, 2=B, 3=Pre3, 4=Pre4, ... 7=Pre7, 16-31=Man Ref Sets |
| **`Val_SpeedRefRaw`** | `INT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Copy of Speed Reference Output (in Raw units) for faceplate |
| **`Val_SpeedFdbkRaw`** | `INT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Copy of Speed Feedback Input (in Raw units) for faceplate |
| **`Val_SpeedRefEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Minimum of Speed Reference = MIN (Cfg_SpeedFdbkEUMin, Cfg_SpeedFdbkEUMax) |
| **`Val_SpeedRefEUMax`** | `REAL` | `130` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Maximum of Speed Reference = MAX (Cfg_SpeedFdbkEUMin, Cfg_SpeedFdbkEUMax) |
| **`Val_SpeedFdbkEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Minimum of Speed Feedback = MIN (Cfg_SpeedFdbkEUMin, Cfg_SpeedFdbkEUMax) |
| **`Val_SpeedFdbkEUMax`** | `REAL` | `130` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Maximum of Speed Feedback = MAX (Cfg_SpeedFdbkEUMin, Cfg_SpeedFdbkEUMax) |
| **`Val_LastFaultCode`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Last drive fault code (enum) (Par 951) |
| **`Val_Cmd`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device command: 0 = None 1 = Stop 2 = Start forward 3 = Start reverse 4 = Jog forward 5 = Jog reverse |
| **`Val_Fdbk`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device feedback: 0 = Stopped 1 = Running forward 2 = Running reverse 3 = Accelerating 4 = Decelerating |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Confirmed Status: 0 = Powerup/Unknown 1 = Stopped 2 = Running forward 3 = Running reverse 4 = Jogging forward 5 = Jogging reverse 6 = Stopping 7 = Starting forward 8 = Starting reverse 33 = Disabled |
| **`Val_Fault`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device fault status: 0 = None 16 = Fail to Start 17 = Fail to Stop 18 = Drive Fault 32 = I/O Fault 34 = Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận của khối này cộng với khối kênh (enum) |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Current Object Owner ID (0 = not owned) |
| **`Sts_Stopped`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive requested to stop and is confirmed stopped |
| **`Sts_StartingFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Drive requested to run forward and awaiting run feedback |
| **`Sts_StartingRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Drive requested to run reverse and awaiting run feedback |
| **`Sts_RunningFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Drive requested to run and is confirmed running forward |
| **`Sts_RunningRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Drive requested to run and is confirmed running reverse |
| **`Sts_StoppingFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Drive running forward requested to stop and awaiting stopped feedback |
| **`Sts_StoppingRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Drive running reverse requested to stop and awaiting stopped feedback |
| **`Sts_JoggingFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Drive requested to Jog Forward |
| **`Sts_JoggingRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Drive requested to Jog Reverse |
| **`Sts_Horn`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Motor Horn Active |
| **`Sts_CommandDir`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Drive commanded to Forward, 0=Reverse |
| **`Sts_ActualDir`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Drive Actual direction is Forward, 0=Reverse |
| **`Sts_Accel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Drive is Accelerating |
| **`Sts_Decel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Drive is Decelerating |
| **`Sts_NotReady`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive reports it is not ready to run |
| **`Sts_Alarm`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Drive has an Alarm (see drive display or manual) |
| **`Sts_AtSpeed`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1=Drive is running at reference speed |
| **`Sts_SpeedLimited`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Speed Reference Setting exceeds configured Max/Min limit |
| **`Sts_DriveSts1`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Drive Status Word #1 (bit mapped) Par 935) |
| **`Sts_DriveSts2`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Drive Status Word #2 (bit mapped) (Par 936) |
| **`Sts_FaultStsA`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Drive Fault Status A )bit mapped) (Par 952) |
| **`Sts_FaultStsB`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Drive Fault Status B (bit mapped) (Par 953) |
| **`Sts_PMSts`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Predictive Maintenance Status (bit mapped) (Par 469) |
| **`Sts_StartInhibits`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Drive Start Inhibit Reasons (bit mapped) (Par. 933) |
| **`Sts_MsgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Error in getting Fault information from drive |
| **`Sts_Available`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive available for control by automation (Prog) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bypassable Interlocks and Permissives are Bypassed |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bypassing Active (Bypassed or Maintenance) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Device Not Ready, see detail bits for reason |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_DriveNR`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Drive Not Ready |
| **`Nrdy_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi thiết bị ngắt tải (yêu cầu nhấn Reset) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Khóa liên động không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi truyền thông I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Thiết bị đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Nrdy_Perm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Cho phép khởi động không thỏa mãn (Permissive Not OK) |
| **`Nrdy_PrioStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang có lệnh Dừng ưu tiên từ Operator/External (yêu cầu nhấn Reset) |
| **`Nrdy_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Deivce Tripped (Drive Fault) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=An Alarm is Disabled or Shelved, display icon |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình (kiểm tra các bit Err_* chi tiết để biết nguyên nhân) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Invalid Check or Reset Pulse Time (use 0 to 2147483) |
| **`Err_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: simulation timer preset: use 0 to 2147483) |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thông số cảnh báo (Thời gian duy trì tối thiểu, thời gian Shelve, mức Severity) không hợp lệ |
| **`Err_FdbkRaw`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Erron in Config: Speed Fdbk Raw Min = Max |
| **`Err_FdbkEU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Speed Fdbk Engineering Units Min = Max |
| **`Err_RefLim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Speed Ref Limit Min > Max |
| **`Err_RefEU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Speed Ref Engineering Units Min = Max |
| **`Err_RefRaw`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1=Error in Config: Speed Ref Raw Min = Max |
| **`Sts_Hand`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Hand đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn OoS, Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_OoS`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Out of Service đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_Maint`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Maintenance đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ovrd, Ext, Prog, Oper) |
| **`Sts_Ovrd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Override đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ext, Prog, Oper) |
| **`Sts_Ext`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = External đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Prog, Oper) |
| **`Sts_Prog`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Program đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_ProgLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Program đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Oper`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_OperLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Selection equals the Normal (Prog or Oper) |
| **`Sts_ExtReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = External request inhibited, cannot Get to External from current state |
| **`Sts_ProgReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Program request inhibited, cannot Get to Program from current state |
| **`Sts_MAcqRcvd`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Maintenance Acquire command received this scan |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = The drive, or a latched alarm or shed condition, is ready to be reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive Failed to Start (One-Shot) |
| **`Alm_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Drive Fail to Start Alarm |
| **`Ack_FailToStart`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Fail to Start Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_FailToStartDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Start Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_FailToStartShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Start Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_FailToStartSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Start Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive Failed to Stop |
| **`Alm_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Drive Fail to Stop Alarm |
| **`Ack_FailToStop`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Fail to Stop Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_FailToStopDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Stop Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_FailToStopShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Stop Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_FailToStopSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Stop Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive was stopped by an Interlock NOT OK (One-Shot) |
| **`Alm_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Alarm: Drive stopped by an Interlock NOT OK |
| **`Ack_IntlkTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Interlock Trip Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IntlkTripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock Trip Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IntlkTripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock Trip Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IntlkTripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock Trip Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_DriveFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive Fault (see drive display or manual) |
| **`Alm_DriveFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Alarm: Drive Fault (see drive display or manual) |
| **`Ack_DriveFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Drive Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_DriveFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_DriveFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_DriveFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] I/O Comm Fault Status (0 = OK, 1 = Bad) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = I/O Fault Alarm |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = I/O Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`MRdy_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`P_PF6000`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 40 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_PF753`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'PF6000 MV Variable Frequency Drive'` | [Biến cấu hình thông số] Description for display on HMI. This string is shown in the title bar of the faceplate. |
| **`Cfg_FwdText`** | `STRING_16` | `' '` | [Biến cấu hình thông số] Name for Forward Direction (not used but required for global objects) |
| **`Cfg_Label`** | `STRING_20` | `'Motor Speed Control'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI. This string appears on the graphic symbol. |
| **`Cfg_RevText`** | `STRING_16` | `' '` | [Biến cấu hình thông số] Name for Reverse Direction (not used but required for global objects) |
| **`Cfg_SpeedFdbkEU`** | `STRING_8` | `'Hz'` | [Biến cấu hình thông số] Speed Feedback Engineering Units for display on HMI |
| **`Cfg_SpeedRefEU`** | `STRING_8` | `'Hz'` | [Biến cấu hình thông số] Speed Reference Engineering Units for display on HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_PF6000'` | [Biến cấu hình thông số] Tagname for display on HMI. This string is shown in the title bar of the faceplate. |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc quản lý lựa chọn nguồn lệnh điều khiển |
| **`DriveFault`** | `P_Alarm` | `{...}` | Drive Faulted Alarm |
| **`FailToStart`** | `P_Alarm` | `{...}` | Drive Failed to Start Alarm |
| **`FailToStop`** | `P_Alarm` | `{...}` | Drive Failed to Stop Alarm |
| **`HMI_FWMajor`** | `DINT` | `5` | [Biến nội bộ giao diện HMI Faceplate] Drive Firmware Major Revision (5, 6 supported) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_PF6000'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | Interlock Trip Alarm |
| **`IOFault`** | `P_Alarm` | `{...}` | I/O Fault Alarm |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bypass all Bypassable Interlocks and Permissives |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Check (not bypass) all Interlocks and Permissives |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`OCmd_JogFwd`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Jog Drive Forward (not cleared by instruction!) |
| **`OCmd_JogRev`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Jog Drive Reverse (not cleared by instruction!) |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset Shed Latches, Cleared Alarms, Drive Fault |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset Shed Latches, Cleared Alarms, Drive Fault, and Xác nhận (Acknowledge) Alarms |
| **`OCmd_StartFwd`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Start Drive Forward |
| **`OCmd_StartRev`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Start Drive Reverse |
| **`OCmd_Stop`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Stop Drive |
| **`ORdy_JogFwd`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_JogFwd (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_JogRev`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_JogRev (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_SpeedRef`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OSet_SpeedRef (enables data entry field) |
| **`ORdy_StartFwd`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_StartFwd (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_StartRev`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_StartRev (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Stop`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Stop (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`OSet_SpeedRef`** | `REAL` | `0` | [Giá trị đặt từ màn hình HMI Operator] Operator Setting of Speed Reference (Engineering Units) |
| **`Val_FaultDesc`** | `STRING_16` | `‘Not yet scanned.’` | [Giá trị thực tế đầu ra (Logic/HMI)] Description of Drive Fault (given LastFaultCode) |
| **`Val_Notify`** | `DINT` | `??` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_PF755`
**Thuộc chương:** Chương 3: Động cơ, Biến tần & Rơ-le Quá tải (Motors & Drives)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Motors 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_PF755`:** **117** Biến đầu vào (`Input Parameters`) | **123** Biến đầu ra (`Output Parameters`) | **40** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_PF755`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp`, `Out`, `Ref_GetFaultMSG`, `Ref_GetFaultDest`, `Inp_FwdPermOK`, `Inp_FwdNBPermOK`, `Inp_RevPermOK`, `Inp_RevNBPermOK`, `Inp_IntlkOK`, `Inp_NBIntlkOK`, `Inp_IOFault`, `Inp_Sim`, `Inp_Hand`, `Inp_Ovrd`, `Inp_OvrdCmd`, `Inp_OvrdSpeed`, `Inp_ExtIhn`, `Inp_HornInh`...
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_PF755T`, `Cfg_HasReverse`, `Cfg_HasJog`, `Cfg_AllowLocal`, `Cfg_HasFwdPermObj`, `Cfg_HasRevPermObj`, `Cfg_HasIntlkObj`, `Cfg_HasMoreObj`, `Cfg_HasResInhObj`, `Cfg_HasRunTimeObj`, `Cfg_SetTrack`, `Cfg_SetTrackOvrdHand`, `Cfg_OperStopPrio`, `Cfg_ExtStopPrio`, `Cfg_OCmdResets`, `Cfg_XCmdResets`, `Cfg_OvrdPermIntlk`, `Cfg_ShedOnFailToStart`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_FwdText`, `Cfg_Label`, `Cfg_RevText`, `Cfg_SpeedFdbkEU`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_PF755` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PSet_SpeedRef`, `PSet_Owner`, `PCmd_StartFwd`, `PCmd_StartRev`, `PCmd_Stop`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_FailToStartAck`, `PCmd_FailToStartSuppress`, `PCmd_FailToStartUnsuppress`, `PCmd_FailToStartUnshelve`, `PCmd_FailToStopAck`, `PCmd_FailToStopSuppress`, `PCmd_FailToStopUnsuppress`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Inp`, `Out`, `Ref_GetFaultMSG`, `Ref_GetFaultDest`, `Out_Horn`, `Val_SpeedRef`, `Val_SpeedFdbk`, `Val_OutCurrent`, `Val_TorqCurrFdbk`, `Val_OutPower`, `Val_OvldC`, `Val_Temp`, `Val_ElapsedMWHr`, `Val_ElapsedRunT`, `Val_SpeedRefSrc`, `Val_SpeedEUMin`, `Val_SpeedEUMax`, `Val_LastFaultCode`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 117 Biến Đầu Vào (`Input Parameters`) của `P_PF755`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp`** | `P_PF755_Inp` | - | Hiện (Pin) | Common part of PowerFlex 755 Input Assembly |
| **`Out`** | `P_PF755_Out` | - | Hiện (Pin) | Common part of PowerFlex 755 Output Assembly |
| **`Ref_GetFaultMSG`** | `MESSAGE` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] MSG to Get Last Fault Record |
| **`Ref_GetFaultDest`** | `P_PFEmb_FltAlmRec` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] Buffer for data from Fault Record or Alarm Record MSG |
| **`Inp_FwdPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Permissives OK, drive can start Forward |
| **`Inp_FwdNBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Non-Bypassable Permissives OK, drive can start Forward |
| **`Inp_RevPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Permissives OK, drive can start Reverse |
| **`Inp_RevNBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Non-Bypassable Permissives OK, motor can start Reverse |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Interlocks OK, drive can start/run |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Non-Bypassable Interlocks OK, drive can start/run |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Input Communication Status 0 = OK, 1 = fail |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Simulation input. When set to 1, the instruction keeps outputs de-energized (zero) and simulates a working drive. When set to 0, the instruction operates the drive normally. |
| **`Inp_Hand`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Acquire Hand (typ. hardwired local), 0 = Release Hand |
| **`Inp_Ovrd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Acquire Override (higher priority program logic), 0 = Release Override |
| **`Inp_OvrdCmd`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Override Command: 0 = None, 1 = Stop, 2=Start Fwd 3=Start Rev |
| **`Inp_OvrdSpeed`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Value to set Speed Reference in Override (SpeedRef Engineering Units) |
| **`Inp_ExtIhn`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Cấm nguồn lệnh ngoài (External) chiếm quyền điều khiển; 0 = Cho phép External chiếm quyền. |
| **`Inp_HornInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Cấm còi báo động âm thanh; 0 = Cho phép phát còi báo trước khi khởi động |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Reset Shed Latches, Cleared Alarms, Drive Fault |
| **`Cfg_PF755T`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1=Tells HMI a P_Perm is connected to Inp_FwdPerm |
| **`Cfg_HasReverse`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Drive can be run reverse, 0 = Forward only |
| **`Cfg_HasJog`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Drive Jog Command enabled/visible, 0 = Drive Jog Command not allowed |
| **`Cfg_AllowLocal`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Allow Local Start/Stop without alarm, 0 = Start/Stop from HMI/prog only |
| **`Cfg_HasFwdPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng forward permissive liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasRevPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng reverse permissive liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng interlock liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_HasResInhObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng restart inhibit liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasRunTimeObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng runtime object is connected and navigation to the runtime liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_SetTrack`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] This parameter is used to set up bumpless behavior of setting parameters when switching modes. When this parameter is 1, in Program mode the operator settings track the program settings; in Operator mode the program settings track the operator settings; and the simulation inputs match the output values (transitions are bumpless). When this parameter is 0, the operator settings and program settings are not modified by this instruction. In this case, when the mode is changed, the effective value of the setting can change depending on the program-set and operator-set values. |
| **`Cfg_SetTrackOvrdHand`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Program/Operator Settings track Override/Hand speed reference |
| **`Cfg_OperStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = OCmd_Stop any time, 0 = OCmd_Stop only when Operator selected |
| **`Cfg_ExtStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = XCmd_Stop any time, 0 = XCmd_Stop only when Ext selected |
| **`Cfg_OCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = New drive OCmd resets shed latches and cleared alarms, 0 = OCmdReset req'd. |
| **`Cfg_XCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = New drive XCmd resets shed latches and cleared alarms, 0 = XCmdReset req'd. |
| **`Cfg_OvrdPermIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Chế độ Override bỏ qua các liên động/điều kiện cho phép có thể bypass; 0 = Luôn tuân thủ mọi liên động. |
| **`Cfg_ShedOnFailToStart`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt lệnh chạy động cơ (Shed) và phát cảnh báo khi bị lỗi Không khởi động được (Fail to Start); 0 = Chỉ phát cảnh báo. |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt động cơ (Shed) và phát cảnh báo khi lỗi thẻ I/O; 0 = Chỉ phát cảnh báo. |
| **`Cfg_DecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Number of decimal places for speed ref/fdbk display (0..6) |
| **`Cfg_HasFailToStartAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasFailToStopAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasDriveFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_FailToStartResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_FailToStopResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_DriveFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_FailToStartAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_FailToStopAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_DriveFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_FailToStartSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the severity of each alarm that gauges the color and symbol that are used to indicate alarm status on the faceplate and global object. The following are valid values: 1…250 = Low 251…500 = Medium 501…750 = High 751…1000 = Urgent IMPORTANT: These severity priorities drive only the indication on the global object and faceplate. The Alarm and Events definition severity drives the color and symbol that is used on the alarm banner and alarm summary as well the value returned by the FactoryTalk Alarm and Events software display commands. |
| **`Cfg_FailToStopSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the severity of each alarm that gauges the color and symbol that are used to indicate alarm status on the faceplate and global object. The following are valid values: 1…250 = Low 251…500 = Medium 501…750 = High 751…1000 = Urgent IMPORTANT: These severity priorities drive only the indication on the global object and faceplate. The Alarm and Events definition severity drives the color and symbol that is used on the alarm banner and alarm summary as well the value returned by the FactoryTalk Alarm and Events software display commands. |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the severity of each alarm that gauges the color and symbol that are used to indicate alarm status on the faceplate and global object. The following are valid values: 1…250 = Low 251…500 = Medium 501…750 = High 751…1000 = Urgent IMPORTANT: These severity priorities drive only the indication on the global object and faceplate. The Alarm and Events definition severity drives the color and symbol that is used on the alarm banner and alarm summary as well the value returned by the FactoryTalk Alarm and Events software display commands. |
| **`Cfg_DriveFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the severity of each alarm that gauges the color and symbol that are used to indicate alarm status on the faceplate and global object. The following are valid values: 1…250 = Low 251…500 = Medium 501…750 = High 751…1000 = Urgent IMPORTANT: These severity priorities drive only the indication on the global object and faceplate. The Alarm and Events definition severity drives the color and symbol that is used on the alarm banner and alarm summary as well the value returned by the FactoryTalk Alarm and Events software display commands. |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the severity of each alarm that gauges the color and symbol that are used to indicate alarm status on the faceplate and global object. The following are valid values: 1…250 = Low 251…500 = Medium 501…750 = High 751…1000 = Urgent IMPORTANT: These severity priorities drive only the indication on the global object and faceplate. The Alarm and Events definition severity drives the color and symbol that is used on the alarm banner and alarm summary as well the value returned by the FactoryTalk Alarm and Events software display commands. |
| **`Cfg_MinSpdRef`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum Speed Reference in Engineering Units (for limiting) |
| **`Cfg_MaxSpdRef`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Maximum Speed Reference in Engineering Units (for limiting) |
| **`Cfg_SpeedEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Reference and Feedback Minimum in Engineering Units (for scaling) |
| **`Cfg_SpeedEUMax`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Reference and Feedback Maximum in Engineering Units (for scaling) |
| **`Cfg_SpeedRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Reference and Feedback Minimum in Drive Units (Hz or RPM) (for scaling) |
| **`Cfg_SpeedRawMax`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Reference and Feedback Maximum in Drive Units (Hz or RPM) (for scaling) |
| **`Cfg_StartHornT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian hú còi cảnh báo tại hiện trường trước khi đóng điện khởi động động cơ (giây, 0 = tắt còi). |
| **`Cfg_SimRampT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Time to ramp speed fdbk when in Simulation (seconds) |
| **`Cfg_FailToStartT`** | `REAL` | `15` | Ẩn (Tag) | [Biến cấu hình thông số] Time after Start to get Run Feedback before Fault (seconds) |
| **`Cfg_FailToStopT`** | `REAL` | `15` | Ẩn (Tag) | [Biến cấu hình thông số] Time after Stop to drop Run Feedback before Fault (seconds) |
| **`Cfg_ResetPulseT`** | `REAL` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Time to pulse Out_Reset to clear drive fault |
| **`Cfg_MaxJogT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian giữ lệnh nhấp thử (Jog) tối đa cho phép (giây, 0 = không giới hạn). |
| **`Cfg_OperKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Operator keeps control in Program or Ext: .0 = Reference, .1 = Commands |
| **`Cfg_ProgKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Program keeps control in Operator or Ext: .0 = Reference, .1 = Commands |
| **`Cfg_ExtKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Ext keeps control in Program or Oper: .0 = Reference, .1 = Commands |
| **`PSet_SpeedRef`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Program Setting of Speed Reference (Engineering Units) |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Mã định danh chủ sở hữu chương trình (Program Owner ID: khác 0 để đăng ký chiếm quyền, bằng 0 để nhả quyền). |
| **`XSet_SpeedRef`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài (External)] External Setting of Speed Reference (Engineering Units) |
| **`PCmd_StartFwd`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Start Drive Forward |
| **`PCmd_StartRev`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Start Drive Reverse |
| **`PCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Stop Drive |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để chuyển quyền điều khiển sang Tự động (từ Operator sang Program) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để chuyển quyền điều khiển sang Bằng tay (từ Program sang Operator) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa cứng chế độ Program (không cho phép Operator giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Mở khóa chế độ Program (cho phép Operator lấy quyền điều khiển) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để trả về nguồn lệnh Bình thường mặc định (Normal: Program hoặc Operator) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Reset Shed Latches, Cleared Alarms, Drive Fault |
| **`PCmd_FailToStartAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Fail to Start Alarm |
| **`PCmd_FailToStartSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Fail to Start Alarm |
| **`PCmd_FailToStartUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Fail to Start Alarm |
| **`PCmd_FailToStartUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Fail to Start Alarm |
| **`PCmd_FailToStopAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Fail to Stop Alarm |
| **`PCmd_FailToStopSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Fail to Stop Alarm |
| **`PCmd_FailToStopUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Fail to Stop Alarm |
| **`PCmd_FailToStopUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Fail to Stop Alarm |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Interlock Trip Alarm |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Interlock Trip Alarm |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Interlock Trip Alarm |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) InterlockTrip Alarm |
| **`PCmd_DriveFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Drive Fault Alarm |
| **`PCmd_DriveFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Drive Fault Alarm |
| **`PCmd_DriveFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Drive Fault Alarm |
| **`PCmd_DriveFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Drive Fault Alarm |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) I/O Fault Alarm |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) I/O Fault Alarm |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) I/O Fault Alarm |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) I/O Fault Alarm |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Release (Maintenance to Operator/Program/Override) |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Unlock Operator (allow Program to acquire) |
| **`XCmd_StartFwd`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Start Drive Forward |
| **`XCmd_StartRev`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Start Drive Reverse |
| **`XCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Stop Drive |
| **`XCmd_JogFwd`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Jog Drive Forward |
| **`XCmd_JogRev`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Jog Drive Reverse |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Acquire (Oper/Prog to Ext) |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Release (Ext to Oper/Prog) |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Reset Shed Latches, Cleared Alarms, Drive Fault |

## 3. Bảng chi tiết 123 Biến Đầu Ra (`Output Parameters`) của `P_PF755`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Inp`** | `P_PF755_Inp` | - | Hiện (Pin) | Common part of PowerFlex 755 Input Assembly |
| **`Out`** | `P_PF755_Out` | - | Hiện (Pin) | Common part of PowerFlex 755 Output Assembly |
| **`Ref_GetFaultMSG`** | `MESSAGE` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] MSG to Get Last Fault Record |
| **`Ref_GetFaultDest`** | `P_PFEmb_FltAlmRec` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] Buffer for data from Fault Record or Alarm Record MSG |
| **`Out_Horn`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1 = Sound audible prior to commanded motor start |
| **`SrcQ_IO`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final drive source and quality. GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`Val_SpeedRef`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Speed Reference (target) to drive |
| **`Val_SpeedFdbk`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Speed Feedback (actual) from drive |
| **`Val_OutCurrent`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Output Current (Amps) (Par 7) |
| **`Val_TorqCurrFdbk`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Torque Current Feedback (Par. 5) |
| **`Val_OutPower`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Output Power (kW) (Par 9) |
| **`Val_OvldC`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Overload Count (%) (Par 940) |
| **`Val_Temp`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Drive Temperatore (Deg C) (Par. 944) |
| **`Val_ElapsedMWHr`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Elapsed Megawhatt Hours (MWh) (Par 13) |
| **`Val_ElapsedRunT`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Elapsed Run Time (Hrs) (Par 15) |
| **`Val_SpeedRefSrc`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Speed reference source (enumeration): 1 = Reference A 2 = Reference B 3 = Preset 3 4 = Preset 4 5 = Preset 5 6 = Preset 6 7 = Preset 7 16…31 = Manual Reference Settings |
| **`Val_SpeedEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Minimum of Speed Reference = MIN (Cfg_SpeedFdbkEUMin, Cfg_SpeedFdbkEUMax) |
| **`Val_SpeedEUMax`** | `REAL` | `130` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Maximum of Speed Reference = MAX (Cfg_SpeedFdbkEUMin, Cfg_SpeedFdbkEUMax) |
| **`Val_LastFaultCode`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Last drive fault code (enum) (Par 951) |
| **`Val_Cmd`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device command: 0 = None 1 = Stop 2 = Start forward 3 = Start reverse 4 = Jog forward 5 = Jog reverse |
| **`Val_Fdbk`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device feedback: 0 = Stopped 1 = Running forward 2 = Running reverse 3 = Accelerating 4 = Decelerating |
| **`Val_Sts`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device confirmed status: 0 = None 1 = Stopped 2 = Running forward 3 = Running reverse 4 = Jogging forward 5 = Jogging reverse 6 = Stopping 7 = Starting forward 8 = Starting reverse 33 = Disabled |
| **`Val_Fault`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device fault status: 0 = None 16 = Fail to Start 17 = Fail to Stop 18 = Drive Fault 32 = I/O Fault 34 = Configuration error |
| **`Val_NotifyAll`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận của khối này cộng với khối kênh (enum) |
| **`Val_UnackAlmC`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Current Object Owner ID (0 = not owned) |
| **`Sts_Stopped`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive requested to stop and is confirmed stopped |
| **`Sts_StartingFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive requested to run forward and awaiting run feedback |
| **`Sts_StartingRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive requested to run reverse and awaiting run feedback |
| **`Sts_RunningFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive requested to run and is confirmed running forward |
| **`Sts_RunningRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive requested to run and is confirmed running reverse |
| **`Sts_StoppingFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive running forward requested to stop and awaiting stopped feedback |
| **`Sts_StoppingRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive running reverse requested to stop and awaiting stopped feedback |
| **`Sts_JoggingFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive requested to Jog Forward |
| **`Sts_JoggingRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive requested to Jog Reverse |
| **`Sts_Horn`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Valve Horn Active |
| **`Sts_CommandDir`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive commanded to Forward, 0 = Reverse |
| **`Sts_ActualDir`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive Actual direction is Forward, 0 = Reverse |
| **`Sts_Accel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive is Accelerating |
| **`Sts_Decel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive is Decelerating |
| **`Sts_NotReady`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive is Not Ready (cannot be started) Check alarms, stops, faults |
| **`Sts_Alarm`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive has an Alarm (see drive display or manual) |
| **`Sts_AtSpeed`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive is running at reference speed |
| **`Sts_SpeedLimited`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Speed Reference Setting exceeds configured Max/Min limit |
| **`Sts_DriveSts1`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Drive Status Word #1 (bit mapped) Par 935) |
| **`Sts_DriveSts2`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Drive Status Word #2 (bit mapped) (Par 936) |
| **`Sts_FaultStsA`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Drive Fault Status A )bit mapped) (Par 952) |
| **`Sts_FaultStsB`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Drive Fault Status B (bit mapped) (Par 953) |
| **`Sts_PMSts`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Predictive Maintenance Status (bit mapped) (Par 469) |
| **`Sts_StartInhibits`** | `DINT` | `2#0000_0000_0000_0000_0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Drive Start Inhibit Reasons (bit mapped) (Par. 933) |
| **`Sts_MsgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Error in getting Fault information from drive |
| **`Sts_Available`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive available for control by automation (Prog) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bypassable Interlocks and Permissives are Bypassed |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bypassing Active (Bypassed or Maintenance) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Device Not Ready, see detail bits for reason |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_DriveNR`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Device Note Ready: Drive Not Ready |
| **`Nrdy_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi thiết bị ngắt tải (yêu cầu nhấn Reset) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Khóa liên động không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi truyền thông I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Thiết bị đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Nrdy_Perm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Cho phép khởi động không thỏa mãn (Permissive Not OK) |
| **`Nrdy_PrioStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang có lệnh Dừng ưu tiên từ Operator/External (yêu cầu nhấn Reset) |
| **`Nrdy_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Device not Ready: Deivce Tripped (Drive Fault) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=An Alarm is Disabled or Shelved, display icon |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình (kiểm tra các bit Err_* chi tiết để biết nguyên nhân) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Invalid Check or Reset Pulse Time (use 0 to 2147483) |
| **`Err_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: simulation timer preset: use 0 to 2147483) |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thông số cảnh báo (Thời gian duy trì tối thiểu, thời gian Shelve, mức Severity) không hợp lệ |
| **`Err_EU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Speed Reference and Feedback Engineering Units Min = Engineering Units Max |
| **`Err_Raw`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Speed Reference and Feedback Raw Min = Raw Max |
| **`Err_RefLim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Speed Reference Clamping Limit Min > Max |
| **`Sts_Hand`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Hand đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn OoS, Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_OoS`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Out of Service đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_Maint`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Maintenance đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ovrd, Ext, Prog, Oper) |
| **`Sts_Ovrd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Override đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ext, Prog, Oper) |
| **`Sts_Ext`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = External đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Prog, Oper) |
| **`Sts_Prog`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Program đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_ProgLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Program đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Oper`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_OperLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Selection equals the Normal (Prog or Oper) |
| **`Sts_ExtReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_ProgReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Program request inhibited, cannot Get to Program from current state |
| **`Sts_MAcqRcvd`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Maintenance Acquire command received this scan |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = The drive, or a latched alarm or shed condition, is ready to be reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive Failed to Start (One-Shot) |
| **`Alm_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Drive Fail to Start Alarm |
| **`Ack_FailToStart`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Fail to Start Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_FailToStartDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Start Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_FailToStartShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Start Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_FailToStartSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Start Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive Failed to Stop |
| **`Alm_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Drive Fail to Stop Alarm |
| **`Ack_FailToStop`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Fail to Stop Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_FailToStopDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Stop Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_FailToStopShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Stop Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_FailToStopSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Stop Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive was stopped by an Interlock NOT OK (One-Shot) |
| **`Alm_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Alarm: Drive stopped by an Interlock NOT OK |
| **`Ack_IntlkTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Interlock Trip Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IntlkTripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock Trip Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IntlkTripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock Trip Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IntlkTripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock Trip Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_DriveFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive Fault (see drive display or manual) |
| **`Alm_DriveFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Alarm: Drive Fault (see drive display or manual) |
| **`Ack_DriveFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Drive Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_DriveFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_DriveFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_DriveFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] I/O Comm Fault Status (0 = OK, 1 = Bad) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = I/O Fault Alarm |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = I/O Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`MRdy_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`P_PF755`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 40 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_PF755`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'PowerFlex 755 Variable Frequency Drive'` | [Biến cấu hình thông số] Description for display on HMI. This string is shown in the title bar of the faceplate. |
| **`Cfg_FwdText`** | `STRING_16` | `'Forward'` | [Biến cấu hình thông số] Name for Forward Direction, e.g., "Up", "Forward" |
| **`Cfg_Label`** | `STRING_20` | `'Motor Speed Control'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI. This string appears on the graphic symbol. |
| **`Cfg_RevText`** | `STRING_16` | `'Reverse'` | [Biến cấu hình thông số] Name for Reverse Direction, e.g., "Down", "Reverse" |
| **`Cfg_SpeedFdbkEU`** | `STRING_8` | `'Hz'` | [Biến cấu hình thông số] Speed Feedback Engineering Units for display on HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_PF755'` | [Biến cấu hình thông số] Tagname for display on HMI. This string is shown in the title bar of the faceplate. |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc quản lý lựa chọn nguồn lệnh điều khiển |
| **`DriveFault`** | `P_Alarm` | `{...}` | Drive Faulted Alarm |
| **`FailToStart`** | `P_Alarm` | `{...}` | Drive Failed to Start Alarm |
| **`FailToStop`** | `P_Alarm` | `{...}` | Drive Failed to Stop Alarm |
| **`HMI_HasMore`** | `BOOL` | `0` | [Biến nội bộ giao diện HMI Faceplate] Drive Failed to Stop Alarm |
| **`HMI_Lib`** | `STRING_8` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_12` | `'P_PF755'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | Interlock Trip Alarm |
| **`IOFault`** | `P_Alarm` | `{...}` | I/O Fault Alarm |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bypass all Bypassable Interlocks and Permissives |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Check (not bypass) all Interlocks and Permissives |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`OCmd_JogFwd`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Jog Drive Forward (not cleared by instruction!) |
| **`OCmd_JogRev`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Jog Drive Reverse (not cleared by instruction!) |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset Shed Latches, Cleared Alarms, Drive Fault |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset Shed Latches, Cleared Alarms, Drive Fault, and Xác nhận (Acknowledge) Alarms |
| **`OCmd_StartFwd`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Start Drive Forward |
| **`OCmd_StartRev`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Start Drive Reverse |
| **`OCmd_Stop`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Stop Drive |
| **`ORdy_JogFwd`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_JogFwd (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_JogRev`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_JogRev (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_SpeedRef`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OSet_SpeedRef (enables data entry field) |
| **`ORdy_StartFwd`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_StartFwd (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_StartRev`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_StartRev (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Stop`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Stop (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`OSet_SpeedRef`** | `REAL` | `0` | [Giá trị đặt từ màn hình HMI Operator] Operator Setting of Speed Reference (Engineering Units) |
| **`Val_DriveEU`** | `STRING_8` | `'Hz'` | [Giá trị thực tế đầu ra (Logic/HMI)] Drive Speed Units (Hz or RPM) for display on HMI |
| **`Val_FaultDesc`** | `STRING_16` | `‘Not yet scanned.’` | [Giá trị thực tế đầu ra (Logic/HMI)] Description of Drive Fault (given LastFaultCode) |
| **`Val_Notify`** | `SINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_PF6000`
**Thuộc chương:** Chương 3: Động cơ, Biến tần & Rơ-le Quá tải (Motors & Drives)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Motors 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_PF6000`:** **112** Biến đầu vào (`Input Parameters`) | **111** Biến đầu ra (`Output Parameters`) | **44** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_PF6000`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp`, `Out`, `Ref_FaultCodeList`, `Inp_PermOK`, `Inp_NBPermOK`, `Inp_IntlkOK`, `Inp_NBIntlkOK`, `Inp_Sim`, `Inp_Hand`, `Inp_Ovrd`, `Inp_OvrdCmd`, `Inp_OvrdSpeed`, `Inp_ExtInh`, `Inp_HornInh`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_AllowLocal`, `Cfg_HasMoreObj`, `Cfg_HasParRWObj`, `Cfg_HasPermObj`, `Cfg_HasIntlkObj`, `Cfg_HasResInhObj`, `Cfg_HasRunTimeObj`, `Cfg_SetTrack`, `Cfg_SetTrackOvrdHand`, `Cfg_OperStopPrio`, `Cfg_ExtStopPrio`, `Cfg_OCmdResets`, `Cfg_XCmdResets`, `Cfg_OvrdPermIntlk`, `Cfg_ShedOnFailToStart`, `Cfg_ShedOnIOFault`, `Cfg_SimScaleEU`, `Cfg_SimScaleRaw`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_FwdText`, `Cfg_Label`, `Cfg_RevText`, `Cfg_SpeedFdbkEU`, `Cfg_SpeedRefEU`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_PF6000` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PSet_SpeedRef`, `PSet_Owner`, `PCmd_Start`, `PCmd_Stop`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_FailToStartAck`, `PCmd_FailToStartSuppress`, `PCmd_FailToStartUnsuppress`, `PCmd_FailToStartUnshelve`, `PCmd_FailToStopAck`, `PCmd_FailToStopSuppress`, `PCmd_FailToStopUnsuppress`, `PCmd_FailToStopUnshelve`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Inp`, `Out`, `Ref_FaultCodeList`, `Out_Horn`, `Val_SpeedRef`, `Val_SpeedFdbk`, `Val_SpeedRefRaw`, `Val_SpeedFdbkRaw`, `Val_SpeedRefEUMin`, `Val_SpeedRefEUMax`, `Val_SpeedFdbkEUMin`, `Val_SpeedFdbkEUMax`, `Val_MotorCurrent`, `Val_MotorCurrentPct`, `Val_MotorVoltage`, `Val_OutputPower`, `Val_MotorPowerPct`, `Val_TotalPowerPct`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 112 Biến Đầu Vào (`Input Parameters`) của `P_PF6000`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp`** | `P_PF6000_Inp` | - | Hiện (Pin) | PowerFlex 6000 Input Assembly (<drivename>:I) |
| **`Out`** | `P_PF6000_Out` | - | Hiện (Pin) | PowerFlex 6000 Output Assembly (<drivename>:O) |
| **`Ref_FaultCodeList`** | `P_DescList[1]` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] Tag containing List of Fault Codes (DINT) and their Descriptions (STRING) |
| **`Inp_PermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Permissives OK, drive can start Forward |
| **`Inp_NBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Non-Bypassable Permissives OK, drive can start Forward |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Interlocks OK, drive can start/run |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Non-Bypassable Interlocks OK, drive can start/run |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Simulation input. When set to 1, the instruction keeps outputs de-energized (zero) and simulates a working drive. When set to 0, the instruction operates the drive normally. |
| **`Inp_Hand`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Acquire Hand (typ. hardwired local) Mode, 0 = Release Hand Mode |
| **`Inp_Ovrd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Acquire Override (higher priority program logic), 0 = Release Override |
| **`Inp_OvrdCmd`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Override Mode Command: 0 = None, 1 = Stop, 2=Start |
| **`Inp_OvrdSpeed`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Value to set Speed Reference in Override Mode (SpeedRef Engineering Units) |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Cấm nguồn lệnh ngoài (External) chiếm quyền điều khiển; 0 = Cho phép External chiếm quyền. |
| **`Inp_HornInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Cấm còi báo động âm thanh; 0 = Cho phép phát còi báo trước khi khởi động |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Reset Shed Latches, Cleared Alarms, Drive Fault |
| **`Cfg_AllowLocal`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Allow Local Start/Stop without alarm, 0 = Start/Stop from HMI/prog only |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_HasParRWObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Enable navigation to Parameter Read/Write Popup |
| **`Cfg_HasPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tells HMI a P_Perm is connected to Inp_Perm |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tells HMI a P_Intlk is connected to Inp_Intlk |
| **`Cfg_HasResInhObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tells HMI a P_ResInh Restart Inhibit is connected |
| **`Cfg_HasRunTimeObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tells HMI a P_RunTime is connected |
| **`Cfg_SetTrack`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = PSets track OSets in Operator OSets track PSets in Prog, 0 = no tracking |
| **`Cfg_SetTrackOvrdHand`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Program/Operator Settings track Override/Hand speed reference |
| **`Cfg_OperStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = OCmd_Stop any time, 0 = OCmd_Stop only when Operator selected |
| **`Cfg_ExtStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = XCmd_Stop any time, 0 = XCmd_Stop only when Ext selected |
| **`Cfg_OCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = New drive OCmd resets shed latches and cleared alarms, 0 = OCmdReset req'd. |
| **`Cfg_XCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = New drive XCmd resets shed latches and cleared alarms, 0 = XCmdReset req'd. |
| **`Cfg_OvrdPermIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Chế độ Override bỏ qua các liên động/điều kiện cho phép có thể bypass; 0 = Luôn tuân thủ mọi liên động. |
| **`Cfg_ShedOnFailToStart`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Stop motor and alarm on Fail to Start. 0 = Alarm only on Fail to Start. IMPORTANT: If a condition is configured to shed the device to the Off state on a fault, a reset is required to clear the shed fault to command the drive to a state other than Off. |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Stop motor and alarm on I/O Fault. 0 = Alarm only on I/O Fault. IMPORTANT: If a condition is configured to shed the device to the Off state on a fault, a reset is required to clear the shed fault to command the drive to a state other than Off. |
| **`Cfg_SimScaleEU`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = In simulation, scale Speed Ref Engineering Units to Speed Fdbk Engineering Units |
| **`Cfg_SimScaleRaw`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = In simulation, scale Speed Ref Engineering Units to raw, then raw to Speed Fdbk Engineering Units |
| **`Cfg_DecPlcs`** | `DINT` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Number of decimal places for speed ref/fdbk display (0..6) |
| **`Cfg_HasFailToStartAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasFailToStopAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasDriveFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_FailToStartResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_FailToStopResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_DriveFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_FailToStartAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_FailToStopAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_DriveFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_FailToStartSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the severity of each alarm that gauges the color and symbol that are used to indicate alarm status on the faceplate and global object. The following are valid values: 1…250 = Low 251…500 = Medium 501…750 = High 751…1000 = Urgent IMPORTANT: These severity priorities drive only the indication on the global object and faceplate. The Alarm and Events definition severity drives the color and symbol that is used on the alarm banner. The definition severity also provides an alarm summary and the value returned by the FactoryTalk Alarm and Events software display commands. |
| **`Cfg_FailToStopSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the severity of each alarm that gauges the color and symbol that are used to indicate alarm status on the faceplate and global object. The following are valid values: 1…250 = Low 251…500 = Medium 501…750 = High 751…1000 = Urgent IMPORTANT: These severity priorities drive only the indication on the global object and faceplate. The Alarm and Events definition severity drives the color and symbol that is used on the alarm banner. The definition severity also provides an alarm summary and the value returned by the FactoryTalk Alarm and Events software display commands. |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the severity of each alarm that gauges the color and symbol that are used to indicate alarm status on the faceplate and global object. The following are valid values: 1…250 = Low 251…500 = Medium 501…750 = High 751…1000 = Urgent IMPORTANT: These severity priorities drive only the indication on the global object and faceplate. The Alarm and Events definition severity drives the color and symbol that is used on the alarm banner. The definition severity also provides an alarm summary and the value returned by the FactoryTalk Alarm and Events software display commands. |
| **`Cfg_DriveFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the severity of each alarm that gauges the color and symbol that are used to indicate alarm status on the faceplate and global object. The following are valid values: 1…250 = Low 251…500 = Medium 501…750 = High 751…1000 = Urgent IMPORTANT: These severity priorities drive only the indication on the global object and faceplate. The Alarm and Events definition severity drives the color and symbol that is used on the alarm banner. The definition severity also provides an alarm summary and the value returned by the FactoryTalk Alarm and Events software display commands. |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the severity of each alarm that gauges the color and symbol that are used to indicate alarm status on the faceplate and global object. The following are valid values: 1…250 = Low 251…500 = Medium 501…750 = High 751…1000 = Urgent IMPORTANT: These severity priorities drive only the indication on the global object and faceplate. The Alarm and Events definition severity drives the color and symbol that is used on the alarm banner. The definition severity also provides an alarm summary and the value returned by the FactoryTalk Alarm and Events software display commands. |
| **`Cfg_MinSpdRef`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum Speed Reference in Engineering Units (for limiting) |
| **`Cfg_MaxSpdRef`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Maximum Speed Reference in Engineering Units (for limiting) |
| **`Cfg_SpeedRefRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Reference Minimum in Drive (raw) Units (for scaling) |
| **`Cfg_SpeedRefRawMax`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Reference Maximum in Drive (raw) Units (for scaling) |
| **`Cfg_SpeedRefEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Reference Minimum in Engineering Units (for scaling) |
| **`Cfg_SpeedRefEUMax`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Reference Maximum in Engineering Units (for scaling) |
| **`Cfg_SpeedFdbkRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Feedback Minimum in Drive (raw) Units (for scaling) |
| **`Cfg_SpeedFdbkRawMax`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Feedback Maximum in Drive (raw) Units (for scaling) |
| **`Cfg_SpeedFdbkEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Feedback Minimum in Engineering Units (for scaling) |
| **`Cfg_SpeedFdbkEUMax`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Feedback Maximum in Engineering Units (for scaling) |
| **`Cfg_StartHornT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian hú còi cảnh báo tại hiện trường trước khi đóng điện khởi động động cơ (giây, 0 = tắt còi). |
| **`Cfg_SimRampT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Time to ramp speed fdbk when in Simulation (seconds) |
| **`Cfg_FailToStartT`** | `REAL` | `15` | Ẩn (Tag) | [Biến cấu hình thông số] Time after Start to get Run Feedback before Fault (seconds) |
| **`Cfg_FailToStopT`** | `REAL` | `15` | Ẩn (Tag) | [Biến cấu hình thông số] Time after Stop to drop Run Feedback before Fault (seconds) |
| **`Cfg_ResetPulseT`** | `REAL` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Time to pulse Out_Reset to clear drive fault |
| **`Cfg_WatchdogT`** | `REAL` | `5` | Ẩn (Tag) | [Biến cấu hình thông số] Communications Watchdog Timeout time (seconds) |
| **`Cfg_OperKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Operator keeps control in Program or Ext: .0 = Reference, .1 = Commands |
| **`Cfg_ProgKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Program keeps control in Operator or Ext: .0 = Reference, .1 = Commands |
| **`Cfg_ExtKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Ext keeps control in Program or Oper: .0 = Reference, .1 = Commands |
| **`PSet_SpeedRef`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Program Setting of Speed Reference (Engineering Units) |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Mã định danh chủ sở hữu chương trình (Program Owner ID: khác 0 để đăng ký chiếm quyền, bằng 0 để nhả quyền). |
| **`XSet_SpeedRef`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài (External)] External Setting of Speed Reference (Engineering Units) |
| **`PCmd_Start`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Start Drive |
| **`PCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Stop Drive |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để chuyển quyền điều khiển sang Tự động (từ Operator sang Program) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để chuyển quyền điều khiển sang Bằng tay (từ Program sang Operator) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa cứng chế độ Program (không cho phép Operator giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Mở khóa chế độ Program (cho phép Operator lấy quyền điều khiển) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để trả về nguồn lệnh Bình thường mặc định (Normal: Program hoặc Operator) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Reset drive fault and all Alarms requiring Reset |
| **`PCmd_FailToStartAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Fail to Start Alarm |
| **`PCmd_FailToStartSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Fail to Start Alarm |
| **`PCmd_FailToStartUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Fail to Start Alarm |
| **`PCmd_FailToStartUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Fail to Start Alarm |
| **`PCmd_FailToStopAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Fail to Stop Alarm |
| **`PCmd_FailToStopSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Fail to Stop Alarm |
| **`PCmd_FailToStopUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Fail to Stop Alarm |
| **`PCmd_FailToStopUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Fail to Stop Alarm |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Interlock Trip Alarm |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Interlock Trip Alarm |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Interlock Trip Alarm |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Interlock Trip Alarm |
| **`PCmd_DriveFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Drive Fault Alarm |
| **`PCmd_DriveFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Drive Fault Alarm |
| **`PCmd_DriveFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Drive Fault Alarm |
| **`PCmd_DriveFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Drive Fault Alarm |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) I/O Fault Alarm |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) I/O Fault Alarm |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) I/O Fault Alarm |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) I/O Fault Alarm |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Release (Maintenance to Operator/Program/Override) |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Unlock Operator (allow Program to acquire) |
| **`XCmd_Start`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Start Drive Forward |
| **`XCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Stop Drive |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Acquire (Oper/Prog to Ext) |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Release (Ext to Oper/Prog) |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Reset Shed Latches, Cleared Alarms, Drive Fault |

## 3. Bảng chi tiết 111 Biến Đầu Ra (`Output Parameters`) của `P_PF6000`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Inp`** | `P_PF6000_Inp` | - | Hiện (Pin) | PowerFlex 6000 Input Assembly (<drivename>:I) |
| **`Out`** | `P_PF6000_Out` | - | Hiện (Pin) | PowerFlex 6000 Output Assembly (<drivename>:O) |
| **`Ref_FaultCodeList`** | `P_DescList[1]` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] Tag containing List of Fault Codes (DINT) and their Descriptions (STRING) |
| **`Out_Horn`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1 = Sound audible prior to commanded motor start |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final drive source and quality. GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`Val_SpeedRef`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Speed Reference (target) to drive |
| **`Val_SpeedFdbk`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Speed Feedback (actual) from drive |
| **`Val_SpeedRefRaw`** | `INT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Copy of Speed Reference Output (in Raw units) for faceplate |
| **`Val_SpeedFdbkRaw`** | `INT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Copy of Speed Feedback Input (in Raw units) for faceplate |
| **`Val_SpeedRefEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Minimum of Speed Reference = MIN (Cfg_SpeedFdbkEUMin, Cfg_SpeedFdbkEUMax) |
| **`Val_SpeedRefEUMax`** | `REAL` | `130` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Maximum of Speed Reference = MAX (Cfg_SpeedFdbkEUMin, Cfg_SpeedFdbkEUMax) |
| **`Val_SpeedFdbkEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Minimum of Speed Feedback = MIN (Cfg_SpeedFdbkEUMin, Cfg_SpeedFdbkEUMax) |
| **`Val_SpeedFdbkEUMax`** | `REAL` | `130` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Maximum of Speed Feedback = MAX (Cfg_SpeedFdbkEUMin, Cfg_SpeedFdbkEUMax) |
| **`Val_MotorCurrent`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Drive output current (Amps) |
| **`Val_MotorCurrentPct`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Motor Current (% Full Load Amps) |
| **`Val_MotorVoltage`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Motor Voltage (Volts) |
| **`Val_OutputPower`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Drive output power (kW) |
| **`Val_MotorPowerPct`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Motor Power (% Rated Power) |
| **`Val_TotalPowerPct`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Total Power (% Rated Power) |
| **`Val_MotorSpeedPct`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Motor Speed (% Nameplate Speed) |
| **`Val_Cmd`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device command: 0 = None 1 = Stop 2 = Start |
| **`Val_Fdbk`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device feedback: 0 = Stopped 1 = Running |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device confirmed status: 0 = None 1 = Stopped 2 = Running 6 = Stopping 7 = Starting 33 = Disabled |
| **`Val_Fault`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device fault status: 0 = None 16 = Fail to Start 17 = Fail to Stop 18 = Drive Fault 32 = I/O Fault 34 = Configuration error |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận của khối này cộng với khối kênh (enum) |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Current Object Owner ID (0 = not owned) |
| **`Sts_Stopped`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive requested to stop and is confirmed stopped |
| **`Sts_Starting`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive requested to run and awaiting run feedback |
| **`Sts_Running`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive requested to run and is confirmed running |
| **`Sts_Stopping`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive requested to stop and awaiting stopped feedback |
| **`Sts_Jogging`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive requested to Jog |
| **`Sts_Horn`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Valve Horn Active |
| **`Sts_NotReady`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive reports it is not ready to run |
| **`Sts_WatchdogTO`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive Comms Watchdog Timed Out (comm fail) |
| **`Sts_SpeedLimited`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Speed Reference Setting exceeds configured Max/Min limit |
| **`Sts_LogicSts`** | `INT` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Logic Status Word from Drive Input Assembly |
| **`Sts_Available`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Thiết bị chưa sẵn sàng hoạt động do: Both Drive and Bypass Are Selected |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bypassable Interlocks and Permissives are Bypassed |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bypassing Active (Bypassed or Maintenance) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Device Not Ready, see detail bits for reason |
| **`Nrdy_BypassVsDrive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Both Drive and Bypass Are Selected |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_DriveNR`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Drive Not Ready |
| **`Nrdy_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi thiết bị ngắt tải (yêu cầu nhấn Reset) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Khóa liên động không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi truyền thông I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Thiết bị đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Nrdy_NotRemote`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Remote Control Not Selected, Cannot Start |
| **`Nrdy_Perm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Cho phép khởi động không thỏa mãn (Permissive Not OK) |
| **`Nrdy_PrioStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang có lệnh Dừng ưu tiên từ Operator/External (yêu cầu nhấn Reset) |
| **`Nrdy_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Deivce Tripped (Drive Fault) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=An Alarm is Disabled or Shelved, display icon |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình (kiểm tra các bit Err_* chi tiết để biết nguyên nhân) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Invalid Check or Reset Pulse Time (use 0 to 2147483) |
| **`Err_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: simulation timer preset: use 0 to 2147483) |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thông số cảnh báo (Thời gian duy trì tối thiểu, thời gian Shelve, mức Severity) không hợp lệ |
| **`Err_FdbkEU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Speed Fdbk Engineering Units Min = Max |
| **`Err_RefLim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Speed Ref Limit Min > Max |
| **`Err_RefEU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Speed Ref Engineering Units Min = Max |
| **`Sts_Hand`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Hand đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn OoS, Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_OoS`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Out of Service đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_Maint`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Maintenance đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ovrd, Ext, Prog, Oper) |
| **`Sts_Ovrd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Override đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ext, Prog, Oper) |
| **`Sts_Ext`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = External đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Prog, Oper) |
| **`Sts_Prog`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Program đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_ProgLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Program đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Oper`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_OperLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Selection equals the Normal (Prog or Oper) |
| **`Sts_ExtReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = External request inhibited, cannot Get to External from current state |
| **`Sts_ProgReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Program request inhibited, cannot Get to Program from current state |
| **`Sts_MAcqRcvd`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Maintenance Acquire command received this scan |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = The drive, or a latched alarm or shed condition, is ready to be reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive Failed to Start (One-Shot) |
| **`Alm_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Drive Fail to Start Alarm |
| **`Ack_FailToStart`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Fail to Start Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_FailToStartDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Start Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_FailToStartShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Start Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_FailToStartSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Start Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive Failed to Stop |
| **`Alm_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Drive Fail to Stop Alarm |
| **`Ack_FailToStop`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Fail to Stop Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_FailToStopDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Stop Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_FailToStopShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Stop Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_FailToStopSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Stop Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive was stopped by an Interlock NOT OK (One-Shot) |
| **`Alm_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Alarm: Drive stopped by an Interlock NOT OK |
| **`Ack_IntlkTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Interlock Trip Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IntlkTripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock Trip Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IntlkTripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock Trip Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IntlkTripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock Trip Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_DriveFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive Fault (see drive display or manual) |
| **`Alm_DriveFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Alarm: Drive Fault (see drive display or manual) |
| **`Ack_DriveFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Drive Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_DriveFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_DriveFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_DriveFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] I/O Comm Fault Status (0 = OK, 1 = Bad) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = I/O Fault Alarm |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = I/O Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`MRdy_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`P_PF6000`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 44 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_PF6000`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'PF6000 MV Variable Frequency Drive'` | [Biến cấu hình thông số] Description for display on HMI. This string is shown in the title bar of the faceplate. |
| **`Cfg_FwdText`** | `STRING_16` | `' '` | [Biến cấu hình thông số] Name for Forward Direction (not used but required for global objects) |
| **`Cfg_Label`** | `STRING_20` | `'Motor Speed Control'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI. This string appears on the graphic symbol. |
| **`Cfg_RevText`** | `STRING_16` | `' '` | [Biến cấu hình thông số] Name for Reverse Direction (not used but required for global objects) |
| **`Cfg_SpeedFdbkEU`** | `STRING_8` | `'Hz'` | [Biến cấu hình thông số] Speed Feedback Engineering Units for display on HMI |
| **`Cfg_SpeedRefEU`** | `STRING_8` | `'Hz'` | [Biến cấu hình thông số] Speed Reference Engineering Units for display on HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_PF6000'` | [Biến cấu hình thông số] Tagname for display on HMI. This string is shown in the title bar of the faceplate. |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc quản lý lựa chọn nguồn lệnh điều khiển |
| **`DriveFault`** | `P_Alarm` | `{...}` | Drive Faulted Alarm |
| **`FailToStart`** | `P_Alarm` | `{...}` | Drive Failed to Start Alarm |
| **`FailToStop`** | `P_Alarm` | `{...}` | Drive Failed to Stop Alarm |
| **`HMI_FWMajor`** | `DINT` | `5` | [Biến nội bộ giao diện HMI Faceplate] Drive Firmware Major Revision (5, 6 supported) |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_PF6000'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | Interlock Trip Alarm |
| **`IOFault`** | `P_Alarm` | `{...}` | I/O Fault Alarm |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bypass all Bypassable Interlocks and Permissives |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Check (not bypass) all Interlocks and Permissives |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset Shed Latches, Cleared Alarms, Drive Fault |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset Shed Latches, Cleared Alarms, Drive Fault, and Xác nhận (Acknowledge) Alarms |
| **`OCmd_Start`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Start Drive |
| **`OCmd_Stop`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Stop Drive |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_SpeedRef`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OSet_SpeedRef (enables data entry field) |
| **`ORdy_Start`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Start (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Stop`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Stop (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`OSet_SpeedRef`** | `REAL` | `0` | [Giá trị đặt từ màn hình HMI Operator] Operator Setting of Speed Reference (Engineering Units) |
| **`Val_FaultDesc[0]`** | `STRING_40` | `‘Logic not yet scanned.’` | [Giá trị thực tế đầu ra (Logic/HMI)] Description of Drive Fault (given LastFaultCode) |
| **`Val_FaultDesc[1]`** | `STRING_40` | `"  (Empty string, no characters)` | [Giá trị thực tế đầu ra (Logic/HMI)] Description of Drive Fault (given LastFaultCode) |
| **`Val_FaultDesc[2]`** | `STRING_40` | `"  (Empty string, no characters)` | [Giá trị thực tế đầu ra (Logic/HMI)] Description of Drive Fault (given LastFaultCode) |
| **`Val_FaultDesc[3]`** | `STRING_40` | `"  (Empty string, no characters)` | [Giá trị thực tế đầu ra (Logic/HMI)] Description of Drive Fault (given LastFaultCode) |
| **`Val_FaultDesc[4]`** | `STRING_40` | `"  (Empty string, no characters)` | [Giá trị thực tế đầu ra (Logic/HMI)] Description of Drive Fault (given LastFaultCode) |
| **`Val_FaultDesc[5]`** | `STRING_40` | `"  (Empty string, no characters)` | [Giá trị thực tế đầu ra (Logic/HMI)] Description of Drive Fault (given LastFaultCode) |
| **`Val_FaultDesc[6]`** | `STRING_40` | `"  (Empty string, no characters)` | [Giá trị thực tế đầu ra (Logic/HMI)] Description of Drive Fault (given LastFaultCode) |
| **`Val_FaultDesc[7]`** | `STRING_40` | `"  (Empty string, no characters)` | [Giá trị thực tế đầu ra (Logic/HMI)] Description of Drive Fault (given LastFaultCode) |
| **`Val_FaultDesc[8]`** | `STRING_40` | `"  (Empty string, no characters)` | [Giá trị thực tế đầu ra (Logic/HMI)] Description of Drive Fault (given LastFaultCode) |
| **`Val_FaultDesc[9]`** | `STRING_40` | `"  (Empty string, no characters)` | [Giá trị thực tế đầu ra (Logic/HMI)] Description of Drive Fault (given LastFaultCode) |
| **`Val_LastFaultCode`** | `DINT[10]` | `{...}` | [Giá trị thực tế đầu ra (Logic/HMI)] Fault code numbers for active faults in drive (-1 = no fault) |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_PF7000`
**Thuộc chương:** Chương 3: Động cơ, Biến tần & Rơ-le Quá tải (Motors & Drives)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Motors 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_PF7000`:** **127** Biến đầu vào (`Input Parameters`) | **149** Biến đầu ra (`Output Parameters`) | **42** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_PF7000`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp`, `Out`, `Ref_GetParSrc`, `Ref_GetParMSG`, `Ref_GetParDest`, `Ref_DriveFaultMSG`, `Ref_DriveAlarmMSG`, `Ref_FaultAlarmDest`, `Ref_RunTimeMSG`, `Ref_RunTimeDest`, `Inp_FwdPermOK`, `Inp_FwdNBPermOK`, `Inp_RevPermOK`, `Inp_RevNBPermOK`, `Inp_IntlkOK`, `Inp_NBIntlkOK`, `Inp_IOFault`, `Inp_Sim`...
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_HasJog`, `Cfg_AllowLocal`, `Cfg_HasFwdPermObj`, `Cfg_HasRevPermObj`, `Cfg_HasIntlkObj`, `Cfg_HasMoreObj`, `Cfg_HasResInhObj`, `Cfg_HasRunTimeObj`, `Cfg_SetTrack`, `Cfg_SetTrackOvrdHand`, `Cfg_OperStopPrio`, `Cfg_ExtStopPrio`, `Cfg_OCmdResets`, `Cfg_XCmdResets`, `Cfg_OvrdPermIntlk`, `Cfg_ShedOnFailToStart`, `Cfg_ShedOnIOFault`, `Cfg_SimScaleEU`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_FwdText`, `Cfg_Label`, `Cfg_RevText`, `Cfg_SpeedFdbkEU`, `Cfg_SpeedRefEU`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_PF7000` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PSet_SpeedRef`, `PSet_Owner`, `PCmd_StartFwd`, `PCmd_StartRev`, `PCmd_Stop`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_FailToStartAck`, `PCmd_FailToStartSuppress`, `PCmd_FailToStartUnsuppress`, `PCmd_FailToStartUnshelve`, `PCmd_FailToStopAck`, `PCmd_FailToStopSuppress`, `PCmd_FailToStopUnsuppress`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Inp`, `Out`, `Ref_GetParSrc`, `Ref_GetParMSG`, `Ref_GetParDest`, `Ref_DriveFaultMSG`, `Ref_DriveAlarmMSG`, `Ref_FaultAlarmDest`, `Ref_RunTimeMSG`, `Ref_RunTimeDest`, `Out_Horn`, `Val_SpeedRef`, `Val_SpeedFdbk`, `Val_SpeedRefRaw`, `Val_SpeedFdbkRaw`, `Val_SpeedRefEUMin`, `Val_SpeedRefEUMax`, `Val_SpeedFdbkEUMin`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 127 Biến Đầu Vào (`Input Parameters`) của `P_PF7000`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp`** | `P_PF7000_Inp` | - | Hiện (Pin) | PowerFlex 7000 Input Assembly |
| **`Out`** | `P_PF7000_Out` | - | Hiện (Pin) | PowerFlex 7000 Output Assembly |
| **`Ref_GetParSrc`** | `INT[36]` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] PowerFlex 7000 Output Assembly |
| **`Ref_GetParMSG`** | `MESSAGE` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] MSG to Get Next Drive Parameter Value |
| **`Ref_GetParDest`** | `INT[36]` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] Buffer for data from Get Drive Parameter MSG |
| **`Ref_DriveFaultMSG`** | `MESSAGE` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] MSG to Get Last Fault Record |
| **`Ref_DriveAlarmMSG`** | `MESSAGE` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] MSG to Get Last Alarm Record |
| **`Ref_FaultAlarmDest`** | `P_PFComm_FltAlmRec` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] Buffer for data from Fault Record or Alarm Record MSG |
| **`Ref_RunTimeMSG`** | `MESSAGE` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] MSG to Get Elapsed Run Time |
| **`Ref_RunTimeDest`** | `LINT` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] Buffer for data from Get Elapsed Run Time MSG |
| **`Inp_FwdPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Permissives OK, drive can start Forward |
| **`Inp_FwdNBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Non-Bypassable Permissives OK, drive can start Forward |
| **`Inp_RevPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Permissives OK, drive can start Reverse |
| **`Inp_RevNBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Non-Bypassable Permissives OK, motor can start Reverse |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Interlocks OK, drive can start/run |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Non-Bypassable Interlocks OK, drive can start/run |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Input Communication Status 0 = OK, 1 = fail |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Simulation input. When set to 1, the instruction keeps outputs de-energized (zero) and simulates a working drive. When set to 0, the instruction operates the drive normally. |
| **`Inp_Hand`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Acquire Hand (typ. hardwired local) Mode, 0 = Release Hand Mode |
| **`Inp_Ovrd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Acquire Override (higher priority program logic), 0 = Release Override |
| **`Inp_OvrdCmd`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Override Mode Command: 0 = None, 1 = Stop, 2=Start Fwd 3=Start Rev |
| **`Inp_OvrdSpeed`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Value to set Speed Reference in Override Mode (SpeedRef Engineering Units) |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Cấm nguồn lệnh ngoài (External) chiếm quyền điều khiển; 0 = Cho phép External chiếm quyền. |
| **`Inp_HornInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Cấm còi báo động âm thanh; 0 = Cho phép phát còi báo trước khi khởi động |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Reset Shed Latches, Cleared Alarms, Drive Fault |
| **`Cfg_HasJog`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Drive Jog Command enabled/visible, 0 = Drive Jog Command not allowed |
| **`Cfg_AllowLocal`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Allow Local Start/Stop without alarm, 0 = Start/Stop from HMI/prog only |
| **`Cfg_HasFwdPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng forward permissive liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasRevPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng reverse permissive liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng interlock liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_HasResInhObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng restart inhibit liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasRunTimeObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng runtime object is connected and navigation to the runtime liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_SetTrack`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] This parameter is used to set up bumpless behavior of setting parameters when switching modes. When this parameter is 1, in Program mode the operator settings track the program settings; in Operator mode the program settings track the operator settings; and the simulation inputs match the output values (transitions are bumpless). When this parameter is 0, the operator settings and program settings are not modified by this instruction. In this case, when the mode is changed, the effective value of the setting can change depending on the program-set and operator-set values. |
| **`Cfg_SetTrackOvrdHand`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Program/Operator Settings track Override/Hand speed reference |
| **`Cfg_OperStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = OCmd_Stop any time, 0 = OCmd_Stop only when Operator selected |
| **`Cfg_ExtStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = XCmd_Stop any time, 0 = XCmd_Stop only when Ext selected |
| **`Cfg_OCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = New drive OCmd resets shed latches and cleared alarms, 0 = OCmdReset req'd. |
| **`Cfg_XCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = New drive XCmd resets shed latches and cleared alarms, 0 = XCmdReset req'd. |
| **`Cfg_OvrdPermIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Chế độ Override bỏ qua các liên động/điều kiện cho phép có thể bypass; 0 = Luôn tuân thủ mọi liên động. |
| **`Cfg_ShedOnFailToStart`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Stop motor and alarm on Fail to Start. 0 = Alarm only on Fail to Start. IMPORTANT: If a condition is configured to shed the device to the Off state on a fault, a reset is required to clear the shed fault to command the drive to a state other than Off. |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Stop motor and alarm on I/O Fault. 0 = Alarm only on I/O Fault. IMPORTANT: If a condition is configured to shed the device to the Off state on a fault, a reset is required to clear the shed fault to command the drive to a state other than Off. |
| **`Cfg_SimScaleEU`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = In simulation, scale Speed Ref Engineering Units to Speed Fdbk Engineering Units |
| **`Cfg_SimScaleRaw`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = In simulation, scale Speed Ref Engineering Units to raw, then raw to Speed Fdbk Engineering Units |
| **`Cfg_DecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Number of decimal places for speed ref/fdbk display (0. .6) |
| **`Cfg_HasFailToStartAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasFailToStopAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasDriveFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_FailToStartResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_FailToStopResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_DriveFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_FailToStartAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_FailToStopAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_DriveFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_FailToStartSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the severity of each alarm that gauges the color and symbol that are used to indicate alarm status on the faceplate and global object. The following are valid values: 1…250 = Low 251…500 = Medium 501…750 = High 751…1000 = Urgent IMPORTANT: These severity priorities drive only the indication on the global object and faceplate. The Alarm and Events definition severity drives the color and symbol that is used on the alarm banner and alarm summary as well as the value returned by the FactoryTalk Alarm and Events software display commands. |
| **`Cfg_FailToStopSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the severity of each alarm that gauges the color and symbol that are used to indicate alarm status on the faceplate and global object. The following are valid values: 1…250 = Low 251…500 = Medium 501…750 = High 751…1000 = Urgent IMPORTANT: These severity priorities drive only the indication on the global object and faceplate. The Alarm and Events definition severity drives the color and symbol that is used on the alarm banner and alarm summary as well as the value returned by the FactoryTalk Alarm and Events software display commands. |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the severity of each alarm that gauges the color and symbol that are used to indicate alarm status on the faceplate and global object. The following are valid values: 1…250 = Low 251…500 = Medium 501…750 = High 751…1000 = Urgent IMPORTANT: These severity priorities drive only the indication on the global object and faceplate. The Alarm and Events definition severity drives the color and symbol that is used on the alarm banner and alarm summary as well as the value returned by the FactoryTalk Alarm and Events software display commands. |
| **`Cfg_DriveFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the severity of each alarm that gauges the color and symbol that are used to indicate alarm status on the faceplate and global object. The following are valid values: 1…250 = Low 251…500 = Medium 501…750 = High 751…1000 = Urgent IMPORTANT: These severity priorities drive only the indication on the global object and faceplate. The Alarm and Events definition severity drives the color and symbol that is used on the alarm banner and alarm summary as well as the value returned by the FactoryTalk Alarm and Events software display commands. |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] These parameters determine the severity of each alarm that gauges the color and symbol that are used to indicate alarm status on the faceplate and global object. The following are valid values: 1…250 = Low 251…500 = Medium 501…750 = High 751…1000 = Urgent IMPORTANT: These severity priorities drive only the indication on the global object and faceplate. The Alarm and Events definition severity drives the color and symbol that is used on the alarm banner and alarm summary as well as the value returned by the FactoryTalk Alarm and Events software display commands. |
| **`Cfg_MinSpdRef`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum Speed Reference in Engineering Units (for limiting) |
| **`Cfg_MaxSpdRef`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Maximum Speed Reference in Engineering Units (for limiting) |
| **`Cfg_SpeedRefRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Reference Minimum in Drive (raw) Units (for scaling) |
| **`Cfg_SpeedRefRawMax`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Reference Maximum in Drive (raw) Units (for scaling) |
| **`Cfg_SpeedRefEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Reference Minimum in Engineering Units (for scaling) |
| **`Cfg_SpeedRefEUMax`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Reference Maximum in Engineering Units (for scaling) |
| **`Cfg_SpeedFdbkRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Feedback Minimum in Drive (raw) Units (for scaling) |
| **`Cfg_SpeedFdbkRawMax`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Feedback Maximum in Drive (raw) Units (for scaling) |
| **`Cfg_SpeedFdbkEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Feedback Minimum in Engineering Units (for scaling) |
| **`Cfg_SpeedFdbkEUMax`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Feedback Maximum in Engineering Units (for scaling) |
| **`Cfg_StartHornT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian hú còi cảnh báo tại hiện trường trước khi đóng điện khởi động động cơ (giây, 0 = tắt còi). |
| **`Cfg_SimRampT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Time to ramp speed fdbk when in Simulation (seconds) |
| **`Cfg_FailToStartT`** | `REAL` | `15` | Ẩn (Tag) | [Biến cấu hình thông số] Time after Start to get Run Feedback before Fault (seconds) |
| **`Cfg_FailToStopT`** | `REAL` | `15` | Ẩn (Tag) | [Biến cấu hình thông số] Time after Stop to drop Run Feedback before Fault (seconds) |
| **`Cfg_ResetPulseT`** | `REAL` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Time to pulse Out_Reset to clear drive fault |
| **`Cfg_MaxJogT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian giữ lệnh nhấp thử (Jog) tối đa cho phép (giây, 0 = không giới hạn). |
| **`Cfg_OperKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Operator keeps control in Program or Ext: .0 = Reference, .1 = Commands |
| **`Cfg_ProgKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Program keeps control in Operator or Ext: .0 = Reference, .1 = Commands |
| **`Cfg_ExtKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Ext keeps control in Program or Oper: .0 = Reference, .1 = Commands |
| **`PSet_SpeedRef`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Program Setting of Speed Reference (Engineering Units) |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Mã định danh chủ sở hữu chương trình (Program Owner ID: khác 0 để đăng ký chiếm quyền, bằng 0 để nhả quyền). |
| **`XSet_SpeedRef`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài (External)] External Setting of Speed Reference (Engineering Units) |
| **`PCmd_StartFwd`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Start Drive Forward |
| **`PCmd_StartRev`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Start Drive Reverse |
| **`PCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Stop Drive |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để chuyển quyền điều khiển sang Tự động (từ Operator sang Program) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để chuyển quyền điều khiển sang Bằng tay (từ Program sang Operator) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa cứng chế độ Program (không cho phép Operator giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Mở khóa chế độ Program (cho phép Operator lấy quyền điều khiển) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để trả về nguồn lệnh Bình thường mặc định (Normal: Program hoặc Operator) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Reset Shed Latches, Cleared Alarms, Drive Fault |
| **`PCmd_FailToStartAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Fail to Start Alarm |
| **`PCmd_FailToStartSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Fail to Start Alarm |
| **`PCmd_FailToStartUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Fail to Start Alarm |
| **`PCmd_FailToStartUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Fail to Start Alarm |
| **`PCmd_FailToStopAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Fail to Stop Alarm |
| **`PCmd_FailToStopSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Fail to Stop Alarm |
| **`PCmd_FailToStopUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Fail to Stop Alarm |
| **`PCmd_FailToStopUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Fail to Stop Alarm |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Interlock Trip Alarm |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Interlock Trip Alarm |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Interlock Trip Alarm |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Interlock Trip Alarm |
| **`PCmd_DriveFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Drive Fault Alarm |
| **`PCmd_DriveFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Drive Fault Alarm |
| **`PCmd_DriveFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Drive Fault Alarm |
| **`PCmd_DriveFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Drive Fault Alarm |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) I/O Fault Alarm |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) I/O Fault Alarm |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) I/O Fault Alarm |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) I/O Fault Alarm |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Release (Maintenance to Operator/Program/Override) |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Unlock Operator (allow Program to acquire) |
| **`XCmd_StartFwd`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Start Drive Forward |
| **`XCmd_StartRev`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Start Drive Reverse |
| **`XCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Stop Drive |
| **`XCmd_JogFwd`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Jog Drive Forward (not cleared by P_VSD!) |
| **`XCmd_JogRev`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Jog Drive Reverse (not cleared by P_VSD!) |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Acquire (Oper/Prog to Ext) |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Release (Ext to Oper/Prog) |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Reset Shed Latches, Cleared Alarms, Drive Fault |

## 3. Bảng chi tiết 149 Biến Đầu Ra (`Output Parameters`) của `P_PF7000`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Inp`** | `P_PF7000_Inp` | - | Hiện (Pin) | PowerFlex 7000 Input Assembly |
| **`Out`** | `P_PF7000_Out` | - | Hiện (Pin) | PowerFlex 7000 Output Assembly |
| **`Ref_GetParSrc`** | `INT[36]` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] PowerFlex 7000 Output Assembly |
| **`Ref_GetParMSG`** | `MESSAGE` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] MSG to Get Next Drive Parameter Value |
| **`Ref_GetParDest`** | `INT[36]` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] Buffer for data from Get Drive Parameter MSG |
| **`Ref_DriveFaultMSG`** | `MESSAGE` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] MSG to Get Last Fault Record |
| **`Ref_DriveAlarmMSG`** | `MESSAGE` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] MSG to Get Last Alarm Record |
| **`Ref_FaultAlarmDest`** | `P_PFComm_FltAlmRec` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] Buffer for data from Fault Record or Alarm Record MSG |
| **`Ref_RunTimeMSG`** | `MESSAGE` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] MSG to Get Elapsed Run Time |
| **`Ref_RunTimeDest`** | `LINT` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] Buffer for data from Get Elapsed Run Time MSG |
| **`Out_Horn`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1 = Sound audible prior to commanded motor start |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final drive signal source and quality. GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`Val_SpeedRef`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Speed Reference (target) to drive |
| **`Val_SpeedFdbk`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Speed Feedback (actual) from drive (Hz) |
| **`Val_SpeedRefRaw`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Copy of Speed Reference Output (in Raw units) for faceplate |
| **`Val_SpeedFdbkRaw`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Copy of Speed Feedback Input (in Raw units) for faceplate |
| **`Val_SpeedRefEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Minimum of Speed Reference = MIN (Cfg_SpeedFdbkEUMin, Cfg_SpeedFdbkEUMax) |
| **`Val_SpeedRefEUMax`** | `REAL` | `130` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Maximum of Speed Reference = MAX (Cfg_SpeedFdbkEUMin, Cfg_SpeedFdbkEUMax) |
| **`Val_SpeedFdbkEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Minimum of Speed Feedback = MIN (Cfg_SpeedFdbkEUMin, Cfg_SpeedFdbkEUMax) |
| **`Val_SpeedFdbkEUMax`** | `REAL` | `130` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Maximum of Speed Feedback = MAX (Cfg_SpeedFdbkEUMin, Cfg_SpeedFdbkEUMax) |
| **`Val_SpeedFdbkRPM`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Motor Speed Feedback (RPM) (par 363) (datalink) |
| **`Val_TorquePctUnfilt`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Motor Torque Feedback, unfiltered (%) (par 489) (datalink) |
| **`Val_MotorTorquePct`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Motor Torque (%) (par 345) |
| **`Val_MotorCurrent`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Motor Current (Amps) (par 361) |
| **`Val_MotorCurrentPct`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Motor Current (% FLA) (par 340) (datalink) |
| **`Val_MotorVolts`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Motor Voltage, filtered (Volts) (par 362) (datalink) |
| **`Val_MotorVoltsPct`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Motor Voltage (% of NP Volts) (par 344) |
| **`Val_MotorPower`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Drive Output Power, filtered (kW) (par 364) |
| **`Val_MotorPowerPct`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Motor Air-Gap Power (%) (par 346) (datalink) |
| **`Val_MotorOvldPct`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Motor Overload Count (%) (par 550) |
| **`Val_DriveOvldPct`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Drive Overload Count (%) (par 551) |
| **`Val_RectTemp`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Rectivier Heatsink Temperature (C) (par 254) |
| **`Val_InvTemp`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Inverter Heatsink Temperature (C) (par 252) |
| **`Val_RectVoltsPct`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Measured Voltage at Input of Rectifier Bridge (%) (par 696) |
| **`Val_LineVolts`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Measured Voltage at Input of Rectifier Bridge (Volts) (par 324) |
| **`Val_LineVoltsPct`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Estimated Line Input Voltage (before inductor) (%) (par 135) |
| **`Val_LineCurrent`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Measured Drive Input Current (A) (par 500) |
| **`Val_LineFreq`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Line Frequency (Hz) (par 657) |
| **`Val_LinePower`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Real Power Consumption by the Drive (kW) (par 753) |
| **`Val_LinePowerPct`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Real (Active) Power at Input of the Drive (%) (par 902) |
| **`Val_TotRunTime`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Total Drive Elapsed Run Time (hr) |
| **`Val_LastFaultCode`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Last Drive Fault code (enum) |
| **`Val_LastAlarmCode`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Last Drive Alarm code (enum) |
| **`Val_Cmd`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device command: 0 = None 1 = Stop 2 = Start forward 3 = Start reverse 4 = Jog forward 5 = Jog reverse |
| **`Val_Fdbk`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device feedback: 0 = Stopped 1 = Running forward 2 = Running reverse 3 = Accelerating 4 = Decelerating |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device confirmed status: 0 = None 1 = Stopped 2 = Running forward 3 = Running reverse 4 = Jogging forward 5 = Jogging reverse 6 = Stopping 7 = Starting forward 8 = Starting reverse 33 = Disabled |
| **`Val_Fault`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Device fault status: 0 = None 16 = Fail to Start 17 = Fail to Stop 18 = Drive Fault 32 = I/O Fault 34 = Configuration error |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận của khối này cộng với khối kênh (enum) |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Current Object Owner ID (0 = not owned) |
| **`Sts_Stopped`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive requested to stop and is confirmed stopped |
| **`Sts_StartingFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive requested to run forward and awaiting run feedback |
| **`Sts_StartingRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive requested to run reverse and awaiting run feedback |
| **`Sts_RunningFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive requested to run and is confirmed running forward |
| **`Sts_RunningRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive requested to run and is confirmed running reverse |
| **`Sts_StoppingFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive running forward requested to stop and awaiting stopped feedback |
| **`Sts_StoppingRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive running reverse requested to stop and awaiting stopped feedback |
| **`Sts_JoggingFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive requested to Jog Forward |
| **`Sts_JoggingRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive requested to Jog Reverse |
| **`Sts_Horn`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=Valve Horn Active |
| **`Sts_CommandDir`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive commanded to Forward, 0 = Reverse |
| **`Sts_ActualDir`** | `BOOL` | `1` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive Actual direction is Forward, 0 = Reverse |
| **`Sts_Accel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive is Accelerating |
| **`Sts_Decel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive is Decelerating |
| **`Sts_NotReady`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Device Not Ready, see detail bits for reason |
| **`Sts_DriveAlarm`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive has an Alarm (see drive display or manual) |
| **`Sts_AtSpeed`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive is running at reference speed |
| **`Sts_SpeedLimited`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Speed Reference Setting exceeds configured Max/Min limit |
| **`Sts_LogicSts`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Drive Logic Status Word (from Input Assembly) |
| **`Sts_DriveSts1`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Drive Status Flag Word #1 (par 569) |
| **`Sts_DriveSts2`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Drive Status Flag Word #2 (par 238) |
| **`Sts_DriveNotRdy1`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Drive Not Ready Flag Word #1 (par 262) |
| **`Sts_DriveNotRdy2`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Drive Not Ready Flag Word #2 (par 699) |
| **`Sts_Contactors`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Contactor Status (par 506) |
| **`Sts_HdwOpts`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Hardware Options (par 141) |
| **`Sts_SpecialFeat1`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Special Features 1 (par 99) |
| **`Sts_Available`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive available for control by automation (Prog) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bypassable Interlocks and Permissives are Bypassed |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bypassing Active (Bypassed or Maintenance) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Motor is Not Ready to Run (independent of Mode) |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_DriveNR`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Drive Not Ready |
| **`Nrdy_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi thiết bị ngắt tải (yêu cầu nhấn Reset) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Khóa liên động không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi truyền thông I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Thiết bị đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Nrdy_Perm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Cho phép khởi động không thỏa mãn (Permissive Not OK) |
| **`Nrdy_PrioStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang có lệnh Dừng ưu tiên từ Operator/External (yêu cầu nhấn Reset) |
| **`Nrdy_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Device not Ready: Deivce Tripped (Drive Fault) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=An Alarm is Disabled or Shelved, display icon |
| **`Sts_MsgErr`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Message Error, unable to read at least one non-RPI drive parameter |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình (kiểm tra các bit Err_* chi tiết để biết nguyên nhân) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Invalid Check or Reset Pulse Time (use 0 to 2147483) |
| **`Err_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: simulation timer preset: use 0 to 2147483) |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thông số cảnh báo (Thời gian duy trì tối thiểu, thời gian Shelve, mức Severity) không hợp lệ |
| **`Err_FdbkEU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Speed Fdbk Engineering Units Min = Max |
| **`Err_RefLim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Speed Ref Limit Min > Max |
| **`Err_RefEU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Speed Ref Engineering Units Min = Max |
| **`Sts_Hand`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Hand đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn OoS, Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_OoS`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Out of Service đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_Maint`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Maintenance đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ovrd, Ext, Prog, Oper) |
| **`Sts_Ovrd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Override đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ext, Prog, Oper) |
| **`Sts_Ext`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = External đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Prog, Oper) |
| **`Sts_Prog`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Program đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_ProgLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Program đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Oper`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_OperLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Selection equals the Normal (Prog or Oper) |
| **`Sts_ExtReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = External request inhibited, cannot Get to External from current state |
| **`Sts_ProgReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Program request inhibited, cannot Get to Program from current state |
| **`Sts_MAcqRcvd`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Maintenance Acquire command received this scan |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = The drive, or a latched alarm or shed condition, is ready to be reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive Failed to Start (One-Shot) |
| **`Alm_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Drive Fail to Start Alarm |
| **`Ack_FailToStart`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Fail to Start Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_FailToStartDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Start Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_FailToStartShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Start Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_FailToStartSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Start Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive Failed to Stop |
| **`Alm_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Drive Fail to Stop Alarm |
| **`Ack_FailToStop`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Fail to Stop Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_FailToStopDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Stop Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_FailToStopShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Stop Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_FailToStopSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Stop Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive was stopped by an Interlock NOT OK (One-Shot) |
| **`Alm_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Alarm: Drive stopped by an Interlock NOT OK |
| **`Ack_IntlkTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Interlock Trip Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IntlkTripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock Trip Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IntlkTripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock Trip Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IntlkTripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock Trip Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_DriveFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive Fault (see drive display or manual) |
| **`Alm_DriveFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Alarm: Drive Fault (see drive display or manual) |
| **`Ack_DriveFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Drive Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_DriveFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_DriveFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_DriveFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] I/O Comm Fault Status (0 = OK, 1 = Bad) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = I/O Fault Alarm |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = I/O Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`MRdy_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Ready to receive MCmd_Disable (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Ready to receive MCmd_Enable (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`P_PF7000`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 42 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_PF7000`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'PowerFlex 7000 Variable Frequency Drive'` | [Biến cấu hình thông số] Description for display on HMI. This string is shown in the title bar of the faceplate. |
| **`Cfg_FwdText`** | `STRING_16` | `'Forward'` | [Biến cấu hình thông số] Name for Forward Direction, e.g., "Up", "Forward" |
| **`Cfg_Label`** | `STRING_20` | `'Motor Speed Control'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI. This string appears on the graphic symbol. |
| **`Cfg_RevText`** | `STRING_16` | `'Reverse'` | [Biến cấu hình thông số] Name for Reverse Direction, e.g., "Down", "Reverse" |
| **`Cfg_SpeedFdbkEU`** | `STRING_8` | `'Hz'` | [Biến cấu hình thông số] Speed Feedback Engineering Units for display on HMI |
| **`Cfg_SpeedRefEU`** | `STRING_8` | `'Hz'` | [Biến cấu hình thông số] Speed Reference Engineering Units for display on HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_PF7000'` | [Biến cấu hình thông số] Tagname for display on HMI. This string is shown in the title bar of the faceplate. |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc quản lý lựa chọn nguồn lệnh điều khiển |
| **`DriveFault`** | `P_Alarm` | `{...}` | Drive Faulted Alarm |
| **`FailToStart`** | `P_Alarm` | `{...}` | Drive Failed to Start Alarm |
| **`FailToStop`** | `P_Alarm` | `{...}` | Drive Failed to Stop Alarm |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_PF7000'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | Interlock Trip Alarm |
| **`IOFault`** | `P_Alarm` | `{...}` | I/O Fault Alarm |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bypass all Bypassable Interlocks and Permissives |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Check (not bypass) all Interlocks and Permissives |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`OCmd_JogFwd`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Jog Drive Forward (not cleared by P_VSD!) |
| **`OCmd_JogRev`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Jog Drive Reverse (not cleared by P_VSD!) |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset Shed Latches, Cleared Alarms, Drive Fault |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset Shed Latches, Cleared Alarms, Drive Fault, and Xác nhận (Acknowledge) Alarms |
| **`OCmd_StartFwd`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Start Drive Forward |
| **`OCmd_StartRev`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Start Drive Reverse |
| **`OCmd_Stop`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Stop Drive |
| **`ORdy_JogFwd`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_JogFwd (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_JogRev`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_JogRev (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_SpeedRef`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OSet_SpeedRef (enables data entry field) |
| **`ORdy_StartFwd`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_StartFwd (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_StartRev`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_StartRev (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Stop`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Stop (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`OSet_SpeedRef`** | `REAL` | `0` | [Giá trị đặt từ màn hình HMI Operator] Operator Setting of Speed Reference (Engineering Units) |
| **`Val_AlarmDesc`** | `STRING_16` | `''` | [Giá trị thực tế đầu ra (Logic/HMI)] Description of Drive Alarm (given LastAlarmCode) |
| **`Val_AlarmDT`** | `DateTime` | `{...}` | [Giá trị thực tế đầu ra (Logic/HMI)] Date and Time last Drive Alarm occurred |
| **`Val_FaultDesc`** | `STRING_16` | `‘Not yet scanned.’` | [Giá trị thực tế đầu ra (Logic/HMI)] Description of Drive Fault (given LastFaultCode) |
| **`Val_FaultDT`** | `DateTime` | `{...}` | [Giá trị thực tế đầu ra (Logic/HMI)] Date and Time last Drive Fault occurred |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_E1PlusE`
**Thuộc chương:** Chương 3: Động cơ, Biến tần & Rơ-le Quá tải (Motors & Drives)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Motors 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_E1PlusE`:** **38** Biến đầu vào (`Input Parameters`) | **49** Biến đầu ra (`Output Parameters`) | **20** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_E1PlusE`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp`, `Inp_TripReset`, `Inp_IOFault`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_AllowOperReset`, `Cfg_AllowMaintReset`, `Cfg_HasWarnAlm`, `Cfg_HasTripAlm`, `Cfg_HasMoreObj`, `Cfg_HasIOFaultAlm`, `Cfg_WarnResetReqd`, `Cfg_TripResetReqd`, `Cfg_IOFaultResetReqd`, `Cfg_WarnAckReqd`, `Cfg_TripAckReqd`, `Cfg_IOFaultAckReqd`, `Cfg_WarnSeverity`, `Cfg_TripSeverity`, `Cfg_IOFaultSeverity`, `Cfg_PulseT`, `Cfg_LocalResetReqdT`, `Cfg_LocalResetReqdNum`
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_Inp1Txt`, `Cfg_Inp2Txt`, `Cfg_Label`, `Cfg_OutATxt`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_E1PlusE` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PCmd_TripReset`, `PCmd_Reset`, `PCmd_WarnAck`, `PCmd_WarnSuppress`, `PCmd_WarnUnsuppress`, `PCmd_WarnUnshelve`, `PCmd_TripAck`, `PCmd_TripSuppress`, `PCmd_TripUnsuppress`, `PCmd_TripUnshelve`, `PCmd_IOFaultAck`, `PCmd_IOFaultSuppress`, `PCmd_IOFaultUnsuppress`, `PCmd_IOFaultUnshelve`
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Inp`, `Out_TripReset`, `Val_AvgPctFLA`, `Val_PctTherm`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Sts_WarnBits`, `Sts_TripBits`, `Sts_Inp1`, `Sts_Inp2`, `Sts_OutA`, `Sts_MotorCurr`, `Sts_LocalResetReqd`, `Sts_TripLog0`, `Sts_TripLog1`, `Sts_TripLog2`, `Sts_TripLog3`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 38 Biến Đầu Vào (`Input Parameters`) của `P_E1PlusE`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp`** | `P_E1PlusE_Inp` | - | Hiện (Pin) | E1Plus Overload (193-ETN) Diagnostic Input Structure |
| **`Inp_TripReset`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Send Trip Reset request to Overload Relay |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = I/O communication with E1Plus has failed |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Reset All Latched Alarms |
| **`Cfg_AllowOperReset`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Trip Reset function is available to operator |
| **`Cfg_AllowMaintReset`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Trip Reset function is available to Maintenance |
| **`Cfg_HasWarnAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Warning Alarm exists and will be checked |
| **`Cfg_HasTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Trip Alarm exists and will be checked |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = I/O Fault Alarm exists and will be checked |
| **`Cfg_WarnResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_TripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_WarnAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_TripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_WarnSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_TripSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_PulseT`** | `REAL` | `5` | Ẩn (Tag) | [Biến cấu hình thông số] Time (seconds) to pulse Trip Reset or Remote Trip output to E3 |
| **`Cfg_LocalResetReqdT`** | `REAL` | `3600` | Ẩn (Tag) | [Biến cấu hình thông số] No more than (1,2,3) remote Trip Resets are allowed in this amount of time (seconds) |
| **`Cfg_LocalResetReqdNum`** | `DINT` | `3` | Ẩn (Tag) | [Biến cấu hình thông số] No more than this many remote Trip Resets are allowed in (cfg'd) time |
| **`PCmd_TripReset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để send Trip Reset request to Overload Relay |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Reset all Alarms requiring Reset |
| **`PCmd_WarnAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Impending Trip Warning alarm |
| **`PCmd_WarnSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Impending Trip Warning alarm |
| **`PCmd_WarnUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Impending Trip Warning alarm |
| **`PCmd_WarnUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Impending Trip Warning alarm |
| **`PCmd_TripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Overload Tripped alarm |
| **`PCmd_TripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Overload Tripped alarm |
| **`PCmd_TripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Overload Tripped alarm |
| **`PCmd_TripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Overload Tripped alarm |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) I/O Fault alarm |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) I/O Fault alarm |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) I/O Fault alarm |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Program Comnmand to Gỡ bỏ tạm hoãn (Unshelve) I/O Fault alarm |
| **`Sts_TripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Overload Trip Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |

## 3. Bảng chi tiết 49 Biến Đầu Ra (`Output Parameters`) của `P_E1PlusE`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Inp`** | `P_E1PlusE_Inp` | - | Hiện (Pin) | E1Plus Overload (193-ETN) Diagnostic Input Structure |
| **`Out_TripReset`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1 = Reset Overload Trip |
| **`Val_AvgPctFLA`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Average Current as Percent Full Load Amps (%) |
| **`Val_PctTherm`** | `INT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Motor Thermal Usage (%) (Trip / Fault at 100%) |
| **`SrcQ_IO`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final device status source and quality: GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`Val_Fault`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Fault Status 0 = None, 15=Warning, 30 = Tripped, 31 = Local Reset Reqd, 32=I/O Fault, 34=Config Error |
| **`Val_NotifyAll`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận của khối này cộng với khối kênh (enum) |
| **`Val_UnackAlmC`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Sts_WarnBits`** | `INT` | `2#0000_0000_0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Warning Reason Bits from Overload Relay |
| **`Sts_TripBits`** | `INT` | `2#0000_0000_0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trip Reason Bits from Overload Relay |
| **`Sts_Inp1`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Status of discrete input #1 |
| **`Sts_Inp2`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Status of discrete input #2 |
| **`Sts_OutA`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Status of discrete output A |
| **`Sts_MotorCurr`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Motor Current Status 1 = Current Present (active) |
| **`Sts_LocalResetReqd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Too many remote resets: go to starter, find cause, and reset there |
| **`Sts_TripLog0`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Trip Log 0 (cause of most recent trip) |
| **`Sts_TripLog1`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Trip Log 1 (cause of next most recent trip) |
| **`Sts_TripLog2`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Trip Log 2 |
| **`Sts_TripLog3`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Trip Log 3 |
| **`Sts_TripLog4`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Trip Log 4 |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Device Not Ready, see detail bits for reason |
| **`Nrdy_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Tripped (at device or by command) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi truyền thông I/O (yêu cầu nhấn Reset) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=An Alarm is Disabled or Shelved, display icon |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình (kiểm tra các bit Err_* chi tiết để biết nguyên nhân) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Reset pulse timer preset (use 0 to 2147483) |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Alarm Min On Time or Severity |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc lỗi ngắt tải (Shed) đã hết điều kiện lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_Warn`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Warning of impending trip (See Sts_WarnBits for reason) |
| **`Alm_Warn`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Alarm: warning of impending trip |
| **`Ack_Warn`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Trip Warning Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_WarnDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Trip Warning Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_WarnShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Trip Warning Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_WarnSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Trip Warning Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_Trip`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Overload Tripped (See Sts_TripBits for reason) |
| **`Alm_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Alarm: Overload Tripped |
| **`Ack_Trip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Overload Trip Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_TripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Overload Trip Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_TripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Overload Trip Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IOFault`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] I/O Comm Fault Status (0 = OK, 1 = Bad) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = I/O Fault Alarm |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = I/O Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`P_E1PlusE`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 20 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_E1PlusE`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'Motor Overload Relay'` | [Biến cấu hình thông số] Description for display on HMI. This string is shown in the title bar of the faceplate. |
| **`Cfg_Inp1Txt`** | `STRING_20` | `'Run Feedback'` | [Biến cấu hình thông số] Text for Input #1 label |
| **`Cfg_Inp2Txt`** | `STRING_20` | `''` | [Biến cấu hình thông số] Text for Input #2 label |
| **`Cfg_Label`** | `STRING_20` | `'Overload Relay'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI. This string appears on the graphic symbol. |
| **`Cfg_OutATxt`** | `STRING_20` | `'Starter Energized'` | [Biến cấu hình thông số] Text for Output A label |
| **`Cfg_Tag`** | `STRING_20` | `'P_E1PlusE'` | [Biến cấu hình thông số] Tagname for display on HMI. This string is shown in the title bar of the faceplate. |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_E1PlusE'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate |
| **`IOFault`** | `P_Alarm` | `{...}` | Alarm: I/O Fault |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset all Alarms requiring Reset |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset all Alarms and latched Shed conditions |
| **`OCmd_TripReset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để send Trip Reset request to Overload Relay |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Ready to receive OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Ready to receive OCmd_ResetAckAll (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_TripReset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Ready to receive OCmd_TripReset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`Trip`** | `P_Alarm` | `{...}` | Alarm: Overload has tripped |
| **`Val_Notify`** | `SINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |
| **`Warn`** | `P_Alarm` | `{...}` | Alarm: Warning of impending trip |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_E3Ovld`
**Thuộc chương:** Chương 3: Động cơ, Biến tần & Rơ-le Quá tải (Motors & Drives)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Motors 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_E3Ovld`:** **43** Biến đầu vào (`Input Parameters`) | **49** Biến đầu ra (`Output Parameters`) | **27** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_E3Ovld`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp`, `Inp_Trip`, `Inp_TripReset`, `Inp_IOFault`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_E3Plus`, `Cfg_EC5`, `Cfg_AllowOperTrip`, `Cfg_AllowOperReset`, `Cfg_AllowMaintTrip`, `Cfg_HasMoreObj`, `Cfg_AllowMaintReset`, `Cfg_HasWarnAlm`, `Cfg_HasTripAlm`, `Cfg_HasIOFaultAlm`, `Cfg_WarnResetReqd`, `Cfg_TripResetReqd`, `Cfg_IOFaultResetReqd`, `Cfg_WarnAckReqd`, `Cfg_TripAckReqd`, `Cfg_IOFaultAckReqd`, `Cfg_WarnSeverity`, `Cfg_TripSeverity`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_Inp1Txt`, `Cfg_Inp2Txt`, `Cfg_Inp3Txt`, `Cfg_Inp4Txt`, `Cfg_Inp5Txt`, `Cfg_Inp6Txt`, `Cfg_Label`, `Cfg_OutATxt`, `Cfg_OutBTxt`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_E3Ovld` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PCmd_Trip`, `PCmd_TripReset`, `PCmd_Reset`, `PCmd_WarnAck`, `PCmd_WarnSuppress`, `PCmd_WarnUnsuppress`, `PCmd_WarnUnshelve`, `PCmd_TripAck`, `PCmd_TripSuppress`, `PCmd_TripUnsuppress`, `PCmd_TripUnshelve`, `PCmd_IOFaultAck`, `PCmd_IOFaultSuppress`, `PCmd_IOFaultUnsuppress`, `PCmd_IOFaultUnshelve`
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Inp`, `Out_TripReset`, `Out_RemoteTrip`, `Val_AvgPctFLA`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`, `Sts_WarnBits`, `Sts_TripBits`, `Sts_Inp1`, `Sts_Inp2`, `Sts_Inp3`, `Sts_Inp4`, `Sts_Inp5`, `Sts_Inp6`, `Sts_OutA`, `Sts_OutB`, `Sts_LocalResetReqd`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 43 Biến Đầu Vào (`Input Parameters`) của `P_E3Ovld`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp`** | `P_E3Ovld_Inp` | - | Hiện (Pin) | E3 Overload Parameter-based Input Assembly (100) |
| **`Inp_Trip`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Send Remote Trip request to Overload Relay |
| **`Inp_TripReset`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Send Trip Reset request to Overload Relay |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = I/O communication with E1Plus has failed |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Reset All Latched Alarms |
| **`Cfg_E3Plus`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 0 = E3 (2 in, 1 out) 1 = E3Plus (-EC2, -EC3: 4 in, 2 out) |
| **`Cfg_EC5`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 0 = -EC2,-EC3 (4 in, 2 out) 1 = -EC5 (6 in, 2 out) |
| **`Cfg_AllowOperTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Remote Trip function is available to Operator |
| **`Cfg_AllowOperReset`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Trip Reset function is available to operator |
| **`Cfg_AllowMaintTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Remote Trip function is available to Maintenance |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_AllowMaintReset`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Trip Reset function is available to Maintenance |
| **`Cfg_HasWarnAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasTripAlm`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_WarnResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_TripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_WarnAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_TripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_WarnSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_TripSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_PulseT`** | `REAL` | `5` | Ẩn (Tag) | [Biến cấu hình thông số] Time (seconds) to pulse Trip Reset or Remote Trip output to E3 |
| **`Cfg_LocalResetReqdT`** | `REAL` | `3600` | Ẩn (Tag) | [Biến cấu hình thông số] No more than (1,2,3) remote Trip Resets are allowed in this amount of time (seconds) |
| **`Cfg_LocalResetReqdNum`** | `DINT` | `3` | Ẩn (Tag) | [Biến cấu hình thông số] No more than this many remote Trip Resets are allowed in (cfg'd) time |
| **`PCmd_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để send Remote Trip request to Overload Relay |
| **`PCmd_TripReset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để send Trip Reset request to Overload Relay |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Reset all Alarms requiring Reset |
| **`PCmd_WarnAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Impending Trip Warning alarm |
| **`PCmd_WarnSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Impending Trip Warning alarm |
| **`PCmd_WarnUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Impending Trip Warning alarm |
| **`PCmd_WarnUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Impending Trip Warning alarm |
| **`PCmd_TripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Overload Tripped alarm |
| **`PCmd_TripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Overload Tripped alarm |
| **`PCmd_TripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Overload Tripped alarm |
| **`PCmd_TripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Overload Tripped alarm |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) I/O Fault alarm |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) I/O Fault alarm |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) I/O Fault alarm |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Program Comnmand to Gỡ bỏ tạm hoãn (Unshelve) I/O Fault alarm |

## 3. Bảng chi tiết 49 Biến Đầu Ra (`Output Parameters`) của `P_E3Ovld`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Inp`** | `P_E3Ovld_Inp` | - | Hiện (Pin) | E3 Overload Parameter-based Input Assembly (100) |
| **`Out_TripReset`** | `BOOL` | `??` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1 = Reset Overload Trip |
| **`Out_RemoteTrip`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1 = Initiate Remote User Trip |
| **`Val_AvgPctFLA`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Average Current as Percent Full Load Amps (%) |
| **`SrcQ_IO`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final source and quality: GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`Val_Fault`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Fault Status: 0 = None 15 = Warning 30 = Tripped 31 = Local Reset required 32 = I/O Fault 34 = Config Error |
| **`Val_NotifyAll`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận của khối này cộng với khối kênh (enum) |
| **`Val_UnackAlmC`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Sts_WarnBits`** | `INT` | `2#0000_0000_0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Warning Reason Bits from Overload Relay |
| **`Sts_TripBits`** | `INT` | `2#0000_0000_0000_0000` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Trip Reason Bits from Overload Relay |
| **`Sts_Inp1`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Status of discrete input #1 |
| **`Sts_Inp2`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Status of discrete input #2 |
| **`Sts_Inp3`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Status of discrete input #3 (E3Plus only) |
| **`Sts_Inp4`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Status of discrete input #4 (E3Plus only) |
| **`Sts_Inp5`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Status of discrete input #5 (-EC5 only) |
| **`Sts_Inp6`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Status of discrete input #6 (-EC5 only) |
| **`Sts_OutA`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Status of discrete output A |
| **`Sts_OutB`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Status of discrete output B (E3Plus only) |
| **`Sts_LocalResetReqd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Too many remote resets: go to starter, find cause, and reset there |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Device Not Ready, see detail bits for reason |
| **`Nrdy_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Tripped (at device or by command) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi truyền thông I/O (yêu cầu nhấn Reset) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=An Alarm is Disabled or Shelved, display icon |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình (kiểm tra các bit Err_* chi tiết để biết nguyên nhân) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Reset pulse timer preset (use 0 to 2147483) |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Alarm Min On Time or Severity |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc lỗi ngắt tải (Shed) đã hết điều kiện lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_Warn`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Warning of impending trip (See Sts_WarnBits for reason) |
| **`Alm_Warn`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Alarm: warning of impending trip |
| **`Ack_Warn`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Trip Warning Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_WarnDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Trip Warning Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_WarnShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Trip Warning Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_WarnSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Trip Warning Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_Trip`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Overload Tripped (See Sts_TripBits for reason) |
| **`Alm_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Alarm: Overload Tripped |
| **`Ack_Trip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Overload Trip Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_TripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Overload Trip Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_TripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Overload Trip Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_TripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Overload Trip Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IOFault`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] I/O Comm Fault Status (0 = OK, 1 = Bad) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = I/O Fault Alarm |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = I/O Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`P_E3Ovld`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 27 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_E3Ovld`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'Motor Overload Relay'` | [Biến cấu hình thông số] Description for display on HMI. This string is shown in the title bar of the faceplate. |
| **`Cfg_Inp1Txt`** | `STRING_20` | `'Run Feedback'` | [Biến cấu hình thông số] Text for Input #1 label |
| **`Cfg_Inp2Txt`** | `STRING_20` | `'Disconnect Closed'` | [Biến cấu hình thông số] Text for Input #2 label |
| **`Cfg_Inp3Txt`** | `STRING_20` | `''` | [Biến cấu hình thông số] Text for Input #3 label (E3Plus only) |
| **`Cfg_Inp4Txt`** | `STRING_20` | `''` | [Biến cấu hình thông số] Text for Input #4 label (E3Plus only) |
| **`Cfg_Inp5Txt`** | `STRING_20` | `''` | [Biến cấu hình thông số] Text for Input #5 label (-EC5 only) |
| **`Cfg_Inp6Txt`** | `STRING_20` | `''` | [Biến cấu hình thông số] Text for Input #6 label (-EC5 only) |
| **`Cfg_Label`** | `STRING_20` | `'Overload Relay'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI. This string appears on the graphic symbol. |
| **`Cfg_OutATxt`** | `STRING_20` | `'Starter Energized'` | [Biến cấu hình thông số] Text for Output A label |
| **`Cfg_OutBTxt`** | `STRING_20` | `''` | [Biến cấu hình thông số] Text for Output B label (E3Plus only) |
| **`Cfg_Tag`** | `STRING_20` | `'P_E3Ovld'` | [Biến cấu hình thông số] Tagname for display on HMI. This string is shown in the title bar of the faceplate. |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_E3Ovld'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate |
| **`IOFault`** | `P_Alarm` | `{...}` | Alarm: I/O Fault |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset all Alarms requiring Reset |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset all Alarms and latched Shed conditions |
| **`OCmd_Trip`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để send Remote Trip request to Overload Relay |
| **`OCmd_TripReset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để send Trip Reset request to Overload Relay |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Ready to receive OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Ready to receive OCmd_ResetAckAll (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Trip`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Ready to receive OCmd_Trip (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_TripReset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Ready to receive OCmd_TripReset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`Trip`** | `P_Alarm` | `{...}` | Alarm: Overload has tripped |
| **`Val_Notify`** | `SINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |
| **`Warn`** | `P_Alarm` | `{...}` | Alarm: Warning of impending trip |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_E300Ovld`
**Thuộc chương:** Chương 3: Động cơ, Biến tần & Rơ-le Quá tải (Motors & Drives)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Motors 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_E300Ovld`:** **43** Biến đầu vào (`Input Parameters`) | **98** Biến đầu ra (`Output Parameters`) | **31** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_E300Ovld`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp`, `Ref_TripCodeList`, `Ref_WarningCodeList`, `Inp_Trip`, `Inp_TripReset`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_AllowOperTrip`, `Cfg_AllowOperReset`, `Cfg_AllowMaintTrip`, `Cfg_HasMoreObj`, `Cfg_AllowMaintReset`, `Cfg_HasWarnAlm`, `Cfg_HasTripAlm`, `Cfg_HasIOFaultAlm`, `Cfg_WarnResetReqd`, `Cfg_TripResetReqd`, `Cfg_IOFaultResetReqd`, `Cfg_WarnAckReqd`, `Cfg_TripAckReqd`, `Cfg_IOFaultAckReqd`, `Cfg_WarnSeverity`, `Cfg_TripSeverity`, `Cfg_IOFaultSeverity`, `Cfg_PulseT`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_InpPt00Txt`, `Cfg_InpPt01Txt`, `Cfg_InpPt02Txt`, `Cfg_InpPt03Txt`, `Cfg_InpPt04Txt`, `Cfg_InpPt05Txt`, `Cfg_Label`, `Cfg_OutPt00Txt`, `Cfg_OutPt01Txt`, `Cfg_OutPt02Txt`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_E300Ovld` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PCmd_Trip`, `PCmd_TripReset`, `PCmd_Reset`, `PCmd_WarnAck`, `PCmd_WarnSuppress`, `PCmd_WarnUnsuppress`, `PCmd_WarnUnshelve`, `PCmd_TripAck`, `PCmd_TripSuppress`, `PCmd_TripUnsuppress`, `PCmd_TripUnshelve`, `PCmd_IOFaultAck`, `PCmd_IOFaultSuppress`, `PCmd_IOFaultUnsuppress`, `PCmd_IOFaultUnshelve`
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Inp`, `Ref_TripCodeList`, `Ref_WarningCodeList`, `Out_TripReset`, `Out_RemoteTrip`, `Val_PctTherm`, `Val_CurrImbal`, `Val_AvgPctFLA`, `Val_AvgCurr`, `Val_L1Curr`, `Val_L2Curr`, `Val_L3Curr`, `Val_GndFaultCurr`, `Val_AvgVolts`, `Val_L1L2Volts`, `Val_L2L3Volts`, `Val_L3L1Volts`, `Val_TotRealPwr`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 43 Biến Đầu Vào (`Input Parameters`) của `P_E300Ovld`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp`** | `P_E300Ovld_Inp` | - | Hiện (Pin) | E300 Overload (193-ECM-ETR) Input Structure |
| **`Ref_TripCodeList`** | `P_DescList[1]` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] Tag containing List of Trip History Codes and Descriptions |
| **`Ref_WarningCodeList`** | `P_DescList[1]` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] Tag containing List of Warning History Codes and Descriptions |
| **`Inp_Trip`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Send Remote Trip request to Overload Relay |
| **`Inp_TripReset`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Send Trip Reset request to Overload Relay |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Reset All Latched Alarms |
| **`Cfg_AllowOperTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Remote Trip function is available to Operator |
| **`Cfg_AllowOperReset`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Trip Reset function is available to operator |
| **`Cfg_AllowMaintTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Remote Trip function is available to Maintenance |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_AllowMaintReset`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Trip Reset function is available to Maintenance |
| **`Cfg_HasWarnAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_WarnResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_TripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_WarnAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_TripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_WarnSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_TripSeverity`** | `DINT` | `750` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_PulseT`** | `REAL` | `5` | Ẩn (Tag) | [Biến cấu hình thông số] Time (seconds) to pulse Trip Reset or Remote Trip output to E3 |
| **`Cfg_LocalResetReqdT`** | `REAL` | `3600` | Ẩn (Tag) | [Biến cấu hình thông số] No more than (1..3) remote Trip Resets are allowed in this amount of time (seconds) |
| **`Cfg_LocalResetReqdNum`** | `DINT` | `3` | Ẩn (Tag) | [Biến cấu hình thông số] No more than this many remote Trip Resets are allowed in (cfg'd) time (1..3) |
| **`PCmd_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để send Remote Trip request to Overload Relay |
| **`PCmd_TripReset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để send Trip Reset request to Overload Relay |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Reset all Alarms requiring Reset |
| **`PCmd_WarnAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Impending Trip Warning alarm |
| **`PCmd_WarnSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Impending Trip Warning alarm |
| **`PCmd_WarnUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Impending Trip Warning alarm |
| **`PCmd_WarnUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Impending Trip Warning alarm |
| **`PCmd_TripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Overload Tripped alarm |
| **`PCmd_TripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Overload Tripped alarm |
| **`PCmd_TripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Overload Tripped alarm |
| **`PCmd_TripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Overload Tripped alarm |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) I/O Fault alarm |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) I/O Fault alarm |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) I/O Fault alarm |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Program Comnmand to Gỡ bỏ tạm hoãn (Unshelve) I/O Fault alarm |
| **`Sts_TripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Overload Trip Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |

## 3. Bảng chi tiết 98 Biến Đầu Ra (`Output Parameters`) của `P_E300Ovld`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Inp`** | `P_E300Ovld_Inp` | - | Hiện (Pin) | E300 Overload (193-ECM-ETR) Input Structure |
| **`Ref_TripCodeList`** | `P_DescList[1]` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] Tag containing List of Trip History Codes and Descriptions |
| **`Ref_WarningCodeList`** | `P_DescList[1]` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] Tag containing List of Warning History Codes and Descriptions |
| **`Out_TripReset`** | `BOOL` | `??` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1 = Reset Overload Trip |
| **`Out_RemoteTrip`** | `BOOL` | `??` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1 = Initiate Remote Trip |
| **`Val_PctTherm`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Motor Thermal Usage (%) (Trip / Fault at 100%) |
| **`Val_CurrImbal`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Current Imbalance (%) |
| **`Val_AvgPctFLA`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Average Current as Percent Full Load Amps (%) |
| **`Val_AvgCurr`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Average Current (Amps) |
| **`Val_L1Curr`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] L1 Phase Current (Amps) |
| **`Val_L2Curr`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] L2 Phase Current (Amps) |
| **`Val_L3Curr`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] L3 Phase Current (Amps) |
| **`Val_GndFaultCurr`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Ground Fault Current (Amps) |
| **`Val_AvgVolts`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Average Line-to-Line Voltage (Volts) |
| **`Val_L1L2Volts`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] L1 Phase to L2 Phase Voltage (Volts) |
| **`Val_L2L3Volts`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] L2 Phase to L3 Phase Voltage (Volts) |
| **`Val_L3L1Volts`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] L3 Phase to L1 Phase Voltage (Volts) |
| **`Val_TotRealPwr`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Total Real Power (kW) |
| **`Val_TotReactPwr`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Total Reactive Power (kVAR) |
| **`Val_TotAppPwr`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Total Apparent Power (kVA) |
| **`Val_PwrFact`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Power Factor (%) |
| **`Val_MinToReset`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Time until overload can be reset (min in mmm:ss) |
| **`Val_SecToReset`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Time unitl overload can be reset (sec in mmm:ss) |
| **`Val_MinToStart`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Time until motor can be started (min in mmm:ss) |
| **`Val_SecToStart`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Time until motor can be started (sec in mmm:ss) |
| **`Val_TripCode`** | `INT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Overload Trip History 0 (most recent trip) Code (enum) |
| **`Val_WarningCode`** | `INT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Overload Warning History 0 (most recent warning) Code (enum) |
| **`Val_InvCfgParam`** | `INT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] ID number of misconfigured parameter in E300 (Par #) |
| **`SrcQ_IO`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Source and Quality of primary I/O (enum) |
| **`SrcQ`** | `SINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final device status source and quality: GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`Val_Fault`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Fault Status 0 = None, 15=Warning, 30 = Tripped, 31 = Local Reset Reqd, 32=I/O Fault, 34=Config Error |
| **`Val_NotifyAll`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận của khối này cộng với khối kênh (enum) |
| **`Val_UnackAlmC`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Sts_MotorCurr`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor Current is Present |
| **`Sts_GFCurr`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Ground Fault Current is Present |
| **`Sts_MotorVolts`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor Voltage Preset |
| **`Sts_EStartEna`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Emergency Start capability is enabled |
| **`Sts_DevLgxEna`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = DeviceLogix capability is enabled |
| **`Sts_FdbkTmoEna`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Feedback Timeout Enabled |
| **`Sts_OpStaPres`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = E300 has Operator Station present |
| **`Sts_VoltSense`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Voltage Sensing capability is present (e.g., VIG) |
| **`Sts_IntGndFaultSense`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Internal Ground Fault Sensing is Present (IG or VIG) |
| **`Sts_ExtGndFaultSense`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = External Ground Fault Sensing is present (IG or VIG) |
| **`Sts_PTCSense`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = PTC Temperature Sensing is present |
| **`Sts_InpPt00`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Status of discrete input Pt00 |
| **`Sts_InpPt01`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Status of discrete input Pt01 |
| **`Sts_InpPt02`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Status of discrete input Pt02 |
| **`Sts_InpPt03`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Status of discrete input Pt03 |
| **`Sts_InpPt04`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Status of discrete input Pt04 |
| **`Sts_InpPt05`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Status of discrete input Pt05 |
| **`Sts_OutPt00`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Status of discrete output Pt00 |
| **`Sts_OutPt01`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Status of discrete output Pt01 |
| **`Sts_OutPt02`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Status of discrete output Pt02 |
| **`Sts_DeviceSts1`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Device Status 1 (Par 21) (Control, Sensing, Expansion Module Presence) |
| **`Sts_InpSts1`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Input Status 1 (Par 17) (Discrete Expansion Module Inputs) |
| **`Sts_OutSts`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Output Status (Par 18) (Discrete Expansion Module Outputs) |
| **`Sts_OpSta`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Local Operator Station Stauts (Par 19) |
| **`Sts_CurrentTrip`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Current Trip Status Bits (Par 4) |
| **`Sts_CurrentWarning`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Current Warning Status Bits (Par 10) |
| **`Sts_VoltageTrip`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Voltage Trip Status Bits (Par 5) |
| **`Sts_VoltageWarning`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Voltage Warning Status Bits (Par 11) |
| **`Sts_PowerTrip`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Power Trip Status Bits (Par 6) |
| **`Sts_PowerWarning`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Power Warning Status Bits (Par 12) |
| **`Sts_ControlTrip`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Control Trip Status Bits (Par 7) |
| **`Sts_ControlWarning`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Control Warning Status Bits (Par 13) |
| **`Sts_AnalogTrip`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Analog Trip Status Bits (Par 8) |
| **`Sts_AnalogWarning`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Analog Warning Status Bits (Par 14) |
| **`Sts_DevLgxOutputs`** | `INT` | `2#0000_0000_0000_0000` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] DeviceLogix Output Bits (Par 348) |
| **`Sts_LocalResetReqd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Too many remote resets: go to starter, find cause, and reset there |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Device Not Ready, see detail bits for reason |
| **`Nrdy_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Tripped (at device or by command) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi truyền thông I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OvldNR`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Overload powerup not complete |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=An Alarm is Disabled or Shelved, display icon |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình (kiểm tra các bit Err_* chi tiết để biết nguyên nhân) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Reset pulse timer preset (use 0 to 2147483) |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc lỗi ngắt tải (Shed) đã hết điều kiện lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Alarm Min On Time or Severity |
| **`Sts_Warn`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Warning of impending trip (See Sts_WarnBits for reason) |
| **`Alm_Warn`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Alarm: warning of impending trip |
| **`Ack_Warn`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Trip Warning Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_WarnDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Trip Warning Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_WarnShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Trip Warning Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_WarnSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Trip Warning Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_Trip`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Overload Tripped (See Sts_TripBits for reason) |
| **`Alm_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Alarm: Overload Tripped |
| **`Ack_Trip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Overload Trip Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_TripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Overload Trip Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_TripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Overload Trip Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IOFault`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] I/O Comm Fault Status (0 = OK, 1 = Bad) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = I/O Fault Alarm |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = I/O Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`P_E300Ovld`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 31 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_E300Ovld`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_8` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'Motor Overload Relay'` | [Biến cấu hình thông số] Description for display on HMI. This string is shown in the title bar of the faceplate. |
| **`Cfg_InpPt00Txt`** | `STRING_20` | `'Starter Aux Contact'` | [Biến cấu hình thông số] Text for Discrete Input Pt00 label |
| **`Cfg_InpPt01Txt`** | `STRING_20` | `''` | [Biến cấu hình thông số] Text for Discrete Input Pt01 label |
| **`Cfg_InpPt02Txt`** | `STRING_20` | `''` | [Biến cấu hình thông số] Text for Discrete Input Pt02 label |
| **`Cfg_InpPt03Txt`** | `STRING_20` | `''` | [Biến cấu hình thông số] Text for Discrete Input Pt03 label |
| **`Cfg_InpPt04Txt`** | `STRING_20` | `''` | [Biến cấu hình thông số] Text for Discrete Input Pt04 label |
| **`Cfg_InpPt05Txt`** | `STRING_20` | `''` | [Biến cấu hình thông số] Text for Discrete Input Pt05 label |
| **`Cfg_Label`** | `STRING_20` | `'Overload Relay'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI. This string appears on the graphic symbol. |
| **`Cfg_OutPt00Txt`** | `STRING_20` | `'Starter Energized'` | [Biến cấu hình thông số] Text for Discrete Output Pt00 label |
| **`Cfg_OutPt01Txt`** | `STRING_20` | `''` | [Biến cấu hình thông số] Text for Discrete Output Pt01 label |
| **`Cfg_OutPt02Txt`** | `STRING_20` | `''` | [Biến cấu hình thông số] Text for Discrete Output Pt02 label |
| **`Cfg_Tag`** | `STRING_20` | `'P_E300Ovld'` | [Biến cấu hình thông số] Tagname for display on HMI. This string is shown in the title bar of the faceplate. |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_E300Ovld'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate |
| **`IOFault`** | `P_Alarm` | `{...}` | Alarm: I/O Fault |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset all Alarms requiring Reset |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset all Alarms and latched Shed conditions |
| **`OCmd_Trip`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để send Remote Trip request to Overload Relay |
| **`OCmd_TripReset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để send Trip Reset request to Overload Relay |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = At least one Alarm requires Reset |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = At least one Alarm requires Reset or Ack |
| **`ORdy_Trip`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Ready to receive OCmd_Trip (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_TripReset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Ready to receive OCmd_TripReset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`Trip`** | `P_Alarm` | `{...}` | Alarm: Overload has tripped |
| **`Val_AnalogIn`** | `INT[12]` | `{...}` | [Giá trị thực tế đầu ra (Logic/HMI)] Analog Input Values 0 to 65535 raw UINT (unsigned) |
| **`Val_Notify`** | `SINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |
| **`Val_TripDesc`** | `STRING_40` | `'` | [Giá trị thực tế đầu ra (Logic/HMI)] Description of Trip Reason (given TripHistory0 code) |
| **`Val_WarningDesc`** | `STRING_40` | `‘Logic not yet scanned.’` | [Giá trị thực tế đầu ra (Logic/HMI)] Description of Warning Reason (given WarnHistory0 code) |
| **`Warn`** | `P_Alarm` | `{...}` | Alarm: Warning of impending trip |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_ResInh`
**Thuộc chương:** Chương 3: Động cơ, Biến tần & Rơ-le Quá tải (Motors & Drives)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Motors 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_ResInh`:** **13** Biến đầu vào (`Input Parameters`) | **8** Biến đầu ra (`Output Parameters`) | **7** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_ResInh`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_Stopped`, `Inp_Starting`, `Inp_Running`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_ThreeColdStarts`, `Cfg_FirstFailCold`, `Cfg_SubseqFailCold`, `Cfg_FirstFailHot`, `Cfg_SubseqFailHot`, `Cfg_HasMoreObj`, `Cfg_HotRestartOK`, `Cfg_RestartHot`, `Cfg_HotToCold`
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_Label`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_ResInh` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Val_MinToReady`, `Val_SecToReady`, `Val_Fdbk`, `Val_State`, `Sts_Ready`, `Sts_Err`
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 13 Biến Đầu Vào (`Input Parameters`) của `P_ResInh`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp_Stopped`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Equipment is confirmed Stopped |
| **`Inp_Starting`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Equipment is Starting, indicating a start attempt |
| **`Inp_Running`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Equipment is confirmed Running |
| **`Cfg_ThreeColdStarts`** | `REAL` | `0.5` | Ẩn (Tag) | [Biến cấu hình thông số] Time within which three starts are allowed if cold (hr) |
| **`Cfg_FirstFailCold`** | `REAL` | `0.5` | Ẩn (Tag) | [Biến cấu hình thông số] Time for cold motor to wait after 1st start failure before ready to start (hr) |
| **`Cfg_SubseqFailCold`** | `REAL` | `4` | Ẩn (Tag) | [Biến cấu hình thông số] Time for cold motor to wait after 2nd and subsequent start failure before ready (hr) |
| **`Cfg_FirstFailHot`** | `REAL` | `0.5` | Ẩn (Tag) | [Biến cấu hình thông số] Time for hot motor to wait after 1st start failure before ready to start (hr) |
| **`Cfg_SubseqFailHot`** | `REAL` | `4` | Ẩn (Tag) | [Biến cấu hình thông số] Time for hot motor to wait after 2nd and subsequent start failure before ready (hr) |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_HotRestartOK`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Time for hot motor to run so it can immediately restart after stop (hr) |
| **`Cfg_RestartHot`** | `REAL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Time for hot motor to wait after stop if stopped before Hot Restart OK time (hr) |
| **`Cfg_HotToCold`** | `REAL` | `16` | Ẩn (Tag) | [Biến cấu hình thông số] Time for a stopped hot motor to become cold (hr) |

## 3. Bảng chi tiết 8 Biến Đầu Ra (`Output Parameters`) của `P_ResInh`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Val_MinToReady`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Minutes yet inhibited before ready to start (mmm:ss) |
| **`Val_SecToReady`** | `SINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Seconds yet inhibited before ready to start (mmm:ss) |
| **`Val_Fdbk`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device feedback: 0 = None/Multiple 1 = Stopped 2 = Starting 3 = Running |
| **`Val_State`** | `SINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] State Number (see State Diagram in docs) for HMI |
| **`Sts_Ready`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] Permissive for unit to start 1 = ready, 0 = not ready |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Error in Config: Invalid Time (use 0.0 to 2147483) |
| **`P_ResInh`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 7 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_ResInh`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_8` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'Restart Inhibit'` | [Biến cấu hình thông số] Description for display on HMI. The string shows in the title bar of the faceplate. |
| **`Cfg_Label`** | `STRING_20` | `'Restart Inhibit'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI. This string appears on the graphic symbol. |
| **`Cfg_Tag`** | `STRING_20` | `'P_ResInh'` | [Biến cấu hình thông số] Tagname for display on HMI. This string is shown in the title bar of the faceplate. |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_ResInh'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI và phần mềm thông tin |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_RunTime`
**Thuộc chương:** Chương 3: Động cơ, Biến tần & Rơ-le Quá tải (Motors & Drives)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Motors 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_RunTime`:** **10** Biến đầu vào (`Input Parameters`) | **6** Biến đầu ra (`Output Parameters`) | **7** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_RunTime`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp_Starting`, `Inp_Running`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_HasMoreObj`
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_Label`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_RunTime` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PCmd_ClearStarts`, `PCmd_ClearMaxHrs`, `PCmd_ClearTotHrs`
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Val_Starts`, `Val_CurRunHrs`, `Val_MaxRunHrs`, `Val_TotRunHrs`
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 10 Biến Đầu Vào (`Input Parameters`) của `P_RunTime`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Inp_Starting`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Motor is Starting (from motor instruction) |
| **`Inp_Running`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Motor is Running (from motor instruction) |
| **`PCmd_ClearStarts`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Clear Count of Starts (attempts) |
| **`PCmd_ClearMaxHrs`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Clear Maximum (continuous) Run Time for any start |
| **`PCmd_ClearTotHrs`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Clear Total Running Time |
| **`MCmd_ClearStarts`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Clear Count of Starts (attempts) |
| **`MCmd_ClearMaxHrs`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Clear Maximum (continuous) Run Time for any start |
| **`MCmd_ClearTotHrs`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Clear Total Running Time |

## 3. Bảng chi tiết 6 Biến Đầu Ra (`Output Parameters`) của `P_RunTime`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Val_Starts`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Total number of motor starts or attempts |
| **`Val_CurRunHrs`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Current running time this start (hours) |
| **`Val_MaxRunHrs`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Maximum continuous running time for a given start (hours) |
| **`Val_TotRunHrs`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Total accumulated running time (hours) |
| **`P_RunTime`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 7 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_RunTime`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_8` | `'HMI'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'Accumulated Run Time (Hours)'` | [Biến cấu hình thông số] Description for display on HMI. The string shows in the title bar of the faceplate. |
| **`Cfg_Label`** | `STRING_20` | `'Run Time'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI. This string appears on the graphic symbol. |
| **`Cfg_Tag`** | `STRING_20` | `'P_RunTime'` | [Biến cấu hình thông số] Tagname for display on HMI |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_RunTime'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI và phần mềm thông tin |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_SMC50`
**Thuộc chương:** Chương 3: Động cơ, Biến tần & Rơ-le Quá tải (Motors & Drives)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Motors 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_SMC50`:** **93** Biến đầu vào (`Input Parameters`) | **106** Biến đầu ra (`Output Parameters`) | **30** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_SMC50`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp`, `Out`, `Ref_FaultCodeList`, `Inp_PermOK`, `Inp_NBPermOK`, `Inp_IntlkOK`, `Inp_NBIntlkOK`, `Inp_IOFault`, `Inp_Sim`, `Inp_Hand`, `Inp_Ovrd`, `Inp_OvrdCmd`, `Inp_ExtInh`, `Inp_HornInh`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_HasJog`, `Cfg_AllowLocal`, `Cfg_HasPermObj`, `Cfg_HasIntlkObj`, `Cfg_HasResInhObj`, `Cfg_HasRunTimeObj`, `Cfg_OperStopPrio`, `Cfg_ExtStopPrio`, `Cfg_OCmdResets`, `Cfg_XCmdResets`, `Cfg_OvrdPermIntlk`, `Cfg_ShedOnFailToStart`, `Cfg_ShedOnIOFault`, `Cfg_HasFailToStartAlm`, `Cfg_HasFailToStopAlm`, `Cfg_HasIntlkTripAlm`, `Cfg_HasMotorFaultAlm`, `Cfg_HasIOFaultAlm`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_Label`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_SMC50` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PSet_Owner`, `PCmd_Start`, `PCmd_Stop`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_FailToStartAck`, `PCmd_FailToStartSuppress`, `PCmd_FailToStartUnsuppress`, `PCmd_FailToStartUnshelve`, `PCmd_FailToStopAck`, `PCmd_FailToStopSuppress`, `PCmd_FailToStopUnsuppress`, `PCmd_FailToStopUnshelve`, `PCmd_IntlkTripAck`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Inp`, `Out`, `Ref_FaultCodeList`, `Out_Horn`, `Val_AvgPctFLA`, `Val_PctTherm`, `Val_CurAvg`, `Val_RealPwr`, `Val_PwrFact`, `Val_MinToTrip`, `Val_SecToTrip`, `Val_MinToReset`, `Val_SecToReset`, `Val_LastFaultCode`, `Val_Cmd`, `Val_Fdbk`, `Val_Sts`, `Val_Fault`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 93 Biến Đầu Vào (`Input Parameters`) của `P_SMC50`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp`** | `P_SMC50_Inp` | - | Hiện (Pin) | Common part of SMC-50 Input Assembly |
| **`Out`** | `P_SMC50_Out` | - | Hiện (Pin) | Common part of SMC-50 Output Assembly |
| **`Ref_FaultCodeList`** | `P_DescList[1]` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] Tag containing List of Fault Codes (DINT) and their Descriptions (STRING) |
| **`Inp_PermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Permissives OK, motor can start |
| **`Inp_NBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Non-Bypassable Permissives OK, motor can start |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Interlocks OK, motor can start/run |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Non-Bypassable Interlocks OK, motor can start/run |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Input Communication Status 0 = OK, 1 = fail |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Simulate working motor; 0 = Start/Stop/ Monitor actual motor |
| **`Inp_Hand`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Acquire Hand (typ. hardwired local) Mode, 0 = Release Hand Mode |
| **`Inp_Ovrd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Acquire Override (higher priority program logic), 0 = Release Override |
| **`Inp_OvrdCmd`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Override Mode Command: 0 = None, 1 = Stop, 2=Start |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Cấm nguồn lệnh ngoài (External) chiếm quyền điều khiển; 0 = Cho phép External chiếm quyền. |
| **`Inp_HornInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Cấm còi báo động âm thanh; 0 = Cho phép phát còi báo trước khi khởi động |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Reset Shed Latches and Cleared Alarms |
| **`Cfg_HasJog`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Motor Job Command enabled/visible, 0 = Motor Jog Command not allowed |
| **`Cfg_AllowLocal`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Allow Local Start/Stop without alarm, 0 = Start/Stop from HMI/prog only |
| **`Cfg_HasPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng reverse permissive liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng interlock liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasResInhObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng restart inhibit object is connected and navigation to the restart inhibit liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasRunTimeObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng run time object is connected and navigation to the runtime liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_OperStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = OCmd_Stop any time, 0 = OCmd_Stop only when Operator selected |
| **`Cfg_ExtStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = XCmd_Stop any time, 0 = XCmd_Stop only when Ext selected |
| **`Cfg_OCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Lệnh vận hành mới từ Operator tự động xóa luôn chốt lỗi cũ; 0 = Bắt buộc phải nhấn Reset trước khi ra lệnh mới. |
| **`Cfg_XCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Lệnh mới từ External tự động xóa luôn chốt lỗi cũ; 0 = Bắt buộc phải gửi lệnh Reset trước. |
| **`Cfg_OvrdPermIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Chế độ Override bỏ qua các liên động/điều kiện cho phép có thể bypass; 0 = Luôn tuân thủ mọi liên động. |
| **`Cfg_ShedOnFailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt lệnh chạy động cơ (Shed) và phát cảnh báo khi bị lỗi Không khởi động được (Fail to Start); 0 = Chỉ phát cảnh báo. |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt động cơ (Shed) và phát cảnh báo khi lỗi thẻ I/O; 0 = Chỉ phát cảnh báo. |
| **`Cfg_HasFailToStartAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Fail to Start alarm, Fail to Stop alarm, Interlock Trip alarm, Motor Fault alarm, or I/O Fault alarm exists and is checked. |
| **`Cfg_HasFailToStopAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Fail to Start alarm, Fail to Stop alarm, Interlock Trip alarm, Motor Fault alarm, or I/O Fault alarm exists and is checked. |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Fail to Start alarm, Fail to Stop alarm, Interlock Trip alarm, Motor Fault alarm, or I/O Fault alarm exists and is checked. |
| **`Cfg_HasMotorFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Fail to Start alarm, Fail to Stop alarm, Interlock Trip alarm, Motor Fault alarm, or I/O Fault alarm exists and is checked. |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Fail to Start alarm, Fail to Stop alarm, Interlock Trip alarm, Motor Fault alarm, or I/O Fault alarm exists and is checked. |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_FailToStartResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Reset required to clear Fail to Start alarm, Fail to Stop alarm, Interlock Trip alarm, Motor Fault alarm, or I/O Fault alarm. |
| **`Cfg_FailToStopResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Reset required to clear Fail to Start alarm, Fail to Stop alarm, Interlock Trip alarm, Motor Fault alarm, or I/O Fault alarm. |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Reset required to clear Fail to Start alarm, Fail to Stop alarm, Interlock Trip alarm, Motor Fault alarm, or I/O Fault alarm. |
| **`Cfg_MotorFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Reset required to clear Fail to Start alarm, Fail to Stop alarm, Interlock Trip alarm, Motor Fault alarm, or I/O Fault alarm. |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Reset required to clear Fail to Start alarm, Fail to Stop alarm, Interlock Trip alarm, Motor Fault alarm, or I/O Fault alarm. |
| **`Cfg_FailToStartAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Xác nhận (Acknowledge) required for Fail to Start alarm, Fail to Stop alarm, Interlock Trip alarm, Motor Fault alarm, or I/O Fault alarm. |
| **`Cfg_FailToStopAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Xác nhận (Acknowledge) required for Fail to Start alarm, Fail to Stop alarm, Interlock Trip alarm, Motor Fault alarm, or I/O Fault alarm. |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Xác nhận (Acknowledge) required for Fail to Start alarm, Fail to Stop alarm, Interlock Trip alarm, Motor Fault alarm, or I/O Fault alarm. |
| **`Cfg_MotorFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Xác nhận (Acknowledge) required for Fail to Start alarm, Fail to Stop alarm, Interlock Trip alarm, Motor Fault alarm, or I/O Fault alarm. |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Xác nhận (Acknowledge) required for Fail to Start alarm, Fail to Stop alarm, Interlock Trip alarm, Motor Fault alarm, or I/O Fault alarm. |
| **`Cfg_FailToStartSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_FailToStopSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_MotorFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_StartHornT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian hú còi cảnh báo tại hiện trường trước khi đóng điện khởi động động cơ (giây, 0 = tắt còi). |
| **`Cfg_SimFdbkT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Time to echo back running / stopped feedback in Simulation (seconds) |
| **`Cfg_FailToStartT`** | `REAL` | `15` | Ẩn (Tag) | [Biến cấu hình thông số] Time after Start to get Run Feedback before Fault (seconds) |
| **`Cfg_FailToStopT`** | `REAL` | `15` | Ẩn (Tag) | [Biến cấu hình thông số] Time after Stop to drop Run Feedback before Fault (seconds) |
| **`Cfg_ResetPulseT`** | `REAL` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Time to pulse Out_Reset to clear motor fault |
| **`Cfg_MaxJogT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian giữ lệnh nhấp thử (Jog) tối đa cho phép (giây, 0 = không giới hạn). |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Mã định danh chủ sở hữu chương trình (Program Owner ID: khác 0 để đăng ký chiếm quyền, bằng 0 để nhả quyền). |
| **`PCmd_Start`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khởi động (Start) động cơ |
| **`PCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Dừng (Stop) động cơ |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để chuyển quyền điều khiển sang Tự động (từ Operator sang Program) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để chuyển quyền điều khiển sang Bằng tay (từ Program sang Operator) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa cứng chế độ Program (không cho phép Operator giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Mở khóa chế độ Program (cho phép Operator lấy quyền điều khiển) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để trả về nguồn lệnh Bình thường mặc định (Normal: Program hoặc Operator) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để ResetShed Latches and Cleared Alarms |
| **`PCmd_FailToStartAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Fail to Start Alarm |
| **`PCmd_FailToStartSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Fail to Start Alarm |
| **`PCmd_FailToStartUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Fail to Start Alarm |
| **`PCmd_FailToStartUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Fail to Start Alarm |
| **`PCmd_FailToStopAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Fail to Stop Alarm |
| **`PCmd_FailToStopSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Fail to Stop Alarm |
| **`PCmd_FailToStopUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Fail to Stop Alarm |
| **`PCmd_FailToStopUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Fail to Stop Alarm |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Interlock Trip Alarm |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Interlock Trip Alarm |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Interlock Trip Alarm |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) InterlockTrip Alarm |
| **`PCmd_MotorFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Motor Fault Alarm |
| **`PCmd_MotorFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Motor Fault Alarm |
| **`PCmd_MotorFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Motor Fault Alarm |
| **`PCmd_MotorFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Motor Fault Alarm |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) I/O Fault Alarm |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) I/O Fault Alarm |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) I/O Fault Alarm |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) I/O Fault Alarm |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Release (Maintenance to Operator/Program/Override) |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Unlock Operator (allow Program to acquire) |
| **`XCmd_Start`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Khởi động (Start) động cơ |
| **`XCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Dừng (Stop) động cơ |
| **`XCmd_Jog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhấp thử (Jog) động cơ |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Acquire (Oper/Prog to Ext) |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Release (Ext to Oper/Prog) |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi |

## 3. Bảng chi tiết 106 Biến Đầu Ra (`Output Parameters`) của `P_SMC50`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Inp`** | `P_SMC50_Inp` | - | Hiện (Pin) | Common part of SMC-50 Input Assembly |
| **`Out`** | `P_SMC50_Out` | - | Hiện (Pin) | Common part of SMC-50 Output Assembly |
| **`Ref_FaultCodeList`** | `P_DescList[1]` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] Tag containing List of Fault Codes (DINT) and their Descriptions (STRING) |
| **`Out_Horn`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1 = Sound audible prior to commanded motor start |
| **`Val_AvgPctFLA`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Average Current as Percent of Full Load Amps (%) |
| **`Val_PctTherm`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Motor Thermal Usage (%) (Trip / Fault at 100%) |
| **`Val_CurAvg`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Output Current (3-Phase Average) (Amps) |
| **`Val_RealPwr`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Power Usage of Motor (Real = V * A * PF) (MW) |
| **`Val_PwrFact`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Power Factor (0.00 to 1.00) (Real power / Apparent power) |
| **`Val_MinToTrip`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Time remaining until overload trip (minutes in mm:ss) |
| **`Val_SecToTrip`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Time remaining until overload trip (seconds in mm:ss) |
| **`Val_MinToReset`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Time remaining until trip can be reset (minutes in mm:ss) |
| **`Val_SecToReset`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Time remaining until trip can be reset (seconds in mm:ss) |
| **`Val_LastFaultCode`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Last starter fault code (enum) (Par 124) |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final controller status source and quality: GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`Val_Cmd`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Command 0 = None, 1 = Stop, 2=Start 4=Jog |
| **`Val_Fdbk`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Feedback 0 = Stopped, 1 = Running 3=Starting 4=Stopping |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Sts: 0 = ? 1 = Stopped 2=RunFwd 3=RunRev 4=RunSlow 5=StartFwd 6=StartRev 7=StartSlow 8=JogFwd 9=JogRev 10 = Stopping |
| **`Val_Fault`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Fault Status 0 = None, 16=Fail to Start, 17 = Fail to Stop, 18=Motor / Starter Fault, 32=I/O Fault, 34=Config Error |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận của khối này cộng với khối kênh (enum) |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Current Object Owner ID (0 = not owned) |
| **`Sts_Stopped`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor / Starter requested to stop and is confirmed stopped |
| **`Sts_Starting`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor / Starter requested to run and awaiting run feedback |
| **`Sts_Running`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor / Starter requested to run and is confirmed running |
| **`Sts_Stopping`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor / Starter requested to stop and awaiting stopped feedback |
| **`Sts_Jogging`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor requested to Jog |
| **`Sts_NotReady`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Motor / Starter is Not Ready (cannot be started) Check alarms, stops, faults |
| **`Sts_Alarm`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor / Starter has an Alarm (see Motor display or manual) |
| **`Sts_AtSpeed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Motor is running with full voltage applied |
| **`Sts_CtrlPwrOK`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Control Power OK, 0 = Control Power Absent |
| **`Sts_Phasing`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Phase Order = ABC, 0 = Phase Order = CBA |
| **`Sts_PhasingActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = 3-phase is valid 0 = No valid 3-phase detected |
| **`Sts_StartIsol`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Start/Isolation Contactor Enabled, 0 = Disabled |
| **`Sts_ExtBypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = External Bypass Contactor Enabled, 0 = Disabled |
| **`Sts_Opt1Inp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Option 1 Input is Active, 0 = Inactive |
| **`Sts_Opt2Inp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Option 2 Input is Active, 0 = Inactive |
| **`Sts_Available`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor available for control by automation (Prog) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bypassable Interlocks and Permissives are Bypassed |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bypassing Active (Bypassed or Maintenance) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Device Not Ready, see detail bits for reason |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_DriveNR`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Device Note Ready: Drive Not Ready |
| **`Nrdy_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi thiết bị ngắt tải (yêu cầu nhấn Reset) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Khóa liên động không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi truyền thông I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Thiết bị đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Nrdy_Perm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Cho phép khởi động không thỏa mãn (Permissive Not OK) |
| **`Nrdy_PrioStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang có lệnh Dừng ưu tiên từ Operator/External (yêu cầu nhấn Reset) |
| **`Nrdy_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Device not Ready: Deivce Tripped (Drive Fault) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=An Alarm is Disabled or Shelved, display icon |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình (kiểm tra các bit Err_* chi tiết để biết nguyên nhân) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Invalid Check or Reset Pulse Time (use 0 to 2147483) |
| **`Err_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: simulation timer preset: use 0 to 2147483) |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Alarm Min On Time or Severity |
| **`Sts_Hand`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Hand đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn OoS, Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_OoS`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Out of Service đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_Maint`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Maintenance đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ovrd, Ext, Prog, Oper) |
| **`Sts_Ovrd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Override đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ext, Prog, Oper) |
| **`Sts_Ext`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = External đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Prog, Oper) |
| **`Sts_Prog`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Program đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_ProgLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Program đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Oper`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_OperLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Selection equals the Normal (Prog or Oper) |
| **`Sts_ExtReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = External request inhibited, cannot Get to External from current state |
| **`Sts_ProgReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Program request inhibited, cannot Get to Program from current state |
| **`Sts_MAcqRcvd`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Maintenance Acquire command received this scan |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc lỗi ngắt tải (Shed) đã hết điều kiện lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Motor Failed to Start (One-Shot) |
| **`Alm_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Motor Fail to Start Alarm |
| **`Ack_FailToStart`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Fail to Start Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_FailToStartDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Start Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_FailToStartShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Start Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_FailToStartSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Start Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Motor Failed to Stop |
| **`Alm_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Motor Fail to Stop Alarm |
| **`Ack_FailToStop`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Fail to Stop Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_FailToStopDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Stop Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_FailToStopShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Stop Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_FailToStopSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Stop Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Motor was stopped by an Interlock NOT OK (One-Shot) |
| **`Alm_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Alarm: Motor stopped by an Interlock NOT OK |
| **`Ack_IntlkTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Interlock Trip Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IntlkTripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock Trip Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IntlkTripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock Trip Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IntlkTripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock Trip Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_MotorFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Motor Fault (see Motor display or manual) |
| **`Alm_MotorFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Alarm: Motor Fault (see Motor display or manual) |
| **`Ack_MotorFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Motor Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_MotorFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Motor Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_MotorFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Motor Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_MotorFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Motor Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] I/O Comm Fault Status (0 = OK, 1 = Bad) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = I/O Fault Alarm |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = I/O Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`MRdy_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`P_SMC50`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 30 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_SMC50`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'SMC-50 Smart Motor Controller'` | [Biến cấu hình thông số] Description for display on HMI. This string is shown in the title bar of the faceplate. |
| **`Cfg_Label`** | `STRING_20` | `'Motor Control'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI. This string appears on the graphic symbol. |
| **`Cfg_Tag`** | `STRING_20` | `'P_SMC50'` | [Biến cấu hình thông số] Tag name for display on the HMI. This string is shown in the title bar of the faceplate. |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Khối con P_CmdSrc quản lý lựa chọn nguồn lệnh điều khiển |
| **`FailToStart`** | `P_Alarm` | `{...}` | Motor Failed to Start Alarm |
| **`FailToStop`** | `P_Alarm` | `{...}` | Motor Failed to Stop Alarm |
| **`HMI_HasMore`** | `BOOL` | `0` | [Biến nội bộ giao diện HMI Faceplate] Motor Failed to Stop Alarm |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_SMC50'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | Interlock Trip Alarm |
| **`IOFault`** | `P_Alarm` | `{...}` | I/O Fault Alarm |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bypass all Bypassable Interlocks and Permissives |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Check (not bypass) all Interlocks and Permissives |
| **`MotorFault`** | `P_Alarm` | `{...}` | Motor Faulted Alarm |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`OCmd_Jog`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Nhấp thử (Jog) động cơ (NOT cleared by P_SMC50!) |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi, plus Xác nhận (Acknowledge) alarms |
| **`OCmd_Start`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Khởi động (Start) động cơ |
| **`OCmd_Stop`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Dừng (Stop) động cơ |
| **`ORdy_Jog`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Jog (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = At least one Alarm or latched Shed condition requires Reset or Ack |
| **`ORdy_Start`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Start (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Stop`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Stop (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`Val_FaultDesc`** | `STRING_40` | `‘Logic not yet scanned.’` | [Giá trị thực tế đầu ra (Logic/HMI)] Description of Motor Fault (given LastFaultCode) |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_SMCFlex`
**Thuộc chương:** Chương 3: Động cơ, Biến tần & Rơ-le Quá tải (Motors & Drives)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Motors 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_SMCFlex`:** **95** Biến đầu vào (`Input Parameters`) | **104** Biến đầu ra (`Output Parameters`) | **30** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_SMCFlex`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Inp`, `Out`, `Ref_FaultCodeList`, `Inp_PermOK`, `Inp_NBPermOK`, `Inp_IntlkOK`, `Inp_NBIntlkOK`, `Inp_IntlkAvail`, `Inp_IOFault`, `Inp_Sim`, `Inp_Hand`, `Inp_Ovrd`, `Inp_OvrdCmd`, `Inp_ExtInh`, `Inp_HornInh`, `Inp_Reset`
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_PwrUnits`, `Cfg_HasJog`, `Cfg_AllowLocal`, `Cfg_HasPermObj`, `Cfg_HasIntlkObj`, `Cfg_HasResInhObj`, `Cfg_HasRunTimeObj`, `Cfg_OperStopPrio`, `Cfg_ExtStopPrio`, `Cfg_OCmdResets`, `Cfg_XCmdResets`, `Cfg_OvrdPermIntlk`, `Cfg_ShedOnFailToStart`, `Cfg_HasMoreObj`, `Cfg_ShedOnIOFault`, `Cfg_HasFailToStartAlm`, `Cfg_HasFailToStopAlm`, `Cfg_HasIntlkTripAlm`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_Label`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_SMCFlex` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PSet_Owner`, `PCmd_Start`, `PCmd_Stop`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_FailToStartAck`, `PCmd_FailToStartSuppress`, `PCmd_FailToStartUnsuppress`, `PCmd_FailToStartUnshelve`, `PCmd_FailToStopAck`, `PCmd_FailToStopSuppress`, `PCmd_FailToStopUnsuppress`, `PCmd_FailToStopUnshelve`, `PCmd_IntlkTripAck`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Inp`, `Out`, `Ref_FaultCodeList`, `Out_Horn`, `Val_AvgPctFLA`, `Val_PctTherm`, `Val_CurPhaseA`, `Val_CurPhaseB`, `Val_CurPhaseC`, `Val_RealPwr`, `Val_PwrFact`, `Val_LastFaultCode`, `Val_Cmd`, `Val_Fdbk`, `Val_Sts`, `Val_Fault`, `Val_NotifyAll`, `Val_UnackAlmC`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 95 Biến Đầu Vào (`Input Parameters`) của `P_SMCFlex`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Inp`** | `P_SMCFlex_Inp` | - | Hiện (Pin) | Common part of SMCFlex Input Assembly |
| **`Out`** | `P_SMCFlex_Out` | - | Hiện (Pin) | Common part of SMCFlex Output Assembly |
| **`Ref_FaultCodeList`** | `P_DescList[1]` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] Tag containing List of Fault Codes (DINT) and their Descriptions (STRING) |
| **`Inp_PermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Permissives OK, motor can start |
| **`Inp_NBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Non-Bypassable Permissives OK, motor can start |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Interlocks OK, motor can start/run |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Non-Bypassable Interlocks OK, motor can start/run |
| **`Inp_IntlkAvail`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Non-Bypassable Interlocks OK, motor can start/run |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Input Communication Status 0 = OK, 1 = fail |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Simulate working motor; 0 = Start/Stop/ Monitor actual motor |
| **`Inp_Hand`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Acquire Hand (typ. hardwired local), 0 = Release Hand |
| **`Inp_Ovrd`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Acquire Override (higher priority program logic), 0 = Release Override |
| **`Inp_OvrdCmd`** | `DINT` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Override Mode Command: 0 = None, 1 = Stop, 2=Start |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Cấm nguồn lệnh ngoài (External) chiếm quyền điều khiển; 0 = Cho phép External chiếm quyền. |
| **`Inp_HornInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Cấm còi báo động âm thanh; 0 = Cho phép phát còi báo trước khi khởi động |
| **`Inp_Reset`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Reset motor / starter fault conditions and latched Alarms |
| **`Cfg_PwrUnits`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Power is in Megawatts (MW), 0 = Power is in Kilowatts (kW) |
| **`Cfg_HasJog`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Cho phép và hiển thị nút nhấp thử (Jog) trên HMI; 0 = Cấm lệnh Jog. |
| **`Cfg_AllowLocal`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Allow Local Start/Stop without alarm, 0 = Start/Stop from HMI/prog only |
| **`Cfg_HasPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng reverse permissive liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng interlock liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasResInhObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng restart inhibit object is connected and navigation to the restart inhibit liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_HasRunTimeObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng runtime object is connected and navigation to the runtime liên kết với khối này và bật nút điều hướng mở cửa sổ Faceplate tương ứng (đặt tên thẻ theo quy ước chuẩn). |
| **`Cfg_OperStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = OCmd_Stop any time, 0 = OCmd_Stop only when Operator selected |
| **`Cfg_ExtStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = XCmd_Stop any time, 0 = XCmd_Stop only when Ext selected |
| **`Cfg_OCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Lệnh vận hành mới từ Operator tự động xóa luôn chốt lỗi cũ; 0 = Bắt buộc phải nhấn Reset trước khi ra lệnh mới. |
| **`Cfg_XCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Lệnh mới từ External tự động xóa luôn chốt lỗi cũ; 0 = Bắt buộc phải gửi lệnh Reset trước. |
| **`Cfg_OvrdPermIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Chế độ Override bỏ qua các liên động/điều kiện cho phép có thể bypass; 0 = Luôn tuân thủ mọi liên động. |
| **`Cfg_ShedOnFailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt lệnh chạy động cơ (Shed) và phát cảnh báo khi bị lỗi Không khởi động được (Fail to Start); 0 = Chỉ phát cảnh báo. |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt động cơ (Shed) và phát cảnh báo khi lỗi thẻ I/O; 0 = Chỉ phát cảnh báo. |
| **`Cfg_HasFailToStartAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Fail to Start alarm, Fail to Stop alarm, Interlock Trip alarm, Motor Fault alarm, or I/O Fault alarm exists and is checked. |
| **`Cfg_HasFailToStopAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Fail to Start alarm, Fail to Stop alarm, Interlock Trip alarm, Motor Fault alarm, or I/O Fault alarm exists and is checked. |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Fail to Start alarm, Fail to Stop alarm, Interlock Trip alarm, Motor Fault alarm, or I/O Fault alarm exists and is checked. |
| **`Cfg_HasMotorFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Fail to Start alarm, Fail to Stop alarm, Interlock Trip alarm, Motor Fault alarm, or I/O Fault alarm exists and is checked. |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Fail to Start alarm, Fail to Stop alarm, Interlock Trip alarm, Motor Fault alarm, or I/O Fault alarm exists and is checked. |
| **`Cfg_FailToStartResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Reset required to clear Fail to Start alarm, Fail to Stop alarm, Interlock Trip alarm, Motor Fault alarm, or I/O Fault alarm. |
| **`Cfg_FailToStopResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Reset required to clear Fail to Start alarm, Fail to Stop alarm, Interlock Trip alarm, Motor Fault alarm, or I/O Fault alarm. |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Reset required to clear Fail to Start alarm, Fail to Stop alarm, Interlock Trip alarm, Motor Fault alarm, or I/O Fault alarm. |
| **`Cfg_MotorFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Reset required to clear Fail to Start alarm, Fail to Stop alarm, Interlock Trip alarm, Motor Fault alarm, or I/O Fault alarm. |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Reset required to clear Fail to Start alarm, Fail to Stop alarm, Interlock Trip alarm, Motor Fault alarm, or I/O Fault alarm. |
| **`Cfg_FailToStartAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Xác nhận (Acknowledge) required for Fail to Start alarm, Fail to Stop alarm, Interlock Trip alarm, Motor Fault alarm, or I/O Fault alarm. |
| **`Cfg_FailToStopAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Xác nhận (Acknowledge) required for Fail to Start alarm, Fail to Stop alarm, Interlock Trip alarm, Motor Fault alarm, or I/O Fault alarm. |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Xác nhận (Acknowledge) required for Fail to Start alarm, Fail to Stop alarm, Interlock Trip alarm, Motor Fault alarm, or I/O Fault alarm. |
| **`Cfg_MotorFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Xác nhận (Acknowledge) required for Fail to Start alarm, Fail to Stop alarm, Interlock Trip alarm, Motor Fault alarm, or I/O Fault alarm. |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Xác nhận (Acknowledge) required for Fail to Start alarm, Fail to Stop alarm, Interlock Trip alarm, Motor Fault alarm, or I/O Fault alarm. |
| **`Cfg_FailToStartSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_FailToStopSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_MotorFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_StartHornT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian hú còi cảnh báo tại hiện trường trước khi đóng điện khởi động động cơ (giây, 0 = tắt còi). |
| **`Cfg_SimFdbkT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Time to echo back running / stopped feedback in Simulation (seconds) |
| **`Cfg_FailToStartT`** | `REAL` | `15` | Ẩn (Tag) | [Biến cấu hình thông số] Time after Start to get Run Feedback before Fault (seconds) |
| **`Cfg_FailToStopT`** | `REAL` | `15` | Ẩn (Tag) | [Biến cấu hình thông số] Time after Stop to drop Run Feedback before Fault (seconds) |
| **`Cfg_ResetPulseT`** | `REAL` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Time to pulse Out_Reset to clear motor fault |
| **`Cfg_MaxJogT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian giữ lệnh nhấp thử (Jog) tối đa cho phép (giây, 0 = không giới hạn). |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Mã định danh chủ sở hữu chương trình (Program Owner ID: khác 0 để đăng ký chiếm quyền, bằng 0 để nhả quyền). |
| **`PCmd_Start`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khởi động (Start) động cơ |
| **`PCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Dừng (Stop) động cơ |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để chuyển quyền điều khiển sang Tự động (từ Operator sang Program) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để chuyển quyền điều khiển sang Bằng tay (từ Program sang Operator) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa cứng chế độ Program (không cho phép Operator giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Mở khóa chế độ Program (cho phép Operator lấy quyền điều khiển) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để trả về nguồn lệnh Bình thường mặc định (Normal: Program hoặc Operator) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Reset motor fault and all Alarms requiring Reset |
| **`PCmd_FailToStartAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Fail to Start Alarm |
| **`PCmd_FailToStartSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Fail to Start Alarm |
| **`PCmd_FailToStartUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Fail to Start Alarm |
| **`PCmd_FailToStartUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Fail to Start Alarm |
| **`PCmd_FailToStopAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Fail to Stop Alarm |
| **`PCmd_FailToStopSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Fail to Stop Alarm |
| **`PCmd_FailToStopUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Fail to Stop Alarm |
| **`PCmd_FailToStopUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Fail to Stop Alarm |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Interlock Trip Alarm |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Interlock Trip Alarm |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Interlock Trip Alarm |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) InterlockTrip Alarm |
| **`PCmd_MotorFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Motor Fault Alarm |
| **`PCmd_MotorFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Motor Fault Alarm |
| **`PCmd_MotorFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Motor Fault Alarm |
| **`PCmd_MotorFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Motor Fault Alarm |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) I/O Fault Alarm |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) I/O Fault Alarm |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) I/O Fault Alarm |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) I/O Fault Alarm |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Release (Maintenance to Operator/Program/Override) |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Unlock Operator (allow Program to acquire) |
| **`XCmd_Start`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Khởi động (Start) động cơ |
| **`XCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Dừng (Stop) động cơ |
| **`XCmd_Jog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Nhấp thử (Jog) động cơ |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Acquire (Oper/Prog to Ext) |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Release (Ext to Oper/Prog) |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Người vận hành (Operator HMI) để Reset all Alarms requiring Reset |

## 3. Bảng chi tiết 104 Biến Đầu Ra (`Output Parameters`) của `P_SMCFlex`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Ngõ ra nối tầng Rung (EnableOut): Giữ nguyên trạng thái của chân đầu vào EnableIn để nối tiếp sang khối lệnh phía sau. |
| **`Inp`** | `P_SMCFlex_Inp` | - | Hiện (Pin) | Common part of SMCFlex Input Assembly |
| **`Out`** | `P_SMCFlex_Out` | - | Hiện (Pin) | Common part of SMCFlex Output Assembly |
| **`Ref_FaultCodeList`** | `P_DescList[1]` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] Tag containing List of Fault Codes (DINT) and their Descriptions (STRING) |
| **`Out_Horn`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1 = Sound audible prior to commanded motor start |
| **`Val_AvgPctFLA`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Average Current as Percent of Full Load Amps (%) |
| **`Val_PctTherm`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Motor Thermal Usage (%) (Trip / Fault at 100%) |
| **`Val_CurPhaseA`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Output Current in Phase A (Amps) |
| **`Val_CurPhaseB`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Output Current in Phase B (Amps) |
| **`Val_CurPhaseC`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Output Current in Phase C (Amps) |
| **`Val_RealPwr`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Power Usage of Motor (Real = V * A * PF) (kW or MW) |
| **`Val_PwrFact`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Power Factor (0.00 to 1.00) (Real power / Apparent power) |
| **`Val_LastFaultCode`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Last starter fault code (enum) (Par 124) |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Final controller status source and quality: GOOD 0 = I/O live and confirmed good quality 1 = I/O live and assumed good quality 2 = No feedback configured, assumed good quality TEST 8 = Device simulated 9 = Device loopback simulation 10 = Manually entered value UNCERTAIN 16 = Live input, off-specification 17 = Value substituted at device/bus 18 = Value substituted by maintenance (Has and not Use) 19 = Shed, using last good value 20 = Shed, using replacement value BAD 32 = Signal failure (out-of-range, NaN, invalid combination) 33 = I/O channel fault 34 = I/O module fault 35 = Bad I/O configuration (for example, scaling parameters) |
| **`Val_Cmd`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Command 0 = None, 1 = Stop, 2=StartFwd 3=StartRev 4=JogFwd 5=JogRev |
| **`Val_Fdbk`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Feedback 0 = Stopped, 1 = Running 3=Starting 4=Stopping |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Sts: 0 = ? 1 = Stopped 2=RunFwd 3=RunRev 4=RunSlow 5=StartFwd 6=StartRev 7=StartSlow 8=JogFwd 9=JogRev 10 = Stopping |
| **`Val_Fault`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Fault Status 0 = None, 16=Fail to Start, 17 = Fail to Stop, 18=Motor / Starter Fault, 32=I/O Fault, 34=Config Error |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Current Object Owner ID (0 = not owned) |
| **`Sts_Stopped`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor / Starter requested to stop and is confirmed stopped |
| **`Sts_Starting`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor / Starter requested to run and awaiting run feedback |
| **`Sts_Running`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor / Starter requested to run and is confirmed running |
| **`Sts_Stopping`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor / Starter requested to stop and awaiting stopped feedback |
| **`Sts_Jogging`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor requested to Jog |
| **`Sts_NotReady`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Device Not Ready, see detail bits for reason |
| **`Sts_Alarm`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor / Starter has an Alarm (see Motor display or manual) |
| **`Sts_AtSpeed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] Motor is running with full voltage applied |
| **`Sts_CtrlPwrOK`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Control Power OK, 0 = Control Power Absent |
| **`Sts_Phasing`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Phase Order = ABC, 0 = Phase Order = CBA |
| **`Sts_PhasingActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = 3-phase is valid 0 = No valid 3-phase detected |
| **`Sts_StartIsol`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Start/Isolation Contactor Enabled, 0 = Disabled |
| **`Sts_ExtBypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = External Bypass Contactor Enabled, 0 = Disabled |
| **`Sts_Opt1Inp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Option 1 Input is Active, 0 = Inactive |
| **`Sts_Opt2Inp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Option 2 Input is Active, 0 = Inactive |
| **`Sts_Available`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Motor available for control by automation (Prog) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bypassable Interlocks and Permissives are Bypassed |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bypassing Active (Bypassed or Maintenance) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Device Not Ready, see detail bits for reason |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_DriveNR`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Device Note Ready: Drive Not Ready |
| **`Nrdy_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi thiết bị ngắt tải (yêu cầu nhấn Reset) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Khóa liên động không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi truyền thông I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Thiết bị đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Nrdy_PrioStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang có lệnh Dừng ưu tiên từ Operator/External (yêu cầu nhấn Reset) |
| **`Nrdy_Perm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Cho phép khởi động không thỏa mãn (Permissive Not OK) |
| **`Nrdy_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Device not Ready: Deivce Tripped (Drive Fault) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=An Alarm is Disabled or Shelved, display icon |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình (kiểm tra các bit Err_* chi tiết để biết nguyên nhân) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Invalid Check or Reset Pulse Time (use 0 to 2147483) |
| **`Err_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: simulation timer preset: use 0 to 2147483) |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Alarm Min On Time or Severity |
| **`Sts_Hand`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Hand đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn OoS, Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_OoS`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Out of Service đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_Maint`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Maintenance đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ovrd, Ext, Prog, Oper) |
| **`Sts_Ovrd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Override đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ext, Prog, Oper) |
| **`Sts_Ext`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = External đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Prog, Oper) |
| **`Sts_Prog`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Program đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_ProgLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Program đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Oper`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_OperLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Selection equals the Normal (Prog or Oper) |
| **`Sts_ExtReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = External request inhibited, cannot Get to External from current state |
| **`Sts_ProgReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Program request inhibited, cannot Get to Program from current state |
| **`Sts_MAcqRcvd`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Maintenance Acquire command received this scan |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc lỗi ngắt tải (Shed) đã hết điều kiện lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Motor Failed to Start (One-Shot) |
| **`Alm_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Motor Fail to Start Alarm |
| **`Ack_FailToStart`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Fail to Start Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_FailToStartDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Start Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_FailToStartShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Start Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_FailToStartSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Start Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Motor Failed to Stop |
| **`Alm_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Motor Fail to Stop Alarm |
| **`Ack_FailToStop`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Fail to Stop Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_FailToStopDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Stop Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_FailToStopShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Stop Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_FailToStopSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Stop Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Motor was stopped by an Interlock NOT OK (One-Shot) |
| **`Alm_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Alarm: Motor stopped by an Interlock NOT OK |
| **`Ack_IntlkTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Interlock Trip Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IntlkTripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock Trip Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IntlkTripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock Trip Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IntlkTripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock Trip Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_MotorFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Motor Fault (see Motor display or manual) |
| **`Alm_MotorFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Alarm: Motor Fault (see Motor display or manual) |
| **`Ack_MotorFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Motor Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_MotorFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Motor Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_MotorFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Motor Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_MotorFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Motor Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] I/O Comm Fault Status (0 = OK, 1 = Bad) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = I/O Fault Alarm |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = I/O Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`MRdy_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`P_SMCFlex`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 30 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_SMCFlex`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'SMC-Flex Smart Motor Controller'` | [Biến cấu hình thông số] Description for display on HMI. This string is shown in the title bar of the faceplate. |
| **`Cfg_Label`** | `STRING_20` | `'Motor Control'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI. This string appears on the graphic symbol. |
| **`Cfg_Tag`** | `STRING_20` | `'P_SMCFlex'` | [Biến cấu hình thông số] Tag name for display on the HMI. This string is shown in the title bar of the faceplate. |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Command Source |
| **`FailToStart`** | `P_Alarm` | `{...}` | Motor Failed to Start Alarm |
| **`FailToStop`** | `P_Alarm` | `{...}` | Motor Failed to Stop Alarm |
| **`HMI_HasMore`** | `BOOL` | `0` | [Biến nội bộ giao diện HMI Faceplate] Motor Failed to Stop Alarm |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_SMCFlex'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | Interlock Trip Alarm |
| **`IOFault`** | `P_Alarm` | `{...}` | I/O Fault Alarm |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bypass all Bypassable Interlocks and Permissives |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Check (not bypass) all Interlocks and Permissives |
| **`MotorFault`** | `P_Alarm` | `{...}` | Motor Faulted Alarm |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`OCmd_Jog`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Nhấp thử (Jog) động cơ |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset all Alarms requiring Reset |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Reset all Alarms and latched Shed conditions |
| **`OCmd_Start`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Khởi động (Start) động cơ |
| **`OCmd_Stop`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Dừng (Stop) động cơ |
| **`ORdy_Jog`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Jog (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Start`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Start (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Stop`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Stop (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`Val_FaultDesc`** | `STRING_40` | `‘Logic not yet scanned.’` | [Giá trị thực tế đầu ra (Logic/HMI)] Description of Motor Fault (given LastFaultCode) |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |


---

# CHI TIẾT TOÀN BỘ BIẾN, CÁCH SỬ DỤNG & CẤU HÌNH KHỐI `P_VSD`
**Thuộc chương:** Chương 3: Động cơ, Biến tần & Rơ-le Quá tải (Motors & Drives)  
**Nguồn dữ liệu gốc:** Bảng thông số kỹ thuật `Motors 4.1.xlsx` đính kèm trong `PROCES-RM013I-EN-P`  
**Thống kê số lượng biến của `P_VSD`:** **153** Biến đầu vào (`Input Parameters`) | **125** Biến đầu ra (`Output Parameters`) | **45** Biến cấu hình cục bộ (`Local Configuration Tags`)

## 1. Hướng dẫn Cách sử dụng, Đấu nối chân & Cấu hình các biến trong `P_VSD`
1. **Các chân đầu vào cần nối dây tín hiệu (`Inp_*` / Chân hiển thị trên khối FBD/Ladder):**
   - Các biến tiêu biểu: `Ref_FaultCodeList`, `Inp_SpeedFdbk`, `Inp_LastFaultCode`, `Inp_Datalink`, `Inp_Ready`, `Inp_Running`, `Inp_CommandDir`, `Inp_ActualDir`, `Inp_Accelerating`, `Inp_Decelerating`, `Inp_Alarm`, `Inp_Faulted`, `Inp_AtSpeed`, `Inp_FwdPermOK`, `Inp_FwdNBPermOK`, `Inp_RevPermOK`, `Inp_RevNBPermOK`, `Inp_IntlkOK`...
   - **Cách đấu nối:** Nối trực tiếp từ thẻ I/O vật lý (ví dụ `Local:1:I.Ch0Data`, `Local:2:I.Data.0`) hoặc từ ngõ ra trạng thái của các khối liên kết (`P_Intlk.Sts_IntlkOK`, `P_Perm.Sts_PermOK`, `P_AIChan.SrcQ`).
2. **Cách cấu hình các biến thiết lập (`Cfg_*` và `Local Configuration Tags`):**
   - Các biến `Cfg_*` trong Input Parameters: `Cfg_HasReverse`, `Cfg_HasJog`, `Cfg_HasMoreObj`, `Cfg_AllowLocal`, `Cfg_HasRunFdbk`, `Cfg_UseRunFdbk`, `Cfg_HasSpeedFdbk`, `Cfg_UseSpeedFdbk`, `Cfg_HasInpDatalink`, `Cfg_HasOutDatalink`, `Cfg_HasFwdPermObj`, `Cfg_HasRevPermObj`, `Cfg_HasIntlkObj`, `Cfg_HasResInhObj`, `Cfg_HasRunTimeObj`, `Cfg_SetTrack`, `Cfg_SetTrackOvrdHand`, `Cfg_OperStopPrio`...
   - Các biến `Cfg_*` trong Local Tags (hiển thị HMI): `Cfg_Area`, `Cfg_Desc`, `Cfg_FwdText`, `Cfg_InpDatalinkEU`, `Cfg_InpDatalinkLabel`, `Cfg_Label`, `Cfg_OutDatalinkEU`, `Cfg_OutDatalinkLabel`, `Cfg_RevText`, `Cfg_SpeedFdbkEU`, `Cfg_SpeedRefEU`, `Cfg_Tag`
   - **Cách cấu hình:** Trong phần mềm **Studio 5000 Logix Designer**, nhấp chuột phải vào thẻ khối lệnh -> chọn **Monitor Tags** (hoặc mở tab **Engineering / Maintenance** trên cửa sổ HMI Faceplate khi đang chạy Online) để nhập giá trị dải đo, giới hạn cảnh báo, thời gian trễ và bật các tính năng phần cứng (`Cfg_Has* = 1`, `Cfg_Use* = 1`). Lưu ý kiểm tra cờ `Sts_Err = 0` sau khi cấu hình để đảm bảo không có thông số nào bị nhập sai.
3. **Cách điều khiển khối `P_VSD` từ Chương trình tự động (`PCmd_*` và `PSet_*`):**
   - Các biến lệnh & giá trị đặt Program: `PSet_SpeedRef`, `PSet_OutDatalink`, `PSet_Owner`, `PCmd_StartFwd`, `PCmd_StartRev`, `PCmd_Stop`, `PCmd_Prog`, `PCmd_Oper`, `PCmd_Lock`, `PCmd_Unlock`, `PCmd_Normal`, `PCmd_Reset`, `PCmd_FailToStartAck`, `PCmd_FailToStartSuppress`, `PCmd_FailToStartUnsuppress`, `PCmd_FailToStartUnshelve`, `PCmd_FailToStopAck`, `PCmd_FailToStopSuppress`...
   - **Cách sử dụng trong code PLC:** Trước khi gửi lệnh tự động, kiểm tra khối đang ở chế độ **Program** (`Sts_Prog = 1` hoặc `Sts_Available = 1`). Nếu đang ở chế độ Operator, kích xung `PCmd_Prog = 1` (hoặc `PCmd_Acq = 1`) để chuyển sang Program, sau đó gán giá trị vào `PSet_*` và kích bit `PCmd_* = 1` (khối sẽ tự xóa bit `PCmd_*` về 0 sau khi nhận lệnh).
4. **Cách đọc Giá trị, Trạng thái và Cảnh báo (`Out_*`, `Val_*`, `Sts_*`, `Alm_*`):**
   - Các biến đầu ra tiêu biểu: `Ref_FaultCodeList`, `Out_SpeedRef`, `Out_Datalink`, `Out_Run`, `Out_Stop`, `Out_Start`, `Out_Jog`, `Out_ClearFault`, `Out_Fwd`, `Out_Rev`, `Out_Horn`, `Val_SpeedRef`, `Val_SpeedFdbk`, `Val_InpDatalink`, `Val_OutDatalink`, `Val_SpeedRefEUMin`, `Val_SpeedRefEUMax`, `Val_SpeedFdbkEUMin`...
   - **Cách sử dụng:** Nối chân `Out_*` ra thẻ Output vật lý. Sử dụng các bit `Sts_*` (trạng thái thực tế) để đưa vào khối liên động `P_Intlk` / `P_Perm` hoặc điều kiện chuyển bước `P_Seq`. Sử dụng các bit `Alm_*` để kích hoạt còi/đèn báo động.

## 2. Bảng chi tiết 153 Biến Đầu Vào (`Input Parameters`) của `P_VSD`
| Tên biến (Input Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng, Cách sử dụng & Cấu hình chi tiết |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableIn`** | `BOOL` | `1` | Ẩn (Tag) | [Chân kích hoạt Rung] Chân cho phép thực thi khối AOI: Trong Ladder (LD), nếu nhánh Rung = True thì chạy chương trình chính (Logic routine), nếu Rung = False thì chạy chương trình xử lý khi ngắt (EnableInFalse routine). Trong FBD, nếu bằng 1 hoặc không nối dây thì khối hoạt động bình thường. |
| **`Ref_FaultCodeList`** | `P_DescList[1]` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] Tag containing List of Fault Codes (DINT) and their Descriptions (STRING) |
| **`Inp_SpeedFdbk`** | `REAL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Speed Feedbak in Drive Units (typ. 0-32767 = 0 to max freq.) |
| **`Inp_LastFaultCode`** | `DINT` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] Most recent drive fault code (enumeration) |
| **`Inp_Datalink`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Auxiliary Signal (datalink) Input in Drive (raw) Units |
| **`Inp_Ready`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Drive is ready to run |
| **`Inp_Running`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Drive is Running (active) |
| **`Inp_CommandDir`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Drive is commanded Forward, 0 = Drive is commanded Reverse |
| **`Inp_ActualDir`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Drive is running Forward, 0 = Drive is running Reverse |
| **`Inp_Accelerating`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Drive is accelerating |
| **`Inp_Decelerating`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Drive is decelerating |
| **`Inp_Alarm`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Drive has an Alarm Condition (see drive display or manual) |
| **`Inp_Faulted`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Drive has Faulted (see drive display or manual) |
| **`Inp_AtSpeed`** | `BOOL` | `0` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Drive is at commanded speed |
| **`Inp_FwdPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Permissives OK, drive can start Forward |
| **`Inp_FwdNBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Non-Bypassable Permissives OK, drive can start Forward |
| **`Inp_RevPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Permissives OK, drive can start Reverse |
| **`Inp_RevNBPermOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Non-Bypassable Permissives OK, motor can start Reverse |
| **`Inp_IntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Interlocks OK, drive can start/run |
| **`Inp_NBIntlkOK`** | `BOOL` | `1` | Hiện (Pin) | [Đầu vào nối dây I/O hoặc Logic] 1 = Non-Bypassable Interlocks OK, drive can start/run |
| **`Inp_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Input Communication Status 0 = OK, 1 = fail |
| **`Inp_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Simulation input. When set to 1, the instruction keeps outputs de-energized (zero) and simulates a working drive. When set to 0, the instruction operates the drive normally. |
| **`Inp_Hand`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Acquire Hand (typ. hardwired local), 0 = Release Hand |
| **`Inp_Ovrd`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Acquire Override (higher priority program logic), 0 = Release Override |
| **`Inp_OvrdCmd`** | `DINT` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Override Command: 0 = None, 1 = Stop, 2=Start Fwd 3=Start Rev |
| **`Inp_OvrdSpeed`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Value to set Speed Reference in Override Mode (SpeedRef Engineering Units) |
| **`Inp_OvrdOutDatalink`** | `REAL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] Value to set Output Datalink in Override Mode (OutDatalink Engineering Units) |
| **`Inp_ExtInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Cấm nguồn lệnh ngoài (External) chiếm quyền điều khiển; 0 = Cho phép External chiếm quyền. |
| **`Inp_HornInh`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Cấm còi báo động âm thanh; 0 = Cho phép phát còi báo trước khi khởi động |
| **`Inp_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Reset Shed Latches and Cleared Alarms |
| **`Inp_IntlkAvail`** | `BOOL` | `0` | Ẩn (Tag) | [Đầu vào nối dây I/O hoặc Logic] 1 = Interlock Availibility OK |
| **`Cfg_HasReverse`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Drive can be run reverse, 0 = Forward only |
| **`Cfg_HasJog`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Drive Jog Command enabled/visible, 0 = Drive Jog Command not allowed |
| **`Cfg_HasMoreObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Báo cho HMI biết có đối tượng mở rộng thông tin đi kèm để bật nút điều hướng. |
| **`Cfg_AllowLocal`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Allow Local Start/Stop without alarm, 0 = Start/Stop from HMI/prog only |
| **`Cfg_HasRunFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Drive provides feedback signal on Inp_Running when running |
| **`Cfg_UseRunFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Drive run feedback should be used for failure checking |
| **`Cfg_HasSpeedFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Drive provides speed feedback on Inp_SpeedFdbk |
| **`Cfg_UseSpeedFdbk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Drive speed determines running state, 0 = state determined by drive Active input bit |
| **`Cfg_HasInpDatalink`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = A signal is connected to Inp_Datalink |
| **`Cfg_HasOutDatalink`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = A signal is connected to Out_Datalink |
| **`Cfg_HasFwdPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tells HMI a permissive object (for example, P_Perm) is connected to Inp_FwdPerm or Inp_RevPerm. IMPORTANT: The name of the Permissive object in the controller must be this object's name with the suffix ‘_FwdPerm’ or '_RevPerm'. For example, if your P_VSD object has the name ’Drive123’, then its Permissive object must be named ‘Drive123_FwdPerm’ or 'Drive123_RevPerm'. |
| **`Cfg_HasRevPermObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tells HMI a permissive object (for example, P_Perm) is connected to Inp_FwdPerm or Inp_RevPerm. IMPORTANT: The name of the Permissive object in the controller must be this object's name with the suffix ‘_FwdPerm’ or '_RevPerm'. For example, if your P_VSD object has the name ’Drive123’, then its Permissive object must be named ‘Drive123_FwdPerm’ or 'Drive123_RevPerm'. |
| **`Cfg_HasIntlkObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tells HMI an interlock object (for example, P_Intlk) is connected to Inp_Intlk. IMPORTANT: The name of the Interlock object in the controller must be this object's name with the suffix ‘_Intlk’. For example, if your P_VSD object has the name’Valve123’, then its Interlock object must be named‘Drive123_Intlk’. |
| **`Cfg_HasResInhObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tells HMI a P_ResInh Restart Inhibit is connected IMPORTANT: The name of the Restart Inhibit object in the controller must be this object's name with the suffix‘_ResInh’. For example, if your P_VSD object has the name‘Drive123’, then its Restart Inhibit object must be named ‘Drive123_ResInh’. |
| **`Cfg_HasRunTimeObj`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Tells HMI a P_RunTime is connected IMPORTANT: The name of the runtime object in the controller must be this object's name with the suffix ‘_RunTime’. For example, if your P_VSD object has the name‘Drive123’, then its runtime object must be named‘Drive123_RunTime’. |
| **`Cfg_SetTrack`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] This parameter is used to set up bumpless behavior of setting parameters when switching modes. When this parameter is 1, in Program mode the operator settings track the program settings; in Operator mode the program settings track the operator settings; and the simulation inputs match the output values (transitions are bumpless). When this parameter is 0, the operator settings and program settings are not modified by this instruction. In this case, when the mode is changed, the effective value of the setting can change depending on the program-set and operator-set values. |
| **`Cfg_SetTrackOvrdHand`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Program/Operator/Ext Settings track Override/Hand |
| **`Cfg_OperStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = OCmd_Stop any time, 0 = OCmd_Stop only when Operator selected |
| **`Cfg_ExtStopPrio`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = XCmd_Stop any time, 0 = XCmd_Stop only when Ext selected |
| **`Cfg_OCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = New drive OCmd resets shed latches and cleared alarms, 0 = OCmdReset req'd. |
| **`Cfg_XCmdResets`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = New drive XCmd resets shed latches and cleared alarms, 0 = XCmdReset req'd. |
| **`Cfg_OvrdPermIntlk`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Chế độ Override bỏ qua các liên động/điều kiện cho phép có thể bypass; 0 = Luôn tuân thủ mọi liên động. |
| **`Cfg_ShedOnFailToStart`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt lệnh chạy động cơ (Shed) và phát cảnh báo khi bị lỗi Không khởi động được (Fail to Start); 0 = Chỉ phát cảnh báo. |
| **`Cfg_ShedOnIOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = Ngắt động cơ (Shed) và phát cảnh báo khi lỗi thẻ I/O; 0 = Chỉ phát cảnh báo. |
| **`Cfg_SimScaleEU`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = In simulation, scale speed reference engineering units to speed feedback engineering units. 0 = If both Cfg_SimScaleEU and Cfg_SimScaleRaw are 0, then in simulation, the speed reference is copied to the speed feedback without scaling. |
| **`Cfg_SimScaleRaw`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] 1 = In simulation, scale speed reference engineering units to raw, then raw to speed feedback Engineering Units 0 = If both Cfg_SimScaleRaw and Cfg_SimScaleEU are 0, then in simulation, the speed reference is copied to the speed feedback without scaling. |
| **`Cfg_DecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Number of decimal places for speed ref/fdbk display (0..6) |
| **`Cfg_InpDLDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Number of decimal places for Input Datalink display (0..6) |
| **`Cfg_OutDLDecPlcs`** | `DINT` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Number of decimal places for Output Datalink display (0..6) |
| **`Cfg_HasFailToStartAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasFailToStopAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasIntlkTripAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasDriveFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_HasIOFaultAlm`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình bật/tắt sự tồn tại của cảnh báo: Khi = 1, cảnh báo tương ứng tồn tại và được giám sát; khi = 0, cảnh báo bị vô hiệu hóa. |
| **`Cfg_FailToStartResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_FailToStopResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IntlkTripResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_DriveFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_IOFaultResetReqd`** | `BOOL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu nhấn Reset để xóa cảnh báo: Khi = 1, cảnh báo tự chốt giữ (Latch) và bắt buộc phải có lệnh Reset sau khi tín hiệu đã về bình thường mới xóa được. Khi = 0, cảnh báo tự xóa ngay khi tín hiệu trở về bình thường. |
| **`Cfg_FailToStartAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_FailToStopAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IntlkTripAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_DriveFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_IOFaultAckReqd`** | `BOOL` | `1` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình yêu cầu Xác nhận (Acknowledge) cảnh báo: Khi = 1, bắt buộc người vận hành hoặc chương trình gửi lệnh Ack khi có báo động. Khi = 0, cảnh báo tự động được xác nhận ngay khi xuất hiện. |
| **`Cfg_FailToStartSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_FailToStopSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IntlkTripSeverity`** | `DINT` | `500` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_DriveFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_IOFaultSeverity`** | `DINT` | `1000` | Ẩn (Tag) | [Biến cấu hình thông số] Cấu hình mức độ nghiêm trọng (Severity) của cảnh báo để điều khiển màu sắc và biểu tượng trên HMI: 1..250 = Thấp (Low), 251..500 = Trung bình (Medium), 501..750 = Cao (High), 751..1000 = Khẩn cấp (Urgent). |
| **`Cfg_MinSpdRef`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum Speed Reference in Engineering Units (for limiting) |
| **`Cfg_MaxSpdRef`** | `REAL` | `60` | Ẩn (Tag) | [Biến cấu hình thông số] Maximum Speed Reference in Engineering Units (for limiting) |
| **`Cfg_SpeedRefRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Reference Minimum in Drive (raw) Units (for scaling) |
| **`Cfg_SpeedRefRawMax`** | `REAL` | `32767` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Reference Maximum in Drive (raw) Units (for scaling) |
| **`Cfg_SpeedRefEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Reference Minimum in Engineering Units (for scaling) |
| **`Cfg_SpeedRefEUMax`** | `REAL` | `130` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Reference Maximum in Engineering Units (for scaling) |
| **`Cfg_SpeedFdbkRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Feedback Minimum in Drive (raw) Units (for scaling) |
| **`Cfg_SpeedFdbkRawMax`** | `REAL` | `32767` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Feedback Maximum in Drive (raw) Units (for scaling) |
| **`Cfg_SpeedFdbkEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Feedback Minimum in Engineering Units (for scaling) |
| **`Cfg_SpeedFdbkEUMax`** | `REAL` | `130` | Ẩn (Tag) | [Biến cấu hình thông số] Speed Feedback Maximum in Engineering Units (for scaling) |
| **`Cfg_InpDatalinkRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Input Datalink Minimum in Drive (raw) Units (for scaling) |
| **`Cfg_InpDatalinkRawMax`** | `REAL` | `4095` | Ẩn (Tag) | [Biến cấu hình thông số] Input Datalink Maximum in Drive (raw) Units (for scaling) |
| **`Cfg_InpDatalinkEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Input Datalink Minimum in Engineering Units (for scaling) |
| **`Cfg_InpDatalinkEUMax`** | `REAL` | `409.5` | Ẩn (Tag) | [Biến cấu hình thông số] Input Datalink Maximum in Engineering Units (for scaling) |
| **`Cfg_OutDatalinkMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Minimum Output Datalink in Engineering Units (for limiting) |
| **`Cfg_OutDatalinkMax`** | `REAL` | `100` | Ẩn (Tag) | [Biến cấu hình thông số] Maximum Output Datalink in Engineering Units (for limiting) |
| **`Cfg_OutDatalinkRawMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Output Datalink Minimum in Drive (raw) Units (for scaling) |
| **`Cfg_OutDatalinkRawMax`** | `REAL` | `4095` | Ẩn (Tag) | [Biến cấu hình thông số] Output Datalink Maximum in Drive (raw) Units (for scaling) |
| **`Cfg_OutDatalinkEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Output Datalink Minimum in Engineering Units (for scaling) |
| **`Cfg_OutDatalinkEUMax`** | `REAL` | `409.5` | Ẩn (Tag) | [Biến cấu hình thông số] Output Datalink Maximum in Engineering Units (for scaling) |
| **`Cfg_StartHornT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian hú còi cảnh báo tại hiện trường trước khi đóng điện khởi động động cơ (giây, 0 = tắt còi). |
| **`Cfg_SimRampT`** | `REAL` | `10` | Ẩn (Tag) | [Biến cấu hình thông số] Time to ramp speed fdbk when in Simulation (seconds) |
| **`Cfg_FailToStartT`** | `REAL` | `15` | Ẩn (Tag) | [Biến cấu hình thông số] Time after Start to get Run Feedback before Fault (seconds) |
| **`Cfg_FailToStopT`** | `REAL` | `15` | Ẩn (Tag) | [Biến cấu hình thông số] Time after Stop to drop Run Feedback before Fault (seconds) |
| **`Cfg_ResetPulseT`** | `REAL` | `2` | Ẩn (Tag) | [Biến cấu hình thông số] Time to pulse Out_Reset to clear drive fault |
| **`Cfg_MaxJogT`** | `REAL` | `0` | Ẩn (Tag) | [Biến cấu hình thông số] Thời gian giữ lệnh nhấp thử (Jog) tối đa cho phép (giây, 0 = không giới hạn). |
| **`Cfg_OperKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Operator keeps control in Program / Ext: .0 = Reference, .1 = Start/Stop/Fwd/ Rev, .3=OutDatalink |
| **`Cfg_ProgKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Program keeps control in Operator / Ext: .0 = Reference, .1 = Start/Stop/Fwd/ Rev, .3=OutDatalink |
| **`Cfg_ExtKeep`** | `SINT` | `2#0000_0000` | Ẩn (Tag) | [Biến cấu hình thông số] Ext keeps control in Program / Oper: .0 = Reference, .1 = Start/Stop/Fwd/ Rev, .3=OutDatalink |
| **`PSet_SpeedRef`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Program Setting of Speed Reference (Engineering Units) |
| **`PSet_OutDatalink`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Program Setting of Output Datalink (Engineering Units) |
| **`PSet_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị đặt từ Chương trình PLC] Mã định danh chủ sở hữu chương trình (Program Owner ID: khác 0 để đăng ký chiếm quyền, bằng 0 để nhả quyền). |
| **`XSet_SpeedRef`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài (External)] External Setting of Speed Reference (Engineering Units) |
| **`XSet_OutDatalink`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị đặt từ Hệ thống ngoài (External)] External Setting of Output Datalink (Engineering Units) |
| **`PCmd_StartFwd`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Start Drive Forward |
| **`PCmd_StartRev`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Start Drive Reverse |
| **`PCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Stop Drive |
| **`PCmd_Prog`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để chuyển quyền điều khiển sang Tự động (từ Operator sang Program) |
| **`PCmd_Oper`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để chuyển quyền điều khiển sang Bằng tay (từ Program sang Operator) |
| **`PCmd_Lock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Khóa cứng chế độ Program (không cho phép Operator giành quyền) |
| **`PCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Mở khóa chế độ Program (cho phép Operator lấy quyền điều khiển) |
| **`PCmd_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để trả về nguồn lệnh Bình thường mặc định (Normal: Program hoặc Operator) |
| **`PCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Reset drive fault and all Alarms requiring Reset |
| **`PCmd_FailToStartAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Fail to Start Alarm |
| **`PCmd_FailToStartSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Fail to Start Alarm |
| **`PCmd_FailToStartUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Fail to Start Alarm |
| **`PCmd_FailToStartUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Fail to Start Alarm |
| **`PCmd_FailToStopAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Fail to Stop Alarm |
| **`PCmd_FailToStopSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Fail to Stop Alarm |
| **`PCmd_FailToStopUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Fail to Stop Alarm |
| **`PCmd_FailToStopUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Fail to Stop Alarm |
| **`PCmd_IntlkTripAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Interlock Trip Alarm |
| **`PCmd_IntlkTripSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Interlock Trip Alarm |
| **`PCmd_IntlkTripUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Interlock Trip Alarm |
| **`PCmd_IntlkTripUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Interlock Trip Alarm |
| **`PCmd_DriveFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) Drive Fault Alarm |
| **`PCmd_DriveFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) Drive Fault Alarm |
| **`PCmd_DriveFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) Drive Fault Alarm |
| **`PCmd_DriveFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) Drive Fault Alarm |
| **`PCmd_IOFaultAck`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Xác nhận (Acknowledge) I/O Fault Alarm |
| **`PCmd_IOFaultSuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Ức chế (Suppress) I/O Fault Alarm |
| **`PCmd_IOFaultUnsuppress`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ ức chế (Unsuppress) I/O Fault Alarm |
| **`PCmd_IOFaultUnshelve`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Chương trình PLC (Xung 1-shot)] Lệnh từ Chương trình tự động (Program) để Gỡ bỏ tạm hoãn (Unshelve) I/O Fault Alarm |
| **`MCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Release (Maintenance to Operator/Program/Override) |
| **`OCmd_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Unlock Operator (allow Program to acquire) |
| **`XCmd_Acq`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Acquire (Oper/Prog to Ext) |
| **`XCmd_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Release (Ext to Oper/Prog) |
| **`XCmd_StartFwd`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Start Drive Forward |
| **`XCmd_StartRev`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Start Drive Reverse |
| **`XCmd_Stop`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Stop Drive |
| **`XCmd_JogFwd`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Jog Drive Forward |
| **`XCmd_JogRev`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Lệnh từ Hệ thống ngoài (External) để Jog Drive Reverse |
| **`XCmd_Reset`** | `BOOL` | `0` | Ẩn (Tag) | [Lệnh từ Hệ thống ngoài (External)] Ext Cmd to clear shed latches and cleared alarms |

## 3. Bảng chi tiết 125 Biến Đầu Ra (`Output Parameters`) của `P_VSD`
| Tên biến (Output Parameter) | Kiểu dữ liệu | Mặc định | Hiển thị chân | Giải thích chức năng & Cách sử dụng trong Logic/HMI |
| :--- | :--- | :--- | :--- | :--- |
| **`EnableOut`** | `BOOL` | `0` | Ẩn (Tag) | [Chân nối tầng Rung] Enable Output - System Defined Parameter |
| **`Ref_FaultCodeList`** | `P_DescList[1]` | - | Hiện (Pin) | [Tham chiếu InOut nối tới cấu trúc ngoài] Tag containing List of Fault Codes (DINT) and their Descriptions (STRING) |
| **`Out_SpeedRef`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Drive Speed Reference in Drive Units (typ. 0-32767 = 0 to max freq.) |
| **`Out_Datalink`** | `REAL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] Auxiliary Signal (datalink) Output in Drive (raw) Units |
| **`Out_Run`** | `BOOL` | `0` | Ẩn (Tag) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1 = Start/Run Drive, 0 = Stop Drive (for held starter type) |
| **`Out_Stop`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1 = Stop Drive, 0 = drive left in current state |
| **`Out_Start`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1 = Start Drive, 0 = drive left in current state |
| **`Out_Jog`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1 = Jog drive at Jog Speed, -->0 = stop jogging |
| **`Out_ClearFault`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1 = Attempt to clear Drive Fault |
| **`Out_Fwd`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1 = Set drive direction to Forward |
| **`Out_Rev`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1 = Set drive direction to Reverse |
| **`Out_Horn`** | `BOOL` | `0` | Hiện (Pin) | [Ngõ ra vật lý điều khiển thiết bị/I/O] 1 = Sound audible prior to commanded motor start |
| **`Val_SpeedRef`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Speed Reference (target) to drive |
| **`Val_SpeedFdbk`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Speed Feedback (actual) from drive |
| **`Val_InpDatalink`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Input Datalink value from drive |
| **`Val_OutDatalink`** | `REAL` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Output Datalink value to drive |
| **`Val_SpeedRefEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Minimum of Speed Reference = MIN (Cfg_SpeedFdbkEUMin, Cfg_SpeedFdbkEUMax) |
| **`Val_SpeedRefEUMax`** | `REAL` | `130` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Maximum of Speed Reference = MAX (Cfg_SpeedFdbkEUMin, Cfg_SpeedFdbkEUMax) |
| **`Val_SpeedFdbkEUMin`** | `REAL` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Minimum of Speed Feedback = MIN (Cfg_SpeedFdbkEUMin, Cfg_SpeedFdbkEUMax) |
| **`Val_SpeedFdbkEUMax`** | `REAL` | `130` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Maximum of Speed Feedback = MAX (Cfg_SpeedFdbkEUMin, Cfg_SpeedFdbkEUMax) |
| **`Val_LastFaultCode`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Most recent drive fault code (enumeration) |
| **`SrcQ_IO`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Mã nguồn gốc và chất lượng của kênh I/O sơ cấp (kiểu liệt kê enum) |
| **`SrcQ`** | `DINT` | `0` | Ẩn (Tag) | [Mã nguồn gốc & chất lượng tín hiệu] Source and Quality of primary Val/Sts (enumeration) |
| **`Val_Cmd`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Command 0 = None, 1 = Stop, 2=StartFwd 3=StartRev 4=JogFwd 5=JogRev |
| **`Val_Fdbk`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Feedback 0 = Stopped, 1 = Running Fwd 2=Running Rev 3=Accelerating 4=Decelerating |
| **`Val_Sts`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Sts: 0 = ? 1 = Stopped 2=RunFwd 3=RunRev 4=RunSlow 5=StartFwd 6=StartRev 7=StartSlow 8=JogFwd 9=JogRev 10 = Stopping |
| **`Val_Fault`** | `DINT` | `0` | Hiện (Pin) | [Giá trị thực tế đầu ra (Logic/HMI)] Device Fault Status 0 = None, 16=Fail to Start, 17 = Fail to Stop, 18=Drive Fault, 32=I/O Fault, 34=Config Error |
| **`Val_NotifyAll`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ ưu tiên cảnh báo cao nhất và trạng thái xác nhận của khối này cộng với khối kênh (enum) |
| **`Val_UnackAlmC`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Tổng số lượng cảnh báo hiện đang chưa được xác nhận (Unacknowledged Alarms) |
| **`Val_Owner`** | `DINT` | `0` | Ẩn (Tag) | [Giá trị thực tế đầu ra (Logic/HMI)] Current Object Owner ID (0 = not owned) |
| **`Sts_Stopped`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive requested to stop and is confirmed stopped |
| **`Sts_StartingFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive requested to run forward and awaiting run feedback |
| **`Sts_StartingRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive requested to run reverse and awaiting run feedback |
| **`Sts_RunningFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive requested to run and is confirmed running forward |
| **`Sts_RunningRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive requested to run and is confirmed running reverse |
| **`Sts_StoppingFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive running forward requested to stop and awaiting stopped feedback |
| **`Sts_StoppingRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive running reverse requested to stop and awaiting stopped feedback |
| **`Sts_JoggingFwd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive requested to Jog Forward |
| **`Sts_JoggingRev`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive requested to Jog Reverse |
| **`Sts_CommandDir`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive commanded to Forward, 0 = Reverse |
| **`Sts_ActualDir`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive Actual direction is Forward, 0 = Reverse |
| **`Sts_Accel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive is Accelerating |
| **`Sts_Decel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive is Decelerating |
| **`Sts_NotReady`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive is Not Ready (cannot be started) Check alarms, stops, faults |
| **`Sts_Alarm`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive has an Alarm (see drive display or manual) |
| **`Sts_AtSpeed`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive is running at reference speed |
| **`Sts_SpeedLimited`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Speed Reference Setting exceeds configured Max/Min limit |
| **`Sts_Available`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Drive available for control by automation (Prog) |
| **`Sts_Bypass`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bypassable Interlocks and Permissives are Bypassed |
| **`Sts_BypActive`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Bypassing Active (Bypassed or Maintenance) |
| **`Sts_NotRdy`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Device Not Ready, see detail bits for reason |
| **`Nrdy_CfgErr`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi cài đặt thông số cấu hình (Configuration Error) |
| **`Nrdy_DriveNR`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Device Note Ready: Drive Not Ready |
| **`Nrdy_Fail`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi thiết bị ngắt tải (yêu cầu nhấn Reset) |
| **`Nrdy_Intlk`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Khóa liên động không thỏa mãn (Interlock Not OK) |
| **`Nrdy_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Lỗi truyền thông I/O (yêu cầu nhấn Reset) |
| **`Nrdy_OoS`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Thiết bị đang ở chế độ Ngưng phục vụ (Out of Service) |
| **`Nrdy_Perm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Điều kiện Cho phép khởi động không thỏa mãn (Permissive Not OK) |
| **`Nrdy_PrioStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Thiết bị chưa sẵn sàng hoạt động do: Đang có lệnh Dừng ưu tiên từ Operator/External (yêu cầu nhấn Reset) |
| **`Nrdy_Trip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo nguyên nhân thiết bị chưa sẵn sàng] 1 = Device not Ready: Deivce Tripped (Drive Fault) |
| **`Sts_MaintByp`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Đang có tác vụ Bypass bảo trì hoạt động (hiển thị biểu tượng Bypass trên HMI) |
| **`Sts_AlmInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1=An Alarm is Disabled or Shelved, display icon |
| **`Sts_Err`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Có lỗi cài đặt thông số cấu hình (kiểm tra các bit Err_* chi tiết để biết nguyên nhân) |
| **`Err_Timer`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Invalid Check or Reset Pulse Time (use 0 to 2147483) |
| **`Err_Sim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: simulation timer preset: use 0 to 2147483) |
| **`Err_Alarm`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Lỗi cấu hình: Thông số cảnh báo (Thời gian duy trì tối thiểu, thời gian Shelve, mức Severity) không hợp lệ |
| **`Err_FdbkRaw`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Erron in Config: Speed Fdbk Raw Min = Max |
| **`Err_FdbkEU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Speed Fdbk Engineering Units Min = Max |
| **`Err_RefLim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Speed Ref Limit Min > Max |
| **`Err_RefEU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Speed Ref Engineering Units Min = Max |
| **`Err_RefRaw`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Speed Ref Raw Min = Max |
| **`Err_InpDLRaw`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Input Datalink Raw Min = Max |
| **`Err_InpDLEU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Input Datalink Engineering Units Min = Max |
| **`Err_OutDLLim`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Output Datalink Limits Min > Max |
| **`Err_OutDLEU`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Output Datalink Engineering Units Min = Max |
| **`Err_OutDLRaw`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo lỗi cài đặt thông số Cfg_*] 1 = Error in Config: Output Datalink Raw Min = Max |
| **`Sts_Hand`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Hand đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn OoS, Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_OoS`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Out of Service đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Maint, Ovrd, Ext, Prog, Oper) |
| **`Sts_Maint`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Maintenance đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ovrd, Ext, Prog, Oper) |
| **`Sts_Ovrd`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Override đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Ext, Prog, Oper) |
| **`Sts_Ext`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = External đang được chọn làm Nguồn lệnh điều khiển hiện hành (ưu tiên cao hơn Prog, Oper) |
| **`Sts_Prog`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Program đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_ProgLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Program đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Oper`** | `BOOL` | `0` | Hiện (Pin) | [Cờ báo trạng thái logic (Status)] 1 = Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành |
| **`Sts_OperLocked`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Operator đang được chọn làm Nguồn lệnh điều khiển hiện hành and Locked |
| **`Sts_Normal`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Selection equals the Normal (Prog or Oper) |
| **`Sts_ExtReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = External request inhibited, cannot Get to External from current state |
| **`Sts_ProgReqInh`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Program request inhibited, cannot Get to Program from current state |
| **`Sts_MAcqRcvd`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Maintenance Acquire command received this scan |
| **`Sts_RdyReset`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo chốt (Latched) hoặc lỗi ngắt tải (Shed) đã hết điều kiện lỗi và sẵn sàng nhận lệnh Reset |
| **`Sts_RdyAck`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Có cảnh báo mới đang chờ được nhấn Xác nhận (Acknowledge) |
| **`Sts_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive Failed to Start (One-Shot) |
| **`Alm_FailToStart`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Drive Fail to Start Alarm |
| **`Ack_FailToStart`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Fail to Start Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_FailToStartDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Start Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_FailToStartShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Start Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_FailToStartSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Start Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive Failed to Stop |
| **`Alm_FailToStop`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Drive Fail to Stop Alarm |
| **`Ack_FailToStop`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Fail to Stop Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_FailToStopDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Stop Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_FailToStopShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Stop Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_FailToStopSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Fail to Stop Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive was stopped by an Interlock NOT OK (One-Shot) |
| **`Alm_IntlkTrip`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Alarm: Drive stopped by an Interlock NOT OK |
| **`Ack_IntlkTrip`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Interlock Trip Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IntlkTripDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock Trip Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IntlkTripShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock Trip Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IntlkTripSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Interlock Trip Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_DriveFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive Fault (see drive display or manual) |
| **`Alm_DriveFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = Alarm: Drive Fault (see drive display or manual) |
| **`Ack_DriveFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = Drive Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_DriveFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_DriveFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_DriveFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Drive Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`Sts_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] I/O Comm Fault Status (0 = OK, 1 = Bad) |
| **`Alm_IOFault`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo động (1 = Đang báo động)] 1 = I/O Fault Alarm |
| **`Ack_IOFault`** | `BOOL` | `1` | Ẩn (Tag) | [Cờ xác nhận báo động (1 = Đã Ack)] 1 = I/O Fault Alarm đã được Xác nhận (Acknowledged) |
| **`Sts_IOFaultDisabled`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Vô hiệu hóa (Disabled) bởi Bảo trì |
| **`Sts_IOFaultShelved`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Tạm hoãn (Shelved) bởi Người vận hành |
| **`Sts_IOFaultSuppressed`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = I/O Fault Alarm đã bị Ức chế (Suppressed) bởi Chương trình PLC |
| **`MRdy_Rel`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Rel (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Unlock`** | `BOOL` | `0` | Ẩn (Tag) | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Unlock (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`Sts_Horn`** | `BOOL` | - | Ẩn (Tag) | [Cờ báo trạng thái logic (Status)] 1 = Motor Horn Active |
| **`P_VSD`** | `BOOL` | `0` | Ẩn (Tag) | Tham số định danh trùng tên AOI dùng cho công cụ tự động dò tìm thẻ (Auto-discovery) của HMI |

## 4. Bảng chi tiết 45 Biến Cấu Hình Cục Bộ (`Local Configuration Tags`) của `P_VSD`
| Tên biến nội bộ (Local Tag) | Kiểu dữ liệu | Mặc định | Giải thích chức năng & Cách cấu hình hiển thị HMI |
| :--- | :--- | :--- | :--- |
| **`Cfg_Area`** | `STRING_Area` | `'area01'` | [Biến cấu hình thông số] Process Area for security |
| **`Cfg_Desc`** | `STRING_40` | `'Variable Speed Drive'` | [Biến cấu hình thông số] Description for display on HMI. This string is shown in the title bar of the faceplate. |
| **`Cfg_FwdText`** | `STRING_16` | `'Forward'` | [Biến cấu hình thông số] Name for Forward Direction, e.g., "Up", "Forward" |
| **`Cfg_InpDatalinkEU`** | `STRING_8` | `''` | [Biến cấu hình thông số] Datalink Input Engineering Units for display on HMI |
| **`Cfg_InpDatalinkLabel`** | `STRING_20` | `''` | [Biến cấu hình thông số] Datalink Input Label displayed on faceplate |
| **`Cfg_Label`** | `STRING_20` | `'Motor Speed Control'` | [Biến cấu hình thông số] Label for graphic symbol displayed on HMI. This string appears on the graphic symbol. |
| **`Cfg_OutDatalinkEU`** | `STRING_8` | `''` | [Biến cấu hình thông số] Datalink Output Engineering Units for display on HMI |
| **`Cfg_OutDatalinkLabel`** | `STRING_20` | `''` | [Biến cấu hình thông số] Datalink Output Label displayed on faceplate |
| **`Cfg_RevText`** | `STRING_16` | `'Reverse'` | [Biến cấu hình thông số] Name for Reverse Direction, e.g., "Down", "Reverse" |
| **`Cfg_SpeedFdbkEU`** | `STRING_8` | `'Hz'` | [Biến cấu hình thông số] Speed Feedback Engineering Units for display on HMI |
| **`Cfg_SpeedRefEU`** | `STRING_8` | `'Hz'` | [Biến cấu hình thông số] Speed Reference Engineering Units for display on HMI |
| **`Cfg_Tag`** | `STRING_20` | `'P_VSD'` | [Biến cấu hình thông số] Tagname for display on HMI. This string is shown in the title bar of the faceplate. |
| **`CmdSrc`** | `P_CmdSrc` | `{...}` | Drive Mode Selection |
| **`DriveFault`** | `P_Alarm` | `{...}` | Drive Faulted Alarm |
| **`FailToStart`** | `P_Alarm` | `{...}` | Drive Failed to Start Alarm |
| **`FailToStop`** | `P_Alarm` | `{...}` | Drive Failed to Stop Alarm |
| **`HMI_Lib`** | `STRING_12` | `'RA-BAS'` | [Biến nội bộ giao diện HMI Faceplate] Tên thư viện màn hình đồ họa dùng để gọi cửa sổ HMI Faceplate |
| **`HMI_Tab`** | `SINT` | `0` | [Biến nội bộ giao diện HMI Faceplate] Chỉ số tab hiển thị hiện hành (dùng cho màn hình FactoryTalk View ME) |
| **`HMI_Type`** | `STRING_16` | `'P_VSD'` | [Biến nội bộ giao diện HMI Faceplate] Chuỗi định danh kiểu khối AOI dùng cho điều hướng HMI Faceplate |
| **`IntlkTrip`** | `P_Alarm` | `{...}` | Interlock Trip Alarm |
| **`IOFault`** | `P_Alarm` | `{...}` | I/O Fault Alarm |
| **`MCmd_Bypass`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Bypass all Bypassable Interlocks and Permissives |
| **`MCmd_Check`** | `BOOL` | `0` | [Lệnh từ tab Bảo trì (HMI Maintenance)] Lệnh từ Nhân viên bảo trì (Maintenance) để Check (not bypass) all Interlocks and Permissives |
| **`MRdy_Bypass`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Bypass (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`MRdy_Check`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh MCmd_Check (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`OCmd_JogFwd`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Jog Drive Forward (not cleared by instruction!) |
| **`OCmd_JogRev`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Jog Drive Reverse (not cleared by instruction!) |
| **`OCmd_Reset`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để clear shed latches and cleared alarms |
| **`OCmd_ResetAckAll`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để xóa các chốt ngắt bảo vệ (Shed latches) và các cảnh báo đã hết lỗi, plus Xác nhận (Acknowledge) alarms |
| **`OCmd_StartFwd`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Start Drive Forward |
| **`OCmd_StartRev`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Start Drive Reverse |
| **`OCmd_Stop`** | `BOOL` | `0` | [Lệnh từ màn hình HMI Operator] Lệnh từ Người vận hành (Operator HMI) để Stop Drive |
| **`ORdy_JogFwd`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_JogFwd (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_JogRev`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_JogRev (enabled HMI button) |
| **`ORdy_OSet`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OSets (other than Speed Ref) (enables data entry fields) |
| **`ORdy_Reset`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Reset (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_ResetAckAll`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_ResetAckAll (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_SpeedRef`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OSet_SpeedRef (enables data entry field) |
| **`ORdy_StartFwd`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_StartFwd (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_StartRev`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_StartRev (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`ORdy_Stop`** | `BOOL` | `0` | [Cờ cho phép sáng nút nhấn trên HMI] 1 = Sẵn sàng nhận lệnh OCmd_Stop (cho phép sáng nút nhấn tương ứng trên HMI Faceplate) |
| **`OSet_OutDatalink`** | `REAL` | `0` | [Giá trị đặt từ màn hình HMI Operator] Operator Setting of Output Datalink (Engineering Units) |
| **`OSet_SpeedRef`** | `REAL` | `0` | [Giá trị đặt từ màn hình HMI Operator] Operator Setting of Speed Reference (Engineering Units) |
| **`Val_FaultDesc`** | `STRING_40` | `‘Logic not yet scanned.’` | [Giá trị thực tế đầu ra (Logic/HMI)] Description of Drive Fault (given LastFaultCode) |
| **`Val_Notify`** | `DINT` | `0` | [Giá trị thực tế đầu ra (Logic/HMI)] Mã mức độ cảnh báo hiện tại và trạng thái xác nhận (0=Không lỗi, 2..9=Low..Urgent) |

